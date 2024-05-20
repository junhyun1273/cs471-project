# CS471 Term Project

## Introduction
In the lecture, we learned node features of GNNs tend to become more similar with the increase of the network depth. This is known as the **over-smoothing** problem. Intuituvely, as the depth of network grows, the range over which nodes exchange information widens. As learning processes, local information get diluted, and the importance of global information increases, leading to the similarity of feature vectors for much more nodes. This naturally triggers a question: "How can we determine the depth of network to avoid the over-smoothing problem and optimize the learning process?"

In the case of GNNs, when the number of layers is $k$, then each node interacts with its $k$-hop neighborhoods. The extent of overlap between the $k$-hop neighborhoods of two nodes(receptive field) will determine how similar their feature vectors converge. We aim to investigate the relationship between the overlap of $k$-hop neighborhoods between nodes and the over-smoothing caused by network depth in GNNs.


- **Objective: Investigate how to find the optimal number of layers of GCN in which over-smoothing problem least occur.**


## Exp 1
Finding the correlation between $k$-hop neighborhood of nodes and feature vector smoothness.

### Details

- Define $k$-hop neighborhood of a node $v \in V$ as $N_k(v) = \{u \in V : \text{dist}(u, v) \le k\}$
- Define overlap between arbitrary two nodes $A, B \in V$ as $\text{overlap}=\frac{|N_k(A) \cap N_k(B)|}{|N_k(A) \cup N_k(B)|}$.
- How to define over-smootheness?
  - over-smoothing problem means feature vectors becoming indistinguishable.
  - Indistinguishability may be measured by cosine-similarity between vectors.
  - We compute the mean of cosine-similarity of all possible combinations of two nodes.
- Hypothesis:
  - Nodes in GCN with $k$ layers interacts with its $k$-hop neighborhood.
  - It is reasonable that $k$-hop neighborhood overlapping is highly related with its over-smoothing possibility.
- For the number of layers $k \in [1,10]$, compute the mean of $k$-hop neighbor overlap and the mean of cosine-similarity of feature vectors in all combinations of nodes.

### Results

TDL.


## Exp 2

* Motivation: In the exp 1 result, we found a tendency that two measurements are well-aligned in dense graph, while it does NOT hold in sparse graph.
* New hypothesis: The graph network (edge) density affects on the neighbor overlap or smoothness of feature vectors.

### Details
- We determined to make use of `Erdos-Renyi` model to generate various graphs in specified edge density. Refer to [here](https://en.wikipedia.org/wiki/Erd%C5%91s%E2%80%93R%C3%A9nyi_model).
- Two factors are considered: The number of nodes $|V|$ and the edge density $\rho=\frac{\epsilon}{|V|\choose2}$.
- Plot the line of measurements for all combinations of $|V|\in [50, 100, 250, 500]$ and $\rho \in [0.01, 0.02, 0.04, 0.06, 0.08, 0.1, 0.15, 0.2]$
  - It is based on the fact that graphs in real-world usually significantly sparse.

### Results


- Measurements become stable as $|V|$ increases.
- Neighbor overlap measurement grows much slower than the mean of cosine-similarities in sparse density.
- The overall mean of cosine-similarities on all combination of nodes cannot explain over-smoothness.


- 밀도 -> 스무싱 영향 X (어느 정도 스케일 이상 그래프) 대부분 레이어 2~4 근방에서 떡상
- k-neigh overlap -> 반대로 밀도에 의한 영향이 상당히 크다. (파란거) -> 이 지표는 스무싱을 설명하기 적절하지 못함.
- 1의 사실로부터 새로운 가설: shallow layer일때 제일 최적임 (가장 급격히 올라가기 전. 대강 레이어 2-4 근방) -> 왜?
  - 한계: 전체 조합에 대한 벡터간의 코사인 유사도 평균(cosine mean)이 커짐 => 모든 점이 한 방향으로 집중됨 => 분류가 어려워짐. 은 맞음
  - 그러나 반대로 (cosine mean 작음 => 분류가 용이해짐). 은 틀림. 클래스별 분포 차이를 설명 못함.
  - Feature vector가 분류 작업에 용이함을 보이려면 클래스별 분포가 서로 distinguishable해야 함. 즉, 클래스 평균 간 차이는 커야하고, 한 클래스 내 벡터들의 분산은 작아져야 함.

## Exp 3

- Motivation: 상기 내용 참조

### Detail

- Karate, Cora 데이터셋으로 앞선 방법과 동일하게 GCN 레이어 개수 바꿔가며 feature vector 추출
- feature vector를 클래스별로 나누어 각각 평균, 분산 계산
- 평균들의 분산을 계산/ 분산 벡터의 L2 norm 계산
- 레이어 개수에 따라 위의 계산값 plot.

### Result

- 클래스별 variance vector norm을 layer 개수 별로 플롯했더니, 레이어 2~4정도에서 급격하게 떡락 -> variance는 작을 수록 분류하기 좋음.
- 클래스별 mean vector들의 분산값은 레이어 개수가 커져도 위 그래프만큼 급격히 줄어들지 않음. mean의 분산은 반대로 작아질수록 분류에 불리하므로,
- variance norm이 가장 급격히 줄어드는 부근까지만 레이어 개수를 쌓는 것이 가장 이상적임.


### Further Research
- 왜 variance는 얕은 레이어에서 떡락함?