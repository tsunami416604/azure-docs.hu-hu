---
title: A Linuxon futó App Service bemutatása | Microsoft Docs
description: Az Azure Linux App Service szolgáltatás ismertetése.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/09/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 9efa6dc8427c58c82702fd5b3449fcd4805bf9e3
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902093"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>A Linuxon futó Azure App Service bemutatása

A [Web App](../app-service-web-overview.md) egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. A Linuxon futó App Service használatával az ügyfelek natív módon üzemeltethetnek webalkalmazásokat a támogatott alkalmazáscsoportok számára a Linuxon. Az alábbi részek sorolják fel a jelenleg támogatott alkalmazáscsoportokat.

## <a name="languages"></a>Nyelvek

Az Linuxon futó App Service számos beépített rendszerképet támogat a fejlesztői termelékenység növelése érdekében. Ha a beépített rendszerképek nem támogatják az alkalmazás számára szükséges futtatókörnyezetet, akkor a [saját Docker rendszerkép felépítésére](tutorial-custom-docker-image.md) vonatkozó utasításokkal üzembe helyezheti azt a Web App for Containers szolgáltatásban.

| Nyelv | Támogatott verziók |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1 verzió |
| Java* | 8.0 |
| PHP | 5.6, 7.0, 7.2 |
| Python (előzetes verzió) | 3.7 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |
| Apache Tomcat | 8.5, 9.0 |

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

* Az [App Service-csomag](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json) szintjének megváltoztatásával az ügyfelek le- és felskálázhatják webalkalmazásaikat

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
* [PHP](quickstart-dotnetcore.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Többtárolós alkalmazások](quickstart-multi-container.md)

A Linuxon futó App Service további részleteihez lásd a következő cikkeket is:

* [App Service Linuxhoz, GYIK](app-service-linux-faq.md)
* [SSH-támogatás a Linuxon futó App Service számára](app-service-linux-ssh-support.md)
* [Átmeneti környezetek beállítása az App Service-ben](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub – folyamatos üzembe helyezés](app-service-linux-ci-cd.md)

Kérdéseit és észrevételeit megoszthatja [fórumunkon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
