# Virtual Environment & Pip Troubleshooting Guide

## check pip and package 
import sys
print(sys.executable)
!{sys.executable} -m pip show langchain-huggingface

## Common Error

```text
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe: No module named pip
```

This means the virtual environment exists, but `pip` is missing or corrupted.

---

# Step 1: Verify Which Python Is Being Used

Open CMD and run:

```cmd
python --version
where python
```

Expected:

* Python version is displayed.
* At least one valid Python installation path is shown.

---

# Step 2: Check Whether Pip Exists in the Virtual Environment

```cmd
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe -m pip --version
```

If you see:

```text
No module named pip
```

continue to Step 3.

---

# Step 3: Attempt to Repair Pip

```cmd
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe -m ensurepip --upgrade
```

Then:

```cmd
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe -m pip install --upgrade pip
```

Verify:

```cmd
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe -m pip --version
```

If successful, stop here.

---

# Step 4: Recreate the Virtual Environment (Recommended Fix)

Delete existing environment:

```cmd
rmdir /s /q myenv
```

Create a fresh environment:

```cmd
python -m venv myenv
```

Activate:

```cmd
myenv\Scripts\activate
```

Upgrade pip:

```cmd
python -m pip install --upgrade pip
```

Verify:

```cmd
pip --version
```

---

# Step 5: Install Required Packages

Example:

```cmd
pip install langchain-huggingface
pip install jupyter
pip install ipykernel
```

Or:

```cmd
pip install -r requirements.txt
```

---

# Step 6: Register Jupyter Kernel

```cmd
python -m ipykernel install --user --name=myenv
```

Restart Jupyter and select:

```text
Kernel → myenv
```

---

# Step 7: Verify Package Installation

```cmd
pip show langchain-huggingface
```

Expected output contains package details.

---

# Step 8: Verify Notebook Environment

In a notebook:

```python
import sys
print(sys.executable)
```

Expected:

```text
d:\GenAI-Bootcamp-Practical\myenv\Scripts\python.exe
```

---

# Step 9: Install Package Into Current Notebook Environment

If notebook and terminal use different environments:

```python
import sys

!{sys.executable} -m pip install langchain-huggingface
```

Restart the kernel after installation.

---

# Import Reference

Install:

```bash
pip install langchain-huggingface
```

Import:

```python
from langchain_huggingface import HuggingFaceEndpoint
```

Important:

❌ Wrong

```python
from langchain-huggingface import HuggingFaceEndpoint
```

✅ Correct

```python
from langchain_huggingface import HuggingFaceEndpoint
```

Reason:

* Package name = `langchain-huggingface`
* Import name = `langchain_huggingface`

---

# Quick Recovery Checklist

1. Check Python

```cmd
python --version
where python
```

2. Check pip

```cmd
python -m pip --version
```

3. Repair pip

```cmd
python -m ensurepip --upgrade
```

4. Recreate venv if needed

```cmd
rmdir /s /q myenv
python -m venv myenv
myenv\Scripts\activate
```

5. Upgrade pip

```cmd
python -m pip install --upgrade pip
```

6. Install dependencies

```cmd
pip install langchain-huggingface jupyter ipykernel
```

7. Verify notebook kernel

```python
import sys
print(sys.executable)
```

8. Retry import

```python
from langchain_huggingface import HuggingFaceEndpoint
```
