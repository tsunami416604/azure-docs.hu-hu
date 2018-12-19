---
title: Az Azure CLI-minták – az App Service |} A Microsoft Docs
description: Az Azure CLI-minták – App Service-ben
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: af28f0fc8a75f32678d94523a01f991ab3d27351
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53585063"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-minták az Azure App Service-ben

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Webalkalmazás létrehozása és üzembe helyezése FTP-fájlok](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása és üzembe helyezi a fájlt FTP használatával. |
| [Webalkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása és üzembe helyezi a kódját egy nyilvános GitHub-adattárból. |
| [Webalkalmazás létrehozása folyamatos üzembe helyezéssel a GitHubról](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre a saját GitHub-adattárból folyamatos közzétételre. |
| [Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-tárházból](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása és kód leküldéses egy helyi Git-adattárból konfigurálja. |
| [Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetbe](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás kódmódosítás átmeneti üzembehelyezési pontot hoz létre. |
| [ASP.NET-magalapú webalkalmazás létrehozása Docker-tárolóban](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure-webalkalmazást Linux rendszeren, és betölti egy Docker-rendszerképet a Docker Hubból. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezése egy webalkalmazásra](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és egy egyéni tartománynevet képez. |
| [Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és kötést hoz létre az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Webalkalmazások manuális méretezése](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és méretezi a 2-példányok között. |
| [Webalkalmazások globális méretezése magas rendelkezésre állású architektúrával](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két Azure-webalkalmazások létrehoz két különböző földrajzi régióban, és elérhetővé teszi azokat az Azure Traffic Managerrel egyetlen végponton keresztül. |
|**Alkalmazás csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás és a egy SQL-adatbázist hoz létre, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazásbeállításokat. |
| [Webalkalmazás csatlakoztatása tárfiókhoz](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás és a egy storage-fiókot hoz létre, majd a tárfiók kapcsolati karakterláncának ad hozzá az alkalmazásbeállításokat. |
| [Webes alkalmazás csatlakoztatása az Azure Cache redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure-webalkalmazást és a egy Azure Cache redis, majd hozzáadja az alkalmazásbeállításokat a redis-kapcsolat adatai.) |
| [Webes alkalmazás csatlakoztatása a Cosmos DB-hez](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure-webalkalmazást és a egy Cosmos DB, majd hozzáadja az alkalmazásbeállításokat a Cosmos DB-kapcsolat adatai. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [A webes alkalmazás biztonsági mentése](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és egyszeri biztonsági mentést hoz. |
| [Egy webalkalmazás ütemezett biztonsági másolat létrehozása](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és a ütemezett biztonsági mentés hozza létre. |
| [Visszaállít egy webalkalmazást egy biztonsági másolatból](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás visszaállítása biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [Webkiszolgáló-naplókkal-WebApp figyelése](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, engedélyezi a naplózást, és letölti a naplóit a helyi gépen. |
| | |