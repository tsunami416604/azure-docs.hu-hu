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
ms.openlocfilehash: b57ec449c597984f8f4a53035be32047ec755274
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015347"
---
# <a name="azure-cli-samples"></a>Azure CLI-minták

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Webalkalmazás létrehozása és üzembe helyezése FTP-fájlok](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása és üzembe helyezi a fájlt FTP használatával. |
| [Webalkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása és üzembe helyezi a kódját egy nyilvános GitHub-adattárból. |
| [Webalkalmazás létrehozása folyamatos üzembe helyezéssel a GitHubról](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre a saját GitHub-adattárból folyamatos közzétételre. |
| [Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-tárházból](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása és kód leküldéses egy helyi Git-adattárból konfigurálja. |
| [Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetbe](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás kódmódosítás átmeneti üzembehelyezési pontot hoz létre. |
| [ASP.NET-magalapú webalkalmazás létrehozása Docker-tárolóban](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure-webalkalmazást Linux rendszeren, és betölti egy Docker-rendszerképet a Docker Hubból. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezése egy webalkalmazásra](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és egy egyéni tartománynevet képez. |
| [Egyéni SSL-tanúsítvány kötése egy webalkalmazáshoz](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és kötést hoz létre az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Webalkalmazások manuális méretezése](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és méretezi a 2-példányok között. |
| [Webalkalmazások globális méretezése magas rendelkezésre állású architektúrával](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két Azure-webalkalmazások létrehoz két különböző földrajzi régióban, és elérhetővé teszi azokat az Azure Traffic Managerrel egyetlen végponton keresztül. |
|**Alkalmazás csatlakoztatása erőforrásokhoz**||
| [Webalkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás és a egy SQL-adatbázist hoz létre, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazásbeállításokat. |
| [Webalkalmazás csatlakoztatása tárfiókhoz](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás és a egy storage-fiókot hoz létre, majd a tárfiók kapcsolati karakterláncának ad hozzá az alkalmazásbeállításokat. |
| [Webes alkalmazás csatlakoztatása az Azure Cache redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure-webalkalmazást és a egy Azure Cache redis, majd hozzáadja az alkalmazásbeállításokat a redis-kapcsolat adatai.) |
| [Webes alkalmazás csatlakoztatása a Cosmos DB-hez](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy Azure-webalkalmazást és a egy Cosmos DB, majd hozzáadja az alkalmazásbeállításokat a Cosmos DB-kapcsolat adatai. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [A webes alkalmazás biztonsági mentése](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és egyszeri biztonsági mentést hoz. |
| [Egy webalkalmazás ütemezett biztonsági másolat létrehozása](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és a ütemezett biztonsági mentés hozza létre. |
| [Visszaállít egy webalkalmazást egy biztonsági másolatból](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás visszaállítása biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [Webkiszolgáló-naplókkal-WebApp figyelése](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, engedélyezi a naplózást, és letölti a naplóit a helyi gépen. |
| | |