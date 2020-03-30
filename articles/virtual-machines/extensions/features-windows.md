---
title: Az Azure VM-bővítmények és -szolgáltatások a Windows hoz
description: Ismerje meg, hogy milyen bővítmények érhetők el az Azure virtuális gépekhez, az általuk biztosított vagy javított eszközök szerint csoportosítva.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066855"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuálisgép-bővítmények és -szolgáltatások a Windows rendszerhez

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Ha egy virtuális gépen például szoftver telepítésére, vírusvédelemre vagy egy szkript futtatására van szükség, erre felhasználható egy virtuálisgép-bővítmény. Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával futtathatók. A bővítmények egy kötegben hajthatók végre az új virtuális gépek üzembe helyezésével, vagy meglévő rendszereken is futtathatók.

Ez a cikk áttekintést nyújt a virtuális gép-bővítményekről, az Azure Virtuálisgép-bővítmények használatának előfeltételeiről, valamint a virtuálisgép-bővítmények észlelésével, kezelésével és eltávolításával kapcsolatos útmutatást. Ez a cikk általános információkat nyújt, mert sok virtuálisgép-bővítmény érhető el, mindegyik potenciálisan egyedi konfigurációval rendelkezik. A kiterjesztés-specifikus részletek az egyes bővítményekre jellemző dokumentumokban találhatók.

 

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure virtuálisgép-bővítmények érhetők el, mindegyik egy adott használati eset. Néhány példa:

- PowerShell kívánt állapotkonfigurációk alkalmazása a Windows DSC-bővítményével rendelkező virtuális gépre. További információ: [Azure Desired State configuration extension.](dsc-overview.md)
- Konfigurálja a virtuális gép figyelését a Log Analytics-ügynök virtuálisgép-bővítményével. További információ: [Azure-beli virtuális gépek csatlakoztatása az Azure Monitor naplóihoz.](../../log-analytics/log-analytics-azure-vm-extension.md)
- Konfiguráljon egy Azure-gép a Chef használatával. További információ: [Az Azure VM üzembe helyezésének automatizálása a Chef segítségével](../../chef/chef-automation.md)című témakörben talál.
- Konfigurálja az Azure-infrastruktúra figyelését a Datadog kiterjesztéssel. További információ: [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


A folyamatspecifikus bővítmények mellett egy egyéni parancsfájl-bővítmény is elérhető Windows és Linux virtuális gépeken. A Windows egyéni parancsfájl-bővítménye lehetővé teszi, hogy bármely PowerShell-parancsfájl futjon a virtuális számítógépen. Az egyéni parancsfájlok hasznosak az Azure-telepítések tervezéséhez, amelyek a natív Azure-eszközök által biztosított konfiguráción túl igényelnek. További információt a [Windows VM egyéni parancsfájl-bővítménye című témakörben](custom-script-windows.md)talál.

## <a name="prerequisites"></a>Előfeltételek

A bővítmény kezeléséhez a virtuális gép, telepítenie kell az Azure Windows-ügynök. Egyes egyes bővítmények előfeltételei, például az erőforrásokhoz vagy a függőségekhez való hozzáférés.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric vezérlő közötti interakciókat. A virtuálisgép-ügynök az Azure virtuális gépek üzembe helyezésének és kezelésének számos funkcionális szempontjáért felelős, beleértve a virtuálisgép-bővítmények futtatását is. Az Azure Virtuálisgép-ügynök előre telepítve van az Azure Marketplace-rendszerképeken, és manuálisan telepíthető a támogatott operációs rendszereken. Az Azure VM Agent for Windows a Windows vendégügynök.

A támogatott operációs rendszerekről és a telepítési utasításokról az [Azure virtuálisgép-ügynök](agent-windows.md)című témakörben talál tájékoztatást.

#### <a name="supported-agent-versions"></a>Támogatott ügynökverziók

Annak érdekében, hogy a lehető legjobb élményt, vannak minimális verziói az ügynök. További információt [ebben a cikkben](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)talál.

#### <a name="supported-oses"></a>Támogatott oses

A Windows vendégügynök több operációs rendszeren fut, azonban a bővítmények keretrendszer rendelkezik egy korlátot a bővítmények, hogy a kiterjesztések. További információt [ebben a cikkben](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
)talál.

Egyes bővítmények nem támogatottak az összes operációs rendszer ben, és *az 51-es, "Nem támogatott operációs rendszer" hibakódot*bocsáthatnak ki. Ellenőrizze az egyes bővítmények dokumentációjában a támogathatóságot.

#### <a name="network-access"></a>Hálózati hozzáférés

A bővítménycsomagok az Azure Storage-bővítménytárházból töltődnek le, és a bővítmény állapotfeltöltései az Azure Storage-ba kerülnek. Ha az ügynökök [támogatott](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzióját használja, nem kell engedélyeznie az Azure Storage-hoz való hozzáférést a virtuális gép régióban, mivel az ügynök segítségével átirányíthatja a kommunikációt az Azure fabric vezérlőre ügynökkommunikációhoz (HostGAPlugin szolgáltatás a privát IP [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)kiemelt csatornán keresztül). Ha az ügynök nem támogatott verzióját használja, engedélyeznie kell a kimenő hozzáférést az Azure storage-hoz az adott régióban a virtuális gépről.

> [!IMPORTANT]
> Ha a vendégtűzfal vagy a proxy használatával letiltotta a hozzáférést a *168.63.129.16-hoz,* a bővítmények a fentiektől függetlenül sikertelenek. A 80-as, 443-as és 32526-os portok szükségesek.

Az ügyintézők csak bővítménycsomagok és jelentési állapot letöltésére használhatók. Ha például egy bővítmény telepítést kell letölteni egy parancsfájlt a GitHubról (Egyéni parancsfájl), vagy hozzáférést kell biztosítania az Azure Storage -hoz (Azure Backup), akkor további tűzfal/hálózati biztonsági csoport portokat kell megnyitni. A különböző kiterjesztések különböző követelményekkel rendelkeznek, mivel önmagukban is alkalmazások. Az Azure Storage-hoz vagy az Azure Active Directoryhoz való hozzáférést igénylő bővítmények esetében engedélyezheti a hozzáférést [az Azure NSG-szolgáltatáscímkék](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) használatával a Storage-ba vagy az AzureActiveDirectoryba.

A Windows vendégügynök nem rendelkezik proxykiszolgáló-támogatással az ügynökforgalom-kérelmek átirányításához, ami azt jelenti, hogy a Windows vendégügynök az egyéni proxyra támaszkodik (ha van ilyen) az interneten vagy a gazdagépen az IP-címen keresztül történő erőforrások eléréséhez 168.63.129.16.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felfedezése

Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. A teljes lista megtekintéséhez használja a [Get-AzVMExtensionImage programot.](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage) A következő példa a *WestUS* helyen elérhető összes bővítményt sorolja fel:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Virtuálisgép-bővítmények futtatása

Az Azure virtuálisgép-bővítmények meglévő virtuális gépeken futnak, ami akkor hasznos, ha konfigurációs módosításokat kell végrehajtania, vagy helyre kell állítania a kapcsolatot egy már üzembe helyezett virtuális számítógépen. Virtuálisgép-bővítmények is csomagban az Azure Resource Manager-sablon központi telepítések. Bővítmények használatával A Resource Manager-sablonok, Az Azure virtuális gépek üzembe helyezhetők és konfigurálhatók a telepítés utáni beavatkozás nélkül.

A következő módszerekkel egy meglévő virtuális gép egy bővítmény futtatásához használható.

### <a name="powershell"></a>PowerShell

Számos PowerShell-parancs létezik az egyes bővítmények futtatásához. A lista megtekintéséhez használja a [Get-Command parancsot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) és szűrje a *bővítményt:*

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Ez a következőhöz hasonló kimenetet biztosít:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

A következő példa az egyéni parancsfájl-bővítmény t használja egy parancsfájl letöltéséhez egy GitHub-tárházból a cél virtuális gépre, majd futtassa a parancsfájlt. Az egyéni parancsfájlbővítményről az [Egyéni parancsfájlbővítmény – áttekintés](custom-script-windows.md)című témakörben olvashat bővebben.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

A következő példában a Virtuálisgép-hozzáférés bővítmény segítségével állítsa vissza a windowsos virtuális gép felügyeleti jelszavát ideiglenes jelszóvá. A Virtuálisgép-hozzáférés bővítményről további információt a Távoli asztali szolgáltatás visszaállítása Windows virtuális gépen című [témakörben talál.](../windows/reset-rdp.md) Miután futtatta ezt, akkor állítsa vissza a jelszót az első bejelentkezés:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

A `Set-AzVMExtension` parancs bármely virtuálisgép-bővítmény indításához használható. További információt a [Set-AzVMExtension referenciacímű témakörben talál.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension)


### <a name="azure-portal"></a>Azure portál

Virtuálisgép-bővítmények egy meglévő virtuális gép az Azure Portalon keresztül alkalmazható. Jelölje ki a virtuális gép a portálon, válassza **a Bővítmények**, majd a **Hozzáadás**lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait.

A következő példa a Microsoft Antimalware bővítmény azure portalról történő telepítését mutatja be:

![Kártevőirtó bővítmény telepítése](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager-sablonhoz, és a sablon központi telepítésével hajthatók végre. Ha egy kiterjesztést egy sablonnal telepít, teljes mértékben konfigurált Azure-központi telepítések hozhat létre. Például a következő JSON egy Resource Manager-sablonból származik, amely egy terheléselosztással ellátott virtuális gépeket és egy Azure SQL-adatbázist telepít, majd telepíti a .NET Core alkalmazást minden virtuális gépre. A virtuális gép bővítmény gondoskodik a szoftver telepítéséről.

További információt a [teljes Erőforrás-kezelő sablonban](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)talál.

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

Az Erőforrás-kezelő sablonok létrehozásáról az [Azure Resource Manager-sablonok létrehozása Windows VM-bővítményekkel című témakörben olvashat bővebben.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatainak védelme

Virtuálisgép-bővítmény futtatásakor szükség lehet bizalmas adatokat, például hitelesítő adatokat, tárfiók neveket és tárfiók hozzáférési kulcsokat. Számos virtuálisgép-bővítmény tartalmaz egy védett konfigurációt, amely titkosítja az adatokat, és csak a cél virtuális gépbelsejében fejti ki. Minden bővítmény rendelkezik egy adott védett konfigurációs sémával, és mindegyik részletesen a bővítmény-specifikus dokumentációban.

A következő példa a Windows egyéni parancsfájl-bővítményének egy példányát mutatja be. A végrehajtandó parancs hitelesítő adatokat tartalmaz. Ebben a példában a végrehajtandó parancs nincs titkosítva:

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

Ha a parancsot a **védett** konfigurációra helyezi át a tulajdonság **végrehajtásához,** az a végrehajtási karakterláncot biztosítja, ahogy az a következő példában látható:

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

Egy bővítményeket használó Azure IaaS virtuális gépen a tanúsítványkonzolon olyan tanúsítványok jelenhetnek meg, amelyek a **_tulajdonos Windows Azure CRP tanúsítványgenerátorral rendelkeznek._** Klasszikus RDFE virtuális gép esetén ezek a tanúsítványok windows Azure Service Management for Extensions nevű tulajdonossal **_rendelkeznek._**

Ezek a tanúsítványok biztosítják a virtuális gép és a gazdagép közötti kommunikációt a bővítmények által használt védett beállítások (jelszó, egyéb hitelesítő adatok) átvitele során. A tanúsítványok az Azure-háló vezérlő által készült, és a virtuális gép ügynöke. Ha minden nap leállítja és elindítja a virtuális gép, a hálóvezérlő új tanúsítványt hozhat létre. A tanúsítvány a számítógép személyes tanúsítványtárolójában tárolódik. Ezek a tanúsítványok törölhetők. A virtuálisgép-ügynök szükség esetén újra létrehozza a tanúsítványokat.

### <a name="how-do-agents-and-extensions-get-updated"></a>Hogyan frissülnek az ügynökök és a bővítmények?

Az ügyintézők és bővítmények ugyanazt a frissítési mechanizmust. Egyes frissítések nem igényelnek további tűzfalszabályokat.

Ha egy frissítés elérhető, csak akkor lesz telepítve a virtuális gépre, ha módosulnak a bővítmények, és más virtuálisgép-modell is módosul, például:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztikai tároló
- Vendég operációs rendszer titkai
- Virtuális gép mérete
- Hálózati profil

A közzétevők különböző időpontokban teszik elérhetővé a frissítéseket a régiók számára, így lehetséges, hogy különböző régiókban lévő virtuális gépek különböző verziókban vannak.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Virtuális gépre telepített bővítmények listázása

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Ügynök frissítések

A Windows vendégügynök csak *bővítménykezelési kódot*tartalmaz, a *Windows kiépítési kód* külön működik. Eltávolíthatja a Windows vendégügynököt. Az ablakvendégügynök automatikus frissítése nem tiltható le.

Az *Extension Handling kód* felelős az Azure-hálóval való kommunikációért és a virtuálisgép-bővítmények műveleteinek, például a telepítések, a jelentési állapot, az egyes bővítmények frissítésének és eltávolításának kezeléséért. A frissítések biztonsági javításokat, hibajavításokat és a *bővítménykezelési kód*továbbfejlesztéseit tartalmazzák.

A futtatott verzió ellenőrzéséről a [Telepített Windows vendégügynök észlelése](agent-windows.md#detect-the-vm-agent)című témakörben található.

#### <a name="extension-updates"></a>Bővítmény frissítései

Ha rendelkezésre áll egy bővítményfrissítés, a Windows vendégügynök letölti és frissíti a bővítményt. Az automatikus bővítményfrissítések *következők: Minor* vagy *Gyorsjavítás.* A bővítmények kisebb *frissítéseket* a bővítmény kiépítésekor engedélyezheti vagy letilthatja. A következő példa bemutatja, hogyan lehet automatikusan frissíteni az alverziókat egy Erőforrás-kezelő sablonban *az autoUpgradeMinorVersion" parancával:':*

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

A legújabb kisebb kiadási hibajavítások lehívásához erősen ajánlott, hogy mindig válassza ki az automatikus frissítést a bővítmény központi telepítésekben. A biztonsági vagy kulcsfontosságú hibajavításokat tartalmazó gyorsjavítások frissítései ről nem lehet leiratkozni.

### <a name="how-to-identify-extension-updates"></a>A bővítményfrissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Annak megállapítása, hogy a bővítmény be van-e állítva az autoUpgradeMinorVersion funkcióval egy virtuális gépen

A virtuális gép modelljéből láthatja, hogy a bővítmény ki lett-e építve az "autoUpgradeMinorVersion" segítségével. Az ellenőrzéshez használja a [Get-AzVm-et,](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) és adja meg az erőforráscsoportot és a virtuális gép nevét az alábbiak szerint:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

A következő példa kimenet azt mutatja, hogy *az autoUpgradeMinorVersion* *értéke true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Annak azonosítása, hogy mikor történt autoUpgradeMinorVersion

A bővítmény frissítésének megtekintéséhez tekintse át a virtuális gép ügynöknaplóit a *C:\WindowsAzure\Logs\WaAppAgent.log webhelyen.*

A következő példában a virtuális gép en telepítve volt a *Microsoft.Compute.CustomScriptExtension 1.8.* A gyorsjavítás az *1.9-es*verzióhoz volt elérhető:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Ügynöki engedélyek

A feladatok végrehajtásához az ügynöknek helyi rendszerként kell *futnia.*

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények – problémamegoldás

Minden virtuálisgép-bővítmény hibaelhárítási lépésekkel rendelkezhet a bővítményre jellemzően. Például az egyéni parancsfájl-bővítmény használatakor a parancsfájl-végrehajtási részletek helyileg találhatók a virtuális gépen, ahol a bővítmény futott. A bővítményspecifikus hibaelhárítási lépéseket a bővítményspecifikus dokumentáció részletezi.

A következő hibaelhárítási lépések az összes virtuálisgép-bővítményre vonatkoznak.

1. A Windows vendégügynöki naplójának ellenőrzéséhez tekintse meg azt a tevékenységet, amikor a bővítmény kiépítés alatt állt a *C:\WindowsAzure\Logs\WaAppAgent.log mappában.*

2. További részleteket a *C:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Ellenőrizze a bővítményspecifikus dokumentációhiba-elhárítási szakaszokat a hibakódok, az ismert problémák stb.

4. Nézd meg a rendszernaplókat. Ellenőrizze, hogy vannak-e olyan műveletek, amelyek esetleg megzavarták a bővítményt, például egy másik alkalmazás hosszú ideig futó telepítése, amely kizárólagos csomagkezelői hozzáférést igényelt.

### <a name="common-reasons-for-extension-failures"></a>A kiterjesztési hibák gyakori okai

1. A bővítmények futtatása 20 min (kivételt képeznek a CustomScript-bővítmények, a Chef és a DSC, amelyek 90 mins-t használnak). Ha a központi telepítés meghaladja ezt az időt, időtúllépésként van megjelölve. Ennek oka lehet az alacsony erőforrás-virtuális gépek, más virtuálisgép-konfigurációk/indítási feladatok, amelyek nagy mennyiségű erőforrást fogyasztanak, miközben a bővítmény próbál kiépíteni.

2. A minimális előfeltételek nem teljesülnek. Egyes bővítmények a virtuális gép termékkészleteitől függnek, például a HPC-lemezképektől. A bővítmények bizonyos hálózati hozzáférési követelményeket igényelhetnek, például az Azure Storage-nak vagy a nyilvános szolgáltatásoknak való kommunikációt. További példák lehetnek a csomagtárolókhoz való hozzáférés, a lemezterület kifutása vagy a biztonsági korlátozások.

3. Exkluzív csomagkezelői hozzáférés. Bizonyos esetekben előfordulhat, hogy egy hosszú ideig futó virtuális gép konfigurációés a bővítmény telepítése ütközik, ahol mindketten kizárólagos hozzáférést igényelnek a csomagkezelőhöz.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután egy virtuális gép bővítménye fut egy virtuális gép, használja [a Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a bővítmény állapotát adja vissza. *Substatuses[0]* azt mutatja, hogy a bővítmény kiépítése sikeres volt, ami azt jelenti, hogy sikeresen telepítve a virtuális gép, de a bővítmény végrehajtása a virtuális gép nem sikerült, *Substatuses[1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A kimenet hasonló a következő példakimenethez:

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

A bővítmény végrehajtási állapota az Azure Portalon is megtalálható. A bővítmény állapotának megtekintéséhez válassza a virtuális gép, válassza **a Bővítmények**lehetőséget, majd válassza ki a kívánt bővítményt.

### <a name="rerun-vm-extensions"></a>Virtuálisgép-bővítmények újrafuttatása

Előfordulhatnak olyan esetek, amikor a virtuális gép bővítményt újra kell futtatni. A bővítmény taszthatja újra, távolítsa el, majd futtassa újra a bővítményt egy ön által választott végrehajtási módszerrel. Bővítmény eltávolításához használja az [Eltávolítás-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) parancsot az alábbiak szerint:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Az Azure Portalon az alábbiak szerint is eltávolíthat egy bővítményt:

1. Válasszon ki egy virtuális gép.
2. Válassza **a Bővítmények lehetőséget**.
3. Válassza ki a kívánt kiterjesztést.
4. Válassza **az Eltávolítás lehetőséget.**

## <a name="common-vm-extensions-reference"></a>Gyakori virtuálisgép-bővítmények – referencia
| Kiterjesztés neve | Leírás | További információ |
| --- | --- | --- |
| Egyéniszkript-bővítmény Windows rendszerre |Parancsfájlok futtatása egy Azure-beli virtuális gépen |[Egyéniszkript-bővítmény Windows rendszerre](custom-script-windows.md) |
| DSC-bővítmény Windows hoz |PowerShell DSC (kívánt állapotkonfiguráció) bővítmény |[DSC-bővítmény Windows hoz](dsc-overview.md) |
| Azure Diagnostics bővítmény |Az Azure diagnosztika kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure Virtuálisgép-hozzáférési bővítmény |Felhasználók és hitelesítő adatok kezelése |[Vm Access extension linuxos](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások áttekintése című témakörben olvashat bővebben.](overview.md)
