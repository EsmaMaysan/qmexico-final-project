# Final Project — 4 × 4 Matching as QUBO and Local QAOA

## Overview

I completed this project for the QMexico Summer School final assignment. The goal of the project is to formulate a real or semi-real 4 × 4 matching problem as a QUBO, validate the result with a classical exact method, and run a local QAOA simulation.

I did not use the molecular/chemistry example from the base notebook as my final dataset. I replaced it with my own semi-real educational dataset based on matching four anonymous project profiles to four project roles.

The project follows this pipeline:

1. I define two sets with four elements each.
2. I build a 4 × 4 compatibility score matrix.
3. I formulate the matching problem as a QUBO.
4. I validate the optimum with exact classical search.
5. I run a local QAOA simulation.
6. I compare the local QAOA result with the classical optimum.

---

## Dataset

I used a semi-real educational dataset for a small quantum computing project team.

The first side of the matching problem is the set of profiles:

- P1: coding-oriented profile
- P2: quantum-oriented profile
- P3: optimization/writing profile
- P4: reporting/balanced profile

The second side of the matching problem is the set of roles:

- R1: Dataset justification and README
- R2: QUBO formulation
- R3: Classical validation code
- R4: Local QAOA implementation

The decision variable is:

x_ij = 1 if I assign profile P_i to role R_j,
x_ij = 0 otherwise.

This gives 4 × 4 = 16 binary decision variables.

The dataset is stored in:

data/dataset_real_4x4.csv

I used anonymized profile labels and avoided private names, grades, health data, or sensitive attributes.

---

## Score Formula

For every possible pair (profile, role), I computed a compatibility score. I replaced the original chemistry-based score from the educational notebook with a formula adapted to my own matching problem.

The score is:

S_ij = 100 * (0.70 * skill_cosine + 0.20 * interest/5 + 0.10 * availability/5)

where:

- skill_cosine measures how well the profile skills match the role requirements;
- interest is a value from 1 to 5;
- availability is a value from 1 to 5.

I gave the largest weight to skill compatibility because the main goal is to assign each profile to the role where it can contribute most effectively. Interest and availability are also included because a technically good assignment may still be weak if the person is not interested or not available.

The resulting score matrix is:

| Profile | R1 | R2 | R3 | R4 |
|---|---:|---:|---:|---:|
| P1 | 68.64 | 87.31 | 93.42 | 84.11 |
| P2 | 73.14 | 87.81 | 80.43 | 96.21 |
| P3 | 94.63 | 97.04 | 78.92 | 71.78 |
| P4 | 93.64 | 83.81 | 84.47 | 86.01 |

---

## Matching Constraints

The matching problem has two constraints.

First, each profile must be assigned to exactly one role:

sum_j x_ij = 1 for every profile i.

Second, each role must receive exactly one profile:

sum_i x_ij = 1 for every role j.

These constraints make the problem a one-to-one matching problem.

---

## QUBO Formulation

The original objective is to maximize the total score:

maximize sum_i sum_j S_ij x_ij.

Since QUBO is written as a minimization problem, I used the negative score:

- sum_i sum_j S_ij x_ij.

Then I added quadratic penalty terms to enforce the matching constraints.

The QUBO energy is:

E(x) =
- sum_i sum_j S_ij x_ij
+ lambda_A * sum_i (sum_j x_ij - 1)^2
+ lambda_B * sum_j (sum_i x_ij - 1)^2.

The row penalties force each profile to be assigned exactly once. The column penalties force each role to be assigned exactly once.

I selected penalty values large enough to make invalid assignments worse than valid assignments.

---

## Classical Validation

I validated the QUBO in two ways.

First, I performed an exhaustive search over all binary strings of length 16. This means checking:

2^16 = 65,536

possible assignments.

Second, I checked all feasible one-to-one matchings directly. Since this is a 4 × 4 matching problem, there are:

4! = 24

valid matchings.

Both methods produced the same optimum, which confirms that the QUBO formulation correctly represents the matching problem.

The best assignment I obtained is:

| Profile | Assigned role | Score |
|---|---|---:|
| P1 | R3 | 93.42 |
| P2 | R4 | 96.21 |
| P3 | R2 | 97.04 |
| P4 | R1 | 93.64 |

The total score is:

380.31

Since the solution is feasible, the penalty terms are zero. Therefore, the QUBO energy is:

-380.31

---

## Local QAOA Simulation

I also ran a local QAOA simulation on the QUBO instance.

The purpose of this part is not to claim quantum advantage. The goal is to demonstrate the QAOA workflow on a small QUBO matching instance and compare the sampled results with the classical optimum.

The local QAOA run sampled the classical optimum among the observed solutions. However, the probability of feasible solutions was low because the unconstrained QUBO search space contains many invalid bitstrings. This is expected for a small p=1 QAOA run with penalty-based constraints.

Therefore, I interpret the QAOA result carefully:

- the local QAOA simulation was able to sample the optimal assignment;
- the exact classical method remains the reference solution;
- the QAOA result is useful as a workflow validation, not as a performance advantage claim.

If a classical repair step is used, it should be understood as post-processing. It should not be presented as pure QAOA performance.

---

## Final Result

The final best assignment is:

P1 -> R3
P2 -> R4
P3 -> R2
P4 -> R1

The total score is:

380.31

This assignment makes sense because:

- P1 is coding-oriented, so it fits the classical validation code role.
- P2 is quantum-oriented, so it fits the local QAOA implementation role.
- P3 has optimization and writing strengths, so it fits the QUBO formulation role.
- P4 is balanced and reporting-oriented, so it fits the dataset justification and README role.

---

## Files in the Repository

The repository contains:

- proyecto_qubo_qaoa.ipynb: final project notebook;
- data/dataset_real_4x4.csv: 4 × 4 compatibility dataset;
- README.txt: explanation of the dataset, QUBO formulation, classical validation, QAOA run, and interpretation.

If GitHub is used, this file can also be renamed as README.md so that GitHub displays it automatically.

---

## How to Run

To run the project:

1. Open the notebook proyecto_qubo_qaoa.ipynb.
2. Make sure the file data/dataset_real_4x4.csv is present.
3. Run all cells in order.
4. Check the classical optimum and the local QAOA results.

The notebook should run locally or in Google Colab.

---

## Limitations

This is a small educational 4 × 4 instance. The exact classical search is possible because the problem size is small.

For larger matching problems, the number of binary variables and possible assignments grows quickly. Stronger classical optimization methods, better QAOA parameter optimization, and more careful benchmarking would be needed.

The local QAOA simulation does not prove quantum advantage. It only shows that I can formulate the problem as a QUBO and run the QAOA workflow on the instance.

---

## Ethical Considerations

I used anonymous profile labels instead of real names. I did not include personal, private, medical, financial, political, or sensitive information.

The dataset is semi-real and educational. It is designed to demonstrate QUBO formulation and QAOA-based optimization, not to evaluate real people.
