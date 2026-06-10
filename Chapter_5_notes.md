# 第五章：網路層 — 控制平面 (Network Layer: Control Plane)

> 教科書：Computer Networking: A Top-Down Approach, 8th Edition
> 期末考範圍：**Chapter 5.1 ~ 5.4**（introduction、routing protocols、OSPF、BGP）

---

## 5.1 簡介 (Introduction)

### 網路層功能回顧

- **轉送 (forwarding)**：把封包從 router 輸入移到適當的輸出（**資料平面**）
- **路由 (routing)**：決定封包從來源到目的的路徑（**控制平面**）

### 控制平面的兩種架構

| 方式 | 說明 |
|------|------|
| **per-router 控制（傳統）** | 每台 router 中都有路由演算法元件，彼此在控制平面互動 |
| **邏輯集中式控制 (SDN)** | 遠端控制器計算並安裝各 router 的 forwarding table |

### 路由協定 (Routing Protocols)

- **目標**：決定從傳送主機到接收主機，穿越 router 網路的「**好 (good)**」路徑
- **路徑 (path)**：封包從來源主機到目的主機所經過的 router 序列
- 「好」= 最低「成本」、最快、最不壅塞
- 路由是「top-10」網路挑戰之一

### 圖抽象 (Graph Abstraction)

- 圖 **G = (N, E)**：N = router 集合，E = 鏈路集合
- **$c_{a,b}$** = 連接 a 與 b 的直接鏈路成本；非鄰居則 = ∞
- 成本由網路操作者定義：可全為 1，或與頻寬成反比，或與壅塞成反比

### 路由演算法分類

| 分類依據 | 類型 | 說明 |
|----------|------|------|
| **全域 vs. 去中心** | **全域 (global)** | 所有 router 擁有完整拓樸與鏈路成本資訊 → **link-state** 演算法 |
| | **去中心 (decentralized)** | router 初始只知到鄰居的鏈路成本，透過與鄰居反覆交換資訊計算 → **distance-vector** 演算法 |
| **路由變化速度** | **靜態 (static)** | 路由隨時間緩慢變化 |
| | **動態 (dynamic)** | 路由變化快，週期更新或回應鏈路成本變化 |

---

## 5.2 路由演算法 (Routing Protocols)

### Link-State 演算法：Dijkstra (HW4-6)

> **集中式 (centralized)**：網路拓樸與鏈路成本對所有節點皆已知（透過「**link state broadcast**」）。每個節點都有相同資訊。

- 計算從**一個來源**到所有其他節點的**最低成本路徑**，得出該節點的 forwarding table
- **iterative（迭代）**：第 k 次迭代後，得知到 k 個目的地的最低成本路徑

**符號 (notation)：**
- $c_{x,y}$：x 到 y 的直接鏈路成本（非鄰居則 ∞）
- $D(v)$：目前從來源到 v 的最低成本路徑估計
- $p(v)$：v 在路徑上的前一個節點 (predecessor)
- $N'$：最低成本路徑已**確定**的節點集合

**演算法：**
```
1  Initialization:
2    N' = {u}
3    for all nodes v
4      if v adjacent to u
5        then D(v) = c(u,v)
6      else D(v) = ∞
7
8  Loop
9    find w not in N' such that D(w) is a minimum
10   add w to N'
11   update D(v) for all v adjacent to w and not in N':
12     D(v) = min( D(v), D(w) + c(w,v) )
15  until all nodes in N'
```

> **HW4-6 Link-state 演算法操作**：
> 1. 每個 router 透過 link-state broadcast 把自己的鏈路狀態（到各鄰居的成本）**洪泛 (flood)** 給全網所有 router，使每個 router 都擁有**完整拓樸**。
> 2. 每個 router 各自執行 **Dijkstra 演算法**：從來源出發，初始化到鄰居的成本；每次迭代從尚未確定的節點中挑出 D 值最小者加入 N'，並更新其鄰居的 D 值（`D(v)=min(D(v), D(w)+c(w,v))`）。
> 3. 直到所有節點都在 N' → 得到到所有目的地的最低成本路徑樹與 forwarding table。

**複雜度：**
- **計算複雜度**：n 個節點，每次迭代要檢查所有不在 N' 的節點 → $n(n+1)/2$ 次比較 → **$O(n^2)$**（更佳實作可達 $O(n\log n)$）
- **訊息複雜度**：每台 router 須向其他 n 台廣播鏈路狀態，每個訊息跨越 $O(n)$ 鏈路 → 整體 **$O(n^2)$**

#### Link-State 的震盪 (Oscillation) (HW4-7)

> 當**鏈路成本與流量有關**時，可能發生路由震盪 (route oscillation)。

> **HW4-7 震盪範例**：
> - 目的地 a，流量從 d、c、e 進入，速率分別為 1、e (<1)、1；鏈路成本是**方向性且與流量相關**。
> - **初始**：各節點依當前成本選路。
> - 大家都選了某條低成本路徑後，該路徑流量暴增 → 成本上升 → 下一輪所有節點又改選另一邊的路徑 → 那條路徑成本又上升…
> - 如此**來回擺盪**，路由永遠無法穩定。
> - 原因：所有節點同步地依「會隨自身選擇而改變的成本」重算路由，造成集體擺動。

### Distance-Vector 演算法 (HW4-8)

> 基於 **Bellman-Ford (BF) 方程式**（動態規劃）。

**Bellman-Ford 方程式：**
$$D_x(y) = \min_v \{\, c_{x,v} + D_v(y) \,\}$$
- $D_x(y)$：x 到 y 的最低成本路徑成本
- min 取遍 x 的所有鄰居 v；$c_{x,v}$ = x 到鄰居 v 的直接成本；$D_v(y)$ = v 到 y 的估計最低成本
- 達到最小值的鄰居 v 就是「往 y 的下一躍程 (next hop)」

> **Bellman-Ford 範例**：u 的鄰居 v、x、w 對 z 的估計：$D_v(z)=5, D_x(z)=3, D_w(z)=3$，$c_{u,v}=2, c_{u,x}=1, c_{u,w}=5$
> $$D_u(z)=\min\{2+5,\ 1+3,\ 5+3\}=\min\{7,4,8\}=4$$
> 達最小者為 x → 下一躍程是 x。

**關鍵想法 (key idea)：**
- 每個節點**不時把自己的 distance vector (DV) 估計傳給鄰居**
- 當 x 收到鄰居的新 DV 估計，就用 BF 方程式更新自己的 DV：
  $$D_x(y) \leftarrow \min_v\{c_{x,v}+D_v(y)\}\quad \forall y \in N$$
- 在自然條件下，估計 $D_x(y)$ 會**收斂**到真正的最低成本 $d_x(y)$

**每個節點的運作（iterative, asynchronous, distributed, self-stopping）：**
- **iterative / asynchronous**：每次本地迭代由「本地鏈路成本變化」或「鄰居傳來 DV 更新訊息」觸發
- **distributed / self-stopping**：每個節點**只在自己 DV 改變時**才通知鄰居；若收不到通知也不採取行動

> **HW4-8 Distance-vector 演算法操作**：
> 1. 每個節點維護一個到所有目的地的距離向量 (DV)，初始只知道到直接鄰居的成本。
> 2. 節點週期性地或在 DV 改變時，把自己的 DV 傳給鄰居。
> 3. 當節點收到鄰居的 DV，就用 **Bellman-Ford 方程式** `Dx(y)=min_v{c(x,v)+Dv(y)}` 重新計算自己到各目的地的最低成本。
> 4. 若自己的 DV 有改變，再通知鄰居；反覆進行直到收斂（分散式、非同步、自我停止）。

#### 鏈路成本變化與收斂

- **好消息傳得快 (good news travels fast)**：鏈路成本降低時很快收斂
- **壞消息傳得慢 (bad news travels slow)** → **count-to-infinity 問題 (HW4-9)**

> **Count-to-infinity 問題 (HW4-9)**：
> 拓樸 `x —(4)— y —(1)— z`，y-x 直接鏈路成本由 4 變成 60。
> - y 發現 y-x 直接成本變 60，但 z 曾說它到 x 的成本是 5（其實 z 是經 y！）→ y 算出「我經 z 到 x 成本 = 1+5 = 6」，通知 z。
> - z 得知經 y 到 x 成本變 6 → z 算「我經 y 到 x = 1+6 = 7」，通知 y。
> - y 又算「經 z 到 x = 1+7 = 8」… z 又算 9…
> - 兩者**來回慢慢遞增**，要經過很多輪才會收斂到正確值（48），這就是 count-to-infinity。

#### Poisoned Reverse（毒性反向）(HW4-10)

> **解決 count-to-infinity 的方法之一**。

> **HW4-10 Poisoned reverse 如何解決 count-to-infinity**：
> 若 z 是「**經過 y**」才能到達 x，則 z 就向 y 廣告「z 到 x 的距離 = ∞」（謊報為無限大，把反向路徑「毒化」）。
> - 如此一來，當 y-x 鏈路成本上升時，y 不會誤以為「可以經 z 到 x」（因為 z 告訴 y 它到 x 是 ∞）。
> - y 便直接得知到 x 的成本要走別的路（或變大），避免了 y↔z 互相依賴對方的舊路徑而緩慢遞增的迴圈。
> - ⚠️ 注意：poisoned reverse 無法解決**三個以上節點**形成的迴圈。

#### Link-State vs. Distance-Vector 比較 (HW4-11)

| 比較面向 | **Link-State (LS)** | **Distance-Vector (DV)** |
|----------|---------------------|---------------------------|
| **訊息複雜度** | n 台 router，需 $O(n^2)$ 訊息（每台廣播給全網） | 只在**鄰居間**交換；收斂時間不定 |
| **收斂速度** | $O(n^2)$ 演算法、$O(n^2)$ 訊息；**可能震盪 (oscillation)** | 收斂時間不定；**可能有路由迴圈、count-to-infinity** |
| **強健性 (robustness)**（router 故障/被攻陷時） | router 只會廣告錯誤的**自己的**鏈路成本；每台**各自**算自己的表 → **錯誤較不會擴散** | router 可能廣告錯誤的**路徑成本**（「我到處都有超低成本路徑」→ black-holing）；且**每台的表被別人使用** → **錯誤會在全網傳播** |

> **HW4-11 答案**：
> - **訊息複雜度**：LS 需 $O(n^2)$（全網洪泛）；DV 只在鄰居間交換。
> - **收斂速度**：LS 較快但可能震盪；DV 收斂時間不定，可能有路由迴圈與 count-to-infinity。
> - **強健性**：LS 中每台 router 獨立計算自己的表、只廣告自己的鏈路狀態，錯誤影響有限；DV 中錯誤的距離資訊會被鄰居採用並繼續傳播，影響全網（black-holing）。

---

## 5.3 Intra-ISP 路由：OSPF

### 可擴展路由 (Scalable Routing) (HW4-13)

- 先前的路由研究是**理想化**的：所有 router 相同、網路「扁平 (flat)」 → 實務不成立
- 兩大挑戰：
  - **規模 (scale)**：數十億目的地，無法全存進路由表；路由表交換會塞爆鏈路
  - **管理自主性 (administrative autonomy)**：網際網路是「網路的網路」，各網路管理者想自行掌控自家路由

### 自治系統 (Autonomous Systems, AS)

> 網際網路的可擴展路由方法：把 router 聚合成稱為 **自治系統 (AS)**（又稱「網域 domains」）的區域。

| | **Intra-AS（網域內）** | **Inter-AS（網域間）** |
|---|---|---|
| 範圍 | 同一 AS 內的路由 | AS 之間的路由 |
| 協定 | AS 內所有 router 須跑**相同**的 intra-domain 協定 | 由 **gateway router** 執行 inter-domain 路由 |
| 不同 AS | 不同 AS 可跑**不同**的 intra-domain 協定 | — |

- **gateway router**：位於自家 AS 的「邊緣」，有鏈路連到其他 AS 的 router

> **Internet 如何用 intra-AS 與 inter-AS 達成可擴展路由 (HW4-13)**
> 網際網路把 router 分組成許多 **AS**。**AS 內部**用 intra-AS 協定（如 OSPF）各自計算內部路由，與其他 AS 無關；**AS 之間**用 inter-AS 協定（BGP）交換「哪些目的地可經由哪個 AS 到達」的可達性資訊。
> - forwarding table 由兩者共同決定：**intra-AS 路由**決定**AS 內部目的地**的項目；**inter-AS + intra-AS** 共同決定**外部目的地**的項目。
> - 這種**階層式**設計使路由表大小與更新流量都大幅縮減，且各 AS 保有管理自主性 → 達成可擴展性。

### Inter-AS 路由在網域內轉送中的角色

- 假設 AS1 的 router 收到一個目的地在 AS1 外的資料報：它該把封包轉給 AS1 內**哪一個** gateway router？
- AS1 的 inter-domain 路由必須：
  1. 學到哪些目的地可經 AS2、哪些可經 AS3
  2. 把這些可達性資訊傳播給 AS1 內所有 router

### 常見 Intra-AS 路由協定

| 協定 | 類型 | 備註 |
|------|------|------|
| **RIP** (Routing Information Protocol) | 經典 DV，每 30 秒交換 DV | 已少用 |
| **EIGRP** (Enhanced Interior Gateway Routing Protocol) | DV based | 原 Cisco 專有，2013 開放 |
| **OSPF** (Open Shortest Path First) | **link-state** 路由 | IS-IS 與 OSPF 本質相同 |

### OSPF (Open Shortest Path First)

- **"open"**：公開可取得
- **經典 link-state**：
  - 每台 router 把 OSPF link-state advertisements **洪泛 (flood)** 給整個 AS 的所有 router（**直接跑在 IP 上**，而非 TCP/UDP）
  - 可用多種鏈路成本度量：頻寬、延遲
  - 每台 router 擁有完整拓樸，用 **Dijkstra 演算法**算 forwarding table
- **安全**：所有 OSPF 訊息皆經**認證 (authenticated)**，防止惡意入侵

### 階層式 OSPF (Hierarchical OSPF) (HW4-12)

> **兩層階層 (two-level hierarchy)**：本地區域 (local area)、骨幹 (backbone)。

- link-state advertisements 只在**區域內或骨幹內**洪泛
- 每個節點有自己**區域的詳細拓樸**，但對其他區域只知道「往哪個方向走」

| 角色 | 功能 |
|------|------|
| **local routers（本地 router）** | 只在區域內洪泛 LS、計算區域內路由；往外的封包送給 area border router |
| **area border routers（區域邊界 router）** | 「摘要 (summarize)」到自家區域內各目的地的距離，廣告到骨幹 |
| **backbone routers（骨幹 router）** | 只在骨幹跑 OSPF |
| **boundary router（邊界 router）** | 連到其他 AS |

> **HW4-12 階層式 OSPF 的概念與好處**：
> - **概念**：把一個大 AS 切成兩層 —— 多個 **area** 與一個 **backbone**。LS 廣告只在各 area 內部洪泛，area 邊界 router 只把「到區域內目的地的摘要距離」廣告到骨幹。
> - **好處**：(1) **可擴展性**——每台 router 只需維護自己 area 的詳細拓樸，而非整個 AS，大幅降低 LS 洪泛範圍、計算量與路由表大小；(2) 控制廣告流量、加快收斂；(3) 便於大型 AS 的管理。

---

## 5.4 Inter-ISP 路由：BGP

### BGP 概觀 (HW4-14)

> **BGP (Border Gateway Protocol)**：事實上的 (de facto) 網域間路由協定，「把網際網路黏在一起的膠水」。

- 讓子網路得以向全網**廣告自己的存在與可達的目的地**：「我在這裡，這是我能到達的對象與方式」
- BGP 是 **path vector（路徑向量）** 協定
- BGP 讓每個 AS 能：
  - **eBGP (external BGP)**：從**鄰近 AS** 取得子網路可達性資訊
  - **iBGP (internal BGP)**：把可達性資訊**傳播給 AS 內部所有 router**
  - 根據可達性資訊與**政策 (policy)** 決定到其他網路的「好」路徑

> **eBGP 與 iBGP 的功能 (HW4-14)**：
> - **eBGP**：跑在**不同 AS 的 gateway router 之間**，用來從鄰近 AS 取得（與廣告）子網路可達性資訊（路徑廣告）。
> - **iBGP**：跑在**同一 AS 內部的 router 之間**，把從 eBGP 學到的外部可達性資訊傳播給 AS 內所有 router。
> - gateway router **同時執行 eBGP 與 iBGP**。

### BGP 基本 (BGP Basics)

- **BGP session**：兩台 BGP router（「peers」）在**半永久 TCP 連線**上交換 BGP 訊息
- 內容：廣告到不同目的網路前綴 (prefix) 的**路徑**
- 例：AS3 gateway 3a 向 AS2 gateway 2c 廣告路徑 **AS3, X** → AS3 承諾會把往 X 的資料報轉送過去

### 路徑屬性與 BGP 路由 (Path Attributes)

- **BGP 廣告的路由 = prefix + attributes**
  - **prefix**：被廣告的目的地
  - 兩個重要屬性：
    - **AS-PATH**：此前綴廣告經過的 AS 清單
    - **NEXT-HOP**：指出到下一個 AS 的特定 AS 內部 router
- **政策式路由 (policy-based routing)**：
  - gateway 收到路由廣告，用 **import policy** 決定接受/拒絕（例如「絕不經過 AS Y」）
  - AS 的政策也決定是否要把路徑廣告給其他鄰近 AS

### BGP 路徑廣告 (Path Advertisement) (HW4-15)

- AS2 router 2c 經 eBGP 從 AS3 router 3a 收到路徑廣告 **AS3, X**
- 依 AS2 政策，2c 接受 AS3,X，經 **iBGP** 傳播給 AS2 所有 router
- 依 AS2 政策，AS2 router 2a 經 eBGP 把路徑 **AS2, AS3, X** 廣告給 AS1 router 1c
- gateway router 可能學到**多條**到同一目的地的路徑（例：1c 從 2a 學到 AS2,AS3,X，又從 3a 學到 AS3,X），依政策擇一並經 iBGP 廣告給 AS1 內部

> **BGP + OSPF 產生 forwarding-table 項目 (HW4-15)**：
> 1. **BGP（inter-AS）**：1c 學到「到 X 要走 AS3,X」這條路徑，經 iBGP 告知 AS1 內所有 router（1a, 1b, 1d）：「到 X 要經過 gateway 1c」。
> 2. **OSPF（intra-AS）**：各內部 router 用 OSPF 算出「到 gateway 1c 該走哪個介面」。
> 3. **組合**：例如在 1d，OSPF 算出「到 1c 用介面 1」，所以 forwarding table 設「到 X → 介面 1」；在 1a，OSPF 算出「到 1c 用介面 2」，所以「到 X → 介面 2」。
> → 結論：**BGP 決定「往哪個 gateway / 哪條 AS 路徑」，OSPF 決定「在 AS 內部走哪個介面到達該 gateway」**，兩者共同填出 forwarding table。

### BGP 訊息 (Messages)

- BGP 訊息在 peers 間透過 **TCP 連線**交換：

| 訊息 | 功能 |
|------|------|
| **OPEN** | 開啟到遠端 BGP peer 的 TCP 連線並認證傳送方 |
| **UPDATE** | 廣告新路徑（或撤回舊路徑） |
| **KEEPALIVE** | 無 UPDATE 時維持連線；也用來 ACK OPEN 請求 |
| **NOTIFICATION** | 回報前一訊息的錯誤；也用來關閉連線 |

### 為何 Intra-AS 與 Inter-AS 路由不同？

| 面向 | 說明 |
|------|------|
| **政策 (policy)** | inter-AS：管理者想控制流量怎麼走、誰能穿越自家網路；intra-AS：單一管理者，政策較不重要 |
| **規模 (scale)** | 階層式路由省下路由表大小、降低更新流量 |
| **效能 (performance)** | intra-AS：可專注於效能；inter-AS：**政策凌駕效能** |

### 燙手山芋路由 (Hot Potato Routing) (HW4-16)

> 當 AS 內某 router（經 iBGP）學到「可經由多個本地 gateway 到達某目的地」時，**選擇 intra-domain 成本最低的本地 gateway**，不管 inter-domain（AS 躍程數）成本。

> **HW4-16 Hot potato routing 如何運作**：
> - 例：2d 經 iBGP 得知可經 2a 或 2c 到達 X。
> - hot potato 的精神是「**儘快把封包丟出自己的 AS**」：選擇從自己出發、**到該 gateway 的 intra-domain（OSPF）成本最小**的那一個 gateway。
> - 例如 2d 到 2a 的 OSPF 成本(201) < 到 2c(263)，即使經 2a 的 AS 躍程較多，2d 仍選 2a。
> - 即：只在意「最快離開本地 AS 的成本」，**不關心** inter-domain 成本。

### BGP 政策範例 (Achieving Policy via Advertisements) (HW4-17)

> ISP 通常只想為**自己的客戶網路**載送流量，**不想替其他 ISP 載送轉送流量 (transit traffic)** —— 典型現實政策。

> **HW4-17 BGP 政策操作範例**：
> **範例一**（A, B, C 為 provider，w 為客戶）：
> - A 把路徑 **A,w** 廣告給 B 和 C。
> - **B 選擇「不」把 B,A,w 廣告給 C** —— 因為 C、A、w 都不是 B 的客戶，B 替 C↔A↔w 載送轉送流量賺不到「收入」。
> - 所以 C 不知道 C,B,A,w 這條路，C 只能用 **C,A,w**（不經 B）去 w。
>
> **範例二**（x 為 dual-homed 客戶，同時連到 B 和 C）：
> - 要強制的政策：**x 不想替 B 到 C 的流量做中繼**。
> - 所以 **x 不會把「經 x 到 C」的路徑廣告給 B**。
> - → 透過「選擇性地廣告/不廣告路徑」，AS 就能實施流量政策。

### BGP 路由選擇 (Route Selection)

當 router 學到多條到同一目的地的路由，依序選擇：

1. **本地偏好值 (local preference)**：政策決定（最優先）
2. **最短 AS-PATH**
3. **最近的 NEXT-HOP router**：即 **hot potato routing**
4. 其他額外準則

---

## 本章重點速查

| 主題 | 重點 |
|------|------|
| LS vs. DV | LS=全域/Dijkstra/$O(n^2)$訊息/可能震盪；DV=去中心/Bellman-Ford/可能 count-to-infinity |
| Dijkstra 更新式 | `D(v)=min(D(v), D(w)+c(w,v))` |
| Bellman-Ford | `Dx(y)=min_v{c(x,v)+Dv(y)}` |
| count-to-infinity 解法 | poisoned reverse（向「下一躍程鄰居」謊報距離為 ∞） |
| AS / scalable routing | intra-AS(OSPF) 管內部、inter-AS(BGP) 管之間，階層式縮表 |
| OSPF | link-state、跑在 IP 上、Dijkstra、訊息認證、可分區域(hierarchical) |
| BGP | path vector、TCP 連線、eBGP/iBGP、政策凌駕效能 |
| BGP 屬性 | AS-PATH、NEXT-HOP |
| hot potato | 選 intra-domain 成本最低的 gateway，最快離開本地 AS |
| BGP 選路順序 | local pref → 最短 AS-PATH → hot potato → 其他 |
