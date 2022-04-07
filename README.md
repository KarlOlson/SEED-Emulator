# SEED-Emulator

# Setup (VM Method)
Setting up the SEED emulator is pretty straightforward. There are a few options here to approach with:

* Build your own custom lightweight VM
* Use the SEED security base VM (Ubuntu) with a few additions. 

If you are just exploring, the SEED security VM has a lot of built-in security vulnerabilities and components that are used by other SEED security labs. If you are just focused on the Internet Emulator, a clean build is the better approach.

To build a base VM with all the required components, first build your base machine with the following settings:

* Ubuntu 64
* RAM: 4GB (The more you can use, the larger environment you can simulate. Ideally, 16gb would allow for a few hundred systems of emulator)
* CPU: 2 CPUs (The emulator does not use much CPU, RAM is the limiting component for scale)
* HD: 8 GB

Once your base VM is built, run and install the following components:

* `sudo apt-get update`
* `sudo apt-get install docker`
* `sudo apt-get install docker-compose`
* `sudo apt-get install python3`
* Navigate to https://github.com/seed-labs/seed-emulator.git and download all files to your desktop or wherever.

# SEED Emulator Initial Setup

The emulator system comes mostly ready to run. There are a few commands you need to run to make sure environment is setup correctly:
* add the `SEEDemu` directory to PATH (run from Seed directory. make sure to use tic \` and not single quote ' around pwd): ```echo PYTHONPATH:"`pwd`:$PYTHONPATH"```

# Running SEED BGP Environment (Pre-built Examples)
The standard process to setup and run an environment is as follows:
* 1. Run the example python setup script for your chosen example(eg. for `A00-simple-peering` run the `simple-peering.py` file: `$ python3 simple-peering.py`). This creates a `\output` directory with a `docker-compose.yaml` file along with the individual folders that will make up the individual docker images for each device in your environment (eg. bgp router, web server, etc.) Within these folders is a `Dockerfile` of the individual component setup scripts which will get run in the next step.
* 2. Next, you create your docker images. From the `/output` directory, run: `$ docker-compose build` followed by `$docker-compose up`. This will launch your environment of networked systems within the VM.
* 3. In order to see/visualize these components, a seperate docker image is used to run the web fron/backend for the environment visualization. Go back to your seed root directory and go into the `/client` folder.  Run a `$ docker-compose build` followed by `$ docker-compose up`. Once everything is built/loaded you can navigate to: `http://127.0.0.1:8080/map.html` to view your simulation and interact with devices graphically. Alternatively, `http://127.0.0.1:8080` will present the device dashboard.

![alt text](https://github.com/KarlOlson/SEED-Emulator/blob/main/Simple-peer.png "Web Environment")

# Interacting with the SEED environment
From your web frontend, you can click on devices and conduct basic actions like ping, etc. If you want to visualize how this is connecting across the network, the top of the page has a `filter` line. Filter by `ICMP` and it will trace the route through the network. From here you can take devices offline and see how the routing topology reacts to find a new path (or not). Re-enable the device to return to normal operation.

# Building custom topologies
Everything within the SEED environment can be built through a simple python script. The script is divided into 5 key components which are each layered together to provide the full emulation environment:

* Emulation layer (Docker-compose): establishes initial emulation environment composed of interconnected docker containers
* Base layer(Docker container): defines the individual docker container initial environment and the necessary components
* Routing layer (BIRD setup): initializes the interconnectivity between devices (links everything together via docker-compose environment)
* Protocol layer (BIRD configuration): initializes device routing protocols (eg. bgp, ospf, etc) for devices
* Device/service layer: adds additional capabilities to the simulation environment such as Web services, DNS, etc.

To build a custom envionment you simply define within the python script the components you need and how they should be linked together. Recommend looking at the `simple-peering.py` in the `A00-simple-peering` example for an easy to understand setup process/workflow. 


