# CONTENTS OF THIS FILE

* [1. Introduction](#introduction)
* [2. System Requirements](#system-requirements)
* [3. Repository Contents](#repo)
* [4. Installation Guide](#installation)
* [5. Experiment Replication Guide](#experiment)
  * [(i) Evaluation of our approach (without learning strategy)](#activity1)
  * [(ii) Evaluation of our approach (with learning strategy)](#activity2)
  * [(iii) Comparison of performances](#activity3)
* [6. Technical Details](#technical-details)
  * [(i) Docker Images](#docker-images)
  * [(ii) Original Environment Settings](#environment)
* [7. Contributors](#contributors)


## 1. <a name="introduction"></a>INTRODUCTION

In our paper, we present a bot-assisted approach to allow modellers to perform domain modelling quickly and interactively. Furthermore, the approach uses an incremental learning strategy empowered by Machine Learning (ML) to improve the accuracy of the bot’s suggestions and extracted domain models by analyzing practitioners’ decisions over time. We build a prototype of our approach in the form of a domain modelling bot. The architecture of the bot is based on four micro-services to enable automated and interactive domain modelling with incremental learning. First, we use a React-based frontend that provides a web-based interface to allow modellers to enter problem descriptions in natural language, as well as interact with the extracted models. Second, we use a Django-based backend to extract domain models from problem descriptions using Natural Language Processing (NLP) and ML techniques. Third, we use a conversational agent empowered by RASA to provide suggestions to modellers and collect their decisions. Finally, we use an action server empowered by RASA to make proactive changes in the extracted models based on the modeller's decisions. Finally, to facilitate the evaluation of our bot, we use Docker Compose to configure and start multiple Docker containers for the four microservices on the same host. Furthermore, based on the focus of our approach in the paper, we organize the evaluation tasks into three activities -- (a) evaluating domain models extracted from our approach without incremental learning, (b) evaluating domain models extracted from our approach with incremental learning, and (c) comparison of the results obtained from the first two activities (a and b).

We provide all the necessary artifacts and documentation on our [GitHub page](https://github.com/Rijul5/bot-artifact-evaluation-22). This repository is open and public.


## 2. <a name="system-requirements"></a>SYSTEM REQUIREMENTS

* 64-bit kernel and CPU support for virtualization
* At least 8 GB of RAM.
* Docker (Follow installation instructions from the [official page of Docker](https://docs.docker.com/engine/install/) based on your operating system)


## <a name="repo"></a>REPOSITORY CONTENTS

We provide two Docker Compose files (YAML format) as mentioned below. Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, we use a YAML file to configure the four microservices of our application. Each service of our application is already deployed on [Docker Hub](https://hub.docker.com/) in the form of a Docker image. In each configuration, we provide these image locations to build corresponding microservices. Finally, we create and start all the services in a configuration using a single command.  

1. __docker-compose-hub-1.yml__
2. __docker-compose-hub-2.yml__



## 3. <a name="installation"></a>INSTALLATION GUIDE

To use the provided Docker Compose files, follow the below steps:
1. Download the Docker Compose files in a directory on your system.
2. Open the command line terminal and navigate to the directory.
3. Use the below command to run Docker containers using __docker-compose-hub-1.yml__ (for example)

```console
docker-compose -f docker-compose-hub-1.yml up --build
```
4. Open a browser and use the link __http://localhost:3000/__ to launch our web-based tool (bot).



## 4. <a name="experiment"></a>EXPERIMENT REPLICATION GUIDE

Based on our approach and its evaluation in the paper, we organize the experiment replication tasks into three activities. As the experiment involves the use of ML models, the outcomes may differ every time these activities are performed. Therefore, we focus on the relative comparison of performances of our approach across these activities rather than the absolute comparison. Moreover, the evaluators mimic the role of a modeller while performing these activities as we explain in the below steps. 

### __(i) <a name="activity1"></a>Evaluation of our approach (without learning strategy)__

  * First, we run the containers using __docker-compose-hub-1.yml__ with the below command and launch the bot in a browser using the link __http://localhost:3000/__.

    ```console
    docker-compose -f docker-compose-hub-1.yml up --build
    ```
    
  * Second, the modeller clears the memory of trace models for previous domain problems (if any) by clicking the _Clear Problem Stack_ button and disables incremental learning for the first activity by toggling off the _Incremental Learning_ radio button.
    
  * Third, we provide the _problem description PD1_ in the _Domain Problem Description_ box of our bot and extract the corresponding domain model by clicking the _Extract Model_ button.


    > **_Problem Description (PD1):_**  A company is comprised of two to eight departments. Each department has an ID and email. A department hires employees for certain projects. Employees working on projects can be temporary or permanent employees. Each employee is identified by a name, email, employee ID, employee number. Projects can be of types - production projects, research projects, education projects, and community projects. All projects have a title, description, budget amount, and deadline. In addition , each education project and community project are associated with one funding group. The funding group can be of types - private group, government group, or mixed group. The production projects are characterized by a site code.

  *  Fourth, we evaluate the accuracy of extracted domain model using the below metrics. We term the accuracy of the extracted domain model in this activity, __AC1__. The objective of this step is to evaluate configurations generated for decision points in a domain model. A decision point represents a scenario that can be modelled by one or more relationships.

  * Fifth, we change the two decision points as mentioned below if the _Current Configurations_ (generated solutions) are not the same as the _Expected Configurations_ (See Table 1). Also, we count the manual steps required to perform these modifications (bringing the state of extracted domain model closer to the expected state). We call these manual steps, __MS1__. As shown in Table 1, there are multiple possible actions which could be performed. The bot considers each modeller's action and presents the other alternative configurations (bot's suggestions) which closely match the modeller’s actions (based on weighted steps). A modeller then accepts one of the configurations from the bot's suggestions. The accepted configuration represent modeller's preference for a configuration among possible configurations to model a scenario in domain modelling. Finally, the bot proactively updates the decision point (domain model) with the configuration selected by the modeller. 

  
  **Table 1: Required Modifications in Configurations**


| Number 	|                      Decision Point                      	| Expected Configuration 	| Current Configuration 	|                                                                                                        Possible Actions (in Extracted Domain Model)                                                                                                        	|
|:------:	|:--------------------------------------------------------:	|:----------------------:	|:---------------------:	|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:	|
|    1   	| FundingGroup – PrivateGroup, MixedGroup, GovernmentGroup 	|     Generalization     	|      Enumeration      	| (a) Bring PrivateGroup, MixedGroup, or GovernmentGroup outside of the Enumeration Class "FundingGroupType", (b) Delete Enumeration Class "FundingGroupType" or the attribute "type: FundingGroupType", or (c) create a new class such as "GovernmentGroup" 	|
|    2   	| Employee – TemporaryEmployee, PermanentEmployee          	|       Player-Role      	|      Enumeration      	| (a) Bring TemporaryEmployee or PermanentEmployee outside of the Enumeration Class "EmployeeType", (b) Delete Enumeration Class "EmployeeType" or the attribute "type: EmployeeType", or (c) create a new class such as "TemporaryEmployee"                 	|
|    3   	| Employee – TemporaryEmployee, PermanentEmployee          	|       Player-Role      	|      Sub-Classes      	| (a) Delete the superclass "Employee"                                                                                                                                                                                                                       	|
  * Finally, a modeller clicks _Save for Training_ button so that the bot can collect the bot-modeller interactions data and re-train the incremental ML learning model online. 
    
   > **_Note:_**  If the modeller now again extracts the domain model for _problem description PD1_ after refreshing the page in the browser and clearing the memory of trace models for previous domain problems (if any) by clicking the _Clear Problem Stack_ button, then the extracted domain model should already have some or all the configurations same as _Expected Configuration_. This is due to the incremental learning performed by the bot online. 

### __(ii) <a name="activity2"></a>Evaluation of our approach (with learning strategy)__

As it is time-consuming and labor-intensive to re-train and package the new version of models obtained from _Activity 1_, we create __docker-compose-hub-2.yml__ file that builds and starts the containers which are associated with the trained version of models that we obtain from _Activity 1_ in real-time. A modeller performs the below steps for _Activity 2_.

  * First, a modller runs the containers using __docker-compose-hub-2.yml__ with the below command and launches the bot in a browser using the link __http://localhost:3000/__.  

    ```console
    docker-compose -f docker-compose-hub-2.yml up --build
    ```
    
  * Second, the modeller clears the memory of trace models for previous domain problems (if any) by clicking the _Clear Problem Stack_ button and enables incremental learning for this activity by toggling on the _Incremental Learning_ radio button.

  * Third, the modeller again provides the problem description (PD1) in the _Domain Problem Description_ box of our bot and extracts the corresponding domain model by clicking the _Extract Model_ button.

  * Fourth, the modeller evaluates the accuracy of the extracted domain model using the below metrics. We call the accuracy of the extracted domain model in this activity, __AC2__. The objective of this step is to evaluate configurations generated for decision points in a domain model. A decision point represents a scenario that can be modelled by one or more relationships.

  * Fifth,  as shown in Table 1, the modeller changes the two decision points if the _Current Configurations_ (generated solutions) are not the same as the _Expected Configurations_. Also, the modeller counts the manual steps required to perform these modifications (bringing the state of extracted domain model closer to the expected state). We call these manual steps, __MS2__. As shown in Table 1, there are multiple possible actions which could be performed. The bot considers each modeller's action and presents the other alternative configurations (bot's suggestions) which closely match the modeller’s actions (based on weighted steps). A modeller then accepts one of the configurations from the bot's suggestions. The accepted configuration represents modeller's preference for a configuration among possible configurations to model a scenario in domain modelling. Finally, the bot proactively updates the whole decision point (domain model) with the configuration selected by the modeller. 


### __(iii) <a name="activity3"></a>Comparison of performances__
In this activity, we compare the results obtained from _Activity 1_ and _Activity 2_.

* First, we compare the accuracy of extracted domain models obtained from _Activity 1_  and _Activity 2_. As we use different weights of underlying models (version in _Activity 1_ and version in _Activity 2_), there should be some differences in their accuracy. The expected outcome: __AC1__ < __AC2__

* Second, we compare __MS1__ and __MS2__. The expected outcome: __MS1__ > __MS2__


## 6. <a name="technical-details"></a>TECHNICAL DETAILS
The architecture of the bot is based on four micro-services to enable automated and interactive domain modelling with incremental learning. We build a separate Docker image for each service. These images are further used by the two Docker Compose files. In this section, we provide more technical details about these Docker images. Also, we provide system/environment settings where our approach was successfully evaluated.

### __(i) <a name="docker-images"></a>Docker Images__
We now provide details about the libraries/frameworks used and their respective versions for each Docker image.

| Microservice       | Image Name | Image Location| Base Image | Underlying Libraries/Frameworks (with version details) | 
| ------------       | ----------- |----------- |----------- |----------- |
| Frontend           | frontend-test | [rijulsaini/frontend-test](https://hub.docker.com/repository/docker/rijulsaini/frontend-test) | node:14.17.5-alpine | [package.json](https://github.com/Rijul5/bot-artifact-evaluation-22/blob/main/image-dependencies/package.json)|
| Backend            | backend-test  | [rijulsaini/backend-test](https://hub.docker.com/repository/docker/rijulsaini/backend-test) | python:3.8-slim-buster | [backend-requirements.txt](https://github.com/Rijul5/bot-artifact-evaluation-22/blob/main/image-dependencies/backend-requirements.txt) |
| RASA               | rasa-test     | [rijulsaini/rasa-test](https://hub.docker.com/repository/docker/rijulsaini/rasa-test) | rasa/rasa:3.1.0 | [rasa-requirements.txt](https://github.com/Rijul5/bot-artifact-evaluation-22/blob/main/image-dependencies/rasa-requirements.txt)  | 
| RASA Action Server | rasa-action-test | [rijulsaini/rasa-action-test](https://hub.docker.com/repository/docker/rijulsaini/rasa-action-test) | rasa/rasa-sdk:3.1.1 | [rasa-action-requirements.txt](https://github.com/Rijul5/bot-artifact-evaluation-22/blob/main/image-dependencies/rasa-action-requirements.txt) |

### __(ii) <a name="environment"></a>Original Environment Settings__

| Category    | Specification |
| ----------- | ----------- |
| Machine      | ThinkPad T470 laptop       |
| Operating System   | Ubuntu (20.04.2 LTS)  |
| RAM      | 20Gb       |
| Processor   | Intel i7 processor 2.70GHz   |


## 7. <a name="contributors"></a>CONTRIBUTORS
-----------

 * __[Rijul Saini](http://www.ece.mcgill.ca/~rsaini6/)__ _rijul.saini@mail.mcgill.ca_
 * __[Professor Gunter Mussbacher](http://www.ece.mcgill.ca/~gmussb1/)__ - _gunter.mussbacher@mcgill.ca_
 * __[Professor Jin L.C. Guo](https://www.cs.mcgill.ca/~jguo/)__ - _jin.guo@mcgill.ca_
 * __[Professor Joerg Kienzle](https://www.cs.mcgill.ca/~joerg/Home/Jorgs_Home.html)__ - _joerg.kienzle@mcgill.ca_
