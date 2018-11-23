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
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290739"
---
# <a name="azure-powershell-samples"></a>Azure PowerShell-minták

Az alábbi táblázat az Azure PowerShell használatával létrehozott PowerShell-parancsfájlok mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Hozzon létre egy alkalmazást a githubról történő üzembe helyezéssel](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure-webalkalmazást, amely a githubról kér le kódokat. |
| [Hozzon létre egy alkalmazást a Githubról történő folyamatos üzembe helyezéssel](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy Azure-webalkalmazást, amely folyamatosan üzembe helyezi a kódját a Githubról. |
| [Hozzon létre egy alkalmazást, és az FTP-kód üzembe helyezése](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy Azure-alapú webes alkalmazás-és feltöltése FTP használatával egy helyi könyvtárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure-webalkalmazás létrehozása és kód leküldéses egy helyi Git-adattárból konfigurálja. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Azure-webalkalmazás kódmódosítás átmeneti üzembehelyezési pontot hoz létre. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány hozzárendelése egy alkalmazáshoz](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és egy egyéni tartománynevet képez. |
| [Egyéni SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Egy Azure-webalkalmazást hoz létre, és kötést hoz létre az egyéni tartománynév SSL-tanúsítványát. |
|**Az alkalmazás méretezése**||
| [Alkalmazás manuális skálázása](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és méretezi a 2-példányok között. |
| [Átviteli alkalmazás világszerte, magas rendelkezésre állású architektúrával](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Két Azure-webalkalmazások létrehoz két különböző földrajzi régióban, és elérhetővé teszi azokat az Azure Traffic Managerrel egyetlen végponton keresztül. |
|**Alkalmazás csatlakoztatása erőforrásokhoz**||
| [Alkalmazás csatlakoztatása SQL-adatbázishoz](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure-webalkalmazás és a egy SQL-adatbázist hoz létre, majd az adatbázis-kapcsolati karakterláncot ad hozzá az alkalmazásbeállításokat. |
| [Alkalmazás csatlakoztatása tárfiókhoz](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure-webalkalmazás és a egy storage-fiókot hoz létre, majd a tárfiók kapcsolati karakterláncának ad hozzá az alkalmazásbeállításokat. |
|**Biztonsági mentése és visszaállítása alkalmazás**||
| [Alkalmazás biztonsági mentése](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és egyszeri biztonsági mentést hoz. |
| [Egy ütemezett biztonsági másolat egy alkalmazás létrehozása](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, és a ütemezett biztonsági mentés hozza létre. |
| [Egy biztonsági másolat egy alkalmazás törlése](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Törli az alkalmazás meglévő biztonsági mentésből. |
| [Alkalmazás visszaállítása biztonsági másolatból](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Alkalmazás visszaállítása egy korábban elvégzett biztonsági másolatból. |
| [Visszaállítás biztonsági másolatból, előfizetések között](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Visszaállít egy webalkalmazást egy biztonsági másolatból egy másik előfizetésben. |
|**Alkalmazás monitorozása**||
| [A figyelő egy alkalmazást a webkiszolgáló-naplókkal](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy Azure-webalkalmazást hoz létre, engedélyezi a naplózást, és letölti a naplóit a helyi gépen. |
| | |
