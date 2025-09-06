# Knapsack Problem by Genetic Algorithm

---

## 1. Goal
The primary objective of this project is to develop a genetic algorithm (GA) to solve a modified version of the classic knapsack problem. The goal is to select an optimal combination of snacks for a picnic, subject to the following constraints:

-   **Weight Limit**: The total weight of the selected items must not exceed a specified maximum (`max_weight`).
-   **Value Maximization**: The total value of the selected items should be as high as possible.
-   **Item Count Constraint**: The number of selected items must fall within a specific interval (`legal_count_interval`).

The GA is designed to efficiently explore the solution space and find a high-quality solution that satisfies all constraints simultaneously.

---

## 2. Methodology: Genetic Algorithm
The solution is built upon a Genetic Algorithm framework, which iteratively evolves a population of candidate solutions towards an optimal one.

### Chromosome
A chromosome represents a single candidate solution. It is a list of integers, where each gene corresponds to an item. A value of `1` indicates the item is selected, while `0` indicates it is not. The length of the chromosome is equal to the total number of available items.

### Fitness Function
The design of the fitness function is crucial for guiding the algorithm toward valid and high-value solutions. The final, successful fitness function is as follows:

```python
def get_fitness(self) -> float:
    fitness = 0
    if (self.picked_item_count not in range(FIT_ITEM_COUNT, FIT_ITEM_COUNT + 1)):
        fitness = 0  # Invalid solution due to item count
    else:
        try:
            if self.weight > WEIGHT_LIMIT:
                raise ValueError
            fitness = self.value  # Reward high value for valid solutions
        except:
            fitness = 0  # Invalid solution due to weight
    return fitness
```
This function heavily penalizes solutions that violate the weight or item count constraints by assigning them a fitness of zero. For valid solutions, it directly uses the total value, ensuring that the algorithm maximizes the value of the items picked. This approach proved more effective than earlier attempts which struggled to balance the constraints.

### Population and Selection
-   **Population**: A collection of `Chromosome` objects. The population size is fixed at `TOTAL_CHROMO_COUNT`.
-   **Initial Generation**: The first generation is created randomly, ensuring some initial diversity. Two special chromosomes (all 0s and all 1s) are also included to represent boundary conditions.
-   **Selection**: A ranked-based selection method is used, where chromosomes are sorted by fitness, and a probability distribution is created. Higher-fitness chromosomes have a greater chance of being selected as parents for the next generation.

### Genetic Operators
-   **Crossover**: A single-point crossover operator combines genes from two parent chromosomes to create two new children. This process facilitates the sharing of beneficial traits between solutions.
-   **Mutation**: With a low probability (`mutation_rate`), a randomly selected gene in a chromosome is flipped (0 to 1, or 1 to 0). This introduces new genetic material into the population, helping to prevent the algorithm from getting stuck in a local maximum.

---

## 3. Data
-   **File**: `c.csv`
-   **Content**: A dataset of 19 snacks, each with a corresponding available weight and value.
-   **Visualization**: Bar plots were used to visualize the `Available Weight` and `Value` of each item, providing an overview of the dataset's characteristics.

---

## 4. Results
The algorithm was run for `TOTAL_GEN_COUNT = 100` generations with a population of `TOTAL_CHROMO_COUNT = 100`.

**Best Solution Found:**
-   **Fitness:** 21.45
-   **Weight:** 9.93
-   **Genes:** `[0, 0, 0.07, 0.94, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1.0, 0, 0, 0, 0, 0]`

The best fitness improved steadily over the generations, as shown in the "Best Fitness per Generation" plot. The final solution successfully met all specified constraints while maximizing the total value.

---

## 5. Discussion

1.  **Effects of Population Size**:
    -   **Extremely Large**: Requires significant computational resources (memory and time), making the algorithm inefficient.
    -   **Extremely Small**: Lacks genetic diversity, increasing the risk of premature convergence and getting trapped in a local optimum.

2.  **Effects of a Growing Population**:
    -   A growing population is not typically used in GAs. The population size is kept constant.
    -   If the population were to grow, it would increase computational complexity at each step, working against the goal of convergence. The algorithm's strength lies in evolving a fixed-size population by favoring fitter individuals.

3.  **Crossover vs. Mutation**:
    -   **Crossover** drives the algorithm's exploration by combining high-fitness solutions.
    -   **Mutation** ensures diversity, preventing the algorithm from converging too early on a suboptimal solution.
    -   It is possible to use only one, but the algorithm's performance would suffer. Crossover alone can lead to a local maximum, while mutation alone would be a slow, random search. Both are essential for an effective GA.

4.  **Approaches for Faster Convergence**:
    -   **Parameter Tuning**: Adjusting parameters like `mutation_rate`, `crossover_rate`, and population size can significantly impact efficiency.
    -   **Advanced Selection Methods**: Using more sophisticated selection techniques can accelerate the process.
    -   **Hybrid Algorithms**: Combining the GA with local search methods can quickly refine solutions.

5.  **Reasons for No Change in Chromosomes**:
    -   The algorithm may have converged to a **local maximum**. The entire population may consist of solutions that are variations of a single, good-but-not-optimal solution, and there isn't enough genetic diversity to break out.
    -   **Solutions**: Increase the `mutation_rate` to introduce more randomness. Use **multi-start** by running the algorithm with different initial populations to explore different parts of the solution space.

6.  **Stopping Without a Solution**:
    -   The algorithm can be stopped after a predefined number of generations (`TOTAL_GEN_COUNT`). This is a simple and effective way to ensure the algorithm terminates, whether a valid solution has been found or not.

---
