# 期末考重點整理 — 電腦網路 Ch3 / Ch4 / Ch5

> **考試時間**：6/11（四）下午 6:30
> **考試範圍**：Chapter 3 全部、Chapter 4.1 & 4.3、Chapter 5.1 ~ 5.4
> **題型**：**簡答題（short answer）**，**考題為英文**
>
> 每題標題為**英文原題**，下方斜體為中文題意；先看 **🔑 背誦關鍵字**（考前快速回想用），再看下方完整答案理解。
> 想看更完整脈絡請搭配 [Chapter_3_notes.md](Chapter_3_notes.md)、[Chapter_4_notes.md](Chapter_4_notes.md)、[Chapter_5_notes.md](Chapter_5_notes.md)。

---

## ⚡ 純口訣速記區（考前 5 分鐘掃這裡）

### Chapter 3
| # | 題目關鍵 | 一句話口訣 |
|---|---------|-----------|
| 1 | UDP/TCP socket 欄位 | **UDP 二、TCP 四**（UDP：目的IP+目的埠；TCP：來源IP+來源埠+目的IP+目的埠） |
| 2 | checksum 機制 | **送算 → 收算 → 比對**，不等就有錯 |
| 3 | checksum 計算 | 加 → **進位繞回** → 取補數 → `0100010001000011` |
| 4 | UDP 應用 | **串流 + DNS + SNMP + HTTP/3** |
| 5 | 復原 bit error | **偵錯+回饋+重送+序號**（checksum、ACK/NAK、重送、seq#） |
| 6 | 1-bit 序號夠 | 一次只飛一個 → 只分「這個/下一個」→ **0/1 夠** |
| 7 | 回饋序號功能 | 知道**哪個被 ACK**，處理重複與延遲 ACK |
| 8 | 復原 packet loss | bit error 那 4 個 **＋計時器＋逾時重送** |
| 9 | timeout 不當 | **太短→過早重送**；太長→反應慢 |
| 10 | pipelining | **多個未確認封包同時飛**（要大序號+緩衝） |
| 11 | GBN vs SR | 重送**全/單**；**不緩衝/緩衝**；timer **1/多** |
| 12 | SR 序號需求 | **≥ 2W**（序號 ≥ 2 倍視窗） |
| 13 | TCP timeout | **EstRTT + 4·DevRTT**（α=.125, β=.25） |
| 14 | delayed ACK | **等 500ms，湊兩個一起 ACK** |
| 15 | fast retransmit | **3 個重複 ACK → 不等逾時就重送** |
| 16 | flow control | **rwnd 宣告空閒，限制飛行量 ≤ rwnd** |
| 17 | TCP vs GBN | TCP = **GBN+SR 混合**（緩衝亂序、只重送單一、fast retransmit） |
| 18 | seq / ack 號 | seq=**本段第一個 byte**；ack=**期望的下一個 byte** |
| 19 | 為何不用 2-way | 防**半開連線 + 重複資料** |
| 20 | 4-way 關閉 | **FIN → ACK → FIN → ACK**，TIMED_WAIT 等 2·MSL |
| 21 | slow start | **1 MSS 起，每 RTT 加倍**（每收 1 ACK +1 MSS） |
| 22 | AIMD | **加法+1、乘法減半**，鋸齒狀 |

### Chapter 4 & 5
| # | 題目關鍵 | 一句話口訣 |
|---|---------|-----------|
| 1 | data/control plane | data=**本地轉送(forwarding)**；control=**全網路由(routing)** |
| 2 | TTL / Upper Layer | TTL=**減到 0 丟、防迴圈**；Upper Layer=**交給 TCP/UDP** |
| 3 | CIDR | **無類別、前綴任意長度** a.b.c.d/x |
| 4 | route aggregation | **多區塊合併成大前綴 → 縮路由表** |
| 5 | 更明確前綴 | **最長前綴比對**，越明確越精準 |
| 6 | link-state | **flood 拓樸 → 各自跑 Dijkstra** |
| 7 | oscillation | 成本隨流量 → **大家同步換邊 → 來回擺盪** |
| 8 | distance-vector | **存 DV → 傳鄰居 → Bellman-Ford 重算 → 變了再傳** |
| 9 | count-to-infinity | **壞消息傳得慢**，+1 慢慢遞增 |
| 10 | poisoned reverse | 經 y 到 x，就**對 y 謊報 ∞** |
| 11 | LS vs DV | 訊息 **LS O(n²)/DV 鄰居**；收斂 **LS 快可能震盪/DV 慢可能 count-∞**；強健 **LS 侷限/DV 全網擴散** |
| 12 | hierarchical OSPF | **切 area + backbone → 縮 flood 範圍** |
| 13 | scalable routing | **分 AS：內 OSPF、外 BGP** |
| 14 | eBGP / iBGP | **eBGP 跨 AS、iBGP AS 內** |
| 15 | BGP+OSPF 建表 | **BGP 挑 gateway/AS 路徑、OSPF 挑介面** |
| 16 | hot potato | **最快丟出 AS**：選 OSPF 成本最低的 gateway |
| 17 | BGP policy | **不替別人做 transit**，靠選擇性宣告 |

---

## 第一部分：Chapter 3 傳輸層

### Q1. What are the header fields to identify a UDP socket and a TCP socket?
> *用來辨識 UDP socket 與 TCP socket 的標頭欄位分別是哪些？*

🔑 **UDP 二、TCP 四**：UDP=（目的 IP + 目的埠）；TCP=（來源 IP + 來源埠 + 目的 IP + 目的埠）。

UDP socket 只需「目的端 IP + 目的端埠號」這組二元組（2-tuple）。所以只要目的埠相同，就算來源不同也會進同一個 UDP socket。TCP socket 則要「來源 IP、來源埠、目的 IP、目的埠」這組四元組（4-tuple），所以來源不同就會被分到不同 socket。

### Q2. Please describe the mechanism of packet error detection using checksum.
> *請描述用 checksum 偵測封包錯誤的機制。*

🔑 **送算 → 收算 → 比對**，不等就有錯（保護力弱）。

傳送端把節段視為一連串 16 位元整數，做「1 的補數和」相加（進位要繞回 wraparound），結果放進 checksum 欄位。接收端重新算一次再跟標頭值比對：不等→偵測到錯誤；相等→視為沒錯。注意保護力弱，位元翻轉後仍可能算出相同值而漏抓。

### Q3. Please calculate the checksum of the following two 16-bit binary integers: `1110011001100110` & `1101010101010101`.
> *請計算這兩個 16 位元二進位整數的 checksum。*

🔑 **加 → 進位繞回 → 取補數** → `0100010001000011`。

1. 相加：`1110011001100110 + 1101010101010101 = (1)1011101110111011`（最高位有進位）。
2. 進位繞回：`1011101110111011 + 1 = 1011101110111100`（sum）。
3. sum 取 1 的補數 → **`0100010001000011`**。

### Q4. Please list the network applications of UDP.
> *請列出使用 UDP 的網路應用。*

🔑 **串流 + DNS + SNMP + HTTP/3**。

串流多媒體（容許少量遺失、對速率敏感）、DNS、SNMP、HTTP/3。這些應用偏好低延遲、機制單純，或自己在應用層加可靠度與壅塞控制。

### Q5. Please describe all necessary mechanisms to recover the packets with bit errors.
> *請描述要復原「位元錯誤（bit errors）」封包所需的所有機制。*

🔑 **偵錯 + 回饋 + 重送 + 序號**（rdt2.0→2.1→2.2）。

1. **錯誤偵測（checksum）**：偵測位元翻轉。
2. **接收端回饋（ACK / NAK）**：ACK=正確收到、NAK=有錯。
3. **重送（retransmission）**：收到 NAK 就重送。
4. **序號（sequence number）**：ACK/NAK 自己也可能毀損而被迫重送，靠序號讓接收端丟棄重複封包。

### Q6. Please explain why one-bit sequence number is enough for a stop-and-wait protocol.
> *請解釋為何「一個位元的序號」對 stop-and-wait 協定就足夠。*

🔑 **一次只飛一個 → 只需分「這個/下一個」→ 0/1 夠**。

stop-and-wait 一次只送一個封包、收到 ACK 前不送下一個，所以任何時刻飛行中的封包最多一個，接收端只需分辨「目前」與「下一個」，用 0、1 交替就能辨識重複。

### Q7. What is the function of sequence numbers in feedback segments?
> *回饋節段（feedback segments）中序號的功能是什麼？*

🔑 **知道哪個封包被 ACK**，正確處理重複與延遲 ACK。

讓傳送端知道被確認的是哪一個封包，才能正確處理重複封包與延遲抵達的 ACK，不會把舊 ACK 誤認成新的、也不會重送錯封包。

### Q8. Please describe all necessary mechanisms to recover packet loss.
> *請描述要復原「封包遺失（packet loss）」所需的所有機制。*

🔑 **bit error 那 4 個 ＋ 計時器 + 逾時重送**（rdt3.0）。

除了 checksum、ACK、重送、序號之外，再加：**倒數計時器**（送出後開始計時）與**逾時重送**（等合理時間沒收到 ACK 就重送）。若只是延遲造成的重複，一樣交給序號處理。

### Q9. Please describe the problems caused by improper timeout values.
> *請描述不當的 timeout 值會造成什麼問題。*

🔑 **太短→過早重送（浪費頻寬）；太長→反應慢（效能差）**。

- 太短：過早逾時（premature timeout），ACK 還在路上就重送，浪費頻寬。
- 太長：真的遺失時要等很久才察覺，反應慢、延遲高、效能差。

### Q10. ... Please describe the idea of the pipelining protocol.
> *stop-and-wait 效能不佳，請描述 pipelining 的想法。*

🔑 **多個未確認封包同時飛**（要更大序號 + 緩衝）。

允許傳送端同時有多個「尚未被確認」的封包在飛行中，不必送一個等一個。需要更大的序號範圍與雙方緩衝，使用率可大幅提升（管線 3 個 → 約 3 倍）。

### Q11. Please describe the difference between Go-Back-N and Selective Repeat ...
> *請就「重送、接收端視窗、計時器數量」比較 GBN 與 SR。*

🔑 **重送 全/單；緩衝 不/有；timer 1/多**。

| 項目 | Go-Back-N | Selective Repeat |
|---|---|---|
| 重送 | 封包 n **及其後全部** | 只重送**該一個** |
| 接收緩衝 | 不緩衝亂序，丟棄並重送舊 ACK | 緩衝亂序，補齊後依序交付 |
| 計時器 | **1 個**（最舊未確認） | **每個未確認各 1 個** |

### Q12. How many sequence numbers are required for selective repeat? (window size = W)
> *SR 需要多少序號？*

🔑 **≥ 2W**（序號 ≥ 2 倍視窗）。

序號數量必須 ≥ 2W，否則接收端分不清「新封包」與「重送的舊封包」。

### Q13. Please introduce the basic idea of calculating TCP timeout value.
> *請介紹計算 TCP timeout 值的基本想法。*

🔑 **EstRTT + 4·DevRTT**（EWMA 平滑，α=.125、β=.25）。

用 EWMA 平滑量測的 SampleRTT，再加安全邊際：
- `EstimatedRTT = (1−α)·EstimatedRTT + α·SampleRTT`（α=0.125）
- `DevRTT = (1−β)·DevRTT + β·|SampleRTT − EstimatedRTT|`（β=0.25）
- `TimeoutInterval = EstimatedRTT + 4·DevRTT`

RTT 變動越大，安全邊際越大。

### Q14. Please describe the procedure of TCP delayed ACK in TCP receivers.
> *請描述 TCP delayed ACK 程序。*

🔑 **等 500ms，湊兩個一起 ACK**。

依序收到期望節段時先不立刻回 ACK，等最多 500ms；若期間又來下一個依序節段，就用一個累積式 ACK 一次確認兩個；500ms 內沒新節段才送 ACK。（亂序/有間隙則立刻送重複 ACK。）

### Q15. Please describe the idea of TCP Fast Retransmit.
> *請描述 TCP Fast Retransmit 的想法。*

🔑 **3 個重複 ACK → 不等逾時就重送**。

傳送端連收 3 個重複 ACK（triple duplicate ACK），判定該（最小序號）節段很可能遺失，不必等逾時就立刻重送。

### Q16. TCP flow control ... Please describe its procedure.
> *請描述 TCP flow control 程序。*

🔑 **rwnd 宣告空閒，飛行量 ≤ rwnd**。

1. 接收端用標頭 **rwnd** 欄位宣告空閒緩衝：`rwnd = RcvBuffer − 已緩衝未讀資料`。
2. 傳送端把飛行中（未確認）資料量限制在 ≤ rwnd。
3. 保證接收端緩衝區不溢位。目的=避免傳送端塞爆接收端。

### Q17. Please describe the difference of reliable data transfer between TCP and Go-Back-N.
> *請描述 TCP 與 GBN 的可靠傳輸差異。*

🔑 **TCP = GBN + SR 混合**。

TCP 也用累積式 ACK（像 GBN），但：會**緩衝亂序節段**（GBN 丟棄）、逾時**只重送單一最小序號節段**（非全部）、還有 **fast retransmit**。所以 TCP 是 GBN 與 SR 的混合體。

### Q18. Please explain the values ... sequence number and acknowledge number ...
> *請解釋 TCP 標頭 seq 與 ack 號的值。*

🔑 **seq = 本段第一個 byte 的編號；ack = 期望的下一個 byte**。

- **序號**：本節段資料第一個位元組在 byte stream 中的編號（以 byte 計，不是 segment）。
- **確認號**：接收端期望對方接下來送來的下一個位元組序號（累積式）。

### Q19. ... Why is two-way handshake not used?
> *為何不用二向交握？*

🔑 **防半開連線 + 重複資料**。

真實網路有可變延遲、遺失重送、亂序、看不到對方狀態，2-way 會出問題：
- **半開連線**：舊請求重送抵達，伺服器建立連線但用戶端早已不在。
- **重複資料被誤收**：舊資料重送被當新連線資料接受。

3-way 讓雙方互相確認「對方活著且願意連線」並交換初始序號，避免上述問題。

### Q20. Please describe the four-way handshake of closing a TCP connection.
> *請描述關閉 TCP 連線的四向交握。*

🔑 **FIN → ACK → FIN → ACK**，TIMED_WAIT 等 2·MSL。

1. 用戶端送 **FIN** → FIN_WAIT_1。
2. 伺服器回 **ACK** → 伺服器 CLOSE_WAIT（仍可送資料）、用戶端 FIN_WAIT_2。
3. 伺服器送 **FIN** → LAST_ACK。
4. 用戶端回 **ACK** → 進 **TIMED_WAIT**（等 2×MSL 確保 ACK 送達）後關閉；伺服器收到 ACK 即 CLOSED。

### Q21. Please describe how TCP slow start phase adjusts CWND value.
> *請描述 slow start 如何調整 CWND。*

🔑 **1 MSS 起，每 RTT 加倍**（每收 1 ACK +1 MSS）。

cwnd 從 1 MSS 開始，每收到一個 ACK 就 +1 MSS，效果是每個 RTT 加倍（指數成長），直到發生遺失或 cwnd 達 ssthresh 才轉入壅塞避免。

### Q22. Please describe the idea of AIMD ...
> *請描述 AIMD（加法增加、乘法遞減）。*

🔑 **加法 +1 MSS、乘法減半**，鋸齒狀。

- **加法增加**：沒遇遺失時每個 RTT +1 MSS，持續探測可用頻寬。
- **乘法遞減**：遇遺失就把 cwnd 減半（逾時則降到 1 MSS）。

形成鋸齒狀（sawtooth），是分散式演算法，能最佳化全網流速且穩定。

---

## 第二部分：Chapter 4 & 5 網路層

### Q1. Please describe the functions of data plane and control plane in each router.
> *請描述 router 中 data plane 與 control plane 的功能。*

🔑 **data = 本地轉送(forwarding)；control = 全網路由(routing)**。

- **資料平面**：每台 router 的本地功能，依封包標頭查 forwarding table，把輸入埠封包轉到適當輸出埠（= forwarding）。
- **控制平面**：全網邏輯，決定封包如何在 router 間沿端到端路徑繞送，即算出 forwarding table 內容（= routing）。

### Q2. Please describe the functions of Time to Live and Upper Layer fields in IPv4 header.
> *請描述 TTL 與 Upper Layer 欄位的功能。*

🔑 **TTL=減到 0 丟、防迴圈；Upper Layer=交給 TCP/UDP**。

- **TTL**：剩餘最大躍程數，每經一台 router −1，減到 0 即丟棄，防封包在路由迴圈中無限繞行。
- **Upper Layer**：指出 payload 屬哪個傳輸協定（TCP/UDP），讓目的主機拆封後交給正確協定（網路層 demux）。

### Q3. What is CIDR?
> *什麼是 CIDR？*

🔑 **無類別、前綴任意長度** a.b.c.d/x。

CIDR（Classless InterDomain Routing）讓網路前綴可為任意長度，以 a.b.c.d/x 表示（x=前綴位元數），不受 Class A/B/C 固定切分限制，分配更有彈性、減少浪費。

### Q4. Please describe the idea of route aggregation.
> *請描述 route aggregation 的想法。*

🔑 **多區塊合併成大前綴 → 縮路由表**。

ISP 把底下多個組織的位址區塊合併成單一較大前綴對外宣告（如 8 個 /23 → 1 個 /20），其他 ISP 路由表只需一筆即可涵蓋，減少路由表大小與更新流量。

### Q5. Why must routers select the route with a more specific subnet identifier ...?
> *為何必須選更明確的前綴才正確？*

🔑 **最長前綴比對**，越明確越精準。

採最長前綴比對（longest prefix matching）。一個目的位址可能符合多筆路由，前綴越長（越明確）越精準指向其真正所在網路。例如組織搬家後，新 ISP 的 /23 比舊 ISP 的 /20 更明確，選較長前綴才送得到正確 ISP。

### Q6. Please describe the operations of link-state routing algorithm.
> *請描述 link-state 演算法的運作。*

🔑 **flood 拓樸 → 各自跑 Dijkstra**。

1. 每台 router 把自己的鏈路狀態（到各鄰居成本）flood 給全網 → 人人有完整拓樸。
2. 各自跑 **Dijkstra**：初始化到鄰居成本，每次迭代挑出 D 最小且未確定的節點加入 N'，更新鄰居 `D(v)=min(D(v), D(w)+c(w,v))`。
3. 全部進 N' → 得最低成本路徑樹與 forwarding table。

### Q7. ... why link-state routing algorithm would cause route oscillation.
> *為何 link-state 會造成路由震盪？*

🔑 **成本隨流量 → 大家同步換邊 → 來回擺盪**。

當鏈路成本與流量大小有關時：所有節點同步把流量導向當下低成本路徑 → 該路徑流量暴增、成本上升 → 下一輪大家又改選另一邊 → 那邊又變貴……如此來回擺盪、永不穩定。

### Q8. Please describe the operations of distance-vector routing algorithm.
> *請描述 distance-vector 演算法的運作。*

🔑 **存 DV → 傳鄰居 → Bellman-Ford 重算 → 變了再傳**。

1. 每節點維護到所有目的地的 DV，初始只知到鄰居成本。
2. 不時把自己的 DV 傳給鄰居。
3. 收到鄰居 DV 後用 **Bellman-Ford** `Dx(y)=min_v{c(x,v)+Dv(y)}` 重算。
4. DV 有變才再通知鄰居，反覆至收斂（迭代、非同步、分散式、自我停止）。

### Q9. Please describe the problem of count-to-infinity ...
> *請描述 count-to-infinity 問題。*

🔑 **壞消息傳得慢**，+1 慢慢遞增。

鏈路成本大增時，兩個互相把對方當成「到目的地路徑」的節點會輪流以 +1 緩慢遞增估計值（A 說 6、B 說 7、A 說 8…），要很多輪才收斂到正確值。

### Q10. Please show how poisoned reverse solves the problem of count-to-infinity.
> *poisoned reverse 如何解決 count-to-infinity？*

🔑 **經 y 到 x，就對 y 謊報 ∞**。

若 z 是「經過 y」才能到 x，z 就向 y 謊報「z 到 x 的距離 = ∞」（毒化反向路徑）。如此 y 不會誤以為能經 z 到 x，避免 y↔z 互相依賴舊路徑而緩慢遞增。⚠️ 無法解決三個以上節點的迴圈。

### Q11. Please compare link-state and distance-vector ... message complexity, convergence speed and robustness.
> *請比較 LS 與 DV 的訊息複雜度、收斂速度、強健性。*

🔑 **訊息 LS O(n²)/DV 鄰居；收斂 LS 快可能震盪/DV 慢可能 count-∞；強健 LS 侷限/DV 全網擴散**。

- **訊息複雜度**：LS 全網 flood，$O(n^2)$；DV 只在鄰居間交換。
- **收斂速度**：LS 較快但可能震盪；DV 不定，可能有路由迴圈與 count-to-infinity。
- **強健性**：LS 各 router 只宣告自己鏈路成本、各自算表，錯誤影響有限；DV 錯誤路徑成本會被鄰居採用並傳播全網（black-holing）。

### Q12. Please describe the concept and benefits of hierarchical OSPF.
> *請描述階層式 OSPF 的概念與好處。*

🔑 **切 area + backbone → 縮 flood 範圍**。

- **概念**：把 AS 切成多個 **area** + 一個 **backbone** 兩層；LS 宣告只在各 area 內 flood，area 邊界 router 只把「到區域內目的地的摘要距離」宣告到 backbone。
- **好處**：每台 router 只需維護自家 area 拓樸 → 降低 flood 範圍、計算量、路由表大小；控制流量、加快收斂；便於大型 AS 管理。

### Q13. ... how Internet uses intra-AS and inter-AS routing to achieve scalable routing.
> *網際網路如何用 intra-AS 與 inter-AS 達成可擴展路由？*

🔑 **分 AS：內 OSPF、外 BGP**。

把 router 分組成 **AS**。AS 內部用 intra-AS 協定（OSPF）算內部路由；AS 之間用 inter-AS 協定（BGP）交換可達性。forwarding table：intra-AS 決定內部目的地項目，inter-AS + intra-AS 共同決定外部目的地項目。階層式設計縮小路由表、降低更新流量，並保有各 AS 管理自主性。

### Q14. Please describe the functions of eBGP and iBGP sessions.
> *請描述 eBGP 與 iBGP session 的功能。*

🔑 **eBGP 跨 AS、iBGP AS 內；gateway 兩者都跑**。

- **eBGP**：跑在不同 AS 的 gateway router 間，取得/宣告鄰近 AS 的子網路可達性。
- **iBGP**：跑在同一 AS 內部 router 間，把外部可達性傳播給 AS 內所有 router。
- gateway router 同時跑兩者。

### Q15. Please describe the generation of forwarding-table entries in a router with BGP and OSPF.
> *請描述 BGP 與 OSPF 下如何產生 forwarding-table 項目。*

🔑 **BGP 挑 gateway/AS 路徑、OSPF 挑介面**。

1. **BGP（inter-AS）**決定「到外部目的地 X 要經哪個 gateway/哪條 AS 路徑」，經 iBGP 告知 AS 內所有 router。
2. **OSPF（intra-AS）**決定「在 AS 內走哪個介面到那個 gateway」。
3. 兩者組合填出項目，例如 BGP 說「到 X 經 gateway 1c」、OSPF 說「到 1c 走介面 1」→「到 X → 介面 1」。

### Q16. Please describe how hot potato routing works.
> *請描述 hot potato routing 如何運作。*

🔑 **最快丟出 AS**：選 OSPF 成本最低的 gateway。

router 經 iBGP 得知可經多個本地 gateway 到某目的地時，選「到該 gateway 的 intra-domain（OSPF）成本最低」者，目的是儘快把封包丟出自己的 AS，不在意 inter-domain（AS 躍程）成本。

### Q17. Please use examples to examine the operations of policy for BGP.
> *請用例子檢視 BGP 政策的運作。*

🔑 **不替別人做 transit**，靠選擇性宣告。

ISP 只想為自己的客戶網路載送流量，不想替別的 ISP 做 transit（沒收入）。
- **例一**：A 宣告 A,w 給 B 和 C；B 不把 B,A,w 宣告給 C（C、A、w 都非 B 客戶）→ C 只能走 C,A,w（不經 B）。
- **例二**：dual-homed 客戶 x（同連 B、C）不想替 B↔C 中繼 → x 不向 B 宣告「經 x 到 C」的路徑。
- → 靠「選擇性地宣告/不宣告路徑」實施流量政策。
