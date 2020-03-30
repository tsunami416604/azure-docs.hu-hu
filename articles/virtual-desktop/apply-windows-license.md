---
title: Windows-licenc alkalmazása munkamenetgazda virtuális gépekre – Azure
description: Ez a témakör a Windows virtuális asztali virtuális gépek Windows-licencének alkalmazását ismerteti.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254234"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows-licenc alkalmazása munkamenetgazda virtuális gépekre

Azok az ügyfelek, akik megfelelő licenccel rendelkeznek a Windows virtuális asztal számítási feladatának futtatásához, jogosultak Windows-licencet alkalmazni a munkamenetgazda virtuális gépeikre, és egy másik licenc megfizetése nélkül futtatni őket. További információt a [Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/)című témakörben talál.

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>A Windows virtuális asztali licenc használatának módjai
A Windows virtuális asztali licencelése lehetővé teszi, hogy licencet alkalmazzon minden olyan Windows vagy Windows Server virtuális gépre, amely munkamenetgazdaként van regisztrálva egy gazdakészletben, és felhasználói kapcsolatokat fogad. Ez a licenc nem vonatkozik a fájlmegosztási kiszolgálóként, tartományvezérlőként és így tovább futó virtuális gépekre.

A Windows virtual desktop licenc használatának több módja is van:
- Az [Azure Marketplace-ajánlattal](./create-host-pools-azure-marketplace.md)létrehozhat egy gazdakészletet és annak munkamenetgazda virtuális gépeit. Az így létrehozott virtuális gépek automatikusan alkalmazzák a licencet.
- A GitHub Azure Resource Manager sablon használatával létrehozhat egy gazdakészletet és annak munkamenetgazda virtuális [gépeit.](./create-host-pools-arm-template.md) Az így létrehozott virtuális gépek automatikusan alkalmazzák a licencet.
- Licenc et alkalmazhat egy meglévő munkamenetgazda virtuális gépre. Ehhez először kövesse a [PowerShell-készlet használatával rendelkező gazdakészlet létrehozása](./create-host-pools-powershell.md) című útmutató utasításait egy gazdakészlet és a társított virtuális gépek létrehozásához, majd térjen vissza ehhez a cikkhez, és ismerje meg, hogyan alkalmazhatja a licencet.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Windows-licenc alkalmazása munkamenetgazda virtuális gépre
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShellt.](/powershell/azure/overview) A Windows-licenc alkalmazásához futtassa a következő PowerShell-parancsmast:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Ellenőrizze, hogy a munkamenetgazda virtuális gépe használja-e a licencelési előnyt
A virtuális gép üzembe helyezése után futtassa ezt a parancsmasna ellenőrizze a licenc típusát:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Az alkalmazott Windows-licenccel rendelkező munkamenetgazda virtuális gép a következőhez hasonlót jelenít meg:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Az alkalmazott Windows-licenc nélküli virtuális gépek a következőhez hasonlót mutatnak:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Futtassa a következő parancsmast az Azure-előfizetésben alkalmazott Windows-licenccel rendelkező munkamenetgazda-virtuális gépek listájának megtekintéséhez:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
