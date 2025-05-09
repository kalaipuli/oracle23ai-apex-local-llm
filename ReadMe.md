# Build Apex Application with Local LLM

Build an Apex Application in Oracle 23ai database utilizing the Generative AI features by connecting to a Local LLM.

Oracle Apex allows you to build generative AI applications like an AI Assistant to chat with Large language models in OCI or any other cloud platforms.

The below setup allows you connect with an LLM running on your local machine. The entire setup runs on containers so that it can deployed anywhere. There is no need to have any paid AI services. 

Please note that there is a compromize betweet the cost and the latency in response time. 

## Initial setup

>**Remeber to ensure the following!**
> 
>**Docker Engine is installed on your machine**
>
>**Following docker images are downloaded before running the docker compose**
>
>>container-registry.oracle.com/database/free:latest
>>
>>container-registry.oracle.com/database/ords-developer:latest
>>
>>ollama/ollama
>>
>>ghcr.io/open-webui/open-webui:main
>
>**There is enough Disk space and RAM (Min 16 GB) to run the application**

Navigate to the repository root folder and run

```console
$ docker compose -f compose.yaml up
```

It takes a while to start up the database and install apex, download.. 

so, **Be very patient!**

#### Note: It takes about *30mins* to install the Oracle Database for the first time. It takes about *45mins* to install APEX on the database. 

The data is persisted in your local drive for the database, apex and ollama. The models are downloaded and stored in your local drive so you are able to restart the containers without losing the data.

## OLLAMA Configuration

- login into ollama container using the following command
```console
$ docker exec -it ollama bash
```
```console
$ ollama pull llama3.2:latest
```

- Wait for the model to be downloaded.

- Exit from the container

## Open WebUI Configuration
Login to "Open WebUI" using the following url "http://
localhost:3000

- Create an administrator account.

- Make sure you are able to see the llama3.2:latest listed in the models dropdown.

- Click on the User Icon at the Top Right corner and Select 'Settings'

    A popup window opens.

- Click on "Account" on the left pane and click "show" button next to the API Keys on the right pane

- Click "Create new secret key"

    Copy the newly created key and store it somewhere safe. We will use this when configuring the APEX Application.

## Database configuration
Access Control List should be updated to allow connection from external machines into the database.

Login to the Oracle container as follows

```console
$ docker exec -it oracle-db bash
```

Login to SQLPLUS
```console
$ sqlplus sys/OraclePwd123@oracle-db:1521/FREEPDB1 as sysdba
```

RUN THE FOLLOWING PROCEDURE

```console
SQL> BEGIN  sys.DBMS_NETWORK_ACL_ADMIN.APPEND_HOST_ACE(host => '*',ace => xs$ace_type(privilege_list => xs$name_list('connect'),                   principal_name => 'APEX_240100',principal_type => xs_acl.ptype_db)); END;
/
```

Exit from the container

## APEX Configuration
Login to Apex using the following url http://localhost:8181

Launch the Apex application from the home page.

When you login the first time,
the workspace name is 'internal'
user 'admin'
password 'Welcome_1'
You will be asked to change the password after login.

Create a new workspace 
Provide a new name for the workspace
Provide a name and a password for the schema.
Provide the workspace administrator username and password

Login to the workspace created with the workspace admin credentials.

import the **demo.sql** as a new application.

After import, navigate to 'Workspace utilties'->Generative AI Services

Edit the entry 'OLLAMA'

Under "credentials" section, select "Create New".
Enter the API Key copied from Open WebUI Console.

Now click on "AppBuilder" and Select DEMO application.

Select the page "LOCALLLM" and click "Run"

A new Tab opens with the application.

Click the "Assistant" Button to open the chat window and start chatting!!!

The very first response will take a considerable amount of time due to the loading of the model to memory etc. so, again, be patient!

The prompts are routed to the local LLM running on your machine and there will be delays in getting the response back.

If you have a machine with sufficient CPU and RAM, it would not be a challenge.

Hapy chatting!!!

