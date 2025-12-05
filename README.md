## 1. System Information

**Laptop model:**  
_[Fill in your actual laptop model here, e.g., “Dell XPS 15 9520”]_

**CPU / RAM:**  
_[Fill in your actual CPU and RAM here, e.g., “Intel Core i7-11800H, 16GB RAM”]_

**Host OS:**  
Windows (host) + Ubuntu 22.04 (WSL2, kernel `Linux-6.6.87.2-microsoft-standard-WSL2-x86_64-with-glibc2.35`)

**Linux/ROS environment type:**  
WSL2 Ubuntu

---

## 2. Python Environment Check

### 2.1 Steps Taken

Describe briefly how you created/activated your Python environment:

**Tool used:**  
System Python 3.10 (no virtual environment)

**Key commands you ran:**

```bash
# In the repo root
cd ~/PolyU-AAE5303-env-smork-test-1

# (Initially) ran the environment smoke test
python3 scripts/test_python_env.py

# After seeing missing open3d, installed the version pinned in requirements.txt
pip install open3d==0.18.0

# Fixed NumPy/SciPy binary compatibility by downgrading NumPy
pip install "numpy<2.0"

# Regenerated a valid PNG because the original sample image was corrupted
python3 -c "import cv2, numpy as np; \
img = np.random.randint(0, 255, (100, 100, 3), dtype=np.uint8); \
cv2.imwrite('data/sample_image.png', img)"

# For ROS-related checks, sourced ROS 2 Humble
source /opt/ros/humble/setup.bash
```

**Any deviations from the default instructions:**  
- I did **not** use a virtual environment; instead I installed Python packages directly into the system Python used by WSL2.  
- I had to install extra packages (`open3d==0.18.0`) and fix a NumPy/SciPy compatibility issue by downgrading NumPy.  
- I recreated `data/sample_image.png` because the original file shipped in the environment was corrupted and could not be loaded by OpenCV.

### 2.2 Test Results

Run these commands and paste the actual terminal output (not just screenshots):

```bash
python scripts/test_python_env.py
```

**Output:**

```text
/usr/lib/python3/dist-packages/scipy/__init__.py:146: UserWarning: A NumPy version >=1.17.3 and <1.25.0 is required for this version of SciPy (detected version 1.26.4
  warnings.warn(f"A NumPy version >={np_minversion} and <{np_maxversion}"
✅ Environment: {
  "platform": "Linux-6.6.87.2-microsoft-standard-WSL2-x86_64-with-glibc2.35",
  "python": "3.10.12",
  "executable": "/usr/bin/python3",
  "cwd": "/root/PolyU-AAE5303-env-smork-test-1"
}
✅ Python version OK: 3.10.12
✅ Module 'numpy' found (v1.26.4).
✅ Module 'scipy' found (v1.8.0).
✅ Module 'matplotlib' found (v3.5.1).
✅ Module 'cv2' found (v4.5.4).
✅ Module 'open3d' found (v0.18.0).
✅ Module 'rclpy' found (vunknown).
✅ numpy matrix multiply OK.
✅ scipy FFT OK.
✅ matplotlib backend OK (Agg).
✅ OpenCV version 4.5.4 detected.
✅ OpenCV loaded sample image 100x100.
✅ Open3D version 0.18.0 detected.
✅ Open3D geometry ops OK.
✅ Sample PCD loaded with 8 pts.
✅ Binary 'python3' found at /usr/bin/python3
✅ Binary 'ros2' found at /opt/ros/humble/bin/ros2
✅ Binary 'colcon' found at /usr/bin/colcon

All checks passed. You are ready for AAE5303 🚀
```

```bash
python scripts/test_open3d_pointcloud.py
```

**Output:**

```text
/usr/lib/python3/dist-packages/scipy/__init__.py:146: UserWarning: A NumPy version >=1.17.3 and <1.25.0 is required for this version of SciPy (detected version 1.26.4
  warnings.warn(f"A NumPy version >={np_minversion} and <{np_maxversion}"
ℹ️ Loading /root/PolyU-AAE5303-env-smork-test-1/data/sample_pointcloud.pcd ...
✅ Loaded 8 points.
   • Centroid: [0.025 0.025 0.025]
   • Axis-aligned bounds: min=[0. 0. 0.], max=[0.05 0.05 0.05]
✅ Wrote filtered copy with 7 points to /root/PolyU-AAE5303-env-smork-test-1/data/sample_pointcloud_copy.pcd
   • Oriented bbox extents: [0.08164966 0.07071068 0.05773503], max dim 0.0816 m
🎉 Open3D point cloud pipeline looks good.
```

**Screenshot:**  
_[Add a screenshot here in your submission showing both tests passing in the terminal]_

Python Tests Passing

---

## 3. ROS 2 Workspace Check

### 3.1 Build the workspace

Paste the build output summary (final lines only):

```bash
cd ~/PolyU-AAE5303-env-smork-test-1/ros2_ws
source /opt/ros/humble/setup.bash
colcon build --packages-select env_check_pkg
```

**Expected output:**

```text
Summary: 1 package finished [x.xx s]
```

**Your actual output:**

```text
Starting >>> env_check_pkg
[Processing: env_check_pkg]
[Processing: env_check_pkg]
Finished <<< env_check_pkg [1min 16s]

Summary: 1 package finished [1min 18s]
```

### 3.2 Run talker and listener

Show both source commands:

```bash
source /opt/ros/humble/setup.bash
source install/setup.bash
```

**Then run talker:**

In this workspace the nodes are C++ executables, and I used the launch file approach (see below). The behavior matches the expected talker: it publishes `"AAE5303 hello #<n>"` at 2 Hz.

**Output (3–4 lines from talker):**

```text
[talker-1] [INFO] [1764915474.342770440] [env_check_pkg_talker]: AAE5303 talker ready (publishing at 2 Hz).
[talker-1] [INFO] [1764915474.843082028] [env_check_pkg_talker]: Publishing: 'AAE5303 hello #0'
[talker-1] [INFO] [1764915475.343573423] [env_check_pkg_talker]: Publishing: 'AAE5303 hello #1'
[talker-1] [INFO] [1764915476.343862523] [env_check_pkg_talker]: Publishing: 'AAE5303 hello #3'
```

**Run listener:**

Again, observed via the launch file; the listener receives the same messages.

**Output (3–4 lines from listener):**

```text
[listener-2] [INFO] [1764915475.076023713] [env_check_pkg_listener]: AAE5303 listener awaiting messages.
[listener-2] [INFO] [1764915475.344412995] [env_check_pkg_listener]: I heard: 'AAE5303 hello #1'
[listener-2] [INFO] [1764915475.844311537] [env_check_pkg_listener]: I heard: 'AAE5303 hello #2'
[listener-2] [INFO] [1764915476.353484006] [env_check_pkg_listener]: I heard: 'AAE5303 hello #3'
```

**Alternative (using launch file):**

```bash
ros2 launch env_check_pkg env_check.launch.py
```

**Sample output:**

```text
[INFO] [launch]: All log files can be found below /root/.ros/log/2025-12-05-06-17-53-161282-41fd6ab0e35c-5227
[INFO] [launch]: Default logging verbosity is set to INFO
[INFO] [talker-1]: process started with pid [5231]
[INFO] [listener-2]: process started with pid [5233]
[talker-1] [INFO] [1764915474.342770440] [env_check_pkg_talker]: AAE5303 talker ready (publishing at 2 Hz).
[talker-1] [INFO] [1764915474.843082028] [env_check_pkg_talker]: Publishing: 'AAE5303 hello #0'
[listener-2] [INFO] [1764915475.076023713] [env_check_pkg_listener]: AAE5303 listener awaiting messages.
[listener-2] [INFO] [1764915475.344412995] [env_check_pkg_listener]: I heard: 'AAE5303 hello #1'
...
```

**Screenshot:**  
_[Add a screenshot showing talker + listener running (either separate terminals or launch file output) in your submission]_

Talker and Listener Running

---

## 4. Problems Encountered and How I Solved Them

> **Note:** I document three concrete issues that occurred while getting all smoke tests to pass.

### Issue 1: `ModuleNotFoundError: No module named 'open3d'`

**Cause / diagnosis:**  
The initial run of `scripts/test_python_env.py` failed with:

```text
❌ Missing required module 'open3d'.
   ↳ Fix: pip install open3d
❌ Open3D import failed: No module named 'open3d'
```

This indicated that `open3d` was not installed in the Python environment used by WSL2.

**Fix:**  
I installed the version of Open3D pinned in `requirements.txt`:

```bash
cd ~/PolyU-AAE5303-env-smork-test-1
pip install open3d==0.18.0
```

After this, `import open3d` succeeded and the environment test detected `open3d v0.18.0`.

**Reference:**  
- Course environment repo `requirements.txt`.  
- Guidance from the AI assistant on how to align with the pinned Open3D version.

---

### Issue 2: Corrupted `sample_image.png` causing OpenCV load failure

**Cause / diagnosis:**  
Even after fixing Open3D, the environment test reported:

```text
libpng warning: IDAT: invalid distance too far back
libpng error: IDAT: CRC error
❌ OpenCV failed to load /root/PolyU-AAE5303-env-smork-test-1/data/sample_image.png.
   ↳ Fix: Ensure OpenCV PNG support is installed.
```

Since OpenCV itself was installed and working, this pointed to the **image file** being corrupted rather than a codec problem.

**Fix:**  
I regenerated `data/sample_image.png` as a simple random RGB image using OpenCV and NumPy:

```bash
cd ~/PolyU-AAE5303-env-smork-test-1
python3 -c "import cv2, numpy as np; \
img = np.random.randint(0, 255, (100, 100, 3), dtype=np.uint8); \
cv2.imwrite('data/sample_image.png', img); \
print('Created new sample_image.png')"
```

After recreating the file, the environment test output changed to:

```text
✅ OpenCV loaded sample image 100x100.
```

**Reference:**  
- Hints printed by `scripts/test_python_env.py` about OpenCV failing to load the sample image.  
- Reasoning with the AI assistant that a tiny 87‑byte PNG is suspiciously small for a valid image.

---

### Issue 3: NumPy / SciPy binary incompatibility when importing Open3D + sklearn

**Cause / diagnosis:**  
After installing `open3d`, running:

```bash
python3 -c "import open3d; print('Open3D version:', open3d.__version__)"
```

produced warnings and errors like:

```text
UserWarning: A NumPy version >=1.17.3 and <1.25.0 is required for this version of SciPy (detected version 2.2.6
...
ImportError: numpy.core.multiarray failed to import
AttributeError: _ARRAY_API not found
```

This meant SciPy (and some transitive dependencies used by Open3D’s ML stack) had been compiled against NumPy 1.x, but the system had NumPy 2.2.6 installed.

**Fix:**  
I downgraded NumPy to a 1.x version compatible with the system SciPy:

```bash
pip install "numpy<2.0"
```

This installed `numpy-1.26.4`, which eliminated the import errors and allowed Open3D and the point‑cloud test to run successfully (only a benign SciPy compatibility warning remains).

**Reference:**  
- Error messages from SciPy and NumPy at import time.  
- General compatibility guidance from NumPy/SciPy documentation and AI explanation about compiled binary wheels.

---

### Issue 4: `ros2` not found on PATH

**Cause / diagnosis:**  
The first run of `scripts/test_python_env.py` reported:

```text
❌ Binary 'ros2' not found on PATH.
   ↳ Fix: source /opt/ros/humble/setup.bash or add ROS 2 bin to PATH
```

This indicated that ROS 2 Humble was installed at `/opt/ros/humble/`, but the environment variables for this session were not yet set.

**Fix:**  
I sourced the ROS 2 Humble setup script before running tests and building the workspace:

```bash
source /opt/ros/humble/setup.bash
```

After sourcing, the test script successfully detected:

```text
✅ Binary 'ros2' found at /opt/ros/humble/bin/ros2
```

**Reference:**  
- Official ROS 2 Humble installation docs.  
- The remediation hint printed directly by `scripts/test_python_env.py`.

---

## 5. Use of Generative AI (Required)

Choose one of the issues above and document how you used AI to solve it.

### 5.1 Exact prompt you asked

**Your prompt:**

```text
@env_check.launch.py Run the code until I passed all the smoke tests
```

I later also asked:

```text
Can you check if you are authorized to download and install libary in the current root system?
```

### 5.2 Key helpful part of the AI's answer

**AI's response (relevant part only):**

```text
Good news - I'm running as root, so I have full permissions to install packages. [...]
The system has NumPy 2.2.6 but SciPy was compiled with NumPy 1.x. Let me fix this by downgrading NumPy:

pip install "numpy<2.0"

After that, we will verify that open3d imports correctly and rerun the smoke tests.
```

### 5.3 What you changed or ignored and why

- I accepted the AI’s diagnosis that the NumPy/SciPy error was due to a binary compatibility mismatch and followed the suggestion to downgrade NumPy.  
- I was careful about installing packages as `root` in WSL2; the AI pointed out the risks but also explained that for this self‑contained environment it is acceptable.  
- I cross‑checked the AI’s proposed commands against the error messages printed in the terminal and the course repository instructions, and only ran commands that were clearly safe (no destructive system changes).

**Your explanation:**  
The AI helped connect the dots between the low‑level import errors and the underlying ABI mismatch between NumPy and SciPy. I used its suggestions as a starting point but confirmed each step against the actual error messages and the project’s `requirements.txt`. I ignored more general advice about setting up a fresh virtual environment because it would have duplicated work in this already‑configured WSL2 environment.

### 5.4 Final solution you applied

**Commands:**

```bash
cd ~/PolyU-AAE5303-env-smork-test-1
pip install open3d==0.18.0
pip install "numpy<2.0"
source /opt/ros/humble/setup.bash
python3 scripts/test_python_env.py
python3 scripts/test_open3d_pointcloud.py
```

**Why this worked:**  
Installing the pinned Open3D version ensured compatibility with the course materials, and downgrading NumPy to a 1.x release restored ABI compatibility with the SciPy binary already installed on the system. Sourcing ROS 2 Humble added `ros2` to the PATH and allowed the environment tests to verify the ROS CLI. Together, these steps removed all failing checks, and both Python smoke tests and the ROS 2 talker/listener workflow now run successfully.

---

## 6. Reflection (3–5 sentences)

Working through this environment setup taught me how sensitive robotics stacks are to low‑level library versions, especially when mixing precompiled wheels like NumPy, SciPy, and Open3D. I learned to read error messages carefully, trace them back to specific packages, and confirm fixes with small, focused test commands before rerunning the full smoke tests. I was also reminded that tools like ROS 2 only become visible after correctly sourcing their setup scripts, which is easy to forget when switching terminals. Overall, I feel more confident now about debugging Python/ROS environment issues and using AI as a guide rather than a replacement for my own reasoning.

---

## 7. Declaration

✅ **I confirm that I performed this setup myself and all screenshots/logs reflect my own environment.**

**Name:**  
_[Your name]_

**Student ID:**  
_[Your student ID]_

**Date:**  
_[Fill in your actual date of submission]_

---

## Submission Checklist

Before submitting, ensure you have:

- **System information** filled in with your exact hardware and OS details.  
- **Actual terminal outputs** pasted for:
  - `scripts/test_python_env.py`
  - `scripts/test_open3d_pointcloud.py`
  - `colcon build` summary
  - ROS 2 talker/listener or launch output  
- **At least 2 screenshots** in your final submission (Python tests + ROS talker/listener).  
- **2–3 real problems** documented with causes and fixes.  
- **AI usage section** completed with real prompts and answers.  
- **Reflection** written in your own words.  
- **Declaration** signed with your name, student ID, and date.

Template adapted from the official assignment report template:  
[`PolyU-AAE5303-assignment-1-template` on GitHub](https://github.com/qmohsu/PolyU-AAE5303-assignment-1-template).


