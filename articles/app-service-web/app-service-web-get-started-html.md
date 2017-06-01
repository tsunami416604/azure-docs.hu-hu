---
title: "Hozzon létre egy statikus HTML-webappot öt perc alatt az Azure-ban | Microsoft Docs"
description: "Egy mintaalkalmazás üzembe helyezésével megtudhatja, mennyire egyszerű a webalkalmazások futtatása az App Service-ben."
services: app-service\web
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/08/2017
ms.author: riande
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 895906e1ab4bc50093ed3b18f043c3dd515ca054
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017

---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Hozzon létre egy statikus HTML-webappot öt perc alatt az Azure-ban

Ez a gyors útmutató végigvezet egy egyszerű HTML+CSS webhely létrehozásán az Azure-ban. Az alkalmazást az [Azure App Service-csomag](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) segítségével futtatjuk, az új webalkalmazást pedig az [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) segítségével hozzuk létre. Az alkalmazást a Git segítségével helyezzük üzembe az Azure-ban. Az előfeltételek telepítése után az oktatóanyag elvégzése mintegy öt percet vesz igénybe.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="prerequisites"></a>Előfeltételek

A minta létrehozása előtt az alábbi összetevőket kell letölteni és telepíteni:

- [Git](https://git-scm.com/)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a mintaalkalmazás tárházát a helyi számítógépre egy terminál-ablakban:

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="view-the-html"></a>HTML megjelenítése

Váltson a minta HTML-t tartalmazó könyvtárra. Nyissa meg a *index.html* fájlt a böngészőben.

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser.png)

[!INCLUDE [login-to-azure](../../includes/login-to-azure.md)] 
[!INCLUDE [configure-deployment-user](../../includes/configure-deployment-user.md)] 

[!INCLUDE [app-service-web-quickstart1](../../includes/app-service-web-quickstart1.md)] 

[Webalkalmazás](app-service-web-overview.md) létrehozása az `quickStartPlan`App Service-csomagban. A webalkalmazás tárhelyet biztosít a kód számára, és URL-címet ad meg a telepített alkalmazás megtekintéséhez.

[!INCLUDE [app-service-web-quickstart2](../../includes/app-service-web-quickstart2.md)] 

Az oldal Azure App Service webalkalmazásként üzemel:

![hello-world-in-browser](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

## <a name="update-and-redeploy-the-app"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

Nyissa meg a *index.html* fájlt. Változtassa meg a lapot. Írja be például, hogy `Hello Azure!` a `Hello world!` helyett

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated HTML"
git push azure master
```

A telepítés befejezése után frissítse a lapot a böngészőben, hogy lássa a változásokat.

[!INCLUDE [manage-azure-web-app](../../includes/manage-azure-web-app.md)]


[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

- [Web Apps CLI-mintaszkriptek](app-service-cli-samples.md) vizsgálata.
- Tudja meg, hogy [képezhet le egyéni tartománynevet](app-service-web-tutorial-custom-domain.md), amilyen a contoso.com egy [App Service alkalmazásba](app-service-web-tutorial-custom-domain.md).
