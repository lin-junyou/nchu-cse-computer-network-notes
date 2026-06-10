# 期末考重點整理 — 電腦網路 Ch3 / Ch4 / Ch5

> **考試時間**：6/11（四）下午 6:30
> **考試範圍**：Chapter 3 全部、Chapter 4.1 & 4.3、Chapter 5.1 ~ 5.4
> **題型**：**簡答題（short answer）**，**考題為英文**
>
> 本檔以 Homework 3（對應 Ch3）與 Homework 4（對應 Ch4、Ch5）為骨幹。
> 每題標題為**英文原題**（即考試出題語言），下方斜體為中文題意；答案以繁體中文（臺灣用語）作答，必要的專業術語附上英文原文。
> 想看更完整的脈絡，請搭配 [Chapter_3_notes.md](Chapter_3_notes.md)、[Chapter_4_notes.md](Chapter_4_notes.md)、[Chapter_5_notes.md](Chapter_5_notes.md)。

---

## 第一部分：Chapter 3 傳輸層（對應 Homework 3）

### Q1. What are the header fields to identify a UDP socket and a TCP socket?
> *用來辨識 UDP socket 與 TCP socket 的標頭欄位分別是哪些？*

UDP socket 只需要兩個欄位來辨識，也就是「目的端 IP 位址」與「目的端埠號」這組二元組（2-tuple）。因此只要目的埠號相同，就算來源 IP 或來源埠號不同，這些封包都會被導向同一個 UDP socket。

TCP socket 則需要四個欄位，也就是「來源 IP、來源埠號、目的 IP、目的埠號」這組四元組（4-tuple）。所以即使目的埠號相同，只要來源不同，TCP 就會把它們分到不同的 socket。

### Q2. Please describe the mechanism of packet error detection using checksum.
> *請描述用 checksum 偵測封包錯誤的機制。*

傳送端會把節段（segment）的內容看成一連串的 16 位元整數，把這些整數做「1 的補數和（one's complement sum）」相加，相加時若最高位產生進位，要把進位繞回（wraparound）再加回去，最後把結果填進 checksum 欄位送出。

接收端收到後，對整個節段重新算一次 checksum，再跟標頭裡帶的值比對：如果兩者不相等，就表示偵測到錯誤；如果相等，就視為沒偵測到錯誤。不過要注意 checksum 的保護力較弱，即使位元發生翻轉，也有可能算出來的值剛好不變而沒被抓到。

### Q3. Please calculate the checksum of the following two 16-bit binary integers: `1110011001100110` & `1101010101010101`.
> *請計算這兩個 16 位元二進位整數的 checksum。*

1. 先相加：`1110011001100110 + 1101010101010101 = (1)1011101110111011`，最高位產生了一個進位。
2. 把進位繞回相加：`1011101110111011 + 1 = 1011101110111100`，這就是相加後的 sum。
3. 對 sum 取 1 的補數（每個位元 0、1 互換）即為 checksum：**`0100010001000011`**。

### Q4. Please list the network applications of UDP.
> *請列出使用 UDP 的網路應用。*

常見的有：串流多媒體（streaming multimedia，這類應用容許少量遺失、但對傳輸速率敏感）、DNS、SNMP，以及 HTTP/3。這些應用通常偏好低延遲、機制單純，或乾脆自己在應用層加上可靠度與壅塞控制，所以選擇 UDP 而不用 TCP。

### Q5. Please describe all necessary mechanisms to recover the packets with bit errors.
> *請描述要復原「位元錯誤（bit errors）」封包所需的所有機制。*

要對付位元錯誤，需要下列幾項機制（對應 rdt2.0 → rdt2.1 → rdt2.2 的演進）：

1. **錯誤偵測（checksum）**：用來偵測封包是否發生位元翻轉。
2. **接收端回饋（ACK / NAK）**：接收端用 ACK 表示「正確收到」、用 NAK 表示「收到的有錯」。
3. **重送（retransmission）**：傳送端一收到 NAK 就把該封包重送。
4. **序號（sequence number）**：當 ACK 或 NAK 本身也毀損時，無法判斷對方到底有沒有收到，傳送端只好重送；此時靠序號讓接收端辨識並丟棄重複（duplicate）封包。

### Q6. Please explain why one-bit sequence number is enough for a stop-and-wait protocol.
> *請解釋為何「一個位元的序號」對 stop-and-wait 協定就足夠。*

因為 stop-and-wait 一次只送一個封包，而且在收到該封包的 ACK 之前不會送下一個。也就是說，任何時刻「飛行中（in-flight）」的封包最多只有一個，接收端只需要分辨「目前這個」跟「下一個」兩種狀態，用 0 與 1 兩個序號交替就足以辨識是否為重複封包。

### Q7. What is the function of sequence numbers in feedback segments?
> *回饋節段（feedback segments）中序號的功能是什麼？*

讓傳送端知道「被確認（ACKed）的到底是哪一個封包」。有了序號，傳送端才能正確處理重複封包與延遲抵達的 ACK，不會把舊的 ACK 誤認成新的，也不會因此重送錯誤的封包。

### Q8. Please describe all necessary mechanisms to recover packet loss.
> *請描述要復原「封包遺失（packet loss）」所需的所有機制。*

除了前面對付位元錯誤的 checksum、ACK、重送、序號之外，要處理封包遺失還需要再加上（對應 rdt3.0）：

- **倒數計時器（countdown timer）**：傳送端送出封包後開始計時。
- **逾時重送（timeout / retransmit）**：等待一段合理時間仍沒收到 ACK，就判定可能遺失而重送。

如果封包或 ACK 其實只是延遲（而非真的遺失），重送會造成重複封包，這部分一樣交由序號來處理。

### Q9. Please describe the problems caused by improper timeout values.
> *請描述不當的 timeout 值會造成什麼問題。*

- **設得太短**：容易發生過早逾時（premature timeout），明明 ACK 還在路上卻先重送，造成不必要的重送、浪費頻寬。
- **設得太長**：一旦真的發生封包遺失，要等很久才會察覺並重送，對遺失反應太慢，導致延遲變高、效能變差。

### Q10. The performance of the stop-and-wait protocol is poor and can be improved by a pipelining protocol. Please describe the idea of the pipelining protocol.
> *stop-and-wait 效能不佳，可用 pipelining 協定改善，請描述 pipelining 的想法。*

pipelining 的核心想法是：允許傳送端同時有「多個尚未被確認的封包」在飛行中，不必送一個就停下來等一個 ACK。為了支援這種做法，需要更大的序號範圍，以及傳送端與接收端的緩衝（buffering）空間。如此一來鏈路使用率可大幅提升，例如一次管線化 3 個封包，使用率大約就能提升到原本的 3 倍。

### Q11. Please describe the difference between Go-Back-N and Selective Repeat for packet retransmission, receiver window (buffer) size, and number of timers.
> *請就「封包重送、接收端視窗（緩衝）大小、計時器數量」描述 Go-Back-N 與 Selective Repeat 的差異。*

| 項目 | Go-Back-N（GBN） | Selective Repeat（SR） |
|---|---|---|
| 重送方式 | 逾時就把封包 n **以及其後全部**重送 | 只重送**該未被確認的那一個**封包 |
| 接收端視窗／緩衝 | 不緩衝亂序封包，直接丟棄並重送對最後一個依序封包的 ACK | 會緩衝亂序封包，等補齊後再依序往上交付 |
| 計時器數量 | 只有 **1 個**（針對最舊的未確認封包） | **每個未確認封包各有一個** |

### Q12. How many sequence numbers are required for selective repeat? (window size = W)
> *Selective Repeat 需要多少序號？（視窗大小為 W）*

序號的數量必須「大於等於 2W」，也就是序號空間至少要是視窗大小的兩倍。否則接收端會無法分辨「全新的封包」與「重送的舊封包」，導致誤判。

### Q13. Please introduce the basic idea of calculating TCP timeout value.
> *請介紹計算 TCP timeout 值的基本想法。*

TCP 用「指數加權移動平均（EWMA）」來平滑化量測到的 SampleRTT，再加上一個安全邊際：

- `EstimatedRTT = (1−α)·EstimatedRTT + α·SampleRTT`（α 通常取 0.125）
- `DevRTT = (1−β)·DevRTT + β·|SampleRTT − EstimatedRTT|`（β 通常取 0.25）
- `TimeoutInterval = EstimatedRTT + 4·DevRTT`

也就是以估計的 RTT 為基礎，再加上 4 倍的偏差量當作安全邊際。RTT 變動越大，安全邊際就越大，以避免過早逾時。

### Q14. Please describe the procedure of TCP delayed ACK in TCP receivers.
> *請描述 TCP 接收端的 delayed ACK（延遲確認）程序。*

當接收端依序收到所期望的節段時，並不會馬上回 ACK，而是先等待最多 500 毫秒。如果在這段時間內又收到下一個依序的節段，就用「一個累積式 ACK（cumulative ACK）」一次確認這兩個節段；如果 500 毫秒內都沒有新的節段抵達，才把 ACK 送出去。（補充：如果收到的是亂序、出現間隙的節段，則會立刻送出重複 ACK。）

### Q15. Please describe the idea of TCP Fast Retransmit.
> *請描述 TCP Fast Retransmit（快速重送）的想法。*

如果傳送端連續收到「3 個重複的 ACK（triple duplicate ACK）」，就判定那個（最小序號的）節段很可能已經遺失，於是不必等到計時器逾時，立刻把它重送。因為收到 3 個重複 ACK 通常代表遺失的節段之後又陸續到了好幾個節段，遺失的可能性很高。

### Q16. TCP flow control can avoid receiver buffer overflows. Please describe its procedure.
> *TCP flow control 可避免接收端緩衝區溢位，請描述其程序。*

1. 接收端在 TCP 標頭的 **rwnd（receive window）**欄位中，廣告（advertise）自己目前還有多少空閒的緩衝空間，其值為「`rwnd = RcvBuffer − 已緩衝但應用程式尚未讀走的資料量`」。
2. 傳送端會把自己「飛行中（未被確認）」的資料量限制在「不超過收到的 rwnd」。
3. 如此就能保證接收端的緩衝區不會溢位。

簡單說，流量控制的目的是「避免傳送端送得太快太多，把接收端塞爆」。

### Q17. Please describe the difference of reliable data transfer between TCP and Go-Back-N.
> *請描述 TCP 與 Go-Back-N 在可靠資料傳輸上的差異。*

TCP 雖然也用累積式 ACK（這點像 GBN），但有幾個關鍵不同：TCP 接收端通常會「緩衝亂序的節段」而不是丟棄；TCP 逾時時「只重送單一、最小序號的那個節段」，而不像 GBN 把整個視窗全部重送；而且 TCP 還有 fast retransmit，收到 3 個重複 ACK 就提早重送。所以 TCP 可說是 GBN 與 SR 的混合體（hybrid）。

### Q18. Please explain the values specified in fields of sequence number and acknowledge number in a TCP header.
> *請解釋 TCP 標頭中 sequence number 與 acknowledgement number 這兩個欄位所指定的值。*

- **序號（sequence number）**：指的是這個節段所攜帶資料的「第一個位元組」在整個位元組串流（byte stream）中的編號。注意 TCP 是以「位元組」計數，不是以「節段」計數。
- **確認號（acknowledgement number）**：指的是接收端「期望對方接下來要送來的下一個位元組」的序號，這是一種累積式確認。

### Q19. TCP uses three-way handshake for connection setup. Why is two-way handshake not used?
> *TCP 用三向交握建立連線，為何不用二向交握（two-way handshake）？*

因為在真實網路中存在「延遲不固定、訊息可能遺失或被重送、訊息可能亂序、雙方又看不到對方的狀態」這些情況，二向交握在這種環境下會出問題：

- **半開連線（half-open connection）**：舊的連線請求被重送後抵達伺服器，伺服器以為是新請求而建立連線，但用戶端其實早就不存在了，留下沒人用的半開連線。
- **重複資料被誤收（duplicate data accepted）**：舊的資料被重送後抵達，伺服器把它當成新連線的資料接受。

三向交握則讓雙方互相確認「對方還活著、而且確實願意建立連線」，並且交換彼此的初始序號（initial sequence number），就能避免上述狀況。

### Q20. Please describe the four-way handshake of closing a TCP connection.
> *請描述關閉 TCP 連線的四向交握（four-way handshake）。*

1. 用戶端送出 **FIN**（表示要關閉），進入 FIN_WAIT_1。
2. 伺服器回一個 **ACK**，伺服器進入 CLOSE_WAIT（此時它仍可繼續送資料），用戶端則進入 FIN_WAIT_2。
3. 伺服器這邊也準備好關閉時，送出自己的 **FIN**，進入 LAST_ACK。
4. 用戶端回一個 **ACK**，並進入 **TIMED_WAIT**，等待 2 倍的 MSL（Max Segment Lifetime）後才真正關閉；伺服器一收到這個 ACK 就進入 CLOSED。

用戶端之所以要在 TIMED_WAIT 等 2×MSL，是為了確保最後那個 ACK 確實送達對方。

### Q21. Please describe how TCP slow start phase adjusts CWND value.
> *請描述 TCP slow start 階段如何調整 CWND（壅塞視窗）值。*

慢啟動時，cwnd 從 1 個 MSS（Maximum Segment Size）開始，而且「每收到一個 ACK 就把 cwnd 加 1 個 MSS」。這樣的效果是「每經過一個 RTT，cwnd 大約就加倍一次」，呈現指數成長。這個過程會一直持續到發生遺失事件，或是 cwnd 達到 ssthresh（門檻值）為止，之後就轉入壅塞避免（congestion avoidance）階段。

### Q22. Please describe the idea of AIMD (additive increase; multiplicative decrease) in TCP for adjusting CWND.
> *請描述 TCP 中用來調整 CWND 的 AIMD（加法增加、乘法遞減）想法。*

- **加法增加（Additive Increase）**：在沒有遇到遺失時，每經過一個 RTT 就把 cwnd 增加 1 個 MSS，持續探測（probe）目前還有多少可用頻寬。
- **乘法遞減（Multiplicative Decrease）**：一旦發生遺失事件，就把 cwnd 砍半（若是因為逾時而偵測到遺失，則直接降到 1 個 MSS）。

這種一邊慢慢試探、一邊在遇到壅塞時大幅退讓的行為，會讓 cwnd 呈現「鋸齒狀（sawtooth）」變化。AIMD 是一種分散式演算法，能讓整個網路的流速率趨於最佳化，同時保有良好的穩定性。

---

## 第二部分：Chapter 4 & 5 網路層（對應 Homework 4）

### Q1. Please describe the functions of data plane and control plane in each router.
> *請描述每台 router 中 data plane 與 control plane 的功能。*

- **資料平面（data plane）**：是每台 router 各自的「本地（per-router）」功能。它依據抵達封包的標頭欄位值，去查 forwarding table，把從輸入埠（input port）進來的封包轉送到適當的輸出埠（output port）。這對應到「轉送（forwarding）」這件事。
- **控制平面（control plane）**：是「全網（network-wide）」的邏輯，負責決定封包要如何在 router 之間沿著端到端路徑被繞送，也就是計算出 forwarding table 的內容。這對應到「路由（routing）」這件事。

### Q2. Please describe the functions of Time to Live and Upper Layer fields in IPv4 header.
> *請描述 IPv4 標頭中 Time to Live 與 Upper Layer 兩個欄位的功能。*

- **TTL（Time to Live）**：記錄封包還能再經過的最大躍程（hop）數，每經過一台 router 就減 1，一旦減到 0 就把封包丟棄。這是為了避免封包在路由迴圈（routing loop）裡無限繞行。
- **Upper Layer**：指出這個 IP 資料報的 payload 屬於哪一個上層（傳輸層）協定，例如 TCP 或 UDP。目的主機在拆封後，就靠這個欄位知道該把資料交給哪個傳輸協定處理，這就是網路層的解多工（demux）依據。

### Q3. What is CIDR?
> *什麼是 CIDR？*

CIDR 是「Classless InterDomain Routing（無類別跨網域路由）」的縮寫。它讓 IP 位址的子網路部分（也就是網路前綴 network prefix）可以是「任意長度」，以 **a.b.c.d/x** 的形式表示，其中 x 就是網路前綴所占的位元數。如此一來就不再受到傳統 Class A／B／C 那種固定切分的限制，能更有彈性地分配位址、減少浪費。

### Q4. Please describe the idea of route aggregation.
> *請描述 route aggregation（路由聚合）的想法。*

路由聚合是指 ISP 把它底下多個組織的位址區塊「合併成單一、較大的網路前綴」對外廣告。舉例來說，把 8 個 /23 的區塊聚合成 1 個 /20 一起廣告出去。這樣其他 ISP 的路由表只需要一筆項目就能涵蓋這些組織，可以大幅減少全球路由表的大小與更新（update）流量，提升路由的可擴展性。

### Q5. Why must routers select the route with a more specific subnet identifier for packet forwarding to ensure correctness?
> *為何 router 必須選擇「更明確的子網路前綴」來轉送，才能確保正確性？*

因為 router 採用「最長前綴比對（longest prefix matching）」。一個目的位址有可能同時符合好幾筆路由項目，而前綴越長（越明確、more specific）的那一筆，就越精確地指向該位址真正所在的網路。

以組織搬家為例：當某組織從原本的 ISP 換到新的 ISP，新 ISP 會廣告一條更明確的 /23 路由，這條 /23 比舊 ISP 的 /20 更明確。router 必須選較長的 /23 前綴，才能把封包送到該組織真正連接的那個 ISP，否則就會被送到錯誤的舊 ISP。

### Q6. Please describe the operations of link-state routing algorithm.
> *請描述 link-state 路由演算法的運作。*

1. 每台 router 把自己的鏈路狀態（也就是到各個鄰居的成本）透過洪泛（flood）的方式廣播給全網所有 router，於是每台 router 都擁有了完整的網路拓樸。
2. 每台 router 各自執行 **Dijkstra 演算法**：先初始化到各鄰居的成本，接著在每次迭代中，從尚未確定的節點裡挑出 D 值最小的那一個加入集合 N'，再更新它鄰居的距離估計：`D(v) = min(D(v), D(w) + c(w,v))`。
3. 一直做到所有節點都進入 N'，就得到到所有目的地的最低成本路徑樹，以及對應的 forwarding table。

### Q7. Please use an example to explain why link-state routing algorithm would cause route oscillation.
> *請用例子解釋為何 link-state 演算法會造成 route oscillation（路由震盪）。*

當「鏈路成本與流量大小有關」時就可能發生震盪。舉例：目的地為 a，流量分別從 d、c、e 三個方向進入，速率為 1、e（小於 1）、1，而鏈路成本是有方向性且與流量相關的。

一開始所有節點同步地把流量導向當下成本最低的那條路徑，結果那條路徑流量暴增、成本跟著上升；下一輪大家又一起改選另一邊的路徑，於是換那一邊變貴……如此來回擺盪，路由永遠無法穩定下來。根本原因就在於：所有節點同時依據「會隨自己選擇而改變的成本」重新算路由，造成集體性的擺動。

### Q8. Please describe the operations of distance-vector routing algorithm.
> *請描述 distance-vector 路由演算法的運作。*

1. 每個節點維護一個到所有目的地的距離向量（DV），一開始只知道到直接鄰居的成本。
2. 節點會不定時（或在 DV 改變時）把自己的 DV 傳給鄰居。
3. 當節點收到鄰居傳來的 DV，就用 **Bellman-Ford 方程式** `Dx(y) = min_v{c(x,v) + Dv(y)}` 重新計算自己到各目的地的最低成本。
4. 如果重算後自己的 DV 有變化，就再通知鄰居；如此反覆進行直到收斂。整個過程是迭代、非同步、分散式且會自我停止（self-stopping）的。

### Q9. Please describe the problem of count-to-infinity for distance-vector routing algorithm.
> *請描述 distance-vector 演算法的 count-to-infinity 問題。*

這就是所謂「壞消息傳得慢（bad news travels slow）」。當某條鏈路成本突然大幅上升時，兩個原本互相把對方當成「到目的地的路徑」的節點，會輪流以每次 +1 的方式慢慢遞增彼此的距離估計（例如 A 說經 B 是 6、B 就說經 A 是 7、A 又改成 8……），要經過很多很多輪才會慢慢收斂到正確的值。這種收斂極慢的現象就稱為 count-to-infinity。

### Q10. Please show how poisoned reverse solves the problem of count-to-infinity.
> *請說明 poisoned reverse（毒性反向）如何解決 count-to-infinity 問題。*

做法是：如果 z 是「經過 y」才能到達 x，那麼 z 就反過來向 y 謊報「z 到 x 的距離是無限大（∞）」，等於把這條反向路徑「毒化」。

這樣一來，當 y 到 x 的鏈路成本上升時，y 不會誤以為「我可以改走 z 到 x」（因為 z 告訴 y 它到 x 是 ∞），於是 y、z 之間就不會互相依賴對方的舊路徑而陷入緩慢遞增的迴圈。

不過要特別注意：poisoned reverse 只能解決兩個節點之間的迴圈，對於「三個以上節點」所形成的迴圈仍然無解。

### Q11. Please compare link-state and distance-vector algorithms in terms of message complexity, convergence speed and robustness.
> *請就 message complexity、convergence speed、robustness 比較 link-state 與 distance-vector 演算法。*

- **訊息複雜度（message complexity）**：LS 需要全網洪泛，每台 router 都要把鏈路狀態廣播給其他人，整體達 $O(n^2)$；DV 則只在相鄰的鄰居之間交換 DV。
- **收斂速度（convergence speed）**：LS 通常收斂較快，但「可能發生震盪（oscillation）」；DV 的收斂時間不固定，而且「可能出現路由迴圈與 count-to-infinity」。
- **強健性（robustness）**：LS 中每台 router 只廣告「自己的」鏈路成本、各自獨立計算自己的表，所以錯誤造成的影響相對有限；DV 中一旦某台 router 廣告了錯誤的路徑成本，這個錯誤會被鄰居採用並一路傳播到全網（造成 black-holing），影響範圍大得多。

### Q12. Please describe the concept and benefits of hierarchical OSPF.
> *請描述 hierarchical OSPF（階層式 OSPF）的概念與好處。*

- **概念**：把一個大的 AS 切成兩層結構——多個「區域（area）」加上一個「骨幹（backbone）」。鏈路狀態廣告（LS advertisement）只在各自的 area 內部洪泛，而 area 邊界 router（area border router）只會把「到該區域內各目的地的摘要（summary）距離」廣告到骨幹。
- **好處**：(1) 可擴展性更好——每台 router 只需維護自己 area 的詳細拓樸，而不必掌握整個 AS，大幅降低 LS 洪泛的範圍、計算量與路由表大小；(2) 控制了廣告流量、加快收斂；(3) 讓大型 AS 更容易管理。

### Q13. Please explain how Internet uses intra-AS and inter-AS routing to achieve scalable routing.
> *請解釋網際網路如何用 intra-AS 與 inter-AS 路由達成可擴展路由（scalable routing）。*

網際網路把眾多 router 分組成一個個「自治系統（AS, Autonomous System）」。在「AS 內部」用 intra-AS 協定（例如 OSPF）各自計算內部路由；在「AS 之間」則用 inter-AS 協定（也就是 BGP）交換「哪些目的地可以經由哪個 AS 到達」的可達性（reachability）資訊。

至於 forwarding table，是由兩者共同決定的：intra-AS 路由負責填「AS 內部目的地」的項目，而「外部目的地」的項目則由 inter-AS 與 intra-AS 一起決定。這種階層式（hierarchical）的設計能大幅縮小路由表、降低更新流量，同時讓每個 AS 保有自己的管理自主性（administrative autonomy），因此達成可擴展性。

### Q14. Please describe the functions of eBGP and iBGP sessions.
> *請描述 eBGP 與 iBGP session 的功能。*

- **eBGP（external BGP）**：跑在「不同 AS 的 gateway router 之間」，用來從鄰近的 AS 取得（並廣告）子網路的可達性資訊，也就是交換路徑廣告。
- **iBGP（internal BGP）**：跑在「同一個 AS 內部的 router 之間」，把從 eBGP 學到的外部可達性資訊，傳播給 AS 內所有的 router。

而 gateway router 會同時執行 eBGP 與 iBGP。

### Q15. Please describe the generation of forwarding-table entries in a router with BGP and OSPF.
> *請描述 router 在 BGP 與 OSPF 之下如何產生 forwarding-table 項目。*

1. **BGP（inter-AS）**先決定「要到外部目的地 X，該經過哪一個 gateway、走哪一條 AS 路徑」，並透過 iBGP 把這個資訊告知 AS 內所有 router。
2. **OSPF（intra-AS）**再決定「在自己這個 AS 內部，要走哪一個介面（interface）才能到達那個 gateway」。
3. 把兩者組合起來就填出 forwarding table 的項目。例如：BGP 決定「到 X 要經由 gateway 1c」，OSPF 算出「到 1c 要走介面 1」，於是該 router 的表就會是「到 X → 介面 1」。

一句話總結：BGP 決定「要走哪個 gateway／哪條 AS 路徑」，OSPF 決定「在 AS 內部走哪個介面到達那個 gateway」。

### Q16. Please describe how hot potato routing works.
> *請描述 hot potato routing（燙手山芋路由）如何運作。*

當某台 router 透過 iBGP 得知「可以經由好幾個本地 gateway 到達某個目的地」時，它會選擇「到該 gateway 的 intra-domain（OSPF）成本最低」的那一個。它的精神就是「儘快把封包丟出自己的 AS」，所以只在意「最快離開本地 AS 的成本」，而完全不去管 inter-domain（要跨幾個 AS）的成本。

例如 2d 經 iBGP 得知可走 2a 或 2c 到達 X，而 2d 到 2a 的 OSPF 成本（201）低於到 2c 的成本（263），那麼即使經 2a 要跨的 AS 躍程比較多，2d 仍然會選 2a。

### Q17. Please use examples to examine the operations of policy for BGP.
> *請用例子檢視 BGP 政策（policy）的運作。*

ISP 通常只想替「自己的客戶網路」載送流量，而不願意替其他 ISP 載送「轉送流量（transit traffic）」，因為那賺不到收入。可用兩個例子說明：

- **例一**（A、B、C 為 ISP，w 為客戶）：A 把路徑「A, w」同時廣告給 B 和 C。但 B 選擇「不」把「B, A, w」這條路徑廣告給 C——因為 C、A、w 對 B 來說都不是客戶，B 替它們做轉送沒有收入可言。結果 C 不知道有經過 B 的這條路，只能走「C, A, w」（不經過 B）去找 w。
- **例二**（x 是同時連到 B 和 C 的 dual-homed 客戶）：x 不想替「B 到 C」的流量當中繼站，所以 x 不會把「經由 x 可到 C」這條路徑廣告給 B。

由此可見，AS 就是透過「選擇性地廣告或不廣告某些路徑」，來實施自己的流量政策。

---

## 最後衝刺：高頻必背清單（Quick Review）

| 主題 | 一句話重點 |
|------|-----------|
| UDP／TCP 解多工 | UDP 用二元組（目的 IP＋目的埠）；TCP 用四元組 |
| rdt 機制 | 位元錯誤 → checksum＋ACK／NAK＋重送＋序號；遺失 → 再加計時器＋逾時重送 |
| stop-and-wait 序號 | 1 個位元（0／1）就夠 |
| SR 序號 | 序號數量 ≥ 2 × 視窗大小 |
| GBN vs SR | 重送全部 vs 只重送單一；不緩衝 vs 緩衝亂序；1 個計時器 vs 每封包一個 |
| Timeout | EstimatedRTT + 4·DevRTT（α=0.125, β=0.25） |
| Fast Retransmit | 收到 3 個重複 ACK 就提早重送 |
| 流量控制 | 用 rwnd 廣告空閒緩衝，限制飛行中資料量 |
| 三向／四向交握 | 建立用三向、關閉用四向（TIMED_WAIT 等 2·MSL） |
| AIMD | 加法增加、乘法遞減，呈鋸齒狀 |
| 慢啟動 | cwnd 從 1 MSS 起，每個 RTT 加倍 |
| TTL／Upper Layer | TTL 防迴圈；Upper Layer 解多工到 TCP／UDP |
| CIDR／最長前綴 | a.b.c.d/x；選最明確（最長）的前綴 |
| NAT 私有位址 | 10/8、172.16/12、192.168/16 |
| IPv6 | 128 位元、無 checksum／分段／options，過渡靠 tunneling |
| Dijkstra／Bellman-Ford | `min(D(v),D(w)+c(w,v))` ／ `min_v{c(x,v)+Dv(y)}` |
| count-to-infinity | 用 poisoned reverse 緩解 |
| OSPF | link-state、直接跑在 IP 上、用 Dijkstra、訊息有認證、可分區域 |
| BGP | path vector、走 TCP、分 eBGP／iBGP、政策凌駕效能 |
| BGP 選路順序 | 本地偏好 → 最短 AS-PATH → hot potato → 其他 |
