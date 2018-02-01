---
title: "A Linuxon futó App Service bemutatása | Microsoft Docs"
description: "Az Azure Linux App Service szolgáltatás ismertetése."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: dea6b137fb12467a881afaa9a3e6985b584f5559
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

A [Web App](../app-service-web-overview.md) egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon. Az alábbi részek sorolják fel a jelenleg támogatott alkalmazáscsoportokat.

## <a name="languages"></a>Nyelvek

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

| Nyelv | Támogatott verziók |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1 |
| Ruby | 2.3 |

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

* Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json) szintjének megváltoztatásával az ügyfelek le- és felskálázhatják webalkalmazásaikat

## <a name="locations"></a>Helyek

Ellenőrizze az [Azure állapot-irányítópultját](https://azure.microsoft.com/status).

## <a name="limitations"></a>Korlátozások

Az Azure Portal megjeleníti a Web App for Containers szolgáltatással jelenleg működő funkciókat. A további funkciók engedélyezésével azok megjelennek a portálon.

A virtuálishálózat-integrációhoz, Azure Active Directory/külső fél közötti hitelesítéshez vagy Kudu webhelybővítményekhez hasonló funkciók még nem elérhetőek. Frissíteni fogjuk a dokumentációt és blogunkat, amikor ezek a funkciók elérhetővé válnak.

A Linuxon futó App Service szolgáltatást csak az [Alapszintű és Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service-csomagok támogatják, és nem rendelkezik [Ingyenes vagy Közös](https://azure.microsoft.com/pricing/details/app-service/plans/) szinttel. Az alábbi korlátozások szintén fontosak a Linuxon futó App Service esetében:

* Nem hozhat létre Web App for Containers szolgáltatást olyan App Service-csomagban, amely már üzemeltet nem Linuxos webalkalmazásokat.
* Nem Linuxos webalkalmazásokat tartalmazó erőforráscsoportokhoz készült Web App for Containers létrehozása esetén a létező App Service-csomag erőforráscsoportjától eltérő App Service-csomagot kell létrehoznia.

## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze a LogFiles könyvtár Docker naplóit, ha az alkalmazás nem indul el, vagy ha meg szeretné tekinteni az alkalmazás naplózását. A könyvtárhoz az SCM-webhelyen vagy FTP-n keresztül férhet hozzá.
Az `stdout` és az `stderr` naplózásához a tárolóból engedélyeznie kell a **Docker-tároló naplózását** a **Diagnosztikai naplók** területen.

![Naplózás engedélyezése][2]

![A Kudu használata a Docker naplók megtekintésére][1]

Az SCM-webhelyet a **Fejlesztési eszközök** menüben található **Haladó eszközök** oldalon érheti el.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a Linuxon futó App Service szolgáltatással az alábbi hivatkozásokat követve. Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Egyéni Docker-rendszerkép használata a Web App for Containers szolgáltatásban](quickstart-custom-docker-image.md)
* [A .NET Core használata a Linuxon futó Azure App Service-ben](quickstart-dotnetcore.md)
* [A Ruby használata a Linuxon futó Azure App Service-ben](quickstart-ruby.md)
* [Azure App Service Web App for Containers – gyakori kérdések](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó Azure App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az Azure App Service-ben](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub folyamatos üzembe helyezése Web App for Containers használatával](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
