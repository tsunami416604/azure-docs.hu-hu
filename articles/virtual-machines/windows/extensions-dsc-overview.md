---
title: "Célállapotkonfiguráció Azure áttekintés |} Microsoft Docs"
description: "A Microsoft Azure-kiterjesztés kezelője segítségével a PowerShell célállapot-konfiguráció áttekintése. Többek között az Előfeltételek, architektúra, parancsmagok..."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure célállapot-konfiguráció kiterjesztés kezelőjének bemutatása

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure Virtuálisgép-ügynök és a kapcsolódó bővítmények a Microsoft Azure infrastruktúra-szolgáltatásokat a részét képezik.
Virtuálisgép-bővítmények olyan szoftverösszetevők, amelyek a virtuális gép bővíthetők, és egyszerűbbé tehető a virtuális gép különböző felügyeleti műveleteket.

Az elsődleges-és nagybetűhasználattal rendszerindításának egy virtuális Gépet nem a kívánt állapot konfigurációs kiterjesztése a [Azure Automation DSC szolgáltatás](../../automation/automation-dsc-overview.md) biztosít [előnyöket](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service) beleértve a folyamatos felügyeletét, a Virtuálisgép-konfiguráció és egyéb működési eszközök – például a figyelés Azure való integráció.

Is lehetőség a DSC-bővítményt, az Azure Automation DSC szolgáltatástól függetlenül, azonban a telepítés során előforduló szinguláris művelet, és nincs folyamatban lévő reporting vagy helyileg a virtuális Gépen belül a nem konfigurációkezeléshez.

Ez a cikk forgatókönyveket, a bevezetési Azure Automation DSC-bővítményt és hogyan használható a DSC-bővítményt eszközként konfigurációk hozzárendelése az Azure SDK-t használó virtuális gépek adatait tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

- **Helyi számítógép** - kommunikál az Azure Virtuálisgép-bővítmény, az Azure-portálon vagy az Azure PowerShell SDK használatával kell.
- **A Vendégügynök** – az Azure virtuális gép a DSC-konfiguráció által konfigurált kell lennie, amely támogatja a Windows Management Framework (WMF) 4.0-s vagy újabb operációs. Támogatott operációsrendszer-verziók teljes listája megtalálható az oldal [DSC-bővítmény verzióelőzmények](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Kifejezések és fogalmak

Ez az útmutató a következő fogalmakat ismeretét feltételezi:

- **Konfigurációs** -A DSC-konfiguráció dokumentum.
- **Csomópont** -céljának DSC-konfiguráció. A jelen dokumentum "csomópont" mindig hivatkozik egy Azure virtuális Gépen.
- **Konfigurációs adatok** - konfiguráció környezeti adatokat tartalmazó .psd1 fájlt.

## <a name="architectural-overview"></a>Az architektúra áttekintése

Az Azure DSC-bővítmény az Azure Virtuálisgép-ügynök keretrendszer használatával kézbesíti, kihirdeti, és a jelentés az Azure virtuális gépeken futó DSC-konfigurációk.
A DSC-bővítményt egy konfigurációs dokumentum és a paraméterek fogadja el.
Ha nincs fájl áll rendelkezésre, egy [konfigurációs parancsfájl alapértelmezett](###default-configuration-script) csak a metaadat-ban történő beállítását használt kiterjesztésű beágyazott [helyi Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

A bővítmény hívásakor először egy verziója, a Windows Management Framework (WMF) használatával a következő programot telepíti:

1. Ha az Azure virtuális gép operációs rendszere Windows Server 2016-os, semmilyen művelet nem lesz végrehajtva. Windows Server 2016 már telepített PowerShell legújabb verzióját.
2. Ha a `wmfVersion` tulajdonság van megadva, a WMF adott verziója van telepítve, kivéve, ha a virtuális gép operációs rendszere kompatibilis.
3. Ha nincs `wmfVersion` tulajdonság van megadva, a WMF legújabb megfelelő verziója telepítve van.

A WMF telepítése újraindítást igényel.
Az újraindítást követően a bővítmény megadott .zip fájl letölti a `modulesUrl` tulajdonságot, ha a megadott.
Ha ezen a helyen az Azure blob Storage tárolóban, SAS-tokennel is megadhatók a `sasToken` tulajdonság a fájl elérésére.
A .zip letöltése és kicsomagolása, után a konfigurációs függvény meghatározott `configurationFunction` futtatjuk a MOF-fájlt létrehozni.
A bővítmény majd futtatja az `Start-DscConfiguration -Force` a generált MOF-fájlt használ.
A bővítmény kimeneti rögzíti, és az Azure állapot csatorna írja azt.

### <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az Azure DSC-bővítményt tartalmaz egy alapértelmezett konfigurációs parancsfájlt, ha használható a virtuális gép Azure Automation DSC szolgáltatásra.
A parancsprogram paramétereinek összhangban legyenek a konfigurálható tulajdonságainak [helyi Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
A parancsfájl paramétereit, [dokumentált](extensions-dsc-template.md##default-configuration-script) és a teljes parancsfájl érhető el [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Az ARM-sablonok DSC-bővítményt

Az Azure Resource Manager (ARM) a központi telepítési sablonok, amelyek a várt használata a legtöbb esetben a DSC-bővítményt.
Az útmutatást és példákat a DSC-bővítményt ARM központi telepítési sablonok, beleértve a dedikált dokumentációs oldal jelenik meg [kívánt állapot konfigurációs kiterjesztése Azure Resource Manager-sablonok](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>A DSC-bővítmény PowerShell-parancsmagok

DSC-bővítményt kezelése a PowerShell-parancsmagok használata leginkább interaktív hibaelhárítását és információgyűjtés forgatókönyvek.
A parancsmagok segítségével csomag közzététele és DSC-bővítmény telepítésének figyelése.
Vegye figyelembe, hogy parancsmagokat a DSC-bővítményt kell még nem frissített történő együttműködésre a [alapértelmezett konfigurációs parancsfájl](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration`időt vesz igénybe, a konfigurációs fájlban, keres a DSC tőle függő erőforrások és a konfiguráció és a konfigurációs kihirdeti szükséges DSC erőforrásokat tartalmazó .zip fájlt hoz létre.
A csomag helyileg is létrehozhat a `-ConfigurationArchivePath` paraméter.
Ellenkező esetben a .zip-fájlt az Azure blob storage közzéteszi, és biztosítja a SAS-tokennel rendelkező.

A zip-fájlt, a parancsmag által létrehozott a .ps1 konfigurációs parancsfájl az archív mappa gyökerében van.
Erőforrások, hogy a modul mappát az archív mappába helyezi.

`Set-AzureRMVMDscExtension`a beállításokat, amelyek használatával a PowerShell DSC-bővítményt a Virtuálisgép-konfigurációs objektum esetében.

`Get-AzureRMVMDscExtension`Lekéri egy adott virtuális gép DSC-bővítmény állapotának.

`Get-AzureRMVMDscExtensionStatus`a DSC-konfiguráció a DSC-bővítmény kezelő helyeztek állapotát olvassa be.
Ez a művelet végrehajtható egyetlen virtuális gép vagy csoportján.

`Remove-AzureRMVMDscExtension`a bővítmény kezelő eltávolítja a megadott virtuális géppel.
Ez a parancsmag does **nem** távolítsa el a konfigurációt, távolítsa el a WMF, vagy módosítsa a virtuális gépen a alkalmazott beállításokat.
Csak a bővítmény kezelő távolítja el. 

A DSC-bővítményt AzureRM-parancsmagokkal kapcsolatos fontos információkat:

- Az Azure Resource Manager parancsmagjainak szinkronizáltak.
- Erőforráscsoport-név, a VMName, a ArchiveStorageAccountName, a verziót és a hely összes kötelező paraméterek tartoznak.
- ArchiveResourceGroupName megadása nem kötelező paraméter. Ez a paraméter is megadhat, ha a tárfiók tartozik egy másik erőforráscsoportban található, mint ahol a virtuális gép létrehozása.
- Az automatikus frissítési kapcsoló lehetővé teszi, hogy a kiterjesztés kezelője a legfrissebb verzióra, és a rendelkezésre álló automatikus frissítés. Megjegyzés: Ez a paraméter van okozhat újraindítja a virtuális Gépre, a WMF egy új verzióinak kiadásakor.

### <a name="getting-started-with-cmdlets"></a>Ismerkedés a parancsmagok

Az Azure DSC-bővítmény által képes a DSC-konfiguráció dokumentumokat közvetlenül konfigurálására Azure virtuális gépek telepítése során bár ez nem regisztrálja az Azure Automation csomópont, a csomópont lesz **nem*-központi felügyeletét.

A következő konfiguráció egy egyszerű példa.
Mentse helyileg "IisInstall.ps1":

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

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Az Azure portál funkció

Tallózással keresse meg a virtuális gépek. A beállítások -> Általános kattintson "Kiterjesztésekkel."
Egy új ablak jön létre.
Kattintson a "Hozzáadás" gombra, és válassza ki a PowerShell DSC.

A portál van szüksége.
**Modulok vagy parancsfájl**: Ez a mező megadása kötelező (az űrlap nem lett frissítve a [alapértelmezett konfigurációs parancsfájl](###default-configuration-script).
Egy konfigurációs parancsfájl tartalmazó .ps1 fájlt, vagy a legfelső szintű és a tőle függő összes erőforrásról, a modul mappákban belül a .zip .ps1 konfigurációs parancsfájl .zip fájl szükséges.
A létrehozás a `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` parancsmag az Azure PowerShell SDK tartalmazza.
A zip-fájlt a SAS-jogkivonat által védett felhasználói blob-tároló van töltve.

**Konfigurációs adatok psd1 kiterjesztésű fájl**: Ez a mező nem kötelező megadni.
Ha a konfiguráció szükséges .psd1 a konfigurációs adatfájlt, használja ebben a mezőben jelölje ki, majd töltse fel a felhasználó blob-tároló, a SAS-jogkivonat által védett ahol.

**Konfiguráció neve modulhoz minősített**: .ps1 fájlok rendelkezhet több konfigurációs funkciók.
Adja meg a konfigurációs .ps1 parancsfájl követ egy "\' és a konfigurációs függvény neve.
Például ha a .ps1 parancsfájl "configuration.ps1" nevet, és a konfiguráció "IisInstall", írja be:`configuration.ps1\IisInstall`

**Konfigurációs argumentumok**: Ha a konfigurációs függvény argumentummal, adja meg őket itt formátumú `argumentName1=value1,argumentName2=value2`.
Megjegyzés: ezt a formátumot más formátumú, mint hogyan konfigurációs argumentumot fogad PowerShell-parancsmagok és a Resource Manager-sablonok használatával.

## <a name="logging"></a>Naplózás
Naplók kerülnek:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>További lépések
További információ a PowerShell DSC [látogasson el a PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview). 

Vizsgálja meg a [Azure Resource Manager sablon a DSC-bővítmény](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

A PowerShell DSC kezelhető további funkciók kereséséhez [keresse meg a PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) további DSC-erőforrásokat.

Bizalmas paraméterek átadása a konfigurációk a részletekért lásd: [biztonságosan a DSC-bővítmény kezelő a hitelesítő adatok kezelése](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
