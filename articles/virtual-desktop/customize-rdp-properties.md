---
title: RDP-tulajdonságok testreszabása a PowerShell használatával – Azure
description: A Windows virtuális asztal RDP-tulajdonságainak testreszabása PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128062"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Állomáskészlet Távoli asztali protokoll tulajdonságainak testreszabása

A gazdakészlet RDP-tulajdonságainak testreszabásával, például a többmonitoros környezetés a hangátirányítás segítségével az igényeiknek megfelelően optimális élményt biztosíthat a felhasználók számára. A **Set-RdsHostPool** parancsmag **-CustomRdpProperty** paraméterével testreszabhatja az RDP-tulajdonságokat a Windows virtuális asztalon.

A támogatott tulajdonságok és alapértelmezett értékeik teljes listáját a [támogatott RDP-fájlbeállításokban](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) találja.

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Alapértelmezett RDP-tulajdonságok

Alapértelmezés szerint a közzétett RDP-fájlok a következő tulajdonságokat tartalmazzák:

|RDP-tulajdonságok | Asztali számítógépek | Távoli alkalmazások |
|---|---| --- |
| Többmonitoros mód | Engedélyezve | N/A |
| A meghajtó átirányítása engedélyezve | Meghajtók, vágólap, nyomtatók, COM-portok, USB-eszközök és intelligens kártyák| Meghajtók, vágólap és nyomtatók |
| Távoli hangmód | Helyi lejátszás | Helyi lejátszás |

A gazdakészlethez megadott egyéni tulajdonságok felülírják ezeket az alapértelmezett értékeket.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Egyetlen egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Egyetlen egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa a következő PowerShell-parancsmackét:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![A Get-RDSRemoteApp Get-RDSRemoteApp parancsmag képernyőképe, amelynek neve és friendlyname jave van kiemelve.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Több egyéni RDP-tulajdonság hozzáadása vagy szerkesztése

Több egyéni RDP-tulajdonság hozzáadásához vagy szerkesztéséhez futtassa a következő PowerShell-parancsmagokat az egyéni RDP-tulajdonságok pontosvesszővel elválasztott karakterláncként történő megadásával:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![A Get-RDSRemoteApp Get-RDSRemoteApp parancsmag képernyőképe, amelynek neve és friendlyname jave van kiemelve.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Az összes egyéni RDP-tulajdonság alaphelyzetbe állítása

Az egyéni RDP-tulajdonságokat visszaállíthatja az alapértelmezett értékekre, ha követi az [Egyetlen egyéni RDP-tulajdonság hozzáadása vagy szerkesztése](#add-or-edit-a-single-custom-rdp-property)című útmutató utasításait, vagy a következő PowerShell-parancsmag futtatásával visszaállíthatja a gazdakészlet összes egyéni RDP-tulajdonságát:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![A Get-RDSRemoteApp Get-RDSRemoteApp parancsmag képernyőképe, amelynek neve és friendlyname jave van kiemelve.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>További lépések

Most, hogy testreszabta egy adott állomáskészlet RDP-tulajdonságait, bejelentkezhet egy Windows virtuális asztali ügyfélbe, hogy tesztelje őket egy felhasználói munkamenet részeként. A következő két útmutató megmutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél segítségével:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-and-10.md)
- [Kapcsolódás a webügyféllel](connect-web.md)
