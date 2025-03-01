# 在 Windows 10 上安装支持 CUDA 的 OpenCV

## 系统要求

- **操作系统**: Windows 10 (64 位)
- **GPU**: NVIDIA GeForce 4070 TI Super（或任何支持 CUDA 的 GPU）
- **CUDA 工具包**: 12.1.0（2023 年 2 月）
- **cuDNN**: v8.9.7（2023 年 12 月），与 CUDA 12.x 兼容
- **OpenCV**: 4.11.0（opencv 和 opencv-contrib）
- **CMake**: 4.0.0-rc2
- **Visual Studio**: 16 2019 Enterprise（可能也适用于 Community 版本）
- **Python**: 3.11.6（venv）
- **NumPy**: 2.1.3

## 安装步骤

> ⚠️ **重要提示：请按照顺序执行所有步骤！**

### 1. 安装 CMake
- 从 [CMake 官方网站](https://cmake.org/download/) 下载最新版本并安装。

### 2. 安装 Visual Studio
- 安装 **Visual Studio 16 2019 Enterprise**（可能也适用于 Community 版本）。
- 确保安装了 **C++** 和 **Python** 相关的组件。

### 3. 检查 GPU 与 CUDA 兼容性
- 访问 [CUDA 支持的 GPU 列表](https://en.m.wikipedia.org/wiki/CUDA#GPUs_supported)。
- 查找您的 GPU 型号（例如，4070 Ti Super → **Ada Lovelace** 架构，支持 CUDA **11.8–12.8**）。
- 如果已经安装了 CUDA，请检查版本：
  ```sh
  nvcc --version
  ```

### 4. 安装 CUDA 工具包
- 从 [CUDA 工具包存档](https://developer.nvidia.com/cuda-toolkit-archive) 下载与您的 GPU 兼容的 CUDA 版本（例如 **CUDA 12.1.0**）。

### 5. 安装 cuDNN
- 从 [NVIDIA cuDNN 存档](https://developer.nvidia.com/rdp/cudnn-archive) 下载与您的 CUDA 版本匹配的 cuDNN。
- 解压后，将其内容复制到您的 CUDA 安装目录（例如 `G:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1\`）。

### 6. 准备 OpenCV 目录
- 创建一个目录用于构建 OpenCV（例如 `G:\opencvGPU\`）。
- 在该目录下创建一个名为 `build` 的空文件夹：
  ```sh
  - opencvGPU
    -- build
  ```

### 7. 下载 OpenCV
- 访问 [OpenCV GitHub 仓库](https://github.com/opencv/opencv)。
- 在 **Tags** 选项中，选择所需的版本（例如 **4.11.0**）。
- 下载 ZIP 文件并解压到 `opencvGPU` 目录。

### 8. 下载 opencv_contrib
- 访问 [opencv_contrib GitHub 仓库](https://github.com/opencv/opencv_contrib)。
- 选择与 OpenCV 相同的版本（例如 **4.11.0**）。
- 下载 ZIP 文件并解压到 `opencvGPU` 目录。

解压后，您的文件夹结构应如下所示：
```sh
- opencvGPU
  -- build
  -- opencv-4.11.0
     --- apps
     --- data
     --- etc...
  -- opencv_contrib-4.11.0
     --- modules
     --- samples
     --- etc...
```

### 9. 安装 Python 和 NumPy
- 如果尚未安装 Python，请从 [Python 官网](https://www.python.org/downloads/) 下载（例如 **Python 3.11.6**）。
- 检查 Python 版本：
  ```sh
  python --version
  ```
- 卸载已有的 OpenCV：
  ```sh
  pip uninstall opencv-python
  ```
- 检查已安装的包：
  ```sh
  pip list
  ```
- 更新 **NumPy**：
  ```sh
  pip install numpy
  ```

### 10. 配置 CMake
- 复制并修改以下命令，确保路径正确：
  ```sh
  set CMAKE_BUILD_PARALLEL_LEVEL=6
  "G:/Program Files/CMake/bin/cmake.exe" -H"G:/opencvGPU/opencv-4.11.0" \
    -DOPENCV_EXTRA_MODULES_PATH="G:/opencvGPU/opencv_contrib-4.11.0/modules" \
    -B"G:/opencvGPU/build" \
    -G "Visual Studio 16 2019" \
    -DINSTALL_TESTS=ON -DINSTALL_C_EXAMPLES=ON -DBUILD_EXAMPLES=ON \
    -DBUILD_opencv_world=ON -DENABLE_CUDA_FIRST_CLASS_LANGUAGE=ON \
    -DWITH_CUDA=ON -DCUDA_GENERATION=Auto -DBUILD_opencv_python3=ON \
    -DPYTHON3_INCLUDE_DIR="Z:/Python/Python311/include" \
    -DPYTHON3_LIBRARY="Z:/Python/Python311/libs/python311.lib" \
    -DPYTHON3_EXECUTABLE="Z:/Python/Python311/python.exe" \
    -DPYTHON3_NUMPY_INCLUDE_DIRS="Z:/Python/Python311/Lib/site-packages/numpy/_core/include" \
    -DPYTHON3_PACKAGES_PATH="Z:/Python/Python311/Lib/site-packages/" \
    -DPYTHON3_LIBRARY_DEBUG="Z:/Python/Python311/libs/python311.lib"
  ```
- 以 **管理员** 身份运行命令提示符（CMD），并执行上述命令。
- 如果路径错误，请清除缓存（删除 `build` 目录中的文件）并重试。

### 11. 编译 OpenCV
- 运行以下命令（可能需要 1–2 小时）：
  ```sh
  "G:/Program Files/CMake/bin/cmake.exe" --build "G:/opencvGPU/build" --target install --config Release
  ```

### 12. 验证安装
打开 Python 并运行以下命令：
```python
import cv2
from cv2 import cuda
cuda.getCudaEnabledDeviceCount()
```
如果输出 **1**，则表示 OpenCV 已成功启用 CUDA。

> ⚠️ **如果输出为 0 或者出现 "ImportError: No module named cv2" 错误，请检查以下事项：**
> - 重新执行 **步骤 10–11**，检查所有路径。
> - 确保 Visual Studio 安装了所有 **C++** 组件。
> - 检查 Python 版本（多个 Python 版本可能导致冲突）。
> - 确保 GPU、CUDA 和 cuDNN 版本匹配。
> - 如果使用旧版 CUDA（例如 **11.3**），请尝试安装旧版 OpenCV。

---
💡 **完成！** OpenCV CUDA 版本安装成功！ 🎉

