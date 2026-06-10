# 第四章：網路層 — 資料平面 (Network Layer: Data Plane)

> 教科書：Computer Networking: A Top-Down Approach, 8th Edition
> 期末考範圍：**Chapter 4.1（概觀）& 4.3（IP）**

---

## 4.1 網路層概觀 (Network Layer: Overview)

### 網路層服務

- 把**傳輸節段 (transport segment)** 從傳送主機送到接收主機
  - **傳送端**：把節段封裝成**資料報 (datagrams)**，往下交給鏈路層
  - **接收端**：把節段交給傳輸層協定
- 網路層協定存在於**每一個**網際網路裝置：主機 (hosts) 與 router
- **router 的工作**：檢查所有經過的 IP 資料報標頭欄位，把資料報從**輸入埠 (input port)** 移到適當的**輸出埠 (output port)**，沿端到端路徑轉送

### 兩大網路層功能 (HW4-1)

| 功能 | 說明 | 旅行類比 |
|------|------|----------|
| **轉送 (forwarding)** | 把封包從 router 的輸入鏈路移到適當的輸出鏈路（**本地、單一 router** 動作） | 通過單一交流道的過程 |
| **路由 (routing)** | 決定封包從來源到目的地所走的路徑（**全網**動作，由路由演算法完成） | 規劃整趟旅程的路線 |

### 資料平面 vs. 控制平面 (HW4-1)

| | **資料平面 (Data Plane)** | **控制平面 (Control Plane)** |
|---|---|---|
| 範圍 | **本地、per-router 功能** | **全網邏輯 (network-wide)** |
| 決定 | 抵達輸入埠的資料報如何轉送到輸出埠 | 資料報如何在 router 之間沿端到端路徑被**路由** |
| 依據 | 抵達封包標頭中的值 | — |
| 對應 | forwarding | routing |

> **HW4-1 答案**：
> - **資料平面**：每台 router 的本地功能，決定抵達輸入埠的封包要往哪個輸出埠轉送（依封包標頭值查 forwarding table）。
> - **控制平面**：全網邏輯，決定封包如何在 router 間沿端到端路徑被路由（即計算 forwarding table 的內容）。

- **兩種控制平面實作方式**：
  1. **傳統路由演算法 (traditional)**：在每台 router 中實作（per-router control）
  2. **軟體定義網路 (SDN)**：在（遠端）伺服器中實作，由遠端控制器計算並安裝各 router 的 forwarding table

### 網路服務模型 (Network Service Model)

- Q：傳送資料報的「通道」要提供什麼服務模型？
  - 對**個別資料報**：保證傳遞 (guaranteed delivery)、保證延遲 < 40ms…
  - 對**資料報流**：依序傳遞、保證最低頻寬、限制封包間距變化…

| 網路架構 | 服務模型 | 頻寬 | 遺失 | 順序 | 時序 |
|----------|----------|------|------|------|------|
| **Internet** | **best effort** | 無 | 無保證 | 無保證 | 無保證 |
| ATM | Constant Bit Rate | 固定速率 | 是 | 是 | 是 |
| ATM | Available Bit Rate | 保證最低 | 無 | 是 | 無 |
| Internet | Intserv Guaranteed | 是 | 是 | 是 | 是 |
| Internet | Diffserv | 可能 | 可能 | 可能 | 無 |

- **Internet best-effort 模型**：對成功傳遞、時序、順序、頻寬**皆無保證**
- 為何 best-effort 成功？機制簡單 → 廣泛部署；頻寬充分供給；應用層複製服務（資料中心、CDN）；彈性服務的壅塞控制

---

## 4.3 IP：網際網路協定 (The Internet Protocol)

> 網路層三大要素：**IP 協定**（資料報格式、定址、封包處理慣例）、**路由協定**（OSPF, BGP / SDN，決定 forwarding table）、**ICMP**（錯誤回報、router 信令）。

### IP 資料報格式 (IP Datagram Format)

| 欄位 | 說明 |
|------|------|
| **ver** | IP 協定版本號 |
| **head. len** | 標頭長度 (bytes) |
| **type of service** | 服務型態：diffserv (0:5)、**ECN (6:7)** |
| **total length** | 資料報總長度 (bytes)，最大 64K，通常 ≤ 1500 |
| **16-bit identifier、flags、fragment offset** | 分段/重組用 |
| **time to live (TTL)** | 剩餘最大躍程數 (max hops)，**每經過一台 router 減 1** |
| **upper layer** | 上層協定（如 TCP=6 或 UDP=17），交給哪個傳輸協定 |
| **header checksum** | 標頭檢查碼 |
| **source / destination IP address** | 各 32 bits |
| options | 選項（如時間戳、記錄路由） |
| payload | 資料（通常為 TCP 或 UDP 節段） |

- **負擔 (overhead)**：TCP 標頭 20 bytes + IP 標頭 20 bytes = **40 bytes** + 應用層負擔

> **TTL 與 Upper Layer 欄位的功能 (HW4-2)**
> - **TTL (Time to Live)**：記錄封包剩餘可經過的最大躍程數；每經過一台 router 就減 1，**減到 0 即丟棄**該封包 → 防止封包在路由迴圈中無限繞行。
> - **Upper Layer**：指出此 IP 資料報的 payload 屬於**哪個上層（傳輸層）協定**（如 TCP 或 UDP），讓目的主機在拆封後知道要把資料交給哪個傳輸協定處理（即網路層的 demux 依據）。

### IP 分段與重組 (Fragmentation / Reassembly)

- 網路鏈路有 **MTU (Maximum Transfer Unit)**：最大鏈路層訊框可載的位元組數；不同鏈路類型 MTU 不同
- 大的 IP 資料報在網路中被**分段 (fragmented)** → 一個資料報變成數個
- **只在目的地重組 (reassembled only at destination)**
- 用 IP 標頭中的位元（identifier、flag、offset）來辨識與排序相關片段

> **分段範例**：4000 byte 資料報、MTU=1500 →
> | 片段 | length | ID | fragflag | offset |
> |------|--------|----|----------|--------|
> | #1 | 1500 | x | 1 | 0 |
> | #2 | 1500 | x | 1 | 185 (=1480/8) |
> | #3 | 1040 | x | 0 | 370 |
> - 每段資料欄 1480 bytes（1500 − 20 標頭）；offset 以 **8 bytes 為單位**計算。

### IP 定址 (IP Addressing)

- **IP 位址**：32-bit 識別碼，與每個主機/router 的**介面 (interface)** 關聯
- **介面**：主機/router 與實體鏈路之間的連接
  - router 通常有**多個**介面；主機通常 1~2 個（如有線乙太、無線 802.11）
- **點分十進位 (dotted-decimal)** 記法：`223.1.1.1 = 11011111 00000001 00000001 00000001`

### 子網路 (Subnets)

- **什麼是子網路？** 介面之間**不需經過 router** 就能彼此實體連通的裝置群
- IP 位址有結構：
  - **子網路部分 (subnet part)**：同一子網路的裝置共用的高位元
  - **主機部分 (host part)**：剩餘的低位元
- **定義子網路的方法**：把每個介面從其主機/router 拆下，形成一座座「孤島」，每座孤島就是一個子網路
- **子網路遮罩 (subnet mask)**：如 `/24` 表示高位 24 bits 為子網路部分

### CIDR (Classless InterDomain Routing) (HW4-3)

> **CIDR**（唸作 "cider"）：無類別跨網域路由。

- **子網路部分可為任意長度 (arbitrary length)**
- 位址格式：**a.b.c.d/x**，其中 **x = 子網路部分的位元數**
  - 例：`200.23.16.0/23` → 前 23 bits 為子網路部分

> **HW4-3 答案**：CIDR 是 Classless InterDomain Routing，允許 IP 位址的子網路（網路前綴）部分為**任意長度**，以 `a.b.c.d/x` 表示（x 為網路前綴位元數），不再受傳統 Class A/B/C 固定切分的限制 → 更有彈性地分配位址、減少浪費。

### 如何取得 IP 位址？

**Q1：主機如何取得位址（host part）？**
1. 由系統管理者**寫死 (hard-coded)** 在設定檔（如 UNIX 的 `/etc/rc.config`）
2. **DHCP (Dynamic Host Configuration Protocol)**：動態向伺服器取得位址（隨插即用 plug-and-play）

#### DHCP

- **目標**：主機加入網路時，**動態**從網路伺服器取得 IP 位址
  - 可續租 (renew lease)、可重用位址、支援行動使用者
- **四步驟**（discover/offer 可省略）：
  1. 主機**廣播 DHCP discover**（src 0.0.0.0:68, dest 255.255.255.255:67）
  2. DHCP 伺服器回 **DHCP offer**（提供 IP）
  3. 主機要求位址：**DHCP request**
  4. 伺服器確認：**DHCP ack**
- DHCP 可回傳的**不只 IP**：還有**第一躍程 router (first-hop router) 位址**、**DNS 伺服器名稱與 IP**、**網路遮罩 (network mask)**

**Q2：網路如何取得位址（network part）？**
- 從其**提供者 ISP 的位址空間**取得分配的一段
  - 例：ISP 區塊 `200.23.16.0/20`，可再切成 8 個 `/23` 塊分給組織 0~7

#### 階層式定址與路由聚合 (Hierarchical Addressing / Route Aggregation) (HW4-4)

> **路由聚合 (route aggregation)** 的想法：階層式定址讓 ISP 能**有效率地廣告路由資訊**。

- ISP（Fly-By-Night-ISP）對外只廣告**一條聚合路由**：「把開頭為 `200.23.16.0/20` 的全部送給我」，而不必逐一廣告底下 8 個組織
- → 大幅減少全球路由表項目與廣告流量

> **HW4-4 答案**：路由聚合是指 ISP 把其底下多個組織的位址區塊**合併成單一較大的網路前綴**對外廣告（例如 8 個 /23 聚合成 1 個 /20），如此其他 ISP 的路由表只需一筆項目即可涵蓋這些組織，**減少路由表大小與更新流量**，提升路由可擴展性。

#### 更明確的路由 (More Specific Routes) (HW4-5)

- 當組織 1 從 Fly-By-Night-ISP 搬到 ISPs-R-Us，ISPs-R-Us 會廣告一條**更明確 (more specific)** 的路由（`200.23.18.0/23`）到組織 1
- 此時兩條路由重疊：Fly-By-Night 的 `200.23.16.0/20` 與 ISPs-R-Us 的 `200.23.18.0/23`

> **為何 router 須選「更明確的子網路前綴」轉送，才能保證正確？(HW4-5)**
> 採用**最長前綴比對 (longest prefix matching)**。當一個目的位址同時符合多個路由項目時，較長（更明確）的前綴代表更精確地指向該位址實際所在的網路。以組織 1 搬家為例：`200.23.18.0/23`（在 ISPs-R-Us）比 `200.23.16.0/20`（在 Fly-By-Night）更明確，選擇較長前綴才能把封包送到組織 1 真正連接的 ISP，否則會被送到錯誤的舊 ISP。

#### IP 位址分配 (最終來源)

- ISP 的位址區塊來自 **ICANN** (Internet Corporation for Assigned Names and Numbers)
  - 分配 IP 位址（透過 5 個區域註冊機構 RRs）、管理 DNS 根區、委派 TLD（.com, .edu…）
- IPv4 位址不夠用：ICANN 於 2011 分配完最後一批 IPv4；**NAT** 緩解、**IPv6** 提供 128-bit 空間

### NAT：網路位址轉換 (Network Address Translation)

- **NAT**：本地網路內所有裝置對外**共用一個** IPv4 位址
- 本地裝置使用**私有 (private) IP 位址空間**（只能在本地網路用）：
  - `10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16`
- **優點**：
  - 只需向 ISP 要**一個** IP 位址即可供所有裝置使用
  - 可任意更改本地主機位址而不需通知外界
  - 可更換 ISP 而不需改本地裝置位址
  - **安全**：內部裝置不被外界直接定址/看見
- **實作（NAT router 須透明地）**：
  - **外送資料報**：把 (來源 IP, 埠號) 換成 (NAT IP, 新埠號)
  - 在 **NAT translation table** 記住每一對 (來源 IP, 埠號) ↔ (NAT IP, 新埠號)
  - **內送資料報**：把目的欄的 (NAT IP, 新埠號) 換回對應的 (來源 IP, 埠號)
- **爭議**：router「應只處理到第 3 層」、位址短缺應由 IPv6 解決、違反端到端論點、**NAT 穿越 (traversal)** 問題（外部用戶端想連到 NAT 後的伺服器）
- 但 NAT 大量用於家庭、機構、4G/5G 蜂巢網路，**留下來了**

### IPv6

- **動機**：
  - 初始：32-bit IPv4 位址空間將用罄
  - 額外：加速處理/轉送（**40-byte 固定長度標頭**）、對「流 (flows)」做不同網路層處理
- **IPv6 資料報格式重點**：
  - **128-bit** 來源/目的位址
  - **priority**：辨識流中資料報的優先序；**flow label**：辨識同一「流」的資料報
- **相較 IPv4 缺少的東西**：
  - **無 checksum**（加速 router 處理）
  - **無分段/重組 (no fragmentation/reassembly)**
  - **無 options**（改用 next-header 上層協定）
- **IPv4 → IPv6 過渡**：
  - router 無法同時全部升級（沒有「flag day」）
  - **隧道 (tunneling)**：IPv6 資料報當成 **payload 包在 IPv4 資料報**內，穿越 IPv4 router（「封包中的封包」）；4G/5G 也大量使用隧道
- **採用現況**：Google 約 30% 用戶端以 IPv6 存取；部署耗時極久（25 年以上！）

---

## 本章重點速查

| 主題 | 重點 |
|------|------|
| forwarding vs. routing | forwarding=本地/單router；routing=全網/決定路徑 |
| data plane vs. control plane | data=per-router 轉送；control=全網路由計算 forwarding table |
| TTL | 每經一 router −1，到 0 丟棄，防迴圈 |
| Upper Layer 欄位 | 指出 payload 屬 TCP/UDP（網路層 demux） |
| CIDR | a.b.c.d/x，子網路部分任意長度 |
| 最長前綴比對 | 多筆符合時選最明確（最長）前綴 |
| NAT 私有位址 | 10/8、172.16/12、192.168/16 |
| IPv6 缺少 | 無 checksum、無分段、無 options；128-bit 位址 |
