# 港口智能眼镜态势感知平台Demo技术规格书
**项目代号**：Port-Demo-Spec
**版本**：v2.0
**文档状态**：详细设计规格

---

## 一、项目概述与目标

### 1.1 Demo定位
**核心目标**：构建可交互的技术演示系统，验证空间计算概念在港口指挥场景中的技术可行性和实用价值。

**演示价值**：
- **技术验证**：验证时空锥算法、空间导航、手势识别等核心技术
- **概念展示**：向港口管理局、船运公司、技术投资人展示未来港口指挥形态
- **用户测试**：收集真实港口工作人员的使用反馈，优化交互设计
- **标准制定**：为后续产品开发建立技术规范和性能基准

### 1.2 场景设定
**模拟港口**：虚拟的"未来港"（FuturePort）
- **地理位置**：亚热带深水港，年吞吐量2000万TEU
- **基础设施**：12个集装箱泊位、4个散货泊位、2个液体化工泊位
- **交通状况**：日均船舶靠泊80艘，集卡流量5000辆次
- **气象条件**：台风季、季风、海雾等复杂气象挑战

---

## 二、系统架构设计

### 2.1 总体架构
```
┌─────────────────────────────────────────────────────────────┐
│                    用户界面层 (Presentation Layer)          │
├─────────────────────────────────────────────────────────────┤
│  战略沙盘    │  区域聚焦    │  实体交互    │  一线代理     │
│  (Unity 3D)  │  (UE5)      │  (WebXR)     │  (Video+)     │
├─────────────────────────────────────────────────────────────┤
│                    核心算法层 (Core Algorithm Layer)        │
├─────────────────────────────────────────────────────────────┤
│  时空锥引擎  │  数据融合    │  预测模型    │  决策支持     │
│  (Python)    │  (C++)      │  (PyTorch)   │  (Rule+ML)    │
├─────────────────────────────────────────────────────────────┤
│                    数据接口层 (Data Interface Layer)        │
├─────────────────────────────────────────────────────────────┤
│  港口API     │  传感器数据  │  外部系统    │  仿真数据     │
│  (REST)      │  (IoT)      │  (GIS)       │  (Simulator)  │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 技术选型与理由

**引擎选择策略**：
- **Unity 3D**：战略沙盘层，跨平台性好，适合快速原型开发
- **Unreal Engine 5**：区域聚焦层，卓越的渲染质量，适合展示性场景
- **WebXR**：实体交互层，无需安装，便于演示和分享
- **原生Video**：一线代理层，确保最低的延迟和最高的稳定性

**后端技术栈**：
```javascript
const backendStack = {
  // 核心服务
  apiGateway: "Node.js + Express",
  realTimeData: "Socket.io",
  database: "MongoDB + Redis",
  messageQueue: "RabbitMQ",

  // AI/ML服务
  predictionModel: "Python + PyTorch",
  computerVision: "OpenCV + TensorFlow",
  dataFusion: "C++ (高性能计算)",

  // 外部集成
  portAPI: "RESTful Adapter Pattern",
  sensorHub: "MQTT Protocol",
  weatherService: "OpenWeather API",
  trafficAPI: "Google Maps API"
};
```

---

## 三、核心功能模块详细设计

### 3.1 时空锥可视化引擎

#### 3.1.1 算法核心
```python
class SpacetimeConeEngine:
    """时空锥可视化引擎"""

    def __init__(self, config):
        self.time_horizon = config.get('time_horizon', 24)  # 小时
        self.possibility_layers = config.get('layers', 5)
        self.decision_points = []
        self.visualization_meshes = []

    def calculate_possibilities(self, current_state, decision_options):
        """计算未来可能性"""
        cone_base = self.create_cone_base(current_state)

        for i, option in enumerate(decision_options):
            # 蒙特卡罗模拟
            simulations = self.run_monte_carlo_simulation(option, 1000)

            # 生成视觉帷幕
            curtain = self.generate_visual_curtain(simulations, i)

            # 计算概率权重
            probability = self.calculate_probability(simulations)

            self.add_possibility_layer(curtain, probability, option)

        return self.build_spacetime_cone()

    def generate_visual_curtain(self, simulations, layer_index):
        """生成半透明视觉帷幕"""
        # 基于模拟结果生成点云
        point_cloud = self.simulations_to_pointcloud(simulations)

        # 创建半透明材质
        material = self.create_translucent_material(
            opacity=0.3 + layer_index * 0.1,
            color=self.get_layer_color(layer_index)
        )

        # 生成网格
        mesh = self.create_curtain_mesh(point_cloud, material)

        return mesh
```

#### 3.1.2 性能指标
```
响应时间：≤500ms（从决策输入到可视化完成）
模拟精度：95%置信区间
可视化帧率：≥60fps
内存占用：≤2GB
并发处理：支持10个决策场景同时计算
```

### 3.2 智能手势识别系统

#### 3.2.1 手势定义库
```javascript
const GestureLibrary = {
  // 基础导航手势
  Navigation: {
    PUSH: {
      description: "向前推手，放大视图",
      handShape: "手掌张开，手指并拢",
      movement: "从胸前向前推，距离>20cm",
      confidence: 0.85
    },
    PULL: {
      description: "向后拉手，缩小视图",
      handShape: "手掌张开，手指并拢",
      movement: "从胸前向后拉，距离>20cm",
      confidence: 0.85
    },
    ROTATE: {
      description: "旋转手，改变视角",
      handShape: "捏合手势，拇指食指相触",
      movement: "以手腕为轴旋转，角度>30°",
      confidence: 0.80
    }
  },

  // 时空锥交互手势
  SpacetimeInteraction: {
    SELECT_CURTAIN: {
      description: "抓取帷幕",
      handShape: "抓握手势，手指弯曲",
      movement: "快速闭合，停留>500ms",
      confidence: 0.90
    },
    PORTAL_DRAW: {
      description: "画圆开启传送门",
      handShape: "食指伸直，其他手指弯曲",
      movement: "画完整圆形，直径>15cm",
      confidence: 0.88
    },
    FLY_MODE: {
      description: "飞行模式激活",
      handShape: "手掌张开，手指分开",
      movement: "向上抬起，速度>10cm/s",
      confidence: 0.82
    }
  },

  // 专用控制手势
  SpecialControl: {
    RESET_VIEW: {
      description: "重置视图",
      handShape: "双手交叉，手掌向外",
      movement: "同时向外推开",
      confidence: 0.95
    },
    EMERGENCY_STOP: {
      description: "紧急停止",
      handShape: "单手立掌，五指并拢",
      movement: "快速向前推出，停顿",
      confidence: 0.98
    }
  }
};
```

#### 3.2.2 机器学习模型
```python
class GestureRecognitionModel:
    """基于MediaPipe和自定义CNN的手势识别"""

    def __init__(self):
        self.mediapipe_hands = mp.solutions.hands
        self.hands = self.mediapipe_hands.Hands(
            static_image_mode=False,
            max_num_hands=2,
            min_detection_confidence=0.7,
            min_tracking_confidence=0.5
        )

        # 自定义CNN模型
        self.gesture_model = self.build_cnn_model()
        self.sequence_length = 30  # 30帧序列
        self.frame_buffer = deque(maxlen=self.sequence_length)

    def build_cnn_model(self):
        """构建CNN+LSTM模型"""
        model = Sequential([
            # 输入层：手部21个关键点的3D坐标
            Input(shape=(30, 21, 3)),

            # CNN层：提取空间特征
            Conv2D(32, (3, 3), activation='relu'),
            MaxPooling2D((2, 2)),
            Conv2D(64, (3, 3), activation='relu'),
            MaxPooling2D((2, 2)),

            # LSTM层：提取时间特征
            Reshape((30, -1)),
            LSTM(128, return_sequences=True),
            LSTM(64),

            # 分类层
            Dense(64, activation='relu'),
            Dropout(0.5),
            Dense(len(GESTURE_CLASSES), activation='softmax')
        ])

        return model

    def recognize_gesture(self, hand_landmarks, frame_timestamp):
        """识别手势"""
        # 预处理关键点数据
        processed_landmarks = self.preprocess_landmarks(hand_landmarks)

        # 添加到帧缓冲区
        self.frame_buffer.append({
            'landmarks': processed_landmarks,
            'timestamp': frame_timestamp
        })

        # 需要足够的帧数
        if len(self.frame_buffer) < self.sequence_length:
            return None

        # 提取序列特征
        sequence = np.array([frame['landmarks'] for frame in self.frame_buffer])

        # 预测手势
        prediction = self.gesture_model.predict(sequence.reshape(1, *sequence.shape))
        gesture_class = np.argmax(prediction)
        confidence = prediction[0][gesture_class]

        # 置信度过滤
        if confidence < 0.8:
            return None

        return {
            'gesture': GESTURE_CLASSES[gesture_class],
            'confidence': confidence,
            'timestamp': frame_timestamp
        }
```

### 3.3 多层空间导航系统

#### 3.3.1 层级切换逻辑
```typescript
interface NavigationLayer {
  id: string;
  name: string;
  scale: number;        // 缩放比例
  detailLevel: number;  // 细节等级 1-4
  cameraSettings: CameraConfig;
  uiElements: UIComponent[];
  dataSources: DataSource[];
  transition: TransitionConfig;
}

class SpatialNavigationController {
  private layers: Map<string, NavigationLayer>;
  private currentLayer: string;
  private targetLayer: string;
  private isTransitioning: boolean = false;

  constructor() {
    this.initializeLayers();
    this.setupTransitionPipeline();
  }

  private initializeLayers(): void {
    // 第一层：战略沙盘
    this.layers.set('strategic', {
      id: 'strategic',
      name: '战略沙盘',
      scale: 0.1,  // 1:10000 比例
      detailLevel: 1,
      cameraSettings: {
        position: { x: 0, y: 1000, z: 0 }, // 上帝视角
        rotation: { x: 90, y: 0, z: 0 },
        fov: 60,
        farClip: 20000
      },
      transition: {
        duration: 2000,
        easing: 'easeInOutQuad',
        effects: ['fade', 'scale', 'rotate']
      }
    });

    // 第二层：区域聚焦
    this.layers.set('regional', {
      id: 'regional',
      name: '区域聚焦',
      scale: 0.5,  // 1:2000 比例
      detailLevel: 2,
      cameraSettings: {
        position: { x: 0, y: 500, z: 0 },
        rotation: { x: 45, y: 0, z: 0 },
        fov: 60,
        farClip: 5000
      },
      transition: {
        duration: 1500,
        easing: 'easeOutCubic',
        effects: ['slide', 'zoom']
      }
    });

    // 第三层：实体交互
    this.layers.set('entity', {
      id: 'entity',
      name: '实体交互',
      scale: 1.0,  // 1:1 比例
      detailLevel: 3,
      cameraSettings: {
        position: { x: 0, y: 50, z: 100 },
        rotation: { x: 15, y: 0, z: 0 },
        fov: 70,
        farClip: 1000
      },
      transition: {
        duration: 1000,
        easing: 'easeOutElastic',
        effects: ['morph', 'fade']
      }
    });

    // 第四层：一线代理
    this.layers.set('agent', {
      id: 'agent',
      name: '一线代理',
      scale: 2.0,  // 2:1 比例（放大）
      detailLevel: 4,
      cameraSettings: {
        position: { x: 0, y: 10, z: 20 }, // 第一人称视角
        rotation: { x: 0, y: 0, z: 0 },
        fov: 90,
        farClip: 100
      },
      transition: {
        duration: 800,
        easing: 'linear',
        effects: ['snap']
      }
    });
  }

  async transitionToLayer(targetLayerId: string, targetObject?: any): Promise<void> {
    if (this.isTransitioning || targetLayerId === this.currentLayer) {
      return;
    }

    this.isTransitioning = true;
    this.targetLayer = targetLayerId;

    const currentLayer = this.layers.get(this.currentLayer);
    const targetLayer = this.layers.get(targetLayerId);

    if (!currentLayer || !targetLayer) {
      throw new Error(`Layer not found: ${targetLayerId}`);
    }

    try {
      // 1. 准备过渡数据
      const transitionData = this.prepareTransitionData(currentLayer, targetLayer, targetObject);

      // 2. 执行预过渡效果
      await this.executePreTransitionEffects(transitionData);

      // 3. 切换数据层
      await this.switchDataLayer(targetLayer, targetObject);

      // 4. 执行过渡动画
      await this.executeTransitionAnimation(transitionData);

      // 5. 执行后过渡效果
      await this.executePostTransitionEffects(transitionData);

      // 6. 更新当前层
      this.currentLayer = targetLayerId;

    } finally {
      this.isTransitioning = false;
      this.targetLayer = '';
    }
  }
}
```

---

## 四、数据集成与仿真系统

### 4.1 多源数据融合

#### 4.1.1 数据源配置
```json
{
  "dataSources": {
    "aisSystem": {
      "type": "realTime",
      "protocol": "NMEA0183",
      "updateFrequency": 3,
      "endpoints": ["tcp://ais.futureport.com:9001"],
      "dataFormat": {
        "mmsi": "string",
        "latitude": "number",
        "longitude": "number",
        "speed": "number",
        "course": "number",
        "heading": "number",
        "timestamp": "datetime"
      }
    },

    "portManagement": {
      "type": "restApi",
      "protocol": "HTTPS",
      "updateFrequency": 60,
      "endpoints": {
        "berths": "https://api.futureport.com/v1/berths",
        "schedule": "https://api.futureport.com/v1/schedule",
        "cargo": "https://api.futureport.com/v1/cargo"
      },
      "authentication": "BearerToken"
    },

    "weatherService": {
      "type": "external",
      "provider": "OpenWeatherMap",
      "updateFrequency": 300,
      "parameters": ["windSpeed", "windDirection", "visibility", "precipitation"],
      "alertThresholds": {
        "windSpeed": 15.0,
        "visibility": 1000.0,
        "waveHeight": 2.5
      }
    },

    "simulationData": {
      "type": "synthetic",
      "scenarios": [
        "normalOperations",
        "stormApproach",
        "equipmentFailure",
        "medicalEmergency",
        "trafficCongestion"
      ],
      "realismLevel": "high",
      "validation": "expertReviewed"
    }
  }
}
```

#### 4.1.2 数据融合算法
```python
class PortDataFusionEngine:
    """港口数据融合引擎"""

    def __init__(self):
        self.data_sources = {}
        self.fusion_weights = {}
        self.uncertainty_model = UncertaintyCalculator()
        self.confidence_threshold = 0.7

    def fuse_vessel_data(self, timestamp, spatial_bounds):
        """融合船舶相关数据"""
        # 1. 收集多源数据
        ais_data = self.get_ais_data(timestamp, spatial_bounds)
        radar_data = self.get_radar_data(timestamp, spatial_bounds)
        visual_data = self.get_visual_observations(timestamp, spatial_bounds)

        # 2. 数据预处理
        processed_data = {
            'ais': self.preprocess_ais(ais_data),
            'radar': self.preprocess_radar(radar_data),
            'visual': self.preprocess_visual(visual_data)
        }

        # 3. 计算数据质量权重
        weights = self.calculate_fusion_weights(processed_data)

        # 4. 执行数据融合
        fused_tracks = self.execute_fusion(processed_data, weights)

        # 5. 不确定性评估
        uncertainty = self.assess_uncertainty(fused_tracks, weights)

        # 6. 生成可视化数据
        visualization_data = self.prepare_visualization(fused_tracks, uncertainty)

        return visualization_data

    def calculate_fusion_weights(self, data_sources):
        """动态计算融合权重"""
        weights = {}

        for source, data in data_sources.items():
            # 数据新鲜度
            freshness = self.calculate_freshness(data['timestamp'])

            # 数据完整性
            completeness = self.calculate_completeness(data)

            # 历史准确性
            accuracy = self.get_historical_accuracy(source)

            # 环境适应性
            adaptability = self.assess_environmental_adaptation(source)

            # 综合权重
            total_weight = (
                freshness * 0.3 +
                completeness * 0.25 +
                accuracy * 0.3 +
                adaptability * 0.15
            )

            weights[source] = max(0.1, min(1.0, total_weight))

        # 归一化权重
        total = sum(weights.values())
        return {k: v/total for k, v in weights.items()}
```

### 4.2 仿真数据生成器

#### 4.2.1 船舶交通仿真
```python
class PortTrafficSimulator:
    """港口交通仿真器"""

    def __init__(self, port_config):
        self.port_config = port_config
        self.vessel_templates = self.load_vessel_templates()
        self.schedule_generator = ScheduleGenerator(port_config)
        self.weather_simulator = WeatherSimulator()

    def generate_realistic_traffic(self, start_time, duration_hours):
        """生成真实的港口交通场景"""
        end_time = start_time + timedelta(hours=duration_hours)
        traffic_scenario = []

        # 1. 生成基础船期表
        base_schedule = self.schedule_generator.generate_schedule(
            start_time, duration_hours
        )

        # 2. 添加随机事件
        events = self.generate_random_events(start_time, duration_hours)

        # 3. 考虑天气影响
        weather_forecast = self.weather_simulator.simulate_weather(
            start_time, duration_hours
        )

        # 4. 生成每艘船的详细轨迹
        for vessel_schedule in base_schedule:
            # 应用天气影响
            adjusted_schedule = self.apply_weather_impact(
                vessel_schedule, weather_forecast
            )

            # 应用随机事件
            final_schedule = self.apply_events(adjusted_schedule, events)

            # 生成详细轨迹
            trajectory = self.generate_vessel_trajectory(final_schedule)

            traffic_scenario.append({
                'vessel': vessel_schedule['vessel'],
                'schedule': final_schedule,
                'trajectory': trajectory,
                'weather_impact': self.calculate_weather_impact(trajectory)
            })

        return traffic_scenario

    def generate_vessel_trajectory(self, schedule):
        """生成船舶详细轨迹"""
        waypoints = []

        # 进港轨迹
        approach_points = self.generate_approach_trajectory(
            schedule['entry_point'],
            schedule['pilot_station'],
            schedule['approach_time']
        )
        waypoints.extend(approach_points)

        # 引航轨迹
        pilot_points = self.generate_pilot_trajectory(
            schedule['pilot_station'],
            schedule['berth'],
            schedule['pilot_time']
        )
        waypoints.extend(pilot_points)

        # 靠泊轨迹
        berthing_points = self.generate_berthing_trajectory(
            schedule['berth'],
            schedule['berthing_time']
        )
        waypoints.extend(berthing_points)

        # 离港轨迹（如果时间允许）
        if schedule.get('departure_time'):
            departure_points = self.generate_departure_trajectory(
                schedule['berth'],
                schedule['exit_point'],
                schedule['departure_time']
            )
            waypoints.extend(departure_points)

        return {
            'waypoints': waypoints,
            'total_distance': self.calculate_total_distance(waypoints),
            'estimated_fuel_consumption': self.estimate_fuel_consumption(waypoints),
            'collision_risk_points': self.identify_risk_points(waypoints)
        }
```

#### 4.2.2 设备故障仿真
```python
class EquipmentFailureSimulator:
    """设备故障仿真器"""

    def __init__(self):
        self.failure_patterns = self.load_failure_patterns()
        self.repair_time_model = RepairTimeEstimator()
        self.cascade_effect_model = CascadeEffectAnalyzer()

    def simulate_crane_failure(self, crane_id, timestamp, weather_conditions):
        """仿真起重机故障"""
        failure_event = {
            'equipment_id': crane_id,
            'failure_type': self.select_failure_type('crane', weather_conditions),
            'severity': self.assess_failure_severity(),
            'detection_time': timestamp,
            'estimated_repair_time': self.repair_time_model.estimate('crane'),
            'impact_assessment': self.assess_operational_impact(crane_id),
            'cascade_risk': self.cascade_effect_model.analyze(crane_id, timestamp)
        }

        # 生成故障影响的可视化数据
        failure_event['visualization'] = {
            'affected_area': self.calculate_affected_zone(crane_id),
            'capacity_loss': self.calculate_capacity_impact(failure_event),
            'alternative_routes': self.suggest_alternatives(crane_id),
            'recovery_timeline': self.generate_recovery_timeline(failure_event)
        }

        return failure_event

    def generate_cascading_scenarios(self, initial_failure, simulation_duration):
        """生成连锁故障场景"""
        scenarios = []
        current_time = initial_failure['detection_time']
        active_failures = [initial_failure]

        while current_time < initial_failure['detection_time'] + simulation_duration:
            # 检查新的连锁故障
            new_failures = self.check_cascade_failures(active_failures, current_time)

            for failure in new_failures:
                # 计算连锁概率
                cascade_probability = self.calculate_cascade_probability(
                    failure, active_failures
                )

                if random.random() < cascade_probability:
                    failure['cascade_trigger'] = [
                        f['equipment_id'] for f in active_failures
                    ]
                    active_failures.append(failure)
                    scenarios.append({
                        'timestamp': current_time,
                        'failure': failure,
                        'cascade_chain': self.build_cascade_chain(active_failures)
                    })

            current_time += timedelta(minutes=15)

        return scenarios
```

---

## 五、用户界面详细设计

### 5.1 统一设计语言

#### 5.1.1 色彩系统
```css
:root {
  /* 主色调 - 科技感蓝紫色系 */
  --primary-deep: #0A0E27;        /* 深海蓝 */
  --primary-main: #1A237E;        /* 科技蓝 */
  --primary-light: #3949AB;       /* 明亮蓝 */
  --accent-cyan: #00BCD4;         /* 强调青 */
  --accent-orange: #FF6B35;       /* 警告橙 */

  /* 状态色彩 */
  --status-normal: #4CAF50;       /* 正常绿 */
  --status-warning: #FFC107;      /* 警告黄 */
  --status-danger: #F44336;       /* 危险红 */
  --status-info: #2196F3;         /* 信息蓝 */

  /* 数据可视化色彩 */
  --data-vessel: #00E5FF;         /* 船舶 - 青色 */
  --data-cargo: #FFEB3B;          /* 货物 - 黄色 */
  --data-berth: #4CAF50;          /* 泊位 - 绿色 */
  --data-weather: #9C27B0;        /* 天气 - 紫色 */
  --data-traffic: #FF9800;        /* 交通 - 橙色 */

  /* 透明度等级 */
  --opacity-light: 0.1;
  --opacity-medium: 0.3;
  --opacity-heavy: 0.6;
  --opacity-solid: 1.0;

  /* 时空锥专用色彩 */
  --cone-past: rgba(255, 255, 255, 0.3);
  --cone-present: rgba(0, 188, 212, 0.6);
  --cone-future: rgba(255, 255, 255, 0.2);
  --cone-selected: rgba(255, 107, 53, 0.8);
}
```

#### 5.1.2 字体与排版
```css
/* 字体层级系统 */
.typography {
  --font-primary: 'Roboto', 'Microsoft YaHei', sans-serif;
  --font-mono: 'Roboto Mono', 'Consolas', monospace;

  /* 标题层级 */
  --heading-1: 2.5rem;    /* 40px - 主标题 */
  --heading-2: 2.0rem;    /* 32px - 副标题 */
  --heading-3: 1.5rem;    /* 24px - 章节标题 */
  --heading-4: 1.25rem;   /* 20px - 小节标题 */

  /* 正文层级 */
  --body-large: 1.125rem; /* 18px - 大正文 */
  --body-regular: 1rem;   /* 16px - 标准正文 */
  --body-small: 0.875rem; /* 14px - 小正文 */
  --caption: 0.75rem;     /* 12px - 说明文字 */

  /* 字重 */
  --weight-light: 300;
  --weight-regular: 400;
  --weight-medium: 500;
  --weight-bold: 700;
}
```

### 5.2 分层界面设计

#### 5.2.1 战略沙盘层 (Unity 3D)
**核心功能**：
- 全港口3D鸟瞰视图
- 实时船舶位置与状态
- 天气系统可视化
- 宏观数据统计面板

**交互元素**：
```csharp
public class StrategicDashboard : MonoBehaviour {
    [Header("UI Elements")]
    public GameObject portOverviewPanel;
    public GameObject vesselStatusPanel;
    public GameObject weatherSystemPanel;
    public GameObject statisticsPanel;

    [Header("3D Elements")]
    public GameObject portModel;
    public GameObject vesselMarkers;
    public GameObject weatherEffects;
    public GameObject trafficFlow;

    // 时空锥可视化
    public void ShowSpacetimeCone(Vector3 position, DecisionData[] options) {
        GameObject cone = Instantiate(spacetimeConePrefab, position, Quaternion.identity);
        cone.GetComponent<SpacetimeConeController>().Setup(options);
    }

    // 手势交互处理
    public void HandleGesture(GestureData gesture) {
        switch(gesture.type) {
            case GestureType.PUSH:
                ZoomToRegion(gesture.targetPosition);
                break;
            case GestureType.SELECT_CURTAIN:
                SelectDecisionOption(gesture.targetOption);
                break;
        }
    }
}
```

#### 5.2.2 区域聚焦层 (Unreal Engine 5)
**核心功能**：
- 选定区域的精细3D模型
- 船舶靠泊过程动画
- 设备状态实时监控
- 区域性决策对比

**蓝图设计**：
```cpp
UCLASS()
class PORTDEMO_API ARegionalFocusController : public AActor
{
    GENERATED_BODY()

public:
    UPROPERTY(EditAnywhere, Category = "UI")
    TSubclassOf<class URegionalWidget> RegionalWidgetClass;

    UPROPERTY(EditAnywhere, Category = "3D Models")
    TArray<UBerth3DModel*> BerthModels;

    UPROPERTY(EditAnywhere, Category = "Animation")
    ULevelSequence* BerthingSequence;

    // 时空锥对比展示
    UFUNCTION(BlueprintCallable)
    void ShowDecisionComparison(FDecisionOption OptionA, FDecisionOption OptionB);

    // 船舶靠泊动画
    UFUNCTION(BlueprintCallable)
    void PlayBerthingAnimation(AVesselActor* Vessel, int32 BerthId);

    // 实时数据更新
    UFUNCTION(BlueprintCallable)
    void UpdateRealtimeData(FPortRegionalData Data);
};
```

#### 5.2.3 实体交互层 (WebXR)
**核心功能**：
- 高精度设备模型查看
- 360度环绕数据展示
- 维修指导AR叠加
- 操作手册空间化显示

**WebXR实现**：
```javascript
class EntityInteractionLayer {
    constructor() {
        this.scene = null;
        this.renderer = null;
        this.camera = null;
        this.controller = null;
        this.entityModel = null;
        this.dataRings = [];
    }

    async initializeWebXR() {
        // 初始化WebXR会话
        this.session = await navigator.xr.requestSession('immersive-ar', {
            requiredFeatures: ['hit-test', 'dom-overlay', 'hand-tracking'],
            domOverlay: { root: document.getElementById('webxr-overlay') }
        });

        // 设置渲染器
        this.renderer = new THREE.WebGLRenderer({
            alpha: true,
            preserveDrawingBuffer: true
        });

        // 创建XR场景
        this.scene = new THREE.Scene();
        this.camera = new THREE.PerspectiveCamera();

        // 初始化控制器
        this.controller = new XRController(this.session, this.renderer);
        this.controller.addEventListener('select', this.handleSelect.bind(this));
        this.controller.addEventListener('squeeze', this.handleSqueeze.bind(this));
    }

    loadEntityModel(entityId) {
        // 加载高精度3D模型
        const loader = new THREE.GLTFLoader();
        loader.load(`/models/equipment/${entityId}.glb`, (gltf) => {
            this.entityModel = gltf.scene;
            this.scene.add(this.entityModel);

            // 添加数据环
            this.createDataRings(entityId);

            // 添加交互热点
            this.createInteractionHotspots(entityId);
        });
    }

    createDataRings(entityId) {
        // 创建信息环系统
        const ringData = this.getEntityData(entityId);

        ringData.forEach((data, index) => {
            const ring = new DataRing(data, index * 0.5);
            this.dataRings.push(ring);
            this.scene.add(ring.mesh);
        });
    }

    handleSelect(event) {
        // 处理选择手势
        const intersection = this.raycast(event.inputSource);

        if (intersection.object.userData.type === 'hotspot') {
            this.showMaintenanceGuide(intersection.object.userData.guideId);
        } else if (intersection.object.userData.type === 'dataRing') {
            this.expandDataRing(intersection.object.userData.ringId);
        }
    }
}
```

#### 5.2.4 一线代理层 (原生视频)
**核心功能**：
- 实时视频流接收显示
- AR数据叠加渲染
- 指令下发与反馈
- 多视角切换

**视频处理管线**：
```cpp
class AgentVideoProcessor {
private:
    cv::VideoCapture capture;
    cv::Mat frameBuffer;
    std::mutex frameMutex;
    std::thread processingThread;
    bool isRunning;

    // AR叠加组件
    ARRenderer arRenderer;
    DataOverlayEngine overlayEngine;

public:
    bool initialize(const std::string& streamUrl) {
        // 初始化视频捕获
        capture.open(streamUrl);
        if (!capture.isOpened()) {
            return false;
        }

        // 设置视频参数
        capture.set(cv::CAP_PROP_BUFFERSIZE, 1);
        capture.set(cv::CAP_PROP_FPS, 30);

        // 初始化AR渲染器
        arRenderer.initialize(capture.get(cv::CAP_PROP_FRAME_WIDTH),
                             capture.get(cv::CAP_PROP_FRAME_HEIGHT));

        // 启动处理线程
        isRunning = true;
        processingThread = std::thread(&AgentVideoProcessor::processingLoop, this);

        return true;
    }

    void processingLoop() {
        while (isRunning) {
            cv::Mat frame;
            if (capture.read(frame)) {
                // 处理帧
                cv::Mat processedFrame = processFrame(frame);

                // 添加AR叠加
                cv::Mat arFrame = arRenderer.renderAR(processedFrame);

                // 更新显示缓冲区
                {
                    std::lock_guard<std::mutex> lock(frameMutex);
                    frameBuffer = arFrame.clone();
                }

                // 通知UI更新
                notifyFrameUpdate();
            }
        }
    }

    cv::Mat processFrame(cv::Mat& frame) {
        // 图像增强
        cv::Mat enhanced;
        cv::detailEnhance(frame, enhanced);

        // 降噪处理
        cv::Mat denoised;
        cv::fastNlMeansDenoisingColored(enhanced, denoised);

        // 边缘增强（便于AR叠加）
        cv::Mat edges;
        cv::Canny(denoised, edges, 50, 150);

        return denoised;
    }

    void updateARData(const AgentData& data) {
        // 更新AR显示数据
        arRenderer.updateData(data);

        // 计算AR元素位置
        overlayEngine.calculatePositions(data);
    }
};
```

---

## 六、性能优化与技术指标

### 6.1 性能目标

#### 6.1.1 响应时间指标
```
系统响应时间要求：
├─ 手势识别响应：≤100ms
├─ 层级切换时间：≤500ms
├─ 数据更新延迟：≤1s
├─ 视频传输延迟：≤200ms
└─ 时空锥计算：≤2s

渲染性能要求：
├─ 战略沙盘帧率：≥60fps
├─ 区域聚焦帧率：≥90fps
├─ 实体交互帧率：≥120fps
└─ 一线代理帧率：≥30fps
```

#### 6.1.2 系统资源占用
```
内存使用限制：
├─ Unity 3D进程：≤4GB
├─ Unreal Engine进程：≤8GB
├─ WebXR进程：≤2GB
├─ 视频处理进程：≤1GB
└─ 后端服务：≤4GB

CPU使用限制：
├─ 手势识别：≤15%单核
├─ 渲染管线：≤50%GPU
├─ 数据处理：≤30%多核
└─ 网络通信：≤5%单核
```

### 6.2 优化策略

#### 6.2.1 渲染优化
```csharp
// Unity层面的LOD系统
public class PortLODManager : MonoBehaviour {
    [System.Serializable]
    public class LODLevel {
        public float distance;
        public GameObject model;
        public int triangleCount;
    }

    public LODLevel[] lodLevels;
    private Transform cameraTransform;

    void Start() {
        cameraTransform = Camera.main.transform;
        StartCoroutine(LODUpdateCoroutine());
    }

    IEnumerator LODUpdateCoroutine() {
        var wait = new WaitForSeconds(0.1f); // 10fps更新频率

        while (true) {
            UpdateLODLevels();
            yield return wait;
        }
    }

    void UpdateLODLevels() {
        foreach (var lod in lodLevels) {
            float distance = Vector3.Distance(transform.position, cameraTransform.position);

            if (distance < lod.distance) {
                // 激活当前LOD级别
                SetActiveLOD(lod);
                break;
            }
        }
    }

    void SetActiveLOD(LODLevel targetLOD) {
        foreach (var lod in lodLevels) {
            lod.model.SetActive(lod == targetLOD);
        }
    }
}

// 动态批处理
public class DynamicBatcher : MonoBehaviour {
    private List<MeshFilter> dynamicObjects = new List<MeshFilter>();
    private CombineInstance[] combine;
    private Mesh combinedMesh;

    void Start() {
        CombineSimilarObjects();
    }

    void CombineSimilarObjects() {
        // 收集相同材质的物体
        var materialGroups = new Dictionary<Material, List<MeshFilter>>();

        MeshFilter[] filters = FindObjectsOfType<MeshFilter>();
        foreach (var filter in filters) {
            var renderer = filter.GetComponent<MeshRenderer>();
            if (renderer != null && renderer.sharedMaterial != null) {
                Material mat = renderer.sharedMaterial;

                if (!materialGroups.ContainsKey(mat)) {
                    materialGroups[mat] = new List<MeshFilter>();
                }
                materialGroups[mat].Add(filter);
            }
        }

        // 合并每组物体
        foreach (var group in materialGroups) {
            if (group.Value.Count > 10) { // 只有足够多的物体才合并
                CombineMeshes(group.Value, group.Key);
            }
        }
    }
}
```

#### 6.2.2 网络优化
```typescript
// 数据压缩与增量更新
class NetworkOptimizer {
    private compressionThreshold = 1024; // 1KB
    private updateBuffer = new Map();
    private lastUpdateTime = 0;

    // 数据压缩
    compressData(data: any): Uint8Array {
        const jsonString = JSON.stringify(data);

        if (jsonString.length > this.compressionThreshold) {
            // 使用LZ4压缩大数据
            return LZ4.compress(jsonString);
        }

        return new TextEncoder().encode(jsonString);
    }

    // 增量更新
    generateDeltaUpdate(currentData: any, previousData: any): DeltaUpdate {
        const delta = this.deepDiff(previousData, currentData);

        return {
            timestamp: Date.now(),
            delta: delta,
            checksum: this.calculateChecksum(currentData),
            compression: delta.length < JSON.stringify(currentData).length
        };
    }

    // 智能更新频率
    shouldUpdate(dataType: string, priority: string): boolean {
        const now = Date.now();
        const timeSinceLastUpdate = now - this.lastUpdateTime;

        // 基于数据类型和优先级决定更新频率
        const updateIntervals = {
            'vessel_position': { 'high': 1000, 'medium': 3000, 'low': 5000 },
            'weather_data': { 'high': 5000, 'medium': 10000, 'low': 30000 },
            'equipment_status': { 'high': 2000, 'medium': 5000, 'low': 10000 }
        };

        const interval = updateIntervals[dataType]?.[priority] || 10000;
        return timeSinceLastUpdate >= interval;
    }

    // 深度差异比较
    private deepDiff(obj1: any, obj2: any, path = ''): any {
        const diff = {};

        for (const key in obj1) {
            const newPath = path ? `${path}.${key}` : key;

            if (!(key in obj2)) {
                diff[newPath] = { op: 'remove', value: obj1[key] };
            } else if (typeof obj1[key] === 'object' && typeof obj2[key] === 'object') {
                const nestedDiff = this.deepDiff(obj1[key], obj2[key], newPath);
                if (Object.keys(nestedDiff).length > 0) {
                    Object.assign(diff, nestedDiff);
                }
            } else if (obj1[key] !== obj2[key]) {
                diff[newPath] = { op: 'update', oldValue: obj1[key], newValue: obj2[key] };
            }
        }

        for (const key in obj2) {
            if (!(key in obj1)) {
                const newPath = path ? `${path}.${key}` : key;
                diff[newPath] = { op: 'add', value: obj2[key] };
            }
        }

        return diff;
    }
}
```

---

## 七、测试方案与验收标准

### 7.1 功能测试矩阵

#### 7.1.1 核心功能测试
```
时空锥功能测试：
├─ 基础可视化 ✓/✗
│  ├─ 5层可能性显示
│  ├─ 透明度渐变效果
│  └─ 交互响应时间
├─ 决策交互 ✓/✗
│  ├─ 手势选择识别
│  └─ 结果固化动画
├─ 数据准确性 ✓/✗
│  ├─ 概率计算精度
│  └─ 预测结果验证
└─ 性能指标 ✓/✗
   ├─ 计算时间≤2s
   └─ 内存占用≤500MB

空间导航测试：
├─ 层级切换 ✓/✗
│  ├─ 4层完整切换
│  ├─ 过渡动画流畅
│  └─ 数据同步正确
├─ 手势控制 ✓/✗
│  ├─ 12种手势识别
│  ├─ 误识别率≤5%
│  └─ 响应时间≤100ms
├─ 视角控制 ✓/✗
│  ├─ 6DOF自由度
│  └─ 平滑移动过渡
└─ 数据加载 ✓/✗
   ├─ 切换时数据完整性
   └─ 加载时间≤500ms
```

#### 7.1.2 性能压力测试
```python
# 自动化性能测试脚本
class PerformanceTestSuite:
    def __init__(self):
        self.metrics = {}
        self.benchmarks = {
            'gesture_response': 100,  # ms
            'layer_transition': 500,  # ms
            'render_fps': 60,         # fps
            'memory_usage': 4096,     # MB
            'cpu_usage': 50,          # %
        }

    def test_gesture_recognition_performance(self):
        """测试手势识别性能"""
        response_times = []

        for i in range(1000):  # 测试1000次手势
            start_time = time.time()

            # 模拟手势输入
            gesture_data = self.generate_test_gesture()
            result = self.gesture_recognizer.recognize(gesture_data)

            end_time = time.time()
            response_time = (end_time - start_time) * 1000
            response_times.append(response_time)

            time.sleep(0.01)  # 10ms间隔

        # 计算统计数据
        avg_response = statistics.mean(response_times)
        max_response = max(response_times)
        min_response = min(response_times)
        percentile_95 = statistics.quantiles(response_times, n=20)[18]  # 95%分位数

        return {
            'average': avg_response,
            'maximum': max_response,
            'minimum': min_response,
            'percentile_95': percentile_95,
            'pass': avg_response <= self.benchmarks['gesture_response']
        }

    def test_rendering_performance(self):
        """测试渲染性能"""
        frame_times = []
        frame_count = 0
        start_time = time.time()

        # 运行60秒的渲染测试
        while time.time() - start_time < 60:
            frame_start = time.time()

            # 渲染一帧
            self.render_frame()

            frame_end = time.time()
            frame_time = (frame_end - frame_start) * 1000
            frame_times.append(frame_time)
            frame_count += 1

        # 计算FPS
        fps_values = [1000 / ft for ft in frame_times]
        avg_fps = statistics.mean(fps_values)
        min_fps = min(fps_values)

        return {
            'average_fps': avg_fps,
            'minimum_fps': min_fps,
            'total_frames': frame_count,
            'pass': avg_fps >= self.benchmarks['render_fps']
        }

    def test_memory_leaks(self):
        """测试内存泄漏"""
        initial_memory = self.get_memory_usage()

        # 运行长时间测试
        for cycle in range(100):  # 100个测试周期
            # 执行完整的功能循环
            self.run_full_functionality_cycle()

            # 强制垃圾回收
            gc.collect()

            time.sleep(1)

        final_memory = self.get_memory_usage()
        memory_growth = final_memory - initial_memory

        return {
            'initial_memory': initial_memory,
            'final_memory': final_memory,
            'memory_growth': memory_growth,
            'pass': memory_growth < 100  # 100MB以内认为是正常的
        }
```

### 7.2 用户体验测试

#### 7.2.1 可用性测试方案
```
测试参与者招募：
├─ 港口调度员（5名）
│  ├─ 5年以上工作经验
│  └─ 熟悉现有指挥系统
├─ 船舶引航员（3名）
│  ├─ 持有有效引航证书
│  └─ 大型船舶引航经验
├─ 码头管理员（4名）
│  ├─ 集装箱码头工作经验
│  └─ 设备操作背景
└─ 港口管理人员（3名）
   ├─ 中层以上管理职位
   └─ 决策制定经验

测试任务设计：
├─ 基础导航任务（5分钟）
│  ├─ 在不同层级间切换
│  ├─ 使用手势控制视角
│  └─ 查找特定船舶信息
├─ 决策制定任务（10分钟）
│  ├─ 处理船舶延误情况
│  ├─ 选择最优靠泊方案
│  └─ 应对设备故障事件
├─ 协作任务（10分钟）
│  ├─ 多人协同指挥
│  ├─ 信息共享与传递
│  └─ 应急情况处理
└─ 探索任务（5分钟）
   ├─ 自由探索功能
   ├─ 发现隐藏特性
   └─ 个性化设置

评估指标：
├─ 任务完成率（%）
├─ 任务完成时间（秒）
├─ 错误操作次数
├─ 帮助请求次数
├─ 主观满意度评分（1-7分）
└─ 认知负荷评分（NASA-TLX）
```

#### 7.2.2 主观体验评估
```python
# 用户体验评估问卷
class UserExperienceSurvey:
    def __init__(self):
        self.questions = {
            'system_usability': [
                {
                    'id': 'sus_1',
                    'question': '我认为系统易于使用',
                    'scale': 7,  # 1-7分
                    'type': 'likert'
                },
                {
                    'id': 'sus_2',
                    'question': '我觉得系统的功能很直观',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'sus_3',
                    'question': '我认为学习使用这个系统很容易',
                    'scale': 7,
                    'type': 'likert'
                }
            ],

            'immersion_presence': [
                {
                    'id': 'ip_1',
                    'question': '我感觉自己真的置身于港口环境中',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'ip_2',
                    'question': '我忘记了周围的真实环境',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'ip_3',
                    'question': '我对虚拟环境中的物体有真实感',
                    'scale': 7,
                    'type': 'likert'
                }
            ],

            'cognitive_load': [
                {
                    'id': 'cl_mental',
                    'question': '完成任务需要多大的脑力活动？',
                    'scale': 100,  # 0-100分
                    'type': 'nasa_tlx'
                },
                {
                    'id': 'cl_physical',
                    'question': '完成任务需要多大的体力活动？',
                    'scale': 100,
                    'type': 'nasa_tlx'
                },
                {
                    'id': 'cl_temporal',
                    'question': '完成任务的时间压力有多大？',
                    'scale': 100,
                    'type': 'nasa_tlx'
                },
                {
                    'id': 'cl_performance',
                    'question': '你认为自己的表现如何？',
                    'scale': 100,
                    'type': 'nasa_tlx'
                },
                {
                    'id': 'cl_effort',
                    'question': '完成任务需要多大的努力？',
                    'scale': 100,
                    'type': 'nasa_tlx'
                },
                {
                    'id': 'cl_frustration',
                    'question': '完成任务时感到多大的挫败感？',
                    'scale': 100,
                    'type': 'nasa_tlx'
                }
            ],

            'gesture_interaction': [
                {
                    'id': 'gi_natural',
                    'question': '手势交互感觉自然吗？',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'gi_precise',
                    'question': '手势识别的精确度如何？',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'gi_fatigue',
                    'question': '长时间使用手势会感到疲劳吗？',
                    'scale': 7,
                    'type': 'likert'
                }
            ],

            'visual_clarity': [
                {
                    'id': 'vc_information',
                    'question': '信息的视觉呈现清晰度如何？',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'vc_hierarchy',
                    'question': '信息层次结构容易理解吗？',
                    'scale': 7,
                    'type': 'likert'
                },
                {
                    'id': 'vc_overload',
                    'question': '是否感到信息过载？',
                    'scale': 7,
                    'type': 'likert'
                }
            ]
        }

    def collect_feedback(self, user_id, session_data):
        """收集用户反馈"""
        feedback = {
            'user_id': user_id,
            'session_id': session_data['session_id'],
            'timestamp': datetime.now(),
            'responses': {}
        }

        # 收集定量数据
        for category, questions in self.questions.items():
            feedback['responses'][category] = []

            for question in questions:
                response = {
                    'question_id': question['id'],
                    'question_text': question['question'],
                    'response_value': self.get_user_response(question),
                    'response_time': self.measure_response_time()
                }
                feedback['responses'][category].append(response)

        # 收集定性反馈
        feedback['qualitative'] = {
            'most_liked': input("您最喜欢这个系统的哪个功能？"),
            'least_liked': input("您最不喜欢这个系统的哪个方面？"),
            'improvement_suggestions': input("您有什么改进建议？"),
            'use_cases': input"您认为这个系统最适合什么使用场景？"
        }

        return feedback

    def calculate_sus_score(self, responses):
        """计算系统可用性量表得分"""
        sus_responses = responses['system_usability']

        # SUS评分计算逻辑
        score = 0
        for i, response in enumerate(sus_responses):
            value = response['response_value']
            # 奇数题正向计分，偶数题反向计分
            if i % 2 == 0:
                score += value - 1
            else:
                score += 7 - value

        # 转换为标准SUS分数（0-100）
        sus_score = (score / 18) * 100

        return {
            'raw_score': score,
            'sus_score': sus_score,
            'grade': self.get_sus_grade(sus_score),
            'adjective': self.get_sus_adjective(sus_score)
        }

    def calculate_nasa_tlx_score(self, responses):
        """计算NASA任务负荷指数"""
        tlx_responses = responses['cognitive_load']

        # 提取六个维度得分
        dimensions = {}
        for response in tlx_responses:
            dimensions[response['question_id']] = response['response_value']

        # 计算加权得分（需要权重评估）
        weights = self.calculate_tlx_weights(dimensions)
        weighted_score = sum(dimensions[dim] * weights[dim] for dim in dimensions)

        return {
            'dimensions': dimensions,
            'weights': weights,
            'weighted_score': weighted_score / 100,  # 归一化到0-1
            'interpretation': self.interpret_tlx_score(weighted_score)
        }
```

---

## 八、部署方案与运维计划

### 8.1 部署架构

#### 8.1.1 硬件配置要求
```
演示环境硬件规格：
├─ 主机系统
│  ├─ CPU: Intel i9-12900K / AMD Ryzen 9 5950X
│  ├─ GPU: NVIDIA RTX 4080 / AMD RX 7800 XT
│  ├─ RAM: 32GB DDR5-5600
│  ├─ Storage: 2TB NVMe SSD
│  └─ Network: 千兆以太网 + WiFi 6E
├─ VR/AR设备
│  ├─ HoloLens 2 (企业版)
│  ├─ Magic Leap 2
│  └─ iPad Pro (WebXR备用)
├─ 传感器系统
│  ├─ Leap Motion Controller
│  ├─ Kinect Azure DK
│  └─ 环境传感器套装
└─ 网络设备
   ├─ 5G路由器
   ├─ 千兆交换机
   └─ 无线AP
```

#### 8.1.2 软件部署流程
```bash
#!/bin/bash
# 自动化部署脚本

# 1. 环境准备
echo "准备部署环境..."
setup_environment() {
    # 安装依赖
    sudo apt update
    sudo apt install -y docker docker-compose
    sudo apt install -y python3 python3-pip
    sudo apt install -y nodejs npm

    # 创建项目目录
    mkdir -p /opt/port-demo
    cd /opt/port-demo
}

# 2. 部署后端服务
deploy_backend() {
    echo "部署后端服务..."

    # 克隆代码
    git clone https://github.com/port-demo/backend.git
    cd backend

    # 安装Python依赖
    pip3 install -r requirements.txt

    # 启动服务
    docker-compose up -d

    # 等待服务就绪
    wait_for_service "http://localhost:8080/health" 300
}

# 3. 部署Unity应用
deploy_unity() {
    echo "部署Unity应用..."

    # 下载预编译的Unity应用
    wget https://releases.port-demo.com/unity/PortDemo-Strategic-v2.0.zip
    unzip PortDemo-Strategic-v2.0.zip -d /opt/port-demo/unity

    # 创建启动脚本
    cat > /opt/port-demo/start-unity.sh << 'EOF'
#!/bin/bash
cd /opt/port-demo/unity
./PortDemo.x86_64 -screen-fullscreen 0 -screen-width 1920 -screen-height 1080
EOF
    chmod +x /opt/port-demo/start-unity.sh
}

# 4. 部署Unreal应用
deploy_unreal() {
    echo "部署Unreal应用..."

    # 下载Unreal应用
    wget https://releases.port-demo.com/unreal/PortDemo-Regional-v2.0.zip
    unzip PortDemo-Regional-v2.0.zip -d /opt/port-demo/unreal

    # 创建启动脚本
    cat > /opt/port-demo/start-unreal.sh << 'EOF'
#!/bin/bash
cd /opt/port-demo/unreal
./PortDemo.exe -windowed -resx=1920 -resy=1080
EOF
    chmod +x /opt/port-demo/start-unreal.sh
}

# 5. 部署WebXR应用
deploy_webxr() {
    echo "部署WebXR应用..."

    # 构建Web应用
    cd /opt/port-demo/webxr
    npm install
    npm run build

    # 配置Nginx
    sudo cp nginx.conf /etc/nginx/sites-available/port-demo
    sudo ln -s /etc/nginx/sites-available/port-demo /etc/nginx/sites-enabled/
    sudo nginx -s reload
}

# 6. 系统配置
configure_system() {
    echo "配置系统..."

    # 设置防火墙
    sudo ufw allow 8080/tcp  # API端口
    sudo ufw allow 3000/tcp  # WebXR端口
    sudo ufw allow 8888/tcp  # 数据端口

    # 配置自动启动
    sudo systemctl enable port-demo-backend
    sudo systemctl enable port-demo-nginx

    # 设置环境变量
    echo "export PORT_DEMO_ENV=production" >> ~/.bashrc
    echo "export PORT_DEMO_API_URL=http://localhost:8080" >> ~/.bashrc
}

# 7. 验证部署
verify_deployment() {
    echo "验证部署..."

    # 检查服务状态
    services=("backend" "nginx" "unity" "unreal")
    for service in "${services[@]}"; do
        if check_service_health $service; then
            echo "$service: ✓ 运行正常"
        else
            echo "$service: ✗ 运行异常"
            exit 1
        fi
    done

    echo "部署验证完成！"
}

# 执行部署流程
main() {
    setup_environment
    deploy_backend
    deploy_unity
    deploy_unreal
    deploy_webxr
    configure_system
    verify_deployment

    echo "港口Demo部署成功！"
    echo "访问地址：http://localhost:3000"
}

# 运行主函数
main "$@"
```

### 8.2 运维监控方案

#### 8.2.1 实时监控指标
```python
# 监控指标收集器
class SystemMonitor:
    def __init__(self):
        self.metrics = {
            'performance': {
                'fps': [],
                'latency': [],
                'memory_usage': [],
                'cpu_usage': []
            },
            'reliability': {
                'error_count': 0,
                'crash_count': 0,
                'recovery_time': [],
                'uptime_percentage': 100.0
            },
            'user_activity': {
                'session_count': 0,
                'gesture_count': 0,
                'interaction_time': [],
                'feature_usage': {}
            }
        }

    def collect_performance_metrics(self):
        """收集性能指标"""
        while True:
            # 帧率监控
            fps = self.get_current_fps()
            self.metrics['performance']['fps'].append({
                'timestamp': datetime.now(),
                'value': fps
            })

            # 延迟监控
            latency = self.measure_system_latency()
            self.metrics['performance']['latency'].append({
                'timestamp': datetime.now(),
                'value': latency
            })

            # 内存使用
            memory = psutil.virtual_memory().percent
            self.metrics['performance']['memory_usage'].append({
                'timestamp': datetime.now(),
                'value': memory
            })

            # CPU使用
            cpu = psutil.cpu_percent(interval=1)
            self.metrics['performance']['cpu_usage'].append({
                'timestamp': datetime.now(),
                'value': cpu
            })

            # 检查性能阈值
            self.check_performance_thresholds(fps, latency, memory, cpu)

            time.sleep(1)  # 每秒收集一次

    def check_performance_thresholds(self, fps, latency, memory, cpu):
        """检查性能阈值并告警"""
        alerts = []

        if fps < 30:  # 帧率低于30fps
            alerts.append({
                'type': 'performance',
                'severity': 'warning',
                'message': f'帧率过低: {fps:.1f}fps',
                'suggestion': '检查渲染负载，考虑降低细节等级'
            })

        if latency > 100:  # 延迟超过100ms
            alerts.append({
                'type': 'performance',
                'severity': 'error',
                'message': f'系统延迟过高: {latency:.1f}ms',
                'suggestion': '检查网络连接，优化数据处理流程'
            })

        if memory > 80:  # 内存使用超过80%
            alerts.append({
                'type': 'resource',
                'severity': 'warning',
                'message': f'内存使用率过高: {memory:.1f}%',
                'suggestion': '检查内存泄漏，考虑重启服务'
            })

        if alerts:
            self.send_alert(alerts)

    def generate_health_report(self):
        """生成系统健康报告"""
        report = {
            'timestamp': datetime.now(),
            'period': '24h',
            'overall_health': self.calculate_overall_health(),
            'performance_summary': self.summarize_performance(),
            'issues_detected': self.identify_issues(),
            'recommendations': self.generate_recommendations()
        }

        return report

    def calculate_overall_health(self):
        """计算整体健康度"""
        # 基于多个指标计算综合健康分数
        performance_score = self.calculate_performance_score()
        reliability_score = self.calculate_reliability_score()
        user_satisfaction = self.calculate_user_satisfaction()

        overall_score = (
            performance_score * 0.4 +
            reliability_score * 0.3 +
            user_satisfaction * 0.3
        )

        if overall_score >= 90:
            return 'excellent'
        elif overall_score >= 80:
            return 'good'
        elif overall_score >= 70:
            return 'fair'
        elif overall_score >= 60:
            return 'poor'
        else:
            return 'critical'
```

#### 8.2.2 预警与自动恢复
```python
# 智能预警与自动恢复系统
class IntelligentRecoverySystem:
    def __init__(self):
        self.recovery_strategies = {
            'performance_degradation': self.handle_performance_issues,
            'memory_leak': self.handle_memory_issues,
            'service_crash': self.handle_service_crashes,
            'network_failure': self.handle_network_issues
        }

        self.prediction_model = self.load_failure_prediction_model()

    def predict_failures(self, metrics):
        """预测系统故障"""
        # 使用机器学习模型预测故障
        features = self.extract_features(metrics)
        failure_probability = self.prediction_model.predict(features)

        if failure_probability > 0.8:
            predicted_failure = {
                'type': self.classify_failure_type(features),
                'probability': failure_probability,
                'estimated_time': self.estimate_failure_time(features),
                'confidence': self.calculate_prediction_confidence(features)
            }

            # 提前采取预防措施
            self.take_preventive_measures(predicted_failure)

            return predicted_failure

        return None

    def auto_recover(self, failure_type, severity):
        """自动恢复处理"""
        if failure_type in self.recovery_strategies:
            try:
                recovery_result = self.recovery_strategies[failure_type](severity)

                # 记录恢复操作
                self.log_recovery_action({
                    'timestamp': datetime.now(),
                    'failure_type': failure_type,
                    'severity': severity,
                    'recovery_action': recovery_result['action'],
                    'success': recovery_result['success'],
                    'recovery_time': recovery_result['time']
                })

                return recovery_result

            except Exception as e:
                # 自动恢复失败，升级处理
                return self.escalate_recovery(failure_type, str(e))

        return {'success': False, 'message': 'Unknown failure type'}

    def handle_performance_issues(self, severity):
        """处理性能问题"""
        if severity == 'low':
            # 轻度性能问题：调整渲染质量
            self.adjust_render_quality(0.8)
            return {
                'action': 'Reduced render quality to 80%',
                'success': True,
                'time': 5
            }

        elif severity == 'medium':
            # 中度性能问题：启用LOD系统
            self.enable_aggressive_lod()
            self.reduce_particle_effects()
            return {
                'action': 'Enabled aggressive LOD and reduced effects',
                'success': True,
                'time': 10
            }

        elif severity == 'high':
            # 严重性能问题：重启渲染服务
            self.restart_render_service()
            return {
                'action': 'Restarted render service',
                'success': True,
                'time': 30
            }

    def escalate_recovery(self, failure_type, error_message):
        """升级恢复处理"""
        # 通知运维团队
        self.notify_operations_team(failure_type, error_message)

        # 尝试最后的恢复手段
        if failure_type == 'system_collapse':
            return self.emergency_restart()

        return {
            'success': False,
            'message': 'Automatic recovery failed, manual intervention required',
            'escalated': True
        }
```

---

## 结语

本技术规格书为港口智能眼镜态势感知平台Demo提供了全面的技术实现方案。通过分层架构设计、核心算法优化、多模态交互整合和严格的性能测试，确保Demo能够充分展示空间计算概念在港口指挥场景中的应用价值。

关键技术亮点：
- **时空锥可视化引擎**：将抽象的决策过程转化为直观的可视化体验
- **多层级空间导航**：实现从宏观到微观的无缝切换
- **智能手势识别**：提供自然直观的交互方式
- **多源数据融合**：确保信息的准确性和实时性
- **高性能渲染优化**：保证流畅的用户体验

该Demo不仅是技术验证平台，更是未来港口智能化发展的前瞻性展示，将为港口行业的数字化转型提供重要的参考和借鉴。通过严格的测试和优化，确保Demo在各类演示环境中都能稳定运行，充分展现空间计算技术的魅力和价值。