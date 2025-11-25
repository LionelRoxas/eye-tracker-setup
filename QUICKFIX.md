# QUICK FIX: Install tobii-research on Apple Silicon Mac

## The Problem
`tobii-research` only has wheels for:
- Python 3.9 on **x86_64 (Intel)** architecture
- NOT available for **ARM64 (Apple Silicon/M1/M2/M3)**

## The Solution: Install x86_64 Python 3.9

### Step 1: Download and Install x86_64 Python 3.9

**Option A: Using Official Python.org Installer (RECOMMENDED)**

1. Download the **macOS Intel-only installer** for Python 3.9:
   ```bash
   # Open this URL in your browser:
   open "https://www.python.org/downloads/release/python-3918/"
   ```

2. Download **macOS 64-bit Intel-only installer**
   - File: `python-3.9.18-macos11.pkg`
   - This installs x86_64 Python that will run via Rosetta 2

3. Install it (double-click the .pkg file)

4. Verify the installation:
   ```bash
   /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 --version
   # Should show: Python 3.9.18
   
   # Check architecture
   /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -c "import platform; print(platform.machine())"
   # Should show: x86_64
   ```

### Step 2: Create Virtual Environment

```bash
cd /Users/uhcaa/Documents/GitHub/eye-tracker-setup

# Create virtual environment with x86_64 Python 3.9
/Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m venv .venv39_x86

# Activate it
source .venv39_x86/bin/activate

# Verify architecture
python -c "import platform; print(platform.machine())"
# Should show: x86_64
```

### Step 3: Install Dependencies

```bash
# Make sure you're in the activated environment
pip install --upgrade pip setuptools wheel

# Install tobii-research
pip install tobii-research

# Install other dependencies
pip install -r requirements.txt
```

### Step 4: Test

```bash
# Test tobii-research import
python -c "import tobii_research; print('tobii-research version:', tobii_research.__version__)"

# Should print: tobii-research version: 1.x.x (no errors)
```

### Step 5: Run the Project

```bash
source .venv39_x86/bin/activate
python src/core/main.py
```

---

## Alternative: Use Conda (If you have it installed)

Conda can manage x86_64 environments more easily:

```bash
# Set conda to use x86_64
CONDA_SUBDIR=osx-64 conda create -n eye-tracker python=3.9

# Activate
conda activate eye-tracker

# Verify
python -c "import platform; print(platform.machine())"
# Should show: x86_64

# Install dependencies
pip install tobii-research
pip install -r requirements.txt
```

---

## Quick Status Check Script

Save this as `check_setup.sh` and run it:

```bash
#!/bin/bash
echo "=== System Info ==="
echo "Architecture: $(uname -m)"
echo ""

echo "=== Python Info ==="
if [ -f ".venv39_x86/bin/python" ]; then
    echo "Virtual env exists: ✓"
    source .venv39_x86/bin/activate
    echo "Python version: $(python --version)"
    echo "Python arch: $(python -c 'import platform; print(platform.machine())')"
    echo ""
    
    echo "=== Package Check ==="
    python -c "import tobii_research; print('✓ tobii-research:', tobii_research.__version__)" 2>/dev/null || echo "✗ tobii-research not installed"
    python -c "import psychopy; print('✓ psychopy installed')" 2>/dev/null || echo "✗ psychopy not installed"
    python -c "import numpy; print('✓ numpy installed')" 2>/dev/null || echo "✗ numpy not installed"
else
    echo "Virtual env not found: ✗"
    echo "Run: /Library/Frameworks/Python.framework/Versions/3.9/bin/python3.9 -m venv .venv39_x86"
fi
```

Run it with:
```bash
chmod +x check_setup.sh
./check_setup.sh
```

---

## Why This Works

- Rosetta 2 on Apple Silicon Macs can run x86_64 applications seamlessly
- Installing x86_64 Python creates an x86_64 virtual environment
- pip in that environment will look for x86_64 wheels
- tobii-research HAS x86_64 wheels for Python 3.9
- Everything runs via Rosetta 2 with minimal performance impact

---

## Next Steps After Installation

Once everything is installed:

1. **Activate the environment** (every time you work on the project):
   ```bash
   source .venv39_x86/bin/activate
   ```

2. **Run the project**:
   ```bash
   python src/core/main.py
   ```

3. **Run Jupyter notebooks**:
   ```bash
   pip install jupyter
   cd src/notebooks
   jupyter notebook
   ```
