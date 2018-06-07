---
title: Az Azure Virtuálisgép-bővítmények és a Windows szolgáltatások |} Microsoft Docs
description: Ismerje meg, milyen bővítmények érhetők el, mi akkor adja meg, vagy javítania szerint csoportosítva, az Azure virtuális gépeken.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9e147e2cbe5ff42562d6fcfab62460df48f3d65
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809726"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuálisgép-bővítmények és a Windows szolgáltatások

Az Azure virtuális gép (VM) a program kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken. Ha például egy virtuális géphez szükséges Szoftvertelepítés, a víruskereső védelmet, vagy saját parancsfájl futtatása, a Virtuálisgép-bővítmény is használható. Az Azure Virtuálisgép-bővítmények az Azure parancssori felület, PowerShell, az Azure Resource Manager-sablonok és az Azure-portálon is futtatható. Bővítmények mellékelhető egy új virtuális gép üzembe helyezéséhez, vagy bármely létező rendszert futtathat.

Ez a cikk áttekintést Virtuálisgép-bővítmények, Azure Virtuálisgép-bővítmények használatára vonatkozó Előfeltételek és útmutatás észleléséhez, kezeléséhez, és távolítsa el a Virtuálisgép-bővítmények. Ez a cikk általános információkat nyújt, sok Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény felhasználóspecifikus adatainak található minden a dokumentumban a egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Számos különböző Azure Virtuálisgép-bővítmények érhetők el, az adott használati eset. Néhány példa:

- A DSC-bővítményt a virtuális gép Windows PowerShell kívánt állapot konfiguráció alkalmazása. További információkért lásd: [Azure kívánt állapot konfigurációs kiterjesztése](dsc-overview.md).
- Konfigurálhatja a virtuális gép és a Microsoft Monitoring Agent Virtuálisgép-bővítmény figyelését. További információkért lásd: [Azure virtuális gépek csatlakozni Naplóelemzési](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfiguráljon egy Azure virtuális gép Chef. További információkért lásd: [automatizálása Azure VM telepítése a Chef](../windows/chef-automation.md).
- Az Azure infrastruktúra Datadog kiterjesztésű figyelés konfigurálása. További információkért lásd: a [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Folyamatokra vonatkozó bővítmények mellett egy egyéni parancsprogramok futtatására szolgáló bővítmény érhető el a Windows és a Linux virtuális gépekhez. Az egyéni parancsprogramok futtatására szolgáló bővítmény Windows lehetővé teszi, hogy a PowerShell-parancsfájlt a virtuális gép futtatásához. Egyéni parancsfájlok túl milyen natív Azure tooling biztosíthat beállításokra van szükség Azure központi telepítések tervezése során hasznosak. További információkért lásd: [Windows virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-windows.md).

## <a name="prerequisites"></a>Előfeltételek

Kezeli a bővítményt a virtuális Gépre, az Azure Linux-ügynök telepítve van szükség. Néhány egyéni bővítményt rendelkezik Előfeltételek, például az erőforrások és a függőségek elérését.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök

Az Azure Virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric controller közötti kommunikációt. A Virtuálisgép-ügynök telepítése és kezelése az Azure virtuális gépeken, beleértve a Virtuálisgép-bővítmények futtató sok működési jellemzői felelős. Az Azure Virtuálisgép-ügynök telepítve van az Azure piactéren elérhető rendszerkép, és támogatott operációs rendszeren manuálisan telepíthető. Az Azure virtuális gép ügynök a Windows a Windows-vendégügynök nevezik.

Információ a támogatott operációs rendszerek és a telepítési utasításokat: [Azure virtuális gépi ügynöke](agent-windows.md).

#### <a name="supported-agent-versions"></a>Támogatott ügynökverziók

Így biztosíthatja a legjobb élményben legyen részük, az ügynök minimális verziója van. További információkért tekintse meg [ezt a cikket](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Támogatott operációs rendszerei között

A Windows-vendégügynök több operációs futó, azonban a bővítmények keretrendszer van korlátozva az operációs rendszer számára, hogy a bővítmények. További információ: [Ez a cikk] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Néhány bővítmény nem támogatottak minden operációs rendszer között, és bocsáthat *hiba kód 51, a "Nem támogatott az operációs rendszer"*. Támogathatóság egyedi bővítmény dokumentációjában.

#### <a name="network-access"></a>Hálózati hozzáférés

Bővítménycsomagok letölteni az Azure Storage bővítménytárban, és Azure Storage feladott bővítmény állapota feltöltések. Ha [támogatott](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) az ügynökök verziója, nem kell hozzáférést a virtuális gép régióban Azure Storage hasonlóan az ügynök a kommunikációs átirányítja az Azure fabric controller ügynök-kommunikációhoz. Ha az ügynök egy nem támogatott verzióját, szüksége az Azure storage régió kimenő hozzáférés engedélyezése a virtuális gépről.

> [!IMPORTANT]
> Ha a letiltott hozzáférés *168.63.129.1* a Vendég tűzfalat használ, akkor bővítmények sikertelen függetlenül a fent.

Ügynökök csak bővítménycsomagok és állapotjelentést letöltésére használható. Például ha egy bővítmény telepítési le kell töltenie egy parancsfájlt a Githubról (egyéni parancsfájl), vagy meg kell majd elérni az Azure Storage (az Azure Backup), további tűzfal és a hálózati biztonsági csoport portokat kell megnyitni. Egyes bővítmények különféle eltérő követelmények vonatkoznak, lehet, mert a saját jobbra lévő alkalmazások. Azure Storage hozzáférést igénylő bővítményekhez, engedélyezheti az Azure NSG szolgáltatás címkék használatával [tárolási](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

A Windows-Vendégügynök nincs proxykiszolgáló támogatja az ügynök forgalom kérelmeket keresztül irányíthatja.

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése

Azure virtuális gépekhez való használatra számos különböző Virtuálisgép-bővítmények érhetők el. Teljes listájának megtekintéséhez használja [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). Az alábbi példa felsorolja az összes rendelkezésre álló bővítményeket a *WestUS* helye:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Azure Virtuálisgép-bővítmények futtassa a meglévő virtuális gépeken, amelyek akkor hasznos, ha a konfigurációs módosításokat, vagy állítsa helyre a kapcsolatot egy már telepített virtuális gépen kell. Virtuálisgép-bővítmények is Azure Resource Manager sablon központi telepítéseket is telepíthet. Bővítmények a Resource Manager-sablonok segítségével Azure virtuális gépeken is telepítését és konfigurálását telepítés utáni beavatkozás nélkül.

Az alábbi módszerek segítségével egy bővítmény egy meglévő virtuális gép futtatásához.

### <a name="powershell"></a>PowerShell

Több PowerShell-parancsok futtatása az egyes bővítmények léteznek. Listájának megtekintéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command) és szűrést végezni *bővítmény*:

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

A következő példa az egyéni parancsprogramok futtatására szolgáló bővítmény parancsfájl letölthető a GitHub-tárházban települ a cél virtuális gépre, és futtassa a parancsfájlt. Az egyéni parancsprogramok futtatására szolgáló bővítmény további információkért lásd: [egyéni parancsfájl-bővítmény áttekintése](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

A következő példában a virtuális gép hozzáférési bővítmény segítségével a Windows virtuális gépek a rendszergazdai jelszó visszaállítása egy ideiglenes jelszót. A virtuális gép hozzáférési bővítmény további információkért lásd: [alaphelyzetbe állítja a távoli asztal szolgáltatás egy Windows virtuális gépre](../windows/reset-rdp.md). Futtatása után ez, állítsa vissza a jelszót első bejelentkezéskor:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

A `Set-AzureRmVMExtension` parancs segítségével indítsa el a Virtuálisgép-bővítmény. További információkért lásd: a [Set-AzureRmVMExtension hivatkozás](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Virtuálisgép-bővítmények egy meglévő virtuális gépre az Azure portálon keresztül is alkalmazható. Válassza ki a virtuális Gépet a portálon, válassza a **bővítmények**, majd jelölje be **Hozzáadás**. Válassza ki a bővítmény szeretné, hogy a rendelkezésre álló bővítmények a listából, és kövesse a varázsló utasításait.

A következő példa bemutatja az Azure-portálról a Microsoft Antimalware-bővítmény telepítése:

![Kártevőirtó-kiterjesztés telepítése](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények felvehető az Azure Resource Manager-sablon és a sablon a központi telepítés végrehajtása. Egy bővítmény a sablon telepítésekor hozhat létre teljesen konfigurált Azure-környezetekhez. Például a következő JSON használatban van egy Resource Manager sablon terheléselosztással rendelkező virtuális gépek és az Azure SQL-adatbázis telepít, majd telepíti a .NET Core alkalmazást minden egyes virtuális gépen. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

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

Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [szerzői Azure Resource Manager-sablon is van Windows Virtuálisgép-bővítmények](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatvédelem

Amikor futtat egy Virtuálisgép-bővítmény, akkor lehet szükség például a hitelesítő adatokat, a tárfiókok neve és a fiók tárelérési kulcsok bizalmas információkat szeretne. Több Virtuálisgép-bővítmények közé tartozik a védett konfigurációkat, amelyek titkosítja az adatokat, és csak visszafejti a cél virtuális gép belül. Mindegyik bővítményt egy adott védett konfigurációs séma van, és az egyes részleteit a bővítmény-specifikus dokumentációját.

A következő példa bemutatja az egyéni parancsprogramok futtatására szolgáló bővítmény példányának Windows. A parancs végrehajtásának olyan hitelesítő adatokat tartalmaz. Ebben a példában a parancs végrehajtása nem titkosított:

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

Helyezze át a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs titkosítja a végrehajtási karakterlánc a következő példában látható módon:

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

Az ügynökök és a bővítmények ossza meg az azonos frissítési mechanizmus. Néhány frissítést nem szükséges további tűzfalszabályokat.

Frissítés érhető el, ha csak telepíti a rendszer a virtuális Gépre van bővítmények módosítva lett, és más virtuális gép modellmódosításokkal többek között:

- Adatlemezek
- Bővítmények
- Rendszerindítási diagnosztika tároló
- Vendég operációs rendszer titkos kulcsok
- Virtuális gép mérete
- Hálózati profil

Közzétevők frissítések számára elérhetővé tenni régiók különböző időpontokban, így a virtuális gépek különböző verzióin különböző régiókban is lehet.

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

#### <a name="agent-updates"></a>Frissítések

A Windows-Vendégügynök csak olyan *kiterjesztés kezelése kód*, a *Windows kiépítés kód* különálló. A Windows-Vendégügynök eltávolítható. Az automatikus frissítés a az ablak Vendégügynökének nem tiltható le.

A *kiterjesztés kezelése kód* kommunikál az Azure-hálót, és kezelése, mint a virtuális gép bővítményei műveletek telepíti, jelentéskészítési állapot, az egyes bővítmények frissítése, és eltávolítja azokat a felelős. Biztonsági javítások, hibajavításokat tartalmaz, és továbbfejlesztett szerepelnek a *kiterjesztés kezelése kód*.

Ellenőrizze, mely verzióját futtatja, lásd: [lemezhibák keresése telepített Windows Vendégügynök](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Bővítmény frissítések

Egy bővítmény frissítése nem érhető el, a Windows-Vendégügynök tölti le, és frissíti a bővítményt. Automatikus bővítmény frissítései vagy *kisebb* vagy *gyorsjavítás*. Részt vevő, vagy kikapcsolja a bővítmények *kisebb* frissíti, amikor a bővítményt. A következő példa bemutatja, hogyan automatikus frissítése a Resource Manager sablonokban alverziót *autoupgrademinorversion elemének értéke pedig ": true,"*:

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

Ahhoz, hogy a legújabb kiadási kisebb hibák javításával, javasoljuk, hogy mindig választotta automatikus frissítés a bővítmény telepítések esetén. Gyorsjavítás-frissítések, biztonsági vagy kulcs hibajavításokat tartalmaz, amelyben nem kapcsolható.

### <a name="how-to-identify-extension-updates"></a>Bővítmény frissítések azonosítása

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Ha a kiterjesztés van állítva az autoupgrademinorversion elemének értéke pedig a virtuális gép azonosítása

Ha a bővítmény ki volt építve "autoUpgradeMinorVersion" tekintheti meg a virtuális gép modellből. Ellenőrizze, hogy használjon [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) , és adja meg az erőforráscsoportot és a virtuális gép neve az alábbiak szerint:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Az alábbi példa kimenetben látható, melyet *autoUpgradeMinorVersion* értéke *igaz*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Amikor egy autoUpgradeMinorVersion történt azonosítása

Ha egy frissítést adunk ki a bővítmény történt megtekintéséhez tekintse át az ügynököt a virtuális gép egyidejű bejelentkezik *C:\WindowsAzure\Logs\WaAppAgent.log*

A következő példában a virtuális gép kellett *Microsoft.Compute.CustomScriptExtension 1.8* telepítve. A gyorsjavítás volt elérhető verzióra *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Ügynök engedélyek

A feladatok végrehajtásához az futnia kell az ügynököt *helyi rendszer*.

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibaelhárítása

Minden egyes Virtuálisgép-bővítmény hibaelhárítási lépések az adott a kiterjesztést is rendelkezhet. Például az egyéni parancsprogramok futtatására szolgáló bővítmény használatakor parancsfájl végrehajtásának részletes adatai található helyileg a virtuális gép, amelyen a bővítmény futott. Bővítmény-specifikus hibaelhárítási lépéseket részletes leírást talál bővítmény vonatkozó dokumentációt.

A következő hibaelhárítási lépéseket az összes Virtuálisgép-bővítmények vonatkozik.

1. A Windows vendég ügynök naplóját ellenőrzéséhez tekintse meg a tevékenység, ha a kiterjesztés lett létre a *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. A tényleges bővítmény további részleteket a naplókban *C:\WindowsAzure\Logs\Plugins\<bővítménynév >*

3. Ellenőrizze a bővítmény megadott dokumentáció hibaelhárítási szakaszok kódok, ismert problémák stb.

4. Tekintse meg a rendszernaplót. Ellenőrizze, hogy előfordulhat, hogy rendelkezik befolyásolható, a kiterjesztéssel, például egy másik alkalmazás kizárólagos package manager hozzáférés szükséges egy hosszú ideig tartó telepített egyéb műveleteket.

### <a name="common-reasons-for-extension-failures"></a>A bővítmény hibák leggyakoribb okai

1. Bővítmények rendelkezik futtatásához 20 perc (Kivételek a következők a CustomScript bővítmények, Chef, és 90 perc rendelkező DSC). Ha a központi telepítés meghaladja a megadott idő, azt egy időtúllépés van megjelölve. A ennek oka az okozhatja, hogy kevés az erőforrás virtuális gépeket, más virtuális gép konfigurációk/kezdő feladatok nagy mennyiségű erőforrás fel, megőrizve a bővítmény próbál kiépítéséhez.

2. Minimális előfeltételek nem teljesültek. Néhány bővítmény tartalmazhat függőségeket VM SKU, például a HPC-lemezképeket. Bővítmények szükség lehet egyes hálózati hozzáférési követelmények, például az Azure Storage- vagy nyilvános szolgáltatásokhoz való kommunikáció során. További példák lehet csomag tárházak találhatók, kevés a szabad lemezterület, vagy a biztonsági korlátozások a hozzáférést.

3. Kizárólagos package manager hozzáférést. Bizonyos esetekben felmerülhet a hosszú ideig futó Virtuálisgép-konfiguráció és a bővítmény telepítése ütköző, ahol mindkét van szükségük a Csomagkezelő kizárólagos hozzáférést.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

Miután egy Virtuálisgép-bővítmény futtatása a virtuális gépek elleni, a [Get-AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) vissza a bővítmény állapotát. *[0] részállapotok* azt mutatja, hogy a bővítmény kiépítés sikeres volt, ami azt jelenti, hogy az informatikai sikeres központi telepítése a virtuális Gépet, de a bővítményt a virtuális Gépen belül végrehajtása sikertelen volt, *részállapotok [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A kimeneti hasonlít a következő egy példa a kimenetre:

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

Bővítmény végrehajtási állapotát az Azure portálon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális Gépet, kattintson a **bővítmények**, majd jelölje ki a kívánt bővítményt.

### <a name="rerun-vm-extensions"></a>Futtassa újra a Virtuálisgép-bővítmények

Előfordulhatnak olyan esetekben, ahol egy Virtuálisgép-bővítmény futtatható kell. Egy bővítmény akkor távolítsa el, majd futtassa újból a kiterjesztés egy végrehajtási módszer az Ön által választott újrafuttathatja. Egy bővítmény eltávolításához használja [Remove-AzureRmVMExtension](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) az alábbiak szerint:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Is eltávolíthatja kiterjesztése az Azure-portálon az alábbiak szerint:

1. Jelöljön ki egy virtuális Gépet.
2. Válasszon **bővítmények**.
3. Jelölje ki a kívánt bővítményt.
4. Válasszon **eltávolítása**.

## <a name="common-vm-extensions-reference"></a>Közös Virtuálisgép-bővítmények hivatkozása
| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| A Windows egyéni parancsprogramok futtatására szolgáló bővítmény |Parancsfájlok futtatásához egy Azure virtuális gépen |[A Windows egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-windows.md) |
| A Windows DSC-bővítményt |PowerShell DSC (célállapot-konfiguráció) bővítmény |[A Windows DSC-bővítményt](dsc-overview.md) |
| Azure Diagnostics bővítmény |Az Azure Diagnostics kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure virtuális gép hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linux virtuális gép hozzáférési bővítmény](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>További lépések

További információ a Virtuálisgép-bővítmények: [Azure virtuális gép bővítményeket és szolgáltatásokat – áttekintés](overview.md).