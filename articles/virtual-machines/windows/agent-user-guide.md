---
title: Az Azure virtuális gépek ügynök áttekintése |} Microsoft Docs
description: Az Azure virtuális gép ügynök – áttekintés
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: a5da5cfb6cda940f68171e42d4512b304fc5f114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Az Azure virtuális gép ügynökének áttekintése

A Microsoft Azure virtuális gép ügynökének (Virtuálisgép-ügynök) rendkívül biztonságos, egyszerű folyamat, amely az Azure Fabric Controller interakcióba VM kezeli. A Virtuálisgép-ügynök engedélyezése és a végrehajtása az Azure virtuálisgép-bővítmények az elsődleges szerepkör tartozik. Virtuálisgép-bővítmények, amely lehetővé teszi a virtuális gépek, például a telepítése és beállítása a szoftver központi telepítési konfigurációs utáni. Virtuálisgép-bővítmények is engedélyezheti a helyreállítási szolgáltatás például a virtuális gép rendszergazdai jelszó alaphelyzetbe állításával. Az Azure Virtuálisgép-ügynök nélküli virtuálisgép-bővítmények nem futtatható.

Ez a dokumentum telepítési, észlelését és az Azure virtuális gép ügynök eltávolításának részletes.

## <a name="install-the-vm-agent"></a>A Virtuálisgép-ügynök telepítése

### <a name="azure-gallery-image"></a>Azure katalógusában kép

Az Azure Virtuálisgép-ügynök telepítve van a Windows virtuális gépek Azure-katalógus lemezképből telepített alapértelmezés szerint. A portálon, a PowerShell, a parancssori felületen vagy a Azure Resource Manager-sablonok az Azure katalógusában lemezképének központi telepítésekor az Azure Virtuálisgép-ügynök is telepítve van. 

### <a name="manual-installation"></a>Manuális telepítés

A Windows Virtuálisgép-ügynök manuálisan telepíthető a Windows installer-csomag. Manuális telepítés akkor lehet szükség, ha egy egyéni virtuálisgép-lemezkép létrehozása, amely telepíti az Azure-ban. A Windows Virtuálisgép-ügynök manuális telepítéséhez töltse le az ügynököt telepítő a következő helyről [Windows Azure VM ügynök letöltése](http://go.microsoft.com/fwlink/?LinkID=394789). 

A Virtuálisgép-ügynök a windows installer fájlra duplán kattintva is telepíthető. A Virtuálisgép-ügynök automatikus vagy felügyelet nélküli telepítésének futtassa a következő parancsot.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>A Virtuálisgép-ügynök észlelése

### <a name="powershell"></a>PowerShell

Az Azure Resource Manager PowerShell-modul segítségével Azure virtuális gépek kapcsolatos információkat lekérni. Futó `Get-AzureRmVM` meglehetősen bit, többek között a telepítés állapotát az Azure virtuális gép ügynök adja vissza.

```PowerShell
Get-AzureRmVM
```

A következő része a csak a `Get-AzureRmVM` kimeneti. Figyelje meg a `ProvisionVMAgent` tulajdonság ágyazott `OSProfile`, ez a tulajdonság segítségével határozza meg, ha a Virtuálisgép-ügynök telepítve van a virtuális géphez.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

A következő parancsfájl segítségével a virtuális gépek neveit és a Virtuálisgép-ügynök állapota tömör listáját adja vissza.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuális észlelés

Ha jelentkezett be a Windows Azure virtuális Gépen, a Feladatkezelő segítségével vizsgálja meg a futó folyamatok. Az Azure virtuális gép ügynököt ellenőrzéséhez nyissa meg a Feladatkezelőt > kattintson a részleteket tartalmazó lapot, és keresse meg a folyamat nevét `WindowsAzureGuestAgent.exe`. A folyamat jelzi, hogy a Virtuálisgép-ügynök telepítve van-e.

## <a name="upgrade-the-vm-agent"></a>A Virtuálisgép-ügynök frissítése

Az Azure virtuális gép ügynök a Windows a rendszer automatikusan frissíti. Új virtuális gépek vannak telepítve, az Azure-ba, mert a legújabb ügynököt kapnak. Egyéni Virtuálisgép-rendszerképek manuálisan frissíteni kell az új Virtuálisgép-ügynök tartalmazza.
