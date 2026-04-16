# 模擬考題（簡答題版）- 電腦網路 Ch1 & Ch2

> 共 30 題，依章節與考點分類，全部簡答題

---

## Chapter 1 範圍

---

### Q1. What is the purpose of network-edge devices? Please also list examples.
**網路邊緣裝置的用途是什麼？請列舉例子。**

**答：**
Network-edge devices 即 hosts (end systems)，包含 clients 和 servers。其目的是在網際網路的「邊緣」執行網路應用程式 (network applications)，作為資料的來源 (source) 或目的地 (destination)。

範例：桌上型電腦、筆電、手機、IoT 裝置、Web server（常位於 data centers）。

---

### Q2. Please list three guided mediums and describe their characteristics.
**請列出三種導引式媒介並描述其特性。**

**答：**
1. **Twisted Pair (TP, 雙絞線)**：兩條絕緣銅線。Cat 5 支援 100 Mbps / 1 Gbps Ethernet，Cat 6 支援 10 Gbps Ethernet。
2. **Coaxial Cable (同軸電纜)**：兩個同心銅導體。雙向 (bidirectional)，寬頻 (broadband)，支援多頻道每頻道數百 Mbps。
3. **Fiber Optic Cable (光纖)**：玻璃光纖攜帶光脈衝。高速 (10s-100s Gbps)，低錯誤率，不受電磁干擾 (immune to electromagnetic noise)，repeaters 可相隔很遠。

---

### Q3. What is the technology used by both DSL and cable modem for enabling Internet access? Please explain how it works.
**DSL 和 cable modem 都使用什麼技術來提供上網服務？請解釋運作方式。**

**答：**
兩者都使用 **FDM (Frequency Division Multiplexing, 分頻多工)**。FDM 將不同的頻率頻帶 (frequency bands) 分配給不同的通道，使語音和資料可以在同一條實體線路上同時傳輸而互不干擾。

- DSL：在既有電話線上，用不同頻率分別傳輸語音（送至電話網路）和資料（送至網際網路），是 dedicated（專用）線路。
- Cable modem：在有線電視電纜上，用不同頻帶分別傳輸影片頻道和資料，是 shared（共享）線路。

---

### Q4. What is the equation for calculating transmission delay? What is the equation for propagation delay? Please explain the difference between them.
**傳輸延遲和傳播延遲的公式各是什麼？請解釋兩者的差異。**

**答：**
- **Transmission delay**: d_trans = **L / R** (L: packet length in bits, R: link transmission rate in bps)
- **Propagation delay**: d_prop = **d / s** (d: length of physical link, s: propagation speed ~2×10⁸ m/sec)

差異：
- Transmission delay 是將整個封包「推入」鏈路所需的時間，取決於**封包大小**和**鏈路速率**。
- Propagation delay 是位元在實體鏈路中「傳播」的時間，取決於**實體距離**和**傳播速度**。
- 兩者是完全不同的概念：transmission delay 與距離無關，propagation delay 與封包大小無關。

---

### Q5. What is "store and forward"? Please explain with an example.
**什麼是「儲存轉送」？請舉例說明。**

**答：**
Store and forward 是封包交換 (packet switching) 的運作方式：**整個封包 (entire packet) 必須完全到達路由器後**，路由器才能開始將它轉送到下一條鏈路。

範例：若 L = 10 Kbits, R = 100 Mbps，則 one-hop transmission delay = L/R = 10,000 / 100,000,000 = 0.1 msec。路由器必須等待整個 10 Kbit 封包到達後，才能轉送至下一條鏈路。

---

### Q6. Please list the consequences of network congestion for packet switching.
**請列出封包交換中網路壅塞的後果。**

**答：**
1. **Queueing delay (排隊延遲)**：封包在路由器的 output buffer 中排隊等待傳輸，當 arrival rate 暫時超過 output link capacity 時，queue 增長。
2. **Packet loss (封包遺失)**：當路由器的 buffer (memory) 滿了，新到達的封包被 dropped (丟棄)。
3. **Increased delay**：traffic intensity (La/R) 趨近 1 時，平均 queueing delay 趨近無限大。
4. **Retransmission overhead (重傳負擔)**：遺失的封包可能需要由 source end system 重傳，造成額外的網路流量。

---

### Q7. Please list the pros and cons of circuit switching and packet switching.
**請列出電路交換和封包交換的優缺點。**

**答：**

**Circuit Switching：**
- 優點：Guaranteed performance（保證效能），dedicated resources（專用資源），適合持續穩定通訊（如傳統電話）
- 缺點：Circuit segment idle 時浪費資源 (no sharing)；使用者數量受限；需要 call setup

**Packet Switching：**
- 優點：適合 bursty data；resource sharing 可支援更多使用者；simpler, no call setup
- 缺點：可能發生 excessive congestion；packet delay and loss；需要 protocols for reliable data transfer and congestion control

---

### Q8. Please list and describe the four sources of packet delay, including their formulas.
**請列出並描述封包延遲的四個來源，包含公式。**

**答：**

**d_nodal = d_proc + d_queue + d_trans + d_prop**

1. **Processing delay (d_proc)**：檢查 bit errors、決定 output link。通常 < microsecs。
2. **Queueing delay (d_queue)**：在 output link 等待傳輸的時間。取決於 router 的 congestion level。
3. **Transmission delay (d_trans = L/R)**：將 L-bit packet 推入 R bps 的 link 所需時間。
4. **Propagation delay (d_prop = d/s)**：bit 在 physical link 中傳播的時間。d 為 link 長度，s 為傳播速度 (~2×10⁸ m/sec)。

---

### Q9. Please list the Internet five-layer protocol stack and describe the function and PDU name of each layer.
**請列出網際網路五層協定堆疊，並描述每層的功能和資料單元名稱。**

**答：**

| Layer | Name | Function | PDU |
|-------|------|----------|-----|
| 5 | Application | 支援網路應用程式 (HTTP, SMTP, DNS) | Message |
| 4 | Transport | Process-to-process data transfer (TCP, UDP) | Segment |
| 3 | Network | Routing datagrams from source to destination (IP) | Datagram |
| 2 | Link | Data transfer between neighboring network elements (Ethernet, WiFi) | Frame |
| 1 | Physical | Bits "on the wire" | Bits |

---

### Q10. How and why are packet headers stacked (encapsulation)?
**封包標頭如何堆疊（封裝）？為什麼要這樣做？**

**答：**

**How：** 每一層向下傳遞資料時，在上層資料前面加上自己的 header：
- Application: M (message)
- Transport: H_t | M (segment)
- Network: H_n | H_t | M (datagram)
- Link: H_l | H_n | H_t | M (frame)

**Why：**
- 每層的 header 包含該層實作其服務所需的資訊（如 source/destination address、sequence number、error check）
- **Modularization（模組化）**：各層獨立運作，改變某層的 implementation 不影響其他層（transparent to rest of system）
- 接收端每一層讀取並移除自己的 header，將資料傳給上層

補充：Switch 只有 Link + Physical 層；Router 有 Network + Link + Physical 層。

---

### Q11. Please explain the concept of throughput and bottleneck link.
**請解釋吞吐量和瓶頸鏈路的概念。**

**答：**
- **Throughput（吞吐量）**：bits 從 sender 到 receiver 的速率 (bits/time unit)。分為 instantaneous throughput（某一時間點的速率）和 average throughput（較長時間的平均速率）。
- **Bottleneck link（瓶頸鏈路）**：端對端路徑上限制 end-end throughput 的鏈路。
- End-end throughput = min(R_s, R_c)。若多條連線共享 backbone link R，每條連線的 throughput = min(R_c, R_s, R/N)。
- 實際上 R_c 或 R_s 通常是 bottleneck。

---

### Q12. What is traffic intensity? What happens when it approaches 1 or exceeds 1?
**什麼是流量強度？當它接近 1 或超過 1 時會發生什麼？**

**答：**
**Traffic Intensity = La/R**（L: packet length, a: average packet arrival rate, R: link bandwidth）

- La/R ≈ 0：average queueing delay 很小
- La/R → 1：average queueing delay 非常大（急遽增長）
- La/R > 1：到達的 work 超過可服務的量，average delay 趨近**無限大**！封包持續累積，queue 不斷增長。

---

## Chapter 2 範圍

---

### Q13. Please compare the client-server and peer-to-peer architectures for network applications.
**請比較用戶端-伺服器和點對點架構。**

**答：**

**Client-Server：**
- Server: always-on host, permanent IP address, often in data centers
- Clients: contact server, may be intermittently connected, may have dynamic IPs
- Clients do NOT communicate directly with each other
- Examples: HTTP, IMAP, FTP

**P2P：**
- No always-on server
- Arbitrary end systems directly communicate
- Self scalability：new peers bring new service capacity AND new demands
- Peers are intermittently connected, change IP addresses → complex management
- Examples: BitTorrent, P2P file sharing

---

### Q14. Please list the four network-service requirements of network applications.
**請列出網路應用程式的四大傳輸服務需求。**

**答：**
1. **Data integrity（資料完整性）**：某些 apps (file transfer, web transactions) 需要 100% reliable；其他 (audio) 可容忍 some loss
2. **Throughput（吞吐量）**：某些 apps (multimedia) 需要 minimum throughput；elastic apps 可用多少算多少
3. **Timing（時序）**：某些 apps (Internet telephony, interactive games) 需要 low delay
4. **Security（安全性）**：encryption, data integrity, end-point authentication

---

### Q15. Please describe the difference between non-persistent and persistent HTTP.
**請描述非持續性 HTTP 和持續性 HTTP 的差異。**

**答：**

**Non-persistent HTTP：**
- 每個物件開一條新的 TCP connection
- 一條連線最多傳送一個 object
- 每個物件需要 **2 RTT + file transmission time**
- 每個 TCP connection 都有 OS overhead
- Browsers 常開多個 parallel TCP connections

**Persistent HTTP (HTTP 1.1)：**
- Server 回應後保持 connection open
- 多個 objects 透過**同一條 TCP connection** 傳送
- Client 一遇到 referenced object 就立即 send request
- 所有 referenced objects 最少只需 **1 RTT**
- 減少 OS overhead

---

### Q16. What is the URL method for uploading form input in HTTP? Please describe both methods.
**HTTP 中上傳表單輸入的方法是什麼？請描述兩種方式。**

**答：**

1. **POST method**：使用者輸入的資料放在 HTTP request message 的 **entity body** 中傳送至 server。適合大量或敏感資料。

2. **GET method**（用於傳送資料時）：使用者資料包含在 URL field 中，在 `?` 之後。例如：`www.somesite.com/animalsearch?monkeys&banana`。資料會顯示在 URL 中。

---

### Q17. Please describe the four components of HTTP cookies.
**請描述 HTTP Cookie 的四個元件。**

**答：**
1. HTTP **response** message 中的 cookie header line：`Set-cookie: 1678`
2. 後續 HTTP **request** message 中的 cookie header line：`cookie: 1678`
3. 儲存在 user's host 上的 **cookie file**，由 browser 管理
4. Web site 的 **back-end database**，記錄對應 cookie ID 的使用者資訊

用途：authorization、shopping carts、recommendations、user session state。
隱私問題：third-party persistent cookies (tracking cookies) 可跨多個 web sites 追蹤使用者。

---

### Q18. Please list the benefits of web cache and explain how Conditional GET works.
**請列出 Web Cache 的好處，並解釋 Conditional GET 的運作方式。**

**答：**

**Web Cache 的好處：**
1. **Reduce response time** for client request：cache 離 client 更近
2. **Reduce traffic** on an institution's access link
3. **Enable "poor" content providers** to more effectively deliver content

**Conditional GET：**
- Goal：若 cache 已有最新版本，就不傳送 object
- Client 在 request 中指定 cached copy 的日期：`If-modified-since: <date>`
- Server 若物件**未修改** → 回 `304 Not Modified`（不含 object，省頻寬）
- Server 若物件**已修改** → 回 `200 OK`（含新 object）

---

### Q19. Please describe how HTTP/2 mitigates HOL blocking.
**請描述 HTTP/2 如何緩解 HOL blocking。**

**答：**

**問題（HTTP 1.1）：** Server 以 FCFS (first-come-first-served) 順序回應 GET requests，小 object 必須等大 object 傳完才能送出 → **HOL (Head-of-Line) blocking**。

**HTTP/2 的解法：**
1. 將 objects 分成小的 **frames（訊框）**
2. 不同 objects 的 frames **交錯傳輸 (interleave)**，不必等一個 object 傳完再傳下一個
3. 根據 **client-specified object priority** 決定傳送順序（不必 FCFS）
4. **Server push**：主動推送 unrequested objects 給 client

結果：小 objects (O₂, O₃, O₄) 快速送達，大 object (O₁) 只是稍微延遲。

補充：HTTP/3 進一步改善，在 UDP 上實作 per-object error and congestion control。

---

### Q20. Please list the hierarchy of DNS databases and describe each level.
**請列出 DNS 資料庫的階層結構並描述每一層。**

**答：**

1. **Root DNS Servers（根 DNS 伺服器）**：13 個邏輯伺服器，全球複製多次 (~200 servers in US)。是無法解析名稱時的最後求助對象 (contact-of-last-resort)。由 ICANN 管理。DNSSEC 提供安全性。

2. **TLD DNS Servers（頂級網域 DNS 伺服器）**：負責 .com, .org, .net, .edu, .aero, .jobs 及所有 top-level country domains（.cn, .uk, .tw, .jp）。如 Network Solutions 管理 .com/.net TLD。

3. **Authoritative DNS Servers（權威 DNS 伺服器）**：組織自己的 DNS server(s)，提供該組織 named hosts 的 authoritative hostname-to-IP mappings。可由組織或 service provider 維護。

4. **Local DNS Server（本地 DNS 伺服器）**：不嚴格屬於階層。每個 ISP 都有。Host 的 DNS query 首先送至此，由它從 cache 回覆或轉送至 DNS hierarchy。

---

### Q21. Why does DNS use distributed databases instead of a centralized one?
**為什麼 DNS 使用分散式資料庫而非集中式？**

**答：**
集中式 DNS 有以下問題：
1. **Single point of failure**：該伺服器故障則全部停擺
2. **Traffic volume**：所有 DNS queries 集中在一台伺服器，無法承受（Comcast alone: 600B queries/day）
3. **Distant centralized database**：離遠端使用者太遠，delay 太高
4. **Maintenance**：所有紀錄集中管理不實際（millions of organizations 各自負責自己的 records）

結論：**Doesn't scale!** 分散式資料庫提供 reliability、scalability、和 performance。

---

### Q22. Please describe the procedures of iterated and recursive query for a local DNS name server.
**請描述本地 DNS 伺服器的迭代式和遞迴式查詢流程。**

**答：**

**Iterated Query（迭代式查詢）：**
1. Host 向 local DNS server 發出查詢
2. Local DNS 向 root DNS server 查詢
3. Root 回覆：「去問 .edu TLD server」
4. Local DNS 向 TLD DNS server 查詢
5. TLD 回覆：「去問 authoritative server dns.cs.umass.edu」
6. Local DNS 向 authoritative DNS server 查詢
7. Authoritative 回覆 IP address
8. Local DNS 回傳結果給 host

特點：被聯繫的 server 回覆「應該聯繫的 server 名稱」（"I don't know, but ask this server"），查詢負擔在 local DNS。

**Recursive Query（遞迴式查詢）：**
1. Host → local DNS → root DNS → TLD DNS → authoritative DNS
2. 結果沿原路逐層回傳：authoritative → TLD → root → local DNS → host

特點：將名稱解析的負擔 (burden) 交給被聯繫的 name server，可能造成上層 servers 負擔過重 (heavy load at upper levels)。

---

### Q23. Please list and describe four typical types of DNS resource records.
**請列出並描述四種典型的 DNS 資源紀錄。**

**答：**

DNS 資源紀錄 (Resource Record, RR) 格式：**(name, value, type, ttl)**

| Type | name | value | 說明 |
|------|------|-------|------|
| **A** | hostname | IP address | 主機名稱到 IP 位址的直接對應 |
| **NS** | domain (e.g., foo.com) | hostname of authoritative name server for this domain | 用於找到負責該 domain 的 DNS server |
| **CNAME** | alias name | canonical (real) name | 將 alias 對應到 canonical name。如 www.ibm.com → servereast.backup2.ibm.com |
| **MX** | name | name of SMTP mail server associated with name | 郵件交換器紀錄，用於找到處理 email 的 server |

---

### Q24. How does dynamic DNS avoid caching old IP addresses?
**動態 DNS 如何避免快取舊的 IP 位址？**

**答：**
透過 **TTL (Time-To-Live)** 機制：
- 每筆 DNS resource record 都有一個 TTL 值，指定該紀錄可被 cached 的時間長度
- Cache entries 在 TTL 到期後會 timeout (expire/disappear)
- TTL 過期後，name server 必須重新向 authoritative DNS server 查詢，取得最新的 IP address
- 經常變動 IP 的 host 可設定較短的 TTL
- 這是 **best-effort name-to-address translation**：在所有 TTLs expire 前可能有短暫的不一致
- TLD servers 通常被 cached 在 local name servers 中，因此 root servers 不常被查詢

---

### Q25. Please describe the main ideas of BitTorrent to accelerate file distribution.
**請描述 BitTorrent 加速檔案分配的主要思想。**

**答：**
1. **File chunking**：將檔案分成 256KB chunks，便於平行傳輸
2. **P2P architecture**：每個 peer 在下載時同時上傳 chunks 給其他 peers，利用所有 peers 的 upload bandwidth
3. **Rarest first**：優先請求 peers 中最稀有的 chunk，確保 chunks 在 torrent 中均勻分布
4. **Tit-for-tat**：傳送 chunks 給提供最高 upload rate 的 top 4 peers → 激勵 peers 貢獻上傳頻寬
5. **Optimistic unchoking**：每 30 秒隨機選一個新 peer 開始傳送，讓新 peer 有機會加入
6. **Self scalability**：新 peer 同時帶來 new capacity 和 new demand，peers 越多分配越快

---

### Q26. How does BitTorrent deal with the problem that a peer selfishly leaves after finishing downloading?
**BitTorrent 如何處理 peer 自私地在下載完成後離開的問題？**

**答：**
透過 **tit-for-tat 機制**：
- 一個 peer 只傳送 chunks 給目前以最高速率傳送給它的 top 4 peers
- 不上傳的 peer 會被 **choked**（被阻擋，得不到 chunks）
- 這意味著如果一個 peer 想要快速下載，它必須同時積極上傳
- 每 10 秒重新評估 top 4，每 30 秒 **optimistically unchoke** 一個隨機 peer（讓新 peer 有機會）
- **Rarest first** 確保 chunks 分布均勻，即使某些 peers 離開，其他 peers 仍持有這些 chunks
- 雖然無法完全阻止自私行為（peer 拿到完整檔案後可以 selfishly leave 或 altruistically remain），但 tit-for-tat 讓上傳速率高的 peer 更快拿到檔案，形成正向激勵

---

### Q27. Please describe the operations of DASH (Server-Side and Client-Side).
**請描述 DASH 的伺服器端和用戶端操作。**

**答：**

**DASH = Dynamic, Adaptive Streaming over HTTP**

**Server-Side Operations：**
1. 將 video file 分成多個 **chunks**
2. 每個 chunk 以 **multiple different rates** 編碼
3. 不同 rate 的 encodings 存在 different files
4. Files **replicated in various CDN nodes**
5. 建立 **manifest file**：提供不同 chunks 在不同 rate 版本的 URLs

**Client-Side Operations：**
1. Periodically **estimates server-to-client bandwidth**
2. Consulting **manifest**, requests one chunk at a time
3. Chooses **maximum coding rate** sustainable given current bandwidth
4. Can choose **different coding rates** at different points in time
5. Can request from **different servers** (close or high bandwidth)

Intelligence at client：決定 **when**（避免 buffer starvation/overflow）、**what encoding rate**（更高 bandwidth → 更高品質）、**where** to request chunk。

**Streaming video = encoding + DASH + playout buffering**

---

### Q28. Please describe the functions of CDNs and the two deployment strategies.
**請描述 CDN 的功能和兩種部署策略。**

**答：**

**CDN (Content Distribution Network) 功能：**
- 在多個 geographically distributed sites (CDN nodes) 儲存/服務 content 的 copies
- 將 content 從離使用者最近的 CDN node 提供，減少 delay
- 避免 single point of failure 和 network congestion
- 解決 scale 問題：如何將 millions of videos 串流給 hundreds of thousands of simultaneous users

**兩種部署策略：**

| Strategy | 說明 | 範例 |
|----------|------|------|
| **Enter deep** | 將 CDN servers 深入 (deep into) 許多 access networks，靠近 users | Akamai: 240,000+ servers in 120+ countries |
| **Bring home** | 較少數量 (10's) 的大型 clusters 在 POPs (Points of Presence) 附近 | Limelight Networks |

---

### Q29. Please explain which operations of CDNs rely on DNS.
**請解釋 CDN 的哪些操作依賴 DNS。**

**答：**

1. **Content request redirection（內容請求重導向）**：
   - 當 client 請求 content 時，content provider 的 DNS 透過 **CNAME record** 將 client 重導向至 CDN 的 DNS server
   - 例如：client 查詢 `video.netcinema.com` → 得到 CNAME 指向 CDN domain（如 `a1105.akamai.net`）

2. **Best CDN node selection（最佳節點選擇）**：
   - CDN 的 authoritative DNS server 根據 client 的 **local DNS server 的 IP address** 判斷 client 的大致位置
   - 回傳 geographically closest 或 network conditions 最好的 CDN node 的 IP address
   - Client 直接從該 CDN node 下載 content

---

### Q30. 計算題：Consider the following scenario and answer the questions.
**An institution has:**
- **Access link rate: 1.54 Mbps**
- **RTT from institutional router to origin server: 2 sec**
- **Web object size: 100 Kbits**
- **Average request rate: 15/sec (average data rate to browsers: 1.50 Mbps)**

**(a) What is the access link utilization without a web cache?**
**(b) If a web cache is installed with a cache hit rate of 0.4, what is the new access link utilization and average end-end delay?**

**答：**

**(a) Without cache：**
- Access link utilization = data rate / link rate = 1.50 / 1.54 = **0.97 (97%)**
- 使用率接近 1 → queueing delay 非常大（minutes 等級）
- End-end delay = 2 sec + minutes + usecs ≈ **minutes**

**(b) With cache (hit rate = 0.4)：**

**Access link utilization：**
- 40% requests served by cache（不經過 access link）
- 60% requests 仍經過 access link
- Rate over access link = 0.6 × 1.50 Mbps = 0.9 Mbps
- New utilization = 0.9 / 1.54 = **0.58 (58%)**
- Utilization 0.58 → low (msec) queueing delay at access link

**Average end-end delay：**
- = 0.6 × (delay from origin servers) + 0.4 × (delay when satisfied at cache)
- = 0.6 × (2 sec + msecs) + 0.4 × (~msecs)
- = 0.6 × 2.01 + 0.4 × (~0)
- ≈ **1.2 secs**

結論：安裝 web cache 比升級 access link 至 154 Mbps **更便宜且平均延遲更低**。
