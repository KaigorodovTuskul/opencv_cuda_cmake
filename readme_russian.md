# Установка OpenCV с поддержкой CUDA на Windows 10

## Мой стек

- **ОС**: Windows 10 (64-bit)
- **GPU**: NVIDIA GeForce 4070 TI Super (или другой с поддержкой CUDA)
- **CUDA Toolkit**: 12.1.0 (февраль 2023)
- **cuDNN**: v8.9.7 (декабрь 2023), совместимый с CUDA 12.x
- **OpenCV**: 4.11.0 (opencv и opencv-contrib)
- **CMake**: 4.0.0-rc2
- **Visual Studio**: 16 2019 Enterprise (можно попробовать Community)
- **Python**: 3.11.6 (venv)
- **NumPy**: 2.1.3

## Пошаговая установка

> ⚠️ **Важно:** Все шаги необходимо выполнять последовательно!

### 1. Установка CMake
- Перейдите на [официальный сайт CMake](https://cmake.org/download/) и скачайте последнюю версию.

### 2. Установка Visual Studio
- Установите **Visual Studio 16 2019 Enterprise** (можно попробовать Community).
- Убедитесь, что установлены все пакеты для **C++** и **Python**.

### 3. Проверка совместимости GPU с CUDA
- Перейдите на страницу [списка GPU, поддерживающих CUDA](https://en.m.wikipedia.org/wiki/CUDA#GPUs_supported).
- Найдите свою видеокарту (например, 4070 Ti Super → **Ada Lovelace**, поддержка CUDA **11.8–12.8**).
- Если CUDA уже установлена, проверьте её версию:
  ```sh
  nvcc --version
  ```

### 4. Установка CUDA Toolkit
- Перейдите в [архив версий CUDA](https://developer.nvidia.com/cuda-toolkit-archive) и скачайте версию, совместимую с вашим GPU (например, **CUDA Toolkit 12.1.0**).

### 5. Установка cuDNN
- Загрузите версию **cuDNN**, совместимую с вашим CUDA, из [архива NVIDIA](https://developer.nvidia.com/rdp/cudnn-archive).
- Распакуйте содержимое архива в папку установки CUDA (например, `G:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.1\`).

### 6. Подготовка папки для сборки OpenCV
- Создайте папку для сборки OpenCV (например, `G:\opencvGPU\`).
- Внутри создайте пустую папку `build`:
  ```sh
  - opencvGPU
    -- build
  ```

### 7. Загрузка OpenCV
- Перейдите в [репозиторий OpenCV](https://github.com/opencv/opencv).
- В разделе **Tags** выберите нужную версию (например, **4.11.0**).
- Скачайте ZIP-архив, распакуйте его в `opencvGPU`.

### 8. Загрузка opencv_contrib
- Перейдите в [репозиторий opencv_contrib](https://github.com/opencv/opencv_contrib).
- Выберите ту же версию (например, **4.11.0**).
- Скачайте ZIP-архив, распакуйте его в `opencvGPU`.

После распаковки структура должна быть следующей:
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

### 9. Установка Python и NumPy
- Если Python не установлен, скачайте с [официального сайта](https://www.python.org/downloads/) (например, **Python 3.11.6**).
- Проверьте установленную версию:
  ```sh
  python --version
  ```
- Удалите все установленные версии OpenCV:
  ```sh
  pip uninstall opencv-python
  ```
- Проверьте установленные пакеты:
  ```sh
  pip list
  ```
- Обновите **NumPy**:
  ```sh
  pip install numpy
  ```

### 10. Конфигурация CMake
- Скопируйте и отредактируйте следующую команду в `notepad`, заменяя пути на свои:
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
- Запустите командную строку **от имени администратора** и выполните команду.
- Если есть ошибки с путями, очистите кэш (удалите файлы в `build`) и попробуйте снова.

### 11. Компиляция OpenCV
- Запустите команду (может занять 1–2 часа):
  ```sh
  "G:/Program Files/CMake/bin/cmake.exe" --build "G:/opencvGPU/build" --target install --config Release
  ```

### 12. Проверка установки
Откройте Python и выполните:
```python
import cv2
from cv2 import cuda
cuda.getCudaEnabledDeviceCount()
```
Если вывод **1**, значит OpenCV успешно собран с поддержкой CUDA.

> ⚠️ **Если результат 0 или ошибка "ImportError: No module named cv2":**
> - Повторите шаги 10–11, проверьте все пути.
> - Убедитесь, что в Visual Studio установлены все **C++** пакеты.
> - Проверьте версии Python (если установлено несколько, это может вызвать конфликты).
> - Убедитесь, что версия CUDA и cuDNN соответствует вашей видеокарте.
> - Если используется старая версия CUDA (например, **11.3**), попробуйте старые версии OpenCV.

---
💡 **Готово!** Теперь у вас установлен OpenCV с поддержкой CUDA. 🎉

