---
title: CLI-minták
description: Tekintse meg az Azure CLI-mintákat néhány gyakori App Service forgatókönyvhöz. Ismerje meg, hogyan automatizálhatja App Service telepítési vagy felügyeleti feladatait.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 06f03e0005c4f98370bb1f6430fff506f9145d5a
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688517"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-minták a Azure App Servicehoz

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása és fájlok üzembe helyezése FTP-vel](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és telepít egy fájlt az FTP használatával. |
| [Alkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és egy nyilvános GitHub-tárházból helyezi üzembe a kódot. |
| [Hozzon létre egy alkalmazást a GitHubról történő folyamatos üzembe helyezéssel](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást folyamatos közzétételsel a saját GitHub-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése helyi git-tárházból](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és beállítja a kód leküldését egy helyi git-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást egy üzembe helyezési ponttal az átmeneti kód változásaihoz. |
| [ASP.NET Core-alkalmazás létrehozása Docker-tárolóban](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást Linux rendszeren, és betölt egy Docker-rendszerképet a Docker hub-ból. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezése egy alkalmazásra](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és egyéni tartománynevet rendel hozzá. |
| [Egyéni SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást, és egy egyéni tartománynév SSL-tanúsítványát köti hozzá. |
|**Alkalmazás méretezése**||
| [Alkalmazások manuális méretezése](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és két példány között méretezi azt. |
| [Az alkalmazások globális skálázása magas rendelkezésre állású architektúrával](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két App Service alkalmazást hoz létre két különböző földrajzi régióban, és egyetlen végponton keresztül elérhetővé teszi őket az Azure Traffic Manager használatával. |
|**Az alkalmazás összekötése az erőforrásokkal**||
| [Alkalmazás összekötése egy SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást és egy SQL-adatbázist, majd hozzáadja az adatbázis-kapcsolódási karakterláncot az alkalmazás beállításaihoz. |
| [Alkalmazás összekötése egy Storage-fiókkal](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service alkalmazást és egy Storage-fiókot, majd hozzáadja a Storage-kapcsolódási karakterláncot az alkalmazás beállításaihoz. |
| [Alkalmazás összekötése egy Azure cache-Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást és egy Azure-gyorsítótárat a Redis, majd hozzáadja az Redis kapcsolati adatait az alkalmazás beállításaihoz.) |
| [Alkalmazás összekötése Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást és egy Cosmos DB, majd hozzáadja a Cosmos DB kapcsolat részleteit az alkalmazás beállításaihoz. |
|**Alkalmazás biztonsági mentése és visszaállítása**||
| [Alkalmazás biztonsági mentése](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és létrehoz egy egyszeri biztonsági mentést. |
| [Ütemezett biztonsági mentés létrehozása egy alkalmazáshoz](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, és létrehoz egy ütemezett biztonsági mentést. |
| [Egy alkalmazás visszaállítása biztonsági másolatból](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Visszaállítja egy App Service alkalmazást egy biztonsági másolatból. |
|**Alkalmazás figyelése**||
| [Alkalmazás figyelése webkiszolgáló-naplókkal](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service alkalmazást, lehetővé teszi a naplózást, és letölti a naplókat a helyi gépre. |
| | |