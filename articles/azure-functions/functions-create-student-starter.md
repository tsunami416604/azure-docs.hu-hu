---
title: Create a Function using Azure for Students Starter
description: Learn how to create an Azure Function from within an Azure for Student Starter subscription
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: b86ea2367a17baa8c57b8d0c0331c0cd3cfbb481
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227104"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Create a Function using Azure for Students Starter

In this tutorial, we will create a hello world HTTP function in an Azure for Students Starter subscription. We'll also walk through what's available in Azure Functions in this subscription type.

Microsoft *Azure for Students Starter* gets you started with the Azure products you need to develop in the cloud at no cost to you. [Learn more about this offer here.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Az Azure Functions lehetővé teszi a kód [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetben történő végrehajtását anélkül, hogy először létre kellene hoznia egy virtuális gépet, vagy közzé kellene tennie egy webalkalmazást. [Learn more about Functions here.](./functions-overview.md)

## <a name="create-a-function"></a>Create a Function

 In this topic, learn how to use Functions to create an HTTP triggered "hello world" function in the Azure portal.

![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. 

1. Válassza az Azure Portal bal felső sarkában található **Új** gombot, majd válassza a **Számítás** > **Függvényalkalmazás** lehetőséget.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-student-starter/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
   | **[App Service Plan/Location](./functions-scale.md)** | Új | The hosting plan that controls what region your function app is deployed to and the density of your resources. Multiple Function Apps deployed to the same plan will all share the same single free instance. This is a restriction of the Student Starter plan. The full hosting options are [explained here.](./functions-scale.md)|
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**[Application Insights](./functions-monitoring.md)**| Engedélyezve | Application Insights is used to store and analyze your function app's logs. It is enabled by default if you choose a location that supports Application Insights. Application Insights can be enabled for any function by manually choosing a nearby region to deploy Application Insights. Without Application Insights, you will only be able to view live streaming logs.

3. Select **App Service plan/Location** above to choose a different location

4. Select **Create new** and then give your plan a unique name.

5. Select the location closest to you. [See a full map of Azure regions here.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-student-starter/function-app-create-notification.png)

8. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

## <a name="create-function"></a>HTTP által aktivált függvény létrehozása

1. Bontsa ki az új függvényalkalmazást, majd kattintson a **Függvények** elem melletti **+** gombra, és válassza a **Portálba épített**, majd a **Folytatás** lehetőséget.

    ![Függvény rövid útmutatója – platform kiválasztása](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Válassza a **WebHook + API**, majd a **Létrehozás** lehetőséget.

    ![A függvények gyors létrehozása az Azure Portalon.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

A rendszer létrehoz egy függvényt egy HTTP által indított függvény nyelvspecifikus sablonjának használatával.

Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben kattintson a **</> Függvény URL-címének beolvasása** elemre a jobb felső sarokban, válassza a **default (Function key)** (alapértelmezett (funkcióbillentyű)) lehetőséget, majd kattintson a **Másolás** gombra. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Illessze be a függvény URL-címét a böngésző címsorába. Az URL-cím végéhez adja hozzá a `&name=<yourname>` lekérdezési sztring értéket, majd nyomja le az `Enter` billentyűt a billentyűzeten a kérés végrehajtásához. Ekkor a függvény által visszaadott válasz jelenik meg a böngészőben.  

    Az alábbi példa a böngészőben visszaadott választ mutatja:

    ![A függvény által visszaadott válasz a böngészőben.](./media/functions-create-student-starter/function-app-browser-testing.png)

    A kérelem URL-címe alapértelmezés szerint tartalmazza a függvény HTTP protokollon keresztüli eléréséhez szükséges kulcsot.

3. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. Az előző végrehajtás nyomkövetési adatainak megtekintéséhez térjen vissza a függvényhez a portálon, és kattintson a képernyő alján található nyílra a **Naplók** elem kibontásához.

   ![A függvények naplómegtekintője az Azure Portalon.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Supported Features in Azure for Students Starter

In Azure for Student Starter you have access to most of the features of the Azure Functions runtime, with several key limitations listed below:

* The HTTP trigger is the only trigger type supported.
    * All input and all output bindings are supported! [See the full list here.](functions-triggers-bindings.md)
* Languages Supported: 
    * C# (.NET Core 2)
    * Javascript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [See languages supported in higher plans here](supported-languages.md)
* Windows is the only supported operating system.
* Scale is restricted to [one free tier instance](https://azure.microsoft.com/pricing/details/app-service/windows/) running for up to 60 minutes each day. You will serverlessly scale from 0 to 1 instance automatically as HTTP traffic is received, but no further.
* Only [the 2.x runtime](functions-versions.md) is supported.
* All developer tooling is supported for editing and publishing functions. This includes VS Code, Visual Studio, the Azure CLI, and the Azure portal. If you'd like to use anything other than the portal, you will need to first create an app in the portal, and then choose that app as a deployment target in your preferred tool.

## <a name="next-steps"></a>Következő lépések

You have created a function app with a simple HTTP triggered function! Now you can explore local tooling, more languages, monitoring, and integrations.

 * [Az első függvény létrehozása a Visual Studio használatával](./functions-create-your-first-function-visual-studio.md)
 * [Create your first function using Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript developer guide](./functions-reference-node.md)
 * [Use Azure Functions to connect to an Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Learn more about Azure Functions HTTP bindings](./functions-bindings-http-webhook.md).
 * [Monitor your Azure Functions](./functions-monitoring.md)
