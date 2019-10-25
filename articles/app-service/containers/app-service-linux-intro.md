---
title: A Linux App Service bemutatása – Azure | Microsoft Docs
description: Az Azure Linux App Service szolgáltatás ismertetése.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819686"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

A [Azure app Service](../overview.md) egy teljes körűen felügyelt számítási platform, amely webhelyek és webalkalmazások üzemeltetésére van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon.

## <a name="languages"></a>Nyelvek

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. A nyelvek közé tartozik a Node. js, a Java (JRE 8 & JRE 11), a PHP, a Python, a .NET Core és a Ruby. [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) futtatásával megtekintheti a legújabb nyelveket és a támogatott verziókat. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

## <a name="deployments"></a>Központi telepítés

* FTP
* Helyi Git
* GitHub
* Bitbucket

## <a name="devops"></a>Fejlesztés és üzemeltetés

* Átmeneti környezetek
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) és DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konzol, közzététel és hibakeresés

* Környezetek
* Központi telepítés
* Alapszintű konzol
* SSH

## <a name="scaling"></a>Méretezés

* Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json) szintjének megváltoztatásával az ügyfelek le- és felskálázhatják webalkalmazásaikat

## <a name="locations"></a>Földrajzi egységek

Ellenőrizze az [Azure állapot-irányítópultját](https://azure.microsoft.com/status).

## <a name="limitations"></a>Korlátozások

Az Azure Portal megjeleníti a Web App for Containers szolgáltatással jelenleg működő funkciókat. A további funkciók engedélyezésével azok megjelennek a portálon.

A Linuxon App Service csak [ingyenes, alapszintű, standard és prémium szintű](https://azure.microsoft.com/pricing/details/app-service/plans/) app Service-csomagokkal támogatott, és nem rendelkezik [megosztott](https://azure.microsoft.com/pricing/details/app-service/plans/) szinttel. Nem hozhat létre linuxos webalkalmazást olyan App Service-csomagban, amely már nem Linux-Web Apps üzemeltet.  

Az egyazon erőforráscsoporthoz tartozó jelenlegi korlátozás alapján nem keverheti össze a Windows és Linux rendszerű alkalmazásokat ugyanabban a régióban.

## <a name="troubleshooting"></a>Hibakeresés

Ellenőrizze a LogFiles könyvtár Docker naplóit, ha az alkalmazás nem indul el, vagy ha meg szeretné tekinteni az alkalmazás naplózását. A könyvtárhoz az SCM-webhelyen vagy FTP-n keresztül férhet hozzá. A `stdout` és `stderr` a tárolóból való beléptetéséhez engedélyeznie kell a **Docker-tároló naplózását** a **app Service naplók**területen. A beállítás azonnal érvénybe lép. App Service észleli a változást, és automatikusan újraindítja a tárolót.

Az SCM-webhelyet a **Fejlesztési eszközök** menüben található **Haladó eszközök** oldalon érheti el.

![A Kudu használata a Docker naplók megtekintésére][1]

## <a name="next-steps"></a>Következő lépések

A következő cikkek a Linuxon futó App Service különböző nyelveken írt webalkalmazásokkal való használatának első lépéseit tartalmazzák:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Többtárolós alkalmazások](quickstart-multi-container.md)

A Linux App Service kapcsolatos további információkért lásd:

* [App Service Linuxhoz, GYIK](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub – folyamatos üzembe helyezés](app-service-linux-ci-cd.md)

Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
