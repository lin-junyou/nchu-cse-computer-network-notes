# 期末考重點整理 — 電腦網路 Ch3 / Ch4 / Ch5

> **考試時間**：6/11（三）下午 6:30
> **考試範圍**：Chapter 3 全部、Chapter 4.1 & 4.3、Chapter 5.1 ~ 5.4
> **題型**：**簡答題 (short answer)**，**考題為英文**
>
> 本檔以 Homework 3（對應 Ch3）與 Homework 4（對應 Ch4、Ch5）為骨幹整理。
> 每題標題為 **英文原題（考試出題語言）**，下方斜體為**中文對照**；答案以中文講解並**保留英文專業術語**。
> 詳細內容請搭配 [Chapter_3_notes.md](Chapter_3_notes.md)、[Chapter_4_notes.md](Chapter_4_notes.md)、[Chapter_5_notes.md](Chapter_5_notes.md)。

---

## 第一部分：Chapter 3 傳輸層（對應 Homework 3）

### Q1. What are the header fields to identify a UDP socket and a TCP socket?
> *辨識 UDP socket 與 TCP socket 的標頭欄位分別是什麼？*

- **UDP socket**：用 **二元組 (2-tuple)** —— **(destination IP address, destination port #)**。
- **TCP socket**：用 **四元組 (4-tuple)** —— **(source IP, source port #, destination IP, destination port #)**。
- 所以 destination port 相同但 source 不同的 UDP 封包會進同一 socket；TCP 則依 4-tuple 分到不同 socket。

### Q2. Please describe the mechanism of packet error detection using checksum.
> *請描述使用 checksum 偵測封包錯誤的機制。*

- **Sender（傳送端）**：把節段內容視為一連串 16-bit integers，做 **one's complement sum（1 的補數和）**（進位需 wraparound 繞回相加），結果放入 checksum 欄位。
- **Receiver（接收端）**：對收到的節段重新計算 checksum，與欄位值比較 —— **不等 = 偵測到錯誤 (error detected)**；相等 = 沒偵測到錯誤（仍可能有未偵出的錯誤，保護力弱 weak protection）。

### Q3. Please calculate the checksum of the following two 16-bit binary integers: `1110011001100110` & `1101010101010101`.
> *請計算這兩個 16-bit 二進位整數的 checksum。*

1. 相加：`1110011001100110 + 1101010101010101 = (1)1011101110111011`（最高位有 carryout 進位 1）
2. 進位繞回 (wraparound)：`1011101110111011 + 1 = 1011101110111100`（sum）
3. checksum = sum 取 1 的補數 (one's complement) → **`0100010001000011`**

### Q4. Please list the network applications of UDP.
> *請列出 UDP 的網路應用。*

**streaming multimedia（串流多媒體）**（loss tolerant、rate sensitive）、**DNS**、**SNMP**、**HTTP/3**。
（這類應用偏好低延遲/簡單，或自行在 application layer 加可靠度。）

### Q5. Please describe all necessary mechanisms to recover the packets with bit errors.
> *請描述復原「位元錯誤 (bit errors)」封包所需的所有機制。*

1. **error detection — checksum（錯誤偵測）**
2. **receiver feedback — ACK / NAK（接收端回饋）**（ACK=收到無誤、NAK=有錯）
3. **retransmission（重送）**：收到 NAK 即重送
4. **sequence number（序號）**：當 ACK/NAK 本身 corrupted 時，用序號讓接收端丟棄 duplicate 重複封包
（對應 rdt2.0 → rdt2.1 → rdt2.2 的演進）

### Q6. Please explain why one-bit sequence number is enough for a stop-and-wait protocol.
> *請解釋為何一個位元的序號 (one-bit sequence number) 對 stop-and-wait 協定就足夠。*

Stop-and-wait 一次只送一個封包並等待其 ACK，未收到前不送下一個 → 任一時刻最多一個 in-flight 飛行中封包，receiver 只需區分「current」與「next」兩種狀態，**0 / 1 兩個序號**即足以辨識 duplicate。

### Q7. What is the function of sequence numbers in feedback segments?
> *回饋節段 (feedback segments) 中序號的功能是什麼？*

讓 sender 知道**被確認 (ACKed) 的是哪一個封包**，以便正確處理 **duplicate packets（重複封包）**與 **delayed ACK（延遲的 ACK）**（避免把舊 ACK 誤認、避免重送錯誤封包）。

### Q8. Please describe all necessary mechanisms to recover packet loss.
> *請描述復原「封包遺失 (packet loss)」所需的所有機制。*

在 checksum / ACK / retransmission / sequence number 之外，再加：
- **countdown timer（倒數計時器）**
- **timeout / retransmit（逾時重送）**：等待合理時間仍無 ACK 就重送
- 若只是 delayed（非 lost），重送造成的 duplicate 由 **sequence number** 處理。
（對應 rdt3.0）

### Q9. Please describe the problems caused by improper timeout values.
> *請描述不當的 timeout 值會造成的問題。*

- **too short（太短）**：premature timeout（過早逾時）→ 不必要的 retransmission，浪費頻寬。
- **too long（太長）**：對 packet loss 反應太慢 → 效能差、延遲高。

### Q10. The performance of the stop-and-wait protocol is poor and can be improved by a pipelining protocol. Please describe the idea of the pipelining protocol.
> *stop-and-wait 效能不佳，可用 pipelining 協定改善。請描述 pipelining 的想法。*

Sender 允許**多個尚未被確認的封包同時 in-flight（在飛行中）**，不必一送一等。需要更大的 sequence number range 與 sender/receiver 的 buffering。可大幅提升鏈路 utilization（如管線 3 個封包 → utilization ×3）。

### Q11. Please describe the difference between Go-Back-N and Selective Repeat for packet retransmission, receiver window (buffer) size, and number of timers.
> *請就「封包重送、接收端視窗(緩衝)大小、計時器數量」描述 Go-Back-N 與 Selective Repeat 的差異。*

| | Go-Back-N (GBN) | Selective Repeat (SR) |
|---|---|---|
| retransmission（重送） | 逾時重送封包 n **及其後全部** | 只重送**該未確認封包** |
| receiver window/buffer（接收緩衝） | 不緩衝 out-of-order 封包（discard、re-ACK） | 緩衝 out-of-order 封包待依序交付 |
| number of timers（計時器數） | **1 個**（最舊未確認封包） | **每個未確認封包各一個** |

### Q12. How many sequence numbers are required for selective repeat? (window size = W)
> *Selective Repeat 需要多少序號？（視窗大小 = W）*

**序號數量 ≥ 2W**（sequence number space ≥ 2 倍 window size），否則 receiver 無法分辨新封包與重送的舊封包。

### Q13. Please introduce the basic idea of calculating TCP timeout value.
> *請介紹計算 TCP timeout 值的基本想法。*

以 **EWMA（exponential weighted moving average）**平滑化量測的 SampleRTT：
- `EstimatedRTT = (1−α)·EstimatedRTT + α·SampleRTT`（α = 0.125）
- `DevRTT = (1−β)·DevRTT + β·|SampleRTT − EstimatedRTT|`（β = 0.25）
- `TimeoutInterval = EstimatedRTT + 4·DevRTT`（estimated RTT 加 safety margin 安全邊際；RTT 變動越大邊際越大）。

### Q14. Please describe the procedure of TCP delayed ACK in TCP receivers.
> *請描述 TCP receiver 的 delayed ACK（延遲確認）程序。*

依序收到 expected segment 時**先不立即回 ACK**，等待最多 **500 ms**；若期間有下一個 in-order segment 到達，就用**一個 cumulative ACK（累積確認）**一次確認兩個節段；若 500 ms 內無新節段才送 ACK。（out-of-order 到達則立即送 duplicate ACK。）

### Q15. Please describe the idea of TCP Fast Retransmit.
> *請描述 TCP Fast Retransmit（快速重送）的想法。*

Sender 若收到 **3 個 duplicate ACK（triple duplicate ACK）**，即判定該（最小序號）segment 很可能 lost，**不必等 timeout 立即 retransmit**。

### Q16. TCP flow control can avoid receiver buffer overflows. Please describe its procedure.
> *TCP flow control 可避免 receiver buffer 溢位。請描述其程序。*

1. Receiver 在 TCP header 的 **rwnd（receive window）**欄位 advertise（廣告）其空閒緩衝空間（`rwnd = RcvBuffer − 已緩衝未讀資料`）。
2. Sender 限制 in-flight（未確認）資料量 **≤ 收到的 rwnd**。
3. → 保證 receive buffer **不會 overflow**。（目的：避免 sender 塞爆 receiver。）

### Q17. Please describe the difference of reliable data transfer between TCP and Go-Back-N.
> *請描述 TCP 與 Go-Back-N 在可靠資料傳輸 (reliable data transfer) 上的差異。*

TCP 雖用 cumulative ACK（像 GBN），但 TCP receiver 通常**緩衝 out-of-order segments**、timeout 時**只重送單一最小序號 segment**（非重送全部）、且有 **fast retransmit**。→ TCP 是 **GBN 與 SR 的混合 (hybrid)**。

### Q18. Please explain the values specified in fields of sequence number and acknowledge number in a TCP header.
> *請解釋 TCP header 中 sequence number 與 acknowledgement number 欄位所指定的值。*

- **sequence number（序號）**：本 segment 資料**第一個 byte** 在 byte stream 中的編號（計 bytes，非 segments）。
- **acknowledgement number（確認號）**：receiver **期望從對方收到的下一個 byte** 的序號（cumulative ACK）。

### Q19. TCP uses three-way handshake for connection setup. Why is two-way handshake not used?
> *TCP 用三向交握建立連線。為何不用二向交握 (two-way handshake)？*

2-way handshake 在 **variable delay、訊息 loss/重送、reordering、看不到對方狀態**的網路下會出問題：
- **half-open connection（半開連線）**：舊的連線請求重送抵達 server，server 建立連線但 client 早已不存在。
- **dup data accepted（重複資料被接受）**：舊資料重送被當成新連線資料接受。
- 3-way handshake 讓雙方互相確認「對方活著且願意連線」並交換 initial sequence numbers，避免上述情況。

### Q20. Please describe the four-way handshake of closing a TCP connection.
> *請描述關閉 TCP 連線的四向交握 (four-way handshake)。*

1. Client 送 **FIN**（close）→ 進入 FIN_WAIT_1。
2. Server 回 **ACK** → Server 進入 CLOSE_WAIT（仍可送資料），Client 進 FIN_WAIT_2。
3. Server 送 **FIN** → 進入 LAST_ACK。
4. Client 回 **ACK** → 進入 **TIMED_WAIT**（等 2×MSL, max segment lifetime）後 CLOSED；Server 收到 ACK 即 CLOSED。

### Q21. Please describe how TCP slow start phase adjusts CWND value.
> *請描述 TCP slow start 階段如何調整 CWND（壅塞視窗）值。*

`cwnd` 從 **1 MSS（maximum segment size）**開始，**每收到一個 ACK 就 +1 MSS**，使得**每個 RTT cwnd 加倍（exponential growth 指數成長）**，直到發生 loss event 或 `cwnd ≥ ssthresh`（轉入 congestion avoidance）。

### Q22. Please describe the idea of AIMD (additive increase; multiplicative decrease) in TCP for adjusting CWND.
> *請描述 TCP 中用以調整 CWND 的 AIMD（加法增加；乘法遞減）想法。*

- **Additive Increase（加法增加）**：每個 RTT 把 cwnd 增加 1 MSS，持續 probing 探測可用頻寬。
- **Multiplicative Decrease（乘法遞減）**：遇 loss event 就把 cwnd **減半**（timeout 則降到 1 MSS）。
- 形成「**sawtooth（鋸齒狀）**」行為，是一種 distributed 演算法，可在全網最佳化流速率且穩定。

---

## 第二部分：Chapter 4 & 5 網路層（對應 Homework 4）

### Q1. Please describe the functions of data plane and control plane in each router.
> *請描述每台 router 中 data plane 與 control plane 的功能。*

- **data plane（資料平面）**：每台 router 的**本地 (local, per-router)** 功能，依封包 header 值查 forwarding table，把抵達 input port 的封包轉送到適當 output port（= **forwarding**）。
- **control plane（控制平面）**：**全網 (network-wide)** 邏輯，決定封包如何在 router 間沿 end-to-end path 被路由，即**計算 forwarding table 的內容**（= **routing**）。

### Q2. Please describe the functions of Time to Live and Upper Layer fields in IPv4 header.
> *請描述 IPv4 header 中 Time to Live 與 Upper Layer 欄位的功能。*

- **TTL (Time to Live)**：剩餘最大 hop 數，每經一台 router 減 1，**減到 0 即丟棄 (discard)**，防止封包在 routing loop 中無限繞行。
- **Upper Layer**：指出 payload 屬於**哪個 upper-layer（transport）protocol（如 TCP 或 UDP）**，供目的主機 demultiplex 時把資料交給正確協定。

### Q3. What is CIDR?
> *什麼是 CIDR？*

**CIDR (Classless InterDomain Routing)**：無類別跨網域路由，subnet（network prefix）部分可為**任意長度 (arbitrary length)**，以 **a.b.c.d/x** 表示（x = prefix 位元數），不受 Class A/B/C 固定切分限制。

### Q4. Please describe the idea of route aggregation.
> *請描述 route aggregation（路由聚合）的想法。*

ISP 把底下多個組織的位址區塊**合併成單一較大的 prefix** 對外 advertise（如 8 個 /23 → 1 個 /20），讓外界 routing table 只需一筆即可涵蓋，**減少 routing table 大小與 update 流量**。

### Q5. Why must routers select the route with a more specific subnet identifier for packet forwarding to ensure correctness?
> *為何 router 必須選擇「更明確的 subnet 前綴」做轉送，才能確保正確性？*

採 **longest prefix matching（最長前綴比對）**。一個 destination address 可能符合多筆 route，**較長（more specific 更明確）的 prefix** 更精確指向該位址真正所在網路。例如組織搬家後，更明確的 `/23` 比舊的 `/20` 更能把封包送到正確的 ISP，否則會送錯。

### Q6. Please describe the operations of link-state routing algorithm.
> *請描述 link-state 路由演算法的運作。*

1. 每台 router 把自己的 link state（到各鄰居成本）**flood（洪泛）**給全網 → 每台都有**完整 topology**。
2. 各 router 跑 **Dijkstra**：初始化到鄰居成本，每次 iteration 挑出 D 值最小且未確定的節點加入 N'，更新其鄰居 `D(v) = min(D(v), D(w) + c(w,v))`。
3. 直到所有節點確定，得 least-cost-path tree 與 forwarding table。

### Q7. Please use an example to explain why link-state routing algorithm would cause route oscillation.
> *請用例子解釋為何 link-state 演算法會造成 route oscillation（路由震盪）。*

當 **link cost 與 traffic volume 相關**時：各節點同步地把流量導向當前 low-cost path → 該路徑流量暴增成本上升 → 下一輪大家又改選另一邊 → 那邊又變貴…如此**來回擺盪 (oscillate)** 永不穩定。
（例：destination a，流量從 d, c, e 以 1, e, 1 進入，directional 且 volume-dependent 成本，每輪重算都翻轉路徑。）

### Q8. Please describe the operations of distance-vector routing algorithm.
> *請描述 distance-vector 路由演算法的運作。*

1. 每節點維護到所有 destination 的 DV (distance vector)，初始只知到直接 neighbor 的成本。
2. 不時把自己的 DV 傳給 neighbors。
3. 收到鄰居 DV 後用 **Bellman-Ford** `Dx(y) = min_v{c(x,v) + Dv(y)}` 重算自己的 DV。
4. 若 DV 改變才通知 neighbors；反覆至 converge（iterative、asynchronous、distributed、self-stopping）。

### Q9. Please describe the problem of count-to-infinity for distance-vector routing algorithm.
> *請描述 distance-vector 演算法的 count-to-infinity 問題。*

「bad news travels slow（壞消息傳得慢）」。當某 link cost 大增時，兩個互相把對方當成到 destination 路徑的節點會**輪流以 +1 緩慢遞增**估計值（A 說經 B=6、B 說經 A=7、A 說 8…），要很多輪才 converge 到正確值。

### Q10. Please show how poisoned reverse solves the problem of count-to-infinity.
> *請說明 poisoned reverse（毒性反向）如何解決 count-to-infinity 問題。*

若 z 是**經過 y** 才能到 x，z 就向 y advertise「z 到 x 的距離 = **∞**」（poison 毒化 reverse path）。如此 y 不會誤以為能經 z 到 x，避免 y↔z 互相依賴對方舊路徑而緩慢遞增的迴圈。
（⚠️ 無法解決三個以上節點的 loop。）

### Q11. Please compare link-state and distance-vector algorithms in terms of message complexity, convergence speed and robustness.
> *請就 message complexity、convergence speed、robustness 比較 link-state 與 distance-vector 演算法。*

- **message complexity（訊息複雜度）**：LS 需 $O(n^2)$（全網 flood）；DV 只在 neighbors 間交換。
- **convergence speed（收斂速度）**：LS 較快但**可能 oscillation**；DV 收斂時間不定，**可能有 routing loops 與 count-to-infinity**。
- **robustness（強健性）**：LS 中每台 router 只 advertise 自己的 link cost、各自算表，錯誤影響有限；DV 中錯誤的 path cost 會被 neighbor 採用並**傳播全網**（black-holing）。

### Q12. Please describe the concept and benefits of hierarchical OSPF.
> *請描述 hierarchical OSPF（階層式 OSPF）的概念與好處。*

- **概念**：把 AS 切成多個 **area** + 一個 **backbone** 兩層；LS advertisement 只在各 area 內 flood，area border router 只把「到區域內 destination 的 summary 摘要距離」廣告到 backbone。
- **好處**：每台 router 只需維護自家 area 的詳細 topology → **降低 LS flooding 範圍、計算量與 table 大小**，控制流量、加快 convergence，便於大型 AS 管理。

### Q13. Please explain how Internet uses intra-AS and inter-AS routing to achieve scalable routing.
> *請解釋 Internet 如何用 intra-AS 與 inter-AS 路由達成可擴展路由 (scalable routing)。*

把 router 分組成 **AS (Autonomous System)**。**AS 內部**用 intra-AS 協定（OSPF）算內部路由；**AS 之間**用 inter-AS 協定（BGP）交換 reachability 可達性資訊。forwarding table：intra-AS 決定**內部 destination** 項目，inter-AS + intra-AS 共同決定**外部 destination** 項目。此 **hierarchical（階層式）**設計縮小 routing table、降低 update 流量，並保有各 AS 的 administrative autonomy 管理自主性。

### Q14. Please describe the functions of eBGP and iBGP sessions.
> *請描述 eBGP 與 iBGP session 的功能。*

- **eBGP (external BGP)**：跑在**不同 AS 的 gateway router 間**，取得/廣告鄰近 AS 的 subnet reachability 資訊。
- **iBGP (internal BGP)**：跑在**同一 AS 內部 router 間**，把外部 reachability 資訊傳播給 AS 內所有 router。
- gateway router 同時跑兩者。

### Q15. Please describe the generation of forwarding-table entries in a router with BGP and OSPF.
> *請描述 router 在 BGP 與 OSPF 下如何產生 forwarding-table 項目。*

1. **BGP（inter-AS）**決定「到外部 destination X 要經哪個 gateway / 哪條 AS path」，經 iBGP 告知 AS 內所有 router。
2. **OSPF（intra-AS）**決定「在 AS 內部要走哪個 interface 才能到那個 gateway」。
3. 兩者組合填出項目，例如「到 X → interface 1」（因 OSPF 算出到 gateway 1c 走 interface 1）。

### Q16. Please describe how hot potato routing works.
> *請描述 hot potato routing（燙手山芋路由）如何運作。*

當 router 經 iBGP 得知可經多個 local gateway 到達某 destination 時，選擇**到該 gateway 的 intra-domain (OSPF) cost 最低**者，目的是**儘快把封包丟出自己的 AS**，**不在意** inter-domain（AS hops）成本。

### Q17. Please use examples to examine the operations of policy for BGP.
> *請用例子檢視 BGP 政策 (policy) 的運作。*

- ISP 通常只想為**自己的 customer networks** 載送流量，不想替其他 ISP 做 **transit traffic（轉送流量）**。
- **例一**：A advertise A,w 給 B 和 C；**B 不把 B,A,w advertise 給 C**（因 C, A, w 都非 B 的 customer，B 無 revenue 可圖）→ C 只能走 C,A,w（不經 B）。
- **例二**：dual-homed 客戶 x（同時連 B、C）**不想替 B↔C 中繼** → x 不向 B advertise「經 x 到 C」的路徑。
- → 透過**選擇性 advertise / 不 advertise 路徑**來實施 traffic policy。

---

## 最後衝刺：高頻必背清單 (Quick Review)

| 主題 | 一句話重點 |
|------|-----------|
| UDP/TCP demux | UDP 2-tuple；TCP 4-tuple |
| rdt 機制 | bit errors → checksum + ACK/NAK + retransmission + seq #；loss → 再加 timer + timeout |
| stop-and-wait 序號 | 1 bit (0/1) 足夠 |
| SR 序號 | ≥ 2 × window size |
| GBN vs SR | 重送全部 vs 單一；不緩衝 vs 緩衝；1 timer vs 每封包 timer |
| Timeout | EstimatedRTT + 4·DevRTT (α=0.125, β=0.25) |
| Fast Retransmit | 3 個 duplicate ACK 即重送 |
| flow control | rwnd advertise 空閒緩衝，限制 in-flight 資料 |
| 3-way / 4-way | 建立 three-way、關閉 four-way（TIMED_WAIT = 2·MSL） |
| AIMD | additive increase、multiplicative decrease，sawtooth |
| Slow start | cwnd 1 MSS 起，每 RTT 加倍 |
| TTL / Upper Layer | 防 loop / demux 到 TCP-UDP |
| CIDR / longest prefix | a.b.c.d/x；選 most specific 前綴 |
| NAT 私有位址 | 10/8、172.16/12、192.168/16 |
| IPv6 | 128-bit、無 checksum/fragmentation/options、tunneling 過渡 |
| Dijkstra / Bellman-Ford | `min(D(v),D(w)+c(w,v))` / `min_v{c(x,v)+Dv(y)}` |
| count-to-infinity | poisoned reverse |
| OSPF | link-state、跑在 IP 上、Dijkstra、authentication、可分 area |
| BGP | path vector、TCP、eBGP/iBGP、policy 凌駕 performance |
| BGP 選路 | local pref → 最短 AS-PATH → hot potato → 其他 |
