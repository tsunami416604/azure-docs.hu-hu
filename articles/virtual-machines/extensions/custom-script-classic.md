---
title: Egyéni szkriptek futtatására szolgáló bővítmény egy Windows virtuális gép |} A Microsoft Docs
description: Azure-beli Virtuálisgép-konfigurációs feladatok automatizálása a PowerShell-parancsfájlok futtatása egy távoli Windows virtuális gépen az egyéni szkriptbővítmény használatával
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: 1331eb311ca629253b4c4c28c9326e290887a752
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058703"
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Egyéni parancsfájl kiterjesztése a Windows a klasszikus üzemi modell használatával

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](custom-script-windows.md)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../includes/virtual-machines-classic-portal.md)]

Az egyéni Szkriptbővítmény letölti és végrehajtja a parancsfájlok Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ból vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI, a PowerShell, az Azure Portal vagy az Azure Virtual Machine REST API használatával is futtatható.

Ez a dokumentum részletesen használata az egyéni Szkriptbővítmény használatával, az Azure PowerShell-modul, az Azure Resource Manager-sablonok és hibaelhárítási lépéseket a Windows rendszerek részleteit.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az egyéni Szkriptbővítmény for Windows is futtatható a Windows Server 2008 R2, 2012, 2012 R2 és 2016-kiadások.

### <a name="script-location"></a>A parancsprogram helye

A parancsfájl kell az Azure storage, vagy egy érvényes URL-cím-n keresztül elérhető más helyen kell tárolni.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Custom Script bővítmény a Windows megköveteli, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON-ra a séma az egyéni Szkriptbővítmény mutatja be. A bővítmény telepítéséhez, egy parancsfájl helye (Azure Storage vagy más helyre érvényes URL-címmel), és a egy parancs végrehajtása. Az Azure Storage használata a parancsfájl forrásként, ha egy Azure storage-fiók nevét és kulcs megadása kötelező. Ezeket az elemeket kell kezelni, mint a bizalmas adatok és a bővítmények védett beállítás konfigurációjában megadott. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen.

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| Bővítmény | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (például:) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (például:) | PowerShell - ExecutionPolicy Unrestricted - fájl konfigurálása music-app.ps1 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémájában az egyéni szkriptek futtatására szolgáló bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható. A mintasablon, amely tartalmazza az egyéni szkriptek bővítménye, itt található [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzureVMCustomScriptExtension` parancs használható az egyéni szkriptek futtatására szolgáló bővítmény hozzáadása egy meglévő virtuális gépet. További információkért lásd: [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmcustomscriptextension).

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

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtása kimeneti velünk a kapcsolatot a cél virtuális gépen a következő könyvtárban található fájlok naplózza.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Magát a parancsfájlt a rendszer letölti a cél virtuális gépen a következő könyvtárba.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
