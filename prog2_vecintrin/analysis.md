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
  - 根本原因是原始逻辑中存在分支判断, 需要用掩码弥补分支


1. Implement a vectorized version of arraySumSerial in arraySumVector.
Aim for runtime of `(N / VECTOR_WIDTH + VECTOR_WIDTH)` or even `(N / VECTOR_WIDTH + log2(VECTOR_WIDTH))`
- 朴素idea：每VECTOR_WIDTH加到result（**向量求和**），然后递归调用arraySumVector, N=N/2（**水平求和**）
  - 时间复杂度满足要求，但是带来而外的空间复杂度（每个Stack Frame会有额外的开销）
- 利用`hadd` and `interleave` operations?
  - hadd：水平求和，将VEC中的数据两两相加
  - interleave：交叉，将相加后的数据置换到数组前部
  - 利用这两个运算，就可以本地化循环运行的到最终的水平求和结果