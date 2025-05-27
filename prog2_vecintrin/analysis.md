1. Run `./myexp -s 10000` and sweep the vector width from 2, 4, 8, to 16. Record the resulting vector utilization. 
You can do this by changing the #define VECTOR_WIDTH value in CS149intrin.h. 
Does the vector utilization increase, decrease or stay the same as VECTOR_WIDTH changes? Why?

- 关键实现思路
  - 向量化代码避免使用条件判断，会带来额外的开销
    - 分支预测
    - 全量执行，掩码丢弃结果，导致Effective下降，最坏情况下为串行性能

- Utilization：86.3%, 80.0%, 76.7%, 75.1%
- 原因：
  - 随着并行化的增加，一个vec中掩码为0的概率也上升，导致并行效率下降
  - 根本原因是原始逻辑中存在分支判断


1. Implement a vectorized version of arraySumSerial in arraySumVector.
Aim for runtime of `(N / VECTOR_WIDTH + VECTOR_WIDTH)` or even `(N / VECTOR_WIDTH + log2(VECTOR_WIDTH))`

- 利用`hadd` and `interleave` operations