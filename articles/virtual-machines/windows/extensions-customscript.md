---
title: "A Windows Azure egyéni parancsprogramok futtatására szolgáló bővítmény |} Microsoft Docs"
description: "Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával Windows virtuális gép konfigurációs feladatok automatizálásához"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: nepeters
ms.openlocfilehash: 4117b6020d2d75a953fd5f032b378e49d2c752ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="custom-script-extension-for-windows"></a>A Windows egyéni parancsprogramok futtatására szolgáló bővítmény

Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájl az Azure virtuális gépeken. A bővítmény akkor hasznos, ha a feladás egy vagy több központi telepítés konfigurálása, a szoftver telepítése vagy a más beállításokat / kezelési feladatot. Parancsfájlok le: az Azure storage vagy a Githubon, vagy megadott futásidejű bővítmény: az Azure portálon. Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok, és is futtathat az Azure parancssori felület, PowerShell, Azure-portálon vagy az Azure virtuális gép REST API használatával.

Ez a dokumentum részletesen az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az Azure PowerShell modul, Azure Resource Manager-sablonok és a Windows rendszer hibaelhárítási részletek használata.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az egyéni parancsfájl kiterjesztése a Windows futtatható elleni ügyfél a Windows 10, Windows Server 2008 R2, 2012, 2012 R2 és 2016 feloldja a.

### <a name="script-location"></a>Parancsfájl helyét

A parancsfájl kell Azure Blob Storage tárolóban, vagy egy érvényes URL-cím elérhető bármely más helyen kell tárolni.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az egyéni parancsfájl kiterjesztése a Windows megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>A séma kiterjesztése

A következő JSON a séma az egyéni parancsprogramok futtatására szolgáló bővítmény jelennek meg. A bővítmény (Azure Storage vagy más helyre érvényes URL-CÍMMEL rendelkező) a parancsfájl helyét, valamint végrehajtandó parancsot kell rendelkeznie. Ha a parancsprogram forráskódjának Azure Storage használ, az Azure storage fiók név és fiókkulcs kulcsot meg kell adni. Legyen bizalmas adatokat a rendszer ezeket az elemeket, és a bővítmények védett beállítás konfigurációjában megadott. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
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
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>A tulajdonság értékek

| Név | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Közzétevő | Microsoft.Compute |
| type | Bővítmények |
| typeHandlerVersion | 1.9 |
| fileUris (például) | https://RAW.githubusercontent.com/Microsoft/DotNet-Core-sample-Templates/Master/DotNet-Core-Music-Windows/scripts/Configure-Music-App.ps1 |
| commandToExecute (például) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása zene-app.ps1 |
| storageAccountName (például) | examplestorageacct |
| storageAccountKey (például) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == |

**Megjegyzés:** -e a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. -Neveket használja, a fent látható telepítési problémák elkerülése érdekében.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Itt található, amely tartalmazza az egyéni parancsprogramok futtatására szolgáló bővítmény mintasablon [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMCustomScriptExtension` parancs használható az egyéni parancsprogramok futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure PowerShell modul segítségével. A következő parancsot egy adott virtuális gép bővítmények központi telepítési állapotának megtekintéséhez.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A cél virtuális gépen a következő könyvtárban található fájlok bővítmény végrehajtás kimenetének van bejelentkezve.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A megadott fájlok letöltése a cél virtuális gépen a következő könyvtárba.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
Ha `<n>` decimális egész szám, amely módosíthatja a bővítmény végrehajtások közötti.  A `1.*` értéke megegyezik a tényleges, jelenlegi `typeHandlerVersion` érték a kiterjesztést.  A tényleges directory lehet például `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Végrehajtásakor a `commandToExecute` parancs, a bővítmény fogja beállítani a könyvtár (pl. `...\Downloads\2`), az aktuális munkakönyvtárban. Ez lehetővé teszi, hogy keresse meg a letöltött fájlokat relatív elérési utak használatát a `fileURIs` tulajdonság. Lásd az alábbi táblázatban a példákat.

Mivel a abszolút letöltési mappa elérési útját idővel változhatnak, ajánlott, hogy a fájl relatív parancsfájl elérési utak a a `commandToExecute` karakterlánc, amikor csak lehetséges. Példa:
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

Útvonal-információinak után az első URI-szegmens őrzi meg a letöltött fájlok a `fileUris` tulajdonságlistát.  Ahogy az alábbi táblázatban is látható, letöltött fájlok letöltési alkönyvtárak szerkezete megfelelően be van leképezve a `fileUris` értékeket.  

#### <a name="examples-of-downloaded-files"></a>A letöltött fájlok példák

| URI-azonosító található fileUris | A letöltött helyzetéhez viszonyítva | Abszolút helye letöltött * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\*Mint a fenti abszolút elérési útjainak fogja módosítani a virtuális gép, de nem esik a CustomScript bővítménnyel egyetlen végrehajtásának életciklusa alatt.

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon a [MSDN Azure és a Stack Overflow fórumokban] Azure szakértők (https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).
