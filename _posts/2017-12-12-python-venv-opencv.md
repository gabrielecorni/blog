---
title: Python Cheatsheet (+OpenCV)
tags: [Notes, HowTo, Python]
style: fill
color: success
description: Set your Python environment up and start coding like a Jedi.
---

![Alt image](https://gabrielecorni.github.io/blog/docs/2017-12-12-python-venv-opencv/py_cv_logo.png)

Do not mess with default versions, install every version you need in virtual environments.
The typical guidelines to follow are:
> Tips:
> Create one virual environment per project.
> Install only the needed package, to export the proper requirements.

## First of all...
* Install python `sudo apt-get install pythonX.Y`
* Install pip `sudo apt-get install pythonX-pip`
* Install git `sudo apt-get install git`
* Check python version `pythonX --version`
* Check the python version pip is linked to
    ```$(head -1 `which pip` | tail -c +3) --version
    ```
* Install virtualenv `sudo pip install virtualenv`

## Create a virtual environment
1. Create the root folder containing all the virtual environments in the desired path. 
```mkdir /home/thisUser/py_envs```
2. Move into the root folder. 
```cd /path/to/the/root```
3. Create a virtual environment. 
```virtualenv -p pythonX virtualenv_name```

## Activate a virtual environment
Open a terminal wherever you like and type:

    source /path/to/the/env/bin/activate

The command line should now begin with your virtualenv's name in brackets: that means you are now working inside your virtual environment.

A smart idea could be register a persistent alias to automatically run your virtualenv.
To do so:

    gedit ~/.bash_aliases
    alias alias_name=”/path/to/the/env/bin/activate”
    save file
    (reboot system, useful but not mandatory)

## Install/Uninstall a package
Please, remember to activate your virtualenv before installing a package with pip, otherwise it will be installed in the system directories!

To install a package: 

    pip install package_name

To uninstall a package:
    
    pip uninstall package_name

> Remember: when using a virtual environment all the installed packages remain local, since they are installed inside the virtualenv. Hence they won't be visible on the outside.

Some useful packages are:

* numpy
* scipy
* scikit-learn
* matplotlib
* pandas
* tensorflow
* keras

## Deactivate a virtual environment
From the command line where the virtual environment is open, just type:
    
    deactivate

## Display and export package requirements
You can visualize all the packages installed inside a virtual environment:
    
    pip freeze

To export the requirements as a file (i.e. when releasing the project):
    
    pip freeze > requirements.txt

## Remove a virtual environment
To remove a virtual environment, it is needed to remove the installed packages first.

    pip freeze > requirements.txt
    pip uninstall -r requirements.txt -y
    deactivate
    rm -r "path/to/the/env"

## Using virtualenvwrapper

First of all install virtualenvwrapper: `sudo pip install virtualenvwrapper`

If the installation fails because of `six`, just type: `sudo pip install virtualenvwrapper --ignore-installed six`

Now set up your .bash_profile:

* open it with nano: `nano .bash_profile`
* add the following lines after the `PATH` statement:

```
# set where virutal environments will live
export WORKON_HOME=$HOME/.virtualenvs
    
# ensure all new environments are isolated from the site-packages directory
export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'

# use the same directory for virtualenvs as virtualenvwrapper
export PIP_VIRTUALENV_BASE=$WORKON_HOME

# makes pip detect an active virtualenv and install to it
export PIP_RESPECT_VIRTUALENV=true

if [[ -r /usr/local/bin/virtualenvwrapper.sh ]]; then
    source /usr/local/bin/virtualenvwrapper.sh
else
    echo "WARNING: Can't find virtualenvwrapper.sh"
fi
```

* create a virtualenv with the `mkvirtualenv` command:

```
(python3) mkvirtualenv --python=`which python3` env_name
(python2) mkvirtualenv env_name
```

* delete a virtualenv: `rmvirtualenv env_name`
* activate environment: `workon env_name`
* deactivate environment: `deactivate`

## Python IDEs
You can easily code from **gedit**, **atom** or **sublime text**, but it is also possible to run code directly from the **command line**.

A cool and web-based possibility is **Jupyter**, a web editor based on **IPython**. To use it, from within your virtualenv, type:

	pip install IPython Jupyter
	python -m ipykernel install --user --name=[my_kernel_name]
	jupyter notebook

Your web browser will load Jupyter homepage. Click on "Kernel" and select the one corresponding to your virtualenv, and start to code directly inside the browser (super useful if your machine is on the cloud --i.e. Amazon AWS EC2 has some free VMs accessible through ssh--).

A good (and free) alternative is [PyCharm](https://www.jetbrains.com/pycharm/).
The real advantage of using PyCharm is the availability of intellisense when coding.
Once you have opened your project, you can link your virtual environment as follows:

    File > Settings > Project:<project_name > Project Interpreter > Explode the upper ‘Project Interpreter’ combobox > ‘Show All’ > ‘+’ >  Add Local > Enter the path of your virtualenv's ‘bin’ folder > Select a python installation (i.e. pythonX) > OK

If everything went fine, under *External libraries* the selected virtualenv should be visible.

## Install OpenCV
This is a bit tricky. There are basically two possibilities:

**Using pip:** this will work fine but you won't have access to the video camera features.

    pip install opencv-contrib-python
    pip uninstall opencv-contrib-python

**Using cmake:** this will give you access to all the features, but you will need to compile opencv from the [source code](https://github.com/opencv/opencv/archive/3.3.0.tar.gz) (main guide [here](https://medium.com/@manuganji/installation-of-opencv-numpy-scipy-inside-a-virtualenv-bf4d82220313)).

- In the environment you want to install opencv into, create a folder called *local*.
- Extract opencv's source code in the location that you prefer.
- Open the extracted opencv folder and create an additional folder called *release*.
- Move inside the *release* folder you just created and activate the virtualenv.

Now run:    
    
    sudo apt-get install cmake libgtk2.0-dev pkg-config
    
    cmake -D MAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=$VIRTUAL_ENV/local/ -D PYTHON_EXECUTABLE=$VIRTUAL_ENV/bin/python -D PYTHON_PACKAGES_PATH=$VIRTUAL_ENV/lib/python3.5/site-packages -D INSTALL_PYTHON_EXAMPLES=ON ..
    
    make -j $(nproc --all)
    
    make install

After the installation, simply ```import cv2``` in your source code and start using opencv's features.

Here is a small python script that can be run to test whether opencv is correctly installed or not.

    import cv2
    cap = cv2.VideoCapture(0)
    limit = 200
    cont = 0
    while cont < limit:
        ret, frame = cap.read()
        cont = cont+1   
        cv2.imshow('camera', frame)
        print('overall progress: {0}% ({1}/{2})'.format(cont/limit*100, cont, limit))
        cv2.waitKey(1)
        

