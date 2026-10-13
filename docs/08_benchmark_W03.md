# 现有做法调研 — W03 需求规格（G2）

> 调研至少 2 种现有做法，记录来源与限制；作为需求与选型的依据。

## 做法 1：人工巡查
- **方式**：管理人员定时/不定期到场巡查，人工判断车位占用并记录或播报；或依赖出入口监控人工比对。
- **优点**：零设备成本、直观、可处理异常。
- **缺点**：人力成本高、时效差、无法实时/高频更新、高峰人手不足；对车位级实时状态覆盖有限（来源：西安建筑科技大学《智能停车场管理系统技术方案》，其采用探测器替代人工巡查）。
- **来源**：https://mse.xauat.edu.cn/info/1036/1859.htm

## 做法 2：摄像头 / 视频识别
- **方式**：摄像头拍摄车位区域，视觉算法识别车辆占用（常含车牌/身份信息，用于反向寻车等）。
- **优点**：覆盖广、可同时检测多车位、自动化程度高、可复用已有摄像头。
- **缺点**：涉及**车辆身份/隐私**采集，合规与隐私风险高；依赖部署位、光照、遮挡；算力需求较高。
- **来源**：中国联通全视频车位引导系统 https://gec.10010.com/p/solution/1892 ；Ultralytics YOLO 停车管理 https://docs.ultralytics.com/guides/parking-management

## 做法 3：传感器方案（本项目互补校验）
- **地磁检测**：车辆金属使局部磁场畸变，判断占用；设环境磁场阈值，空闲变化小、停车变化大；灵敏度可配置（来源：Global Hi-Tech https://globalhi-tech.sg/wireless-parking-guidance-system/ ；上海门漾）。
- **超声波检测**：车位正上方探测，实时采集车辆信息，指示灯绿/红（来源：西安建筑科技大学）。
- **双模（地磁＋超声波/雷达）**：户外全天气高精度，双模检测精度可达约99%（来源：ChinaIoTDevices https://www.chinaiotdevices.com/iotdevices/ultrasonic-and-geomagnetic-parking-occupation-detector-with-lorawan/ ；ZOJE https://www.zoje-parking.com/knowledge/wireless-dual-mode-geomagnetic-parking-detector-for-easy-installation）。
- **优点**：隐私友好、低功耗、可单车位部署、成本低；**缺点**：单一传感器易受环境干扰（地磁漂移、超声波遮挡），故作为本项目**互补校验**。

## 主路线：YOLO 视觉占用检测（已确定）
- 基于视频流用 YOLO 检测"car"类目标，并按车位区域（IoU/最近车位匹配）判断占用/空闲（来源：arXiv、ScholaRepos、AUA）。
- 优点：覆盖直观、可多车位、可配置**不识别身份**；缺点：算力需求高、依赖摄像头与部署位/光照。
- 来源：Ultralytics https://docs.ultralytics.com/guides/parking-management ；arXiv 基于 YOLOv11 的边缘停车架构 https://arxiv.org/html/2602.01754v1

## 结论
- 结合隐私、成本、部署与校园场景，本项目**最终采用 YOLO 视觉占用检测（配置为不识别车辆身份）＋STM32 主控**，经 Wi-Fi 模块接入 MQTT → SQLite → Web Dashboard；地磁/超声波作为低成本互补校验。
- 变更经团队＋教师评审确认，记入 Change Log（CH03）。
