---
title: "Hozza létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy ASP.NET-mintaalkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c55f844b89729b1ef5a6316b0b2731472074219e
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Hozza létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

A rövid útmutató segítségével csupán pár perc alatt üzembe helyezheti az első ASP.NET-webalkalmazását az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).

A rövid útmutató elindítása előtt győződjön meg róla, hogy [az Azure CLI telepítve van](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) a gépen.

## <a name="create-an-aspnet-web-app-in-azure"></a>ASP.NET-webalkalmazás létrehozása
2. Jelentkezzen be az Azure-ba az `az login` parancs futtatásával, és kövesse a képernyőn látható utasításokat.
   
    ```azurecli
    az login
    ```
   
3. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). Ide kell helyeznie az összes olyan Azure-erőforrást, amelyet közösen szeretne kezelni, mint például a webappot és a hozzá tartozó SQL Database-háttérrendszert.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    A `---location` paraméterhez használható lehetséges értékek megtekintéséhez használja az `az appservice list-locations` Azure CLI-parancsot.

3. Hozzon létre egy „INGYENES” [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Hozzon létre egy új, egyéni névvel rendelkező webappot az `<app_name>` paraméterben.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Helyezzen üzembe egy ASP.NET-mintaalkalmazást a GitHubból.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git" --branch master --manual-integration 
    ```

5. Az Azure-alkalmazást az alábbi paranccsal tekintheti meg működés közben:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Gratulálunk, az első ASP.NET-webalkalmazása immáron elérhető az Azure App Service-ben!

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az előre létrehozott, [webappokhoz készült CLI-szkriptek vizsgálata](app-service-cli-samples.md).

