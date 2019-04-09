### Evaluation of EPI distortion correction methods for quantitative MRI of the brain at high magnetic field

[**paper**](https://www.sciencedirect.com/science/article/pii/S0730725X15001551?via%3Dihub)

#### Abstract
> High field MRI has been applied to high-resolution structural and functional imaging of the brain. Echo planar imaging (EPI) is an ultrafast acquisition technique widely used in diffusion imaging, functional MRI and perfusion imaging. However, it suffers from geometric and intensity distortions caused by static magnetic field inhomogeneity, which is worse at higher field strengths. Such susceptibility artifacts are particularly severe in relation to the small size of the mouse brain. In this study we compared different distortion correction methods, including nonlinear registration, field map-based, and reversed phaseencoding-based approaches, on quantitative imaging of T1 and perfusion in the mouse brain acquired by spin-echo EPI with inversion recovery and pseudo-continuous arterial spin labeling, respectively, at 7 T. Our results showed that the 3D reversed phase-encoding correction outperformed other methods in terms of geometric fidelity, and that conventional field map-based correction could be improved by combination with affine transformation to reduce the bias in the field map. Both methods improved quantification with smaller fitting error and regional variation. These approaches offer robust correction of EPI distortions at high field strengths and hence could lead to more accurate co-registration and quantification of imaging biomarkers in both clinical and preclinical applications

高场强MRI已被应用于脑的高分辨率结构和功能成像。回波平面成像(EPI)是一种广泛应用于扩散成像、功能磁共振成像和灌注成像的超快采集技术。但静磁场非均匀性引起的几何畸变和强度畸变(intensity distortions)在磁场强度较大时更为严重。这种易感伪影与小鼠大脑的小尺寸有关，尤其严重。在本研究中，我们比较了不同的畸变校正方法，包括**非线性配准、基于场图和基于反相编码**的方法，分别在7t时对自旋回波EPI获得的T1和灌注的定量成像，以及倒置恢复和伪连续动脉自旋标记。结果表明，三维反相编码校正在几何保真度方面优于其他方法，**传统的基于场图的校正方法可以结合仿射变换进行改进，以减小场图中的偏置**。两种方法均提高了量化效果，拟合误差小，区域变化小。这些方法在高场强下提供了对EPI畸变的鲁棒校正，因此可以在临床和临床前应用中更准确地联合注册和定量成像生物标志物。

**这篇文章主要评估了在high magnetic field下几种distortion corretion的方法，这里我主要关注了一下常用的一些算法和原理，并不对结果做过多讨论**

#### Introduction
提出了几种EPI失真校正算法，并在较低场的人体实验中得到了成功的验证。最常见的方法是基于场图的方法。

**基于场图的方法：what is field map?**

- 场图由两幅TE不同的梯度回波图像的相位差测量得到，描述了磁场的空间变化，可以从场图的数据中计算出voxel的位移图并且用于校正扭曲图像。

- 其**主要缺点**是这项技术需要分开采集回波图像，个体的移动可能会使得场图的数据不够准确。另外，相位unwrpping的过程对噪声非常敏感，所以基于场图的方法在较低信噪比的区域就会变得不可信。

**基于配准(registration)的方法**

- 几何失真可以通过在解剖图像上对扭曲影像进行非线性的配准来校正。扭曲域(deformation field)可以通过最小化扭曲的EPI影像与解剖像之间的 <strong>squared differences of intensity or log-intensity</strong> 计算得到

- 但是基于配准的校正方法非常依赖于配准算法的实现和参数的优化
- 此外，它只对几何畸变进行校正，而对强度畸变不进行校正，仍可能导致量化误差

**基于相位编码方向相反的数据的方法**
> The basic idea is that phase-encoding gradients with opposite polarities will produce opposite spatial and intensity distortions in the phase-encoding direction.
>
> 其基本思想是极性相反的相位编码梯度在相位编码方向上会产生相反的空间和强度畸变。
> Therefore an anatomically correct image should be the one with minimal difference between the pair of distorted images
>
>因此，解剖学上正确的图像应该是两幅扭曲图像之间差异最小的图像。

- 这种方法的优点之一是成像时间短，对于完整的EPI volume，成像时间为秒，而对于传统的场图采集，成像时间为分钟，因此对受试者的运动不那么敏感

- 然而，由于算法是在单一维度上展开的，因此得到的图像可能在其他两个维度上显示不连续；
  - 一种改进的实现方法是生成连续光滑的三维位移场
  - Further improvement uses computationally more efficient method, achieving subvoxel resolution of the distortion map.
  进一步改进采用计算效率更高的方法，实现了畸变图的亚体素分辨率

#### Material and method
> 这里就简要介绍一下采用了什么样的矫正方法

首先将T2w的解剖像刚体配准到场图上以在EPI空间中创建一张几何以及解剖上正确的精标准影像，五个校正方法被应用在了EPI数据上：
1. 使用FSL中的FNIRT工具将EPI影像非线性配准到精标准FSE数据上，损失函数采用二次方差函数
2. 采用FSL FUGUE对EPI数据进行校正
3. 重复第2种方法，然后用3D仿射变换将校正后的影像配准到精标准影像上，损失函数采用correlation ratio
4. 采用MATLAB内置函数进行一维相位编码的校正
5. 使用FSL TOPUP进行三维反向相位编码校正

#### Result
1. 第5种方法表现最好
2. 利用fugue工具校正影像后，再对其进行仿射变换配准可以有效改善结果