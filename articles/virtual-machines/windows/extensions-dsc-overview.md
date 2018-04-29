---
title: Állapotkonfiguráció szükséges Azure áttekintés |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a Microsoft Azure-kiterjesztés kezelője a PowerShell szükséges konfiguráló (DSC). A cikk Előfeltételek, architektúrájának és parancsmagokat tartalmazza.
services: virtual-machines-windows
documentationcenter: ''
author: mgreenegit
manager: jeconnoc
editor: ''
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
ms.openlocfilehash: b6bfe48df685952d2b465d9549e2f1c086c1c490
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure célállapot-konfiguráció kiterjesztés kezelőjének bemutatása

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure Virtuálisgép-ügynök és a kapcsolódó bővítmények a Microsoft Azure infrastruktúra-szolgáltatások részét képezik. Virtuálisgép-bővítmények olyan szoftverösszetevők, amelyek VM bővíthetők, és egyszerűbbé tehető a virtuális gép különböző felügyeleti műveleteket.

Az elsődleges-és nagybetűhasználattal a Azure kívánt állapot konfigurációs szolgáltatása (DSC) bővítményt a virtuális gépek rendszerindításának érték a [Azure Automation DSC szolgáltatás](../../automation/automation-dsc-overview.md). Biztosít egy virtuális gép rendszerindítása [előnyöket](https://docs.microsoft.com/powershell/dsc/metaconfig#pull-service) , amelyek tartalmazzák a Virtuálisgép-konfiguráció és egyéb működési eszközök – például a figyelés Azure integráció folyamatos kezelését.

A DSC-bővítményt az Automation DSC szolgáltatás függetlenül is használhatja. Azonban ez magában foglalja a központi telepítése során egy szinguláris műveleteket. Nincs folyamatban lévő reporting vagy konfigurációkezelés áll rendelkezésre, nem helyileg a virtuális gép.

Ez a cikk mindkét forgatókönyvet ismerteti: Automation bevezetése a DSC-bővítményt használja, és használja a DSC-bővítményt eszközként konfigurációk hozzárendelése a virtuális gépek az Azure SDK segítségével.

## <a name="prerequisites"></a>Előfeltételek

- **Helyi számítógép**: kommunikál az Azure Virtuálisgép-bővítmény, az Azure-portálon vagy az Azure PowerShell SDK-t kell használnia.
- **A Vendégügynök**: az Azure virtuális gép a DSC-konfiguráció által konfigurált, amely támogatja a Windows Management Framework (WMF) 4.0-s vagy újabb operációs kell lennie. A támogatott operációsrendszer-verziók teljes listájáért lásd: a [DSC-bővítmény verzióelőzmények](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Kifejezések és fogalmak

Ez az útmutató a következő fogalmakat ismeretét feltételezi:

- **Konfigurációs**: A DSC-konfiguráció dokumentum.
- **Csomópont**: egy cél DSC-konfiguráció. Ebben a dokumentumban *csomópont* mindig egy Azure virtuális gép hivatkozik.
- **Konfigurációs adatok**: konfiguráció környezeti adatok .psd1 fájlt.

## <a name="architecture"></a>Architektúra

Az Azure DSC-bővítmény az Azure Virtuálisgép-ügynök keretrendszer használatával kézbesíti, kihirdeti, és a jelentés az Azure virtuális gépeken futó DSC-konfigurációk. A DSC-bővítményt egy konfigurációs dokumentum és a paraméterek fogadja el. Ha nincs fájl áll rendelkezésre, egy [konfigurációs parancsfájl alapértelmezett](#default-configuration-script) kiterjesztésű van beágyazva. Az alapértelmezett konfigurációs parancsfájl használatával csak metaadat beállított [helyi Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig).

A bővítmény hívásakor először a következő logika használatával telepíti a WMF verziója:

* Ha az Azure virtuális gép operációs rendszere Windows Server 2016-os, semmilyen művelet nem lesz végrehajtva. Windows Server 2016 már telepített PowerShell legújabb verzióját.
* Ha a **wmfVersion** tulajdonság van megadva, a WMF verzió telepítve van, kivéve, ha nem, hogy a virtuális gép operációs rendszere kompatibilis.
* Ha nincs **wmfVersion** tulajdonság van megadva, a WMF legújabb megfelelő verziója telepítve van.

A WMF telepítése újraindítást igényel. Az újraindítás után a bővítmény a .zip-fájlban megadott letölti a **modulesUrl** tulajdonság, ha a megadott. Ha ezen a helyen az Azure Blob Storage tárolóban, megadhat egy SAS-jogkivonat a a **sasToken** tulajdonság a fájl elérésére. A .zip letöltése és kicsomagolása, után a konfigurációs függvény meghatározott **configurationFunction** felveszi .mof-fájl létrehozása. A bővítmény majd futtatja az **Start-DscConfiguration-Force** a generált .mof fájl használatával. A bővítmény kimeneti rögzíti, és az Azure állapot csatorna írja azt.

### <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az Azure DSC-bővítményt tartalmaz egy alapértelmezett konfigurációs parancsfájlt, amely rendelkezik tekinthető használni bevezetésében, egy virtuális Gépet az Azure Automation DSC szolgáltatásra. A parancsprogram paramétereinek összhangban legyenek a konfigurálható tulajdonságainak [helyi Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig). A parancsfájl paramétereit, lásd: [konfigurációs parancsfájl alapértelmezett](extensions-dsc-template.md#default-configuration-script) a [célállapot-konfiguráció bővítmény Azure Resource Manager-sablonok](extensions-dsc-template.md). A teljes parancsfájl, tekintse meg a [GitHub-sablon Azure gyors üzembe helyezés](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>A DSC-bővítményt a Resource Manager-sablonok

A legtöbb esetben a Resource Manager központi telepítési sablonok a várt módon használható a DSC-bővítményt. További információt és példákat a DSC-bővítményt tartalmazza a Resource Manager központi telepítési sablonok [célállapot-konfiguráció bővítmény Azure Resource Manager-sablonok](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>A DSC-bővítmény PowerShell-parancsmagok

A PowerShell-parancsmagok használatával kezelheti a DSC-bővítményt az interaktív hibaelhárítási és információgyűjtési forgatókönyvek legérdemesebb használni. A parancsmagok segítségével csomag közzététele és DSC-bővítmény telepítésének figyelése. Vegye figyelembe, hogy a DSC-bővítmény parancsmagok még nem frissített történő együttműködésre a [konfigurációs parancsfájl alapértelmezett](#default-configuration-script).

A **Publish-AzureRMVMDscConfiguration** parancsmag időt vesz igénybe, a konfigurációs fájlban, keres a DSC tőle függő erőforrások, és létrehoz egy .zip fájlt. A zip-fájlt a konfigurációs és a DSC-erőforrások, amelyek szükségesek ahhoz, hogy a konfigurációs kihirdeti tartalmazza. A parancsmag használatával helyben is létrehozhat a csomag a *- OutputArchivePath* paraméter. Ellenkező esetben a parancsmag közzéteszi a blob-tároló zip-fájlt, és majd biztonságossá tételére egy SAS-tokennel rendelkező.

A .ps1 konfigurációs parancsfájl, amely a parancsmag létrehozza a .zip-fájlt az archív mappa gyökerében van. A modul mappába kerül erőforrások archív mappájában.

A **Set-AzureRMVMDscExtension** parancsmag esetében a PowerShell DSC-bővítményhez olyan beállításokat egy virtuális gép konfigurációs objektumban.

A **Get-AzureRMVMDscExtension** parancsmag beolvassa a DSC-bővítmény állapotát egy adott virtuális gép.

A **Get-AzureRMVMDscExtensionStatus** parancsmag beolvassa a DSC-konfiguráció, amely a DSC-bővítmény kezelő végrehajtása állapotát. Ez a művelet egy virtuális vagy virtuális gépek egy csoportján hajtható végre.

A **Remove-AzureRMVMDscExtension** parancsmag eltávolítja a bővítmény kezelő egy adott virtuális géptől. Ez a parancsmag does *nem* távolítsa el a konfigurációt, távolítsa el a WMF, vagy módosítsa a virtuális Gépen a alkalmazott beállításokat. Csak a bővítmény kezelő távolítja el. 

Erőforrás-kezelő DSC-bővítmény parancsmagok fontos információkat:

- Az Azure Resource Manager parancsmagjainak szinkronizáltak.
- A *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *verzió*, és *hely*paraméterek is kötelező megadni.
- *ArchiveResourceGroupName* egy nem kötelező paraméter. Ez a paraméter is megadhat, ha a tárfiók tartozik egy másik erőforráscsoportban található, mint ahol a virtuális gép létrehozása.
- Használja a **AutoUpdate** kapcsolót, hogy a kiterjesztés kezelő automatikus frissítése a legújabb verzióra a rendelkezésre álló. Vegye figyelembe, hogy ez a paraméter rendelkezik-e újraindítást okozhat a virtuális Gépre, ha egy új verziója WMF szabadul fel.

### <a name="get-started-with-cmdlets"></a>Ismerkedés a parancsmagok

Az Azure DSC-bővítményt a DSC-konfiguráció dokumentumok segítségével közvetlenül konfigurálása az Azure virtuális gépek telepítése során. Ezt a lépést nem regisztrálja az Automation csomópont. A csomópont *nem* központilag felügyelt.

Az alábbi példában egy egyszerű példa konfiguráció látható. Mentse helyileg a konfigurációs IisInstall.ps1.

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

Az alábbi parancsokat a IisInstall.ps1 parancsfájl a megadott virtuális gép helyezze el. A parancsok is végrehajtani a konfigurációs, és jelentést készít a biztonsági állapotát.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Az Azure portál funkció

A portál beállítása DSC:

1. Ugrás a virtuális gép. 
2. A **beállítások** > **általános**, jelölje be **bővítmények**. 
3. A létrehozott új ablaktábla, válassza a **Hozzáadás**, majd válassza ki **PowerShell DSC**.

A portál a következő beavatkozást igényel:

* **Modulok vagy parancsfájl**: Ez a mező megadása kötelező (az űrlap nem lett frissítve a [konfigurációs parancsfájl alapértelmezett](#default-configuration-script)). Konfigurációs modulok és a parancsfájlokat egy konfigurációs parancsfájl .ps1 fájlt vagy a meghajtó gyökérkönyvtárát .ps1 konfigurációs parancsfájl .zip fájl szükséges. Egy .zip fájl használatakor tőle függő összes erőforrásról szerepelnie kell a .zip modul mappákat. A .zip fájl segítségével létrehozható a **Publish-AzureVMDscConfiguration - OutputArchivePath** parancsmag, amely az Azure PowerShell SDK megtalálható. A zip-fájlt a felhasználó blobtárolóba feltöltött, és egy SAS-jogkivonat által védett.

* **Konfigurációs adatok psd1 kiterjesztésű fájl**: Ez a mező nem kötelező megadni. Ha a konfiguráció szükséges .psd1 a konfigurációs adatfájlt, használja a ebben a mezőben jelölje ki az adatok mezőt, és töltse fel azt a felhasználói blob-tároló. A konfigurációs adatfájlt védi-e egy SAS-jogkivonat a blob Storage tárolóban.

* **Konfiguráció neve modulhoz minősített**: .ps1 fájl több konfigurációs funkciók is felvehet. Adja meg a konfigurációs .ps1 parancsfájl követ \\ és a konfigurációs függvény neve. Például, ha a .ps1 parancsfájlt a név configuration.ps1 és a konfigurációs van **IisInstall**, adja meg **configuration.ps1\IisInstall**.

* **Konfigurációs argumentumok**: Ha a konfigurációs függvény argumentummal, adja meg őket itt formátumban **argumentName1 = érték1, argumentName2 = érték2**. Ebben a formátumban, amelyben konfigurációs argumentumokat fogadják el és PowerShell-parancsmagok és a Resource Manager-sablonok más formátumú.

## <a name="logs"></a>Logs
Hely naplózza a következő helyen:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>További lépések
* A PowerShell DSC kapcsolatos további információkért lásd a [PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview). 
* Vizsgálja meg a [Resource Manager-sablon a DSC-bővítmény](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* A PowerShell DSC használatával felügyelhető további funkciókat, és további DSC-erőforrásokat, keresse meg a [PowerShell-galériában](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* További információk a konfigurációk a bizalmas paraméterek átadása: [biztonságosan a DSC-bővítmény kezelő a hitelesítő adatok kezelése](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

