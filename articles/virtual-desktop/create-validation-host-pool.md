---
title: A Windows virtuális asztali gazdakészletszolgáltatásának frissítései – Azure
description: Érvényesítési gazdakészlet létrehozása a szolgáltatásfrissítések figyeléséhez az éles környezetben a frissítések létrehozása előtt.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365389"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Oktatóanyag: Gazdagépkészlet létrehozása a szolgáltatásfrissítések érvényesítéséhez

A gazdakészletek egy vagy több azonos virtuális gép gyűjteménye a Windows virtuális asztal bérlői környezetében. A gazdagépkészletek éles környezetben történő üzembe helyezése előtt javasoljuk, hogy hozzon létre egy érvényesítési gazdakészletet. A frissítések először az érvényesítési gazdakészletekre vonatkoznak, így figyelheti a szolgáltatásfrissítéseket, mielőtt azéles környezetbe való kihelyezésüket. Érvényesítési gazdakészlet nélkül előfordulhat, hogy nem észlelolyan módosításokat, amelyek hibákat okoznak, ami az éles környezetben lévő felhasználók számára állásidőt eredményezhet.

Annak érdekében, hogy az alkalmazások működjenek a legújabb frissítésekkel, az érvényesítési gazdakészletnek a lehető legközelebb kell lennie az éles környezetben lévő gazdakészletekhez. A felhasználóknak ugyanolyan gyakran kell csatlakozniuk az érvényesítési gazdakészlethez, mint az éles gazdagépkészlethez. Ha automatikus tesztelést végzett a gazdakészleten, akkor az érvényesítési gazdakészleten is automatikus tesztelést kell megadnia.

Az érvényesítési gazdakészletben a [diagnosztikai szolgáltatással](diagnostics-role-service.md) vagy a [Windows virtuális asztal hibaelhárítási cikkeivel](troubleshoot-set-up-overview.md)hibakeresést okozhat az érvényesítési állomáskészletben.

>[!NOTE]
> Azt javasoljuk, hogy hagyja az ellenőrzési állomáskészletet a helyén az összes jövőbeli frissítés teszteléséhez.

Mielőtt elkezdené, [töltse le és importálja a Windows Virtual Desktop PowerShell modult,](/powershell/windows-virtual-desktop/overview/)ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>A gazdagépkészlet létrehozása

Gazdakészletet az alábbi cikkekben található utasítások szerint hozhat létre:
- [Oktatóanyag: Gazdagépkészlet létrehozása az Azure Piactérrel](create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagépkészlet definiálása érvényesítési gazdakészletként

Futtassa a következő PowerShell-parancsmagokat az új gazdakészlet ellenőrzési gazdakészletként való definiálásához. Cserélje le az idézőjelekben lévő értékeket a munkamenetre vonatkozó értékekre:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Futtassa a következő PowerShell-parancsmast annak ellenőrzéséhez, hogy az érvényesítési tulajdonság be van-e állítva. Cserélje le az idézőjelekben lévő értékeket a munkamenetre vonatkozó értékekre.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

A parancsmag eredményeinek ehhez a kimenethez hasonlóan kell kinézniük:

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

## <a name="update-schedule"></a>Ütemezés frissítése

A szolgáltatásfrissítések havonta történnek. Ha jelentős problémák merülnek fel, a kritikus frissítések et gyakoribb ütemben biztosítjuk.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy érvényesítési gazdakészletet, megtudhatja, hogyan használhatja az Azure Service Health használatával a Windows virtuális asztal központi telepítésének figyelésére. 

> [!div class="nextstepaction"]
> [Szolgáltatásriasztások beállítása](./set-up-service-alerts.md)
