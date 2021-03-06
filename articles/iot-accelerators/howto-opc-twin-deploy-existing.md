---
title: How to deploy Azure IoT OPC UA device management module to an existing project | Microsoft Docs
description: How to deploy OPC Twin to an existing project.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
---

# Deploy OPC Twin to an existing project

The OPC Device Twin module runs on IoT Edge and provides several edge services to the OPC Device Twin and Registry services. 

The OPC Device Twin micro service facilitates the communication between factory operators and OPC UA server devices on the factory floor via an OPC Twin IoT Edge module. The micro service exposes OPC UA services (Browse, Read, Write, and Execute) via its REST API. 

The OPC UA Device Registry micro service provides access to registered OPC UA applications and their endpoints. Operators and administrators can register and unregister new OPC UA applications and browse the existing ones, including their endpoints. In addition to application and endpoint management, the registry service also catalogs registered OPC Device Twin IoT Edge modules. The service API gives you control of edge module functionality, for example, starting or stopping server discovery (scanning services), or activating new endpoint twins that can be accessed using the OPC Twin micro service.

The core of the module is the Supervisor identity. The supervisor manages endpoint twin, which corresponds to OPC UA server endpoints that are activated using the corresponding OPC UA registry API. This endpoint twins translate OPC UA JSON received from the OPC Twin micro service running in the cloud into OPC UA binary messages, which are sent over a stateful secure channel to the managed endpoint. The supervisor also provides discovery services that send device discovery events to the OPC UA Device Onboarding service for processing, where these events result in updates to the OPC UA registry.  This article shows you how to deploy the OPC Twin module to an existing project. 

> [!NOTE]
> For more information on deployment details and instructions, see the GitHub [repository](https://github.com/Azure/azure-iiot-opc-twin-module).

## Prerequisites

> [!NOTE]
> Note: Due the dependency on the AzureRM module, deployment currently is only supported on Windows.

Make sure you have PowerShell and [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) extensions installed.   If you have not done so yet, clone this GitHub repository.  Open a command prompt or terminal and run:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## Deploy industrial IoT services to Azure

1. On the open command prompt or terminal run:

   ```bash
   deploy
   ```

2. Follow the prompts to assign a name to the resource group of the deployment and a name to the website.   The script deploys the micro services and their Azure platform dependencies into the resource group in your Azure subscription.  The script also registers an Application in your Azure Active Directory (AAD) tenant to support OAUTH-based authentication.  Deployment will take several minutes.  An example of what you'd see once the solution is successfully deployed:

   ![Industrial IoT OPC Twin deploy to existing project](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   The output includes the  URL of the public endpoint. 

3. Once the script completes successfully, select whether you want to save the .env file.  You need the .env environment file if you want to connect to the cloud endpoint using tools such as the Console or deploy modules for development and debugging.

## Troubleshooting deployment failures

### Resource group name

Ensure you use a short and simple resource group name.  The name is used also to name resources as such it must comply with resource naming requirements.  

### Website name already in use

It is possible that the name of the website is already in use.  If you run into this error, you need to use a different application name.

### Azure Active Directory (AAD) registration

The deployment script tries to register two AAD applications in Azure Active Directory.  Depending on your rights to the selected AAD tenant, the deployment might fail. There are two options:

1. If you chose a AAD tenant from a list of tenants, restart the script and choose a different one from the list.
2. Alternatively, deploy a private AAD tenant in another subscription, restart the script, and select to use it.

> [!WARNING]
> NEVER continue without Authentication.  If you choose to do so, anyone can access your OPC Device Management endpoints from the Internet unauthenticated.   You can always choose the ["local" deployment option](howto-opc-twin-deploy-dependencies.md) to kick the tires.

## Deploy an all-in-one industrial IoT services demo

Instead of just the services and dependencies you can also deploy an all-in-one demo.  The all in one demo contains three OPC UA servers, the OPC Twin module, all micro services and a sample Web Application.  It is intended for demonstration purposes.

1. Make sure you have a clone of the repository (see above). Open a command prompt or terminal in the  root of the repository and run:

   ```bash
   deploy -type demo
   ```

2. Follow the prompts to assign a new name to the resource group and a name to the website.  Once deployed successfully, the script will display the URL of the web application endpoint.

## Deployment script options

The script takes the following parameters:

```bash
-type
```

The type of deployment (vm, local, demo)

```bash
-resourceGroupName
```

Can be the name of an existing or a new resource group.

```bash
-subscriptionId
```

Optional, the subscription ID where resources will be deployed.

```bash
-subscriptionName
```

Or the subscription name.

```bash
-resourceGroupLocation
```

Optional, a resource group location. If specified, will try to create a new resource group in this location.

```bash
-aadApplicationName
```

A name for the AAD application to register under. 

```bash
-tenantId
```

AAD tenant to use.

```bash
-credentials
```

## Next steps

Now that you have learned how to deploy OPC Twin to an existing project, here is the suggested next step:

> [!div class="nextstepaction"]
> [Secure communication of OPC Client and OPC PLC ](howto-opc-vault-deploy-existing-client-plc-communication.md)