---
title: Szolgáltatásfrissítések – Azure ellenőrzése Windows virtuális asztal előzetes gazdagép készlet létrehozása
description: Szolgáltatás figyelése érvényesítési gazdagép készlet létrehozása frissíti, mielőtt megvalósítaná a frissítések az éles környezetbe.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b654c177accb92d5b9a861f14874e795f4525a43
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761211"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Oktatóanyag: Szolgáltatásfrissítések ellenőrzése gazdagép-készlet létrehozása

Gazdagép-készletekre egy gyűjtemény egy vagy több egyforma virtuális gépek virtuális Windows Desktop előzetes verziójához bérlői környezetekben. Gazdagép-készleteket az éles környezetben üzembe helyezése előtt erősen ajánlott érvényesítési gazdagép készletet hoz létre. Frissítés alkalmazza a rendszer érvényesítési gazdagép tárolókészletekben, figyelheti a szolgáltatásfrissítések, mielőtt megvalósítaná azokat az éles környezetben, ami lehetővé teszi. Érvényesítési gazdagép készletek nélkül nem Észreveheti a módosításokat, amelyek bemutatják a hibákat, ami állásidőt jelent a felhasználók számára az éles környezetben.

Ahhoz, hogy az alkalmazásai működését a legújabb frissítésekkel, az érvényesítési gazdagép készletet a gazdagép készletek a lehető az éles környezetben kell lennie. Felhasználók gyakorisággal csatlakoznia az érvényesítési gazdagép-készlet, mint a termelési gazdagép-készlethez. Ha automatizálta a gazdagép készleten tesztelése, tartalmaznia kell az érvényesítési gazdagép készletében automatizált tesztelés.

Ön is a érvényesítési gazdagép készlet a diagnostics szolgáltatással kapcsolatos problémák megoldásában, vagy a [Windows virtuális asztal hibaelhárítási cikkek](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Azt javasoljuk, hogy hagyja-e az érvényesítési gazdagép készlet összes jövőbeli frissítés tesztelése helyben.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), ha még nem tette.

## <a name="create-your-host-pool"></a>A gazdagép-készlet létrehozása

Az alábbi cikkek valamelyikére utasításait követve egy gazdagép készletet hozhat létre:
- [Oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace-en](create-host-pools-azure-marketplace.md)
- [Gazdagép-készlet létrehozása egy Azure Resource Manager-sablonnal](create-host-pools-arm-template.md)
- [Gazdagép-készlet létrehozása a PowerShell-lel](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>A gazdagép-készletet meghatározni, egy érvényesítési gazdagép-készlet

Futtassa a következő PowerShell-parancsmagok az új gazdagép-készletet meghatározni, egy érvényesítési gazdagép készletet. Cserélje le a munkamenetre vonatkozó értékek szerint idézőjelek között szereplő értékeket:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Futtassa az alábbi PowerShell-parancsmagot annak ellenőrzéséhez, hogy az érvényesítés tulajdonság van beállítva. Cserélje le a munkamenetre vonatkozó értékek szerint idézőjelek között szereplő értékeket.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

A parancsmag eredményeinek Ez a kimenet hasonlóan kell kinéznie:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Frissítés ütemezése

Előzetes verzióban elérhető a szolgáltatásfrissítések fordulhat elő, körülbelül egy havi kiadása ütemben történik. Jelentős probléma merül fel, kritikus frissítések biztosítjuk gyakoribb kiadása ütemben történik.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy érvényesítési gazdagép-készletet, az érvényesítési gazdagép készlet távoli alkalmazások igény szerint fel lehet tölteni. Virtuális asztali Windows-alkalmazások kezeléséről további információkért tekintse meg a kezelés alkalmazás csoportok oktatóanyag.

> [!div class="nextstepaction"]
> [Útmutató alkalmazások csoportok kezelése](./manage-app-groups.md)
