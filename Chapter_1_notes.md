# 第一章：電腦網路概論 (Introduction)

> 教科書：Computer Networking: A Top-Down Approach, 8th Edition - Jim Kurose, Keith Ross

---

## 1.1 什麼是網際網路 (What is the Internet)?

### 「螺絲與螺帽」觀點 (Nuts and Bolts View)

- 數十億個連網的計算裝置 (computing devices)
  - **主機 (hosts)** = **終端系統 (end systems)**
  - 在網際網路的「邊緣 (edge)」執行網路應用程式 (network apps)
- **封包交換器 (packet switches)**：轉送封包 (packets)（資料區塊）
  - **路由器 (routers)**、**交換器 (switches)**
- **通訊鏈路 (communication links)**
  - 光纖 (fiber)、銅線 (copper)、無線電 (radio)、衛星 (satellite)
  - **傳輸速率 (transmission rate)**：又稱**頻寬 (bandwidth)**
- **網路 (networks)**：裝置、路由器、鏈路的集合，由組織管理
- **網際網路 (Internet)**：「網路中的網路 (network of networks)」
  - 互相連接的網際網路服務提供者 (ISP, Internet Service Providers)
- **協定 (protocols)** 無所不在
  - 控制訊息的傳送與接收
  - 例如：HTTP、TCP、IP、WiFi、4G、乙太網路 (Ethernet)
- **網際網路標準 (Internet standards)**
  - **RFC (Request for Comments)**：網際網路標準文件
  - **IETF (Internet Engineering Task Force)**：網際網路工程任務小組

### 「服務」觀點 (Services View)

- **基礎設施 (infrastructure)**：為應用程式提供服務
  - 網頁 (Web)、串流影片 (streaming video)、多媒體電話會議、電子郵件、遊戲、電子商務、社群媒體等
- 提供**程式設計介面 (programming interface)** 給分散式應用程式 (distributed applications)
  - 「掛勾 (hooks)」讓應用程式可以「連接」並使用網際網路傳輸服務 (transport service)

---

## 1.2 什麼是協定 (What is a Protocol)?

> **協定 (protocol)** 定義了網路實體 (network entities) 之間傳送和接收訊息的**格式 (format)**、**順序 (order)**，以及在訊息傳送/接收時所採取的**動作 (actions)**。

- 人類協定 (human protocols)：打招呼、問時間等
- 網路協定 (network protocols)：電腦（裝置）之間的通訊規則
- 網際網路中所有的通訊活動都由協定管理

---

## 1.3 網路邊緣 (Network Edge)

### 主機 (Hosts)

- 分為**用戶端 (clients)** 和**伺服器 (servers)**
- 伺服器通常位於**資料中心 (data centers)**

### 主機傳送資料的方式

- 將應用程式訊息 (application message) 拆成較小的區塊，稱為**封包 (packets)**，每個封包長度為 *L* 位元 (bits)
- 以**傳輸速率 (transmission rate) *R*** 將封包傳入存取網路
- **封包傳輸延遲 (packet transmission delay)** = L/R

### 存取網路 (Access Networks)

如何將終端系統連接到邊緣路由器 (edge router)？

#### 1. 有線電視網路存取 (Cable-based Access)

- **混合光纖同軸電纜 (HFC, Hybrid Fiber Coax)**
  - 非對稱 (asymmetric)：下行 (downstream) 40 Mbps ~ 1.2 Gbps，上行 (upstream) 30-100 Mbps
  - 使用**分頻多工 (FDM, Frequency Division Multiplexing)**：不同頻道在不同頻帶傳輸
  - 家庭**共享存取網路 (shared access network)** 至電纜頭端 (cable headend)
  - **CMTS (Cable Modem Termination System)**：纜線數據機終端系統

#### 2. 數位用戶迴路 (DSL, Digital Subscriber Line)

- 使用**既有的電話線** 至電信局 (central office) 的 **DSLAM (DSL Access Multiplexer)**
  - 資料透過 DSL 電話線傳至網際網路
  - 語音透過 DSL 電話線傳至電話網路
- **專用線路 (dedicated line)**（非共享）
- 下行 24-52 Mbps，上行 3.5-16 Mbps

#### 3. 家用網路 (Home Networks)

- **WiFi 無線存取點 (wireless access point)**：54, 450 Mbps
- **路由器 (router)**、**防火牆 (firewall)**、**NAT (Network Address Translation)**
- 有線乙太網路 (wired Ethernet)：1 Gbps
- 纜線或 DSL 數據機 (modem)

#### 4. 無線存取網路 (Wireless Access Networks)

- 透過**基地台 (base station)**（又稱「存取點 (access point)」）連接
- **無線區域網路 (WLAN, Wireless Local Area Network)**
  - 通常在建築物內（約 100 英尺）
  - 802.11b/g/n (WiFi)：11, 54, 450 Mbps
- **廣域蜂巢式存取網路 (Wide-area Cellular Access Networks)**
  - 由行動/蜂巢式網路業者提供（數十公里）
  - 數十 Mbps
  - 4G 蜂巢式網路，5G 即將到來

#### 5. 企業網路 (Enterprise Networks)

- 公司、大學等
- 混合有線、無線鏈路技術
- 乙太網路 (Ethernet)：有線存取 100Mbps、1Gbps、10Gbps
- WiFi：無線存取 11, 54, 450 Mbps

#### 6. 資料中心網路 (Data Center Networks)

- 高頻寬鏈路 (high-bandwidth links)：10s-100s Gbps
- 連接數百至數千台伺服器

### 實體媒介 (Physical Media)

- **位元 (bit)**：在傳送器/接收器對之間傳播
- **實體鏈路 (physical link)**：傳送器與接收器之間的媒介
- **導引式媒介 (guided media)**：訊號在固態媒介中傳播（銅線、光纖、同軸電纜）
- **非導引式媒介 (unguided media)**：訊號自由傳播（如無線電）

#### 導引式媒介類型

| 媒介 | 說明 |
|------|------|
| **雙絞線 (Twisted Pair, TP)** | 兩條絕緣銅線。Cat 5：100 Mbps, 1 Gbps。Cat 6：10 Gbps |
| **同軸電纜 (Coaxial Cable)** | 兩個同心銅導體，雙向 (bidirectional)，寬頻 (broadband)，每頻道 100's Mbps |
| **光纖 (Fiber Optic Cable)** | 玻璃光纖攜帶光脈衝，高速 (10's-100's Gbps)，低錯誤率，不受電磁干擾 |

#### 無線電 (Wireless Radio)

- 訊號在電磁頻譜 (electromagnetic spectrum) 的各「頻帶 (bands)」中傳播
- 無實體「線路」，廣播式 (broadcast)、半雙工 (half-duplex)
- 受反射 (reflection)、物體遮蔽 (obstruction)、干擾/雜訊 (interference/noise) 影響

| 類型 | 說明 |
|------|------|
| **無線區域網路 (Wireless LAN, WiFi)** | 10-100's Mbps，數十公尺 |
| **廣域網路 (Wide-area, 4G cellular)** | 數十 Mbps，約 10 公里 |
| **藍牙 (Bluetooth)** | 短距離，有限速率 |
| **地面微波 (Terrestrial Microwave)** | 點對點 (point-to-point)，45 Mbps |
| **衛星 (Satellite)** | 每頻道最高 45 Mbps，端對端延遲 270 毫秒 |

---

## 1.4 網路核心 (Network Core)

- 互連路由器的網狀結構 (mesh of interconnected routers)

### 兩個關鍵功能

1. **轉送 (Forwarding)**（又稱「交換 (switching)」）
   - **區域 (local)** 動作：將到達封包從路由器的輸入鏈路移至適當的輸出鏈路
   - 使用**本地轉送表 (local forwarding table)**
2. **路由 (Routing)**
   - **全域 (global)** 動作：決定封包從來源到目的地的路徑
   - 使用**路由演算法 (routing algorithms)**

### 封包交換 (Packet Switching)

#### 儲存轉送 (Store-and-Forward)

- **整個封包**必須到達路由器後，才能傳送到下一條鏈路
- **封包傳輸延遲 (packet transmission delay)**：傳輸 L 位元封包進入速率 R bps 的鏈路需要 **L/R 秒**
- 範例：L = 10 Kbits, R = 100 Mbps => 單跳傳輸延遲 = 0.1 msec

#### 排隊 (Queueing)

- 當工作到達速度超過能處理的速度時就會發生排隊
- **封包排隊與遺失 (packet queuing and loss)**：
  - 若到達鏈路的位元速率超過鏈路的傳輸速率，封包會在佇列 (queue) 中等待
  - 若路由器的記憶體（緩衝區 buffer）滿了，封包會被**丟棄 (dropped)**（遺失 loss）

### 電路交換 (Circuit Switching)

- 端對端資源被配置 (allocated) 並**保留 (reserved)** 給來源和目的地之間的「通話 (call)」
- **專用資源 (dedicated resources)**：不共享 => 有保證的效能 (guaranteed performance)
- 若電路區段閒置則浪費（不共享）
- 常用於傳統電話網路

#### 電路交換的多工方式

1. **分頻多工 (FDM, Frequency Division Multiplexing)**
   - 將頻率分成窄頻帶 (narrow frequency bands)
   - 每個通話分配自己的頻帶，以該頻帶的最大速率傳輸
2. **分時多工 (TDM, Time Division Multiplexing)**
   - 時間分成時槽 (time slots)
   - 每個通話分配週期性的時槽，在其時槽期間以全頻寬傳輸

### 封包交換 vs. 電路交換

- 範例：1 Gb/s 鏈路，每個使用者活躍時 100 Mb/s，活躍時間占 10%
  - 電路交換：**10 個使用者**
  - 封包交換：35 個使用者時，同時超過 10 個活躍的機率 < 0.0004
- 封包交換適合**突發性 (bursty) 資料**
  - 資源共享 (resource sharing)
  - 較簡單，不需通話設定 (no call setup)
- 封包交換可能發生**過度壅塞 (excessive congestion)**
  - 封包延遲與遺失
  - 需要可靠資料傳輸和壅塞控制的協定

---

## 1.5 網際網路結構：「網路中的網路」(Internet Structure: Network of Networks)

- 主機透過**存取 ISP (access ISP)** 連接到網際網路
- 存取 ISP 之間必須互連，使任意兩台主機都能通訊
- 階層結構：
  - **第一層 ISP (Tier-1 ISP)**：如 Level 3、Sprint、AT&T、NTT，全國與國際覆蓋
  - **區域 ISP (Regional ISP)**：連接存取網路至 ISP
  - **網際網路交換點 (IXP, Internet Exchange Point)**：讓多個 ISP 互連
  - **對等鏈路 (Peering Link)**：同層 ISP 之間直接互連
  - **內容提供者網路 (Content Provider Network)**：如 Google、Facebook，自行建立私有網路連接資料中心

---

## 1.6 效能 (Performance)：延遲、遺失與吞吐量

### 封包延遲的四個來源 (Four Sources of Packet Delay)

**d_nodal = d_proc + d_queue + d_trans + d_prop**

| 延遲類型 | 說明 | 公式/量級 |
|----------|------|----------|
| **處理延遲 (Processing Delay, d_proc)** | 檢查位元錯誤、決定輸出鏈路 | 通常 < 微秒 (microsecs) |
| **排隊延遲 (Queueing Delay, d_queue)** | 在輸出鏈路等待傳輸的時間 | 取決於路由器壅塞程度 |
| **傳輸延遲 (Transmission Delay, d_trans)** | 將封包推入鏈路所需時間 | **d_trans = L/R** |
| **傳播延遲 (Propagation Delay, d_prop)** | 位元在實體鏈路中傳播的時間 | **d_prop = d/s**（d：鏈路長度，s：傳播速度 ~2x10^8 m/sec） |

> **注意**：傳輸延遲 (d_trans) 和傳播延遲 (d_prop) 是**非常不同的**！

### 流量強度 (Traffic Intensity)

- *a*：平均封包到達率 (average packet arrival rate)
- *L*：封包長度 (packet length, bits)
- *R*：鏈路頻寬 (link bandwidth, bps)

**流量強度 = La/R**

- La/R ~ 0：平均排隊延遲小
- La/R -> 1：平均排隊延遲大
- La/R > 1：到達的工作量超過處理能力，平均延遲趨近無限大！

### Traceroute 程式

- 提供從來源到路由器沿端對端路徑的延遲測量
- 對每個路由器 *i* 發送三個封包（TTL = i），測量往返時間

### 封包遺失 (Packet Loss)

- 佇列 (queue)（又稱緩衝區 buffer）容量有限
- 當佇列滿時，到達的封包被丟棄 (dropped)
- 遺失的封包可能由前一個節點、來源終端系統重傳，或完全不重傳

### 吞吐量 (Throughput)

- **吞吐量 (throughput)**：位元從傳送端到接收端的速率 (bits/time unit)
  - **瞬時吞吐量 (instantaneous)**：某一時間點的速率
  - **平均吞吐量 (average)**：較長時間的平均速率
- **瓶頸鏈路 (bottleneck link)**：端對端路徑上限制吞吐量的鏈路
  - 端對端吞吐量 = min(R_s, R_c)
  - 多條連線共享骨幹鏈路時，每條連線的端對端吞吐量 = min(R_c, R_s, R/N)
  - 實際上 R_c 或 R_s 通常是瓶頸

---

## 1.7 網路安全 (Network Security)

- 網際網路最初設計時**沒有考慮太多安全性**
- 需要考慮的三個面向：
  1. 攻擊者如何攻擊電腦網路
  2. 如何防禦網路攻擊
  3. 如何設計對攻擊免疫的架構

### 常見攻擊類型

#### 1. 封包攔截 / 封包嗅探 (Packet Sniffing)

- 在廣播媒介（共享乙太網路、無線網路）上
- 混雜模式 (promiscuous mode) 的網路介面讀取/記錄所有經過的封包（包括密碼！）
- 工具：Wireshark

#### 2. 偽造身分 / IP 位址偽造 (IP Spoofing)

- 注入帶有偽造來源位址 (false source address) 的封包

#### 3. 阻斷服務攻擊 (DoS, Denial of Service)

- 攻擊者用大量偽造流量 (bogus traffic) 淹沒目標資源
- 步驟：1. 選擇目標 2. 入侵網路上的主機（殭屍網路 botnet）3. 從被攻陷的主機發送封包

### 防禦方式 (Lines of Defense)

| 防禦機制 | 說明 |
|----------|------|
| **認證 (Authentication)** | 證明身分。行動網路用 SIM 卡提供硬體身分 |
| **機密性 (Confidentiality)** | 透過加密 (encryption) |
| **完整性檢查 (Integrity Checks)** | 數位簽章 (digital signatures) 防止/偵測竄改 (tampering) |
| **存取限制 (Access Restrictions)** | 密碼保護的 VPN |
| **防火牆 (Firewalls)** | 存取和核心網路中的「中介盒 (middleboxes)」，過濾封包以限制傳送者、接收者、應用程式 |

---

## 1.8 協定層次與服務模型 (Protocol Layers and Service Models)

### 為什麼要分層 (Why Layering)?

- 明確的結構 (explicit structure) 便於識別系統各部分的關係
- **模組化 (modularization)** 便於維護和更新
  - 改變某層的服務實作 (implementation) 對系統其他部分透明 (transparent)

### 網際網路協定堆疊 (Internet Protocol Stack) - 五層

| 層次 | 名稱 | 功能 | 協定範例 | 資料單元 |
|------|------|------|---------|---------|
| 5 | **應用層 (Application)** | 支援網路應用程式 | HTTP, IMAP, SMTP, DNS | 訊息 (Message) |
| 4 | **傳輸層 (Transport)** | 行程對行程的資料傳輸 | TCP, UDP | 區段 (Segment) |
| 3 | **網路層 (Network)** | 從來源到目的地的資料報路由 | IP, 路由協定 | 資料報 (Datagram) |
| 2 | **鏈路層 (Link)** | 相鄰網路元素間的資料傳輸 | Ethernet, 802.11 (WiFi), PPP | 訊框 (Frame) |
| 1 | **實體層 (Physical)** | 線路上的位元傳輸 | - | 位元 (Bits) |

### ISO/OSI 參考模型 (七層)

比網際網路協定堆疊多了兩層：
- **展示層 (Presentation Layer)**：讓應用程式能解譯資料意義（加密、壓縮、機器特定慣例）
- **會議層 (Session Layer)**：同步 (synchronization)、檢查點 (checkpointing)、資料交換的復原 (recovery)
- 網際網路堆疊「缺少」這兩層，若需要則由應用程式自行實作

### 封裝 (Encapsulation)

每一層都會在上層傳下來的資料加上自己的**標頭 (header)**：

```
應用層：M (訊息)
傳輸層：H_t | M (區段)
網路層：H_n | H_t | M (資料報)
鏈路層：H_l | H_n | H_t | M (訊框)
```

- **交換器 (Switch)**：只有鏈路層和實體層
- **路由器 (Router)**：有網路層、鏈路層和實體層

---

## 1.9 網際網路歷史 (Internet History)

### 1961-1972：早期封包交換原理

- **1961**：Kleinrock - 排隊理論 (queueing theory) 證明封包交換有效性
- **1964**：Baran - 軍事網路的封包交換
- **1967**：ARPAnet 由 ARPA (Advanced Research Projects Agency) 構思
- **1969**：第一個 ARPAnet 節點運作
- **1972**：ARPAnet 公開展示、NCP (Network Control Protocol)、第一個電子郵件程式

### 1972-1980：網路互連、新的專有網路

- **1970**：ALOHAnet 夏威夷衛星網路
- **1974**：Cerf & Kahn - 網路互連架構
- **1976**：Xerox PARC 的乙太網路 (Ethernet)
- **1979**：ARPAnet 有 200 個節點
- Cerf & Kahn 的網路互連原則：
  - 最小主義 (minimalism)、自主性 (autonomy)
  - 最佳努力服務模型 (best-effort service model)
  - 無狀態路由 (stateless routing)
  - 分散式控制 (decentralized control)

### 1980-1990：新協定、網路增生

- **1982**：SMTP 電子郵件協定
- **1983**：TCP/IP 部署、DNS 定義
- **1985**：FTP 協定
- **1988**：TCP 壅塞控制 (congestion control)
- 100,000 台主機連接

### 1990-2000s：商業化、全球資訊網

- **1990s 初**：ARPAnet 除役、Web 誕生（HTML, HTTP: Berners-Lee）
- **1994**：Mosaic 瀏覽器，後來的 Netscape
- **1990s 末**：Web 商業化、即時通訊 (instant messaging)、P2P 檔案分享
- 約 5000 萬台主機、1 億以上使用者

### 2005-至今：規模、SDN、行動、雲端

- 寬頻家用存取 (broadband home access)：10-100's Mbps
- **2008**：軟體定義網路 (SDN, Software-Defined Networking)
- 高速無線存取：4G/5G、WiFi
- 內容提供者 (Google, FB, Microsoft) 建立自有網路
- 企業在「雲端 (cloud)」運行服務（AWS、Azure）
- 智慧型手機崛起：行動裝置多於固定裝置 (2017)
- 約 180 億裝置連接網際網路 (2017)

---

## 重點公式整理

| 公式 | 說明 |
|------|------|
| **d_trans = L / R** | 傳輸延遲 = 封包長度 / 鏈路傳輸速率 |
| **d_prop = d / s** | 傳播延遲 = 鏈路長度 / 傳播速度 |
| **d_nodal = d_proc + d_queue + d_trans + d_prop** | 節點總延遲 |
| **流量強度 (Traffic Intensity) = La / R** | L: 封包長度, a: 平均到達率, R: 鏈路頻寬 |
| **吞吐量 (Throughput) = min(R_s, R_c)** | 端對端吞吐量取決於瓶頸鏈路 |

---

## 關鍵名詞對照表

| 繁體中文 | English |
|----------|---------|
| 網際網路 | Internet |
| 協定 | Protocol |
| 主機/終端系統 | Host / End System |
| 封包 | Packet |
| 路由器 | Router |
| 交換器 | Switch |
| 通訊鏈路 | Communication Link |
| 頻寬 | Bandwidth |
| 傳輸速率 | Transmission Rate |
| 網際網路服務提供者 | ISP (Internet Service Provider) |
| 存取網路 | Access Network |
| 網路邊緣 | Network Edge |
| 網路核心 | Network Core |
| 封包交換 | Packet Switching |
| 電路交換 | Circuit Switching |
| 儲存轉送 | Store-and-Forward |
| 轉送 | Forwarding |
| 路由 | Routing |
| 轉送表 | Forwarding Table |
| 路由演算法 | Routing Algorithm |
| 排隊 | Queueing |
| 緩衝區 | Buffer |
| 分頻多工 | FDM (Frequency Division Multiplexing) |
| 分時多工 | TDM (Time Division Multiplexing) |
| 混合光纖同軸電纜 | HFC (Hybrid Fiber Coax) |
| 數位用戶迴路 | DSL (Digital Subscriber Line) |
| 雙絞線 | Twisted Pair |
| 同軸電纜 | Coaxial Cable |
| 光纖 | Fiber Optic Cable |
| 導引式媒介 | Guided Media |
| 非導引式媒介 | Unguided Media |
| 處理延遲 | Processing Delay |
| 排隊延遲 | Queueing Delay |
| 傳輸延遲 | Transmission Delay |
| 傳播延遲 | Propagation Delay |
| 流量強度 | Traffic Intensity |
| 吞吐量 | Throughput |
| 瓶頸鏈路 | Bottleneck Link |
| 封包遺失 | Packet Loss |
| 封包嗅探 | Packet Sniffing |
| IP 位址偽造 | IP Spoofing |
| 阻斷服務攻擊 | DoS (Denial of Service) |
| 殭屍網路 | Botnet |
| 認證 | Authentication |
| 機密性 | Confidentiality |
| 完整性 | Integrity |
| 防火牆 | Firewall |
| 加密 | Encryption |
| 數位簽章 | Digital Signature |
| 協定堆疊 | Protocol Stack |
| 應用層 | Application Layer |
| 傳輸層 | Transport Layer |
| 網路層 | Network Layer |
| 鏈路層 | Link Layer |
| 實體層 | Physical Layer |
| 展示層 | Presentation Layer |
| 會議層 | Session Layer |
| 封裝 | Encapsulation |
| 訊息 | Message |
| 區段 | Segment |
| 資料報 | Datagram |
| 訊框 | Frame |
| 網際網路交換點 | IXP (Internet Exchange Point) |
| 對等鏈路 | Peering Link |
| 內容提供者網路 | Content Provider Network |
| 軟體定義網路 | SDN (Software-Defined Networking) |
| 網路位址轉換 | NAT (Network Address Translation) |
