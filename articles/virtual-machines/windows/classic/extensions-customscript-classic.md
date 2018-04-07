---
title: A Windows virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény |} Microsoft Docs
description: Azure virtuális gép konfigurációs feladatok automatizálása a PowerShell-parancsfájlok futtatása egy távoli Windows virtuális gépre az egyéni parancsprogramok futtatására szolgáló bővítmény használatával
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: danis
ms.openlocfilehash: 0ed73a53e6ec40af888f12cbf8f002a62098cda5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Egyéni parancsfájl kiterjesztése a Windows a klasszikus üzembe helyezési modellel

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](../extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájl az Azure virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Ez a dokumentum részletesen az egyéni parancsprogramok futtatására szolgáló bővítmény használatával az Azure PowerShell modul, Azure Resource Manager-sablonok és a Windows rendszer hibaelhárítási részletek használata.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az egyéni parancsprogramok futtatására szolgáló bővítmény a Windows is futtathatók a Windows Server 2008 R2, 2012, 2012 R2 és 2016 kiadását.

### <a name="script-location"></a>Parancsfájl helyét

A parancsfájl kell az Azure storage vagy bármely más egy érvényes URL-cím elérhető helyen kell tárolni.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az egyéni parancsfájl kiterjesztése a Windows megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>A séma kiterjesztése

A következő JSON a séma az egyéni parancsprogramok futtatására szolgáló bővítmény jelennek meg. A bővítmény (Azure Storage vagy más helyre érvényes URL-CÍMMEL rendelkező) a parancsfájl helyét, valamint végrehajtandó parancsot kell rendelkeznie. Ha a parancsprogram forráskódjának Azure Storage használ, az Azure storage fiók név és fiókkulcs kulcsot meg kell adni. Legyen bizalmas adatokat a rendszer ezeket az elemeket, és a bővítmények védett beállítás konfigurációjában megadott. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen.

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| Bővítmény | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (például) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (például) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása zene-app.ps1 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Itt található, amely tartalmazza az egyéni parancsprogramok futtatására szolgáló bővítmény mintasablon [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureVMCustomScriptExtension` parancs használható az egyéni parancsprogramok futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure PowerShell modul segítségével. A következő parancsot egy adott virtuális gép bővítmények központi telepítési állapotának megtekintéséhez.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtási kimeneti nekünk a cél virtuális gépen a következő könyvtárban található fájlokat a rendszer naplózza.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

A parancsfájl magát a cél virtuális gépen a következő könyvtárba lesznek letöltve.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).