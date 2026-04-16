# 期中考重點整理 - 電腦網路 Ch1 & Ch2

> 中英文對照，依考試出題方向統整

---

## 一、網際網路基本組成 (Internet Components)

### 1.1 網際網路的「螺絲螺帽」觀點 (Nuts and Bolts View)

| 英文 | 中文 | 考點說明 |
|------|------|--------|
| Host / End System | 主機 / 終端系統 | 在網際網路邊緣執行應用程式的裝置 |
| Packet Switch | 封包交換器 | 轉送封包的裝置，包含 router 和 switch |
| Router | 路由器 | 網路核心的封包交換器 |
| Switch | 交換器 | 鏈路層的封包交換器 |
| Communication Link | 通訊鏈路 | 連接裝置的媒介（光纖、銅線、無線電、衛星） |
| Bandwidth / Transmission Rate | 頻寬 / 傳輸速率 | 鏈路的資料傳送速率 (bps) |
| ISP (Internet Service Provider) | 網際網路服務提供者 | 提供網際網路接取服務 |
| Protocol | 協定 | 定義訊息的格式、順序及動作 |
| RFC (Request for Comments) | 網際網路標準文件 | 定義開放協定的文件 |
| IETF (Internet Engineering Task Force) | 網際網路工程任務小組 | 制定網際網路標準的組織 |

### 1.2 協定的定義 (What is a Protocol?)

> **Protocols define the format, order of messages sent and received among network entities, and actions taken on message transmission, receipt.**

> 協定定義了網路實體之間傳送和接收訊息的**格式 (format)**、**順序 (order)**，以及在訊息傳送/接收時所採取的**動作 (actions)**。

---

## 二、網路結構三大區域 (Three Network Regions)

| 英文 | 中文 | 內容 |
|------|------|------|
| **Network Edge** | 網路邊緣 | 主機 (hosts)：clients 和 servers；servers 常在 data centers |
| **Access Networks** | 存取網路 | 將終端系統連接到邊緣路由器的網路 |
| **Network Core** | 網路核心 | 互連路由器的網狀結構 (mesh of interconnected routers) |

---

## 三、存取網路與實體媒介 (Access Networks & Physical Media)

### 3.1 存取網路類型

| 類型 | 英文 | 特點 |
|------|------|------|
| 有線電視網路 | Cable-based (HFC) | 共享 (shared)；下行 40Mbps-1.2Gbps，上行 30-100Mbps；FDM |
| 數位用戶迴路 | DSL (Digital Subscriber Line) | 專用 (dedicated)；下行 24-52Mbps，上行 3.5-16Mbps；用既有電話線 |
| 家用網路 | Home Network | WiFi AP + router/firewall/NAT + cable/DSL modem |
| 企業網路 | Enterprise Network | Ethernet (100Mbps/1Gbps/10Gbps) + WiFi |
| 無線存取 | Wireless Access | WLAN (WiFi 802.11): ~100ft；Cellular (4G/5G): ~10km |
| 資料中心 | Data Center Network | 高頻寬 10s-100s Gbps |

### 3.2 DSL 與 Cable 都使用的技術

> **FDM (Frequency Division Multiplexing, 分頻多工)**：在同一條線路上，用不同頻率頻帶同時傳輸語音和資料。

### 3.3 實體媒介分類

| 分類 | 英文 | 範例 |
|------|------|------|
| 導引式媒介 | Guided Media | 雙絞線 (Twisted Pair)、同軸電纜 (Coaxial Cable)、光纖 (Fiber Optic) |
| 非導引式媒介 | Unguided Media | 無線電 (Radio)：WiFi、4G cellular、Bluetooth、衛星 (Satellite) |

### 3.4 三種導引式媒介比較

| 媒介 | 英文 | 特點 |
|------|------|------|
| 雙絞線 | Twisted Pair (TP) | 兩條絕緣銅線；Cat5: 100Mbps/1Gbps；Cat6: 10Gbps |
| 同軸電纜 | Coaxial Cable | 兩個同心銅導體；雙向 (bidirectional)；寬頻 (broadband)；每頻道 100's Mbps |
| 光纖 | Fiber Optic Cable | 玻璃光纖攜帶光脈衝；10s-100s Gbps；低錯誤率；不受電磁干擾 (immune to electromagnetic noise) |

---

## 四、網路核心 (Network Core)

### 4.1 兩個關鍵功能

| 英文 | 中文 | 說明 |
|------|------|------|
| **Forwarding** (Switching) | 轉送（交換） | **區域 (local)** 動作：根據 forwarding table 將封包從輸入鏈路移至正確的輸出鏈路 |
| **Routing** | 路由 | **全域 (global)** 動作：使用 routing algorithms 決定來源到目的地的路徑 |

### 4.2 封包交換 vs. 電路交換 (Packet Switching vs. Circuit Switching)

| 比較項目 | Packet Switching (封包交換) | Circuit Switching (電路交換) |
|---------|---|---|
| 資源分配 | 按需共享 (on-demand, shared) | 預先保留專用 (reserved, dedicated) |
| 適合場景 | 突發性資料 (bursty data) | 持續穩定通訊（傳統電話） |
| 設定 | 不需通話設定 (no call setup) | 需要通話設定 |
| 資源利用 | 資源共享，更多使用者 | 閒置時浪費資源 |
| 缺點 | 壅塞 (congestion)、延遲、遺失 | 使用者數量受限 |
| 多工方式 | 統計多工 (statistical multiplexing) | FDM 或 TDM |

### 4.3 儲存轉送 (Store and Forward)

> 整個封包 (entire packet) 必須**完全到達路由器**後，才能傳送到下一條鏈路。
> 傳輸一個 L-bit 封包需要 **L/R 秒**。

### 4.4 排隊與遺失 (Queueing and Loss)

- 當到達速率 > 鏈路傳輸速率 => 封包排隊 (queue)
- 緩衝區 (buffer) 滿 => 封包丟棄 (dropped / lost)
- 遺失封包可能由來源重傳或完全不重傳

### 4.5 電路交換的多工方式

| 英文 | 中文 | 說明 |
|------|------|------|
| FDM (Frequency Division Multiplexing) | 分頻多工 | 頻率分成窄頻帶，每個通話分配一個頻帶 |
| TDM (Time Division Multiplexing) | 分時多工 | 時間分成時槽，每個通話分配週期性時槽 |

### 4.6 經典計算題

> 1 Gbps link, each user: 100 Mbps when active, active 10% of time
> - Circuit switching: **10 users**
> - Packet switching: 35 users, P(>10 active) < 0.0004

---

## 五、效能指標 (Performance)

### 5.1 四種封包延遲 (Four Sources of Packet Delay)

**d_nodal = d_proc + d_queue + d_trans + d_prop**

| 英文 | 中文 | 公式 | 說明 |
|------|------|------|------|
| Processing Delay (d_proc) | 處理延遲 | — | 檢查位元錯誤、決定輸出鏈路；通常 < microsecs |
| Queueing Delay (d_queue) | 排隊延遲 | — | 在輸出鏈路等待傳輸；取決於壅塞程度 |
| Transmission Delay (d_trans) | 傳輸延遲 | **L/R** | L: 封包長度 (bits)，R: 鏈路速率 (bps) |
| Propagation Delay (d_prop) | 傳播延遲 | **d/s** | d: 鏈路長度，s: 傳播速度 (~2×10⁸ m/sec) |

> **注意：Transmission delay 和 Propagation delay 非常不同！**
> - Transmission delay：把封包「推入」鏈路的時間（取決於封包大小和鏈路速率）
> - Propagation delay：位元在鏈路中「傳播」的時間（取決於實體距離和傳播速度）

### 5.2 流量強度 (Traffic Intensity)

**Traffic Intensity = La/R**
- a: 平均封包到達率；L: 封包長度；R: 鏈路頻寬
- La/R ≈ 0：排隊延遲小
- La/R → 1：排隊延遲大
- La/R > 1：延遲趨近無限大！

### 5.3 吞吐量 (Throughput)

| 英文 | 中文 | 說明 |
|------|------|------|
| Throughput | 吞吐量 | 位元從傳送端到接收端的速率 (bits/time) |
| Instantaneous Throughput | 瞬時吞吐量 | 某一時間點的速率 |
| Average Throughput | 平均吞吐量 | 較長時間的平均速率 |
| Bottleneck Link | 瓶頸鏈路 | 端對端路徑上限制吞吐量的鏈路 |

> **End-end throughput = min(R_s, R_c)**
> 多條連線共享：per-connection throughput = min(R_c, R_s, R/N)

### 5.4 Traceroute

- 提供從來源沿路徑到目的地的延遲測量
- 對每個路由器 i 發送 3 個封包（TTL=i），測量來回時間

---

## 六、協定層次 (Protocol Layers)

### 6.1 網際網路五層協定堆疊 (Internet Five-Layer Protocol Stack)

| Layer | 名稱 | 功能 | 協定範例 | PDU |
|-------|------|------|---------|-----|
| 5 | Application (應用層) | 支援網路應用 | HTTP, SMTP, DNS, IMAP | Message (訊息) |
| 4 | Transport (傳輸層) | 行程對行程資料傳輸 | TCP, UDP | Segment (區段) |
| 3 | Network (網路層) | 來源到目的地路由 | IP, routing protocols | Datagram (資料報) |
| 2 | Link (鏈路層) | 相鄰節點間資料傳輸 | Ethernet, WiFi, PPP | Frame (訊框) |
| 1 | Physical (實體層) | 線路上的位元 | — | Bits (位元) |

### 6.2 ISO/OSI 七層模型（多出的兩層）

| 英文 | 中文 | 說明 |
|------|------|------|
| Presentation Layer | 展示層 | 加密、壓縮、資料格式轉換 |
| Session Layer | 會議層 | 同步、檢查點、資料交換復原 |

> Internet stack「缺少」這兩層，若需要則由應用程式自行實作。

### 6.3 封裝 (Encapsulation)

每層加上自己的標頭 (header)：

```
Application:  M                         → Message
Transport:    H_t | M                    → Segment
Network:      H_n | H_t | M             → Datagram
Link:         H_l | H_n | H_t | M       → Frame
```

- **Switch**：只有 Link + Physical 層
- **Router**：有 Network + Link + Physical 層

> **Why headers are stacked?** 每層標頭包含該層實作服務所需的資訊；模組化設計讓各層獨立運作。

---

## 七、網路安全 (Network Security)

### 7.1 三種攻擊類型

| 英文 | 中文 | 說明 |
|------|------|------|
| Packet Sniffing | 封包嗅探 | 在廣播媒介上，混雜模式 (promiscuous mode) 讀取所有封包 |
| IP Spoofing | IP 位址偽造 | 注入帶偽造來源位址的封包 |
| Denial of Service (DoS) | 阻斷服務攻擊 | 用大量偽造流量淹沒目標，使合法流量無法使用 |

### 7.2 防禦機制

| 英文 | 中文 | 說明 |
|------|------|------|
| Authentication | 認證 | 證明身分 |
| Confidentiality | 機密性 | 透過加密 (encryption) |
| Integrity Checks | 完整性檢查 | 數位簽章 (digital signatures) |
| Access Restrictions | 存取限制 | 密碼保護的 VPN |
| Firewalls | 防火牆 | 過濾封包以限制傳送者/接收者/應用程式 |

---

## 八、應用層原理 (Application Layer Principles)

### 8.1 Client-Server vs. P2P 架構比較

| 比較項目 | Client-Server | P2P |
|---------|---------------|-----|
| Server | Always-on, permanent IP | No always-on server |
| Communication | Client contacts server; clients don't communicate directly | Arbitrary end systems directly communicate |
| Scalability | Add servers / data centers | Self scalability: new peers bring capacity + demand |
| IP addresses | Server: fixed; Client: may be dynamic | Peers change IPs |
| Management | Simpler | Complex (churn) |
| Examples | HTTP, IMAP, FTP | BitTorrent, P2P file sharing |

### 8.2 行程通訊 (Process Communication)

| 英文 | 中文 | 說明 |
|------|------|------|
| Process | 行程 | 在主機中運行的程式 |
| Socket | 插座 | 行程傳送/接收訊息的「門」 |
| Port Number | 連接埠號碼 | 識別主機上哪個行程（HTTP: 80, Mail: 25） |
| Client Process | 用戶端行程 | 發起通訊的行程 |
| Server Process | 伺服器行程 | 等待被聯繫的行程 |

### 8.3 四大傳輸服務需求

| 英文 | 中文 | 說明 |
|------|------|------|
| Data Integrity | 資料完整性 | 檔案傳輸需 100% 可靠；音訊可容忍 loss |
| Throughput | 吞吐量 | 多媒體需最低保證；elastic apps 用多少算多少 |
| Timing | 時序 | 網路電話/遊戲需低延遲 |
| Security | 安全性 | 加密、完整性、認證 |

### 8.4 TCP vs. UDP 服務比較

| 特點 | TCP | UDP |
|------|-----|-----|
| Transport | Reliable (可靠) | Unreliable (不可靠) |
| Flow Control | Yes (不淹沒接收端) | No |
| Congestion Control | Yes (節制傳送端) | No |
| Connection | Connection-oriented (連線導向) | No connection setup |
| Does NOT provide | Timing, throughput guarantee, security | Reliability, flow/congestion control, timing, throughput, security |

### 8.5 TLS (Transport Layer Security)

- 在應用層實作，使用 TCP
- 提供加密的 TCP 連線、資料完整性、端點認證
- 原始 TCP/UDP：無加密，明文傳輸

---

## 九、HTTP 重點 (Web and HTTP)

### 9.1 HTTP 基本特性

| 英文 | 中文 | 說明 |
|------|------|------|
| HTTP | 超文本傳輸協定 | Web 的應用層協定 |
| Stateless | 無狀態 | 伺服器不維護過去的請求資訊 |
| Uses TCP | 使用 TCP | Port 80 |
| URL | 統一資源定位器 | host name + path name |

### 9.2 Non-persistent vs. Persistent HTTP

| | Non-persistent HTTP | Persistent HTTP (HTTP 1.1) |
|---|---|---|
| TCP connection | 每個物件開新連線 | 多個物件用同一連線 |
| Objects per connection | 最多 1 個 | 多個 |
| Response time per object | **2RTT + file transmission time** | 最少 1 RTT for all referenced objects |
| OS overhead | 每個連線都有 | 減少 |

> **RTT (Round Trip Time)**：小封包從 client 到 server 再回來的時間

### 9.3 HTTP Methods

| Method | 用途 |
|--------|------|
| **GET** | 請求物件；可在 URL 中附帶資料（`?` 之後） |
| **POST** | 表單輸入，資料在 entity body 中 |
| **HEAD** | 只請求 headers，不回傳物件 |
| **PUT** | 上傳/取代伺服器上的檔案 |

### 9.4 HTTP Response Status Codes

| Code | Meaning |
|------|---------|
| **200 OK** | 請求成功 |
| **301 Moved Permanently** | 物件永久移動 |
| **400 Bad Request** | 伺服器無法理解請求 |
| **404 Not Found** | 找不到請求的文件 |
| **505 HTTP Version Not Supported** | 不支援的 HTTP 版本 |

### 9.5 Cookie 四元件

1. Response message 的 `Set-cookie:` header line
2. 後續 request message 的 `cookie:` header line
3. 使用者主機上的 cookie file（由 browser 管理）
4. 網站的 back-end database

### 9.6 Web Cache (Proxy Server)

**Benefits（好處）**：
1. Reduce response time（減少回應時間）：cache 離 client 更近
2. Reduce traffic on access link（減少存取鏈路流量）
3. Enable poor content providers to deliver content effectively

**Conditional GET**：
- Client: `If-modified-since: <date>`
- Server: `304 Not Modified`（未修改，不傳物件）或 `200 OK`（已修改，傳新物件）

### 9.7 快取計算範例

> Access link: 1.54 Mbps, RTT: 2 sec, object: 100K bits, rate: 15/sec
> - 無快取：utilization = 0.97 => 延遲 = 2 sec + **minutes**
> - 升級至 154 Mbps：utilization = 0.0097 => 延遲 = 2 sec + msecs（貴）
> - 安裝 cache (hit rate 0.4)：avg delay = 0.6(2.01) + 0.4(~msecs) ≈ **1.2 secs**（便宜又快）

### 9.8 HTTP/2 與 HOL Blocking

| 問題 | 解法 |
|------|------|
| HTTP 1.1: FCFS ordering → small objects wait behind large objects (**HOL blocking**) | HTTP/2: 將物件分成 **frames**，交錯傳輸 (interleave)；client-specified priority；server push |

### 9.9 HTTP/3

- HTTP/2 仍用單一 TCP 連線 => packet loss 卡住所有物件
- HTTP/3：在 **UDP** 上加入安全性、per-object error & congestion control

---

## 十、電子郵件 (E-mail)

### 10.1 三大元件

| 英文 | 中文 |
|------|------|
| User Agent (UA) | 使用者代理（mail reader）：Outlook, iPhone mail |
| Mail Server | 郵件伺服器：含 mailbox + message queue |
| SMTP (Simple Mail Transfer Protocol) | 簡易郵件傳輸協定 |

### 10.2 SMTP 重點

- 使用 **TCP, port 25**
- **Push protocol**（client push）vs. HTTP 是 pull protocol
- 三階段：handshaking → transfer → closure
- 訊息須為 **7-bit ASCII**
- 使用 **persistent connections**
- 用 **CRLF.CRLF** 判斷訊息結尾

### 10.3 SMTP vs. HTTP

| | HTTP | SMTP |
|---|---|---|
| Direction | Client **pull** | Client **push** |
| Object encapsulation | Each object in own response | Multiple objects in multipart message |
| Encoding | No restriction | 7-bit ASCII required |
| Connection | Persistent/non-persistent | Persistent |

### 10.4 Mail Access Protocols

| 英文 | 中文 | 用途 |
|------|------|------|
| SMTP | 簡易郵件傳輸協定 | 傳送/儲存郵件至接收端伺服器 |
| IMAP (Internet Mail Access Protocol) | 網際網路郵件存取協定 | 從伺服器擷取郵件，支援資料夾管理 |
| HTTP | 超文本傳輸協定 | Web-based 介面（如 Gmail），底層用 SMTP 寄 + IMAP 收 |

---

## 十一、DNS (Domain Name System)

### 11.1 DNS 基本概念

- **分散式資料庫 (distributed database)**，實作於階層式名稱伺服器
- **應用層協定 (application-layer protocol)**
- 核心功能：hostname-to-IP-address translation（主機名稱到 IP 位址的轉譯）

### 11.2 DNS 階層結構

```
Root DNS Servers（根）
       ↓
TLD DNS Servers（頂級網域：.com, .org, .edu, .tw）
       ↓
Authoritative DNS Servers（權威：amazon.com, nyu.edu）

+ Local DNS Server（本地，不嚴格屬於階層，每個 ISP 都有）
```

### 11.3 為何不集中式？(Why not centralize DNS?)

1. Single point of failure（單一故障點）
2. Traffic volume（流量過大）
3. Distant centralized database（遠端太慢）
4. Maintenance（維護困難）
=> **Doesn't scale!**

### 11.4 DNS 服務

| 英文 | 中文 |
|------|------|
| Hostname-to-IP-address translation | 主機名稱對 IP 位址轉譯 |
| Host aliasing | 主機別名（canonical name ↔ alias） |
| Mail server aliasing | 郵件伺服器別名 |
| Load distribution | 負載分散（一個名稱對應多個 IP） |

### 11.5 迭代式 vs. 遞迴式查詢

| | Iterated Query (迭代式) | Recursive Query (遞迴式) |
|---|---|---|
| 方式 | 被聯繫的伺服器回覆「去問誰」，查詢負擔在 local DNS | 被聯繫的伺服器代為查詢，負擔在上層伺服器 |
| 優點 | 上層伺服器負擔輕 | local DNS 實作簡單 |
| 缺點 | local DNS 要發多次查詢 | 上層伺服器負擔重 |

### 11.6 DNS Resource Records (RR)

格式：**(name, value, type, ttl)**

| Type | name | value | 用途 |
|------|------|-------|------|
| **A** | hostname | IP address | 主機 → IP |
| **NS** | domain (foo.com) | authoritative name server hostname | 網域 → 名稱伺服器 |
| **CNAME** | alias name | canonical name | 別名 → 正規名稱 |
| **MX** | name | SMTP mail server name | 名稱 → 郵件伺服器 |

### 11.7 DNS Caching & TTL

- 名稱伺服器學到對應後會 **cache**
- Cache entries 在 **TTL (Time-To-Live)** 到期後消失
- 可能 **out-of-date**：主機改 IP 後需等 TTL 過期才更新
- TLD servers 通常被 cached 在 local name servers

### 11.8 DNS Security

| 攻擊 | 英文 | 防禦 |
|------|------|------|
| 轟炸根/TLD 伺服器 | DDoS attacks | Traffic filtering; local DNS cache TLD IPs |
| 攔截查詢回傳偽造回覆 | Spoofing / DNS cache poisoning | DNSSEC (RFC 4033) |

---

## 十二、P2P 與 BitTorrent

### 12.1 檔案分配時間公式

| 模式 | 公式 | 特點 |
|------|------|------|
| Client-Server | **D_cs >= max{NF/u_s, F/d_min}** | 隨 N 線性增長 |
| P2P | **D_P2P >= max{F/u_s, F/d_min, NF/(u_s+Σu_i)}** | 服務容量也隨 N 增長 |

### 12.2 BitTorrent 機制

| 英文 | 中文 | 說明 |
|------|------|------|
| Chunk | 區塊 | 檔案分成 256KB 的區塊 |
| Torrent | 洪流 | 交換某檔案區塊的 peer 群組 |
| Tracker | 追蹤器 | 追蹤參與 torrent 的 peers |
| Rarest First | 稀有優先 | 優先請求最稀有的區塊 |
| Tit-for-tat | 以牙還牙 | 傳送給提供最高速率的前 4 個 peer；每 10 秒重評 |
| Optimistically Unchoke | 樂觀性解除阻擋 | 每 30 秒隨機選一個新 peer 開始傳送 |
| Churn | 攪動 | Peers 隨時加入/離開 |

### 12.3 BitTorrent 如何處理自私離開的 peer？

> Tit-for-tat 機制：不上傳就得不到下載 => 激勵 peer 在下載時持續上傳。Optimistic unchoking 讓新 peer 有機會加入。Rarest first 確保區塊分布均勻。

---

## 十三、影片串流與 CDN

### 13.1 影片編碼

| 英文 | 中文 | 說明 |
|------|------|------|
| CBR (Constant Bit Rate) | 固定位元率 | 編碼速率固定 |
| VBR (Variable Bit Rate) | 可變位元率 | 速率隨內容變化 |
| Spatial Coding | 空間編碼 | 利用影像內冗餘 |
| Temporal Coding | 時間編碼 | 利用影像間冗餘（只傳差異） |

### 13.2 Streaming 挑戰

| 英文 | 中文 |
|------|------|
| Continuous playout constraint | 連續播放限制：播放時序須符合原始時序 |
| Client-side buffer | 用戶端緩衝：補償網路延遲的 jitter（抖動） |
| Playout delay | 播放延遲 |

### 13.3 DASH (Dynamic Adaptive Streaming over HTTP)

**Server-side**：
- 影片分成 chunks → 每個 chunk 編碼成多種 rates → 存在不同檔案 → 複製到 CDN nodes → 建立 **manifest file**

**Client-side**（intelligence at client）：
- 估算可用頻寬 → 參考 manifest → 選擇最大可支撐的 coding rate → 決定 when / what rate / where 請求 chunk

> **Streaming video = encoding + DASH + playout buffering**

### 13.4 CDN (Content Distribution Network)

**功能**：在多個地理分散站點儲存/服務內容副本

| 策略 | 英文 | 說明 |
|------|------|------|
| 深入策略 | Enter Deep | CDN 伺服器深入存取網路，靠近使用者（Akamai: 240K+ servers） |
| 帶回策略 | Bring Home | 較少大型叢集在 POP 點附近（Limelight） |

### 13.5 CDN 依賴 DNS 的操作

- **Content request redirection**：用 DNS (CNAME record) 將用戶端重導向至最近的 CDN 節點
- **Best CDN node selection**：CDN 的 DNS 根據用戶端本地 DNS 的 IP 位址選擇最佳節點

---

## 十四、Socket Programming

| 英文 | 中文 | 說明 |
|------|------|------|
| Socket | 插座 | Application process 和 transport protocol 之間的「門」 |
| UDP Socket | UDP 插座 | 不可靠的資料報 (unreliable datagram) |
| TCP Socket | TCP 插座 | 可靠的位元組串流導向 (reliable, byte stream-oriented) |

---

## 重點公式速查

| 公式 | 說明 |
|------|------|
| **d_trans = L / R** | 傳輸延遲 = 封包長度 / 鏈路速率 |
| **d_prop = d / s** | 傳播延遲 = 鏈路長度 / 傳播速度 |
| **d_nodal = d_proc + d_queue + d_trans + d_prop** | 節點總延遲 |
| **Traffic Intensity = La / R** | 流量強度（→1 延遲爆增，>1 無限大） |
| **Throughput = min(R_s, R_c)** | 端對端吞吐量取瓶頸鏈路 |
| **Non-persistent HTTP = 2RTT + file transmission time** | 每個物件的回應時間 |
| **D_cs >= max{NF/u_s, F/d_min}** | C/S 檔案分配下界 |
| **D_P2P >= max{F/u_s, F/d_min, NF/(u_s+Σu_i)}** | P2P 檔案分配下界 |
| **Cache: avg delay = 0.6×(origin) + 0.4×(cache)** | 快取命中率 0.4 時的平均延遲 |
