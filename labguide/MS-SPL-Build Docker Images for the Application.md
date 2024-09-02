## Hands-On-Lab: Build Docker Images for the Application
  
**Duration**: 30 Minutes

## Overview

In this exercise, you will learn how to Containerize the Contoso traders application using Docker images. Containerized applications are applications that run in isolated runtime environments called containers. A Docker image is a file used to execute code in a Docker container. Docker images act as a set of instructions to build a Docker container, like a template. Also, you will be pushing the created Docker images to the Azure Container Registry.
   
### Task 1: Set up a local infrastructure with the Linux VM

In this task, you will be connecting to the Build agent VM using the Command prompt and will be cloning the Contoso trader website GitHub repo.  

1. Once you log into the VM, search for **cmd** **(1)** in the windows search bar and click on **Command Prompt** **(2)** to open.

   ![](media/latest-ex1-opencmd.png "open cmd")
    
1. Run the given command **<inject key="Command to Connect to Build Agent VM" enableCopy="true" />** to connect to the Linux VM using ssh.
   
   >**Note**: In the command prompt, type **yes** and press **Enter** for `Are you sure you want to continue connecting (yes/no/[fingerprint])?`
   
1. Once the ssh is getting connected to the VM, please enter the VM password given below:
   
    * Password: **<inject key="Build Agent VM Password" enableCopy="true" />**

   ![](media/ex1-connecttolinuxvm.png "open cmd")
   
   >**Note**: Please note that while typing the password you won’t be able to see it due to security concerns.
    
1. Once the VM is connected, run the below command to clone the GitHub repository that we are going to use for the lab.

    ``` 
    git clone https://github.com/CloudLabsAI-Azure/Cloud-Native-Application
    ```
    
    ![](media/ex1-gitclone.png)
    
    > **Note:** If you recieve a output message stating - destination path 'Cloud-Native-Application' already exists and is not an empty directory. Please run the following commands and then reperform step - 4 of the task.

     ```
        sudo su
        rm -rf Cloud-Native-Application
        exit
     ```   
     ![](media/cloudn.png)
    
1. After the GitHub cloning is completed, run the below command to change the directory to the lab files.
    
    ```
    cd Cloud-Native-Application/labfiles/ 
    ```
    
    ![](media/ex1-cd.png)
    
### Task 2: Build Docker images to containerize the application and push them to the container registry

In this task, you will be building the docker images to containerize the application and will be pushing them to the ACR (Azure Container Registry) to later use in AKS.

1. Run the below command to log in to Azure, navigate to the device login URL `https://microsoft.com/devicelogin` in the browser and copy the authentication code.

   ``` 
   az login
   ```
    
   ![](media/ex-azlogincode.png)

    > **Note:** If you recieve an error command 'az' not found, then please run _'sudo apt install azure-cli'_ which will install the az cli. 
1. Enter the copied authentication code **(1)** and click on **Next** **(2)**.

   ![](media/ex1-codelogin.png)
   
1. On the **Sign in to Microsoft Azure** tab you will see a login screen, in that enter the following email/username and then click on **Next**.

   * Email/Username: **<inject key="AzureAdUserEmail"></inject>**

1. Now enter the following password and click on **Sign in**.

   * Password: **<inject key="AzureAdUserPassword"></inject>**

1. In a pop-up to confirm the sign-into Microsoft Azure CLI, click on **Continue**.

   ![](media/ex1-logincontinue.png)

1. Once you logged in to Azure, you are going to build the Docker images in the next steps and will be pushing them to ACR.

   ![](media/ex1-logincomplete.png)

1. Please make sure that you are in the **labfiles** directory before running the next steps as the docker build needs to find the DockerFile to create the image.

    ```
    cd Cloud-Native-Application/labfiles/
    ```
    
1. Now build the **contosotraders-carts** docker image using the Dockerfile in the directory. Take note of how the deployed Azure Container Registry is referenced. Replace the SUFFIX placeholder in the below command with the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** value.

    ```
     docker build src -f ./src/ContosoTraders.Api.Carts/Dockerfile -t contosotradersacr[SUFFIX].azurecr.io/contosotradersapicarts:latest -t contosotradersacr[SUFFIX].azurecr.io/contosotradersapicarts:latest
    ```
    
    ![](media/ex1-apicarts.png)
    
1. Repeat the steps to create the **contosotraders-Products** docker image with the below command. Make sure to replace the SUFFIX with the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** value in the below command.

    ```
     docker build src -f ./src/ContosoTraders.Api.Products/Dockerfile -t contosotradersacr[SUFFIX].azurecr.io/contosotradersapiproducts:latest -t contosotradersacr[SUFFIX].azurecr.io/contosotradersapiproducts:latest
    ```

    ![](media/ex1-apiproducts.png)

1. Run the below command to change the directory to `services` and open the `configService.js` file.

    ```
    cd src/ContosoTraders.Ui.Website/src/services
    sudo chmod 777 configService.js
    vi configService.js
    ```
    
    ![](media/latest-ex1-cd-website.png)
    
1. In the `vi` editor, press **_i_** to get into the `insert` mode. Replace the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** and Region **<inject key="Region" enableCopy="true"/>** value in the APIUrl. Then press **_ESC_**, write **_:wq_** to save your changes, and close the file. We need to update the API URL here so that the Contoso Traders application can connect to product API once it's pushed to AKS containers.
    
    >**Note**: If **_ESC_** doesn't work press `ctrl + [` and then write **_:wq_** to save you changes and close the file.
    

    ```
    const APIUrl = 'http://contoso-traders-productsXXXXX.eastus.cloudapp.azure.com';
    ```

    ![](media/latest-ex1-didconfig.png)

1. Run the below command to change the directory to the `ContosoTraders.Ui.Website` folder.

    ```
    cd
    cd Cloud-Native-Application/labfiles/src/ContosoTraders.Ui.Website
    ```

1. Now build the **contosotraders-UI-Website** docker image with the below command. Make sure to replace the SUFFIX with the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** value in the below command.

    ```
    docker build . -t contosotradersacr[SUFFIX].azurecr.io/contosotradersuiweb:latest -t contosotradersacr[SUFFIX].azurecr.io/contosotradersuiweb:latest
    ```    
    
    ![](media/latest-ex1-apiwebsite.png)
    
    
    >**Note**: Please be aware that the above command may take up to 5 minutes to finish the build. Before taking any further action, make sure it ran successfully.
    
1. Redirect to the **labfiles** directory before running the next steps.

    ```
    cd
    cd Cloud-Native-Application/labfiles/
    ```

1. Observe the built Docker images by running the command `docker image ls`. The images are tagged with the latest, also it is possible to use other tag values for versioning.

    ```
    docker image ls
    ```

   ![](media/T2S15.png)

1. Navigate to Azure portal, open **contosotradersacr<inject key="DeploymentID" enableCopy="false" />** Container registry from **ContosoTraders-<inject key="DeploymentID" enableCopy="false" />** resource group.

   ![](media/ex1-acr1.png)
   
1. From **contosotradersacr<inject key="DeploymentID" enableCopy="false" />** **(1)** Container registry, select **Access keys** under Settings from left side menu. **Copy** the Password and paste it into a text file for later use.

   ![](media/ex1-acr2.png)    

1. Now login to ACR using the below command, please update the Suffix and ACR password value in the below command. You should be able to see that output below in the screenshot. Make sure to replace the SUFFIX with the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** value and password with the copied container registry password which you have copied in the previous step in the below command.

    ```
    docker login contosotradersacr[SUFFIX].azurecr.io -u contosotradersacr[SUFFIX] -p [password]
    ```

    ![](media/ex1-dockerlogin.png "open cmd")

1. Once you logged in to the ACR, please run the below commands to push the Docker images to the Azure container registry. Also, ensure that you update the SUFFIX value with the given DeploymentID **<inject key="DeploymentID" enableCopy="true"/>** value in the below commands.

   ```
   docker push contosotradersacr[SUFFIX].azurecr.io/contosotradersapicarts:latest 
   ```
   
   ```
   docker push contosotradersacr[SUFFIX].azurecr.io/contosotradersapiproducts:latest
   ```
   
   ```
   docker push contosotradersacr[SUFFIX].azurecr.io/contosotradersuiweb:latest
   ```
   
1. You should be able to see the docker image getting pushed to ACR as shown in the below screenshot. 
    
    ![](media/ex1-dockerpush.png "open cmd")
    
1. Click the **Next** button located in the bottom right corner of this lab guide to continue with the next exercise.


## Summary

In this exercise, you have completely containerized your web application with the help of docker and pushed them to the container registry.
