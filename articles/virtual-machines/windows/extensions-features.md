---
title: "Virtuálisgép-bővítmények és a szolgáltatások a Windows Azure-ban |} Microsoft Docs"
description: "Ismerje meg, milyen bővítmények érhetők el, mi akkor adja meg, vagy javítania szerint csoportosítva, az Azure virtuális gépeken."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ce0eebd2585c9457d7f922898d7f2fa3e7ffad7
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuálisgép-bővítmények és a Windows szolgáltatások

Az Azure virtuálisgép-bővítmények kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken. Például ha egy virtuális gépet a Szoftvertelepítés, a víruskereső védelmet, vagy a Docker konfigurációs igényel, a Virtuálisgép-bővítmény segítségével ezeket a feladatokat. Az Azure Virtuálisgép-bővítmények futtathatja az Azure parancssori felület, PowerShell, az Azure Resource Manager-sablonok és az Azure-portál használatával. Bővítmények mellékelhető egy új virtuálisgép-telepítést, vagy bármely létező rendszert futtathat.

Ez a dokumentum áttekintést virtuálisgép-bővítmények, virtuálisgép-bővítmények és útmutatás észleléséhez, kezeléséhez, és távolítsa el a virtuálisgép-bővítmények használatának előfeltételei. A dokumentum általános információkat tartalmaz a Virtuálisgép-bővítmények érhetők el, mert egyes potenciálisan egyedi konfigurációval. Bővítmény felhasználóspecifikus adatainak található minden a dokumentumban a egyéni bővítmény egyedi.

## <a name="use-cases-and-samples"></a>Használati esetek és minták

Nincsenek elérhető számos különböző Azure Virtuálisgép-bővítmények, az adott használati eset. Néhány példa használati esetek a következők:

- A virtuális gépek a DSC-bővítményt a Windows PowerShell kívánt állapot konfiguráció alkalmazása. További információkért lásd: [Azure kívánt állapot konfigurációs kiterjesztése](extensions-dsc-overview.md).
- Konfigurálja a virtuális gép figyelése a Microsoft Monitoring Agent Virtuálisgép-bővítmény használatával. További információkért lásd: [Log Analyticshez való csatlakozás Azure virtuális gépek](../../log-analytics/log-analytics-azure-vm-extension.md).
- Az Azure infrastruktúra Datadog kiterjesztésű figyelés konfigurálása. További információkért lásd: a [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfiguráljon egy Azure virtuális gép Chef. További információkért lásd: [automatizálása Azure virtuális gépek telepítése a Chef](chef-automation.md).

Folyamatokra vonatkozó bővítmények mellett egy egyéni parancsprogramok futtatására szolgáló bővítmény érhető el a Windows és a Linux virtuális gépekhez. Az egyéni parancsprogramok futtatására szolgáló bővítmény Windows lehetővé teszi, hogy minden virtuális gépen futtatandó PowerShell-parancsfájlt. Ez akkor hasznos, amikor az identitásfelügyelet Azure-környezetekhez, hogy milyen natív Azure tooling biztosíthat túl beállításokra van szükség. További információkért lásd: [Windows virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény](extensions-customscript.md).


## <a name="prerequisites"></a>Előfeltételek

Minden egyes virtuálisgép-bővítmény is rendelkeznek a saját előfeltételek. Például a Docker Virtuálisgép-bővítmény van egy támogatott Linux-disztribúció előfeltétele. Egyes bővítmények követelményeinek részletes leírást talál a bővítmény-specifikus dokumentációját.

### <a name="azure-vm-agent"></a>Azure virtuálisgép-ügynök
Az Azure Virtuálisgép-ügynök kezeli az Azure virtuális gép és az Azure fabric controller közötti interakció. A Virtuálisgép-ügynök telepítése és kezelése az Azure virtuális gépeken, beleértve a Virtuálisgép-bővítmények futtató sok működési jellemzői felelős. Az Azure Virtuálisgép-ügynök telepítve van az Azure piactéren elérhető rendszerkép, és támogatott operációs rendszerekre telepíthető.

Információ a támogatott operációs rendszerek és a telepítési utasításokat: [Azure virtuális gépi ügynöke](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Virtuálisgép-bővítmények felderítése
Számos különböző Virtuálisgép-bővítmények állnak rendelkezésre az Azure virtuális gépekkel. Teljes listájának megtekintéséhez futtassa a következő parancsot az Azure Resource Manager PowerShell-modul. Ügyeljen arra, hogy adja meg a kívánt helyre, ha futtatja a parancsot.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Futtassa a Virtuálisgép-bővítmények

Azure virtuálisgép-bővítmények futtathatja a meglévő virtuális gépeken, amelyek akkor hasznos, ha a konfigurációs módosításokat, vagy állítsa helyre a kapcsolatot egy már telepített virtuális gépen kell. Virtuálisgép-bővítmények is Azure Resource Manager sablon központi telepítéseket is telepíthet. Bővítmények a Resource Manager-sablonok segítségével engedélyezheti az Azure virtuális gépek telepítése és a telepítés utáni beavatkozás nélkül konfigurálva.

Az alábbi eljárások segítségével bővítmény futtathat egy meglévő virtuális gépet.

### <a name="powershell"></a>PowerShell

Több PowerShell-parancsok futtatása az egyes bővítmények léteznek. Listájának megtekintéséhez futtassa a következő PowerShell-parancsokat.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Ez biztosítja, hogy a kimenet az alábbihoz hasonló:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

A következő példa az egyéni parancsprogramok futtatására szolgáló bővítmény parancsfájl letölthető a GitHub-tárházban települ a cél virtuális gépre, és futtassa a parancsfájlt. Az egyéni parancsprogramok futtatására szolgáló bővítmény további információkért lásd: [egyéni parancsfájl-bővítmény áttekintése](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Ebben a példában a virtuális gép hozzáférési bővítmény segítségével Windows rendszerű virtuális gép rendszergazdai jelszavát. A virtuális gép hozzáférési bővítmény további információkért lásd: [alaphelyzetbe állítja a távoli asztal szolgáltatás egy Windows virtuális gépre](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

A `Set-AzureRmVMExtension` parancs segítségével indítsa el a Virtuálisgép-bővítmény. További információkért lásd: a [Set-AzureRmVMExtension hivatkozás](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

A Virtuálisgép-bővítmény egy meglévő virtuális gépet az Azure portálon keresztül is alkalmazható. Ehhez az szükséges, válassza ki a virtuális gép szeretne használni, válasszon **bővítmények**, és kattintson a **Hozzáadás**. Ez a rendelkezésre álló bővítmények listáját tartalmazza. Jelölje ki a kívánt, és kövesse a varázsló lépéseit.

A következő kép bemutatja az Azure-portálról a Microsoft Antimalware-bővítmény telepítése.

![Kártevőirtó-kiterjesztés telepítése](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Virtuálisgép-bővítmények felvehető az Azure Resource Manager-sablon és a sablon a központi telepítés végrehajtása. Központi telepítése és egy sablon létrehozása esetén hasznos teljesen konfigurált Azure-környezetekhez. Például a következő JSON származik a Resource Manager-sablon, amely telepíti az elosztott terhelésű virtuális gépek és Azure SQL-adatbázis, és ezután telepíti a .NET Core alkalmazások minden egyes virtuális gépen. A Virtuálisgép-bővítmény gondoskodik a szoftver telepítését.

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
    "typeHandlerVersion": "1.4",
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

További információkért lásd: [szerzői Azure Resource Manager-sablon is van Windows Virtuálisgép-bővítmények](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Virtuálisgép-bővítmény adatvédelem

Jelenik meg a Virtuálisgép-bővítmény, akkor lehet szükség, például a hitelesítő adatokat, a tárfiókok neve és a fiók tárelérési kulcsok bizalmas adatokat tartalmazza. Több Virtuálisgép-bővítmények közé tartozik a védett konfigurációkat, amelyek titkosítja az adatokat, és csak visszafejti a cél virtuális gépen belül. Minden bővítmény rendelkezik egy adott védett konfigurációs sémában, amely a bővítmény-specifikus dokumentációjának nyilatkozatát.

A következő példa bemutatja az egyéni parancsprogramok futtatására szolgáló bővítmény példányának Windows. Figyelje meg, hogy a parancs végrehajtásának tartalmazza-e a hitelesítő adatokat. Ebben a példában a parancs végrehajtása nem lesznek titkosítva.


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
    "typeHandlerVersion": "1.4",
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

A végrehajtási karakterlánc biztonságos át a **végrehajtandó parancs** tulajdonságot a **védett** konfigurációs.

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
    "typeHandlerVersion": "1.4",
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

## <a name="troubleshoot-vm-extensions"></a>Virtuálisgép-bővítmények hibaelhárítása

Előfordulhat, hogy az egyes Virtuálisgép-bővítmény adott hibaelhárítási lépéseket. Például az egyéni parancsprogramok futtatására szolgáló bővítmény használatakor parancsfájl végrehajtásának részletes adatai található helyileg a virtuális gépen, amelyen a bővítmény futott. Bővítmény-specifikus hibaelhárítási lépéseket részletes leírást talál bővítmény vonatkozó dokumentációt.

A következő hibaelhárítási lépéseket az összes virtuálisgép-bővítmény vonatkozik.

### <a name="view-extension-status"></a>Bővítmény állapotának megtekintése

A virtuálisgép-bővítmény futtatása a virtuális gépek ellen, után a következő PowerShell-parancs segítségével térjen vissza a bővítmény állapotát. Példa paraméterneveknek cserélje le a saját értékeit. A `Name` paraméter szükséges a bővítmény a végrehajtás során a megadott név.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A kimenet a következőképpen néznek:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Bővítmény végrehajtási állapotát az Azure portálon is található. Egy bővítmény állapotának megtekintéséhez válassza ki a virtuális gépet, kattintson a **bővítmények**, és jelölje ki a kívánt bővítményt.

### <a name="rerun-vm-extensions"></a>Futtassa újra a Virtuálisgép-bővítmények

Előfordulhatnak olyan esetekben, ahol egy virtuálisgép-bővítmény futtatható kell. Ehhez a bővítmény eltávolítása, és majd futtassa újból a kiterjesztés egy végrehajtási módszer az Ön által választott. Egy bővítmény eltávolításához futtassa a következő parancsot az Azure PowerShell modullal. Példa paraméterneveknek cserélje le a saját értékeit.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Egy bővítmény is távolítható el az Azure portál használatával. Ehhez tegye a következőket:

1. Válasszon ki egy virtuális gépet.
2. Válassza ki **bővítmények**.
3. Válassza ki a kívánt bővítményt.
4. Válassza ki **eltávolítása**.

## <a name="common-vm-extensions-reference"></a>Közös Virtuálisgép-bővítmények hivatkozása
| Bővítmény neve | Leírás | További információ |
| --- | --- | --- |
| A Windows egyéni parancsprogramok futtatására szolgáló bővítmény |Parancsfájlok futtatásához egy Azure virtuális gépen |[A Windows egyéni parancsprogramok futtatására szolgáló bővítmény](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| A Windows DSC-bővítményt |PowerShell DSC (célállapot-konfiguráció) bővítmény |[A Windows DSC-bővítményt](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure Diagnostics bővítmény |Az Azure Diagnostics kezelése |[Azure Diagnostics bővítmény](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure virtuális gép hozzáférési bővítmény |Felhasználók és a hitelesítő adatok kezelése |[Linux virtuális gép hozzáférési bővítmény](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
