# Homework 1 答案整理（Chapter 1 & 2 前半）

---

## Q1. A network consists of network edge, network core, and access networks. What is the purpose of network-edge devices?

網路邊緣裝置 (network-edge devices) 即**主機 (hosts) / 終端系統 (end systems)**，包含用戶端 (clients) 和伺服器 (servers)。其目的是：

- 執行網路應用程式 (network applications)
- 作為資料的來源 (source) 或目的地 (destination)
- 伺服器通常位於資料中心 (data centers)

---

## Q2. Please list three guided mediums of access networks.

三種導引式媒介 (guided media)：

1. **雙絞線 (Twisted Pair, TP)**：兩條絕緣銅線（Cat 5: 100 Mbps/1 Gbps、Cat 6: 10 Gbps）
2. **同軸電纜 (Coaxial Cable)**：兩個同心銅導體，雙向，寬頻，每頻道 100's Mbps
3. **光纖 (Fiber Optic Cable)**：玻璃光纖攜帶光脈衝，高速（10's-100's Gbps），低錯誤率，不受電磁干擾

---

## Q3. What is the technology used by both DSL and cable modem for enabling Internet access?

兩者都使用**分頻多工 (Frequency Division Multiplexing, FDM)** 技術：

- 將不同頻率的頻帶分配給不同的通道（語音、資料、影片等），讓語音和資料可以在同一條線路上同時傳輸
- **DSL**：透過既有電話線，在不同頻率上傳輸語音和資料至電信局 DSLAM
- **Cable modem**：透過共享的有線電視電纜，在不同頻帶傳輸影片和資料

---

## Q4. What is the equation for calculating transmission delay?

$$d_{trans} = \frac{L}{R}$$

- **L**：封包長度 (packet length)，單位為 bits
- **R**：鏈路傳輸速率 (link transmission rate / bandwidth)，單位為 bits/sec (bps)
- **d_trans**：傳輸延遲 (transmission delay)，即將整個封包推入鏈路所需的時間

---

## Q5. Please draw the architecture of DSL internet access.

```
家庭 (Home)                           電信局 (Central Office)
┌──────────┐                         ┌──────────┐
│ 電話 ─┐   │    DSL 電話線            │          │    電話網路
│       ├─分歧器──────────────────────→  DSLAM  ─┼──→ (Telephone
│ 電腦 ─┤   │  (既有電話線)             │          │     Network)
│   DSL  │   │  語音和資料在不同頻率     │   DSL    │
│  modem │   │  透過專用線路傳輸         │  Access  │    ISP
│        │   │                        │Multiplexer├──→ Internet
└────────┘                           └──────────┘
```

- 使用**既有的電話線 (existing telephone line)** 至電信局 (central office) 的 DSLAM
- 資料透過 DSL 電話線傳至網際網路
- 語音透過 DSL 電話線傳至電話網路
- **專用線路 (dedicated line)**，非共享
- 下行 (downstream) 24-52 Mbps，上行 (upstream) 3.5-16 Mbps

---

## Q6. What is "store and forward"?

**儲存轉送 (Store and Forward)**：

- 在封包交換 (packet switching) 中，**整個封包 (entire packet) 必須完全到達路由器後**，路由器才能將它轉送到下一條鏈路
- 傳輸一個 L-bit 封包進入速率 R bps 的鏈路需要 **L/R 秒**的傳輸延遲
- 範例：L = 10 Kbits, R = 100 Mbps => 單跳傳輸延遲 = 0.1 msec

---

## Q7. Please list the consequences of network congestion for packet switching.

網路壅塞 (network congestion) 對封包交換的影響：

1. **排隊延遲 (Queueing Delay)**：封包在路由器的輸出鏈路緩衝區中排隊等待傳輸，當到達速率超過鏈路傳輸速率時，佇列會增長
2. **封包遺失 (Packet Loss)**：當路由器的記憶體（緩衝區 buffer）滿了，新到達的封包會被**丟棄 (dropped)**
3. **延遲增加**：流量強度 (traffic intensity) La/R 趨近 1 時，平均排隊延遲趨近無限大
4. **重傳負擔**：遺失的封包可能需要由來源終端系統重傳，造成額外的網路流量

---

## Q8. Please list the pros and cons of circuit switching and packet switching.

| | 電路交換 (Circuit Switching) | 封包交換 (Packet Switching) |
|---|---|---|
| **優點** | - 保證效能 (guaranteed performance)，專用資源<br>- 適合連續性、穩定的通訊（如傳統電話） | - 適合突發性資料 (bursty data)<br>- 資源共享 (resource sharing)，更多使用者<br>- 較簡單，不需通話設定 (no call setup) |
| **缺點** | - 電路閒置時浪費資源（不共享）<br>- 需要通話設定<br>- 使用者數量受限（如 1 Gbps 鏈路只能支援 10 個 100 Mbps 使用者） | - 可能發生過度壅塞 (excessive congestion)<br>- 封包延遲和遺失<br>- 需要可靠資料傳輸和壅塞控制協定 |

---

## Q9. Please list and describe the definitions of four sources of packet delay.

**d_nodal = d_proc + d_queue + d_trans + d_prop**

| 延遲來源 | 定義 | 量級 |
|---------|------|------|
| **處理延遲 (Processing Delay, d_proc)** | 檢查位元錯誤、決定輸出鏈路 | 通常 < 微秒 (microsecs) |
| **排隊延遲 (Queueing Delay, d_queue)** | 在輸出鏈路等待傳輸的時間，取決於路由器的壅塞程度 | 變動大，可從微秒到毫秒 |
| **傳輸延遲 (Transmission Delay, d_trans)** | 將 L-bit 封包推入速率 R bps 的鏈路所需時間。**d_trans = L/R** | 取決於封包大小和鏈路速率 |
| **傳播延遲 (Propagation Delay, d_prop)** | 位元在實體鏈路中傳播的時間。**d_prop = d/s**（d: 鏈路長度, s: 傳播速度 ~2×10⁸ m/sec） | 取決於實體距離 |

---

## Q10. Please list Internet five-layer protocol stack.

由上而下：

| 層次 | 名稱 | 功能 | 協定範例 | 資料單元 |
|------|------|------|---------|---------|
| 5 | **應用層 (Application)** | 支援網路應用程式 | HTTP, SMTP, DNS, IMAP | 訊息 (Message) |
| 4 | **傳輸層 (Transport)** | 行程對行程的資料傳輸 | TCP, UDP | 區段 (Segment) |
| 3 | **網路層 (Network)** | 從來源到目的地的資料報路由 | IP, 路由協定 | 資料報 (Datagram) |
| 2 | **鏈路層 (Link)** | 相鄰網路元素間的資料傳輸 | Ethernet, 802.11 (WiFi), PPP | 訊框 (Frame) |
| 1 | **實體層 (Physical)** | 線路上的位元傳輸 | — | 位元 (Bits) |

---

## Q11. How and why packet headers are stacked?

### How（如何）— 封裝 (Encapsulation)：

每一層向下傳遞資料時，會在上層傳來的資料前面加上自己的**標頭 (header)**：

```
應用層：M（訊息）
傳輸層：H_t | M（區段 segment）
網路層：H_n | H_t | M（資料報 datagram）
鏈路層：H_l | H_n | H_t | M（訊框 frame）
```

### Why（為什麼）：

- 每一層的標頭包含該層實作其服務所需的資訊（如來源/目的地位址、序號、錯誤檢查等）
- **模組化 (modularization)**：各層獨立運作，改變某層的實作不影響其他層
- 接收端每一層讀取並移除自己的標頭，將資料傳給上層

---

## Q12. Please list the four network-service requirements of network applications.

1. **資料完整性 (Data Integrity)**：某些應用（檔案傳輸、網頁交易）需要 100% 可靠傳輸；其他應用（音訊）可容忍部分遺失
2. **吞吐量 (Throughput)**：某些應用（多媒體）需要最低吞吐量才能「有效」；彈性應用 (elastic apps) 可利用任何可用吞吐量
3. **時序 (Timing)**：某些應用（網路電話、互動遊戲）需要低延遲
4. **安全性 (Security)**：加密 (encryption)、資料完整性 (data integrity)、端點認證

---

## Q13. Please compare the client-server and peer-to-peer architectures for network applications.

| 特點 | 用戶端-伺服器 (Client-Server) | 點對點 (Peer-to-Peer, P2P) |
|------|---|---|
| **伺服器** | 永遠運行 (always-on)、固定 IP | 沒有永遠運行的伺服器 |
| **通訊方式** | 用戶端與伺服器通訊，用戶端之間不直接通訊 | 任意終端系統直接通訊 |
| **IP 位址** | 伺服器固定 IP，用戶端可能動態 IP | Peer 間歇性連線，IP 位址會改變 |
| **擴展性** | 需要增加伺服器來擴展（如使用資料中心） | 自我擴展 (self scalability)：新 peer 同時帶來容量和需求 |
| **管理** | 相對簡單 | 複雜（peer 隨時加入/離開） |
| **範例** | HTTP, IMAP, FTP | BitTorrent, P2P 檔案分享 |

---

## Q14. Please describe the difference between non-persistent and persistent HTTP.

| 特點 | 非持續性 HTTP (Non-persistent) | 持續性 HTTP (Persistent, HTTP 1.1) |
|------|---|---|
| **TCP 連線** | 每個物件開一條新的 TCP 連線 | 多個物件透過同一條 TCP 連線傳送 |
| **物件傳送** | 一條連線最多傳送一個物件 | 伺服器回應後保持連線開啟 |
| **回應時間** | 每個物件需要 **2 RTT + 檔案傳輸時間** | 所有被參照物件最少只需**一個 RTT** |
| **額外負擔** | 每個 TCP 連線都有 OS 額外負擔 | 減少 OS 額外負擔 |
| **並行** | 瀏覽器常開多個平行 TCP 連線 | 用戶端一遇到被參照物件就立即請求 |

---

## Q15. What is the URL method for uploading form input in HTTP?

使用 **POST 方法 (POST method)**：

- 網頁常包含表單輸入 (form input)
- 使用者輸入的資料從用戶端傳送至伺服器，放在 HTTP POST 請求訊息的**實體主體 (entity body)** 中

另外，也可使用 **GET 方法**傳送表單資料：
- 使用者資料包含在 HTTP GET 請求訊息的 **URL 欄位**中（在 `?` 之後）
- 例如：`www.somesite.com/animalsearch?monkeys&banana`

---

## Q16. Please describe the components of HTTP cookies.

HTTP Cookie 的四個元件：

1. **HTTP 回應訊息的 cookie 標頭行**：伺服器在回應中設定 `Set-cookie: <ID>` 標頭
2. **後續 HTTP 請求訊息的 cookie 標頭行**：用戶端在之後的請求中附帶 `cookie: <ID>` 標頭
3. **儲存在使用者主機上的 cookie 檔案**：由使用者的瀏覽器管理
4. **網站的後端資料庫 (back-end database)**：記錄對應 cookie ID 的使用者資訊

---

## Q17. Please list the benefits of web cache.

網頁快取 (Web Cache / Proxy Server) 的好處：

1. **減少用戶端請求的回應時間 (reduce response time)**：快取離用戶端更近
2. **減少機構存取鏈路的流量 (reduce traffic on institution's access link)**：快取命中的請求不需經過存取鏈路
3. **讓資源較少的內容提供者也能有效傳遞內容 (enable "poor" content providers to effectively deliver content)**：網際網路中充滿了快取

---

## Q18. Please describe how HTTP/2 mitigates HOL blocking.

HTTP 1.1 的問題：伺服器以 FCFS（先到先服務）順序回應，小物件必須等待大物件傳輸完成，造成**隊頭阻塞 (HOL blocking, Head-of-Line blocking)**。

HTTP/2 的緩解方式：

1. **將物件分成訊框 (frames)**：不再一次傳送整個物件，而是將物件切割成小的訊框
2. **交錯排程訊框傳輸 (interleave frame transmission)**：不同物件的訊框交替傳送
3. **用戶端指定物件優先順序 (client-specified object priority)**：不必按照 FCFS，可根據優先順序決定傳送順序
4. **伺服器推送 (server push)**：可主動推送未被請求的物件給用戶端

結果：小物件 (O₂, O₃, O₄) 可以快速送達，大物件 (O₁) 只是稍微延遲。

---

---

# Homework 2 答案整理（Chapter 2 後半）

---

## Q1. Please list the hierarchy of DNS databases.

DNS 資料庫的階層結構（由上而下）：

1. **根 DNS 伺服器 (Root DNS Servers)**：13 個邏輯根伺服器，全球複製多次，是最後的求助對象
2. **頂級網域 DNS 伺服器 (TLD DNS Servers, Top-Level Domain)**：負責 .com、.org、.net、.edu 及各國頂級網域（.tw、.jp、.uk 等）
3. **權威 DNS 伺服器 (Authoritative DNS Servers)**：組織自己的 DNS 伺服器，提供該組織已命名主機的權威主機名稱到 IP 位址對應

另外還有**本地 DNS 伺服器 (Local DNS Server)**，不嚴格屬於階層結構，但每個 ISP 都有，是主機 DNS 查詢的第一站。

---

## Q2. Why does DNS use distributed databases?

如果使用集中式 DNS，會有以下問題：

1. **單一故障點 (Single point of failure)**：該伺服器故障就全部停擺
2. **流量過大 (Traffic volume)**：所有 DNS 查詢集中在一台伺服器
3. **遠端集中式資料庫 (Distant centralized database)**：離遠端使用者太遠，延遲太高
4. **維護困難 (Maintenance)**：所有紀錄集中管理不實際

**結論：集中式無法擴展 (doesn't scale!)**

使用分散式資料庫的好處：約十億筆紀錄、每日處理數兆次查詢、組織化地去中心化、各組織負責自己的紀錄、兼顧可靠性與安全性。

---

## Q3. Please describe the procedures of iterated and recursive query for a local name server.

### 迭代式查詢 (Iterated Query)

範例：engineering.nyu.edu 的主機想查詢 gaia.cs.umass.edu 的 IP

1. 主機向**本地 DNS 伺服器** (dns.nyu.edu) 發出查詢
2. 本地 DNS 向**根 DNS 伺服器**查詢
3. 根 DNS 回覆：「我不知道，但去問 .edu TLD 伺服器」
4. 本地 DNS 向 **TLD DNS 伺服器**查詢
5. TLD 回覆：「我不知道，但去問 dns.cs.umass.edu 權威伺服器」
6. 本地 DNS 向**權威 DNS 伺服器** (dns.cs.umass.edu) 查詢
7. 權威 DNS 回覆 gaia.cs.umass.edu 的 IP 位址
8. 本地 DNS 將結果回傳給主機

**特點**：被聯繫的伺服器回覆「應該聯繫的伺服器名稱」，查詢負擔在本地 DNS。

### 遞迴式查詢 (Recursive Query)

1. 主機向本地 DNS 發出查詢
2. 本地 DNS 向根 DNS 查詢
3. 根 DNS 向 TLD DNS 查詢
4. TLD DNS 向權威 DNS 查詢
5. 權威 DNS 回覆給 TLD
6. TLD 回覆給根
7. 根回覆給本地 DNS
8. 本地 DNS 回覆給主機

**特點**：將名稱解析的負擔交給被聯繫的名稱伺服器，可能造成上層伺服器負擔過重。

---

## Q4. Please list and describe four typical types of DNS resource records.

DNS 資源紀錄 (Resource Records, RR) 格式：**(name, value, type, ttl)**

| 類型 | name | value | 說明 |
|------|------|-------|------|
| **Type=A** | 主機名稱 (hostname) | IP 位址 | 主機名稱到 IP 位址的直接對應 |
| **Type=NS** | 網域名稱（如 foo.com） | 該網域權威名稱伺服器的主機名稱 | 用於找到負責該網域的 DNS 伺服器 |
| **Type=CNAME** | 別名 (alias name) | 正規名稱 (canonical name) | 將別名對應到真正的主機名稱。如 www.ibm.com 其實是 servereast.backup2.ibm.com |
| **Type=MX** | 名稱 | 與該名稱關聯的 SMTP 郵件伺服器名稱 | 郵件交換器紀錄，用於找到處理電子郵件的伺服器 |

---

## Q5. How does dynamic DNS avoid caching old IP addresses?

DNS 透過 **TTL (Time-To-Live, 存活時間)** 機制避免快取過期的 IP 位址：

- 每筆 DNS 資源紀錄都有一個 **TTL 值**，指定該紀錄可以被快取多長時間
- 快取條目在 TTL 到期後會**逾時消失 (timeout/expire)**
- 一旦 TTL 過期，本地 DNS 伺服器會重新向權威 DNS 伺服器查詢，取得最新的 IP 位址
- 對於經常變動 IP 的主機，可設定較短的 TTL 值
- 這是一種**盡力而為的名稱到位址轉譯 (best-effort name-to-address translation)**，在所有 TTL 過期前可能會有短暫的不一致

---

## Q6. Please describe the main ideas of BT to accelerate file distribution.

BitTorrent 加速檔案分配的主要思想：

1. **檔案分塊 (File chunking)**：將檔案分成 256KB 的區塊 (chunks)，便於平行傳輸
2. **P2P 架構**：每個 peer 在下載的同時也上傳區塊給其他 peer，利用所有 peer 的上傳頻寬
3. **稀有優先 (Rarest first)**：優先請求最稀有的區塊，確保區塊在洪流中均勻分布
4. **以牙還牙 (Tit-for-tat)**：優先傳送給提供最高上傳速率的前四個 peer，激勵 peer 貢獻上傳頻寬
5. **樂觀性解除阻擋 (Optimistic unchoking)**：每 30 秒隨機選擇一個新的 peer 開始傳送，讓新 peer 有機會加入
6. **自我擴展性 (Self scalability)**：新加入的 peer 同時帶來新的服務容量和需求，peer 越多分配越快

---

## Q7. How does BT deal with the problem that a peer selfishly leaves after finishing downloading?

BitTorrent 透過 **以牙還牙 (tit-for-tat)** 機制來處理這個問題：

1. **激勵機制**：Alice 只傳送區塊給目前以最高速率傳送給她的前四個 peer。如果某個 peer 不上傳，就不會從 Alice 得到區塊（被 choked）。這激勵 peer 在下載時持續上傳
2. **樂觀性解除阻擋 (Optimistic unchoking)**：每 30 秒隨機選一個新 peer 開始傳送，讓新加入的 peer 有機會開始交換
3. **稀有優先策略**：確保區塊在洪流中廣泛分布，即使某些 peer 離開，其他 peer 仍持有這些區塊
4. **實際上**：一旦 peer 擁有完整檔案後，可以自私地離開 (selfishly leave) 或利他地留下 (altruistically remain)。BitTorrent 的設計使得即使有 peer 離開，只要有足夠的活躍 peer，檔案仍可被分配

---

## Q8. Please describe the operations of DASH.

### Server-Side Operations（伺服器端操作）：

1. 將影片檔案分成多個**區塊 (chunks)**
2. 每個區塊以**多種不同位元率 (multiple different rates)** 編碼
3. 不同速率的編碼存在不同的檔案中
4. 檔案**複製到各 CDN 節點**
5. 建立**清單檔 (manifest file)**：提供不同區塊在不同速率版本的 URL

### Client-Side Operations（用戶端操作）：

1. **定期估算 (periodically estimates)** 伺服器到用戶端的可用頻寬
2. 參考**清單檔 (manifest)**，每次請求一個區塊
3. 選擇當前頻寬能支撐的**最大編碼速率 (maximum coding rate)**
4. 可在不同時間點選擇**不同的編碼速率**（根據可用頻寬）
5. 可從**不同的伺服器**請求區塊（選擇離自己「近」或頻寬高的伺服器）
6. 用戶端自行決定：**何時 (when)** 請求（避免緩衝區飢餓或溢出）、**什麼速率 (what encoding rate)**、**從哪裡 (where)** 請求

---

## Q9. Please describe the functions of CDNs.

**內容分配網路 (CDN, Content Distribution Network)** 的功能：

1. **內容複製與儲存**：在多個地理分散的站點（CDN 節點）儲存影片/內容的副本
2. **就近服務**：將內容從離使用者最近的 CDN 節點提供，減少延遲
3. **負載分散**：避免單一伺服器的壅塞和單一故障點
4. **兩種部署策略**：
   - **深入策略 (Enter deep)**：將 CDN 伺服器深入許多存取網路，靠近使用者（如 Akamai: 240,000+ 伺服器，120+ 國家）
   - **帶回策略 (Bring home)**：較少數量（10's）的大型叢集在 POP 點附近（如 Limelight）
5. **利用 manifest file**：訂閱者請求內容時，服務提供者回傳清單檔，用戶端根據清單檔以最高可支援速率從合適的 CDN 節點擷取內容

---

## Q10. Please explain which operations of CDNs rely on DNS.

CDN 依賴 DNS 的操作：

1. **內容請求重導向 (Content request redirection)**：
   - 當用戶端請求內容（如影片）時，內容提供者的 DNS 將用戶端的請求**重導向至 CDN 的 DNS 伺服器**
   - CDN 的 DNS 伺服器根據用戶端的位置（透過用戶端本地 DNS 伺服器的 IP 位址判斷），回傳**最適合的 CDN 節點 IP 位址**

2. **最佳節點選擇 (Best CDN node selection)**：
   - CDN 使用 DNS 來決定將用戶端導向哪個 CDN 節點
   - 通常選擇地理上最近或網路條件最好的節點
   - 透過 DNS 的 CNAME 紀錄，將內容提供者的網域名稱映射到 CDN 網域名稱

3. **流程範例**：
   - 用戶端查詢 `video.netcinema.com` => 得到 CNAME 指向 CDN 域名（如 `a]1105.akamai.net`）
   - CDN 的權威 DNS 根據用戶端本地 DNS 的 IP 位址，回傳最近 CDN 節點的 IP
   - 用戶端直接從該 CDN 節點下載內容
