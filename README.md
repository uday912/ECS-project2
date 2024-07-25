**ECS PROJECT-2**
Project: Deploy Docker Container to AWS ECS Automatically with CI CD Pipeline:

STEPS TO FOLLOW: PRE-REQUISITES:- 
      --> AWS EC2 INSTALLATION SETUP
      
      --> DOCKER INSTALLATION
      
      --> ECR FOR STORING DOCKER IMAGE
      
      --> LOAD BALANCER TO CONTROL THE TRAFFIC
      
       --> FINALLY **ECS** FOR ORCHESTRATION AND PROVISIONING OF DOCKER CONTAINERS AND FOR HOST WEB APPLICATION


EC2 INSTALLATION AND SETUP: First and foremost login into aws account and then search for ec2 on search bar

           --> click on ec2
           
               --> on ec2 dashoard click on instances and then click on **LAUNCH INSTANCE**
               
                    -->  SELECT AND ENTER THE REQUIRED DETAILS: 
                    
                                       --> NAME
                                       
                                       --> AMI
                                       
                                       --> INSTANCE TYPE
                                       
                                       --> KEY PAIR - SELECT EXISTING KEY PAIR IF HAVE ANY OR OTHERWISE CREATE A NEW
                                       
                                       --> SELECT VPC AND SECURITY GROUPS EITHER DEFAULT ONE'S OR CREATE A NEW ONE'S
                                       
                                       --> CONFIGURE THE STORAGE AS PER REQUIREMENT OR TAKE DEFAULT

THEN NAVIGATE TO TERMINAL EITHER GITBASH, PUTTY OR MOBAXTERM...

CONNECT WITH THE EC2 INSTANCE USING IP AND PEM KEY..

AFTER SETUP THE TERMINAL, RUN THR FOLLOWING COMMANDS FOR AWS CONFIGURATION AND INSTALLATION OF DOCKER

FOR AWS CONFIGURATION:

FIRST INSTALL THE AWS CLI, FOR THAT WE NEED, 

  **COMMANDS:**
  
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install

THEN RUN aws configure

  FOR AWS CONFIGURATION, WE NEED ACCESS KEY AND SECRET ACCESS KEY ID: FOR THAT

         SEARCH FOR ** IAM ** ON SEARCH BAR
            
             --> NAVIAGATE TO IAM PAGE AND ON LEFT HAND SIDE THERE IS OPTION CALLED "USER"

                 --> CLICK ON USER AND CREATE A USER IF YOU DON'T HAVE ANY 

                    --> CLICK ON SECURITY CREDENTIALS 

                    --> ON ACCESS KEYS

                    --> CREATE ACCESS KEY

  AFTER CREATING THE KEYS, NAVIAGATE TO TERMINAL AND RUN AWS CONFIGURE COMMAND AND THERE COPY AND PASTE THE ACCESS KEY AND SECRET ACCESS KEY AND REGION TOO.

**INSTALL THE DOCKER:-** {ON UBUNTU}

	sudo apt-get update

	sudo apt-get install ca-certificates curl

	sudo install -m 0755 -d /etc/apt/keyrings

	sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

	sudo chmod a+r /etc/apt/keyrings/docker.asc

	   echo \

	  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \

	  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \

	  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

	  sudo apt-get update

       --> docker --version - to check for the docker version
    

 **ECR SETUP ON AWS:**

  SEARCH FOR ECR:
      
      CLICK ON ELASTIC CONTAINER REGISTRY

         CLICK ON CREATE 

           --> SELECT EITHER PRIVATE OR PUBLIC REPOSITORY OPTION

             --> FILL THE REPOSITORY NAME

               --> CLICK ON CREATE REPOSITORY

    AFTER CREATION OF REPO AND ON CREATED REPO DASHBOARD YOU HAVE OPTION CALLED "VIEW PUSH COMMANDS" ON TOP

           FOR AUTHENTICATION AND FOR PUSH THE IMAGES TO ECR WE NEED PERMISSIONS, FOR THAT WE NEED TO CREATE IAM ROLE

          --> NAVIGATE TO IAM 

              --> CLICK ON ROLE ON LEFT SIDE 

                 --> CREATE A ROLE 

                   -- NAME 

                     -- SELECT REQUIRED ENTITY TYPE {AWS  SERVICE}

                       -- SELECT USE CASE {EC2}

                              -- ATTACH THE PERMISSION POLICIES AS PER REQUIREMENT AND FINALLY CREATE THE ROLE

   AFTER CREATION, WE NEED TO LOGIN TO ECR FROM AWS CLI, FOR THAT

        NAVIGATE TO ECR, 
            
            -- CLICK ON VIEW PUSH COMMANDS 

               FROM THERE COPY AND PASTE THE 

                     -- AUTHENTICATE TOKEN COMMAND 

                     -- DOCKER TAG COMMAND AND DOCKER PUSH COMMAND 


    AFTER THAT WE NEED TO CREATE A LOAD BALANCER FOR BETTER ROUTING TRAFFIC ;

         NAVIGATE TO EC2 PAGE 

             CLICK ON LOAD BALANCING --> LOAD BALANCERS 

                 -- CREATE A LOAD BALANCER

                    -- SELECT THE LOAD BALANCER TYPE [APPLICATION LOAD BALANCER, NETWORK LOAD BALANCER, GATEWAY LOAD BALANCER]

                         FILL AND SELECT THE REQUIRED OPTIONS:

                             -- LOADBALANCER NAME

                             -- NETWORK MAPPING

                                  --> VPC
                                  
                                  --> SUBNETS
                                  
                             -- SECURITY GROUPS : CHOOSE SG FOR PORT OPENING FOR 3000 AND ATTACH TO LOAD BALANCER THAT WE GONNA USE                
                                                                      AT ECS SERVICE 

                             -- LISTNERS AND ROUTING 

                                 IN LISTNERS 

                                     --> CREATE A TARGET GROUP 

                                            -- CHOOSE TARGET TYPE

                                            -- TARGET GROUP NAME 

                                            -- SELECT THE VPC AS PER REQUIREMENT



** ECS SETUP FOR WEB APPLICATION: **
        SEARCH FOR ECS

            -- NAVIGATE TO ECS DASHBOARD

               --> CLICK ON TASK DEFINITIONS ON LEFT SIDE 

                   --> CREATE A NEW TASK DEFINITION

                       --> FILL THE DETAILS:

                            -- TASK FAMILY NAME

                            -- INFRASTRUCTURE REQUIREMENTS:

                                 -- LAUNCH TYPE : HERE WE USE "FARGATE" - IT'S A SERVERLESS COMPUTE FOR CONTAINERS                                                

                            -- TASK DEFINITION ROLE - A task execution IAM role is used by the container agent to make AWS API requests.

                                  -- CREATE A NEW IAM ROLE AND ATTACH POLICIES THAT IS REQUIRED 

                                             -- ROLE: POLICIES:- "AMAZONECS TAKE EXECUTION ROLE POLICY"
                                             
                --> NAVIGATE AND CLICK ON "CONTAINER SECTION"

                      --> NAME 

                      --> IMAGE URI - COPY AND PASTE THE ECR REPOSITORY URI THAT IS CREATED ON ECR

               REST OF OPTIONS LEAVE IT AS DEFAULT ONE'S 

               ** FINALLY TASK DEFINITION IS CREATED....


    ** NAVIGATE TO CLUSTER OPTION ON ECS DASHBOARD:

        --> CLICK ON CREATE CLUSTER

            -- CLUSTER NAME 

            -- INFRASTRUTURE 

                -- AWS FARGATE- SERVERLESS

         CREATE A CLUSTER....

            THEN NAVIGATE TO CREATED CLUSTER AND AT DOWN HAVE AN OPTION CALLED " SERVICE- MAINTAIN THE EC2 INSTANCES "

                  -- CLICK ON " SERVICE "

                        -- CLICK ON CREATE 

                             --> SELECT THE TASK DEFINITION- WHICH WE ARE CREATED 

                             --> SERVICE NAME -

                             --> DESIRED TASK - 

         
                  -- NETWORKING 

                        --> VPC

                        --> SUBNETS

                        --> SECURITY GROUPS- CREATE A NEW ONE IF REQUIRED/ SELECT CREATED SG, TWO SECURITY GROUPS, ONE FOR HTTP  
                             PROTOCOL: PORT NO.80 AND ANOTHER ONE FOR PORT:3000 :

                                              -- NAME

                                              -- TYPE
                   
                   -- LOAD BALANCING 

                      --> SELECT EXISTING CREATED LOAD BALANCER {ALB}

                      --> TARGET GROUP

                         -- NAME 

                         -- PROTOCOL

                         -- EVALUATION ORDER


                    -- AUTO-SCALING 

                       --> DEFAULT ONE 

   AFTER THE SERVICE IS CREATED, NAVIAGATE TO THE SERVICE WHICH IS CREATED AND THERE WE CAN CHECK FOR LOGS, NETWORKING,
   CONFIGURATION, MONITORING AND HEALTH CHECKS... 

	SERVICE
	NETWORKING
	DNS – COPY AND PASTE IT ON ENDPOINT 


CI/CD FOR AWS ECS USING THE CODECOMMIT, CODEBUILD AND CODEPIPELINE:

   **CODECOMMIT**: 
   
          **WHAT IS CODECOMMIT**:
          
                CodeCommit is a fully managed source control service provided by Amazon Web Services (AWS). It hosts secure Git-based repositories, similar to services like GitHub, GitLab, or Bitbucket. CodeCommit allows you to store and manage your source code, binaries, and other assets privately in the cloud.
    
                 NAVIGATE TO THE CODECOMMIT
  
             -  CLICK ON CREATE REPO
             
                      FILL THE 
                      
                      	NAME
                      
                      	NAME DESCRIPTION
                      
               CLICK ON CREATE….
               
                    AND THEN WE NEED TO PUSH THE CODE TO CODECOMMIT REPOSITORY, FOR THAT WE NEED TO CREATE IAM USER AND ATTACH THE DESIRED PERMISSIONS TO:
                    
                            	PERMISSIONS: AWS CODECOMMIT FULL ACCESS
         
              ON THE SAME USER DASHBOARD, NAVIGATE TO SECURITY CREDENTIALS 
                                 
                      CLICK ON GENERATE CREDENTIALS:
                      
                                 	USERNAME AND PASSWORD
                                 
  THEN MOVE INTO THE CODECOMMIT DASHBOARD AND COPY THE CODE REPO URL AND PASTE ON LOCAL VM TERMINAL BY FOLLOWING COMMAND 
                   
             	GIT CLONE {CODECOMMIT URL}

             	USERNAME {CREATED ON USER HTTPS GIT CREDENTIALS}
             
             	PASSWD {CREATED ON USER HTTPS GIT CREDENTIALS}
             
      THEN COPY THE CODE FILES INTO THE CODECOMMIT REPO BY FOLLOWING:
      
             	CP (LOCAL DIR CODE FILE NAME ) (NEWLY CREATED CODECOMMIT DIRECTORY)
             
      THEN CHANGE THE DIRECTORY TO CODECOMMIT DIR AND RUN THE FOLLOWING COMMANDS:
      
             	GIT STATUS
             
             	GIT ADD ./FILENAME
             
             	GIT COMMIT -M (COMMIT ID)
             
            	GIT PUSH (HERE WE HAVE TO GIVE USERNAME AND PASSWORD OF HTTP AGAIN)
            
      THEN CHECK FOR CODE FILES ON CODECOMMIT DASHBOARD.

**CODEBUILD**:
         WHAT IS CODEBUILD:
         
                          AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests,
                          
                                   and produces software artifacts, such as executable files or application packages.
           HOW DOES IT WORKS:
           
                  CodeBuild uses build specifications defined in buildspec.yml files. When triggered by a source code change, it 
                  
                           pulls the code from the repository, follows the build steps specified, and generates the build artifacts.

     AFTER CODE SUCCESSFULLY PUSH INTO CODECOMMIT, NAVIGATE TO CODEBUILD PAGE:
     
	CLICK ON CREATE PROJECT

-	NAME
-	
-	SOURCE PROVIDER – AWS CODECOMMIT
-	
-	REPO – REPOSITORY IN CODECOMMIT THAT WE ARE CRERATED BEFORE
-	
-	BRANCH
-	
-MASTER/MAIN

-COMMIT-ID

- OS – LINUX
- 
- RT – STANDARD
- 
- IMAGE – ACCORDING TO NEED; THE LATEST ONE

-  - PRIVILEGED – SELECT THIS SECTION FOR SURE
   - 
CLICK ON CREATE PROJECT……….

                        THEN NAVIGATE TO BUILD DETAILS ON CREATED PROJECT
      
	SERVICE ROLE -CLICK ON SERVICE ROLE

	ATTACH POLICIES – AMAZON ECR FULL ACCESS


**CODEPIPELINE**:

             WHAT IS AWS CODEPIPELINE:
             
                   AWS CodePipeline is a fully managed continuous integration and continuous delivery (CI/CD) service
                   
                            that automates the release process of software applications. It enables developers to build, test, and 
                            
                            deploy their code changes automatically and efficiently.

                  HOW DOES IT WORKS:
                  
                           CodePipeline orchestrates the flow of code changes through multiple stages. Each stage represents a step in
                           
                           the release process, such as source code retrieval, building, testing, and deployment. Developers define the 
                           
                           pipeline structure, including the sequence of stages and associated actions, to automate the entire software 
                           
                          delivery lifecycle.
                          
             THEN NAVIGATE TO CODEPIPELINE:
             
	CLICK ON CREATE PIPELINE

        --> NAME
	
        -->	SOURCE –  SELECT - AWS CODE COMMIT

        -->	REPO NAME
        
        -->	BRANCH NAME – MASTER, IN MY CASE IT’S MASTER
	
-------  CLICK ON NEXT

        --> BUILD STAGE
        
               --> BUILD PROVIDER – AWS CODE BUILD
               
               -->	NAME – AS PER WISH

                                THEN: 
                                       IN ENVIRONMENT VARIABLES: TAKE THE VARIABLES AS KEY:VALUE FROM BUILDSPEC.YAML
 	
                                                                  NAME: AWS_DEFAULT_REGION, AWS_ACCOUNT_ID, IMAGE_REPO_NAME, REPOSITORY_URI,  
                                                                               IMAGE_TAG, CONTAINER_NAME
 	
                                                                  VALUE: FILL THEIR VALUES
 	
                                  THEN: CLICK ON NEXT : NAVIGATE DEPLOY STAGE
 	
                                                   ---- DEPLOY PROVIDER – AMAZON ECS

                                                   ---- CLUSTER NAME 

                                                    ---- SERVICE NAME
 	
                                  CREATE A PIPELINE FINALLY, AND IT’S NAVIGATE TO PIPELINE DASHBOARD, THERE WE CAN SEE RUNNIG PROCESS…. 


                                   
                                   






