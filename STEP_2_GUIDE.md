# Migration Step 2: Setup Target Environment

This guide walks you through the process of setting up the target environment for migrating a PHP application to Python. By the end of this step, you will have a functional Python development environment, including the installation of necessary tools, project structure setup, and build tool configurations.

---

## **1. Step Overview and Objectives**

**Objective**: Prepare a Python-based development environment, mirroring the structure and functionality of the PHP source application.

### Key Tasks:
1. Install Python and relevant frameworks.
2. Create and configure the project structure.
3. Set up build tools for dependency management and automation.

This step ensures a smooth transition from PHP to Python by laying the foundation for further migration and development activities.

---

## **2. Prerequisites and Dependencies**

Before starting, ensure the following:

- **Source Code Access**: Clone the PHP repository (`repository`) that needs to be migrated.
- **Development Tools**:
  - A code editor such as VS Code, PyCharm, or your preferred IDE.
  - Git installed for version control.
  - A package manager like `pip` or `pipenv`.
- **System Requirements**:
  - Python 3.9+ installed.
  - Admin privileges to install dependencies.

### Software Installation
- Download Python: [Python.org](https://www.python.org/)
- Install Git: [Git Downloads](https://git-scm.com/downloads)
- Recommended IDEs:
  - [VS Code](https://code.visualstudio.com/)
  - [PyCharm](https://www.jetbrains.com/pycharm/)

---

## **3. Detailed Implementation Instructions**

### **Task 1: Install Python and Relevant Frameworks**

#### Step 1.1: Install Python
1. Go to the [official Python website](https://www.python.org/downloads/).
2. Download and install the latest stable Python version (3.9+ recommended).
3. Verify the installation using:
   ```bash
   python --version
   ```
   or
   ```bash
   python3 --version
   ```

#### Step 1.2: Install the Required Framework
For this migration, we will use **Flask** as the initial web framework (you can adapt for Django if needed).

1. Install Flask via `pip`:
   ```bash
   pip install flask
   ```
2. Verify the installation:
   ```bash
   python -c "import flask; print(flask.__version__)"
   ```

> **💡 INFO**: Flask is chosen for its simplicity and flexibility, making it ideal for migrations. If your PHP application is larger and needs an ORM or admin panel, consider using Django.

---

### **Task 2: Setup Project Structure**

Organize your Python project to mimic the modularity of your PHP application.

#### Step 2.1: Create a Project Directory
1. Create a new directory for your project:
   ```bash
   mkdir my_python_project
   cd my_python_project
   ```

2. Initialize a Git repository:
   ```bash
   git init
   ```

#### Step 2.2: Define the Directory Structure
Use the following structure:

```
my_python_project/
│
├── app/                # Application code
│   ├── __init__.py     # Application factory
│   ├── routes.py       # Route definitions
│   ├── models.py       # Database models
│   └── templates/      # HTML templates (if any)
│
├── tests/              # Unit tests
│   ├── __init__.py
│   └── test_app.py
│
├── requirements.txt    # Python dependencies
├── app.py              # Entry point for the application
└── README.md           # Documentation
```

#### Step 2.3: Create Initial Project Files
1. Create the `app.py` file:
   ```python
   from flask import Flask

   def create_app():
       app = Flask(__name__)

       @app.route('/')
       def home():
           return "Hello, Python!"

       return app

   if __name__ == "__main__":
       app = create_app()
       app.run(debug=True)
   ```

2. Create an empty `requirements.txt` file to manage dependencies:
   ```bash
   touch requirements.txt
   ```

3. Add Flask to `requirements.txt`:
   ```
   flask==2.3.2
   ```

> **⚠️ WARNING**: Ensure each dependency is version-pinned in `requirements.txt` to avoid compatibility issues.

---

### **Task 3: Configure Build Tools**

#### Step 3.1: Automate Dependency Management
Use `pip` or `pipenv` to manage project dependencies.

1. Install dependencies using `pip`:
   ```bash
   pip install -r requirements.txt
   ```

2. Alternatively, use `pipenv` for a virtual environment:
   ```bash
   pip install pipenv
   pipenv install flask
   ```

#### Step 3.2: Set Up Linting and Formatting Tools
1. Install popular Python tools:
   ```bash
   pip install black flake8
   ```

2. Run `black` for formatting:
   ```bash
   black .
   ```

3. Run `flake8` for linting:
   ```bash
   flake8 app/
   ```

---

## **4. Code Examples and Snippets**

### Sample Route in Flask (`routes.py`):
```python
from flask import Blueprint

bp = Blueprint('main', __name__)

@bp.route('/')
def index():
    return "Welcome to the migrated Python app!"
```

### Sample Test Case (`tests/test_app.py`):
```python
import pytest
from app import create_app

@pytest.fixture
def client():
    app = create_app()
    app.testing = True
    return app.test_client()

def test_home(client):
    response = client.get('/')
    assert response.data == b"Hello, Python!"
```

---

## **5. Common Pitfalls and How to Avoid Them**

1. **Inconsistent Python Versions**:
   - Ensure the same Python version is used by all developers.
   - Use `pyenv` for managing multiple Python versions.

2. **Misconfigured Virtual Environments**:
   - Always activate the virtual environment before installing dependencies:
     ```bash
     source venv/bin/activate
     ```

3. **Framework Mismatch**:
   - Choose the framework that aligns with your PHP application's complexity (Flask for small apps, Django for larger ones).

---

## **6. Testing Checklist for This Step**

- [ ] Python is installed and version verified.
- [ ] Flask is installed and functioning.
- [ ] Project structure mirrors the PHP application.
- [ ] Dependencies are pinned and installed without errors.
- [ ] A basic `app.py` file runs without issues.
- [ ] Unit tests execute successfully.

---

## **7. Validation Criteria**

- The development environment is ready for Python application development.
- The application runs a basic Flask server and responds to requests.
- All build tools are configured correctly and functional.

---

## **8. Troubleshooting Guide**

### Problem: `command not found: python`
**Solution**: Ensure Python is added to your system PATH. Restart your terminal after installation.

### Problem: Flask fails to import
**Solution**: Verify Flask is installed in your virtual environment:
   ```bash
   pip show flask
   ```

### Problem: Dependencies conflict
**Solution**: Use `pip-tools` to resolve dependency conflicts:
   ```bash
   pip install pip-tools
   pip-compile
   ```

---

## **9. Resources and References**

- [Python Official Documentation](https://docs.python.org/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [Virtual Environments Guide](https://docs.python.org/3/tutorial/venv.html)
- [pipenv Documentation](https://pipenv.pypa.io/)

---

## **10. Next Steps**

Proceed to **Step 3: Migrate Code** where you will begin converting PHP logic into Python functions and modules, ensuring parity with the source application.

> **Estimated Time**: 2-4 hours depending on the complexity of your project's environment setup.