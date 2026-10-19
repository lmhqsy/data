# UAV RTK/LIO 定位实验数据 · UAV RTK/LIO localization recordings

本仓库包含论文中 **5 个自采场景、每个场景 3 次、共 15 次** 无人机飞行的原始 ROS1 bag。
配套论文研究的是黑盒 LiDAR 惯性里程计（LIO）之后的位置修正层：
*Reachability-Coupled Maximum-Consensus Correction (RCMC)*。

This repository hosts the **15 raw ROS1 bag recordings** (5 scenes × 3 repetitions)
used in our paper on a position-correction layer behind a black-box LIO estimator.

- 记录总量 / size: **162.3 MB**
- 采集日期 / capture date: 未在数据中保留（bag 内部时间戳以 1970 起始的录制时钟为准）
- 数据所有者 / owner: 见仓库提交者 (lmhqsy)

---

## 1. 目录结构 / Layout

```
bags/
├── scene1_building_entrance/      场景 1 · 建筑入口
│   ├── scene1_run1.bag
│   ├── scene1_run2.bag
│   └── scene1_run3.bag
├── scene2_arcade_corridor/        场景 2 · 半遮挡连廊
├── scene3_tree_lined_path/        场景 3 · 林荫通道
├── scene4_building_loop/          场景 4 · 绕楼大环线
└── scene5_underground_garage/     场景 5 · 地下车库进出
```

命名规则 / naming: `scene<场景号>_run<第几次>.bag`。
`run1`–`run3` 是**同一场景下的三次独立重复飞行**，不是同一架次的分段。

`MANIFEST.sha256` 与 `MANIFEST.json` 给出每个文件的 SHA-256 与字节数。

---

## 2. 记录清单 / Recordings

| 记录 | 文件 | 时长 s | LIO 历元 | GNSS 样本 | IMU 样本 | 大小 MB |
|---|---|---:|---:|---:|---:|---:|
| scene1_run1 | `scene1_building_entrance/scene1_run1.bag` | 222.6 | 2225 | 1113 | 11128 | 10.7 |
| scene1_run2 | `scene1_building_entrance/scene1_run2.bag` | 169.9 | 1699 | 850 | 8496 | 8.2 |
| scene1_run3 | `scene1_building_entrance/scene1_run3.bag` | 289.2 | 2892 | 1445 | 14458 | 13.9 |
| scene2_run1 | `scene2_arcade_corridor/scene2_run1.bag` | 131.2 | 1311 | 656 | 6558 | 6.3 |
| scene2_run2 | `scene2_arcade_corridor/scene2_run2.bag` | 124.5 | 1244 | 623 | 6222 | 6.0 |
| scene2_run3 | `scene2_arcade_corridor/scene2_run3.bag` | 143.2 | 1432 | 716 | 7159 | 6.9 |
| scene3_run1 | `scene3_tree_lined_path/scene3_run1.bag` | 151.8 | 1519 | 759 | 7592 | 7.3 |
| scene3_run2 | `scene3_tree_lined_path/scene3_run2.bag` | 149.3 | 1493 | 747 | 7467 | 7.2 |
| scene3_run3 | `scene3_tree_lined_path/scene3_run3.bag` | 178.3 | 1783 | 892 | 8915 | 8.6 |
| scene4_run1 | `scene4_building_loop/scene4_run1.bag` | 153.4 | 1499 | 766 | 7667 | 7.4 |
| scene4_run2 | `scene4_building_loop/scene4_run2.bag` | 152.7 | 1527 | 763 | 7635 | 7.4 |
| scene4_run3 | `scene4_building_loop/scene4_run3.bag` | 261.9 | 2619 | 1310 | 13094 | 12.6 |
| scene5_run1 | `scene5_underground_garage/scene5_run1.bag` | 608.6 | 6076 | 3043 | 30428 | 29.3 |
| scene5_run2 | `scene5_underground_garage/scene5_run2.bag` | 308.6 | 2739 | 1543 | 15428 | 14.6 |
| scene5_run3 | `scene5_underground_garage/scene5_run3.bag` | 326.8 | 3219 | 1634 | 16340 | 15.7 |

- LIO：`/Odometry`，约 10 Hz
- GNSS：`/mavros/gpsstatus/gps1/raw`，约 5 Hz
- IMU：`/mavros/imu/data`，约 50 Hz

---

## 3. 话题 / Topics

| 话题 | 内容 |
|---|---|
| `/Odometry` | 直接 LiDAR 里程计输出的位姿与协方差（本工作把它当作黑盒 LIO） |
| `/mavros/gpsstatus/gps1/raw` | 接收机原始定位输出：经纬度、椭球高、fix 类型、精度估计 |
| `/mavros/imu/data` | 飞控 IMU，仅用于输入健康检查 |

---

## 4. 重要说明 / Important notes

1. **没有独立位置真值。** 这批数据不含测绘检查点或全站仪参考；RTK 是算法的**输入**，
   不是真值。因此这些记录**不能**用来计算 ATE / RPE / 定位 RMSE。
   *There is no independent position ground truth. RTK is an input, not truth; these
   recordings cannot be used to compute ATE/RPE/localization RMSE.*
2. **`scene5_run1`–`run3` 的接收机长时间失效**（进地下车库），其中 `run2`、`run3` 的
   直接 LIO 流存在轨迹尺度的数值异常（相邻历元位移达数百米）。这些是**输入流失效**，
   不是真实飞行轨迹，使用时请显式剔除或作为失效样本处理。
3. **`scene4_run2`** 在 120 s 准备段之后的接收机合格样本极少，相应结果不可用于性能比较。
4. 时间戳为录制时钟；不同记录之间不可直接对齐。

---

## 5. 隐私 / Privacy

**本仓库包含接收机原始经纬度与绝对时间戳**，以及飞行器周围环境的信息。
上传者已确认公开这些数据。若你要复用，请遵守所在单位的相关规定。
*This repository contains raw GNSS latitude/longitude and absolute timestamps.
The owner has confirmed public release; reuse is subject to your institution's rules.*

---

## 6. 引用 / Citation

若使用本数据，请引用配套论文（投稿中，待补）：

```bibtex
@article{rcmc2026,
  title   = {Reachability-Coupled Maximum-Consensus Correction for Black-Box LiDAR--Inertial Odometry},
  author  = {Zhu, Jun-Wei and Li, Meng-Hui and Che, Wei-Wei and Wang, Xin},
  journal = {IEEE Transactions on Instrumentation and Measurement},
  year    = {2026},
  note    = {under review}
}
```

## 7. 许可 / License

数据以 **CC BY 4.0** 发布（见 `LICENSE`）。使用时请保留出处。
*Released under CC BY 4.0; please keep attribution.*

## 8. 联系 / Contact

问题或合作请开 issue，或联系仓库所有者。
