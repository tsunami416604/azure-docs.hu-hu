---
title: PowerShell-példák
description: Az Azure PowerShell-minták at a gyakori App Service-forgatókönyvek. Ismerje meg, hogyan automatizálhatja az App Service központi telepítési vagy felügyeleti feladatait.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f289bd453f2387282402394c807fe9700151f221
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532425"
---
# <a name="powershell-samples-for-azure-app-service"></a>PowerShell-minták az Azure App Service-hez

Az alábbi táblázat az Azure PowerShell használatával készített PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása a GitHubról történő telepítéssel](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, amely lekéri a kódot a GitHubról. |
| [Alkalmazás létrehozása folyamatos üzembe helyezéssel a GitHubról](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, amely folyamatosan telepíti a githubról a kódot. |
| [Alkalmazás létrehozása és kód telepítése FTP-vel](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és ftp-n keresztül fájlokat tölt fel egy helyi könyvtárból. |
| [Alkalmazás létrehozása és kód központi telepítése egy helyi Git-tárházból](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és konfigurálja a kódleküldéset egy helyi Git-tárházból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást egy központi telepítési réshellyel az átmeneti kód módosításához. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány hozzárendelése alkalmazáshoz](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és hozzárendel egy egyéni tartománynevet. |
| [Egyéni TLS-/SSL-tanúsítvány kötése alkalmazáshoz](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és hozzá köti az egyéni tartománynév TLS/SSL tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális méretezése](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és két példányra méretezi azt. |
| [Alkalmazás méretezése világszerte magas rendelkezésre állású architektúrával](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Két App Service-alkalmazást hoz létre két különböző földrajzi régióban, és elérhetővé teszi őket egyetlen végponton keresztül az Azure Traffic Manager használatával. |
|**Az alkalmazás összekötése az erőforrásokkal**||
| [Alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást és egy SQL-adatbázist, majd hozzáadja az adatbázis-kapcsolati karakterláncot az alkalmazásbeállításokhoz. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást és egy tárfiókot, majd hozzáadja a tárolási kapcsolat karakterláncát az alkalmazás beállításaihoz. |
|**Alkalmazás biztonsági és visszaállítási biztonsági őre**||
| [Alkalmazás biztonsági mentése](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és létrehoz egy egyszeri biztonsági mentést. |
| [Ütemezett biztonsági másolat létrehozása alkalmazáshoz](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és létrehoz egy ütemezett biztonsági másolatot. |
| [Alkalmazás biztonsági másolatának törlése](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy alkalmazás meglévő biztonsági másolatának törlése. |
| [Alkalmazás visszaállítása biztonsági másolatból](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy alkalmazás visszaállítása egy korábban befejezett biztonsági másolatból. |
| [Biztonsági másolat visszaállítása az előfizetések között](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy webalkalmazás visszaállítása egy másik előfizetés biztonsági mentéséből. |
|**Alkalmazás monitorozása**||
| [Alkalmazás figyelése webkiszolgálói naplókkal](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, engedélyezi a naplózást, és letölti a naplókat a helyi számítógépre. |
| | |
