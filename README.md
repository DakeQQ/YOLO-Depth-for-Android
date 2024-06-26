# YOLO-Depth-Drivable Estimation-for-Android
1. Running YOLO series, monocular depth (distance) and drivable area estimation on Android devices.
2. Demo models have been uploaded to Google Drive: https://drive.google.com/drive/folders/1MPUvCQCNkjBiHtMjx-eTywetKbkTK7VA?usp=sharing
3. After downloading, please place the model files in the assets folder.
4. Remember to unzip the *.so compressed files stored in the libs/arm64-v8a folder.
5. Demo models include YOLO: v8, v9, v10, NAS series / Depth Estimation: Depth Anything-Small / Drivable Area: TwinLiteNet. They are converted from HuggingFace or ModelScope or Github and optimized for peak execution speed.
6. Hence, the input/output of the demo models slightly differs from the original models.
7. Demo models are set to accept h720*w1280 image input; hence, they work when the phone is held horizontally.
8. For better compatibility with ONNX Runtime-Android, export does not utilize dynamic-axes. Therefore, the exported ONNX model may not be optimal for x86_64.
9. This project uses OpenGL ES 3.x to directly capture camera frame textures and pass them to the GPU, minimizing image data copying and transfer. It uses GPU compute shaders to convert YUV to RGB and normalize before feeding to the model, minimizing CPU usage.
10. Dual-buffering and asynchronous inference are used to boost FPS. (Using previous camera frame data for inference, not waiting for current image processing)
11. Finally, YOLO bounding boxes are efficiently rendered using the OpenGL ES.
12. If using YOLO-v10 series, replace the original GLRender.java with the one in the "YOLO_Depth_Drivable/v10" folder.
13. The FPS of YOLO-v10 series is lower than the v8 series, with the specific reason yet to be determined.
14. Enabling both YOLO and depth estimation simultaneously drops FPS by about 30% (compared to YOLO-only tasks).
15. Estimation accuracy at the focus point (central area) is higher due to the influence of the current camera focal length. It is advised against estimating distances that are either too close or too distant. Indoor scenes are more accurate than outdoor ones.
16. Consider the higher-res Model_Depth_h294_w518.ort for increased accuracy, though it runs at 1/5 the speed. Don't forget to adjust width and height in GLRender.java and project.h.
17. Based on depth model principles, precision is lower for smooth, luminescent objects, scenes without light and shadow changes, and image edges. For more details, refer to papers on monocular depth estimation.
18. TwinLiteNet's lane detection accuracy is low, with the cause currently unclear.
19. Drivable area detection is less accurate due to water, reflections, shadows, and low light.
20. For high-resolution screens, use GL_LINE_STRIP to draw the drivable area (the settings at GLRender.java - lines 345 & 350), or you won't see anything.
21. We will make the model exported method public later.
22. See more about the project: https://dakeqq.github.io/overview/
# 安卓本地运行YOLO+深度(距离)+可行驶区域估计
1. 在Android设备上运行YOLO系列, 单目深度(距离), 可行驶区域估计。
2. 演示模型已上传至云端硬盘：https://drive.google.com/drive/folders/1MPUvCQCNkjBiHtMjx-eTywetKbkTK7VA?usp=sharing
3. 百度云盘: https://pan.baidu.com/s/1WzRPiV9EL_ijpkgCJaZRTg?pwd=dake 提取码: dake。
4. 下载后，请将模型文件放入assets文件夹。
5. 记得解压存放在libs/arm64-v8a文件夹中的*.so压缩文件。
6. 演示模型是YOLO: v8, v9, v10, NAS 系列 / 深度(距离)估计: Depth Anything-Small / 可行驶区域: TwinLiteNet。 它们是从HuggingFace或ModelScope或Github转换来的，并经过代码优化，以实现极致执行速度。
7. 因此，演示模型的输入输出与原始模型略有不同。
8. 演示模型导出设定为接收h720*w1280的图象输入，因此"横置手机"才能使用。
9. 为了更好的适配ONNXRuntime-Android，导出时未使用dynamic-axes. 因此导出的ONNX模型对x86_64而言不一定是最优解。
10. 本项目使用OpenGL ES 3.x，直接获取相机帧纹理后传递给GPU，尽可能减少图象数据的复制与传输. 再利用GPU计算着色器将YUV转成RGB并完成归一化后传递给模型, 尽量降低CPU占用。
11. 采用双重缓冲+异步推理來提升FPS。 (推理时使用前一刻相机帧数据，不等待当前的图像处理)
12. 最后使用OpenGL ES来高效的渲染YOLO框线。
13. 若使用YOLO-v10系列, 请将"YOLO_Depth_Drivable/v10"文件夹里的GLRender.java替换原文件。
14. YOLO-v10系列的FPS比v8系列还低，具体原因不明。
15. 同时启用YOLO与距离估计，FPS会下降约30%。(与单YOLO任务时相比)
16. 受到当前相机焦距影响，因此对焦位置 (中心区域) 的估计精度较高, 太近或太远的都不准。室内场景比室外准。
17. 您也可以考虑使用Model_Depth_h294_w518.ort更高分辨率的模型，它会准一些，但推理速度只有1/5. 记得在GLRender.java与project.h中修改对应的width与height数值.
18. 根据深度模型原理，光滑物体，发光物体，无光线阴影变化场景，画面边缘等等的精度不高，详细请参阅单目深度估计的相关论文.
19. TwinLiteNet的车道线估计准确率不高，原因暂时不明。
20. 可行驶区域检测会受到积水或光滑地面的反光影响，阴影交界处, 昏暗地区等等的精确度也较低。
21. 对于高屏幕分辨率的手机，请改使用GL_LINE_STRIP来绘制可行驶区域（GLRender.java-第345 & 350行）, 否則你啥也看不見。
22. 我们未来会提供转换导出模型的方法。
23. 看更多項目: https://dakeqq.github.io/overview/
# YOLO - 性能 Performance
| OS | Device | Backend | Model | FPS<br>Camera: h720*w1280 |
|:-------:|:-------:|:-------:|:-------:|:-------:|
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v10-m<br>q8f32 | 9.5 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v10-s<br>q8f32 | 17.5 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v10-n<br>q8f32 | 35 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v9-C<br>q8f32 | 6.5 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v8-s<br>q8f32 | 21 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | v8-n<br>q8f32 | 43 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | NAS-m<br>q8f32 | 9 |
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | NAS-s<br>q8f32 | 19 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v10-m<br>q8f32 | 5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v10-s<br>q8f32 | 9.5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v10-n<br>q8f32 | 18.5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v9-C<br>q8f32 | 3.5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v8-s<br>q8f32 | 10.5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | v8-n<br>q8f32 | 22 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | NAS-m<br>q8f32 | 5 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | NAS-s<br>q8f32 | 9.5 |

# Depth - 性能 Performance
| OS | Device | Backend | Model | FPS<br>Camera: h720*w1280 |
|:-------:|:-------:|:-------:|:-------:|:-------:|
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | Depth Anything-Small<br>q8f32 | 22 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | Depth Anything-Small<br>q8f32 | 11 |

# YOLO+Depth - 性能 Performance
| OS | Device | Backend | Model | YOLO FPS<br>Camera: h720*w1280 | Depth FPS<br>Camera: h720*w1280 |
|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | YOLOv8-n & <br>Depth Anything-Small<br>q8f32 | 28 | 16.7 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | YOLOv8-n & <br>Depth Anything-Small<br>q8f32 | 16 | 7.7 |

# Drivable Area - 性能 Performance
| OS | Device | Backend | Model | FPS<br>Camera: h720*w1280 |
|:-------:|:-------:|:-------:|:-------:|:-------:|
| Android 13 | Nubia Z50 | 8_Gen2-CPU<br>(X3+A715) | TwinLiteNet<br>q8f32 | 56 |
| Harmony 4 | P40 | Kirin_990_5G-CPU<br>(2*A76) | TwinLiteNet<br>q8f32 | 28 |

# 演示结果 Demo Results
(YOLOv8-n & Depth Anything-Small)<br>
<br>
![Demo Animation](https://github.com/DakeQQ/YOLO-Depth-Estimation-for-Android/blob/main/yolo_depth.gif?raw=true?raw=true)
<br>
<br>
(YOLOv8-s & Depth Anything-Small-h294_w518)<br>
<br>
![Demo Animation](https://github.com/DakeQQ/YOLO-Depth-Estimation-for-Android/blob/main/yolo_depth2.gif?raw=true?raw=true)
<br>
<br>
(TwinLiteNet)<br>
<br>
![Demo Animation](https://github.com/DakeQQ/YOLO-Depth-Estimation-for-Android/blob/main/drivable.gif?raw=true?raw=true)



