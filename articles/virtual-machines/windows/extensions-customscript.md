---
title: A Windows Azure egyéni parancsprogramok futtatására szolgáló bővítmény |} Microsoft Docs
description: Az egyéni parancsprogramok futtatására szolgáló bővítmény használatával Windows virtuális gép konfigurációs feladatok automatizálásához
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: 3a979df6148ae396cf5d9a34dc5f2eb0d455b736
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="custom-script-extension-for-windows"></a>A Windows egyéni parancsprogramok futtatására szolgáló bővítmény

Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájl az Azure virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Ez a dokumentum részletesen az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az Azure PowerShell modul, Azure Resource Manager-sablonok és a Windows rendszer hibaelhárítási részletek használata.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]  
> Nem használhatja egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához frissítés-AzureRmVM operátormetódusának a paraméterével ugyanazon VM óta, várjon a saját magát.  
>   
> 

### <a name="operating-system"></a>Operációs rendszer

Az egyéni parancsfájl kiterjesztése a Windows futtatható elleni ügyfél a Windows 10, Windows Server 2008 R2, 2012, 2012 R2 és 2016 feloldja a.

### <a name="script-location"></a>Parancsfájl helyét

A parancsfájl kell Azure Blob Storage tárolóban, vagy egy érvényes URL-cím elérhető bármely más helyen kell tárolni.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az egyéni parancsfájl kiterjesztése a Windows megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

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

| Name (Név) | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| type | Bővítmények |
| typeHandlerVersion | 1.9 |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (például) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása zene-app.ps1 |
| storageAccountName (például) | examplestorageacct |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Megjegyzés:** -e a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. -Neveket használja, a fent látható telepítési problémák elkerülése érdekében.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Itt található, amely tartalmazza az egyéni parancsprogramok futtatására szolgáló bővítmény mintasablon [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMCustomScriptExtension` parancs használható az egyéni parancsprogramok futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
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
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Útvonal-információinak után az első URI-szegmens őrzi meg a letöltött fájlok a `fileUris` tulajdonságlistát.  Ahogy az alábbi táblázatban is látható, letöltött fájlok letöltési alkönyvtárak szerkezete megfelelően be van leképezve a `fileUris` értékeket.  

#### <a name="examples-of-downloaded-files"></a>A letöltött fájlok példák

| URI-azonosító található fileUris | A letöltött helyzetéhez viszonyítva | Abszolút helye letöltött * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Mint a fenti abszolút elérési útjainak fogja módosítani a virtuális gép, de nem esik a CustomScript bővítménnyel egyetlen végrehajtásának életciklusa alatt.

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
