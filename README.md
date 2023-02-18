# Virtual Environments in python
Virtual environments are used to create an isolated environment for Python projects. This means that each project can have its own dependencies, regardless of what dependencies every other project has.  
First of all let's review how to install, update, and uninstall packages in a virtual environment with pip.  
Note: The command specified below are for linux (ubuntu).

#### Installing packages
```bash
pip3 install {package_name}
pip3 install -r requirements.txt
```

#### Uninstalling packages
```bash
pip3 uninstall {package_name}
```

## Updating packages
```bash
pip3 install --upgrade {package_name}
``` 

#### Freezing packages
```bash
pip3 freeze > requirements.txt
```

## Creating a virtual environment
To create a virtual environment, you must use the `venv` module that comes with Python.

### venv (built-in module in python 3.3+)
```bash
python3 -m venv {env_name} # Run the venv module as a script with the -m option, passing the route where the virtual environment info will be store (environment name).
source {env_name}/bin/activate
pip3 install -r requirements.txt
pip3 freeze > requirements.txt
deactivate
```

### virtualenv (Python library)
```bash
pip3 install virtualenv
virtualenv --no-site-packages {env_name}
virtualenv --python=/usr/bin/python2.7 {env_name} # specify the python version, note that the python version must be installed in the system.
lsvirtualenv # list all the virtual environments
source {env_name}/bin/activate
pip3 install -r requirements.txt # All other pip commands work as usual
deactivate
rm -rf {env_name} # remove the virtual environment
```

### pipenv (Recommended)
```bash
pip3 install pipenv
cd {project_dir}
pipenv install -r requirements.txt
pipenv uninstall {package_name}
pipenv install -e git+repo_url@branch#egg=package_name # install a package from a git repo
pipenv shell
pipenv lock # creates a Pipfile.lock file
exit
```
Install all the packages in the Pipfile.lock file (replicates the environment)
```bash
pipenv install --ignore-pipfile
pipenv graph # shows the dependency graph
pipenv check # checks for security vulnerabilities
exit
```

## Isolating the versions of Python

Different projects may require different versions of Python. To isolate the versions of Python, you can use `pyenv` or `pyenv-virtualenv`.

### pyenv (does not work on windows)
It allows you to create virtual environments for different versions of python.  
`pyenv install --list` to see all the versions of python available.  
`pyenv versions` to see all the versions of python installed.  
`pyenv virtualenvs` to see all the virtual environments created.  
`pyenv global {version}` to set the global version of python. 
`pyenv local {version}` to set the local version of python.
`pyenv shell {version}` to set the shell version of python.

```bash
pip3 install pyenv
pyenv install {version}
pyenv virtualenv {env_name}
pyenv activate {env_name}
pyenv deactivate
```

### pyenv-virtualenv
```bash
pyenv install {version}
pyenv virtualenv {version} {env_name}
pyenv local {env_name}
```

### Anaconda
Anaconda is a free and open-source distribution of the Python and R programming languages for scientific computing, that aims to simplify package management and deployment.
```bash
wget -O anaconda.sh https://repo.anaconda.com/archive/Anaconda3-2022.10-Linux-x86_64.sh
bash anaconda.sh
#source ~/.bashrc
conda info
conda create --name {env_name} python={version} 
conda activate {env_name}
conda install {package_name}
conda list
conda update {package_name}
conda remove {package_name}
conda deactivate
conda remove --name {env_name} --all
conda create --name {env_name} --clone {env_name} # clone an environment
conda env export > environment.yml # export an environment
conda env create -f environment.yml # create an environment from a yml file
conda env list
conda env remove -n {env_name} # remove an environment
```
some useful commands
```bash
conda list # list all the packages installed in the current environment
conda list --explicit # list all the packages installed in the current environment with their versions
conda install --channel {conda-forge} {package_name} # install a package from a specific channel
conda list --revisions # list all the revisions of the environment
conda install --revision {revision_number} # install a specific revision of the environment
conda env export --from-history --file environment.yml # export the environment to a yml file
```

### Mamba
Mamba is a fast, drop-in replacement for the conda package manager. It is a C++ implementation of the conda package manager, that uses multi-threading and a sophisticated dependency solver to achieve maximum performance.  
```bash
conda install -c conda-forge mamba
mamba --help
mamba create --name {env_name} python={version}
mamba activate {env_name}
mamba install {package_name}
mamba list
mamba update {package_name}
mamba remove {package_name}
mamba deactivate
mamba env create -f environment.yml # create an environment from a yml file
```

### Docker
Docker is a containerization platform that allows you to create and run containers.  
Docker containers are isolated environments that run on a single host.  
To install docker on ubuntu, follow [this](https://docs.docker.com/engine/install/ubuntu/) tutorial. 

```bash
docker run -it --rm -v $(pwd):/app -w /app python:3.7 bash
```
#### Dockering a python script
```Dockerfile
FROM python:3.7
WORKDIR ["/app"]
COPY ["requirements.txt", "/app"]
RUN ["pip", "install", "--no-cache-dir", "upgrade", "-r", "requirements.txt"]
COPY ["app.py", "/app"] # copy the app.py file to the /app directory if you want to run the app
CMD ["python", "app.py"]
```
Alternatively, you can use docker-compose to build and run the docker container.

```docker-compose.yml
services:
    app-csv:
        build:
            context: .
            dockerfile: Dockerfile
        volumes:
            - .:/app
```

```bash
docker-compose build
docker-compose up -d
docker-compose ps
docker-compose logs
docker-compose exec app-csv bash
cd /app
python3 app.py
exit
docker-compose down
```

#### Dockering a flask app
```Dockerfile
FROM python:3.7
WORKDIR ["/app"]
COPY ["requirements.txt", "/app"]
RUN ["pip", "install", "--no-cache-dir", "upgrade", "-r", "requirements.txt"]
COPY ["/app", "/app"]
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "80"]
```
```docker-compose.yml
services:
    app-flask:
        build:
            context: .
            dockerfile: Dockerfile
        volumes:
            - .:/app
        ports:
            - "80:80"
```
```bash
docker-compose build
docker-compose up -d
docker-compose ps
```

### Recommendations
#### Divide and conquer
- Use `pyenv` to manage the versions of Python.
- Use `pipenv` to manage the packages of your project.


