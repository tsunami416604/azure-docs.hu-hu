---
title: Create your first durable function in Azure using JavaScript
description: Create and publish an Azure Durable Function using Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 94ba2830824c4a918e9451a9fc5140d422110370
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231314"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Create your first durable function in JavaScript

*Durable Functions* is an extension of [Azure Functions](../functions-overview.md) that lets you write stateful functions in a serverless environment. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In this article, you learn how to use the Visual Studio Code Azure Functions extension to locally create and test a "hello world" durable function.  This function will orchestrate and chain together calls to other functions. Ezután közzéteheti a függvénykódot az Azure-ban.

![Running durable function in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Install [Visual Studio Code](https://code.visualstudio.com/download).

* Make sure you have the latest version of the [Azure Functions Core Tools](../functions-run-local.md).

* On a Windows computer, verify you have the [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) installed and running. On a Mac or Linux computer, you must use an actual Azure storage account.

* Make sure that you have version 8.0 or a later version of [Node.js](https://nodejs.org/) installed.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Install the Durable Functions npm package

1. Install the `durable-functions` npm package by running `npm install durable-functions` in the root directory of the function app.

## <a name="creating-your-functions"></a>Creating your functions

We'll now create the three functions you need to get started with Durable Functions: an HTTP starter, an orchestrator, and an activity function. The HTTP starter will initiate your entire solution, and the orchestrator will dispatch work to various activity functions.

### <a name="http-starter"></a>HTTP starter

First, create an HTTP triggered function that starts a durable function orchestration.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions HTTP Starter** function template.

    ![Choose the HTTP starter template](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Leave the default name as `DurableFunctionsHttpStart` and press ****Enter**, then select **Anonymous** authentication.

    ![A névtelen hitelesítés kiválasztása](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

We've now created an entry-point into our Durable Function. Let's add an orchestrator.

### <a name="orchestrator"></a>Orchestrator

Now, we'll create an orchestrator to coordinate activity functions.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions orchestrator** function template. Leave the name as the default "DurableFunctionsOrchestrator"

    ![Choose the orchestrator template](./media/quickstart-js-vscode/create-function-choose-template.png)

We've added an orchestrator to coordinate activity functions. Let's now add the referenced activity function.

### <a name="activity"></a>Tevékenység

Now, we'll create an activity function to actually carry out the work of the solution.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Függvény létrehozása](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions activity** function template. Leave the name as the default "Hello".

    ![Choose the activity template](./media/quickstart-js-vscode/create-function-choose-template.png)

We've now added all components needed to start off an orchestration and chain together activity functions.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. On a Windows computer, start the Azure Storage Emulator and make sure that the **AzureWebJobsStorage** property of *local.settings.json* is set to `UseDevelopmentStorage=true`.

    For Storage Emulator 5.8 make sure that the **AzureWebJobsSecretStorageType** property of local.settings.json is set to `files`. On     a Mac or Linux computer, you must set the **AzureWebJobsStorage** property to the connection string of an existing Azure storage         account. You create a storage account later in this article.

2. A függvény teszteléséhez állítson be egy töréspontot a függvény kódjában, majd nyomja le az F5 billentyűt a függvényalkalmazás-projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg. If this is your first time using Durable Functions, the Durable Functions extension is installed and the build might take a few seconds.

    > [!NOTE]
    > JavaScript Durable Functions require version **1.7.0** or greater of the **Microsoft.Azure.WebJobs.Extensions.DurableTask** extension. Run the following command from the root folder of your Azure Functions app to install the Durable Functions extension `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Cserélje le a `{functionName}` elemet a `DurableFunctionsOrchestrator` kérdésre.

5. Using a tool like [Postman](https://www.getpostman.com/) or [cURL](https://curl.haxx.se/), send an HTTP POST request to the URL endpoint.

   The response is the initial result from the HTTP function letting us know the durable orchestration has started successfully. It is not yet the end result of the orchestration. The response includes a few useful URLs. For now, let's query the status of the orchestration.

6. Copy the URL value for `statusQueryGetUri` and paste it in the browser's address bar and execute the request. Alternatively you can also continue to use Postman to issue the GET request.

   The request will query the orchestration instance for the status. You should get an eventual response, which shows us the instance has completed, and includes the outputs or results of the durable function. It looks like: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. To stop debugging, press **Shift + F5** in VS Code.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. You should get the same status response as before when using the published app.

## <a name="next-steps"></a>Következő lépések

You have used Visual Studio Code to create and publish a JavaScript durable function app.

> [!div class="nextstepaction"]
> [Learn about common durable function patterns](durable-functions-overview.md#application-patterns)