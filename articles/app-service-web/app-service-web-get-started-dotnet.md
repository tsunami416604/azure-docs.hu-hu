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
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: a428e183e7535c7745e214332f8e449b4a8bf22c
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Hozza létre az első ASP.NET-webalkalmazását öt perc alatt az Azure-ban

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

A rövid útmutató segítségével csupán pár perc alatt üzembe helyezheti az első ASP.NET-webalkalmazását az [Azure App Service-ben](../app-service/app-service-value-prop-what-is.md).

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure-ba az `az login` parancs futtatásával, és kövesse a képernyőn látható utasításokat.
   
```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot   
Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md). Ide kell helyeznie az összes olyan Azure-erőforrást, amelyet közösen szeretne kezelni, mint például a webappot és a hozzá tartozó SQL Database-háttérrendszert.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

A `---location` paraméterhez használható lehetséges értékek megtekintéséhez használja az `az appservice list-locations` Azure CLI-parancsot.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása
Hozzon létre egy „INGYENES” [App Service-csomagot](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
Hozzon létre egy webappot, amelyhez az `<app_name>` helyén megad egy egyedi nevet.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>Mintaalkalmazás üzembe helyezése
Helyezzen üzembe egy ASP.NET-mintaalkalmazást a GitHubból.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Webalkalmazás kikeresése
Az Azure-alkalmazást az alábbi paranccsal tekintheti meg működés közben:

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Gratulálunk, az első ASP.NET-webalkalmazása immáron elérhető az Azure App Service-ben!

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az előre létrehozott, [webappokhoz készült CLI-szkriptek vizsgálata](app-service-cli-samples.md).

