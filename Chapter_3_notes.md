# 第三章：傳輸層 (Transport Layer)

> 教科書：Computer Networking: A Top-Down Approach, 8th Edition
> 期末考範圍：**Chapter 3 全部**

---

## 3.1 傳輸層服務 (Transport-Layer Services)

### 傳輸層的角色

- 在執行於不同主機的**應用程式行程 (application processes)** 之間提供**邏輯通訊 (logical communication)**
- 傳輸層協定的動作發生在**終端系統 (end systems)**：
  - **傳送端 (sender)**：將應用層訊息切割成**節段 (segments)**，往下傳給網路層
  - **接收端 (receiver)**：將節段重組為訊息，往上交給應用層
- 網際網路提供兩種傳輸協定：**TCP** 與 **UDP**

### 傳輸層 vs. 網路層

| 層級 | 提供的邏輯通訊 |
|------|----------------|
| **網路層 (network layer)** | 主機 (hosts) 之間的邏輯通訊 |
| **傳輸層 (transport layer)** | 行程 (processes) 之間的邏輯通訊；**依賴並強化**網路層的服務 |

> **家庭類比**：12 個小孩在 Ann 家寄信給 Bill 家的 12 個小孩。
> - 主機 = 房子；行程 = 小孩；應用訊息 = 信件
> - **傳輸協定 = Ann 與 Bill**（負責分信給家裡的兄弟姊妹 → demux）
> - **網路層協定 = 郵政服務**

### 兩大網際網路傳輸協定

| | **TCP** (Transmission Control Protocol) | **UDP** (User Datagram Protocol) |
|---|---|---|
| 可靠性 | 可靠 (reliable)、依序 (in-order) 傳遞 | 不可靠 (unreliable)、不依序 |
| 壅塞控制 | 有 (congestion control) | 無 |
| 流量控制 | 有 (flow control) | 無 |
| 連線 | 連線導向，需連線設定 (connection setup) | 無連線 |
| 本質 | — | 「best-effort」IP 的簡易延伸 |

- **兩者皆不提供**：延遲保證 (delay guarantees)、頻寬保證 (bandwidth guarantees)

---

## 3.2 多工與解多工 (Multiplexing / Demultiplexing)

| 名詞 | 說明 |
|------|------|
| **多工 (multiplexing)**（傳送端） | 處理來自多個 socket 的資料，加上傳輸標頭（供日後解多工用） |
| **解多工 (demultiplexing)**（接收端） | 利用標頭資訊，將收到的節段交給**正確的 socket** |

### 解多工如何運作

- 主機收到 IP 資料報 (datagram)，每個資料報帶有 **來源 IP、目的 IP**
- 每個資料報攜帶一個傳輸層節段，每個節段有 **來源埠號、目的埠號**
- 主機用 **IP 位址 + 埠號** 把節段導向正確的 socket

### 無連線解多工 (Connectionless / UDP)

- UDP socket 由 **二元組 (2-tuple)** 識別：**(目的 IP, 目的埠號)**
- 接收端只檢查**目的埠號**，把節段導向該埠號的 socket
- ⭐ **來源 IP / 來源埠號不同**、但**目的埠號相同**的資料報，會被導向**同一個** socket

### 連線導向解多工 (Connection-oriented / TCP)

- TCP socket 由 **四元組 (4-tuple)** 識別：
  - **來源 IP、來源埠號、目的 IP、目的埠號**
- 接收端用**全部四個值**來導向正確的 socket
- 一台伺服器可同時支援多個 TCP socket，每個 socket 對應一個不同的連線用戶端

> **考點 (HW3-1)**：辨識 UDP socket 用 **(目的 IP, 目的埠號)** 兩欄位；辨識 TCP socket 用 **(來源 IP, 來源埠, 目的 IP, 目的埠)** 四欄位。

---

## 3.3 無連線傳輸：UDP (Connectionless Transport)

### UDP 特性

- 「no frills / bare bones」陽春傳輸協定，提供 **best-effort** 服務
- UDP 節段可能：**遺失 (lost)**、**亂序送達 (out-of-order)**
- 無連線 (connectionless)：傳送/接收端無交握 (no handshaking)，每個節段獨立處理

### 為什麼要有 UDP？

- **無連線建立**：不需 RTT 延遲（建立連線會增加延遲）
- **簡單**：傳送/接收端無連線狀態 (no connection state)
- **標頭小** (small header, 僅 8 bytes)
- **無壅塞控制**：UDP 可以「全速狂送」，網路壅塞時仍能運作

### UDP 的應用 (HW3-4)

- **串流多媒體** (loss tolerant、rate sensitive)
- **DNS**
- **SNMP**
- **HTTP/3**（在應用層自行加上可靠度與壅塞控制）

### UDP 節段標頭 (8 bytes)

| 欄位 | 大小 | 說明 |
|------|------|------|
| 來源埠號 (source port #) | 16 bits | |
| 目的埠號 (dest port #) | 16 bits | |
| 長度 (length) | 16 bits | UDP 節段位元組數（含標頭） |
| 檢查碼 (checksum) | 16 bits | 偵測錯誤 |

### UDP Checksum (HW3-2, HW3-3)

- **目標**：偵測傳輸節段中的**錯誤（位元翻轉, flipped bits）**
- **傳送端**：
  1. 將 UDP 節段內容（含 UDP 標頭與 IP 位址）視為一連串 **16-bit 整數**
  2. checksum = 各 16-bit 整數的**相加（1 的補數和, one's complement sum）**
  3. 把 checksum 放入 checksum 欄位
- **接收端**：
  1. 對收到的節段重新計算 checksum
  2. 與標頭中的 checksum 比對：**不等 → 偵測到錯誤**；**相等 → 沒偵測到錯誤**（但可能仍有錯）
- ⭐ **加法注意**：最高位元產生的進位 (carryout) 要**繞回 (wraparound)** 加回結果
- ⚠️ **弱保護**：即使位元翻轉，checksum 仍可能不變（保護力弱）

> **HW3-3 計算範例**：求 `1110011001100110` 與 `1101010101010101` 的 checksum
> 1. 相加：`1110011001100110 + 1101010101010101 = 1 1011101110111011`（有進位）
> 2. 進位繞回：`1011101110111011 + 1 = 1011101110111100`（sum）
> 3. checksum = sum 取 1 的補數 = `0100010001000011`

---

## 3.4 可靠資料傳輸原理 (Principles of Reliable Data Transfer, rdt)

> 在**不可靠 (unreliable)** 的底層通道上，提供**可靠**的服務抽象。複雜度取決於不可靠通道的特性（會遺失？毀損？亂序？）。

### rdt 介面

| 函式 | 呼叫者 | 說明 |
|------|--------|------|
| `rdt_send()` | 上層（應用） | 要傳送的資料交給 rdt |
| `udt_send()` | rdt | 把封包透過**不可靠通道**送出 |
| `rdt_rcv()` | 系統 | 封包抵達接收端時被呼叫 |
| `deliver_data()` | rdt | 把資料交給上層 |

### rdt 各版本演進 (HW3-5, HW3-8)

| 版本 | 通道假設 | 新增機制 |
|------|----------|----------|
| **rdt1.0** | 完全可靠（無錯、無遺失） | 無需特別機制 |
| **rdt2.0** | 可能有**位元錯誤** | **checksum**（偵錯）、**ACK / NAK**、出錯重送 |
| **rdt2.1** | ACK/NAK 也可能毀損 | 封包加 **序號 (seq #, 0/1)**、收到重複封包則丟棄 |
| **rdt2.2** | 同上，但**不用 NAK** | 以「對最後正確封包的 ACK（含 seq #）」取代 NAK |
| **rdt3.0** | 可能有**錯誤 + 遺失** | **倒數計時器 (timer)**、逾時重送 (timeout retransmit) |

#### ⭐ 復原「位元錯誤」的必要機制 (HW3-5)

1. **錯誤偵測 (checksum)**：偵測位元翻轉
2. **接收端回饋**：
   - **ACK**：明確告知封包正確收到
   - **NAK**：明確告知封包有錯
3. **重送 (retransmission)**：收到 NAK 即重送
4. **序號 (sequence number)**：處理 ACK/NAK 毀損造成的**重複封包**

#### ⭐ 復原「封包遺失」的必要機制 (HW3-8)

- 在前述機制之外，再加：
  - **倒數計時器 (countdown timer)**：傳送端等待「合理時間」
  - **逾時重送 (timeout retransmit)**：時間內未收到 ACK 就重送
  - 若封包/ACK 只是延遲（非遺失），重送會造成重複 → **序號已可處理**

> **為何 stop-and-wait 一個位元序號就夠？(HW3-6)**
> Stop-and-wait 一次只送一個封包並等待回應，傳送端在收到當前封包的 ACK 前不會送下一個。任一時刻「飛行中」的封包只有一個，傳送端只需區分「目前」與「下一個」兩種封包 → **0/1 兩個序號**即足夠。

> **回饋節段中序號的功能 (HW3-7)**：ACK 攜帶序號，讓傳送端知道**是哪一個封包**被確認，以正確處理重複封包與延遲的 ACK。

> **不當 timeout 值造成的問題 (HW3-9)**
> - **太短 (too short)**：過早逾時 (premature timeout) → 不必要的重送
> - **太長 (too long)**：對封包遺失反應太慢，效能差

### rdt3.0 效能：Stop-and-Wait

- $U_{sender}$（使用率）= 傳送端忙於傳送的時間比例
- $$U_{sender} = \frac{L/R}{RTT + L/R}$$
- **範例**：1 Gbps 鏈路、15 ms 傳播延遲、8000 bit 封包
  - $D_{trans} = L/R = 8000/10^9 = 8\ \mu s$
  - $U_{sender} = 0.008 / 30.008 \approx 0.00027$（0.027%）→ **效能極差**
- 結論：協定限制了底層基礎建設（通道）的效能

### 管線化 (Pipelining) (HW3-10)

> **idea**：傳送端允許**多個「飛行中 (in-flight)」、尚未被確認**的封包同時存在，不必一送一等。

- 需要：**增加序號範圍**、**傳送端 / 接收端緩衝 (buffering)**
- 效果：使用率大幅提升（例：3 個封包管線化 → 使用率提升 3 倍）
- 兩種管線化協定：**Go-Back-N (GBN)** 與 **Selective Repeat (SR)**

### Go-Back-N (GBN)

- **傳送端**：視窗 (window) 內最多 N 個連續、已送但未確認的封包
  - **累積式 ACK (cumulative ACK)**：ACK(n) 確認**到 n 為止（含 n）**的所有封包；收到 ACK(n) → 視窗前移到 n+1
  - **單一計時器**：只為「最舊的飛行中封包」計時
  - **timeout(n)**：重送封包 n **以及視窗內所有更高序號**的封包
- **接收端**：
  - 只送「目前正確依序收到的最高序號」的 ACK（可能產生**重複 ACK**）
  - 收到**亂序封包**：**丟棄 (discard)、不緩衝**，重送對最高依序封包的 ACK
  - 只需記住 `rcv_base`（**無接收端緩衝**）

### Selective Repeat (SR)

- **接收端**：**個別確認**每個正確收到的封包，**緩衝 (buffer)** 亂序封包以待日後依序交付
- **傳送端**：為**每個未確認封包個別計時**，逾時只重送**該封包**
- 視窗：N 個連續序號

#### GBN vs. Selective Repeat 比較 (HW3-11)

| 項目 | Go-Back-N | Selective Repeat |
|------|-----------|------------------|
| 重送方式 | 逾時重送封包 n **及之後全部** | 只重送**該未確認封包** |
| 接收端視窗(緩衝) | 不緩衝亂序封包（視窗=1 概念） | 緩衝亂序封包 |
| 計時器數量 | **1 個**（最舊未確認封包） | **每個未確認封包各 1 個** |
| ACK 型態 | 累積式 ACK | 個別 ACK |

#### SR 的兩難 (dilemma) 與序號需求 (HW3-12)

- 若**視窗大小過大**，接收端無法分辨「新封包」與「重送的舊封包」
- ⭐ **避免問題的條件**：**序號數量 ≥ 2 × 視窗大小**
  - 即 window size $W$ 時，需 **$\ge 2W$ 個序號**

---

## 3.5 連線導向傳輸：TCP

### TCP 概觀

- **點對點 (point-to-point)**：一個傳送端、一個接收端
- **可靠、依序的位元組串流 (reliable, in-order byte stream)**：無「訊息邊界」
- **全雙工 (full duplex)**：同一連線雙向資料流；**MSS** = 最大節段大小 (Maximum Segment Size)
- **累積式 ACK**、**管線化**（視窗大小由壅塞控制與流量控制決定）
- **連線導向**：交握 (handshaking) 初始化雙方狀態
- **流量控制**：傳送端不會壓垮接收端

### TCP 節段結構

| 欄位 | 說明 |
|------|------|
| 來源埠號 / 目的埠號 | 解多工用 |
| **序號 (sequence number)** | 節段資料**第一個位元組**在位元組串流中的編號（計算的是**位元組**，不是節段！） |
| **確認號 (acknowledgement number)** | 期望從對方收到的**下一個位元組**的序號（累積式 ACK） |
| 標頭長度 (header length) | TCP 標頭長度 |
| 旗標 C, E | 壅塞通知 (congestion notification) |
| 旗標 **RST, SYN, FIN** | 連線管理 |
| 旗標 A | 此為 ACK |
| **接收視窗 (receive window, rwnd)** | 流量控制：接收端願意接受的位元組數 |
| checksum | Internet checksum |

> **序號與確認號的值 (HW3-18)**
> - **序號**：節段中第一個資料位元組的「位元組串流編號」
> - **確認號**：接收端期望對方傳來的**下一個位元組**序號（= 已累積確認位元組的下一個）
> - TCP 規格**未規定**亂序節段如何處理，由實作者決定

### TCP RTT 估算與 Timeout (HW3-13)

- **如何設 timeout？** 要比 RTT 長，但 RTT 會變動
  - 太短 → 過早逾時、不必要重送；太長 → 對遺失反應慢
- **SampleRTT**：從節段送出到收到 ACK 的時間（忽略重送的節段）
- **EWMA（指數加權移動平均）平滑化**：
  $$EstimatedRTT = (1-\alpha)\cdot EstimatedRTT + \alpha\cdot SampleRTT$$
  - 典型 $\alpha = 0.125$；過去樣本影響呈**指數衰減**
- **安全邊際 (safety margin)**：
  $$DevRTT = (1-\beta)\cdot DevRTT + \beta\cdot|SampleRTT - EstimatedRTT|$$
  $$TimeoutInterval = EstimatedRTT + 4\cdot DevRTT$$
  - 典型 $\beta = 0.25$；EstimatedRTT 變動大 → 安全邊際要大

### TCP 傳送端事件（簡化）

1. **收到應用資料**：建立節段（seq # = 第一個位元組編號），若計時器未跑就啟動
2. **timeout**：重送造成逾時的（最小序號）節段，重啟計時器
3. **收到 ACK**：若確認了先前未確認的節段，更新已確認資訊；若仍有未確認節段則重啟計時器

### TCP 接收端：ACK 產生 [RFC 5681] (HW3-14)

| 接收端事件 | 動作 |
|------------|------|
| 依序到達、期望 seq #，先前資料皆已確認 | **延遲 ACK (delayed ACK)**：等待最多 **500 ms** 看是否有下一個節段；若無則送 ACK |
| 依序到達、期望 seq #，且已有一個 ACK 等待中 | 立刻送**單一累積式 ACK**，確認兩個依序節段 |
| 亂序到達，序號高於預期（偵測到間隙 gap） | 立刻送**重複 ACK (duplicate ACK)**，指出期望的下一個位元組序號 |
| 到達的節段部分/完全填補間隙 | 立刻送 ACK（若節段從間隙低端開始） |

> **TCP 延遲 ACK 程序 (HW3-14)**：依序收到期望節段時，**先不立即回 ACK**，等待最多 500ms；若期間有下一個依序節段到達，則用**一個累積 ACK** 一次確認兩者；若 500ms 內無新節段，才送出 ACK。

### TCP 快速重送 (Fast Retransmit) (HW3-15)

- 若傳送端收到對**同一資料的 3 個額外 ACK**（**triple duplicate ACK**），就立即重送該未確認、最小序號的節段
- ⭐ 不必等 timeout —— 收到 3 個重複 ACK 表示遺失節段之後又收到 3 個節段，遺失機率很高

> **TCP vs. GBN 的可靠傳輸差異 (HW3-17)**
> TCP 雖用累積式 ACK（似 GBN），但：
> - TCP 接收端通常會**緩衝亂序節段**（GBN 丟棄）
> - TCP **逾時只重送單一（最小序號）節段**，非像 GBN 重送全部
> - TCP 有 **fast retransmit**（3 個重複 ACK 即重送），不必等逾時
> → TCP 是 **GBN 與 SR 的混合體**

### TCP 流量控制 (Flow Control) (HW3-16)

> **目的**：接收端控制傳送端，使傳送端**不會因傳太多太快而塞爆接收端的緩衝區 (receive buffer)**。

- **程序**：
  1. TCP 接收端在標頭的 **rwnd** 欄位「廣告 (advertise)」其**空閒緩衝空間**
     - `rwnd = RcvBuffer − (已緩衝但未被應用讀取的資料)`
  2. 傳送端限制其「飛行中 (未確認)」資料量 **≤ 收到的 rwnd**
  3. → **保證接收端緩衝區不會溢位**
- 注意：流量控制（一個傳送端對一個接收端）**不同於**壅塞控制（太多傳送端）

### TCP 連線管理 — 三向交握 (3-way Handshake)

```
Client                              Server
  │  SYNbit=1, Seq=x                 │   選 init seq x，送 SYN
  │ ───────────────────────────────▶│
  │  SYNbit=1, Seq=y, ACKbit=1,      │   選 init seq y，送 SYNACK（確認 SYN）
  │  ACKnum=x+1                      │
  │ ◀───────────────────────────────│
  │  ACKbit=1, ACKnum=y+1            │   送 ACK（可夾帶資料）
  │ ───────────────────────────────▶│
 ESTAB                             ESTAB
```

> **為何不用二向交握？(HW3-19)**
> 二向交握 (2-way handshake) 在有**可變延遲、訊息遺失重送、訊息亂序、看不到對方狀態**的網路中會失敗：
> - **半開連線 (half-open connection)**：舊的 `req_conn(x)` 重送抵達伺服器，伺服器建立連線並回 `acc_conn(x)`，但用戶端早已終止 → 伺服器留下無用的半開連線
> - **重複資料被接受 (dup data accepted)**：舊的 `data(x+1)` 重送被伺服器當成新連線資料接受
> → 三向交握讓雙方都能確認對方「活著且願意建立連線」，並交換初始序號，避免上述問題。

### 關閉 TCP 連線 — 四向交握 (4-way Handshake) (HW3-20)

```
Client                                 Server
 ESTAB                                  ESTAB
  │  FINbit=1, seq=x   (clientSocket.close())
  │ ───────────────────────────────────▶│
 FIN_WAIT_1                           CLOSE_WAIT  (仍可送資料)
  │  ACKbit=1, ACKnum=x+1               │
  │ ◀───────────────────────────────────│
 FIN_WAIT_2 (等待 server close)         │
  │  FINbit=1, seq=y                    │
  │ ◀───────────────────────────────────│ LAST_ACK
 TIMED_WAIT                             │
  │  ACKbit=1, ACKnum=y+1               │
  │ ───────────────────────────────────▶│ CLOSED
 (等待 2×最大節段壽命 2*MSL)
 CLOSED
```

- 雙方各自關閉自己的方向：送 **FIN=1**；收到 FIN 回 **ACK**（ACK 可與自己的 FIN 合併）
- 用戶端進入 **TIMED_WAIT**，等待 **2 × Max Segment Lifetime** 後才真正關閉（確保最後 ACK 送達）

---

## 3.6 壅塞控制原理 (Principles of Congestion Control)

### 什麼是壅塞 (Congestion)？

- 非正式定義：「**太多來源、傳送太多資料、太快**，超過網路所能負荷」
- **表現**：
  - **長延遲**（router 緩衝區排隊）
  - **封包遺失**（router 緩衝區溢位）
- ⚠️ **與流量控制不同**！壅塞控制 = 太多傳送端共用網路；流量控制 = 單一傳送端對單一接收端

### 壅塞的成因/代價 (3 個情境)

- **情境 1**（一台 router、無限緩衝、無重送）：當 $\lambda_{in} \to R/2$，延遲趨於無限大；最大每連線吞吐量 = R/2
- **情境 2**（有限緩衝、需重送）：
  - 傳送端需重送遺失封包，「代價」是對同樣的接收吞吐量要做**更多傳送工作**
  - **過早逾時**送出不必要的重複封包 → 鏈路載送多份同封包 → 降低最大可達吞吐量
- **情境 3**（多傳送端、多躍程路徑）：封包在下游被丟棄時，**上游已用掉的傳輸容量與緩衝全部浪費**

### 兩種壅塞控制方法

| 方法 | 說明 | 採用者 |
|------|------|--------|
| **端到端 (end-to-end)** | 網路**不給**明確回饋；由觀察到的遺失、延遲**推斷**壅塞 | **TCP** |
| **網路輔助 (network-assisted)** | router 直接回饋給主機（指出壅塞程度或明確設定傳送率） | TCP ECN、ATM、DECbit |

---

## 3.7 TCP 壅塞控制 (TCP Congestion Control)

### AIMD（加法增加、乘法遞減）(HW3-22)

> **核心想法**：傳送端持續**增加傳送率**直到發生**封包遺失（壅塞）**，遺失時就**降低**傳送率。

| 階段 | 行為 |
|------|------|
| **加法增加 (Additive Increase, AI)** | 每個 RTT 將傳送率（cwnd）增加 **1 個 MSS**，直到偵測到遺失 |
| **乘法遞減 (Multiplicative Decrease, MD)** | 每次遺失事件將傳送率**減半** |

- 形成 **AIMD 鋸齒狀 (sawtooth)** 行為：不斷「探測 (probe)」可用頻寬
- **乘法遞減細節**：
  - **由 3 個重複 ACK 偵測遺失**（TCP Reno）→ cwnd **減半**
  - **由 timeout 偵測遺失**（TCP Tahoe）→ cwnd **降到 1 MSS**
- **為何用 AIMD？** 是一種分散式、非同步演算法，可在全網最佳化壅塞流速率，並有良好穩定性

### 壅塞視窗 (cwnd)

- TCP 傳送端限制：`LastByteSent − LastByteAcked ≤ cwnd`
- 大致行為：送出 cwnd 位元組 → 等待 RTT 收到 ACK → 再送更多
  $$TCP\ rate \approx \frac{cwnd}{RTT}\ \text{bytes/sec}$$
- cwnd 隨觀察到的網路壅塞動態調整

### 慢啟動 (Slow Start) (HW3-21)

> 連線開始時，傳送率**指數成長**直到第一次遺失事件。

- 初始 `cwnd = 1 MSS`
- **每個 RTT 把 cwnd 加倍**（作法：每收到一個 ACK 就 `cwnd += 1 MSS`）
- 總結：初始速率慢，但**指數般迅速攀升**

> **HW3-21 慢啟動如何調整 CWND**：cwnd 從 1 MSS 開始，每收到一個 ACK 就增加 1 MSS，使得**每個 RTT cwnd 加倍（指數成長）**，直到發生遺失事件或 cwnd 達到 ssthresh。

### 慢啟動 → 壅塞避免 (Congestion Avoidance)

- **Q：指數成長何時轉為線性？** A：當 **cwnd 達到 ssthresh** 時
- 變數 **ssthresh (slow start threshold)**：遺失事件發生時，ssthresh 設為**遺失前 cwnd 的一半**

### TCP 壅塞控制狀態總結

| 條件 | 狀態 | 視窗成長 |
|------|------|----------|
| `cwnd < ssthresh` | **慢啟動 (slow start)** | 指數成長 |
| `cwnd ≥ ssthresh` | **壅塞避免 (congestion avoidance)** | 線性成長（每 RTT +1 MSS） |
| 發生 **3 個重複 ACK** | → fast recovery | ssthresh = cwnd/2，cwnd ≈ ssthresh |
| 發生 **timeout** | → slow start | ssthresh = cwnd/2，**cwnd = 1 MSS** |

### TCP CUBIC

- 比 AIMD 更佳的頻寬探測法（Linux 預設、最熱門的 Web 伺服器 TCP）
- **直覺**：$W_{max}$ = 偵測到遺失時的傳送率；瓶頸鏈路壅塞狀態大概沒變太多
  - 遺失後減半，**初期快速逼近 $W_{max}$，接近 $W_{max}$ 時放慢（謹慎）**
- $K$ = 視窗預計達到 $W_{max}$ 的時間點；以「目前時間與 K 距離的**立方 (cube)**」為函數增加 W

### 延遲式壅塞控制 (Delay-based)

- 目標：「**讓端到端管線剛好填滿、但不要更滿**」(keep the pipe just full, but not fuller)
- $RTT_{min}$ = 觀察到的最小 RTT（無壅塞路徑）；無壅塞吞吐量 = $cwnd / RTT_{min}$
  - 測得吞吐量「接近」無壅塞吞吐量 → cwnd 線性**增加**（路徑未壅塞）
  - 「遠低於」→ cwnd 線性**減少**（路徑壅塞）
- 優點：**不需製造遺失**即可控制壅塞；**BBR**（Google 骨幹網路）採此法

### 明確壅塞通知 (ECN, Explicit Congestion Notification)

- 屬於**網路輔助**壅塞控制
- **IP 標頭 (ToS 欄位) 中 2 個位元**由 router 標記以指出壅塞
- 壅塞指示送達目的端 → 目的端在 ACK 上設 **ECE 位元**通知傳送端
- 同時涉及 **IP**（標記 ECN 位元）與 **TCP**（標頭 C、E 位元）

### TCP 公平性 (Fairness)

- **公平目標**：K 個 TCP 連線共用瓶頸鏈路頻寬 R，各應得 **R/K** 平均速率
- **TCP 公平嗎？** 在理想假設下（**相同 RTT、固定數量連線、皆在壅塞避免階段**）→ **是公平的**
- 不公平來源：
  - **UDP**：多媒體 app 常用 UDP 不受壅塞控制節制
  - **平行 TCP 連線**：app 可開多條平行連線（例：對速率 R、已有 9 條連線的鏈路，新 app 開 1 條得 R/10，開 11 條得 R/2）

---

## 3.8 傳輸層功能演進 (Evolution of Transport-Layer Functionality)

- TCP、UDP 已是 40 年來主要傳輸協定；針對特定情境發展出不同 TCP「口味」（長肥管線、無線網路、長延遲鏈路、資料中心、背景流量等）
- **趨勢**：把傳輸層功能搬到**應用層**、架在 **UDP** 之上 → **HTTP/3: QUIC**

### QUIC (Quick UDP Internet Connections)

- **應用層協定**，架在 **UDP** 上，用以提升 HTTP 效能（部署於眾多 Google 伺服器、Chrome、YouTube app）
- 採用本章學過的方法做**連線建立、錯誤控制、壅塞控制**（演算法類似 TCP）
- **連線建立**：可靠度、壅塞控制、認證、加密、狀態建立**僅需 1 個 RTT**
  - 對比：TCP 交握（傳輸層）+ TLS 交握（安全）= **2 個序列交握**
- **多個應用層 streams 多工**在單一 QUIC 連線上：
  - 各 stream 獨立做可靠傳輸與安全，**共用**壅塞控制
  - **解決 HOL blocking（隊頭阻塞）**：某 stream 封包遺失不會阻塞其他 stream

| 協定堆疊 | HTTP/2 over TCP | HTTP/3 (HTTP/2 over QUIC) |
|----------|-----------------|---------------------------|
| 應用層 | HTTP/2 + TLS | HTTP/3 + **QUIC**（含 TLS） |
| 傳輸層 | **TCP** | **UDP** |
| 網路層 | IP | IP |

---

## 本章重點公式速查

| 主題 | 公式 |
|------|------|
| Stop-and-Wait 使用率 | $U = \dfrac{L/R}{RTT + L/R}$ |
| EstimatedRTT | $(1-\alpha)EstimatedRTT + \alpha\,SampleRTT$，$\alpha=0.125$ |
| DevRTT | $(1-\beta)DevRTT + \beta\,|SampleRTT-EstimatedRTT|$，$\beta=0.25$ |
| TimeoutInterval | $EstimatedRTT + 4\,DevRTT$ |
| TCP 速率 | $\approx cwnd / RTT$ |
| SR 序號需求 | 序號數 $\ge 2 \times$ 視窗大小 |
| 平均 TCP 吞吐量 | $\dfrac{3}{4}\cdot\dfrac{W}{RTT}$ |
