---
title: "Célállapotkonfiguráció Azure áttekintés |} Microsoft Docs"
description: "A Microsoft Azure-kiterjesztés kezelője segítségével a PowerShell célállapot-konfiguráció áttekintése. Többek között az Előfeltételek, architektúra, parancsmagok..."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
ms.openlocfilehash: c05c2d541a5f526f362f9cd72fe6d878374112b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure célállapot-konfiguráció kiterjesztés kezelőjének bemutatása
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure Virtuálisgép-ügynök és a kapcsolódó bővítmények a Microsoft Azure infrastruktúra-szolgáltatásokat a részét képezik. Virtuálisgép-bővítmények olyan szoftverösszetevők, amelyek a virtuális gép bővíthetők, és egyszerűbbé tehető a virtuális gép különböző felügyeleti műveleteket. Például a VMAccess bővítmény segítségével a rendszergazda jelszavát, vagy az egyéni parancsprogramok futtatására szolgáló bővítmény használható parancsfájl végrehajtása a virtuális Gépen.

Ez a cikk a PowerShell kívánt állapot konfigurációs szolgáltatása (DSC) bővítmény be Azure virtuális gépek az Azure PowerShell SDK részeként. Új parancsmagok segítségével töltse fel, és a PowerShell DSC-konfiguráció alkalmazása egy Azure virtuális gépen engedélyezve van a PowerShell DSC-bővítménnyel. A PowerShell DSC bővítmény hívások a PowerShell DSC kihirdeti a fogadott DSC-konfiguráció a virtuális Gépen. Ez a funkció az Azure portálon keresztül is érhető el.

## <a name="prerequisites"></a>Előfeltételek
**Helyi számítógép** kommunikál az Azure Virtuálisgép-bővítmény, az Azure-portálon vagy az Azure PowerShell SDK használatával szüksége. 

**A Vendégügynök** az Azure virtuális gép a DSC-konfiguráció által konfigurált kell lennie, amely támogatja a Windows Management Framework (WMF) 4.0-s vagy 5.0 operációs. A támogatott operációsrendszer-verziók teljes listája megtalálható a [DSC-bővítmény verzióelőzmények](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Kifejezések és fogalmak
Ez az útmutató a következő fogalmakat ismeretét feltételezi:

Konfiguráció - DSC konfigurációs dokumentum. 

Csomópont - céljának DSC-konfiguráció. A jelen dokumentum "csomópont" mindig hivatkozik egy Azure virtuális Gépen.

Konfigurációs adatok - egy .psd1 fájlt a környezeti adatokat tartalmazó konfiguráció

## <a name="architectural-overview"></a>Az architektúra áttekintése
Az Azure DSC-bővítmény az Azure Virtuálisgép-ügynök keretrendszer használatával kézbesíti, kihirdeti, és a jelentés az Azure virtuális gépeken futó DSC-konfigurációk. A DSC-bővítményt vár legalább egy konfigurációs dokumentum, és az Azure PowerShell SDK vagy az Azure-portálon keresztül megadott paraméterek tartalmazó .zip fájlt.

A bővítmény hívásakor először a telepítési folyamat futása. Ez a folyamat egy olyan verzióját, a Windows Management Framework (WMF) használatával a következő programot telepíti:

1. Ha az Azure virtuális gép operációs rendszere Windows Server 2016-os, semmilyen művelet nem lesz végrehajtva. Windows Server 2016 már telepített PowerShell legújabb verzióját.
2. Ha a `wmfVersion` tulajdonság van megadva, a WMF adott verziója van telepítve, kivéve, ha a virtuális gép operációs rendszere kompatibilis.
3. Ha nincs `wmfVersion` tulajdonság van megadva, a WMF legújabb megfelelő verziója telepítve van.

A WMF telepítése újraindítást igényel. Az újraindítást követően a bővítmény megadott .zip fájl letölti a `modulesUrl` tulajdonság. Ha ezen a helyen az Azure blob Storage tárolóban, SAS-tokennel is megadhatók a `sasToken` tulajdonság a fájl elérésére. A .zip letöltése és kicsomagolása, után a konfigurációs függvény meghatározott `configurationFunction` fut a MOF-fájl létrehozásához. A bővítmény majd futtatja az `Start-DscConfiguration -Force` meg a létrehozott MOF-fájlt. A bővítmény kimeneti, mind az írás vissza azt az Azure állapot csatorna rögzíti. A DSC LCM ettől kezdve, kezeli a figyelés és a szokásos módon javítása. 

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
PowerShell-parancsmagok segítségével az Azure Resource Manager vagy a klasszikus üzembe helyezési modellel csomag közzététele és DSC-bővítmény telepítésének figyelése. A következő táblázatban szereplő parancsmagok a klasszikus üzembe helyezési modulok, de "Azure" lecserélhető a "AzureRm" az Azure Resource Manager modellt használja. Például `Publish-AzureVMDscConfiguration` a klasszikus üzembe helyezési modellt használ, ahol `Publish-AzureRmVMDscConfiguration` Azure Resource Managert használja. 

`Publish-AzureVMDscConfiguration`időt vesz igénybe, a konfigurációs fájlban, keres a DSC tőle függő erőforrások és a konfiguráció és a konfigurációs kihirdeti szükséges DSC erőforrásokat tartalmazó .zip fájlt hoz létre. A csomag helyileg is létrehozhat a `-ConfigurationArchivePath` paraméter. Ellenkező esetben a .zip-fájlt az Azure blob storage közzéteszi, és biztosítja a SAS-tokennel rendelkező.

A zip-fájlt, a parancsmag által létrehozott a .ps1 konfigurációs parancsfájl az archív mappa gyökerében van. Erőforrások, hogy a modul mappát az archív mappába helyezi. 

`Set-AzureVMDscExtension`a beállításokat, amelyek használatával a PowerShell DSC-bővítményt a Virtuálisgép-konfigurációs objektum esetében. A klasszikus üzembe helyezési modellel, a virtuális gép alkalmazni kell a változtatásokat egy Azure virtuális gépre a `Update-AzureVM`. 

`Get-AzureVMDscExtension`Lekéri egy adott virtuális gép DSC-bővítmény állapotának. 

`Get-AzureVMDscExtensionStatus`a DSC-konfiguráció a DSC-bővítmény kezelő helyeztek állapotát olvassa be. Ez a művelet végrehajtható egyetlen virtuális gép vagy csoportján.

`Remove-AzureVMDscExtension`a bővítmény kezelő eltávolítja a megadott virtuális géppel. Ez a parancsmag does **nem** távolítsa el a konfigurációt, távolítsa el a WMF, vagy módosítsa a virtuális gépen a alkalmazott beállításokat. Csak a bővítmény kezelő távolítja el. 

**A címterület-kezelés és az Azure Resource Manager parancsmagok főbb változásai**

* Az Azure Resource Manager parancsmagjainak szinkronizáltak. Címterület-kezelési parancsmagok aszinkron jellegűek.
* Erőforráscsoport-név, a VMName, a ArchiveStorageAccountName, a verziót és a hely összes kötelező paraméterek az Azure Resource Manager tartoznak.
* ArchiveResourceGroupName egy új Azure Resource Manager nem kötelező paraméter. Ez a paraméter is megadhat, ha a tárfiók tartozik egy másik erőforráscsoportban található, mint ahol a virtuális gép létrehozása.
* ConfigurationArchive nevezik ArchiveBlobName az Azure Resource Manager
* ContainerName nevezik ArchiveContainerName az Azure Resource Manager
* StorageEndpointSuffix nevezik ArchiveStorageEndpointSuffix az Azure Resource Manager
* Az automatikus frissítési kapcsoló vált elérhetővé, az Azure Resource Manager a kiterjesztés kezelője a legfrissebb verzióra, és a rendelkezésre álló automatikus frissítésének engedélyezése. Megjegyzés: Ez a paraméter van okozhat újraindítja a virtuális Gépre, a WMF egy új verzióinak kiadásakor. 

## <a name="azure-portal-functionality"></a>Az Azure portál funkció
Tallózással keresse meg a virtuális gépek. A beállítások -> Általános kattintson "Kiterjesztésekkel." Egy új ablak jön létre. Kattintson a "Hozzáadás" gombra, és válassza ki a PowerShell DSC.

A portál van szüksége.
**Modulok vagy parancsfájl**: A mező kitöltése kötelező. Egy konfigurációs parancsfájl tartalmazó .ps1 fájlt, vagy a legfelső szintű és a tőle függő összes erőforrásról, a modul mappákban belül a .zip .ps1 konfigurációs parancsfájl .zip fájl szükséges. A létrehozás a `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` parancsmag az Azure PowerShell SDK tartalmazza. A zip-fájlt a SAS-jogkivonat által védett felhasználói blob-tároló van töltve. 

**Konfigurációs adatok psd1 kiterjesztésű fájl**: Ez a mező nem kötelező megadni. Ha a konfiguráció szükséges .psd1 a konfigurációs adatfájlt, használja ebben a mezőben jelölje ki, majd töltse fel a felhasználó blob-tároló, a SAS-jogkivonat által védett ahol. 

**Konfiguráció neve modulhoz minősített**: .ps1 fájlok rendelkezhet több konfigurációs funkciók. Adja meg a konfigurációs .ps1 parancsfájl követ egy "\' és a konfigurációs függvény neve. Például ha a .ps1 parancsfájl "configuration.ps1" nevet, és a konfiguráció "IisInstall", írja be:`configuration.ps1\IisInstall`

**Konfigurációs argumentumok**: Ha a konfigurációs függvény argumentummal, adja meg őket itt formátumú `argumentName1=value1,argumentName2=value2`. Megjegyzés: ezt a formátumot más formátumú, mint hogyan konfigurációs argumentumot fogad PowerShell-parancsmagok és a Resource Manager-sablonok használatával. 

## <a name="getting-started"></a>Bevezetés
Az Azure DSC-bővítmény veszi a DSC-konfiguráció dokumentumok és ír elő őket az Azure virtuális gépeken. A következő konfiguráció egy egyszerű példa. Mentse helyileg "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Helyezze a IisInstall.ps1 parancsfájl a megadott virtuális gép, végrehajtani a konfigurációs és jelentéseket küldhetnek vissza állapota az alábbi lépéseket.
###<a name="classic-model"></a>Klasszikus modell
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Az Azure Resource Manager modellt

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Naplózás
Naplók kerülnek:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[verziószám]

## <a name="next-steps"></a>Következő lépések
További információ a PowerShell DSC [látogasson el a PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview). 

Vizsgálja meg a [Azure Resource Manager sablon a DSC-bővítmény](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

A PowerShell DSC kezelhető további funkciók kereséséhez [keresse meg a PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) további DSC-erőforrásokat.

Bizalmas paraméterek átadása a konfigurációk a részletekért lásd: [biztonságosan a DSC-bővítmény kezelő a hitelesítő adatok kezelése](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

