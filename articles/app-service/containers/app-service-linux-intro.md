---
title: Kód futtatása alapértelmezett Linux-tárolókon
description: Az Azure App Service előre elkészített Linux-tárolókon futtathatja a kódot. Ismerje meg, hogyan futtathatja Linux-webalkalmazásait az Azure-ban.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 27f085543869b1a77db9c97ca2e7ae7d3d3b7b88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046417"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

[Az Azure App Service](../overview.md) egy teljes körűen felügyelt számítási platform, amely webhelyek és webalkalmazások üzemeltetésére van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon.

## <a name="languages"></a>Nyelvek

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. Nyelvek: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core és Ruby. Futtassa [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) a legújabb nyelvek és támogatott verziók megtekintéséhez. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

## <a name="deployments"></a>Központi telepítés

* FTP
* Helyi Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Átmeneti környezetek
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) és DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konzol, közzététel és hibakeresés

* Környezetek
* Központi telepítés
* Alapszintű konzol
* SSH

## <a name="scaling"></a>Méretezés

* Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json) szintjének megváltoztatásával az ügyfelek le- és felskálázhatják webalkalmazásaikat

## <a name="locations"></a>Helyek

Ellenőrizze az [Azure állapot-irányítópultját](https://azure.microsoft.com/status).

## <a name="limitations"></a>Korlátozások

Az Azure Portal megjeleníti a Web App for Containers szolgáltatással jelenleg működő funkciókat. A további funkciók engedélyezésével azok megjelennek a portálon.

A Linuxon futó App Service csak [ingyenes, alapszintű, standard és prémium](https://azure.microsoft.com/pricing/details/app-service/plans/) alkalmazásszolgáltatási csomagokkal támogatott, és nem rendelkezik [megosztott](https://azure.microsoft.com/pricing/details/app-service/plans/) szinttel. Nem hozhat létre Linux Web Alkalmazást olyan App Service-csomagban, amely már nem Linuxos webalkalmazásokat üzemeltet.  

A jelenlegi korlátozás alapján, ugyanazon erőforráscsoport nem keverheti a Windows és linuxos alkalmazások ugyanabban a régióban.

## <a name="troubleshooting"></a>Hibaelhárítás

> [!NOTE]
> Az [Azure Monitoring (előzetes verzió)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) új integrált naplózási funkcióval rendelkezik. 
>
>

Ellenőrizze a LogFiles könyvtár Docker naplóit, ha az alkalmazás nem indul el, vagy ha meg szeretné tekinteni az alkalmazás naplózását. A könyvtárhoz az SCM-webhelyen vagy FTP-n keresztül férhet hozzá. A tároló `stdout` `stderr` naplózásához engedélyeznie kell **az Alkalmazásnaplózást** az **App Service-naplók**területen. A beállítás azonnal érvénybe lép. Az App Service észleli a módosítást, és automatikusan újraindítja a tárolót.

Az SCM-webhelyet a **Fejlesztési eszközök** menüben található** Haladó eszközök** oldalon érheti el.

![A Kudu használata a Docker naplók megtekintésére][1]

## <a name="next-steps"></a>További lépések

A következő cikkek a Linuxon futó App Service különböző nyelveken írt webalkalmazásokkal való használatának első lépéseit tartalmazzák:

* [.NET Core](quickstart-dotnetcore.md)
* [Php](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Ugrás](quickstart-docker-go.md)
* [Többtárolós alkalmazások](quickstart-multi-container.md)

A Linuxos App Service szolgáltatásról a következő témakörben talál további információt:

* [App Service Linuxhoz, GYIK](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub – folyamatos üzembe helyezés](app-service-linux-ci-cd.md)

Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
