---
title: RDP-tulajdonságok testreszabása a PowerShell használatával – Azure
description: A Windows rendszerű virtuális asztal RDP-tulajdonságainak testreszabása PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: helohr
ms.openlocfilehash: 624edaea9a0fb56e34eb83f033dfdab64985bd5c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950708"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Gazdagépek RDP protokoll tulajdonságainak testreszabása

A gazdagépek RDP protokoll (RDP) tulajdonságainak (például a többmonitoros élmény és a hangátirányítás) testreszabása lehetővé teszi, hogy a felhasználók igényei alapján optimális élményt nyújtson a felhasználóknak. Az RDP-tulajdonságokat a Windows virtuális asztalon a **set-RdsHostPool** parancsmag **-CustomRdpProperty** paraméterrel szabhatja testre.

A támogatott tulajdonságok teljes listáját és alapértelmezett értékeit a [Távoli asztal RDP-fájl beállításai](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) részben tekintheti meg.

Először [töltse le és importálja a](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Egyetlen egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Egyetlen egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Több egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Több egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa az alábbi PowerShell-parancsmagokat úgy, hogy az egyéni RDP-tulajdonságokat pontosvesszővel tagolt karakterláncként adja meg:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Az összes egyéni RDP-tulajdonság alaphelyzetbe állítása

Az egyéni RDP-tulajdonságokat alapértékre állíthatja úgy, hogy az [Egyéni RDP-tulajdonság hozzáadása vagy szerkesztése](#add-or-edit-a-single-custom-rdp-property)lehetőségre kattint, vagy a következő PowerShell-parancsmag futtatásával alaphelyzetbe állíthatja a gazdagépek összes egyéni RDP-tulajdonságát:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>További lépések

Most, hogy testreszabta az adott címkészlet RDP-tulajdonságait, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, hogy egy felhasználói munkamenet részeként tesztelje őket. Ehhez folytassa a Kapcsolódás a Windows rendszerű virtuális asztali környezetekhez:

- [Windows 10 és Windows 7 közötti kapcsolat](connect-windows-7-and-10.md)
- [Webböngészőből való csatlakozási szolgáltatás](connect-web.md)
