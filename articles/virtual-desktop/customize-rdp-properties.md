---
title: A PowerShell használatával – Azure RDP tulajdonságainak testreszabása
description: A PowerShell-parancsmagokkal RDP tulajdonságok a Windows virtuális asztal testreszabásának módját.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: ce14f990272fa1e70d07c0f4a1f18025b536eccc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618868"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Egy gazdagép készlet Remote Desktop Protocol tulajdonságainak testreszabása

Testreszabás, a gazdagép-készletben Remote Desktop Protocol (RDP) tulajdonságait, például több figyelő felhasználói élményt és hang átirányítása révén a felhasználók saját igényeik szerint az optimális működés érdekében. Testre szabhatja az RDP-tulajdonságok Windows virtuális asztalok használata a **- CustomRdpProperty** paramétert a **Set-RdsHostPool** parancsmagot.

Lásd: [távoli asztal RDP-fájl beállításainak](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) támogatott tulajdonságok és alapértelmezett értékeik teljes listáját.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Adja hozzá, vagy egyetlen egyéni RDP-tulajdonság módosítása

Adja hozzá, vagy egy egyéni RDP-tulajdonság módosítása, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![PowerShell-parancsmag képernyőképe a Get-RDSRemoteApp nevét és rövid név kiemelve.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Több egyéni RDP-tulajdonságok hozzáadása vagy szerkesztése

Tulajdonságok hozzáadása vagy szerkesztése több egyéni RDP, futtassa a következő PowerShell-parancsmagok pontosvesszővel tagolt karakterláncként RDP egyéni tulajdonságok megadásával:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![PowerShell-parancsmag képernyőképe a Get-RDSRemoteApp nevét és rövid név kiemelve.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Az összes egyéni RDP-tulajdonság alaphelyzetbe állítása

Visszaállíthatja az egyes egyéni RDP-tulajdonságok az alapértelmezett értékekre a következő témakör utasításait követve [hozzáadása vagy szerkesztése egyetlen egyéni RDP tulajdonság](#add-or-edit-a-single-custom-rdp-property), vagy visszaállíthatja egy gazdagép készlet összes egyéni RDP tulajdonságát a következő futtatásával PowerShell-parancsmagot:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![PowerShell-parancsmag képernyőképe a Get-RDSRemoteApp nevét és rövid név kiemelve.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>További lépések

Most, hogy testre szabta a egy adott gazdagép készlet RDP tulajdonságát, bejelentkezhet egy Windows virtuális asztali ügyfélhez, tesztelje le azokat a felhasználói munkamenet részeként. Ehhez továbbra is a csatlakozás a Windows virtuális asztal útmutatók:

- [Csatlakozás a Windows 10 és Windows 7](connect-windows-7-and-10.md)
- [Egy webböngészőből csatlakozzon](connect-web.md)
