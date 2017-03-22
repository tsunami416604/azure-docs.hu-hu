---
title: "Hozza létre az első PHP-webappját öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy PHP-mintaalkalmazás üzembe helyezésével megismerheti, mennyire egyszerű a webalkalmazások futtatása az App Service-ben."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 9a756618549cafc41c4f09683fd710748bf7b411
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>Hozza létre az első PHP-webappját öt perc alatt az Azure-ban
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

A rövid útmutató segítségével csupán pár perc alatt üzembe helyezheti az első PHP-webappját az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).

A rövid útmutató elindítása előtt győződjön meg róla, hogy [az Azure CLI telepítve van](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) a gépen.

## <a name="create-a-php-web-app-in-azure"></a>PHP-webapp létrehozása az Azure-ban
   
2. Jelentkezzen be az Azure-ba az `az login` parancs futtatásával, és kövesse a képernyőn látható utasításokat.
   
    ```azurecli
    az login
    ```
   
3. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). Ide kell helyeznie az összes olyan Azure-erőforrást, amelyet közösen szeretne kezelni, mint például a webappot és a hozzá tartozó SQL Database-háttérrendszert.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    A `---location` paraméterhez használható lehetséges értékek megtekintéséhez használja az `az appservice list-locations` Azure CLI-parancsot.

3. Hozzon létre egy „Standard” [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). A Linux-tárolók futtatásához Standard csomag szükséges.

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku S1 --is-linux 
    ```

4. Hozzon létre egy webappot, amelyhez az `<app_name>` helyén megad egy egyedi nevet.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Konfigurálja a Linux-tárolót az alapértelmezett PHP 7.0.6-rendszerkép használatára.

    ```azurecli
    az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
    ```

4. Helyezzen üzembe egy PHP-mintaappot a GitHubból.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
    ```

5. Az Azure-alkalmazást az alábbi paranccsal tekintheti meg működés közben:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Gratulálunk, az első PHP-webappja immáron elérhető az Azure App Service-ben.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az előre létrehozott, [webappokhoz készült CLI-szkriptek vizsgálata](app-service-cli-samples.md).

