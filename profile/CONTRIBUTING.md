# Contributing to DigitalWaters-fi

Welcome! 👋  
We appreciate your interest in contributing to **DigitalWaters-fi**. Contributions can include code, documentation, data, models, or discussions. By following this guide, you help keep our projects organized, consistent, and high-quality.

---

## Table of Contents
1. [How to Contribute](#how-to-contribute)
2. [Reporting Issues](#reporting-issues)
3. [Submitting Pull Requests](#submitting-pull-requests)
4. [Code Style Guidelines](#code-style-guidelines)
5. [Process Models](#process-models)
6. [Data Contributions](#data-contributions)
7. [Community Code of Conduct](#community-code-of-conduct)

---

## How to Contribute

You can contribute in several ways:

- **Report bugs or request features** by opening an [issue](https://github.com/DigitalWaters-fi/REPO-NAME/issues).
- **Participate in discussions** in our [community repo](https://github.com/DigitalWaters-fi/community).
- **Improve documentation** by suggesting edits or submitting pull requests.
- **Contribute code, data, or models** following the rules below.

---

## Reporting Issues

When reporting an issue:

1. Check if the issue already exists. If so, add details to the existing thread.
2. Include a **clear title** and **description**.
3. Include **steps to reproduce** (for bugs) or detailed context (for feature requests).
4. Tag it appropriately (bug, enhancement, documentation, data, model, etc.).

---

## Submitting Pull Requests (PRs)

1. Fork the repository and clone your fork:
   ```bash
   git clone https://github.com/YOUR-USERNAME/REPO-NAME.git
   ````
2. Create a new branch for your contribution:

   ```bash
   git checkout -b feature/awesome-feature
   ```
3. Make your changes and commit with a clear message:

   ```bash
   git commit -m "Add feature X to improve Y"
   ```
4. Push your branch to your fork:

   ```bash
   git push origin feature/awesome-feature
   ```
5. Open a pull request against the `main` branch of the original repository.
6. Ensure your changes adhere to **code style**, **data**, and **modeling guidelines** below.

---

## Code Style Guidelines

To keep our projects consistent, please follow the guidelines for the language you are contributing in.

### **Julia**

* **Naming:**

  * Modules & types: `CamelCase` (e.g., `MyModule`)
  * Functions & variables: `snake_case` (e.g., `compute_average`)
  * Constants: `UPPERCASE` (e.g., `MAX_ITERATIONS`)
* **Indentation:** 4 spaces, no tabs
* **Line length:** ≤ 100 characters
* **Comments & docstrings:** Use `#` for inline comments, `""" """` for function/module docstrings
* **File organization:** One module per file, use submodules if needed
* **Best practices:** Avoid globals, prefer broadcasting, explicit typing for clarity

### **R**

* **Naming:**

  * Functions & variables: `snake_case` (e.g., `compute_average`)
  * Classes (S3/S4): `CamelCase`
  * Constants: `UPPERCASE`
* **Indentation:** 2 spaces
* **Line length:** ≤ 80 characters
* **Comments & documentation:** `#` for inline, `roxygen2` style for functions
* **Best practices:** Use vectorized operations, avoid globals, split scripts into functions

### **Python**

* **Naming:** Follow [PEP8](https://www.python.org/dev/peps/pep-0008/)

  * Functions & variables: `snake_case`
  * Classes: `CamelCase`
  * Constants: `UPPERCASE`
* **Indentation:** 4 spaces
* **Line length:** ≤ 79 characters
* **Comments & docstrings:** `#` for inline, triple quotes `""" """` for docstrings
* **Best practices:** Use meaningful names, modular code, type hints when possible

---

## Process Models

All **process models** contributed must:

* Adhere to the **OpenMI standard**
  ([Open Modeling Interface](https://www.openmi.org/))
* Include documentation explaining model inputs, outputs, and assumptions
* Be version-controlled and tested where possible

---

## Data Contributions

All **data contributions** must:

* Follow the **data models and quality standards** outlined in the [Data Quality Handbook](https://www.wiley.com/en-us/Data+Quality+Handbook-p-9780471369309)
* Include metadata: source, date, units, quality checks performed
* Use standard formats (CSV, NetCDF, JSON, etc.) with consistent naming

---

## Community Code of Conduct

We aim to create a **welcoming and inclusive community**. By participating, you agree to follow our [Code of Conduct](CODE_OF_CONDUCT.md):

* Be respectful and kind
* Avoid harassment, discrimination, or personal attacks
* Engage constructively, even in disagreements

---

## Thank You!

Your contributions make **DigitalWaters-fi** stronger and more impactful.
Thank you for your time, effort, and ideas.

```


If you want, I can also **add a “Discussion & Idea Submission” section** so non-code contributors know exactly how to participate in your community repo. This is great for open-source orgs with non-developer contributors.  

Do you want me to do that?
```
