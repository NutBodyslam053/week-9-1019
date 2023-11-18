# week-9-1019
NutBodyslam053@TCC231019


```bash
name: Python application

on:
  push:
    branches: [ "develop" ]
  pull_request:
    branches: [ "develop" ]
  # schedule:
  #   - cron: '37 3 * * *'

permissions:
  contents: read

env:
  PYTHON_VERSION: "3.10"
  REGISTRY: docker.io
  
jobs:
  build:
    runs-on: ubuntu-latest
    # runs-on: self-hosted
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
      with:
        ref: develop  # This line specifies the branch to checkout
        fetch-depth: 0  # Fetch all history for the branch
        clean: true  # Clean the workspace before checking out the code
    
    - name: Set up Python
      uses: actions/setup-python@v3
      with:
        python-version: ${{ env.PYTHON_VERSION }}
        
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 pytest
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
        
  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
      with:
        ref: develop  # This line specifies the branch to checkout
        fetch-depth: 0  # Fetch all history for the branch
        clean: true  # Clean the workspace before checking out the code

    - name: Lint with flake8
      run: |
        # stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
        
    - name: Test with pytest
      run: |
        pytest

  pack:
    runs-on: ubuntu-latest
    needs: ['build', 'test']
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
      with:
        ref: develop  # This line specifies the branch to checkout
        fetch-depth: 0  # Fetch all history for the branch
        clean: true  # Clean the workspace before checking out the code

    - name: Build & Push Docker image
      uses: mr-smithers-excellent/docker-build-push@v6
      with:
        image: nutbodyslam053/${{ github.repository }}
        tags: ${{ github.sha }}
        registry: ${{ env.REGISTRY }}
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

```