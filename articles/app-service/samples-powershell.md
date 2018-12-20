---
title: Az Azure PowerShell-minták – az App Service |} A Microsoft Docs
description: Az Azure PowerShell-minták – App Service-ben
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 69fd27785e5fc16a79fc23728b6d1e50a0a7b834
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632857"
---
# <a name="powershell-samples-for-azure-app-service"></a>PowerShell-minták az Azure App Service-ben

Az alábbi táblázat az Azure PowerShell használatával létrehozott PowerShell-parancsfájlok mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Hozzon létre egy alkalmazást a githubról történő üzembe helyezéssel](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, amely a githubról kér le kódokat. |
| [Hozzon létre egy alkalmazást a Githubról történő folyamatos üzembe helyezéssel](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazás, amely folyamatosan üzembe helyezi a kódját a Githubról. |
| [Hozzon létre egy alkalmazást, és az FTP-kód üzembe helyezése](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazás- és feltöltése a fájlok egy helyi könyvtárból FTP használatával. |
| [Alkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és konfigurálja a kód leküldéses egy helyi Git-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service-alkalmazások kódmódosítás átmeneti üzembehelyezési pontot hoz létre. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány hozzárendelése egy alkalmazáshoz](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és egy egyéni tartománynevet képez. |
| [Egyéni SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service-alkalmazást, és kötést hoz létre az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális skálázása](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és méretezi a 2-példányok között. |
| [Átviteli alkalmazás világszerte, magas rendelkezésre állású architektúrával](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz két App Service-alkalmazások két különböző földrajzi régióban, és elérhetővé teszi azokat az Azure Traffic Managerrel egyetlen végponton keresztül. |
|**Alkalmazás csatlakoztatása erőforrásokhoz**||
| [Alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service-alkalmazások és a egy SQL-adatbázist hoz létre, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazásbeállításokat. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service-alkalmazások és a egy storage-fiókot hoz létre, majd a tárfiók kapcsolati karakterláncának ad hozzá az alkalmazásbeállításokat. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [Alkalmazás biztonsági mentése](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és egyszeri biztonsági mentést hoz. |
| [Egy ütemezett biztonsági másolat egy alkalmazás létrehozása](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, és a ütemezett biztonsági mentés hozza létre. |
| [Egy biztonsági másolat egy alkalmazás törlése](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Törli az alkalmazás meglévő biztonsági mentésből. |
| [Alkalmazás visszaállítása biztonsági másolatból](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Alkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |
| [Visszaállítás biztonsági másolatból, előfizetések között](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Visszaállít egy webalkalmazást egy biztonsági másolatból egy másik előfizetésben. |
|**Alkalmazás monitorozása**||
| [A figyelő egy alkalmazást a webkiszolgáló-naplókkal](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service-alkalmazást, engedélyezi a naplózást, és letölti a naplóit a helyi gépen. |
| | |
