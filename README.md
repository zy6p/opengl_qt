# 在Qt中使用OpenGL画地图

- 使用管线模式(pipeline)即核心模式(code mode)
- 简单绘制模式早已被弃用，没有学习价值
- DirectX

![效果展示](./img/2021-05-17_15-10.png)

## Project Structure

这个项目是一个基于 **Qt + OpenGL + GDAL/OGR** 的地图渲染实验，核心流程是：
**UI 选择数据 → RenderLayer 解析与索引 → OpenGLWidget 渲染与交互显示**。

### 核心代码

- **入口与窗口层**
  - `main.cpp`：创建 `MainWindow` 与 `OpenGLWindow`。
  - `mainwindow.*`：基础 Qt 窗口与示例文件读取逻辑。
  - `openglwindow.*` + `openglwindow.ui`：OpenGL 窗口控制面板（加载 shp、旋转、密度、清空图层、选择开关等）。

- **渲染与交互层**
  - `openglwidget.*`：OpenGL 初始化、Shader 绑定、图层绘制、鼠标/键盘交互（平移、缩放、选择、相机漫游）。
  - `shaders/simple.vert`、`shaders/simple.frag`：顶点/片元着色器。

- **数据处理层（核心）**
  - `renderlayer.*`：
    - 读取 OGR 图层、抽取要素顶点；
    - 建立规则网格索引用于快速筛选；
    - 面要素三角化（`gpc.*`）；
    - 选择查询、密度计算与渲染数据准备；
    - 输出 R-Tree 骨架调试顶点。

- **空间索引与工具**
  - `rtreeindex.*`：自定义 R-Tree（插入、分裂、搜索、MBR 维护）。
  - `utils.*`：OGREnvelope 面积、合并增量等工具函数。

- **构建与测试**
  - `CMakeLists.txt` / `Demo.pro`：CMake 与 qmake 工程配置。
  - `tests/testtriangulate.cpp`：三角化相关测试样例。

### 关键特性

- 支持 Shapefile 加载与基础属性查询。
- 支持点/线/面渲染，面要素支持三角化渲染。
- 支持鼠标框选/点选、相机旋转与缩放。
- 支持点密度分析可视化。
- 内置规则网格索引 + R-Tree 结构用于空间检索与调试展示。

### Structure Diagram

![Project structure diagram](./img/project-structure.svg)

# 备注
- 这个代码是一个星期赶出来的，注释没写清楚、代码功能也都没有重构封装
- 当时时间有限，怎么方便怎么来，写出了这种无法维护的代码QAQ
