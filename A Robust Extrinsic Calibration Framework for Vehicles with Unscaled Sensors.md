# A Robust Extrinsic Calibration Framework for Vehicles with Unscaled Sensors
## URL  
- [http://personal.ee.surrey.ac.uk/Personal/R.Bowden/publications/2019/Walters_IROS2019pp.pdf](http://personal.ee.surrey.ac.uk/Personal/R.Bowden/publications/2019/Walters_IROS2019pp.pdf)
- [https://mp.weixin.qq.com/s/rf9AJEMwuSm0B28PjgRDQg](https://mp.weixin.qq.com/s/rf9AJEMwuSm0B28PjgRDQg)

## Abstract
第一个贡献是在手眼标定的基础上增加了尺度恢复，第二个贡献是收集独立的位姿信息并自动选择理想的有效信息，并对于运动退化提出了鲁棒性的改进。

## Introduction
- Automatic calibration常见的三个问题，1. 预先需要知道尺度，2. 运动退化，3. 偏移敏感；本文的针对的策略分别是，1. 采用包含尺度因子s的相似矩阵，是否有尺度的传感器均可通用，建议有尺度时fix s可减小误差，2. 框架在可观测时更新对应参数，不需要专门的干预，3. 框架降低了由于低可观性和噪声引起的漂移。(感觉这些都没有说得太清楚)
- Rotation-then-translation会导致relationship分离，但并没有实质性的缺点证明。
- 尺度因子的问题，[4] encode scale as the norm of the dual quaternion，rotation, scale and translation are obtained separately in evaluation. Heller et al. use second-order cone programming to revocer the scaled translation component. Again the rotation is separately calculated. (这些方法都不了解) 本文希望实现这些估计 in the same operation in real-time。(这种想法显而易见，只是这样误差会不会更大呢，以及如何解决)

## Methodology
### Extrinsic Calibration
手眼标定的常用公式：
$$AX = XB$$
其中，
$$A, B=\left[\begin{array}{cc}{R} & {t} \\ {0^{T}} & {1}\end{array}\right], \quad X=s\left[\begin{array}{cc}{R} & {t} \\ {0^{T}} & {1 / s}\end{array}\right]$$
这里的$X$即是相似矩阵，包含scale。
对应cost function：
$$h\left(\boldsymbol{X} | \zeta_{A}, \zeta_{B}\right)=\sum_{\tau}\left\|\boldsymbol{A}_{\tau} \boldsymbol{X}-\boldsymbol{X} \boldsymbol{B}_{\tau}\right\|$$
### Robust Estimation Pipeline
真实场景中的数据存在噪声和漂移，且在优化过程中不会调整输入的位姿，这需要BA和IMU bias估计等技术。更长的优化时间窗口，可以使得6个自由度更可能的客观，但长时间又带来了更大的漂移误差，这里提到了一些策略。
- 通过阈值去除各自cost较大的信息，并用RANSAC去除平移和旋转的outlier。
- 在cost func中引入regularizer term：
$$\boldsymbol{X}_{\text {final }}=\arg \min _{\boldsymbol{X}}\left(h\left(\boldsymbol{X} | \hat{\zeta}_{A}, \hat{\zeta}_{B}\right)+\alpha \omega\right)$$
文中$\alpha$取值0.1，$\omega$是指外参估计和手动测量的欧式距离。

## Results
设计了几个实验，不同baseline对于精度的影响；两个固定stereo camera+ARTag精度验证，基于Turtlebot的轨迹验证，基于KITTI的轨迹验证。
## Conclusion
整篇文章内容较少，motivation确实是大家想要的，从结果来看感觉一般，一方面说明了方法有待改进，另一方面也可能说明这种大而干净的理想标定思路还有待商榷。