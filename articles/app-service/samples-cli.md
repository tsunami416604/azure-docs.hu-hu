---
title: CLI-minták
description: Azure CLI-minták at a gyakori App Service-forgatókönyvek. Ismerje meg, hogyan automatizálhatja az App Service központi telepítési vagy felügyeleti feladatait.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 0b3acb1b421962cde7d90398f42bdfeefda578e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971502"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-minták az Azure App Service-hez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása és fájlok telepítése FTP-vel](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és FTP-n keresztül telepít ia fájlt. |
| [Alkalmazás létrehozása és kód üzembe helyezése a GitHubról](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és egy nyilvános GitHub-tárházból telepíti a kódot. |
| [Alkalmazás létrehozása folyamatos üzembe helyezéssel a GitHubról](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást folyamatos közzététellel egy saját GitHub-tárházból. |
| [Alkalmazás létrehozása és kód központi telepítése egy helyi Git-tárházból](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és konfigurálja a kódleküldéset egy helyi Git-tárházból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást egy központi telepítési réshellyel az átmeneti kód módosításához. |
| [ASP.NET Core alkalmazás létrehozása Docker-tárolóban](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást Linuxon, és betölt egy Docker-lemezképet a Docker Hubról. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány hozzárendelése alkalmazáshoz](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és hozzárendel egy egyéni tartománynevet. |
| [Egyéni SSL-tanúsítvány kötése alkalmazáshoz](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és hozzá köti az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális méretezése](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és két példányra méretezi azt. |
| [Alkalmazás méretezése világszerte magas rendelkezésre állású architektúrával](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Két App Service-alkalmazást hoz létre két különböző földrajzi régióban, és elérhetővé teszi őket egyetlen végponton keresztül az Azure Traffic Manager használatával. |
|**Az alkalmazás védelme**||
| [Integráció az Azure Application Gateway alkalmazással](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és integrálja azt az Application Gateway szolgáltatásvégpont és hozzáférési korlátozások használatával. |
|**Az alkalmazás összekötése az erőforrásokkal**||
| [Alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást és egy SQL-adatbázist, majd hozzáadja az adatbázis-kapcsolati karakterláncot az alkalmazásbeállításokhoz. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Létrehoz egy App Service-alkalmazást és egy tárfiókot, majd hozzáadja a tárolási kapcsolat karakterláncát az alkalmazás beállításaihoz. |
| [Alkalmazás csatlakoztatása a Redis Azure-gyorsítótárához](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást és egy Azure-gyorsítótárat a Redis számára, majd hozzáadja a redis-kapcsolat részleteit az alkalmazásbeállításokhoz.) |
| [Alkalmazás csatlakoztatása a Cosmos DB-hoz](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást és egy Cosmos DB-t, majd hozzáadja a Cosmos DB-kapcsolat részleteit az alkalmazásbeállításokhoz. |
|**Alkalmazás biztonsági és visszaállítási biztonsági őre**||
| [Alkalmazás biztonsági mentése](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és létrehoz egy egyszeri biztonsági mentést. |
| [Ütemezett biztonsági másolat létrehozása alkalmazáshoz](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és létrehoz egy ütemezett biztonsági másolatot. |
| [Alkalmazás visszaállítása biztonsági másolatból](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Egy App Service-alkalmazás visszaállítása biztonsági másolatból. |
|**Alkalmazás monitorozása**||
| [Alkalmazás figyelése webkiszolgálói naplókkal](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Létrehoz egy App Service-alkalmazást, engedélyezi a naplózást, és letölti a naplókat a helyi számítógépre. |
| | |