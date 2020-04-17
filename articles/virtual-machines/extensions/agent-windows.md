---
title: Az Azure virtuálisgép-ügynök – áttekintés
description: Az Azure virtuálisgép-ügynök – áttekintés
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: f29a20ddeb93ec3d4aa98bbcb36f50456b543667
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452570"
---
# <a name="azure-virtual-machine-agent-overview"></a>Az Azure virtuálisgép-ügynök – áttekintés
A Microsoft Azure virtuálisgép-ügynök (VM-ügynök) egy biztonságos, könnyű folyamat, amely kezeli a virtuális gép (VM) interakció az Azure Fabric Controller. A virtuálisgép-ügynök elsődleges szerepkörrel rendelkezik az Azure virtuálisgép-bővítmények engedélyezésében és végrehajtásában. A virtuálisgép-bővítmények lehetővé teszik a virtuális gép üzembe helyezés utáni konfigurációját, például a szoftverek telepítését és konfigurálását. A virtuálisgép-bővítmények olyan helyreállítási funkciókat is engedélyeznek, mint például a virtuális gép felügyeleti jelszavának alaphelyzetbe állítása. Az Azure virtuálisgép-ügynök nélkül a virtuálisgép-bővítmények nem futtathatók.

Ez a cikk az Azure virtuálisgép-ügynök telepítését és észlelését részletezi.

## <a name="install-the-vm-agent"></a>A virtuálisgép-ügynök telepítése

### <a name="azure-marketplace-image"></a>Azure Piactér lemezképe

Az Azure Virtuálisgép-ügynök alapértelmezés szerint telepítve van az Azure Piactér-lemezképből telepített bármely Windows virtuális gépre. Amikor üzembe helyez egy Azure Marketplace-lemezképet a portálról, a PowerShellből, a parancssori felületről vagy egy Azure Resource Manager-sablonból, az Azure VM-ügynök is telepítve lesz.

A Windows vendégügynök-csomag két részre van osztva:

- Kiépítési ügynök (PA)
- Windows vendégügynök (WinGA)

A virtuális gép indításához a virtuális gépre telepítve kell lennie a pa-nak, de a WinGA-t nem kell telepíteni. Virtuális gép üzembe helyezésekor kiválaszthatja, hogy ne telepítse a WinGA.At VM deploy time, you can select not to install the WinGA. A következő példa bemutatja, hogyan választhatja ki a *provisionVmAgent* beállítást egy Azure Resource Manager-sablonnal:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Ha nincs telepítve az ügynökök, nem használhat bizonyos Azure-szolgáltatásokat, például az Azure Backup vagy az Azure Security. Ezeknek a szolgáltatásoknak egy bővítmény telepítéséhez van szükség. Ha a WinGA nélkül telepített virtuális gép, később telepítheti az ügynök legújabb verzióját.

### <a name="manual-installation"></a>Manuális telepítés
A Windows virtuálisgép-ügynök manuálisan telepíthető egy Windows telepítőcsomaggal. Manuális telepítés re lehet szükség, ha létrehoz egy egyéni virtuálisgép-lemezképet, amely telepítve van az Azure-ban. A Windows virtuálisgép-ügynök manuális telepítéséhez [töltse le a VM-ügynök telepítőt.](https://go.microsoft.com/fwlink/?LinkID=394789) A virtuálisgép-ügynök windows Server 2008 R2 és újabb rendszer által támogatott.

> [!NOTE]
> Fontos, hogy frissítse az AllowExtensionOperations beállítást, miután manuálisan telepítette a VMAgent-et egy olyan virtuális gépre, amely a ProvisionVMAgent engedélyezése nélkül lett telepítve.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Előfeltételek
- A Windows virtuálisgép-ügynök futtatásához legalább Windows Server 2008 R2 (64 bites) szükséges a . Lásd: [Virtuálisgép-ügynökök minimális verziótámogatása az Azure-ban](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Győződjön meg arról, hogy a virtuális gép hozzáfér a 168.63.129.16 IP-címhez. További információ: [Mi az IP-cím 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>A virtuálisgép-ügynök észlelése

### <a name="powershell"></a>PowerShell

Az Azure Resource Manager PowerShell modul azure-beli virtuális gépek adatainak lekéréséhez használható. A virtuális gépekkel kapcsolatos információk megtekintéséhez, például az Azure Virtuálisgép-ügynök létesítési állapotának megtekintéséhez használja a [Get-AzVM-et:](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)

```powershell
Get-AzVM
```

A következő tömörített példa kimenet az *OSProfile-ba*ágyazott *ProvisionVMAgent* tulajdonságot jeleníti meg. Ezzel a tulajdonsággal megállapítható, hogy a virtuálisgép-ügynök telepítve van-e a virtuális gépre:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

A következő parancsfájl segítségével a virtuális gép nevek tömör listáját és a virtuális gép ügynökállapotát adja vissza:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Kézi észlelés

Windows virtuális gépre bejelentkezve a Feladatkezelő használható a futó folyamatok vizsgálatára. Az Azure VM-ügynök ellenőrzéséhez nyissa meg a *Feladatkezelőt,* kattintson a Részletek fülre, és keresse meg a **WindowsAzureGuestAgent.exe**folyamatnevet. A folyamat jelenléte azt jelzi, hogy a virtuális gép ügynök telepítve van.


## <a name="upgrade-the-vm-agent"></a>A virtuális gépügynök frissítése
Az Azure VM Agent for Windows automatikusan frissül. Az új virtuális gépek üzembe helyezése az Azure-ban, a virtuális gép üzembe helyezése a virtuális gép üzembe helyezési idő. Egyéni virtuálisgép-rendszerképek manuálisan kell frissíteni, hogy tartalmazza az új virtuálisgép-ügynök a lemezkép létrehozása kor.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows vendégügynök automatikus naplók gyűjtése
A Windows vendégügynök rendelkezik egy szolgáltatással, amely automatikusan összegyűjti a naplókat. Ezt a szolgáltatást a CollectGuestLogs.exe folyamat vezérlője. A PaaS Cloud Services és az IaaS virtuális gépek számára is létezik, és célja, hogy gyorsan & automatikusan összegyűjtsön néhány diagnosztikai naplót egy virtuális gépről , így azok offline elemzéshez használhatók. Az összegyűjtött naplók eseménynaplók, operációs rendszer naplók, Azure-naplók és néhány beállításkulcsok. Ez létrehoz egy ZIP-fájlt, amely át a virtuális gép gazdagép. Ezt a ZIP-fájlt ezután a mérnöki csapatok és a támogatási szakemberek megvizsgálhatják a virtuális gép tulajdonában lévő ügyfél kérésére felmerülő problémák kivizsgálásához.

## <a name="next-steps"></a>További lépések
A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások áttekintése című témakörben olvashat bővebben.](overview.md)
