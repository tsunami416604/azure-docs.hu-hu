---
title: Bevezetés az App Service linuxon – Azure |} A Microsoft Docs
description: Az Azure Linux App Service szolgáltatás ismertetése.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 898d663f3ef9a71944d96b0978947d10a3e26b06
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232787"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

A [Web App](../overview.md) egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon. Az alábbi részek sorolják fel a jelenleg támogatott alkalmazáscsoportokat.

## <a name="languages"></a>Nyelvek

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

| Nyelv | Támogatott verziók |
|---|---|
| Node.js | 4.4-es, 4.5-ös, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0-s, 8.1, 8,2, 8.8, 8,9, 8.11, 9.4, 10.1,10.10 |
| Java* | A tomcat 8.5, 9.0 Java SE, WildFly 14 (összes futó JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (előzetes verzió) | 2.7-es, 3.6, 3.7. |
| .NET Core | 1.0-s, 1.1, 2.0-s, 2.1-es verziója |
| Ruby | 2.3 |

További információt a [Java-webalkalmazás létrehozása a Linuxon futó App Service-ben](https://docs.microsoft.com/azure/app-service/containers/quickstart-java) című cikkben tekinthet meg.

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

A virtuálishálózat-integrációhoz, Azure Active Directory/külső fél közötti hitelesítéshez vagy Kudu webhelybővítményekhez hasonló funkciók még nem elérhetőek. Frissíteni fogjuk a dokumentációt és blogunkat, amikor ezek a funkciók elérhetővé válnak.

A Linuxon futó App Service szolgáltatást csak az [Alapszintű, a Standard és a Prémium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service-csomagok támogatják, és nem rendelkezik [Ingyenes vagy Közös](https://azure.microsoft.com/pricing/details/app-service/plans/) szinttel. Nem hozhat létre Web App for Containers szolgáltatást olyan App Service-csomagban, amely már üzemeltet nem Linuxos webalkalmazásokat. A rendszer emellett jelenleg azt sem engedélyezi, hogy ugyanazon erőforráscsoporton belül Windows- és Linux-alkalmazások is legyenek.

## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze a LogFiles könyvtár Docker naplóit, ha az alkalmazás nem indul el, vagy ha meg szeretné tekinteni az alkalmazás naplózását. A könyvtárhoz az SCM-webhelyen vagy FTP-n keresztül férhet hozzá.
Az `stdout` és az `stderr` naplózásához a tárolóból engedélyeznie kell a **Docker-tároló naplózását** a **Diagnosztikai naplók** területen.

![Naplózás engedélyezése][2]

![A Kudu használata a Docker naplók megtekintésére][1]

Az SCM-webhelyet a **Fejlesztési eszközök** menüben található **Haladó eszközök** oldalon érheti el.

## <a name="next-steps"></a>További lépések

A következő cikkek a Linuxon futó App Service különböző nyelveken írt webalkalmazásokkal való használatának első lépéseit tartalmazzák:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Többtárolós alkalmazások](quickstart-multi-container.md)

A Linuxon futó App Service további részleteihez lásd a következő cikkeket is:

* [App Service Linuxhoz, GYIK](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az App Service-ben](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub – folyamatos üzembe helyezés](app-service-linux-ci-cd.md)

Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
