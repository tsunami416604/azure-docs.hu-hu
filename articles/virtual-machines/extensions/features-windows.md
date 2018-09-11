---
title: Az Azure Virtuálisgép-bővítmények és szolgáltatások a Windows |} A Microsoft Docs
description: Ismerje meg, hogy mely bővítmények érhetők el az Azure virtual machines, mit, adja meg vagy javíthatja a szerint csoportosítva.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4fd6175f68f98cce726e70e76b0b884a7a31fbda
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325328"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuálisgép-bővítmények és szolgáltatások Windows

Az Azure virtuális gép (VM) a bővítmények olyan kisebb alkalmazásoknál, amelyek az üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure virtuális gépeken. Ha például egy virtuális géphez szükséges szoftver telepítése, víruskereső védelmet, vagy belül, a parancsfájl futtatásához a VM-bővítmény segítségével. Az Azure Virtuálisgép-bővítmények az Azure CLI, PowerShell, az Azure Resource Manager-sablonokkal és az Azure Portalon is futtatható. Bővítmények egy új virtuális gép üzembe helyezésének mellékelhető, vagy minden meglévő rendszeren futtatható.

Ez a cikk a Virtuálisgép-bővítmények, az Azure Virtuálisgép-bővítmények, használatára vonatkozó Előfeltételek áttekintést nyújt, és ismeri fel, hogyan kezelheti, és távolítsa el a Virtuálisgép-bővítmények. Ez a cikk általános információkat nyújt, számos Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény-specifikus részletei megtalálhatók az egyes dokumentumok az egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használatieset-forgatókönyveit és a minták

Számos különböző Azure-beli Virtuálisgép-bővítmények érhetők el, amelyek mindegyike egy adott használati eset. Néhány példa:

- A DSC-bővítmény virtuális gépek a Windows PowerShell célállapot-konfiguráció vonatkozik. További információkért lásd: [Azure Desired State configuration bővítmény](dsc-overview.md).
- A Microsoft Monitoring Agent Virtuálisgép-bővítmény rendelkező virtuális gépek figyelés konfigurálása. További információkért lásd: [Azure virtuális gépek csatlakoztatása a Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Chef segítségével konfigurálhatja egy Azure virtuális Gépen. További információkért lásd: [automatizálása Azure virtuális gép üzembe helyezése a Cheffel](../windows/chef-automation.md).
- Figyelés a Datadoggal kiterjesztése az Azure infrastruktúra konfigurálása. További információkért lásd: a [Datadoggal blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Folyamat-specifikus kiterjesztéseket mellett egy egyéni szkriptek bővítménye Windows és a Linux rendszerű virtuális gépek számára érhető el. Az egyéni szkriptek bővítménye Windows esetében lehetővé teszi, hogy bármely virtuális gépen futtatandó PowerShell-parancsfájl. Egyéni parancsfájlok tervezése az Azure-telepítések konfigurációs túli milyen natív Azure-eszközkészlet biztosíthat igénylő hasznosak. További információkért lásd: [Windows virtuális gép egyéni szkriptek futtatására szolgáló bővítmény](custom-script-windows.md).

## <a name="prerequisites"></a>Előfeltételek

A bővítményt a virtuális gép kezelése érdekében az Azure Windows-ügynök telepítve van szükség. Néhány egyéni bővítményt kell Előfeltételek, például az erőforrások és a függőségek eléréséhez.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure-beli Virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric controller közötti interakciókat. A Virtuálisgép-ügynök üzembe helyezése és kezelése az Azure virtuális gépekhez, beleértve a futó Virtuálisgép-bővítmények számos funkcionális szempontból felelős. Az Azure-beli Virtuálisgép-ügynök az Azure Marketplace-rendszerképek előre telepítve van, és a egy támogatott operációs rendszeren manuálisan telepíthető. Az Azure VM Agent a Windows a Windows vendégügynöke néven ismert.

A támogatott operációs rendszerek és telepítésével kapcsolatos tudnivalókat lásd: [Azure-beli virtuálisgép-ügynök](agent-windows.md).

#### <a name="supported-agent-versions"></a>Az ügynök támogatott verziója

Annak érdekében, hogy a lehető legjobb élményt, vannak az ügynök minimális verziója. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Beállított támogatott operációs rendszerek

A Windows vendégügynöke futtat több OSE-kre, azonban a bővítmények keretrendszer van korlátozva a OSE-kre vonatkozó adott bővítmények. További információkért lásd: [Ez a cikk] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Néhány bővítmény nem támogatja az összes OSE-kre között, és bocsáthat *hiba kódja 51, a "Nem támogatott operációs rendszer"*. Támogatási lehetőségek az egyes bővítmény dokumentációjában.

#### <a name="network-access"></a>Hálózati hozzáférés

Bővítménycsomagok letöltődnek az Azure Storage-bővítmény adattárból, és az Azure Storage-bővítmény állapota feltöltések videóközpontba. Ha [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) az ügynökök verzióját, akkor nem kell engedélyezi a hozzáférést az Azure Storage a virtuális gép régióban, mivel az ügynök használhatják a kommunikáció átirányítása az Azure fabric controller, az ügynök-kommunikációhoz. Ha az ügynök nem támogatott verziója van, engedélyezi a kimenő hozzáférést az adott régióban az Azure Storage a virtuális gépről szeretne.

> [!IMPORTANT]
> Ha a hozzáférést a letiltott *168.63.129.16* a Vendég tűzfalat használ, akkor bővítmények sikertelen a fenti függetlenül.

Ügynökök csak bővítménycsomagok és állapotjelentést letöltéséhez használható. Például, ha a bővítmény telepítését le kell töltenie egy szkriptet a Githubról (egyéni szkript), vagy kell elérni az Azure Storage (Azure Backup), majd további tűzfal és a hálózati biztonsági csoport portokat kell megnyitni. A különböző bővítmények eltérő követelmények vonatkoznak, lehet, mert a saját jobb alkalmazások. Az Azure Storage-hozzáférést igénylő bővítmények, Azure NSG szolgáltatás címkék használatával hozzáférést biztosíthat [tárolási](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

A Windows Vendégügynöke nem rendelkezik a proxykiszolgálón keresztül ügynökkérelmek forgalom átirányítását támogatása.

## <a name="discover-vm-extensions"></a>Fedezze fel a Virtuálisgép-bővítmények

Számos különböző Virtuálisgép-bővítmények Azure virtuális gépekhez való használatra érhetők el. Teljes listájának megtekintéséhez használja [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). Az alábbi példa felsorolja az összes rendelkezésre álló bővítményeket a *WestUS* helye:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Azure Virtuálisgép-bővítmények futtassa a meglévő virtuális gépeken, amelyek akkor hasznos, ha a konfigurációs módosításokat, vagy helyreállítani a kapcsolatot egy már üzembe helyezett virtuális gépen kell. Virtuálisgép-bővítmények emellett az Azure Resource Manager-sablon-üzembehelyezések is telepíthet. Bővítmények használatával a Resource Manager-sablonok, Azure virtuális gépeken telepíthető és üzembe helyezés utáni beavatkozás nélkül konfigurálva.

Az alábbi módszerek bővítmény egy meglévő virtuális gép futtatásához használható.

### <a name="powershell"></a>PowerShell

Számos PowerShell-parancsok futtatása az egyes bővítmények léteznek. Listájának megtekintéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command) és szűrheti a *bővítmény*:

```powershell
Get-Command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Ez biztosítja, hogy a kimenet az alábbihoz hasonló:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVmssDiskEncryptionExtension             4.5.0      AzureRM.Compute
```

Az alábbi példa egy szkript letöltése a GitHub-adattárból a cél virtuális gépre, és futtassa a szkriptet az egyéni szkriptek futtatására szolgáló bővítmény használatával. Az egyéni szkriptek bővítménye további információkért lásd: [egyéni szkriptek bővítményének áttekintése](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

A következő példában a Virtuálisgép-hozzáférési bővítmény segítségével egy Windows virtuális gép a rendszergazdai jelszó visszaállítása egy ideiglenes jelszót. A Virtuálisgép-hozzáférési bővítmény további információkért lásd: [egy Windows virtuális gép alaphelyzetbe állítása a távoli asztal szolgáltatás](../windows/reset-rdp.md). Futtatása után ez, vissza kell állítania a jelszót az első bejelentkezéskor:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

A `Set-AzureRmVMExtension` parancs is használható, bármely Virtuálisgép-bővítmény indítása. További információkért lásd: a [Set-AzureRmVMExtension referencia](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension).


### <a name="azure-portal"></a>Azure Portal

A Virtuálisgép-bővítmények is alkalmazható egy meglévő virtuális Gépet az Azure Portalon keresztül. Válassza ki a virtuális Gépet a portálon, válassza a **bővítmények**, majd **Hozzáadás**. Válassza ki a bővítményt, az elérhető bővítmények listájából szeretne, majd kövesse a varázsló utasításait.

Az alábbi példa bemutatja a Microsoft Antimalware az Azure portal-bővítmény telepítése:

![A kártevőirtó bővítmény telepítése](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények hozzáadható az Azure Resource Manager-sablon és a központi telepítési sablon végrehajtani. Amikor telepít egy bővítmény-sablonnal, teljes konfigurációjú Azure-környezetek is létrehozhat. Például a következő JSON használatban van egy Resource Manager sablon elosztott terhelésű virtuális gépek és a egy Azure SQL database telepíti, majd telepíti a .NET Core-alkalmazást az egyes virtuális Gépeken. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

További információkért lásd: a [teljes Resource Manager-sablon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Authoring Azure Resource Manager-sablonok Windows Virtuálisgép-bővítményekkel](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>VM-bővítmény adatok védelme

Amikor futtat egy Virtuálisgép-bővítmény, lehet szükséges a például hitelesítő adatokat, a tárfiókok nevének és a tárfiók hozzáférési kulcsainak bizalmas adatokat. Számos Virtuálisgép-bővítmények közé tartozik egy védett konfigurációt, amely titkosítja az adatokat, és csak visszafejti a cél virtuális Gépen belül. Minden kiterjesztéssel rendelkezik egy adott védett konfigurációs sémáját, és az egyes részleteit a bővítmény-specifikus dokumentációját.

Az alábbi példa bemutatja egy példányát az egyéni szkriptek bővítménye Windows számára. A parancs végrehajtásához hitelesítő adatok készletét tartalmazza. Ebben a példában a parancs végrehajtásához nincs titkosítva:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Áthelyezés a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs védi a végrehajtási karakterlánc az alábbi példában látható módon:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan hajtsa végre az ügynökök és a bővítmény frissített?

Az ügynökök és a bővítmények ossza meg az azonos frissítési mechanizmust. Néhány frissítést nem szükséges további tűzfalszabályokat.

Ha egy frissítés érhető el, csak telepíti a rendszer a virtuális gépen, és megváltoznak a bővítmények és más virtuális gép adatmodell változásainak, mint például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tároló
- Vendég-operációsrendszer titkok
- Virtuális gép mérete
- Hálózati profil

A kiadók elérhetővé frissítések régiók eltérő időpontokban, így a különböző verziói különböző régiókban található virtuális gépek is használhat.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Egy virtuális gépre telepített bővítmények listázása

```powershell
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Ügynökfrissítések

Csak azokat a Windows Vendégügynöke *kiterjesztés kezelő kód*, a *Windows kiépítés kód* elkülönül. A Windows-Vendégügynök eltávolítása. Az automatikus frissítés a az ablak Vendégügynök nem tiltható le.

A *kiterjesztés kezelő kód* felelős az Azure-hálót kommunikáló, illetve a VM-bővítmények műveletek kezelésére például telepíti, állapotát, az egyes bővítmények frissítése és eltávolítása őket. Frissítések tartalmazzák a biztonsági javítások, a hibajavítások és a webhelyszolgáltatásokkal kapcsolatos fejlesztéseket tartalmaz az *kiterjesztés kezelő kód*.

Ellenőrizze, melyik verzióját használja, lásd: [e telepítve a Windows Vendégügynöke](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Bővítmény-frissítések

Bővítmény frissítés érhető el, ha a Windows Vendégügynöke tölti le, és frissíti a bővítményt. Frissítések automatikus bővítmény kiadásokhoz *kisebb* vagy *gyorsjavítás*. Engedélyezve van a hibajelentések, vagy tilthatják le az bővítmények *kisebb* frissíti, amikor üzembe helyezi a bővítményt. A következő példa bemutatja, hogyan automatikus frissítése a Resource Manager-sablonnal rendelkező alverziót *autoUpgradeMinorVersion ": true,"*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

A legújabb kiadás kisebb hibajavítások lekéréséhez azt javasoljuk, hogy mindig választja az automatikus frissítés a bővítmény központi telepítések. Gyorsjavítás-frissítések, biztonsági vagy kulcs hibajavításokat tartalmaz, amelyek nem kilépteti a programból.

### <a name="how-to-identify-extension-updates"></a>Bővítmény frissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Ha a bővítmény az autoUpgradeMinorVersion van beállítva a virtuális gép azonosítása

Ha a bővítmény lett üzembe helyezve, az "autoUpgradeMinorVersion" láthatja a virtuális gép modellből. Ellenőrzéséhez használja [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) , és adja meg az erőforráscsoportot és a virtuális gép neve a következő:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Az alábbi példa kimenetében látható, amely *autoUpgradeMinorVersion* értékre van állítva *igaz*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Amikor egy autoUpgradeMinorVersion történt azonosítása

Tekintse meg, mikor történt, a bővítmény frissítését, tekintse át az ügynököt a virtuális gép egyidejű bejelentkezik *C:\WindowsAzure\Logs\WaAppAgent.log*

A következő példában a virtuális Gépet kellett *Microsoft.Compute.CustomScriptExtension 1.8-as* telepítve. A gyorsjavítás volt elérhető verzióra *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Az ügynök engedélyek

A feladatok végrehajtásához az ügynököt futtató minden *helyi rendszer*.

## <a name="troubleshoot-vm-extensions"></a>A Virtuálisgép-bővítmények hibaelhárítása

Minden egyes Virtuálisgép-bővítmény lehet hibaelhárítási lépések adott, a bővítményt. Például az egyéni szkriptek futtatására szolgáló bővítmény használatakor szkriptvégrehajtás részletei találhatók helyileg a virtuális gép, ahol a bővítmény futtatták. Bővítmény-specifikus hibaelhárítási lépéseket részletesen ismertetett bővítmény-specifikus dokumentációját.

Az alábbi hibaelhárítási lépéseket az összes Virtuálisgép-bővítmények vonatkozik.

1. A Windows vendég Ügynöknaplóban ellenőrzéséhez tekintse meg a tevékenység során lett üzembe helyezve a bővítményt a *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Tekintse meg a tényleges bővítmény naplókat a további részletekért *C:\WindowsAzure\Logs\Plugins\<bővítménynév >*

3. Ellenőrizze a bővítmény megfelelő dokumentációban hibaelhárítási szakaszokat hibakódok ismert problémák stb.

4. Tekintse meg a rendszernaplót. Ellenőrizze, hogy előfordulhat, hogy rendelkezik zavarja a kiterjesztéssel, például egy másik alkalmazás kizárólagos package manager hozzáférés szükséges egy hosszú ideig futó telepített egyéb műveleteket.

### <a name="common-reasons-for-extension-failures"></a>Bővítmény hibák leggyakoribb okai

1. Bővítmények rendelkezik futtatásához 20 perc (kivétel a CustomScript bővítmény, a Chef és a DSC, amelyek 90 perc). Ha az üzemelő példány ez idő meghaladja, van megjelölve, időtúllépés. Ennek oka valószínűleg kevés az erőforrás-beli virtuális gépek más virtuális gép konfigurációk/indítási feladatok nagy mennyiségű erőforrást használ, miközben a bővítmény kiépítése próbál.

2. Minimális előfeltételek nem teljesülnek. Néhány bővítmény függőségekkel rendelkező VM termékváltozatokban, például a HPC-képeket. Bővítmények szükség lehet bizonyos hálózati hozzáférési követelmények, például az Azure Storage-vagy nyilvános szolgáltatásokhoz való kommunikációhoz. További példák lehet csomag adattárak, kevés a szabad lemezterület, vagy a biztonsági korlátozások a hozzáférést.

3. Kizárólagos package manager-hozzáféréssel. Bizonyos esetekben felmerülhet egy hosszú ideig futó Virtuálisgép-konfiguráció és a bővítmény telepítése ütköző, ahol mindkettő kell a package manager kizárólagos hozzáférést.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

VM-bővítmény virtuális gépek elleni futott, miután a [Get-AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) a bővítmény állapotát adja vissza. *[0] részállapotok* azt mutatja, hogy a bővítmény kiépítési sikeres volt, ami azt jelenti, hogy a virtuális gép központi telepítése sikeres azt, de a bővítményt a virtuális gép végrehajtása sikertelen volt, *részállapotok [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A kimenet hasonlít az alábbi példa kimenetében:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Bővítmény végrehajtási állapotát az Azure Portalon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális Gépet, válassza a **bővítmények**, majd válassza ki a kívánt bővítményt.

### <a name="rerun-vm-extensions"></a>Futtassa újra a Virtuálisgép-bővítmények

Előfordulhat, hogy esetek, amelyben a VM-bővítmény kell újra kell futtatni. Egy bővítmény akkor távolítsa el, majd újbóli a bővítmény-végrehajtási módszerrel tetszőleges futtathatja. Egy bővítmény eltávolításához használja [Remove-AzureRmVMExtension](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) módon:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Valamint eltávolíthatja kiterjesztése az Azure Portalon a következő:

1. Válasszon egy virtuális Gépet.
2. Válasszon **bővítmények**.
3. Válassza ki a kívánt bővítményt.
4. Válasszon **eltávolítása**.

## <a name="common-vm-extensions-reference"></a>Általános virtuális gép bővítmények referencia
| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| A Windows egyéni szkriptek futtatására szolgáló bővítmény |Szkriptek futtatása az Azure virtuális gépek ellen |[A Windows egyéni szkriptek futtatására szolgáló bővítmény](custom-script-windows.md) |
| Windows DSC-bővítmény |PowerShell DSC (Desired State Configuration) bővítmény |[Windows DSC-bővítmény](dsc-overview.md) |
| Azure Diagnostics bővítmény |Az Azure Diagnostics kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Az Azure Virtuálisgép-hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linuxos Virtuálisgép-hozzáférési bővítmény](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

Virtuálisgép-bővítményekkel kapcsolatos további információkért lásd: [Azure-beli virtuálisgép-bővítmények és szolgáltatások áttekintése](overview.md).
