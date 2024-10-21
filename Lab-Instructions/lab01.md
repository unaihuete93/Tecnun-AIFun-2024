
# Lab 01: Get Started with Azure AI Services

In this exercise, you'll get started with Azure AI Services by creating an **Azure AI Services** resource in your Azure subscription and using it from a client application. The goal of the exercise is not to gain expertise in any particular service, but rather to become familiar with a general pattern for provisioning and working with Azure AI services as a developer.


## Provision an Azure AI Services resource

Azure AI Services are cloud-based services that encapsulate artificial intelligence capabilities you can incorporate into your applications. You can provision individual Azure AI services resources for specific APIs (for example, **Language** or **Vision**), or you can provision a single **Azure AI Services** resource that provides access to multiple Azure AI services APIs through a single endpoint and key. In this case, you'll use a single **Language AI** resource.

1. Open the Azure portal at `https://portal.azure.com`, and sign in using the Microsoft account associated with your Azure subscription.
2. In the top search bar, search for *Language*, select **Language**, and **create** a Language resource with the following settings:
    - In **Select additional features**, click on **continue...**
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *Choose or create a resource group for the day*
    - **Region**: *Choose any available region*
    - **Name**: *Enter a unique name*
    - **Pricing tier**: S 
3. Select the required checkboxes and create the resource.
4. Wait for deployment to complete, and then view the deployment details.
5. Go to the resource and view its **Keys and Endpoint** page. This page contains the information that you will need to connect to your resource and use it from applications you develop. Specifically:
    - An HTTP *endpoint* to which client applications can send requests.
    - Two *keys* that can be used for authentication (client applications can use either key to authenticate).
    - The *location* where the resource is hosted. This is required for requests to some (but not all) APIs.

## Launch a GitHub Codespace as the dev environment

GitHub Codespaces provide a container based environment that can be used to build/test/debug your applications. This lab has a codespace prepared with the Python and Azure tools required to run the labs.

1. Go to the main page of the labs repository https://github.com/huete93/Tecnun-AIFun-2024/ 
2. Launch the codespace by clicking **Code**>**Codespaces**>**Create codespace on main**.
3. Wait for the codespace to start, it will take some minutes. 

## Use a REST Interface

The Azure AI services APIs are REST-based, so you can consume them by submitting JSON requests over HTTP. In this example, you'll explore a console application that uses the **Language** REST API to perform language detection; but the basic principle is the same for all of the APIs supported by the Azure AI Services resource.

1. In GitHub Codespace, expand the **Lab-Files/Lab01/rest-client** folder.
2. View the contents of the **rest-client** folder, and note that it contains a file for configuration settings:

    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your Azure AI services resource. Save your changes.

3. Note that the **rest-client** folder contains a code file for the client application:

    - **Python**: rest-client.py

    Open the code file and review the code it contains, noting the following details:
    - Various namespaces are imported to enable HTTP communication
    - Code in the **Main** function retrieves the endpoint and key for your Azure AI services resource - these will be used to send REST requests to the Text Analytics service.
    - The program accepts user input, and uses the **GetLanguage** function to call the Text Analytics language detection REST API for your Azure AI services endpoint to detect the language of the text that was entered.
    - The request sent to the API consists of a JSON object containing the input data - in this case, a collection of **document** objects, each of which has an **id** and **text**.
    - The key for your service is included in the request header to authenticate your client application.
    - The response from the service is a JSON object, which the client application can parse.

4. Right click on the **rest-client** folder, select *Open in Integrated Terminal* and run the following command:

    

    **Python**

    ```
    python rest-client.py
    ```

5. When prompted, enter some text and review the language that is detected by the service, which is returned in the JSON response. For example, try entering any sentence for the language to be detected, for example, "Hello and welcome to the Azure IA services". Longer sentences will work better. 
6. When you have finished testing the application, enter "quit" to stop the program.

## Use an SDK

You can write code that consumes Azure AI services REST APIs directly, but there are software development kits (SDKs) for many popular programming languages, including Microsoft C#, Python, Java, and Node.js. Using an SDK can greatly simplify development of applications that consume Azure AI services.

1. In Visual Studio Code, expand the **sdk-client** folder under the **Lab-Files/Lab01** folder. Right click on the **sdk-client** folder, select *Open in Integrated Terminal*

2. Install the Text Analytics SDK package by running the appropriate command for your language preference:


    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

3. View the contents of the **sdk-client** folder, and note that it contains a file for configuration settings:

    - **Python**: .env

    Open the configuration file and update the configuration values it contains to reflect the **endpoint** and an authentication **key** for your Azure AI services resource. Save your changes.
    
4. Note that the **sdk-client** folder contains a code file for the client application:

    - **Python**: sdk-client.py

    Open the code file and review the code it contains, noting the following details:
    - The namespace for the SDK you installed is imported
    - Code in the **Main** function retrieves the endpoint and key for your Azure AI services resource - these will be used with the SDK to create a client for the Text Analytics service.
    - The **GetLanguage** function uses the SDK to create a client for the service, and then uses the client to detect the language of the text that was entered.

5. Return to the terminal, ensure you are in the **sdk-client** folder, and enter the following command to run the program:

    **Python**

    ```
    python sdk-client.py
    ```

6. When prompted, enter some text and review the language that is detected by the service. For example, try entering "Goodbye", "Au revoir", and "Hasta la vista", or any longer sentence.


## ACTION Lab 01

**Upload an screenshot to ADI with the execution of the sdk-client program testing it with a sentence.** 


## More information

For more information about Azure AI Services, see the [Azure AI Services documentation](https://docs.microsoft.com/azure/ai-services/what-are-ai-services).