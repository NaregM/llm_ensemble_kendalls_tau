# Quantifying Rater Agreement in Ordered-Task Scenarios  

---

## Introduction  

In this study we analyse how consistently different raters (LLM based) order the
five tasks that belong to each scenario/question.  
Two complementary views are compared:

1. **Pairwise Rater Correlation Analysis** – a single value per rater-pair summarising overall agreement.  
2. **Majority-rater Analysis** – for every scenario, build a consensus ranking and
   see how far each individual deviates from it.

---

## Data structure  

* $N$ – number&nbsp;of raters (LLM instances), indexed as $\mathcal{L}_1,\dots,\mathcal{L}_N$
* $Q$ – number of scenarios/questions  
* each entry  

  $$
  \mathbf R_{i,q}= \bigl(r_{i,q}[1],\,r_{i,q}[2],\,r_{i,q}[3],\,r_{i,q}[4],\,r_{i,q}[5]\bigr),
  $$

where $r_{i, q}[t]$ indicates what task does rater $i$ puth $t^{th}$ for scenario $q$.
For example if
  $\mathbf R_{i,q}=(3,2,4,1,5)$  
  then rater $i$ believes task 3 should be done first, task 2 second, and so on.

We store all rankings in a 3-D NumPy array

$$
\mathbf R\in\{1,\dots,5\}^{N\times Q\times 5}.
$$

---

## 1 Inter-rater Correlation Analysis

For raters $\mathcal{L}_i$ and $\mathcal{L}_j$:

1. Compute Kendall’s $\tau_b$ **inside every scenario**

   $$
   \large
    \tau_{ij}^{(q)}
    \;=\;
    \tau_b\!\Bigl(\,\mathcal{L}_{i}^{(q)},\;\mathcal{L}_{j}^{(q)}\Bigr).
   \qquad q=1,\dots,Q.
   $$

2. Average across scenarios  

   $$
   K_{ij}= \frac{1}{Q}\sum_{q=1}^{Q}\tau_{ij}^{(q)}.
   $$

Collecting all pairs yields the $(Q \times Q)$ symmetric matrix

$$
\mathcal K=(K_{ij})_{N\times N}, \qquad -1\le K_{ij}\le 1.
$$

> **Interpretation**  
> * $K_{ij}=1$ – raters $i$ and $j$ give *identical* orderings for every scenario.  
> * $K_{ij}=0$ – no systematic association.  
> * $K_{ij}=-1$ – perfect reverse order on average.

---

## 2 Majority-rater comparison  

### 2.1 Building the consensus ranking  

For each scenario $q$ compute the position-wise mode

$$
m_q[t]=\operatorname*{mode}_{i=1,\dots,N}\{\,r_{i,q}[t]\},
\qquad t=1,\dots,5.
$$

Tie-break rule: choose the smallest tied task ID so that  
$\mathbf m_q=(m_q[1],\dots,m_q[5])$ is a valid permutation. Call the majority voter $\mathcal{L}^{\star}$.

### 2.2 Individual agreement scores  

For each rater $\mathcal{L}_i$

$$
\large
A_{i,q}= \tau_b\!\bigl(\,\mathcal{L}_i(q),\,\mathcal{L}^{\star}(q)\bigr),
\qquad -1\le A_{i,q}\le 1.
$$

The matrix  

$$
\mathbf A=(A_{i,q})_{N\times Q}
$$

lets us

* **rank raters** by row-averaging $A_{i,q}$ – higher mean $\to$ closer to consensus,  
* **spot controversial scenarios** by column-averaging – lower mean $\to$ greater disagreement.

---