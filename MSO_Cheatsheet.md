# MSO 40.012 — Exam Cheatsheet (Weeks 2-5)
*Two-sided formula sheet allowed. Scientific calculator only.*

---

## WEEK 2: FORECASTING & AGGREGATE PLANNING

### Forecasting Methods

| Method | Formula | When to Use |
|--------|---------|-------------|
| **Naive** | F_t = A_{t-1} | Very responsive; good baseline |
| **Moving Average (n)** | F_t = (A_{t-1} + A_{t-2} + ... + A_{t-n}) / n | Smooths noise; n periods |
| **Weighted Moving Avg** | F_t = Sum(w_i * A_i) / Sum(w_i) | Heavier weights on recent data |
| **Exp. Smoothing** | F_t = F_{t-1} + alpha(A_{t-1} - F_{t-1}) | alpha=0-1; higher alpha = more responsive |
| **Linear Regression** | y = a + bx; b = [n*Sum(xy) - Sum(x)*Sum(y)] / [n*Sum(x^2) - (Sum(x))^2] | Trend projection |

### Forecast KPIs
- **MAPE** = (1/n) * Sum(|A_t - F_t| / A_t) * 100%
- **Forecast Bias** = Sum(A_t - F_t) — positive = under-forecasting
- **MAD** = (1/n) * Sum(|A_t - F_t|)

### Forecast Characteristics
- Aggregated data (product family) > SKU-level accuracy
- Short-term > long-term accuracy
- Forecasts are never 100% accurate — "fail to plan = plan to fail"

### Aggregate Planning
- **Goal**: determine production rates, workforce, inventory over fixed horizon
- **Planning Time Fence (PTF)**: frozen fence (~4 weeks) + PTF (~3 months)
- **I_t = I_{t-1} + P_t - F_t** (ending inventory = previous + production - forecast)

### Chase vs Level Strategy

| | Chase | Level |
|--|-------|-------|
| **Production** | Matches demand each period | Constant rate |
| **Pros** | Low inventory, min obsolescence | Stable workforce, predictable |
| **Cons** | Hiring/firing costs, equipment underuse | Excess inventory or shortages |

---

## WEEK 3: MPS & MRP

### MPS (Master Production Schedule)
- Disaggregates aggregate plan into specific SKUs by week
- Uses: fixed historical ratios, customer orders, safety stock
- MPS is a **statement of what to produce**, not a forecast
- Rolling schedule; near-term is frozen

### Fixed Planning Horizon Problem
**Objective**: minimize total cost = setup costs + holding costs

| Method | Description | Cost |
|--------|-------------|------|
| **One Time Run** | Produce everything in period 1 | 1 setup, max holding |
| **Lot for Lot** | Produce exactly what's needed each period | Max setups, 0 holding |
| **EOQ** | Produce in fixed EOQ batches | Balanced |
| **Silver-Meal** | Add periods to batch until avg cost/period starts rising | Near-optimal heuristic |
| **Wagner-Whitin** | Dynamic programming — optimal | Exact solution |

### Silver-Meal Algorithm
1. Start a new batch at period k
2. Compute average cost per period if you extend batch to cover k, k+1, k+2...
3. Stop extending when avg cost/period starts increasing
4. Start new batch at next uncovered period

### MRP (Material Requirements Planning)
- **Inputs**: MPS, BOM (Bill of Materials), Inventory records
- **BOM**: hierarchical tree of parent-child components with quantities
- **MRP Logic** (for each component, level by level):
  - Gross Requirements = parent planned orders * quantity per parent
  - Net Requirements = Gross - Scheduled Receipts - On Hand
  - Planned Order Receipts = Net Requirements (adjusted by lot sizing)
  - Planned Order Releases = Receipts offset by Lead Time

### MRP Assumptions
- Known demand, no capacity constraints, constant lead times, no back-ordering

---

## WEEK 4: SCHEDULING & PROJECT MANAGEMENT

### Scheduling Definitions
- **Forward scheduling**: start ASAP -> earliest end date
- **Backward scheduling**: work back from due date -> latest start
- **Loading**: assigning jobs to workstations

### Sequencing Rules (Single Machine)

| Rule | Sort by | Minimizes |
|------|---------|-----------|
| **FCFS** | Arrival order | — (fair) |
| **SPT** | Shortest processing time first | Mean flow time (Theorem 1) |
| **EDD** | Earliest due date first | Max lateness |
| **CR** | Critical Ratio = (due date - now) / processing time; lowest first | — (dynamic) |

### Performance Metrics
- **Flow Time** F_i = waiting time + processing time (= completion time if start=0)
- **Mean Flow Time** = Sum(F_i) / n
- **Lateness** L_i = F_i - d_i (can be negative = early)
- **Tardiness** T_i = max(L_i, 0)
- **Average Tardiness** = Sum(T_i) / n
- **Utilization** = Sum(processing times) / total flow time of last job

### Key Theorems
- **Theorem 1 (SPT)**: minimizes mean flow time, mean waiting time, mean lateness
- **Moore's Algorithm**: minimizes NUMBER of tardy jobs
  1. Order by EDD
  2. Find first tardy job [i]
  3. Among jobs [1]..[i], reject the one with longest processing time
  4. Repeat; append rejected jobs at end
- **Lawler's Algorithm**: minimizes MAXIMUM tardiness (with precedence constraints)
  - Builds schedule backwards; at each step picks the job from set V (no successors) with minimum tardiness if placed last

### Johnson's Rule (2 machines, n jobs)
1. List processing times on Machine A and Machine B
2. Find smallest value across both columns
3. If it's in column A → schedule that job **first** (from left)
4. If it's in column B → schedule that job **last** (from right)
5. Cross off job, repeat

### Akers Procedure (2 jobs, m machines)
- Cartesian plot: Job 1 on X-axis, Job 2 on Y-axis
- Block rectangles where both jobs use same machine
- Shortest path from origin to top-right = optimal schedule
- Horizontal = Job 1 processed; Vertical = Job 2 processed; 45-degree = both on different machines

### Assembly Line Balancing
- **Cycle Time C** = time allocated per workstation
- **# Workstations** = ceil(T / C) where T = sum of all task times
- **Ranked Positional Weight**: weight_i = t_i + sum of successor times
- Sort by decreasing weight, assign to stations respecting precedence and capacity

### Project Management (CPM/PERT)
- **WBS**: decompose project into work packages
- **Activity-on-Node (AON)** network diagram
- **Forward pass**: ES, EF (earliest start/finish)
  - ES = max(EF of predecessors); EF = ES + duration
- **Backward pass**: LS, LF (latest start/finish)
  - LF = min(LS of successors); LS = LF - duration
- **Slack** = LS - ES = LF - EF
- **Critical Path**: activities with slack = 0; determines project duration
- To shorten project: crash activities on the critical path

---

## WEEK 5: INVENTORY MANAGEMENT

### Why Inventory?
- Buffer against demand variability, supply variability, lead time uncertainty
- Types: Raw Materials, WIP, Finished Goods, MRO
- Inventory purposes: Reliability stock, Functional stock, Flexibility stock

### Inventory Costs
1. **Purchasing cost** = C * D
2. **Ordering cost** = A * (D/Q) — A per order
3. **Holding cost** = h * (Q/2) — where h = r * C (carrying rate * unit cost)
4. **Shortage/Backlog cost** (often ignored in basic models)

### EOQ (Wilson Formula)
```
Q* = sqrt(2AD / h)

TC(Q) = C*D + h*(Q/2) + A*(D/Q)
TVC(Q*) = sqrt(2*h*A*D)    [variable cost at optimum]
T* = Q*/D = sqrt(2A / hD)  [optimal cycle time]
N* = D/Q* = sqrt(hD / 2A)  [optimal order frequency]
```

**EOQ Assumptions**: constant known demand, instantaneous lead time, independent items, continuous review, single location, unlimited capacity, no discounts, no excess demand, no perishability, infinite horizon, one item, single stage

### Reorder Point (with lead time L)
- **ROP = d * L** (demand rate * lead time)
- With safety stock: **ROP = d*L + SS**
- SS = z * sigma_d * sqrt(L)

### Inventory Policies

| | Fixed Date | Variable Date |
|--|-----------|---------------|
| **Fixed Qty** | Automatic replenishment | **Reorder Point (r,Q)** |
| **Variable Qty** | **Periodic Review (T,S)** | Opportunistic buys |

- (r,Q): continuous review, order Q when inventory hits r — for Class A items
- (T,S): periodic review, order up to S every T periods — for Class C items

### EOQ Sensitivity
- TC curve is flat near Q* — ordering slightly more/less than Q* has small cost impact
- EOQ is robust to estimation errors in D, A, h

### Newsvendor Model (Single Period, Stochastic Demand)
- One-time ordering decision (perishable/seasonal goods)
- **C_u** = underage cost (cost of ordering too little) = selling price - cost
- **C_o** = overage cost (cost of ordering too much) = cost - salvage value
- **Critical ratio**: P(D <= Q*) = C_u / (C_u + C_o)
- Order Q* where the CDF of demand = critical ratio

### Bullwhip Effect
- Demand variability amplifies upstream in supply chain
- Causes: demand signal processing, order batching, price fluctuations, rationing/shortage gaming

---

## QUICK REFERENCE: EXAM QUESTION TYPES (from 2025 paper)

1. **Forecasting calculation** (~25pts): Naive, MA, WMA, trend projection + MAPE comparison
2. **Aggregate planning** (~25pts): Chase vs Level strategy with full cost table (hiring, firing, inventory, stockout, payroll)
3. **Project management** (~25pts): Draw AON network, find critical path, identify slack
4. **Scheduling** (~25pts): FCFS, SPT, CR tables with flow time, tardiness, utilization + Moore's algorithm
