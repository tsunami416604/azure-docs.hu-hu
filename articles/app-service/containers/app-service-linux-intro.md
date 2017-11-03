---
title: "Az App Service Linux bemutatása |} Microsoft Docs"
description: "További tudnivalók az Azure App Service Linux rendszeren."
keywords: az Azure app service, linux, oss
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
ms.openlocfilehash: 89cb7dc488da42724f212d13f8550064ff8b9188
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Bevezetés az Azure App Service Linux rendszeren

[Webalkalmazás](../app-service-web-overview.md) egy teljes körűen felügyelt számítógépes platform, amely webhelyek és webalkalmazások üzemeltetéséhez van optimalizálva. Felhasználók használhatják az App Service Linux gazdagép webalkalmazások Linux rendszeren natív módon a támogatott alkalmazás verem. Az alábbi szakaszok a jelenleg támogatott alkalmazás verem sorolja fel.

## <a name="languages"></a>Nyelvek

Linux App Service számos olyan beépített képek legyen a hatékonyság növelésére támogat. A az alkalmazás által kért futtatókörnyezet nem támogatja a beépített lemezképek, hogy vannak-e útmutatást [saját Docker lemezkép](tutorial-custom-docker-image.md) a tárolók a webes alkalmazás telepítéséhez.

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
* [Azure-tárolót beállításjegyzék](https://docs.microsoft.com/azure/container-registry/container-registry-intro) és DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konzol, a közzétételi és hibakeresés

* Környezetekben
* Központi telepítés
* Alapszintű konzol
* SSH

## <a name="scaling"></a>Méretezés

* Az ügyfelek is méretezhető webalkalmazások felfelé és lefelé a szint módosítása a [App Service-csomag](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Helyek

Ellenőrizze a [Azure állapota irányítópult](https://azure.microsoft.com/status).

## <a name="limitations"></a>Korlátozások

Az Azure-portálon csak a jelenleg működik a webalkalmazás az tárolókat funkciókat jeleníti meg. Engedélyezzük a szolgáltatásokat, mivel azok látható lesz a portálon.

Egyes szolgáltatások, például a virtuális hálózati integráció, az Azure Active Directory vagy harmadik fél hitelesítést és a Kudu helyhez kiterjesztések, még nem érhető el. Ezek a funkciók érhetők el, ha a dokumentáció és a változásokról blog frissítjük.

App Service Linux rendszeren csak a támogatott [Basic és Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) app service-csomagok, és nem rendelkezik egy [ingyenes vagy közös](https://azure.microsoft.com/pricing/details/app-service/plans/) réteg. Az alábbiakban is az App Service Linux fontos korlátozások:

* Tárolók webalkalmazást az App Service-csomag már üzemeltet a nem Linux webes alkalmazások nem hozható létre.
* A nem Linux Web Apps tartalmazó erőforráscsoport tárolók webalkalmazás létrehozásakor egy másik régióban, mint a meglévő App Service-csomag App Service-csomagot kell létrehoznia.

## <a name="troubleshooting"></a>Hibaelhárítás

Az alkalmazás nem indul el, vagy a naplózás a alkalmazás ellenőrizni kívánja, hogy a Docker naplózza a naplófájlok könyvtárban. Ez a könyvtár az SCM-hely vagy FTP-n keresztül érheti el.
A napló a `stdout` és `stderr` a tárolóból, engedélyezni kell a **Docker-tároló naplózási** alatt **diagnosztikai naplók**.

![Naplózás engedélyezése][2]

![A Kudu segítségével Docker naplók megtekintése][1]

Érheti el az SCM helyet **speciális eszközök** a a **Fejlesztőeszközök** menü.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az alábbi hivatkozásokra kattintva az App Service Linux rendszeren. Kérdések és problémákat is közzétesz a [fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [A tárolók webalkalmazás egyéni Docker-lemezkép használata](quickstart-custom-docker-image.md)
* [Használatával a .NET Core Linux Azure App Service-ben](quickstart-dotnetcore.md)
* [Ruby használata Linux Azure App Service-ben](quickstart-ruby.md)
* [Az Azure App Service webalkalmazás tárolók – gyakori kérdések](app-service-linux-faq.md)
* [Az Azure App Service-Linux SSH-támogatás](app-service-linux-ssh-support.md)
* [Átmeneti környezet az Azure App Service beállítása](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub folyamatos üzembe a webes alkalmazás tárolók](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
