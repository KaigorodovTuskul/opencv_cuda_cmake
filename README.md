🔗 **[ENGLISH](readme.md)**
🔗 **[CHINESE](readme_chinese.md)**
🔗 **[RUSSIAN](readme_russian.md)**

# Installing OpenCV with CUDA Support on Windows 10

## System Requirements

- **OS**: Windows 10 (64-bit)
- **GPU**: NVIDIA GeForce 4070 TI Super (or any CUDA-supported GPU)
- **CUDA Toolkit**: 12.1.0 (February 2023)
- **cuDNN**: v8.9.7 (December 2023), compatible with CUDA 12.x
- **OpenCV**: 4.11.0 (opencv and opencv-contrib)
- **CMake**: 4.0.0-rc2
- **Visual Studio**: 16 2019 Enterprise (Community version may work)
- **Python**: 3.11.6 (venv)
- **NumPy**: 2.1.3

## Step-by-Step Installation

> ⚠️ **Important:** Follow all steps in order!

### 1. Install CMake
- Download the latest version from [CMake's official website](https://cmake.org/download/).

### 2. Install Visual Studio
- Install **Visual Studio 16 2019 Enterprise** (Community version may work).
- Ensure all **C++** and **Python** packages are installed.

### 3. Check GPU Compatibility with CUDA
- Visit the [CUDA-supported GPU list](https://en.m.wikipedia.org/wiki/CUDA#GPUs_supported).
- Find your GPU model (e.g., 4070 Ti Super → **Ada Lovelace**, CUDA **11.8–12.8** support).
- If CUDA is already installed, check the version:
  ```sh
  nvcc --version
  ```

### 4. Install CUDA Toolkit
- Download a CUDA version compatible with your GPU from the [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) (e.g., **CUDA Toolkit 12.1.0**).

### 5. Install cuDNN
- Download the **cuDNN** version compatible with your CUDA from the [NVIDIA cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive).
- Extract the contents into your CUDA installation directory (e.g., `G:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1\`).

### 6. Prepare OpenCV Build Directory
- Create a folder for building OpenCV (e.g., `G:\opencvGPU\`).
- Inside, create an empty `build` folder:
  ```sh
  - opencvGPU
    -- build
  ```

### 7. Download OpenCV
- Go to the [OpenCV GitHub repository](https://github.com/opencv/opencv).
- In the **Tags** section, select the required version (e.g., **4.11.0**).
- Download the ZIP archive and extract it into `opencvGPU`.

### 8. Download opencv_contrib
- Visit the [opencv_contrib GitHub repository](https://github.com/opencv/opencv_contrib).
- Select the same version as OpenCV (e.g., **4.11.0**).
- Download the ZIP archive and extract it into `opencvGPU`.

After extraction, your folder structure should look like this:
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

### 9. Install Python and NumPy
- If Python is not installed, download it from the [official Python website](https://www.python.org/downloads/) (e.g., **Python 3.11.6**).
- Check the installed version:
  ```sh
  python --version
  ```
- Remove any existing OpenCV installations:
  ```sh
  pip uninstall opencv-python
  ```
- Verify installed packages:
  ```sh
  pip list
  ```
- Update **NumPy**:
  ```sh
  pip install numpy
  ```

### 10. Configure CMake
- Copy and modify the following command in `notepad`, adjusting the paths accordingly:
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
- Open Command Prompt **as Administrator** and run the command.
- If there are path errors, clear the cache (delete files in `build`) and retry.

### 11. Compile OpenCV
- Run the following command (this may take 1–2 hours):
  ```sh
  "G:/Program Files/CMake/bin/cmake.exe" --build "G:/opencvGPU/build" --target install --config Release
  ```

### 12. Verify Installation
Open Python and run:
```python
import cv2
from cv2 import cuda
cuda.getCudaEnabledDeviceCount()
```
If the output is **1**, OpenCV with CUDA is successfully installed.

> ⚠️ **If the result is 0 or you get "ImportError: No module named cv2":**
> - Repeat steps 10–11, double-check all paths.
> - Ensure Visual Studio has all necessary **C++** packages installed.
> - Check Python versions (multiple versions may cause conflicts).
> - Verify your GPU, CUDA, and cuDNN versions match.
> - If using an older CUDA version (e.g., **11.3**), try older OpenCV versions.

---
💡 **Done!** OpenCV with CUDA support is now installed. 🎉

