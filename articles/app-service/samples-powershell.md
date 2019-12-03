---
title: PowerShell-minták
description: Azure PowerShell mintákat talál néhány gyakori App Service forgatókönyvhöz. Ismerje meg, hogyan automatizálhatja App Service telepítési vagy felügyeleti feladatait.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f32db2a4dda0ceb69644b64a5c76155b448c5a28
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687030"
---
# <a name="powershell-samples-for-azure-app-service"></a>PowerShell-minták a Azure App Servicehoz

Az alábbi táblázat a Azure PowerShell használatával létrehozott PowerShell-szkriptekre mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Alkalmazás létrehozása**||
| [Alkalmazás létrehozása a GitHubról történő üzembe helyezéssel](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást, amely kódot húz le a GitHubról. |
| [Hozzon létre egy alkalmazást a GitHubról történő folyamatos üzembe helyezéssel](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást, amely folyamatosan üzembe helyezi a kódot a GitHubról. |
| [Alkalmazás létrehozása és kód üzembe helyezése FTP-vel](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, és fájlokat tölt fel egy helyi könyvtárból FTP használatával. |
| [Alkalmazás létrehozása és kód üzembe helyezése helyi git-tárházból](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, és beállítja a kód leküldését egy helyi git-adattárból. |
| [Alkalmazás létrehozása és kód üzembe helyezése átmeneti környezetben](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást egy üzembe helyezési ponttal az átmeneti kód változásaihoz. |
|**Alkalmazás konfigurálása**||
| [Egyéni tartomány leképezése egy alkalmazásra](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást, és egyéni tartománynevet rendel hozzá. |
| [Egyéni SSL-tanúsítvány kötése egy alkalmazáshoz](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást, és egy egyéni tartománynév SSL-tanúsítványát köti hozzá. |
|**Alkalmazás méretezése**||
| [Alkalmazások manuális méretezése](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, és két példány között méretezi azt. |
| [Az alkalmazások globális skálázása magas rendelkezésre állású architektúrával](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Két App Service alkalmazást hoz létre két különböző földrajzi régióban, és egyetlen végponton keresztül elérhetővé teszi őket az Azure Traffic Manager használatával. |
|**Az alkalmazás összekötése az erőforrásokkal**||
| [Alkalmazás összekötése egy SQL Database](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást és egy SQL-adatbázist, majd hozzáadja az adatbázis-kapcsolódási karakterláncot az alkalmazás beállításaihoz. |
| [Alkalmazás összekötése egy Storage-fiókkal](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Létrehoz egy App Service alkalmazást és egy Storage-fiókot, majd hozzáadja a Storage-kapcsolódási karakterláncot az alkalmazás beállításaihoz. |
|**Alkalmazás biztonsági mentése és visszaállítása**||
| [Alkalmazás biztonsági mentése](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, és létrehoz egy egyszeri biztonsági mentést. |
| [Ütemezett biztonsági mentés létrehozása egy alkalmazáshoz](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, és létrehoz egy ütemezett biztonsági mentést. |
| [Alkalmazás biztonsági másolatának törlése](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy meglévő biztonsági másolat törlése az alkalmazáshoz. |
| [Alkalmazás visszaállítása biztonsági másolatból](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Egy korábban befejezett biztonsági másolatból állítja vissza az alkalmazást. |
| [Biztonsági másolat visszaállítása előfizetések között](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Visszaállítja egy webalkalmazást egy másik előfizetésben található biztonsági másolatból. |
|**Alkalmazás figyelése**||
| [Alkalmazás figyelése webkiszolgáló-naplókkal](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Létrehoz egy App Service alkalmazást, lehetővé teszi a naplózást, és letölti a naplókat a helyi gépre. |
| | |
