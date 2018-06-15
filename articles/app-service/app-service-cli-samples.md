---
title: Az Azure CLI minták – az App Service |} Microsoft Docs
description: Az Azure CLI minták – az App Service
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
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
ms.locfileid: "26658364"
---
# <a name="azure-cli-samples"></a>Az Azure CLI-minták

A következő táblázat a bash parancsfájlok az Azure parancssori felület használatával készített mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Webalkalmazás létrehozása és központi telepítése FTP-vel fájlok](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure webalkalmazás és FTP használatával telepíti a fájl. |
| [Webalkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozza, és egy nyilvános GitHub-tárházban lévő kód telepíti. |
| [Webalkalmazás létrehozása folyamatos üzembe helyezéssel a GitHubról](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Saját GitHub-tárházban folyamatos közzététel hoz létre egy Azure-webalkalmazásban. |
| [Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-tárházból](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása, és konfigurálja a helyi Git-tárház kód leküldéses. |
| [Webalkalmazás létrehozása és kód üzembe helyezése átmeneti környezetbe](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás egy üzembe helyezési pont kódmódosításokat átmeneti hoz létre. |
| [ASP.NET-magalapú webalkalmazás létrehozása Docker-tárolóban](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása a Linux és a Docker-lemezkép betölti a Docker Hub. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezése egy webalkalmazásra](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása és egy egyéni tartománynevet rendel. |
| [Egyéni SSL-tanúsítvány kötése a webes alkalmazás](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás létrehozása, és az SSL-tanúsítvány egy egyéni tartománynév kötődik. |
|**Skála alkalmazás**||
| [Webalkalmazások manuális méretezése](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása és méretezi a 2 példányok között. |
| [Webalkalmazások globális méretezése magas rendelkezésre állású architektúrával](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két Azure-webalkalmazásokban két különböző földrajzi régió hoz létre, és egy végpontot, használja az Azure Traffic Manager keresztül elérhetővé válnak. |
|**Alkalmazás kapcsolódni tudjon erőforrásokhoz**||
| [Webes alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy Azure web app és az SQL-adatbázis, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazás beállításaiban. |
| [Webalkalmazás csatlakoztatása tárfiókhoz](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Azure-webalkalmazás és a storage-fiók létrehozása, majd hozzáadja a tárolási kapcsolati karakterlánc az alkalmazás beállításaiban. |
| [Egy webes alkalmazás csatlakoztatása a redis gyorsítótár](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás és a redis gyorsítótár hoz létre, majd hozzáadja a redis kapcsolódási adatait az alkalmazás beállításaiban.) |
| [Webes alkalmazás csatlakoztatása az Cosmos-Adatbázishoz](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás és egy Cosmos DB hoz létre, majd hozzáadja a Cosmos DB kapcsolódási adatait az alkalmazás beállításai. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [Készítsen biztonsági másolatot a webes alkalmazás](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása és a egyszeri biztonsági hozza létre. |
| [Ütemezett biztonsági mentés egy webalkalmazás létrehozása](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása és a hozza létre az ütemezett biztonsági mentés. |
| [A webes alkalmazás visszaállítja egy biztonsági másolatból](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás visszaállítja egy biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [Webalkalmazás figyelése a webkiszolgáló-naplókkal](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Azure-webalkalmazás létrehozása, engedélyezi a naplózást, és a naplók letölti a helyi számítógépre. |
| | |