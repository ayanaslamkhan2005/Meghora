# ☁️ Meghora — Cloud VM Scheduling Simulator

**An interactive cloud infrastructure simulator for virtual machine placement, resource allocation, and scheduling algorithm comparison.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-Visit%20Meghora-5AA9FF?style=for-the-badge)](https://ayanaslamkhan2005.github.io/Meghora/)

## 🌐 Live Demo

**[https://ayanaslamkhan2005.github.io/Meghora/](https://ayanaslamkhan2005.github.io/Meghora/)**

Meghora is a browser-based cloud VM scheduling simulator that demonstrates how virtual machines (VMs) can be allocated across physical hosts while considering CPU and RAM constraints.

It implements and compares three resource allocation strategies — **First Fit, Best Fit, and Load Balanced** — allowing users to experiment with different workloads and observe how each algorithm affects resource utilization and load distribution.

The application provides a visual representation of cloud infrastructure, real-time allocation results, utilization statistics, and side-by-side algorithm comparisons.

---

## 📌 Features

### 1. Cloud Infrastructure Management
- Add and configure multiple physical hosts.
- Specify CPU core capacity and RAM capacity for each host.
- Remove existing hosts.
- Monitor individual host resource utilization.
- View the number of virtual machines assigned to each host.

### 2. Virtual Machine Management
- Create virtual machines with custom configurations.
- Specify CPU and RAM requirements for each VM.
- Assign High, Medium, or Low priority labels.
- Remove virtual machines from the workload.
- Schedule VMs according to their order in the workload list.

**Note:** VM priority is displayed for reference only. It does not influence scheduling decisions. Allocation follows the order in which VMs appear in the list.

### 3. Multiple Scheduling Algorithms

Meghora implements three different VM placement strategies:

| Algorithm | Scheduling Strategy |
|---|---|
| First Fit | Selects the first host with sufficient available resources. |
| Best Fit | Selects the feasible host with the smallest normalized remaining capacity. |
| Load Balanced | Selects the placement that minimizes projected host load variation. |

Each algorithm uses the same CPU and RAM constraints but follows a different placement strategy.

### 4. Cloud Infrastructure Visualization
- Visual representation of individual physical hosts.
- CPU and RAM utilization progress bars.
- Display of VMs assigned to each host.
- Identification of unscheduled virtual machines.
- Animated feedback when scheduling is executed.

### 5. Scheduling Statistics

The dashboard provides eight key metrics:

- Total Hosts
- Total VMs
- Successfully Scheduled VMs
- Failed VM Allocations
- Average Host CPU Utilization
- Average Host RAM Utilization
- Overall CPU Utilization
- Overall RAM Utilization

### 6. Algorithm Comparison

Compare all three scheduling algorithms using the exact same host configuration and VM workload.

The comparison dashboard displays:

- Number of successfully scheduled VMs.
- Number of failed allocations.
- Average CPU utilization across hosts.
- Average RAM utilization across hosts.
- Overall CPU utilization.
- Overall RAM utilization.
- Load imbalance between hosts.

A visual comparison chart also helps identify how effectively each algorithm schedules the given workload.

### 7. Additional Functionality
- Generate a predefined example workload.
- Reset the simulator to an empty configuration.
- Automatically save host and VM configurations using browser LocalStorage.
- Restore saved configurations when reopening or refreshing the application.
- Validate user inputs and prevent duplicate host and VM IDs.
- Responsive interface for different screen sizes.

---

## ⚙️ Scheduling Algorithms Explained

Meghora implements three classic resource allocation strategies, each approaching the VM placement problem differently.

### 1. First Fit

**Approach:** Allocate each VM to the first available host that satisfies its CPU and RAM requirements.

**How it works:**
1. Iterate through the virtual machines in their listed order.
2. Traverse the physical hosts in their listed order.
3. Check whether the current host has sufficient remaining CPU and RAM.
4. Allocate the VM to the first host satisfying both conditions.
5. If no suitable host is found, mark the VM as failed.
6. Continue until all VMs have been processed.

**Example:**

Suppose three hosts have the following available resources:

| Host | Available CPU | Available RAM |
|---|---:|---:|
| Host A | 8 cores | 16 GB |
| Host B | 16 cores | 32 GB |
| Host C | 12 cores | 24 GB |

A VM requests 4 CPU cores and 8 GB RAM.

First Fit selects **Host A**, because it is the first host capable of accommodating the VM.

**Advantages:**
- Simple to implement.
- Fast allocation decisions.
- Predictable placement behaviour.

**Limitations:**
- Depends heavily on host ordering.
- May produce uneven resource utilization.
- Does not explicitly optimize resource distribution.

**Time Complexity:** `O(V × H)`

### 2. Best Fit

**Approach:** Allocate each VM to the feasible host that leaves the smallest combined normalized amount of unused CPU and RAM.

Unlike First Fit, Best Fit evaluates all feasible hosts before selecting one.

**How it works:**
1. Iterate through the virtual machines.
2. Examine every physical host.
3. Ignore hosts that cannot accommodate the VM.
4. Calculate a placement score for every feasible host.
5. Select the host with the lowest score.
6. Update the host's remaining resources.
7. Mark the VM as failed if no feasible host exists.

The placement score is calculated using:

```text
Score =
    (Remaining CPU after placement / Total Host CPU)
  + (Remaining RAM after placement / Total Host RAM)
```

The algorithm selects the host with the minimum score.

Normalizing CPU and RAM against their respective host capacities makes the two resource dimensions comparable.

**Advantages:**
- Considers both CPU and RAM when choosing a host.
- Attempts to pack VMs into hosts efficiently.
- Can reduce unused capacity across individual hosts.

**Limitations:**
- May concentrate resource usage on fewer hosts.
- Does not explicitly optimize load distribution.
- Requires evaluating all hosts for every VM.

**Time Complexity:** `O(V × H)`

### 3. Load Balanced

**Approach:** Select the feasible host that produces the most balanced projected resource utilization across the entire cluster.

Unlike First Fit and Best Fit, Load Balanced considers the effect of each possible placement on the overall distribution of host loads.

**How it works:**
1. Iterate through the virtual machines.
2. Consider every host capable of accommodating the current VM.
3. Temporarily calculate the projected resource utilization of every host after placing the VM on that candidate.
4. Calculate the standard deviation of projected host pressures.
5. Select the placement producing the lowest standard deviation.
6. If two placements have approximately equal standard deviations, prefer the one with the lower average projected pressure.
7. Commit the selected placement.
8. Mark the VM as failed if no host can accommodate it.

Host pressure is calculated as:

```text
Host Pressure = max(CPU Utilization %, RAM Utilization %)
```

The algorithm uses standard deviation to evaluate how evenly pressure is distributed across hosts.

**Advantages:**
- Attempts to distribute workloads more evenly.
- Considers both CPU and RAM constraints.
- Evaluates the impact of placement across the entire cluster.

**Limitations:**
- Computationally more expensive.
- May not maximize the number of scheduled VMs in every workload.
- Balanced resource distribution does not necessarily mean maximum resource packing efficiency.

**Time Complexity:** `O(V × H²)`

---

## 📊 Performance Metrics

Meghora calculates multiple metrics to evaluate scheduling performance.

### Average Host CPU Utilization

Calculates the arithmetic mean of CPU utilization percentages across all physical hosts.

```text
Average Host CPU =
    Sum of individual host CPU utilization percentages
    ---------------------------------------------------
                    Number of hosts
```

Every host receives equal weight, regardless of its CPU capacity.

### Average Host RAM Utilization

Calculates the arithmetic mean of RAM utilization percentages across all physical hosts.

```text
Average Host RAM =
    Sum of individual host RAM utilization percentages
    ---------------------------------------------------
                    Number of hosts
```

Every host receives equal weight.

### Overall CPU Utilization

Measures the proportion of total available CPU capacity being utilized across the entire cluster.

```text
Overall CPU Utilization =
    Total Used CPU / Total Available CPU × 100
```

Unlike average host utilization, this metric accounts for differences in host capacities.

### Overall RAM Utilization

Measures the proportion of total available RAM being utilized across all hosts.

```text
Overall RAM Utilization =
    Total Used RAM / Total Available RAM × 100
```

### Load Imbalance

Measures the difference between the highest and lowest host pressure.

```text
Load Imbalance =
    Maximum Host Pressure - Minimum Host Pressure
```

A lower value indicates a more uniform distribution of host pressure.

Host pressure is the maximum of CPU and RAM utilization percentages for that host.

**Important:** Average host utilization, overall utilization, and load imbalance measure different aspects of scheduling performance. They should be considered together rather than treating any single metric as a complete measure of algorithm quality.

---

## 🏗️ Project Architecture

Meghora follows a modular architecture that separates the scheduling engine from the user interface.

```text
Meghora/
│
├── index.html       # Application structure and interface
├── style.css        # Styling, layout and responsiveness
├── scheduler.js     # Core VM scheduling engine
├── script.js        # Application logic and UI interactions
└── README.md        # Project documentation
```

### Component Responsibilities

| File | Responsibility |
|---|---|
| `index.html` | Defines the application layout, controls, dashboard, tables and modal dialogs. |
| `style.css` | Handles visual styling, responsive layouts, animations and component appearance. |
| `scheduler.js` | Implements validation, resource allocation algorithms and performance calculations. |
| `script.js` | Manages application state, user interactions, rendering and browser storage. |

### Scheduling Engine: `scheduler.js`

The scheduling engine is independent of the user interface.

It exposes a global `SchedulerCore` API containing:

- `ALGORITHMS` – Supported scheduling strategies.
- `validateWorkload()` – Validates host and VM configurations.
- `createWorkingHosts()` – Creates host objects with initialized resource counters.
- `canPlace()` – Checks whether a host can accommodate a VM.
- `schedule()` – Executes the selected scheduling algorithm.
- `buildResult()` – Generates allocation results and performance metrics.

The engine can operate in a browser or a Node.js environment.

### Application Controller: `script.js`

The application controller connects the scheduling engine to the interface.

Its responsibilities include:

- Managing host and VM configurations.
- Handling user interactions.
- Executing scheduling algorithms.
- Rendering infrastructure and allocation results.
- Updating dashboard statistics.
- Comparing scheduling strategies.
- Persisting application configurations using LocalStorage.

This separation makes the scheduling engine easier to maintain, test and potentially reuse in other applications.

---

## 🛠️ Technology Stack

| Technology | Usage |
|---|---|
| HTML5 | Application structure and semantic elements |
| CSS3 | Responsive interface, styling, animations and visual components |
| JavaScript (Vanilla JS) | Scheduling algorithms, application logic and DOM manipulation |
| Browser LocalStorage | Persistent storage of host and VM configurations |
| Git & GitHub | Version control and project hosting |
| GitHub Pages | Static website deployment |

**No external frameworks, backend servers, databases or build tools are required.**

---

## 🚀 Getting Started

You can run Meghora locally or access the deployed application directly.

### Option 1: Use the Live Application

Visit:

**[Open Meghora](https://ayanaslamkhan2005.github.io/Meghora/)**

No installation is required.

### Option 2: Run Locally

**1. Clone the repository**

```bash
git clone https://github.com/ayanaslamkhan2005/Meghora.git
```

**2. Navigate to the project directory**

```bash
cd Meghora
```

**3. Open the application**

Open `index.html` directly in your browser.

Alternatively, use the VS Code Live Server extension to run it locally.

There is no dependency installation or compilation step.

---

## 📖 How to Use

Follow these steps to experiment with different scheduling strategies.

**Step 1: Configure Cloud Hosts**

- Click `+ Add Host`.
- Enter a unique host ID.
- Specify CPU cores and RAM capacity.
- Add as many hosts as required.

**Step 2: Configure Virtual Machines**

- Click `+ Add VM`.
- Enter a unique VM ID.
- Specify CPU and RAM requirements.
- Select a priority label.

**Step 3: Select a Scheduling Algorithm**

Choose one of the following:
- First Fit
- Best Fit
- Load Balanced

The application displays a brief explanation of the selected algorithm, its complexity, advantages and limitations.

**Step 4: Run the Scheduler**

Click `Run Scheduler` to allocate VMs across the available hosts.

The application will update:
- Host resource utilization.
- VM allocation details.
- Scheduled and failed VM counts.
- Performance statistics.

**Step 5: Compare Algorithms**

Click `Compare Algorithms` to execute all three algorithms against the same workload.

Review the comparison table and visual chart to understand how different placement strategies affect resource utilization and load distribution.

**Step 6: Experiment Further**

Modify host capacities, add or remove VMs, generate the example workload, and observe how scheduling outcomes change.

---

## 🧪 Example Workload

Meghora includes a predefined workload that can be loaded using the `Generate Example` button.

### Physical Hosts

| Host | CPU Cores | RAM |
|---|---:|---:|
| Host-01 | 16 | 32 GB |
| Host-02 | 8 | 16 GB |
| Host-03 | 24 | 48 GB |
| Host-04 | 16 | 32 GB |

### Virtual Machines

The example contains 10 virtual machines with varying CPU and RAM requirements.

| VM | CPU Cores | RAM | Priority |
|---|---:|---:|---|
| VM-01 | 4 | 8 GB | High |
| VM-02 | 2 | 4 GB | Medium |
| VM-03 | 6 | 12 GB | High |
| VM-04 | 2 | 4 GB | Low |
| VM-05 | 8 | 16 GB | High |
| VM-06 | 4 | 8 GB | Medium |
| VM-07 | 2 | 4 GB | Low |
| VM-08 | 6 | 10 GB | Medium |
| VM-09 | 3 | 6 GB | Low |
| VM-10 | 4 | 8 GB | High |

This workload provides a convenient starting point for comparing the behaviour of different scheduling algorithms.

---

## 🔒 Validation and Data Persistence

### Input Validation

The application validates user inputs before adding hosts or VMs.

| Field | Validation |
|---|---|
| Host/VM ID | Required, unique and maximum 40 characters |
| CPU Cores | Integer between 1 and 256 |
| RAM | Integer between 1 and 1024 GB |
| Duplicate IDs | Not permitted, case-insensitive |
| Resource allocation | A host must have sufficient CPU and RAM |

The scheduling engine also performs its own workload validation independently of the UI.

### LocalStorage

Meghora uses browser LocalStorage to preserve host configurations, VM configurations and the last selected scheduling algorithm.

This allows users to refresh or reopen the application without losing their saved workload.

Data is stored locally in the browser rather than on a remote server.

---

## ⚠️ Current Scope and Limitations

Meghora is an educational scheduling simulator designed to demonstrate VM placement strategies and resource allocation concepts.

The current implementation has the following limitations:

- It uses static CPU and RAM capacities rather than monitoring real cloud infrastructure.
- It does not connect to actual cloud providers such as AWS, Azure or Google Cloud.
- VM priority is informational and does not influence scheduling.
- Scheduling is performed sequentially in the order of the VM list.
- It does not implement VM migration or automatic rescheduling.
- It does not simulate network bandwidth, storage I/O, power consumption or monetary cost.
- Resource allocation results are simulated and do not represent actual VM deployments.

---

## 🔮 Future Improvements

Potential extensions to Meghora include:

- **Priority-Based Scheduling:** Incorporate VM priorities into allocation decisions.
- **Additional Algorithms:** Implement Round Robin, Worst Fit and other scheduling strategies.
- **VM Migration:** Introduce workload migration between hosts to improve resource distribution.
- **Advanced Metrics:** Include energy consumption, resource fragmentation and cost estimation.
- **Dynamic Workloads:** Simulate changing CPU and RAM requirements over time.
- **Cloud Integration:** Explore integration with cloud infrastructure APIs.
- **Data Export:** Allow users to export scheduling results and comparison reports.
- **Performance Visualization:** Introduce additional charts for resource utilization and load distribution.

---

## 🎯 Learning Objectives

This project explores several important concepts in computer science and cloud computing:

- Cloud computing and virtualization.
- Virtual machine placement and resource allocation.
- Bin packing and online scheduling concepts.
- Greedy scheduling algorithms.
- CPU and memory utilization.
- Load balancing and statistical analysis.
- Algorithmic time complexity.
- Modular JavaScript architecture.
- DOM manipulation and browser-based state management.

---

## 👨‍💻 Author

**Ayan Aslam Khan**

GitHub: [@ayanaslamkhan2005](https://github.com/ayanaslamkhan2005)

Project: [Meghora — Cloud VM Scheduling Simulator](https://ayanaslamkhan2005.github.io/Meghora/)

---

## 📄 License

No license has currently been specified for this project. All rights are reserved by the author unless a license is added to the repository.

---

**Meghora — A smarter way to balance the cloud. ☁️**
