# Modern Setup Guide for Eye Tracker Setup

## ⚠️ Critical Compatibility Issues

This project was originally written for **Python 2.7** and needs updates to work with modern Python versions. Here are the main issues and solutions:

### 1. **tobii-research Package Compatibility**

The `tobii-research` package has **LIMITED** Python version support:
- ✅ Supports: Python 2.7, 3.5, 3.6, 3.7, 3.8, 3.9
- ❌ Does NOT support: Python 3.10, 3.11, 3.12, 3.13
- ❌ Does NOT support: Apple Silicon (ARM64/M1/M2/M3 Macs) natively

**You are currently using Python 3.13 on ARM64 (Apple Silicon), which is not supported.**

### 2. **Solutions**

#### Option A: Use Python 3.9 with Rosetta 2 (Recommended for macOS ARM64)

1. Install Python 3.9 x86_64 version using Rosetta 2:
```bash
# Install Rosetta 2 if you haven't already
softwareupdate --install-rosetta

# Install Python 3.9 x86_64 using pyenv
arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
arch -x86_64 /usr/local/bin/brew install pyenv
arch -x86_64 pyenv install 3.9.18

# Create a virtual environment with Python 3.9
arch -x86_64 ~/.pyenv/versions/3.9.18/bin/python -m venv .venv39

# Activate it
source .venv39/bin/activate

# Install dependencies
pip install -r requirements.txt
```

#### Option B: Use Python 3.9 natively (Intel Macs or Linux)

```bash
# Create virtual environment with Python 3.9
python3.9 -m venv .venv39
source .venv39/bin/activate
pip install --upgrade pip setuptools
pip install -r requirements.txt
```

#### Option C: Use Conda (Cross-platform)

```bash
# Create conda environment with Python 3.9
conda create -n eye-tracker python=3.9
conda activate eye-tracker
pip install -r requirements.txt
```

### 3. **Code Updates Required**

The following Python 2 to Python 3 changes have been made:

#### `src/libs/tobii_pro_wrapper.py`
- ✅ Changed `basestring` to `str` (Python 2 → Python 3)

### 4. **Quick Setup (After installing Python 3.9)**

```bash
# Navigate to project directory
cd /path/to/eye-tracker-setup

# Create Python 3.9 virtual environment
python3.9 -m venv .venv39
source .venv39/bin/activate  # On Windows: .venv39\Scripts\activate

# Upgrade pip and setuptools
pip install --upgrade pip setuptools

# Install dependencies
pip install -r requirements.txt

# Run the main script
python src/core/main.py
```

### 5. **Verify Installation**

```bash
# Check Python version (should be 3.9.x)
python --version

# Check if tobii-research installed correctly
python -c "import tobii_research; print(tobii_research.__version__)"

# Check other dependencies
python -c "import psychopy; import numpy; import scipy; print('All dependencies OK')"
```

### 6. **Common Errors and Fixes**

#### Error: "No matching distribution found for tobii-research"
- **Cause**: Using unsupported Python version (3.10+) or ARM64 architecture
- **Fix**: Use Python 3.9 with x86_64 architecture (see Option A above)

#### Error: "basestring is not defined"
- **Cause**: Python 2 code not updated for Python 3
- **Fix**: Already fixed in this update, ensure you're using updated files

#### Error: "Import 'psychopy' could not be resolved"
- **Cause**: Dependencies not installed
- **Fix**: Run `pip install -r requirements.txt`

### 7. **Alternative: Docker (Future Consideration)**

For consistent cross-platform setup, consider using Docker:

```dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
CMD ["python", "src/core/main.py"]
```

## Additional Resources

- [Tobii Pro SDK Documentation](http://developer.tobiipro.com/python.html)
- [Python 3.9 Downloads](https://www.python.org/downloads/)
- [Rosetta 2 for Apple Silicon](https://support.apple.com/en-us/HT211861)
- [pyenv Installation Guide](https://github.com/pyenv/pyenv#installation)

## Notes

- This project requires a Tobii Eye Tracker device to function
- You need a special license from Tobii for analytical use (see README.md)
- The license file should be placed in `licenses/se_internal_license_for_system_tests`
