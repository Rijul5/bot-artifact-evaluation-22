# CONTENTS OF THIS FILE

 * Introduction
 * System Requirements
 * Repository Contents
 * Installation Guide
 * Experiment Replication Guide
 * Contributors


## INTRODUCTION

In our paper, we present a bot-assisted approach to allow modellers to perform domain modelling quickly and interactively. Furthermore, the approach uses an incremental learning strategy empowered by Machine Learning (ML) to improve the accuracy of the bot’s suggestions and extracted domain models by analyzing practitioners’ decisions over time. We build a prototype of our approach in the form of a domain modelling bot. The bot uses four micro-services to enable automated and interactive domain modelling with incremental learning. First, we use a React-based frontend that provides a web-based interface to allow modellers for providing problem descriptions in natural language and performing modifications in the extracted models. Second, we use a Django-based backend to extract domain models from problem descriptions using Natural Language Processing (NLP) and ML techniques. Third, we use a conversational agent empowered by RASA to provide suggestions to modellers and collect their decisions. Finally, we use an action server empowered by RASA to make proactive changes in the extracted models based on modellers' decisions. Finally, to facilitate the evaluation of our bot, we use Docker Compose to configure and start multiple Docker containers for the four microservices on the same host. Furthermore, based on the focus of our approach in the paper, we organize the evaluation tasks into three activities -- (a) evaluating domain models extracted from our approach (without incremental learning), (b) evaluating domain models extracted from our approach (with incremental learning), and (c) evaluation of results obtained from the first two activities (a and b).

We provide all the necessary artifacts and documentation on our [GitHub page](https://github.com/Rijul5/bot-artifact-evaluation-22). 


## SYSTEM REQUIREMENTS

* 64-bit kernel and CPU support for virtualization
* At least 8 GB of RAM.
* Docker (Follow installation instructions from the [official page of Docker](https://docs.docker.com/engine/install/) based on your operating system)


## REPOSITORY CONTENTS

We provide two Docker Compose files (YAML format) as mentioned below. Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, we use a YAML file to configure the four microservices of our application. Each service of our application is already deployed on [Docker Hub](https://hub.docker.com/) in the form of a Docker image. In each configuration, we provide these image locations to build corresponding microservices. Finally, we create and start all the services in a configuration using a single command.  

1. __docker-compose-hub-1.yml__
2. __docker-compose-hub-2.yml__



## INSTALLATION GUIDE

To use the provided Docker Compose files, follow the below steps:
1. Download the Docker Compose files in a directory on your system.
2. Open the command line terminal and navigate to the directory.
3. Use the below command to run Docker containers using __docker-compose-hub-1.yml__ (for example)

```console
docker-compose -f docker-compose-hub-1.yml up --build
```
4. Open a browser and use the link __http://localhost:3000/__ to launch our web-based tool (bot).



## EXPERIMENT REPLICATION GUIDE

Based on our approach and its evaluation in the paper, we organize the experiment replication tasks into three activities. As the experiment involves the use of ML models, the outcomes may differ every time these activities are performed. Therefore, we focus on the relative comparison of performances of our approach across these activities rather than the absolute comparison. Moreover, the evaluators mimic the role of a modeller while performing these activities as we explain in the below steps. 

## 1. Evaluation of our approach (without learning strategy)

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

## 2. Evaluation of our approach (with learning strategy)

As it is time-consuming and labor-intensive to re-train and package the new version of models obtained from _Activity 1_, we create __docker-compose-hub-2.yml__ file that builds and starts the containers which are associated with the trained version of models that we obtain from _Activity 1_ in real-time. A modeller performs the below steps for _Activity 2_.

  * First, a modller runs the containers using __docker-compose-hub-2.yml__ with the below command and launches the bot in a browser using the link __http://localhost:3000/__.  

    ```console
    docker-compose -f docker-compose-hub-2.yml up --build
    ```
    
  * Second, the modeller clears the memory of trace models for previous domain problems (if any) by clicking the _Clear Problem Stack_ button and enables incremental learning for this activity by toggling on the _Incremental Learning_ radio button.

  * Third, the modeller again provides the problem description (PD1) in the _Domain Problem Description_ box of our bot and extracts the corresponding domain model by clicking the _Extract Model_ button.

  * Fourth, the modeller evaluates the accuracy of the extracted domain model using the below metrics. We call the accuracy of the extracted domain model in this activity, __AC2__. The objective of this step is to evaluate configurations generated for decision points in a domain model. A decision point represents a scenario that can be modelled by one or more relationships.

  * Fifth,  as shown in Table 1, the modeller changes the two decision points if the _Current Configurations_ (generated solutions) are not the same as the _Expected Configurations_. Also, the modeller counts the manual steps required to perform these modifications (bringing the state of extracted domain model closer to the expected state). We call these manual steps, __MS2__. As shown in Table 1, there are multiple possible actions which could be performed. The bot considers each modeller's action and presents the other alternative configurations (bot's suggestions) which closely match the modeller’s actions (based on weighted steps). A modeller then accepts one of the configurations from the bot's suggestions. The accepted configuration represents modeller's preference for a configuration among possible configurations to model a scenario in domain modelling. Finally, the bot proactively updates the whole decision point (domain model) with the configuration selected by the modeller. 


## 3. Comparison of performances
In this activity, we evaluate the results obtained from _Activity 1_ and _Activity 2_.

* First, we compare the accuracy of extracted domain models obtained from _Activity 1_  and _Activity 2_. As we change the weights of underlying models (version in _Activity 1_ and version in _Activity 2_), there should be some differences in their accuracy. The expected outcome: __AC1__ < __AC2__

* Second, we compare __MS1__ and __MS2__. The expected outcome: __MS1__ > __MS2__
    



## CONTRIBUTORS
-----------

 * __[Rijul Saini](http://www.ece.mcgill.ca/~rsaini6/)__
 * __[Professor Gunter Mussbacher](http://www.ece.mcgill.ca/~gmussb1/)__
 * __[Professor Jin L.C. Guo](https://www.cs.mcgill.ca/~jguo/)__
 * __[Professor Joerg Kienzle](https://www.cs.mcgill.ca/~joerg/Home/Jorgs_Home.html)__
