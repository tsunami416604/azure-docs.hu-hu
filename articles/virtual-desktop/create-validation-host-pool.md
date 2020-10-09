---
title: Windows rendszerű virtuális asztali munkamenetgazda szolgáltatás frissítései – Azure
description: Ellenőrzési címkészlet létrehozása a szolgáltatások frissítéseinek figyeléséhez, mielőtt az éles környezetbe helyezné a frissítéseket.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8eb8378480b8784939e3ab63327e73e3705c74a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90526510"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Oktatóanyag: gazdagép-készlet létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei, amelyek a Windows rendszerű virtuális asztali környezetben találhatók. Javasoljuk, hogy hozzon létre egy érvényesítési gazdagépet, amelyben először a szolgáltatás frissítéseit alkalmazza a rendszer. Ez lehetővé teszi a szolgáltatások frissítéseinek figyelését, mielőtt a szolgáltatás alkalmazza azokat a standard vagy nem érvényesítési környezetbe. Érvényesítési címkészlet nélkül előfordulhat, hogy nem deríti fel azokat a módosításokat, amelyek hibákat vezetnek be, ami leállást eredményezhet a normál környezetben lévő felhasználók számára.

Annak biztosítása érdekében, hogy az alkalmazások a legújabb frissítésekkel működjenek, az érvényesítési gazdagépnek ugyanúgy kell lennie, mint amennyire csak lehetséges, a nem érvényesítési környezetben lévő gazdagépekhez hasonlóan. A felhasználóknak a szokásos gazdagép-készlethez hasonlóan gyakran kell csatlakozniuk az ellenőrzési gazdagéphez. Ha automatizált tesztelést végez a gazdagép-készleten, az automatikus tesztelést is tartalmaznia kell az ellenőrzési gazdagépen.

Az érvényesítési gazdagépen lévő hibák hibakereséséhez használhatja a [diagnosztikai szolgáltatást](diagnostics-role-service.md) vagy a [Windows virtuális asztali hibaelhárítási cikkeit](troubleshoot-set-up-overview.md).

>[!NOTE]
> Javasoljuk, hogy az összes jövőbeli frissítés teszteléséhez hagyja meg az ellenőrző gazdagép készletét.

>[!IMPORTANT]
>Az Azure Resource Management-integrációval rendelkező Windows rendszerű virtuális asztal jelenleg nem tesz lehetővé az ellenőrzési környezetek engedélyezését és letiltását. A probléma megoldásához frissíteni fogjuk a cikket.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, kövesse a [Windows rendszerű virtuális asztali PowerShell-modul beállítása](powershell-module.md) című témakör útmutatását a PowerShell-modul beállításához és az Azure-ba való bejelentkezéshez.

## <a name="create-your-host-pool"></a>A gazda készlet létrehozása

A következő cikkek utasításait követve hozhat létre egy gazdagépet:
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagép-készlet definiálása érvényesítési gazdagépként

Futtassa az alábbi PowerShell-parancsmagokat az új címkészlet érvényesítési gazdagépként való definiálásához. Cserélje le a zárójelben lévő értékeket a munkamenetéhez tartozó értékekre:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

A következő PowerShell-parancsmag futtatásával ellenőrizze, hogy az érvényesítési tulajdonság be van-e állítva. Cserélje le a zárójelben lévő értékeket a munkamenetéhez tartozó értékekre.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

A parancsmag eredményeinek ehhez a kimenethez hasonlóan kell kinéznie:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Frissítési ütemterv

A szolgáltatás frissítései havonta történnek. Jelentős problémák esetén a kritikus frissítések gyakoribb ütemben lesznek elérhetők.

Ha vannak szolgáltatási frissítések, győződjön meg arról, hogy legalább egy kis csoporttal rendelkezik, amely minden nap bejelentkezik a környezet ellenőrzéséhez. Javasoljuk, hogy rendszeresen látogasson el a [TechCommunity-webhelyre](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) , és kövesse az WVDUPdate-mel kapcsolatos bejegyzéseket, hogy értesüljön a szolgáltatás frissítéseiről.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy érvényesítési gazdagépet, megtudhatja, hogyan használhatja a Azure Service Health a Windows rendszerű virtuális asztali környezet figyelésére.

> [!div class="nextstepaction"]
> [Szolgáltatásriasztások beállítása](./set-up-service-alerts.md)
