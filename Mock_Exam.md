# 模擬考題 - 電腦網路 Ch1 & Ch2

---

## Part A: 選擇題 (Multiple Choice) - 每題 3 分

### Q1. Which of the following is NOT a function provided by DNS?
(A) Hostname-to-IP-address translation
(B) Load distribution
(C) Packet routing
(D) Mail server aliasing

---

### Q2. In packet switching, what happens when a packet arrives at a router with a full buffer?
(A) The router increases its buffer size
(B) The packet is dropped (lost)
(C) The router slows down the sender
(D) The packet is sent back to the source

---

### Q3. What is the response time for non-persistent HTTP to fetch ONE object?
(A) 1 RTT + file transmission time
(B) 2 RTT + file transmission time
(C) 3 RTT + file transmission time
(D) 1 RTT only

---

### Q4. Which DNS record type maps a hostname to an IP address?
(A) NS
(B) MX
(C) CNAME
(D) A

---

### Q5. HTTP is a ______ protocol, while SMTP is a ______ protocol.
(A) push; pull
(B) pull; push
(C) stateful; stateless
(D) connectionless; connection-oriented

---

### Q6. Which layer in the Internet protocol stack is responsible for routing datagrams from source to destination?
(A) Application layer
(B) Transport layer
(C) Network layer
(D) Link layer

---

### Q7. In BitTorrent, what strategy does a peer use to decide which chunks to request first?
(A) Largest first
(B) Rarest first
(C) Newest first
(D) Random selection

---

### Q8. What technology do both DSL and cable modem use to transmit voice and data simultaneously on the same line?
(A) TDM (Time Division Multiplexing)
(B) FDM (Frequency Division Multiplexing)
(C) CDM (Code Division Multiplexing)
(D) WDM (Wavelength Division Multiplexing)

---

### Q9. Which of the following is TRUE about circuit switching?
(A) Resources are shared among all users
(B) It is suitable for bursty data
(C) Dedicated resources are reserved for a call, no sharing
(D) No call setup is required

---

### Q10. In the context of CDN, what does "enter deep" strategy mean?
(A) Place CDN servers only at the network core
(B) Push CDN servers deep into many access networks, close to users
(C) Use a single mega-server
(D) Place CDN servers at tier-1 ISPs only

---

### Q11. What does HTTP status code 304 mean?
(A) OK
(B) Bad Request
(C) Not Modified
(D) Not Found

---

### Q12. Which transport protocol does SMTP use?
(A) UDP, port 80
(B) TCP, port 25
(C) TCP, port 80
(D) UDP, port 25

---

### Q13. What is the PDU (Protocol Data Unit) name at the Transport layer?
(A) Message
(B) Segment
(C) Datagram
(D) Frame

---

### Q14. In DASH, where does the "intelligence" reside for deciding encoding rate and server selection?
(A) Server
(B) CDN node
(C) Client
(D) DNS server

---

### Q15. Which is NOT one of the four components of HTTP cookies?
(A) Cookie header line in HTTP response message
(B) Cookie file on user's host
(C) Cookie stored in the router's forwarding table
(D) Back-end database at Web site

---

## Part B: 簡答題 (Short Answer) - 每題 5 分

### Q16. 請列出封包延遲的四個來源 (four sources of packet delay)，並分別寫出英文名稱和公式（若有）。

---

### Q17. 請比較 Packet Switching 和 Circuit Switching 的優缺點。(Compare the pros and cons of packet switching and circuit switching.)

---

### Q18. 請說明 HTTP/2 如何緩解 HOL (Head-of-Line) blocking 問題。(Describe how HTTP/2 mitigates HOL blocking.)

---

### Q19. 請畫出 DNS 的階層結構，並說明每一層的功能。(List the DNS hierarchy and describe the function of each level.)

---

### Q20. 請說明 DASH 的 Server-side 和 Client-side 操作。(Describe the server-side and client-side operations of DASH.)

---

### Q21. 請解釋 BitTorrent 的 tit-for-tat 機制如何運作，以及它如何激勵 peers 上傳。(Explain how BitTorrent's tit-for-tat mechanism works and how it incentivizes peers to upload.)

---

### Q22. 請列出並說明 DNS 的四種 Resource Record (RR) 類型。(List and describe four types of DNS resource records.)

---

### Q23. 請說明 Web Cache 的三個好處，以及 Conditional GET 的運作方式。(List three benefits of web cache and explain how Conditional GET works.)

---

## Part C: 計算題 (Calculation) - 每題 8 分

### Q24.
A packet of length **L = 2,000 bytes** is transmitted over a link with transmission rate **R = 10 Mbps**. The link has a physical length of **d = 2,000 km**, and the propagation speed is **s = 2 × 10⁸ m/sec**.

(a) What is the transmission delay? (傳輸延遲)
(b) What is the propagation delay? (傳播延遲)
(c) What is the total delay (assuming no processing and queueing delay)?

---

### Q25.
Consider a link with transmission rate **R = 1 Gbps**. Users generate data at **100 Mbps** when active, and are active **10%** of the time.

(a) How many users can be supported using **circuit switching**?
(b) If **packet switching** is used, with **35 users**, is the probability of more than 10 users being active simultaneously greater or less than 0.0004?
(c) 為什麼封包交換在此情境下能支援更多使用者？(Why can packet switching support more users?)

---

### Q26.
An institution has an access link rate of **1.54 Mbps**, the RTT from institutional router to origin server is **2 sec**, web object size is **100 Kbits**, and the average request rate is **15 requests/sec** (average data rate = 1.50 Mbps).

(a) What is the access link utilization WITHOUT a web cache? (存取鏈路使用率)
(b) 安裝 web cache 後，假設 cache hit rate = 0.4，計算：
   - 存取鏈路的新使用率 (new access link utilization)
   - 平均端對端延遲 (average end-end delay)

---

### Q27.
Consider distributing a file of size **F = 15 Gbits** to **N = 10** peers. The server upload rate is **u_s = 30 Mbps**. Each peer has download rate **d_i = 2 Mbps** and upload rate **u_i = 1 Mbps**.

(a) Calculate the minimum distribution time using the **client-server** approach.
    D_cs >= max{NF/u_s, F/d_min}

(b) Calculate the minimum distribution time using the **P2P** approach.
    D_P2P >= max{F/u_s, F/d_min, NF/(u_s + Σu_i)}

(c) 哪種方式更快？為什麼？

---

---

# 模擬考答案

---

## Part A 選擇題答案

| 題號 | 答案 | 解說 |
|------|------|------|
| Q1 | **(C)** | DNS 提供 hostname-to-IP translation、load distribution、host/mail aliasing，但 packet routing 是 network layer (IP) 的功能 |
| Q2 | **(B)** | Buffer 滿時封包被丟棄 (dropped/lost) |
| Q3 | **(B)** | Non-persistent HTTP: 1 RTT (TCP connection) + 1 RTT (HTTP request/response) + file transmission |
| Q4 | **(D)** | Type A: name=hostname, value=IP address |
| Q5 | **(B)** | HTTP 是 client pull（用戶端拉取）；SMTP 是 client push（用戶端推送） |
| Q6 | **(C)** | Network layer 負責 routing datagrams from source to destination |
| Q7 | **(B)** | Rarest first：優先請求最稀有的區塊 |
| Q8 | **(B)** | FDM 分頻多工：不同資料用不同頻帶傳輸 |
| Q9 | **(C)** | Circuit switching 預先保留專用資源，不共享 |
| Q10 | **(B)** | Enter deep = 將 CDN 伺服器深入存取網路，靠近使用者（如 Akamai） |
| Q11 | **(C)** | 304 Not Modified：快取版本仍為最新，不需傳送物件 |
| Q12 | **(B)** | SMTP 使用 TCP, port 25 |
| Q13 | **(B)** | Transport layer 的 PDU 叫 Segment (區段) |
| Q14 | **(C)** | DASH 中「intelligence」在 client 端：決定 when/what rate/where |
| Q15 | **(C)** | Cookie 不存在路由器的轉送表中。四元件是：response header、request header、cookie file、backend database |

---

## Part B 簡答題參考答案

### Q16. 四種封包延遲

**d_nodal = d_proc + d_queue + d_trans + d_prop**

1. **Processing Delay (d_proc，處理延遲)**：檢查位元錯誤、決定輸出鏈路。通常 < microsecs。
2. **Queueing Delay (d_queue，排隊延遲)**：在輸出鏈路等待傳輸的時間。取決於壅塞程度。
3. **Transmission Delay (d_trans，傳輸延遲)**：將封包推入鏈路的時間。**d_trans = L/R**（L: 封包長度 bits, R: 鏈路速率 bps）
4. **Propagation Delay (d_prop，傳播延遲)**：位元在實體鏈路中傳播的時間。**d_prop = d/s**（d: 鏈路長度, s: 傳播速度 ~2×10⁸ m/sec）

---

### Q17. Packet Switching vs. Circuit Switching

**Packet Switching 優點**：適合 bursty data；資源共享可支援更多使用者；簡單不需 call setup。
**Packet Switching 缺點**：可能發生 congestion；packet delay and loss；需要可靠傳輸和壅塞控制協定。

**Circuit Switching 優點**：guaranteed performance（專用資源）；適合持續穩定通訊。
**Circuit Switching 缺點**：閒置時浪費資源（no sharing）；使用者數受限；需要 call setup。

---

### Q18. HTTP/2 緩解 HOL Blocking

HTTP 1.1 問題：伺服器以 FCFS 回應 => 小物件被大物件卡住 (HOL blocking)。

HTTP/2 解法：
1. 將物件分成 **frames（訊框）**
2. **交錯排程（interleave）** 不同物件的 frames 傳輸
3. 根據 **client-specified object priority** 決定傳送順序
4. **Server push**：主動推送未被請求的物件

結果：小物件快速送達，大物件僅稍微延遲。

---

### Q19. DNS 階層結構

1. **Root DNS Servers（根）**：13 個邏輯伺服器，全球複製。最後的求助對象。由 ICANN 管理。
2. **TLD DNS Servers（頂級網域）**：負責 .com, .org, .edu, .tw 等。如 Network Solutions 管理 .com/.net。
3. **Authoritative DNS Servers（權威）**：組織自己的 DNS，提供該組織主機名稱到 IP 的權威對應。
4. **Local DNS Server（本地）**：不嚴格屬於階層。每個 ISP 都有。主機的 DNS 查詢首先送至此。

---

### Q20. DASH 操作

**Server-side**：將影片分成 chunks → 每個 chunk 編碼成多種 rates → 存在不同檔案 → 複製到 CDN nodes → 建立 manifest file。

**Client-side**：定期估算可用 bandwidth → 參考 manifest → 每次請求一個 chunk → 選擇當前 bandwidth 能支撐的最大 coding rate → 可從不同伺服器、不同時間選不同速率。Intelligence at client: 決定 when / what rate / where。

---

### Q21. BitTorrent Tit-for-tat

運作方式：Alice 傳送 chunks 給目前以**最高速率**傳送給她的前 4 個 peers（top 4）。其他 peers 被 choked（不給 chunks）。每 10 秒重新評估 top 4。每 30 秒隨機選一個 peer 開始傳送（optimistically unchoke）。

激勵機制：不上傳就得不到下載（被 choke）。上傳速率越高 => 越容易成為別人的 top 4 => 更快拿到檔案。Optimistic unchoke 讓新 peer 有機會被發現並加入交換。

---

### Q22. DNS RR 四種類型

| Type | name | value | 用途 |
|------|------|-------|------|
| **A** | hostname | IP address | 主機名稱 → IP 位址 |
| **NS** | domain | authoritative name server hostname | 網域 → 負責的名稱伺服器 |
| **CNAME** | alias name | canonical (real) name | 別名 → 正規名稱 |
| **MX** | name | SMTP mail server name | 名稱 → 郵件伺服器 |

---

### Q23. Web Cache 好處 & Conditional GET

三個好處：
1. **Reduce response time**：cache 離 client 近
2. **Reduce traffic on access link**：命中的請求不需過存取鏈路
3. **Enable poor content providers to deliver effectively**：網際網路中充滿 cache

Conditional GET：
- Client 在請求中加 `If-modified-since: <date>`
- Server 若物件未修改 => 回 `304 Not Modified`（不傳物件，省頻寬）
- Server 若物件已修改 => 回 `200 OK` + 新物件

---

## Part C 計算題答案

### Q24.

**(a) Transmission delay:**
d_trans = L / R = (2,000 × 8 bits) / (10 × 10⁶ bps) = 16,000 / 10,000,000 = **1.6 × 10⁻³ sec = 1.6 ms**

**(b) Propagation delay:**
d_prop = d / s = (2,000 × 10³ m) / (2 × 10⁸ m/sec) = 2,000,000 / 200,000,000 = **0.01 sec = 10 ms**

**(c) Total delay:**
d_total = d_trans + d_prop = 1.6 ms + 10 ms = **11.6 ms**

---

### Q25.

**(a) Circuit switching:**
Each user needs 100 Mbps when active. Link capacity = 1 Gbps = 1000 Mbps.
Number of users = 1000 / 100 = **10 users**

**(b) Packet switching with 35 users:**
Probability of > 10 active at the same time is **less than 0.0004**（根據二項分布計算）

**(c) 為什麼封包交換能支援更多使用者？**
因為使用者只有 10% 的時間是活躍的（bursty data）。封包交換利用**統計多工 (statistical multiplexing)**，允許使用者共享資源。35 個使用者同時有超過 10 個活躍的機率極低 (< 0.0004)，所以大多數時候不會超過鏈路容量。電路交換則無論是否活躍都必須預留專用資源，造成浪費。

---

### Q26.

**(a) Access link utilization WITHOUT cache:**
utilization = data rate / link rate = 1.50 Mbps / 1.54 Mbps = **0.97 (97%)**

使用率接近 1，排隊延遲非常大（數分鐘級）。
End-end delay = Internet delay + access link delay + LAN delay = 2 sec + **minutes** + usecs

**(b) WITH web cache (hit rate = 0.4):**

**新使用率：**
- 40% 請求由 cache 服務（不經過 access link）
- 60% 請求仍需經過 access link
- 經過 access link 的 data rate = 0.6 × 1.50 = 0.9 Mbps
- New utilization = 0.9 / 1.54 = **0.58 (58%)**（排隊延遲低，約 msec 級）

**平均端對端延遲：**
avg delay = 0.6 × (delay from origin) + 0.4 × (delay from cache)
= 0.6 × (2 sec + msecs) + 0.4 × (~msecs)
= 0.6 × 2.01 + 0.4 × (~0)
≈ **1.2 secs**

---

### Q27.

**(a) Client-Server approach:**
- NF/u_s = (10 × 15 Gbits) / (30 Mbps) = 150 Gbits / 0.03 Gbps = **5,000 sec**
- F/d_min = 15 Gbits / (2 Mbps) = 15,000 / 0.002 = **7,500 sec**
- D_cs >= max{5000, 7500} = **7,500 sec (= 125 min)**

**(b) P2P approach:**
- F/u_s = 15 Gbits / 30 Mbps = **500 sec**
- F/d_min = 15 Gbits / 2 Mbps = **7,500 sec**
- NF/(u_s + Σu_i) = (10 × 15 Gbits) / (30 Mbps + 10 × 1 Mbps) = 150 Gbits / 40 Mbps = **3,750 sec**
- D_P2P >= max{500, 7500, 3750} = **7,500 sec (= 125 min)**

**(c) 比較：**
在此範例中兩者相同（都受限於 d_min = 2 Mbps 的瓶頸）。但若 N 增大，C/S 的 NF/u_s 會線性增長而成為瓶頸，P2P 則因 Σu_i 也增長而保持較低的分配時間。P2P 的擴展性 (scalability) 優於 C/S。
