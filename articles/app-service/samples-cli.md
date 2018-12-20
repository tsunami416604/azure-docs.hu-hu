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
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628374"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-minták az Azure App Service-ben

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása és üzembe helyezése FTP-fájlok](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és üzembe helyezi a fájlt FTP használatával. |
| [Alkalmazás létrehozása és kód üzembe helyezése a Githubról](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és üzembe helyezi a kódját egy nyilvános GitHub-adattárból. |
| [Hozzon létre egy alkalmazást a Githubról történő folyamatos üzembe helyezéssel](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazás folyamatos közzététel a saját GitHub-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és konfigurálja a kód leküldéses egy helyi Git-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | App Service-alkalmazások kódmódosítás átmeneti üzembehelyezési pontot hoz létre. |
| [ASP.NET Core-alkalmazás létrehozása a Docker-tárolóban](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazás Linux rendszeren, és betölti egy Docker-rendszerképet a Docker Hubból. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány hozzárendelése egy alkalmazáshoz](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és egy egyéni tartománynevet képez. |
| [Egyéni SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és kötést hoz létre az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális skálázása](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és méretezi a 2-példányok között. |
| [Átviteli alkalmazás világszerte, magas rendelkezésre állású architektúrával](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz két App Service-alkalmazások két különböző földrajzi régióban, és elérhetővé teszi azokat az Azure Traffic Managerrel egyetlen végponton keresztül. |
|**Alkalmazás csatlakoztatása erőforrásokhoz**||
| [Alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| App Service-alkalmazások és a egy SQL-adatbázist hoz létre, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazásbeállításokat. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| App Service-alkalmazások és a egy storage-fiókot hoz létre, majd a tárfiók kapcsolati karakterláncának ad hozzá az alkalmazásbeállításokat. |
| [Alkalmazás csatlakoztatása az Azure Cache redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazás és a egy Azure Cache redis, majd hozzáadja az alkalmazásbeállításokat a redis-kapcsolat adatai.) |
| [Az alkalmazás csatlakoztatása a Cosmos DB használatával](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazás és a egy Cosmos DB, majd hozzáadja az alkalmazásbeállításokat a Cosmos DB-kapcsolat adatai. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [Alkalmazás biztonsági mentése](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és egyszeri biztonsági mentést hoz. |
| [Egy ütemezett biztonsági másolat egy alkalmazás létrehozása](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és a ütemezett biztonsági mentés hozza létre. |
| [Visszaállít egy alkalmazást egy biztonsági másolatból](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | App Service-alkalmazások visszaállít egy biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [A figyelő egy alkalmazást a webkiszolgáló-naplókkal](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, engedélyezi a naplózást, és letölti a naplóit a helyi gépen. |
| | |