# O-RAN-note
# ns3 with O-RAN near-RT RIC installation guide

## Reference:
* Open RAN gym
    * https://openrangym.com/tutorials/ns-o-ran
* guide note
    * https://hackmd.io/@Estif/H1CteAXXT#-Setup-and-Use-ns-O-RAN-Platform-with-a-near-RT-RIC
    * https://hackmd.io/@Richard-quan/Byry8uuNa#Assignments3--ns-3-Open-RAN
* [My ns3 with O-RAN near-RT RIC note](https://hackmd.io/ZCa_QvNZQxSvxBHi3YUKvw?both)

## [Install ns-3 needed tools](https://hackmd.io/dHJkmNsMTWGnLZ3idaY_gA)

### Installation environment:

* OS : Ubuntu 20.04.6 LTS
* CPU : Intel® Core™ i7-9750H
* Stroge : 40GB
* Memory : 6GB
* gcc : 9.4.0
* Python : 3.8.10
* CMake version : 3.21.3
* GNU Make : 4.2.1
* ninja : 1.10.0
* Git version : 2.25.1
* tar (GNU tar) : 1.30
* bzip2 : 1.0.8


## Download the sim-ns3-o-ran-e2

Clone the sim-ns3-o-ran-e2:
```shell=
cd workspace
git clone https://github.com/o-ran-sc/sim-ns3-o-ran-e2.git
```
![image](https://hackmd.io/_uploads/SJv-ujIYT.png)


## Download the sim-e2-interface

* This module can be used with **an extension of the ns3-mmWave module** that will soon be released. The module needs to be cloned in the contrib folder. **The e2sim library needs to be installed**.

![image](https://hackmd.io/_uploads/BJZidsLK6.png)


## ns3 with O-RAN near-RT RIC

### Near-RT RIC Setup

* This part of the tutorial requires a working version of Docker for hosting the RIC on your localhost.

* First start by cloning and setup the Colosseum’s near-RT RIC.
```shell=
git clone -b ns-o-ran https://github.com/wineslab/colosseum-near-rt-ric
cd colosseum-near-rt-ric/setup-scripts
```
![image](https://hackmd.io/_uploads/r16zojIKp.png)

==Install docker, will be use later.==
```shell=
sudo apt update
sudo apt install docker.io
```
![image](https://hackmd.io/_uploads/S1ki0sUKp.png)

![image](https://hackmd.io/_uploads/HkghAo8tp.png)

* Then we first import all the images we need in Docker, we tag them properly and we build and launch them:
```shell=
sudo ./import-wines-images.sh  # import and tag
```
![image](https://hackmd.io/_uploads/SyzXe38FT.png)
```shell=
sudo ./setup-ric-bronze.sh  # setup and launch
```
![image](https://hackmd.io/_uploads/rJU4lhUtp.png)

* After the below step, the main entities of the RIC should be up and running in different Docker containers (this can be easily **checked with the ```docker ps``` command**). To understand what is going on in the RIC and to have a feedback once we start ns-O-RAN, **we can open two terminal for the RIC, one for logging the values on the E2Term and check the E2AP messages exchange, the other for the xApp**.
* **Terminal 1:**

```shell=
## Terminal 1 : logs the E2Term
sudo docker logs <e2term> -f --since=1s 2>&1 | grep gnb:
## <e2term> need to use *docker ps* to check
## this will help to show only when a gnb is interacting
## this Terminal is for logging the values on the E2Term 
## and check the E2AP messages exchange
```
* check e2term ID:
```shell=
sudo docker ps
```
![image](https://hackmd.io/_uploads/BJlu8_DDFp.png)
**--> e2term ID : 2a38c34656b2**

### -->==sudo docker logs 2a38c34656b2 -f --since=1s 2>&1 | grep gnb:==


* **Terminal 2:**
```shell=
## Terminal 2 : builds and run the x-app container
cd colosseum-near-rt-ric/setup-scripts
sudo ./start-xapp-ns-o-ran.sh
## this Terminal is for the xApp
```
![image](https://hackmd.io/_uploads/BkHBkuvYa.png)

* Check docker ID or Name
```shell=
sudo docker ps 
```
![image](https://hackmd.io/_uploads/r1gE1_PY6.png)
**--> sample-xapp ID : 59db17607574**

```shell=
sudo docker exec -it <CONTAINER_ID_OR_NAME> /bin/bash
## CONTAINER_ID_OR_NAME = 59db17607574
```

* run xapp
```shell=
cd /home/sample-xapp
./run_xapp.sh
```
![image](https://hackmd.io/_uploads/S1udx_wFa.png)



### ns-O-RAN Setup__e2sim

* First start with the installation of the prerequisites. In Ubuntu 20.04 LTS, can be installed with(If you do not install [**ns-3 needed tools**](https://hackmd.io/B4DXG46nTNuWXaMflbzOFA#Install-ns-3-needed-tools)):
```shell=
sudo apt-get update
# Requirements for e2sim
sudo apt-get install -y build-essential git cmake libsctp-dev autoconf automake libtool bison flex libboost-all-dev 
# Requirements for ns-3
sudo apt-get install g++ python3
```
![image](https://hackmd.io/_uploads/ry18Z_DY6.png)

![image](https://hackmd.io/_uploads/HJbwN_vYa.png)

* Then we can clone and install the e2Sim software. To see the E2 ASN messages on the e2sim, we build it with **LOG_LEVEL equal to 3 (DEBUG)**.

```shell=
## create a folder called oran-e2sim
git clone https://github.com/wineslab/ns-o-ran-e2-sim oran-e2sim 
```
![image](https://hackmd.io/_uploads/S1VLwuvt6.png)

```shell=
cd oran-e2sim/e2sim/
mkdir build
./build_e2sim.sh 3
```
![image](https://hackmd.io/_uploads/r1JhvuvKT.png)

![image](https://hackmd.io/_uploads/SJQcDuwK6.png)

* This last command shall configure the cmake project and install the e2sim on the system. Its main actions are also in the aforementioned Dockerfile that we report here to clarify the operations conducted by the script:

```shell=
# Creation and cd on the build directory
cd ~/workspace/oran-e2sim/e2sim
mkdir -p build
cd build
# build of the project with the LOG_LEVEL desired
sudo cmake .. -DDEV_PKG=1 -DLOG_LEVEL=${log_level_e2sim}
```
![image](https://hackmd.io/_uploads/HJ2EuODYp.png)

```shell=
sudo make package # Creation of the package
```
![image](https://hackmd.io/_uploads/ByvI_uDKa.png)

```shell=
echo "Going to install e2sim-dev"
## Installation of the generated package on the system
sudo dpkg --install ./e2sim-dev_1.0.0_amd64.deb 
## library update to make the package linkable 
## from ns-3 without rebooting the machine
sudo ldconfig  
```
![image](https://hackmd.io/_uploads/B1kauODKa.png)


### ns-O-RAN Setup__ns3-mmWave

* clone and install the ns3-mmWave project:

```shell=
git clone https://github.com/wineslab/ns-o-ran-ns3-mmwave ns-3-mmwave-oran
cd ns-3-mmwave-oran
```
![image](https://hackmd.io/_uploads/SkhrFuPtT.png)

* At the time of writing this tutorial, this project supports the 3.36 version of ns-3, thus we still use the old waf toolchain to configure, build and run ns-3. We plan to upgrade our version of ns-3 in the near future and, as a consequence of this, we will also update the guide.
* After this step, we can clone the ns-O-RAN module and insert it in the ns3-mmWave project in the contrib directory:
```shell=
cd ns-3-mmwave-oran/contrib
git clone -b master https://github.com/o-ran-sc/sim-ns3-o-ran-e2 oran-interface
cd ..  # go back to the ns-3-mmwave-oran folder
```
![image](https://hackmd.io/_uploads/BJC6YOPFT.png)

* Now have all the software in place to configure and build ns-3:
```shell=
./waf configure --enable-examples --enable-tests
```
![image](https://hackmd.io/_uploads/rk7f5uvtp.png)

```shell=
./waf build
```
![image](https://hackmd.io/_uploads/H1CFTdvt6.png)


### Run Scenario Zero

* Finally, we can run an example ns-3 scenario called **“Scenario Zero”**.
* This scenario features a Non Stand Alone (NSA) 5G setup in which we have one LTE eNB positioned in the center of the scenario and four gNBs around it with an inter site distance of 1000 between the eNB and each gNB.
* We can run the scenario with the command:
```shell=
## in ns-O-RAN terminal
cd workspace/ns-3-mmwave-oran
./waf --run scratch/scenario-zero.cc
```

```shell=
## in E2term terminal
#E2term LOG:
sudo docker logs e6d52081a052 -f --since=1s 2>&1 | grep gnb:
```
```shell=
## in xAPP terminal
#Run xAPP :
cd /home/sample-xapp
./run_xapp.sh
```
* **Near-RT RIC E2term:**
![image](https://hackmd.io/_uploads/Syu3lcitp.png)
* **Xapp:**
![image](https://hackmd.io/_uploads/rkK-Zqsta.png)
* **ns-O-RAN:**
![image](https://hackmd.io/_uploads/HJ2Nb9oF6.png)
* **Dissected Packets from the Sample Scenario**
![image](https://hackmd.io/_uploads/r1axfnoFa.png)


## Demo video:
 **[ns3 with O-RAN near-RT RIC installation guide](https://youtu.be/zBnWT_FTd3A)**
