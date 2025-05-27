# Part 1
```
[mandelbrot serial]:            [281.886] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot ispc]:              [59.412] ms
Wrote image file mandelbrot-ispc.ppm
                                (4.74x speedup from ISPC)
```
```
foreach (j = 0 ... height, i = 0 ... width) {
    ...
}
```
理论上来说，使用AVX2指令，并且按照像素级别并行，应该有8x的性能提升    
- mandelbrot每个像素计算独立

但是，ispc实际上还是把相邻的8个标量组成一个向量，这时候，并不能完全达到并行Task计算量的平均分布
- 对于单核SIMD，应该把数据按复杂度Gather到一起，保证一个向量内到水桶效应不明显

# Part 2
```
[mandelbrot serial]:            [281.824] ms
Wrote image file mandelbrot-serial.ppm
[mandelbrot ispc]:              [59.768] ms
Wrote image file mandelbrot-ispc.ppm
[mandelbrot multicore ispc]:    [30.024] ms
Wrote image file mandelbrot-task-ispc.ppm
                                (4.72x speedup from ISPC)
                                (9.39x speedup from task ISPC)
```
符合预期


