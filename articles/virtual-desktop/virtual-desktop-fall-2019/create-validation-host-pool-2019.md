---
title: Windows Virtual Desktop Host Pool Service-frissítések (Fall 2019) – Azure
description: Megtudhatja, hogyan hozhat létre egy érvényesítési gazdagépet a szolgáltatások frissítéseinek figyelésére, mielőtt az éles környezetbe helyezné a frissítéseket.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 806c3396d9188ea6abc5f779a26d99247d802ebe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527584"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-fall-2019-release"></a>Oktatóanyag: állomáslista létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez (2019-es kiadás)

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../create-validation-host-pool.md).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mielőtt üzembe helyezi a gazdagép-készleteket az éles környezetben, javasoljuk, hogy hozzon létre egy érvényesítési gazdagépet. A rendszer először alkalmazza a frissítéseket az érvényesítési gazdagépekre, így a szolgáltatás frissítéseinek figyelésével megfigyelheti őket az éles környezetbe. Érvényesítési címkészlet nélkül előfordulhat, hogy nem deríti fel azokat a módosításokat, amelyek hibákat vezetnek be, ami az éles környezetben lévő felhasználók számára állásidőt eredményezhet.

Annak biztosítása érdekében, hogy az alkalmazások a legújabb frissítésekkel működjenek, az ellenőrzési gazdagép-készletnek a lehető leghasonlónak kell lennie az éles környezetben lévő gazdagépekhez. A felhasználóknak az üzemi gazdagép-készlethez hasonlóan gyakran kell csatlakozniuk az ellenőrzési gazdagéphez. Ha automatizált tesztelést végez a gazdagép-készleten, az automatikus tesztelést is tartalmaznia kell az ellenőrzési gazdagépen.

Az érvényesítési gazdagépen lévő hibák hibakereséséhez használhatja a [diagnosztikai szolgáltatást](diagnostics-role-service-2019.md) vagy a [Windows virtuális asztali hibaelhárítási cikkeit](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Javasoljuk, hogy az összes jövőbeli frissítés teszteléséhez hagyja meg az ellenőrző gazdagép készletét.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/), ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>A gazda készlet létrehozása

A következő cikkek utasításait követve hozhat létre egy gazdagépet:
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace-2019.md)
- [Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell-2019.md)

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
> [Szolgáltatásriasztások beállítása](set-up-service-alerts-2019.md)
