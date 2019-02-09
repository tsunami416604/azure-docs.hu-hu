---
title: Az Azure áttekintése a Desired State Configuration
description: Ismerje meg, hogyan használhatja a Microsoft Azure-kiterjesztés kezelő a PowerShell Desired State Configuration (DSC). A cikk Előfeltételek, architektúra és parancsmagokat tartalmaz.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: e5e134fa7dd08bad4220866dd4f5bd9b788e624e
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980601"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure Desired State Configuration bővítmény kezelő bemutatása

Az Azure Virtuálisgép-ügynök és a kapcsolódó bővítmények a Microsoft Azure infrastruktúra-szolgáltatások részét képezik. Virtuálisgép-bővítmények olyan szoftverösszetevők, amelyek a virtuális gép funkciójának bővítésére, és különböző Virtuálisgép-felügyeleti műveletek egyszerűsítését.

Az elsődleges használati eset az Azure Desired State Configuration (DSC) bővítmény elindíthat egy virtuális gép van a [Azure Automation DSC szolgáltatás](../../automation/automation-dsc-overview.md). Biztosít egy virtuális gép rendszerindításra [előnyöket](/powershell/dsc/metaconfig#pull-service) , amely tartalmazza a Virtuálisgép-konfiguráció és egyéb üzemeltetési eszközök, például az Azure Monitoring-integráció folyamatos felügyeletét.

A DSC-bővítmény, az Automation DSC szolgáltatás függetlenül is használhat. Azonban ebbe beletartozik egy egyetlen művelet, amely központi telepítése során megy végbe. Nincs folyamatban lévő jelentésben vagy a konfigurációkezelés nem érhető el, nem helyileg a virtuális gép.

Ez a cikk ismerteti, mindkét forgatókönyv: a DSC-bővítmény használata az Automation bevezetése, és a DSC-bővítmény használata eszközként konfigurációk hozzárendelése a virtuális gépek az Azure SDK-val.

## <a name="prerequisites"></a>Előfeltételek

- **Helyi gépen**: Együttműködik az Azure-beli Virtuálisgép-bővítmény, az Azure Portalon vagy az Azure PowerShell SDK-t kell használnia.
- **A Vendégügynök**: Az Azure virtuális Gépen a DSC-konfiguráció által az operációs rendszer, amely támogatja a Windows Management Framework (WMF) 4.0-s vagy újabb verzióját kell lennie. A támogatott operációs rendszerekről teljes listájáért tekintse meg a [DSC bővítményeinek verzióelőzményei](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Kifejezések és fogalmak

Ez az útmutató a következő fogalmak ismeretét feltételezi:

- **Konfigurációs**: DSC-konfiguráció dokumentum.
- **Csomópont**: A DSC-konfiguráció céljának. Ebben a dokumentumban *csomópont* mindig egy Azure virtuális gép hivatkozik.
- **Konfigurációs adatok**: Egy .psd1 fájlban, amely konfiguráció környezeti adatokat tartalmaz.

## <a name="architecture"></a>Architektúra

Az Azure DSC bővítmény kézbesítéséhez, kihirdeti és a jelentés az Azure virtuális gépeken futó DSC-konfigurációk az Azure VM Agent keretrendszer használatával. A DSC-bővítmény egy konfigurációs dokumentum és a különböző paraméterek fogad el. Nincs fájl áll rendelkezésre, ha egy [alapértelmezett konfigurációs parancsfájl](#default-configuration-script) kiterjesztésű van beágyazva. Az alapértelmezett konfigurációs parancsfájl segítségével csak metaadatok beállítása az [helyi Configuration Manager](/powershell/dsc/metaconfig).

Először meghívásakor a bővítményt, az alábbi logika használatával telepíti a WMF verziója:

- Ha az Azure virtuális gép operációs rendszer Windows Server 2016-ban, nincs művelet nem lesz végrehajtva. A Windows Server 2016-ban már telepítve van a PowerShell legújabb verzióját.
- Ha a **wmfVersion** tulajdonság meg van adva, a WMF verziója van telepítve, kivéve, ha a verzió nem kompatibilis a virtuális gép operációs Rendszeréhez.
- Ha nincs **wmfVersion** tulajdonság meg van adva, a WMF legújabb megfelelő verziója van telepítve.

A WMF telepítése újraindítást igényel. Az újraindítás után a bővítmény a .zip-fájlban megadott letölti a **modulesUrl** tulajdonságot, ha meg van adva. Ha ezen a helyen az Azure Blob storage-ban, egy SAS-tokent a is megadhat a **sasToken** tulajdonság használatával hozzáfér a fájlt. Miután a .zip letöltött és kicsomagolása, a konfiguráció függvény meghatározott **configurationFunction** futtatások .mof-fájl létrehozásához. A bővítményt, majd futtatja `Start-DscConfiguration -Force` a generált .mof fájllal. A bővítmény kimeneti rögzíti, és az Azure állapota csatorna írja azt.

### <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az Azure-DSC-bővítmény tartalmaz egy alapértelmezett konfigurációs parancsfájlt, amely rendelkezik tekinthető használni fogunk előkészíteni az Azure Automation DSC szolgáltatás egy virtuális Gépet. A parancsprogram paramétereinek összhangban legyenek a konfigurálható tulajdonságokról az [helyi Configuration Manager](/powershell/dsc/metaconfig). Szkriptparaméterek, lásd: [alapértelmezett konfigurációs parancsfájl](dsc-template.md#default-configuration-script) a [Desired State Configuration bővítmény az Azure Resource Manager-sablonok](dsc-template.md). A teljes parancsfájl, tekintse meg a [Azure gyorsindítási sablon github](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-bővítmény a Resource Manager-sablonok

A legtöbb esetben üzembe helyezés Resource Manager-sablonok, amelyek a várt a DSC-bővítmény használata. További információ és példák a Resource Manager üzembe helyezési sablonjait közé tartozik a DSC-bővítmény: [Desired State Configuration bővítmény az Azure Resource Manager-sablonok](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>DSC-bővítmény PowerShell-parancsmagok

A PowerShell-parancsmagokkal kezelhetők a DSC-bővítmény legjobb interaktív hibaelhárítási és információgyűjtés forgatókönyvekhez használhatók. A parancsmagok használatával csomag közzététele és DSC-bővítmény telepítésének figyelése. Még a nem frissített parancsmagok a DSC-bővítmény használata a [alapértelmezett konfigurációs parancsfájl](#default-configuration-script).

A **Publish-AzVMDscConfiguration** parancsmag lép a konfigurációs fájlban, keres a függő DSC-erőforrások és majd létrehoz egy .zip fájlt. A .zip-fájlban található, a konfiguráció és a DSC-erőforrásokat, melyek szükségesek ahhoz, hogy a konfiguráció kihirdeti. A parancsmag használatával helyben is létrehozhat a csomagot a *- OutputArchivePath* paraméter. Ellenkező esetben a parancsmag a blob storage-bA .zip-fájlt tesz közzé, és majd védi egy SAS-jogkivonat használatával.

A .ps1 konfigurációs parancsfájl, amely a parancsmag létrehozza a .zip-fájlt az archív mappa gyökerében van. Az archívum mappába, az erőforrások a modul mappába kerül.

A **Set-AzVMDscExtension** parancsmagot a beállítások a PowerShell DSC bővítmény igénylő kódtárba egy virtuális gép konfigurációs objektumban.

A **Get-AzVMDscExtension** parancsmag egy adott VM DSC bővítmény állapotát kérdezi le.

A **Get-AzVMDscExtensionStatus** parancsmag a DSC-konfiguráció, amely a DSC-bővítmény kezelő végrehajtása állapotát kérdezi le. Ez a művelet is elvégezhető, egyetlen virtuális gép vagy a virtuális gépek csoportjai.

A **Remove-AzVMDscExtension** parancsmag eltávolítja a bővítmény-kezelő egy adott virtuális gépről. Ez a parancsmag does *nem* távolítsa el a konfigurációt, a virtuális gépre alkalmazott beállításainak módosítása vagy eltávolítása a WMF. Csak a kiterjesztés kezelő távolítja el. 

Erőforrás-kezelő DSC bővítmény-parancsmagokkal kapcsolatos fontos információkat:

- Az Azure Resource Manager parancsmagjainak szinkronizáltak.
- A *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *verzió*, és *hely*paraméterei az összes szükséges.
- *ArchiveResourceGroupName* egy nem kötelező paraméter. Ezzel a paraméterrel megadhatja, ha a tárfiók tartozik egy másik erőforráscsoportban található, mint ahol a virtuális gép létrehozása.
- Használja a **AutoUpdate** kapcsolót, hogy a kiterjesztés kezelő automatikus frissítése a legújabb verzióra elérhetővé válik. Ez a paraméter okozhat a virtuális gép újraindul, a WMF új verziójának kiadásakor rendelkezik.

### <a name="get-started-with-cmdlets"></a>Parancsmagok használatának első lépései

Az Azure DSC bővítmény DSC konfigurációs dokumentum segítségével konfigurálhatja közvetlenül az Azure virtuális gépek üzembe helyezése során. Ez a lépés nem regisztrálja a csomópontot az Automation szolgáltatásban. A csomópont *nem* központilag felügyelt.

Az alábbi példa bemutatja egy egyszerű példa egy konfigurációját. Mentse helyileg a konfiguráció IisInstall.ps1.

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

Az alábbi parancsokat a IisInstall.ps1 parancsfájl a megadott virtuális gép helyezze el. A parancsok is hajtsa végre a konfigurációt, és jelentést készít vissza a állapot.

```powershell
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate $true -ConfigurationName 'IISInstall'
```

## <a name="azure-portal-functionality"></a>Az Azure portal-funkciók

DSC beállítása a portálon:

1. Nyissa meg egy virtuális Gépet.
2. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet.
3. Válassza ki a létrehozott új lapon, **+ Hozzáadás**, majd válassza ki **PowerShell Desired State Configuration**.
4. Kattintson a **létrehozás** a bővítmény információkat tartalmazó oldal alján.

A portál a következő bemeneti gyűjti:

- **Konfigurációs modulok vagy parancsfájl**: Ez a mező megadása kötelező (az űrlap nem lett frissítve az a [alapértelmezett konfigurációs parancsfájl](#default-configuration-script)). Konfigurációs modulok és a parancsfájlokat egy .ps1 kiterjesztésű fájlba, amely rendelkezik egy konfigurációs parancsfájl vagy egy .zip fájlt .ps1 konfigurációs parancsfájllal gyökerénél igényelnek. Ha egy .zip-fájlt használ, tőle függő összes erőforrásról a modul mappákban lévő a .zip szerepelnie kell. A zip-fájlt a használatával létrehozhat a **Publish-AzureVMDscConfiguration - OutputArchivePath** parancsmag, amely az Azure PowerShell SDK része. A zip-fájlt a felhasználó blobtárolóba feltöltött, és a egy SAS-tokent védi.

- **Konfiguráció modulhoz minősített nevét**: Több konfigurációs funkciók is megadhat a egy .ps1 kiterjesztésű fájlba. Adja meg a konfigurációs .ps1 szkriptben követ nevét \\ és a konfiguráció függvény neve. Például, ha a .ps1 szkriptben a név configuration.ps1 és a konfigurációs van **IisInstall**, adja meg **configuration.ps1\IisInstall**.

- **Konfigurációs argumentumok**: A konfigurációs függvény argumentumokat veszi fel, ha formátumban adja meg azokat itt a **argumentName1 = érték1, argumentName2 = value2**. Ezt a formátumot, amelyben konfigurációs argumentumokat fogadják el és PowerShell-parancsmagokkal vagy a Resource Manager-sablonok más formátumban.

- **Konfigurációs adatok psd1 kiterjesztésű fájl**: A mező kitöltése nem kötelező. A konfigurációs van szüksége a .psd1 konfigurációs adatfájlt, ha a mező használatával válassza ki a data-mezőt, és töltse fel a felhasználó a blob storage. A konfigurációs adatfájlt védi egy SAS-tokent a blob storage-ban.

- **A WMF verzió**: Adja meg a verziót a Windows Management Framework (WMF), amely a virtuális Gépen kell telepíteni. A tulajdonság beállítása a legújabb telepíti a WMF legújabb verzióját. Ez a tulajdonság csak lehetséges értékei jelenleg 4.0-s verzióját, 5.0, 5.1-es, és a legújabb. Ezek lehetséges értékek a következők vonatkoznak a frissítéseket. Az alapértelmezett érték **legújabb**.

- **Az adatgyűjtés**: Azt határozza meg, ha a bővítmény telemetriai adatokat gyűjt. További információkért lásd: [Azure DSC bővítmény adatgyűjtés](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Verzió**: Adja meg a telepítendő DSC bővítmény verziója. Verziókkal kapcsolatos információkért lásd: [DSC bővítményeinek verzióelőzményei](/powershell/dsc/azuredscexthistory).

- **Automatikus frissítési alverzió**: Képez le ezt a mezőt a **AutoUpdate** a parancsmagok váltson, és lehetővé teszi, hogy a bővítmény automatikus frissítése a legújabb verzióra a telepítés során. **Igen** fel fog szólítania a kiterjesztés kezelő a legújabb elérhető verzió használata és **nem** kényszeríti a **verzió** megadott kell telepíteni. Egyik sem kiválasztása **Igen** sem **nem** ugyanaz, mint kiválasztania **nem**.

## <a name="logs"></a>Logs

A bővítmény naplók a következő helyen találhatók: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>További lépések

- További információ a PowerShell DSC, nyissa meg a [PowerShell dokumentációs központban](/powershell/dsc/overview).
- Vizsgálja meg a [Resource Manager-sablon a DSC-bővítmény](dsc-template.md).
- A további funkciókat, amelyeket felügyelhet a PowerShell DSC használatával, és további DSC-erőforrásokat, keresse meg a [PowerShell-galériából](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Bizalmas paraméterek átadása az konfigurációk kapcsolatos részletekért lásd: [biztonságosan a DSC-kiterjesztés kezelő a hitelesítő adatok kezelése](dsc-credentials.md).