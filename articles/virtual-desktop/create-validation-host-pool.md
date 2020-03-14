---
title: Windows rendszerű virtuális asztali munkamenetgazda szolgáltatás frissítései – Azure
description: Ellenőrzési címkészlet létrehozása a szolgáltatások frissítéseinek figyeléséhez, mielőtt az éles környezetbe helyezné a frissítéseket.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365389"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Oktatóanyag: gazdagép-készlet létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mielőtt üzembe helyezi a gazdagép-készleteket az éles környezetben, javasoljuk, hogy hozzon létre egy érvényesítési gazdagépet. A rendszer először alkalmazza a frissítéseket az érvényesítési gazdagépekre, így a szolgáltatás frissítéseinek figyelésével megfigyelheti őket az éles környezetbe. Érvényesítési címkészlet nélkül előfordulhat, hogy nem deríti fel azokat a módosításokat, amelyek hibákat vezetnek be, ami az éles környezetben lévő felhasználók számára állásidőt eredményezhet.

Annak biztosítása érdekében, hogy az alkalmazások a legújabb frissítésekkel működjenek, az ellenőrzési gazdagép-készletnek a lehető leghasonlónak kell lennie az éles környezetben lévő gazdagépekhez. A felhasználóknak az üzemi gazdagép-készlethez hasonlóan gyakran kell csatlakozniuk az ellenőrzési gazdagéphez. Ha automatizált tesztelést végez a gazdagép-készleten, az automatikus tesztelést is tartalmaznia kell az ellenőrzési gazdagépen.

Az érvényesítési gazdagépen lévő hibák hibakereséséhez használhatja a [diagnosztikai szolgáltatást](diagnostics-role-service.md) vagy a [Windows virtuális asztali hibaelhárítási cikkeit](troubleshoot-set-up-overview.md).

>[!NOTE]
> Javasoljuk, hogy az összes jövőbeli frissítés teszteléséhez hagyja meg az ellenőrző gazdagép készletét.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/), ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>A gazda készlet létrehozása

A következő cikkek utasításait követve hozhat létre egy gazdagépet:
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace.md)
- [Gazdagép létrehozása Azure Resource Manager sablonnal](create-host-pools-arm-template.md)
- [Gazdagép létrehozása a PowerShell-lel](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagép-készlet definiálása érvényesítési gazdagépként

Futtassa az alábbi PowerShell-parancsmagokat az új címkészlet érvényesítési gazdagépként való definiálásához. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

A következő PowerShell-parancsmag futtatásával ellenőrizze, hogy az érvényesítési tulajdonság be van-e állítva. Cserélje le az idézőjelek értékeit a munkamenetéhez tartozó értékekre.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

A parancsmag eredményeinek ehhez a kimenethez hasonlóan kell kinéznie:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Frissítési ütemterv

A szolgáltatás frissítései havonta történnek. Jelentős problémák esetén a kritikus frissítések gyakoribb ütemben lesznek elérhetők.

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy érvényesítési gazdagépet, megtudhatja, hogyan használhatja a Azure Service Health a Windows rendszerű virtuális asztali környezet figyelésére. 

> [!div class="nextstepaction"]
> [Szolgáltatási riasztások beállítása](./set-up-service-alerts.md)
