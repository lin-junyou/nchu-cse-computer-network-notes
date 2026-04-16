# 第二章：應用層 (Application Layer)

> 教科書：Computer Networking: A Top-Down Approach, 8th Edition

---

## 2.1 網路應用程式原理 (Principles of Network Applications)

### 應用程式架構 (Application Architectures)

#### 用戶端-伺服器模式 (Client-Server Paradigm)

- **伺服器 (Server)**：
  - 永遠運行的主機 (always-on host)
  - 固定的 IP 位址 (permanent IP address)
  - 通常在資料中心 (data centers) 中以利擴展
- **用戶端 (Client)**：
  - 主動聯繫並與伺服器通訊
  - 可能間歇性連線 (intermittently connected)
  - 可能有動態 IP 位址 (dynamic IP addresses)
  - 用戶端之間**不會**直接通訊
- 範例：HTTP、IMAP、FTP

#### 點對點架構 (Peer-to-Peer, P2P Architecture)

- **沒有**永遠運行的伺服器
- 任意終端系統 (end systems) 直接通訊
- 對等節點 (peers) 互相請求服務，也互相提供服務
  - **自我擴展性 (self scalability)**：新的 peer 帶來新的服務容量，也帶來新的服務需求
- 對等節點間歇性連線，且 IP 位址會改變 => 管理複雜
- 範例：P2P 檔案分享 (BitTorrent)

### 行程通訊 (Processes Communicating)

- **行程 (process)**：在主機中運行的程式
- 同一主機中，兩個行程透過**行程間通訊 (inter-process communication, IPC)** 溝通（由作業系統定義）
- 不同主機的行程透過交換**訊息 (messages)** 來通訊
- **用戶端行程 (client process)**：發起通訊的行程
- **伺服器行程 (server process)**：等待被聯繫的行程

### 插座 (Sockets)

- 行程透過其**插座 (socket)** 傳送/接收訊息
- Socket 類比為「門」：傳送行程將訊息推出門外，依賴傳輸基礎設施將訊息傳送至接收行程的 socket
- 每次通訊涉及兩個 socket：兩端各一個

### 定址行程 (Addressing Processes)

- 行程要接收訊息必須有**識別碼 (identifier)**
- 主機裝置有唯一的 32 位元 **IP 位址 (IP address)**
- 識別碼包含 **IP 位址**和**連接埠號碼 (port number)**
- 常見連接埠號碼：
  - HTTP 伺服器：**80**
  - 郵件伺服器：**25**

### 應用層協定定義 (Application-Layer Protocol Defines)

- 交換的訊息類型 (types of messages)：如請求 (request)、回應 (response)
- **訊息語法 (message syntax)**：訊息中有哪些欄位、如何區分
- **訊息語意 (message semantics)**：欄位中資訊的意義
- **規則 (rules)**：行程何時以及如何傳送和回應訊息
- **開放協定 (open protocols)**：定義於 RFC，如 HTTP、SMTP
- **私有協定 (proprietary protocols)**：如 Skype、Zoom

### 應用程式需要的傳輸服務 (Transport Service Requirements)

| 需求面向 | 說明 |
|----------|------|
| **資料完整性 (Data Integrity)** | 某些應用（檔案傳輸）需要 100% 可靠傳輸；其他（音訊）可容忍部分遺失 |
| **吞吐量 (Throughput)** | 某些應用（多媒體）需要最低吞吐量；彈性應用 (elastic apps) 可接受任何吞吐量 |
| **時序 (Timing)** | 某些應用（網路電話、互動遊戲）需要低延遲 |
| **安全性 (Security)** | 加密 (encryption)、資料完整性 (data integrity) |

### 常見應用的傳輸需求

| 應用 | 資料遺失 | 吞吐量 | 時間敏感 |
|------|---------|--------|---------|
| 檔案傳輸 | 不容許 | 彈性 | 否 |
| 電子郵件 | 不容許 | 彈性 | 否 |
| 網頁文件 | 不容許 | 彈性 | 否 |
| 即時音訊/視訊 | 可容忍 | 音訊: 5Kbps-1Mbps | 是，10's msec |
| 串流音訊/視訊 | 可容忍 | 同上 | 是，數秒 |
| 互動遊戲 | 可容忍 | Kbps+ | 是，10's msec |

### 網際網路傳輸協定服務

| | TCP | UDP |
|---|-----|-----|
| 傳輸方式 | **可靠傳輸 (reliable transport)** | **不可靠資料傳輸 (unreliable data transfer)** |
| 流量控制 | **流量控制 (flow control)**：不會淹沒接收端 | 不提供 |
| 壅塞控制 | **壅塞控制 (congestion control)**：網路過載時節制傳送端 | 不提供 |
| 連線方式 | **連線導向 (connection-oriented)**：需要設定 | 不需連線設定 |
| 不提供 | 時序、最低吞吐量保證、安全性 | 可靠性、流量控制、壅塞控制、時序、吞吐量保證、安全性 |

### 常見應用與傳輸協定

| 應用 | 應用層協定 | 傳輸協定 |
|------|---------|---------|
| 檔案傳輸 | FTP [RFC 959] | TCP |
| 電子郵件 | SMTP [RFC 5321] | TCP |
| 網頁文件 | HTTP 1.1 [RFC 7320] | TCP |
| 網路電話 | SIP, RTP 或私有協定 | TCP 或 UDP |
| 串流音訊/視訊 | HTTP, DASH | TCP |
| 互動遊戲 | WOW, FPS (私有) | UDP 或 TCP |

### 保護 TCP 的安全 (Securing TCP)

- 原始 TCP 和 UDP 插座：**無加密**，明文密碼在網際網路上傳輸
- **傳輸層安全性 (TLS, Transport Layer Security)**：
  - 提供加密的 TCP 連線
  - 資料完整性 (data integrity)
  - 端點認證 (end-point authentication)
  - TLS 在應用層實作，應用程式使用 TLS 函式庫

---

## 2.2 全球資訊網與 HTTP (Web and HTTP)

### 基本概念

- 網頁 (web page) 由**物件 (objects)** 組成，每個物件可存在不同的網頁伺服器上
- 物件可以是 HTML 檔案、JPEG 影像、Java applet、音訊檔案等
- 網頁由**基底 HTML 檔案 (base HTML-file)** 組成，包含多個**被參照物件 (referenced objects)**
- 每個物件透過 **URL (Uniform Resource Locator)** 定址
  - URL 包含：**主機名稱 (host name)** + **路徑名稱 (path name)**

### HTTP 概觀 (HTTP Overview)

- **HTTP (HyperText Transfer Protocol)**：超文本傳輸協定
- Web 的應用層協定，採用**用戶端/伺服器模式**
  - **用戶端 (client)**：瀏覽器 (browser)，使用 HTTP 請求、接收並顯示網頁物件
  - **伺服器 (server)**：網頁伺服器，使用 HTTP 回應物件
- **HTTP 使用 TCP**：
  - 用戶端在連接埠 80 發起 TCP 連線
  - 伺服器接受 TCP 連線
  - HTTP 訊息在瀏覽器和伺服器之間交換
  - TCP 連線關閉
- **HTTP 是無狀態的 (stateless)**：伺服器不維護過去用戶端請求的資訊

### HTTP 連線的兩種類型

#### 非持續性 HTTP (Non-persistent HTTP)

- 每次 TCP 連線最多傳送**一個物件**
- 下載多個物件需要多次連線
- **回應時間 = 2RTT + 檔案傳輸時間**
  - **RTT (Round Trip Time, 來回時間)**：小封包從用戶端到伺服器再回來的時間
  - 1 個 RTT 建立 TCP 連線 + 1 個 RTT 用於 HTTP 請求和回應
- 問題：每個物件需要 2 個 RTT、每個 TCP 連線都有 OS 額外負擔

#### 持續性 HTTP (Persistent HTTP) - HTTP 1.1

- 伺服器回應後保持連線開啟
- 同一用戶端/伺服器之間的後續 HTTP 訊息透過**同一條 TCP 連線**傳送
- 用戶端一遇到被參照物件就立即傳送請求
- 所有被參照物件最少只需**一個 RTT**

### HTTP 請求訊息 (HTTP Request Message)

- 兩種 HTTP 訊息：**請求 (request)** 和**回應 (response)**
- HTTP 請求訊息為 ASCII（人類可讀格式）
- 格式：**請求行 (request line)** + **標頭行 (header lines)** + **主體 (body)**
- 請求行：`方法 URL 版本`

#### HTTP 方法 (Methods)

| 方法 | 說明 |
|------|------|
| **GET** | 請求物件；可在 URL 中附帶使用者資料（`?` 之後） |
| **POST** | 表單輸入，使用者資料在請求主體 (entity body) 中傳送 |
| **HEAD** | 只請求標頭，不回傳物件本身 |
| **PUT** | 上傳新檔案至伺服器，完全取代指定 URL 的檔案 |

### HTTP 回應狀態碼 (HTTP Response Status Codes)

| 狀態碼 | 說明 |
|--------|------|
| **200 OK** | 請求成功，物件在此訊息中 |
| **301 Moved Permanently** | 物件已永久移動，新位置在 Location 欄位 |
| **400 Bad Request** | 伺服器無法理解請求訊息 |
| **404 Not Found** | 伺服器上找不到請求的文件 |
| **505 HTTP Version Not Supported** | 不支援的 HTTP 版本 |

### Cookie

- HTTP 是無狀態的，但網站和瀏覽器使用 **cookie** 維護交易之間的狀態
- Cookie 的四個元件：
  1. HTTP **回應**訊息中的 cookie 標頭行（`set-cookie:`）
  2. 後續 HTTP **請求**訊息中的 cookie 標頭行（`cookie:`）
  3. 儲存在使用者主機上的 cookie 檔案，由瀏覽器管理
  4. 網站的後端資料庫 (back-end database)
- Cookie 用途：授權 (authorization)、購物車 (shopping carts)、推薦 (recommendations)、使用者工作階段狀態 (user session state)
- 隱私問題：第三方持續性 cookie（追蹤 cookie）可跨多個網站追蹤使用者

### 網頁快取 (Web Caches) / 代理伺服器 (Proxy Servers)

- **目標**：不經過原始伺服器 (origin server) 就滿足用戶端請求
- 運作方式：
  - 瀏覽器將所有 HTTP 請求傳送至快取
  - 若物件在快取中 => 快取直接回傳
  - 若不在 => 快取向原始伺服器請求，快取後再回傳
- Web 快取同時扮演用戶端和伺服器
- **為什麼要使用 Web 快取？**
  - 減少用戶端請求的回應時間
  - 減少機構存取鏈路的流量
  - 讓「弱小」的內容提供者也能有效傳遞內容
- 快取控制標頭：`Cache-Control: max-age=<seconds>` 或 `Cache-Control: no-cache`

### 快取範例計算

- 情境：存取鏈路 1.54 Mbps、RTT 到伺服器 2 秒、物件大小 100K bits、請求速率 15/sec
- **無快取**：存取鏈路使用率 = 0.97 => 延遲數分鐘
- **選項 1 - 升級鏈路至 154 Mbps**：使用率 = 0.0097 => 延遲 ~msecs，但很貴
- **選項 2 - 安裝快取**（假設命中率 0.4）：
  - 40% 請求由快取服務（延遲 ~msecs）
  - 60% 請求至原始伺服器（存取鏈路使用率 = 0.58）
  - 平均端對端延遲 = 0.6 * 2.01 + 0.4 * (~msecs) ≈ **1.2 秒**
  - 比升級鏈路更便宜且效果更好！

### 條件式 GET (Conditional GET)

- **目標**：若快取版本是最新的，就不傳送物件
- 用戶端在請求中指定快取副本的日期：`If-modified-since: <date>`
- 伺服器回應：
  - 物件未修改 => `304 Not Modified`（不含物件）
  - 物件已修改 => `200 OK`（含新物件）

### HTTP/2

- 主要目標：減少多物件 HTTP 請求的延遲
- HTTP 1.1 問題：**隊頭阻塞 (HOL blocking, Head-of-Line blocking)** - 小物件必須等待大物件傳完
- HTTP/2 改進：
  - 根據用戶端指定的物件優先順序 (object priority) 決定傳送順序
  - **推送 (push)** 未請求的物件給用戶端
  - 將物件分成**訊框 (frames)**，交錯排程傳輸以緩解 HOL 阻塞

### HTTP/3

- HTTP/2 仍在單一 TCP 連線上，封包遺失恢復仍會卡住所有物件傳輸
- HTTP/3：在 UDP 上加入安全性、每物件錯誤和壅塞控制（更多管線化 pipelining）

---

## 2.3 電子郵件 (E-mail)：SMTP、IMAP

### 三大元件

1. **使用者代理 (User Agent, UA)**：又稱「郵件閱讀器」，如 Outlook、iPhone mail
2. **郵件伺服器 (Mail Servers)**：
   - **信箱 (mailbox)**：存放使用者的收件訊息
   - **訊息佇列 (message queue)**：存放待送出的郵件
3. **簡易郵件傳輸協定 (SMTP, Simple Mail Transfer Protocol)**

### SMTP [RFC 5321]

- 使用 **TCP** 可靠傳輸郵件，連接埠 **25**
- 直接傳輸：傳送端伺服器（扮演用戶端）到接收端伺服器
- 傳輸三階段：
  1. SMTP 握手 (handshaking)
  2. SMTP 訊息傳輸 (transfer of messages)
  3. SMTP 關閉 (closure)
- 命令/回應互動（類似 HTTP）
  - 命令 (commands)：ASCII 文字
  - 回應 (response)：狀態碼和片語

### SMTP vs. HTTP 比較

| 特點 | HTTP | SMTP |
|------|------|------|
| 傳輸方向 | 用戶端拉 (client pull) | 用戶端推 (client push) |
| 物件封裝 | 每個物件在自己的回應訊息中 | 多個物件在多部分訊息 (multipart message) 中 |
| 連線方式 | 持續/非持續 | 持續連線 (persistent connections) |
| 編碼要求 | 無特別限制 | 訊息（標頭和主體）須為 7-bit ASCII |
| 訊息結尾 | 由 Content-Length 決定 | CRLF.CRLF |

### 郵件存取協定 (Mail Access Protocols)

- **SMTP**：傳遞/儲存郵件至接收端伺服器
- **IMAP (Internet Mail Access Protocol, 網際網路郵件存取協定)** [RFC 3501]：
  - 訊息儲存在伺服器上
  - 提供擷取、刪除、伺服器上已儲存訊息的資料夾管理
- **HTTP**：gmail、Hotmail 等提供基於 Web 的介面，在 SMTP（傳送）和 IMAP（擷取）之上

---

## 2.4 網域名稱系統 (DNS, Domain Name System)

### DNS 概觀

- 網際網路主機/路由器有：
  - **IP 位址** (32 bit) - 用於定址資料報
  - **名稱 (name)** - 如 cs.umass.edu，供人類使用
- **DNS**：**分散式資料庫 (distributed database)**，實作於名稱伺服器 (name servers) 的階層結構中
- **應用層協定 (application-layer protocol)**：主機和 DNS 伺服器通訊以**解析 (resolve)** 名稱

### DNS 提供的服務

| 服務 | 說明 |
|------|------|
| **主機名稱對 IP 位址的轉譯 (hostname-to-IP-address translation)** | 核心功能 |
| **主機別名 (host aliasing)** | 正規名稱 (canonical name) 和別名 (alias names) |
| **郵件伺服器別名 (mail server aliasing)** | 將郵件伺服器名稱對應到 IP |
| **負載分散 (load distribution)** | 複製的 Web 伺服器：多個 IP 位址對應一個名稱 |

### 為什麼不集中式 DNS？

- 單一故障點 (single point of failure)
- 流量過大 (traffic volume)
- 遠端集中式資料庫 (distant centralized database)
- 維護困難 (maintenance)
- **結論：無法擴展！(doesn't scale!)**

### DNS 階層結構

```
根 DNS 伺服器 (Root DNS Servers)
     |
頂級網域 DNS 伺服器 (TLD DNS Servers): .com, .org, .edu, .tw ...
     |
權威 DNS 伺服器 (Authoritative DNS Servers): amazon.com, nyu.edu ...
```

#### 根名稱伺服器 (Root Name Servers)

- 13 個邏輯根名稱伺服器，全球複製多次（美國約 200 台）
- 是無法解析名稱時的「最後求助對象」
- **ICANN (Internet Corporation for Assigned Names and Numbers)** 管理根 DNS 網域
- **DNSSEC**：提供安全性（認證、訊息完整性）

#### 頂級網域伺服器 (TLD, Top-Level Domain Servers)

- 負責 .com、.org、.net、.edu、.aero、.jobs、.museums 及所有頂級國家網域（如 .cn、.uk、.tw、.jp）

#### 權威 DNS 伺服器 (Authoritative DNS Servers)

- 組織自己的 DNS 伺服器，提供該組織已命名主機的權威主機名稱到 IP 對應

#### 本地 DNS 名稱伺服器 (Local DNS Name Server)

- 主機發出 DNS 查詢時，會送至其**本地 DNS 伺服器**
- 本地 DNS 伺服器回覆（從快取或轉送至 DNS 階層）
- 每個 ISP 都有本地 DNS 名稱伺服器
- 不嚴格屬於 DNS 階層

### DNS 名稱解析方式

#### 迭代式查詢 (Iterated Query)

- 被聯繫的伺服器回覆「應該聯繫的伺服器名稱」
- 「我不知道這個名稱，但去問這台伺服器」

#### 遞迴式查詢 (Recursive Query)

- 將名稱解析的負擔交給被聯繫的名稱伺服器
- 可能造成上層伺服器負擔過重

### DNS 快取 (Caching DNS Information)

- 名稱伺服器學到對應關係後會**快取 (cache)** 起來，並立即回應後續查詢
- 快取條目在一段時間後**逾時 (timeout)**（TTL, Time-To-Live）
- TLD 伺服器通常被快取在本地名稱伺服器中
- 快取條目可能**過期 (out-of-date)**

### DNS 資源紀錄 (DNS Records, RR)

DNS 是儲存**資源紀錄 (Resource Records, RR)** 的分散式資料庫

RR 格式：**(name, value, type, ttl)**

| 類型 | name | value | 說明 |
|------|------|-------|------|
| **A** | 主機名稱 | IP 位址 | 主機名稱到 IP 位址對應 |
| **NS** | 網域（如 foo.com） | 該網域權威名稱伺服器的主機名稱 | 網域對應到名稱伺服器 |
| **CNAME** | 別名 (alias name) | 正規名稱 (canonical name) | 別名到正規名稱對應 |
| **MX** | 名稱 | 與該名稱關聯的 SMTP 郵件伺服器名稱 | 郵件交換器紀錄 |

### DNS 協定訊息 (DNS Protocol Messages)

- DNS 查詢 (query) 和回覆 (reply) 訊息格式**相同**
- 訊息標頭：
  - **識別碼 (identification)**：16 位元查詢編號，回覆使用相同編號
  - **旗標 (flags)**：查詢/回覆、遞迴期望、遞迴可用、回覆是否為權威
- 訊息區段：questions、answers、authority、additional info

### DNS 註冊 (Getting Info into DNS)

- 在 **DNS 註冊商 (DNS registrar)** 註冊網域名稱（如 Network Solutions）
- 提供權威名稱伺服器的名稱和 IP 位址
- 註冊商在 .com TLD 伺服器中插入 NS 和 A 紀錄

### DNS 安全 (DNS Security)

- **DDoS 攻擊**：轟炸根伺服器或 TLD 伺服器
- **偽造攻擊 (Spoofing attacks)**：攔截 DNS 查詢，回傳偽造回覆
  - DNS 快取中毒 (DNS cache poisoning)
  - RFC 4033: **DNSSEC** 提供認證服務

---

## 2.5 P2P 應用程式 (P2P Applications)

### 檔案分配：用戶端-伺服器 vs. P2P

#### 用戶端-伺服器方式的分配時間

- 伺服器必須循序傳送 N 份檔案副本
- **D_cs >= max{NF/u_s, F/d_min}** — 隨 N **線性增長**

#### P2P 方式的分配時間

- 伺服器至少上傳一份副本
- **D_P2P >= max{F/u_s, F/d_min, NF/(u_s + Σu_i)}** — 隨 N 線性增長，但**服務容量也隨之增長**

### BitTorrent

- 檔案分成 **256KB 的區塊 (chunks)**
- **洪流 (torrent)**：交換某檔案區塊的對等節點群組
- **追蹤器 (tracker)**：追蹤參與洪流的對等節點
- 新加入的節點：
  - 沒有區塊，隨時間從其他 peer 累積
  - 向 tracker 註冊取得 peer 清單，連接部分 peer（「鄰居 neighbors」）
- **攪動 (churn)**：對等節點可能隨時加入或離開

#### BitTorrent 的請求與傳送策略

- **請求區塊 (Requesting chunks)**：
  - 向每個 peer 詢問其擁有的區塊清單
  - 優先請求**最稀有的區塊 (rarest first)**
- **傳送區塊 (Sending chunks)**：以牙還牙 **(tit-for-tat)**
  - 傳送區塊給目前以**最高速率**傳送給自己的前四個 peer
  - 其他 peer 被阻擋 (choked)
  - 每 10 秒重新評估前四名
  - 每 30 秒隨機選一個 peer 開始傳送（**樂觀性解除阻擋 optimistically unchoke**）

---

## 2.6 影片串流與內容分配網路 (Video Streaming and CDNs)

### 多媒體影片 (Multimedia Video)

- 影片：以固定速率顯示的影像序列（如 24 images/sec）
- 數位影像：像素 (pixels) 陣列，每個像素用位元表示
- 編碼：利用影像**內 (spatial)** 和影像**間 (temporal)** 的冗餘減少位元數
- **CBR (Constant Bit Rate, 固定位元率)**：編碼速率固定
- **VBR (Variable Bit Rate, 可變位元率)**：編碼速率隨內容變化
- 範例：MPEG 1 (1.5 Mbps)、MPEG2 (3-6 Mbps)、MPEG4 (64Kbps-12 Mbps)

### 串流儲存的影片 (Streaming Stored Video)

- 挑戰：
  - 伺服器到用戶端的頻寬會隨時間變化
  - 封包遺失和延遲導致播放延遲或畫質不佳
- **連續播放限制 (continuous playout constraint)**：播放時序必須符合原始時序
- 需要**用戶端緩衝 (client-side buffer)** 來補償網路延遲的抖動 (jitter)

### DASH (Dynamic, Adaptive Streaming over HTTP)

- **伺服器端**：
  - 將影片分成多個區塊 (chunks)
  - 每個區塊以多種不同速率編碼
  - 不同速率的編碼存在不同檔案中
  - 檔案複製到各 CDN 節點
  - **清單檔 (manifest file)**：提供不同區塊的 URL
- **用戶端**：
  - 定期估算伺服器到用戶端的頻寬
  - 參考清單檔，每次請求一個區塊
  - 選擇當前頻寬能支撐的最大編碼速率
  - 可在不同時間點選擇不同速率、不同伺服器
- **「智慧 (intelligence)」在用戶端**：決定**何時 (when)**、**什麼速率 (what encoding rate)**、**從哪裡 (where)** 請求區塊

### 內容分配網路 (CDN, Content Distribution Networks)

- **挑戰**：如何將內容串流給數十萬同時在線的使用者？
- 單一大型「超級伺服器」行不通（單一故障點、壅塞、路徑長）
- **CDN 解決方案**：在多個地理分散的站點儲存/服務影片副本
  - **深入策略 (enter deep)**：將 CDN 伺服器深入許多存取網路，靠近使用者（如 Akamai: 240,000+ 伺服器，120+ 國家）
  - **帶回策略 (bring home)**：較少數量的大型叢集在 POP 點附近（如 Limelight）
- CDN 在 CDN 節點儲存內容副本
- 訂閱者請求內容 => 服務提供者回傳清單檔 (manifest) => 用戶端以最高可支援速率擷取內容
- **OTT (Over The Top)**：在「邊緣」應對壅塞的網際網路

---

## 2.7 Socket 程式設計 (Socket Programming)

### Socket 程式設計概觀

- **目標**：學習如何建立用戶端/伺服器應用程式
- **Socket**：應用程式行程和端對端傳輸協定之間的「門」
- 兩種 socket 類型：
  - **UDP**：不可靠的資料報 (unreliable datagram)
  - **TCP**：可靠的位元組串流導向 (reliable, byte stream-oriented)

---

## 關鍵名詞對照表

| 繁體中文 | English |
|----------|---------|
| 應用層 | Application Layer |
| 用戶端-伺服器模式 | Client-Server Paradigm |
| 點對點架構 | Peer-to-Peer (P2P) Architecture |
| 行程 | Process |
| 行程間通訊 | Inter-Process Communication (IPC) |
| 插座 | Socket |
| 連接埠號碼 | Port Number |
| IP 位址 | IP Address |
| 資料完整性 | Data Integrity |
| 吞吐量 | Throughput |
| 超文本傳輸協定 | HTTP (HyperText Transfer Protocol) |
| 統一資源定位器 | URL (Uniform Resource Locator) |
| 來回時間 | RTT (Round Trip Time) |
| 非持續性 HTTP | Non-persistent HTTP |
| 持續性 HTTP | Persistent HTTP |
| 請求訊息 | Request Message |
| 回應訊息 | Response Message |
| 狀態碼 | Status Code |
| Cookie | Cookie |
| 無狀態 | Stateless |
| 網頁快取 | Web Cache |
| 代理伺服器 | Proxy Server |
| 條件式 GET | Conditional GET |
| 隊頭阻塞 | HOL Blocking (Head-of-Line Blocking) |
| 簡易郵件傳輸協定 | SMTP (Simple Mail Transfer Protocol) |
| 使用者代理 | User Agent (UA) |
| 信箱 | Mailbox |
| 訊息佇列 | Message Queue |
| 網際網路郵件存取協定 | IMAP (Internet Mail Access Protocol) |
| 網域名稱系統 | DNS (Domain Name System) |
| 名稱伺服器 | Name Server |
| 分散式資料庫 | Distributed Database |
| 根 DNS 伺服器 | Root DNS Server |
| 頂級網域 | TLD (Top-Level Domain) |
| 權威 DNS 伺服器 | Authoritative DNS Server |
| 本地 DNS 伺服器 | Local DNS Server |
| 迭代式查詢 | Iterated Query |
| 遞迴式查詢 | Recursive Query |
| 資源紀錄 | Resource Record (RR) |
| 正規名稱 | Canonical Name |
| 別名 | Alias Name |
| DNS 註冊商 | DNS Registrar |
| DNS 快取中毒 | DNS Cache Poisoning |
| 洪流 | Torrent |
| 追蹤器 | Tracker |
| 區塊 | Chunk |
| 以牙還牙 | Tit-for-Tat |
| 樂觀性解除阻擋 | Optimistically Unchoke |
| 自我擴展性 | Self Scalability |
| 攪動 | Churn |
| 固定位元率 | CBR (Constant Bit Rate) |
| 可變位元率 | VBR (Variable Bit Rate) |
| 空間編碼 | Spatial Coding |
| 時間編碼 | Temporal Coding |
| 連續播放限制 | Continuous Playout Constraint |
| 用戶端緩衝 | Client-side Buffer |
| 抖動 | Jitter |
| 動態自適應串流 | DASH (Dynamic Adaptive Streaming over HTTP) |
| 清單檔 | Manifest File |
| 內容分配網路 | CDN (Content Distribution Network) |
| 傳輸層安全性 | TLS (Transport Layer Security) |
| 流量控制 | Flow Control |
| 壅塞控制 | Congestion Control |
| 連線導向 | Connection-oriented |
| 可靠傳輸 | Reliable Transport |
| 開放協定 | Open Protocol |
| 私有協定 | Proprietary Protocol |

---

## 重點公式整理

| 公式 | 說明 |
|------|------|
| **非持續性 HTTP 回應時間 = 2RTT + 檔案傳輸時間** | 每個物件需要 2 個 RTT |
| **D_cs >= max{NF/u_s, F/d_min}** | 用戶端-伺服器檔案分配時間下界 |
| **D_P2P >= max{F/u_s, F/d_min, NF/(u_s + Σu_i)}** | P2P 檔案分配時間下界 |
| **存取鏈路使用率 = 到達速率 / 鏈路容量** | 用於快取計算 |
