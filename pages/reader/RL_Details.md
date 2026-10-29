# Reinforcement Learning (RL): Complete Exam Study Guide

---

## 1. Core Concepts & The RL Paradigm

Reinforcement Learning (RL) is a framework for learning sequential decision-making through trial-and-error interactions with an environment. Unlike **Supervised Learning** (which learns from labeled input-output pairs) and **Unsupervised Learning** (which finds hidden structure in unlabeled data), RL learns from **delayed evaluative feedback** (rewards and penalties).

### The Agent-Environment Loop
At each discrete time step $t = 0, 1, 2, \dots$:
1. The **Agent** observes the current **State** $s_t \in \mathcal{S}$ of the **Environment**.
2. Based on its policy, the agent selects and executes an **Action** $a_t \in \mathcal{A}$.
3. The environment transitions to a new state $s_{t+1} \in \mathcal{S}$ according to transition dynamics.
4. The environment emits a scalar **Reward** $r_{t+1} \in \mathbb{R}$ back to the agent.

$$\text{State } (s_t) \xrightarrow{\text{Agent chooses Action } (a_t)} \text{Environment} \xrightarrow{\text{Emits}} \text{Next State } (s_{t+1}) + \text{Reward } (r_{t+1})$$

---

## 2. Markov Decision Processes (MDPs)

An MDP provides the formal mathematical framework for modeling decision-making in situations where outcomes are partly random and partly under the control of a decision-maker.

### The Markov Property
A state $s_t$ is **Markov** if and only if the future state and reward depend purely on the present state and action, and are conditionally independent of all past states and actions:

$$P(S_{t+1} = s', R_{t+1} = r \mid S_t = s_t, A_t = a_t, S_{t-1} = s_{t-1}, \dots, S_0 = s_0) = P(S_{t+1} = s', R_{t+1} = r \mid S_t = s_t, A_t = a_t)$$

*"The present completely captures all relevant history."*

### The MDP 5-Tuple: $(\mathcal{S}, \mathcal{A}, P, R, \gamma)$
1. **$\mathcal{S}$ (State Space):** The set of all valid states the environment can be in.
2. **$\mathcal{A}$ (Action Space):** The set of all possible actions available to the agent.
3. **$P$ (State Transition Probability Matrix):** Defines the environment's dynamics.
   $$P(s' \mid s, a) = \Pr(S_{t+1} = s' \mid S_t = s, A_t = a)$$
4. **$R$ (Reward Function):** The expected immediate reward received after transitioning from state $s$ to $s'$ via action $a$:
   $$R(s, a, s') = \mathbb{E}[R_{t+1} \mid S_t = s, A_t = a, S_{t+1} = s']$$
5. **$\gamma$ (Discount Factor):** A scalar value where $0 \le \gamma \le 1$ that determines the present value of future rewards.

### Return ($G_t$) and Discounting
The agent's objective is not to maximize immediate reward $r_{t+1}$, but to maximize the **cumulative discounted return** $G_t$ from time step $t$ onward:

$$G_t = R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + \dots = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1}$$

Notice the recursive relationship (crucial for deriving the Bellman equation):
$$G_t = R_{t+1} + \gamma G_{t+1}$$

**Why do we use a discount factor $\gamma$?**
* **Mathematical Convergence:** Prevents infinite returns in continuous (non-terminating) tasks or cyclic MDPs.
* **Uncertainty:** Future transitions are stochastic; immediate rewards are more certain than distant ones.
* **Behavioral Control:**
  * If $\gamma = 0$: The agent is completely **myopic** (short-sighted) and only cares about the immediate reward $R_{t+1}$.
  * If $\gamma \rightarrow 1$: The agent is **far-sighted** and weighs future rewards almost as heavily as immediate ones.

---

## 3. Policies, Value Functions, and Bellman Equations

### 1. Policy ($\pi$)
A policy is the agent's behavior function mapping states to actions:
* **Deterministic Policy:** $a = \pi(s)$
* **Stochastic Policy:** $\pi(a \mid s) = \Pr(A_t = a \mid S_t = s)$

### 2. State-Value Function ($V^{\pi}(s)$)
The expected return starting from state $s$ and following policy $\pi$ thereafter:
$$V^{\pi}(s) = \mathbb{E}_{\pi}[G_t \mid S_t = s] = \mathbb{E}_{\pi}\left[ R_{t+1} + \gamma V^{\pi}(S_{t+1}) \mid S_t = s \right]$$

### 3. Action-Value Function / Q-Function ($Q^{\pi}(s, a)$)
The expected return starting from state $s$, taking action $a$, and following policy $\pi$ thereafter:
$$Q^{\pi}(s, a) = \mathbb{E}_{\pi}[G_t \mid S_t = s, A_t = a]$$

### 4. The Bellman Optimality Equations
The optimal policy $\pi^*$ maximizes the value of every state. The optimal state-value $V^*(s)$ and optimal action-value $Q^*(s, a)$ satisfy the **Bellman Optimality Equations**:

$$V^*(s) = \max_{a \in \mathcal{A}} Q^*(s, a) = \max_{a \in \mathcal{A}} \sum_{s' \in \mathcal{S}} P(s' \mid s, a) \left[ R(s, a, s') + \gamma V^*(s') \right]$$

$$Q^*(s, a) = \sum_{s' \in \mathcal{S}} P(s' \mid s, a) \left[ R(s, a, s') + \gamma \max_{a' \in \mathcal{A}} Q^*(s', a') \right]$$

Once you have $Q^*(s, a)$, finding the optimal action in any state is a simple lookup:
$$\pi^*(s) = \arg\max_{a \in \mathcal{A}} Q^*(s, a)$$

---

## 4. Q-Learning (Model-Free Reinforcement Learning)

In many real-world scenarios, the agent **does not know** the transition probabilities $P(s' \mid s, a)$ or the reward function $R(s, a, s')$. It must learn purely from experience by interacting with the environment.

**Q-Learning** (Watkins, 1989) is an **off-policy, model-free Temporal Difference (TD)** control algorithm that iteratively approximates $Q^*(s, a)$ stored in a lookup table (the **Q-Table**).

### The Q-Learning Update Rule
After taking action $a_t$ in state $s_t$, observing reward $r_{t+1}$, and landing in next state $s_{t+1}$, we update the single entry $Q(s_t, a_t)$ using:

$$Q(s_t, a_t) \leftarrow Q(s_t, a_t) + \alpha \left[ r_{t+1} + \gamma \max_{a'} Q(s_{t+1}, a') - Q(s_t, a_t) \right]$$

*(Alternative algebraic form often used in exams)*:
$$Q_{\text{new}}(s_t, a_t) = (1 - \alpha) Q_{\text{old}}(s_t, a_t) + \alpha \left( r_{t+1} + \gamma \max_{a'} Q(s_{t+1}, a') \right)$$

### Breakdown of Terms
| Term | Name | Meaning |
| :--- | :--- | :--- |
| $\alpha \in (0, 1]$ | **Learning Rate** | Controls how fast new information overwrites old Q-values. |
| $r_{t+1}$ | **Immediate Reward** | Actual reward received from the step $(s_t, a_t) \rightarrow s_{t+1}$. |
| $\max_{a'} Q(s_{t+1}, a')$ | **Max Future Value** | The highest Q-value available among all actions in the **next state** $s_{t+1}$. |
| $r_{t+1} + \gamma \max_{a'} Q(s_{t+1}, a')$ | **TD Target** | The new improved estimate of what $Q(s_t, a_t)$ should be. |
| $\text{TD Target} - Q(s_t, a_t)$ | **TD Error ($\delta_t$)** | Discrepancy between the new estimate and our current stored value. |

> **Important Exam Rule (Terminal States):** If the next state $s_{t+1}$ is a **terminal state** (the episode ends), there are no future actions to take, so $\max_{a'} Q(s_{\text{terminal}}, a') = 0$. The update simplifies to:
> $$Q(s_t, a_t) \leftarrow Q(s_t, a_t) + \alpha \left[ r_{t+1} - Q(s_t, a_t) \right]$$

### Exploration vs. Exploitation ($\epsilon$-Greedy Policy)
How does the agent choose actions during training?
* **Exploitation:** Choose the best-known action $\arg\max_a Q(s_t, a)$ to maximize reward based on current knowledge.
* **Exploration:** Choose a random action to discover potentially better pathways.
* **$\epsilon$-Greedy Strategy:** With probability $\epsilon$ (e.g., $\epsilon = 0.1$), pick a random action; with probability $1 - \epsilon$, pick the greedy action $\arg\max_a Q(s_t, a)$.

*(Why is Q-Learning called **Off-Policy**? Because even if the agent follows an exploratory $\epsilon$-greedy behavior policy to move around, the update rule always uses $\max_{a'} Q(s_{t+1}, a')$—assuming the optimal greedy target policy is followed from the next state onward!)*

---

## 5. Whiteboard Problem: 3-Step Q-Value Calculation

Below is a complete, exam-style numerical walkthrough formatted step-by-step like a lecture whiteboard.

---

### **WHITEBOARD WALKTHROUGH: 3-Step Q-Learning Update**

#### **Problem Statement**
Consider an agent navigating a $2 \times 2$ Grid-World with four states: $S_1 \text{ (Start)}, S_2, S_3, \text{ and } S_4 \text{ (Terminal Goal)}$. At each state, the agent can take two actions: **Right ($R$)** or **Down ($D$)**.

```
+-------------------+-------------------+
|        S1         |        S2         |
|      (Start)      |   (Hazard: -2)    |
+-------------------+-------------------+
|        S3         |        S4         |
|   (Bonus: +2)     |  (Terminal: +10)  |
+-------------------+-------------------+
```

**Given Hyperparameters:**
* Learning Rate: $\alpha = 0.5$
* Discount Factor: $\gamma = 0.8$

**Initial Q-Table (partially trained from earlier episodes):**

| State | Action: Right ($R$) | Action: Down ($D$) |
| :---: | :---: | :---: |
| **$S_1$** | $1.0$ | $2.0$ |
| **$S_2$** | $0.0$ | $4.0$ |
| **$S_3$** | $5.0$ | $1.0$ |
| **$S_4$ (Terminal)** | $0.0$ | $0.0$ |

**Observed 3-Step Trajectory:**
1. **Step 1:** From $S_1$, take action **Right ($R$)** $\rightarrow$ land in $S_2$, receive reward $r_1 = -2$.
2. **Step 2:** From $S_2$, take action **Down ($D$)** $\rightarrow$ land in $S_4$ ( wait—let's trace $S_1 \xrightarrow{R} S_2 \xrightarrow{D} S_3 \xrightarrow{R} S_4$ to visit 3 distinct transitions):
   * **Transition 1 ($t=0$):** $(S_1, R) \rightarrow S_2$ with reward $r_1 = -2$
   * **Transition 2 ($t=1$):** $(S_2, D) \rightarrow S_3$ with reward $r_2 = +2$
   * **Transition 3 ($t=2$):** $(S_3, R) \rightarrow S_4 \text{ (Terminal)}$ with reward $r_3 = +10$

*Calculate the updated Q-values for each of the 3 steps and write out the final Q-table.*

---

#### **Step 1: Master Update Formula**
$$Q_{\text{new}}(s_t, a_t) = Q_{\text{old}}(s_t, a_t) + \alpha \left[ r_{t+1} + \gamma \max_{a'} Q(s_{t+1}, a') - Q_{\text{old}}(s_t, a_t) \right]$$

---

#### **Step 2: Iteration 1 — Transition $(S_1, R) \rightarrow S_2$, Reward $r_1 = -2$**

1. **Identify Current Estimate:**
   $$Q_{\text{old}}(S_1, R) = 1.0$$

2. **Find Maximum Future Value at Next State ($S_2$):**
   Look at row $S_2$ in the Q-table: $Q(S_2, R) = 0.0$ and $Q(S_2, D) = 4.0$.
   $$\max_{a'} Q(S_2, a') = \max(0.0, 4.0) = 4.0$$

3. **Compute TD Target:**
   $$\text{TD Target} = r_1 + \gamma \max_{a'} Q(S_2, a') = -2 + (0.8)(4.0) = -2 + 3.2 = 1.2$$

4. **Compute TD Error ($\delta_1$):**
   $$\delta_1 = \text{TD Target} - Q_{\text{old}}(S_1, R) = 1.2 - 1.0 = 0.2$$

5. **Update $Q(S_1, R)$:**
   $$Q_{\text{new}}(S_1, R) = 1.0 + 0.5(0.2) = 1.0 + 0.1 = \mathbf{1.10}$$

---

#### **Step 3: Iteration 2 — Transition $(S_2, D) \rightarrow S_3$, Reward $r_2 = +2$**

1. **Identify Current Estimate:**
   $$Q_{\text{old}}(S_2, D) = 4.0$$

2. **Find Maximum Future Value at Next State ($S_3$):**
   Look at row $S_3$ in the Q-table: $Q(S_3, R) = 5.0$ and $Q(S_3, D) = 1.0$.
   $$\max_{a'} Q(S_3, a') = \max(5.0, 1.0) = 5.0$$

3. **Compute TD Target:**
   $$\text{TD Target} = r_2 + \gamma \max_{a'} Q(S_3, a') = 2 + (0.8)(5.0) = 2 + 4.0 = 6.0$$

4. **Compute TD Error ($\delta_2$):**
   $$\delta_2 = \text{TD Target} - Q_{\text{old}}(S_2, D) = 6.0 - 4.0 = 2.0$$

5. **Update $Q(S_2, D)$:**
   $$Q_{\text{new}}(S_2, D) = 4.0 + 0.5(2.0) = 4.0 + 1.0 = \mathbf{5.00}$$

---

#### **Step 4: Iteration 3 — Transition $(S_3, R) \rightarrow S_4 \text{ (Terminal)}$, Reward $r_3 = +10$**

1. **Identify Current Estimate:**
   $$Q_{\text{old}}(S_3, R) = 5.0$$

2. **Find Maximum Future Value at Next State ($S_4$):**
   Since $S_4$ is a **Terminal State**, its future value is strictly $0$:
   $$\max_{a'} Q(S_4, a') = 0.0$$

3. **Compute TD Target:**
   $$\text{TD Target} = r_3 + \gamma (0.0) = 10 + 0 = 10.0$$

4. **Compute TD Error ($\delta_3$):**
   $$\delta_3 = \text{TD Target} - Q_{\text{old}}(S_3, R) = 10.0 - 5.0 = 5.0$$

5. **Update $Q(S_3, R)$:**
   $$Q_{\text{new}}(S_3, R) = 5.0 + 0.5(5.0) = 5.0 + 2.5 = \mathbf{7.50}$$

---

#### **Step 5: Final Updated Q-Table & Greedy Policy Extraction**

| State | Action: Right ($R$) | Action: Down ($D$) | Optimal Action $\pi^*(s) = \arg\max_a Q(s,a)$ |
| :---: | :---: | :---: | :---: |
| **$S_1$** | $\mathbf{1.10}$ *(Updated)* | $2.00$ | **Down ($D$)** ($2.00 > 1.10$) |
| **$S_2$** | $0.00$ | $\mathbf{5.00}$ *(Updated)* | **Down ($D$)** ($5.00 > 0.00$) |
| **$S_3$** | $\mathbf{7.50}$ *(Updated)* | $1.00$ | **Right ($R$)** ($7.50 > 1.00$) |
| **$S_4$ (Terminal)** | $0.00$ | $0.00$ | *N/A (Goal Reached)* |

---

## 6. Clean Python Implementation of Tabular Q-Learning

If you need to write pseudocode or understand how the algorithm loops in code:

```python
import numpy as np

# Initialize Q-table from the whiteboard example
# Rows: S1 (0), S2 (1), S3 (2), S4 (3) | Cols: Right (0), Down (1)
Q = np.array([
    [1.0, 2.0],  # S1
    [0.0, 4.0],  # S2
    [5.0, 1.0],  # S3
    [0.0, 0.0]   # S4 (Terminal)
])

alpha = 0.5
gamma = 0.8

# Trajectory of (state, action, reward, next_state)
trajectory = [
    (0, 0, -2.0, 1),  # Step 1: (S1, Right) -> S2, r = -2
    (1, 1,  2.0, 2),  # Step 2: (S2, Down)  -> S3, r = +2
    (2, 0, 10.0, 3)   # Step 3: (S3, Right) -> S4, r = +10
]

for step, (s, a, r, s_next) in enumerate(trajectory, 1):
    old_q = Q[s, a]
    max_next_q = np.max(Q[s_next])
    td_target = r + gamma * max_next_q
    td_error = td_target - old_q
    Q[s, a] = old_q + alpha * td_error
    
    print(f"Step {step}: Old Q = {old_q:.2f} | TD Target = {td_target:.2f} | New Q = {Q[s, a]:.2f}")
```

---

## 7. Where to Find This in Your Reading Materials

* **Richard S. Sutton & Andrew G. Barto — *Reinforcement Learning: An Introduction* (2nd Edition):**
  * **Chapter 3 (Finite Markov Decision Processes):** Covers the agent-environment interface, returns, discounting ($\gamma$), value functions, and Bellman equations.
  * **Chapter 6 (Temporal-Difference Learning), Section 6.5 (Q-Learning: Off-Policy TD Control):** Derives the exact Q-learning formula and step-by-step updates.
* **Stuart Russell & Peter Norvig — *Artificial Intelligence: A Modern Approach*:**
  * **Chapter 17 (Making Complex Decisions):** Covers MDPs, Value Iteration, and Policy Iteration.
  * **Chapter 21 (Reinforcement Learning):** Covers Passive vs. Active RL and Q-Learning.