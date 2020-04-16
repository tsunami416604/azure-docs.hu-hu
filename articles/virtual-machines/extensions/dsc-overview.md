---
title: Kívánt állapotkonfiguráció az Azure-hoz – áttekintés
description: Megtudhatja, hogy miként használhatja a Microsoft Azure bővítménykezelőt a Kívánt PowerShell-konfigurációhoz (DSC). A cikk előfeltételeket, architektúrát és parancsmagokat tartalmaz.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: c61ba0840b75bff10af1d802a9b90c922ef1f12f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415871"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Az Azure Desired State Configuration-bővítménykezelő bemutatása

Az Azure Virtuálisgép-ügynök és a kapcsolódó bővítmények a Microsoft Azure-infrastruktúra-szolgáltatások részét képezik. A virtuálisgép-bővítmények olyan szoftverösszetevők, amelyek kiterjesztik a virtuális gép funkcióit, és egyszerűsítik a különböző virtuálisgép-kezelési műveleteket.

Az Azure desired state configuration (DSC) bővítmény elsődleges használati esete az [Azure Automation State Configuration (DSC) szolgáltatás](../../automation/automation-dsc-overview.md)ba.
A szolgáltatás [olyan előnyöket](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) biztosít, amelyek magukban foglalják a virtuális gép konfigurációjának folyamatos kezelését és más operatív eszközökkel, például az Azure Monitoringszolgáltatással való integrációt.
A bővítmény használatával regisztrálja a virtuális gépek a szolgáltatás rugalmas megoldást, amely még működik az Azure-előfizetések között.

A DSC-bővítmény az Automation DSC szolgáltatástól függetlenül használható.
Ez azonban csak egy konfigurációt a virtuális gépre.
Nincs folyamatban lévő jelentéskészítési érhető el, kivéve a helyi legkülönbben a virtuális gép.

Ez a cikk mindkét forgatókönyvről tartalmaz információkat: a DSC-bővítmény automation-bevezetéshez, és a DSC-bővítmény eszközként való hozzárendelése konfigurációk virtuális gépek hez az Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

- **Helyi gép:** Az Azure VM-bővítmény kezeléséhez az Azure Portalon vagy az Azure PowerShell SDK-t kell használnia.
- **Vendégügynök:** A DSC-konfiguráció által konfigurált Azure virtuális gépnek olyan operációs rendszernek kell lennie, amely támogatja a Windows Management Framework (WMF) 4.0-s vagy újabb verzióit. A támogatott operációsrendszer-verziók teljes listáját a [DSC-bővítmény verzióelőzményei](/powershell/scripting/dsc/getting-started/azuredscexthistory)között található.

## <a name="terms-and-concepts"></a>Feltételek és fogalmak

Ez az útmutató feltételezi a következő fogalmak ismeretét:

- **Konfiguráció**: DSC konfigurációs dokumentum.
- **Csomópont**: DSC-konfiguráció célja. Ebben a dokumentumban *a csomópont* mindig egy Azure virtuális gép.
- **Konfigurációs adatok**: Konfigurációs adatok : .psd1 fájl, amely környezeti adatokat a konfiguráció.

## <a name="architecture"></a>Architektúra

Az Azure DSC-bővítmény az Azure VM Agent keretrendszer használatával biztosítja, életbe lépteti és jelentést készít az Azure virtuális gépeken futó DSC-konfigurációkról. A DSC-bővítmény elfogad egy konfigurációs dokumentumot és egy sor paramétert. Ha nem áll rendelkezésre fájl, a bővítménybe egy [alapértelmezett konfigurációs parancsfájl](#default-configuration-script) lesz beágyazva. Az alapértelmezett konfigurációs parancsfájl csak a Metaadatok beállítására szolgál a [Helyi konfigurációkezelőben.](/powershell/scripting/dsc/managing-nodes/metaConfig)

A bővítmény első alkalommal történő meghívásakor a WMF egy verzióját telepíti a következő logika használatával:

- Ha az Azure VM operációs rendszer Windows Server 2016, nincs művelet. A Windows Server 2016 már telepítette a PowerShell legújabb verzióját.
- Ha a **wmfVersion** tulajdonság meg van adva, a WMF adott verziója telepítve van, kivéve, ha ez a verzió nem kompatibilis a virtuális gép operációs rendszerével.
- Ha nincs **megadva wmfVersion** tulajdonság, a WMF legújabb megfelelő verziója lesz telepítve.

A WMF telepítéséhez újra kell indítani. Az újraindítás után a bővítmény letölti a **modulesUrl** tulajdonságban megadott .zip fájlt, ha meg van adva. Ha ez a hely az Azure Blob storage-ban található, megadhat egy SAS-jogkivonatot a **SasToken** tulajdonságban a fájl eléréséhez. A .zip letöltése és kicsomagolása után a **configurationFunction** függvényben definiált konfigurációs függvény .mof([Managed Object Format](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-)) fájlt hoz létre. A kiterjesztés `Start-DscConfiguration -Force` ezután a létrehozott .mof fájl használatával fut. A bővítmény rögzíti a kimenetet, és írja az Azure-állapotcsatornába.

### <a name="default-configuration-script"></a>Alapértelmezett konfigurációs parancsfájl

Az Azure DSC-bővítmény tartalmaz egy alapértelmezett konfigurációs parancsfájlt, amelyet használni kell, amikor egy virtuális gép az Azure Automation DSC szolgáltatásba. A parancsfájl-paraméterek a [Helyi konfigurációkezelő](/powershell/scripting/dsc/managing-nodes/metaConfig)konfigurálható tulajdonságaihoz igazodnak. A parancsfájl-paraméterekről az [Alapértelmezett konfigurációs parancsfájl](dsc-template.md#default-configuration-script) a [Kívánt állapotkonfiguráció bővítményben az Azure Resource Manager-sablonokkal.](dsc-template.md) A teljes parancsfájlról az Azure rövid útmutató sablonját a [GitHubon](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)olvashatja.

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Információ az Azure Automation State Configuration (DSC) szolgáltatásban való regisztrációhoz

Ha a DSC-bővítmény használatával regisztrál egy csomópontot az Állapotkonfiguráció szolgáltatással, három értéket kell megadni.

- RegistrationUrl - az Azure Automation-fiók https-címe
- RegistrationKey - egy megosztott titok, amely a csomópontok at regisztrála a szolgáltatásban
- NodeConfigurationName - a szolgáltatásból a kiszolgálói szerepkör konfigurálásához lehívandó csomópontkonfiguráció (MOF) neve

Ez az információ látható az [Azure Portalon,](../../automation/automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal) vagy használhatja a PowerShellt.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

A csomópont konfigurációjának neve, győződjön meg arról, hogy a csomópont konfigurációja létezik az Azure állapotkonfiguráció.  Ha nem, a bővítmény központi telepítése hiba lesz.  Győződjön meg arról is, hogy a *csomópont-konfiguráció* nevét használja, és nem a konfigurációt.
A konfiguráció egy parancsfájlban van definiálva, amely [a csomópontkonfiguráció (MOF-fájl) fordítására](https://docs.microsoft.com/azure/automation/automation-dsc-compile)szolgál.
A név mindig a Konfiguráció lesz, amelyet egy pont `.` és egy adott `localhost` számítógépnév követ.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-bővítmény az Erőforrás-kezelő sablonjaiban

A legtöbb esetben az Erőforrás-kezelő központi telepítési sablonjai a DSC-bővítmény rel való munka várható módja. További információkért és példákat, hogyan lehet a DSC-bővítmény t a Resource Manager telepítési sablonok, lásd: [Kívánt állapot konfiguráció bővítmény az Azure Resource Manager-sablonok.](dsc-template.md)

## <a name="dsc-extension-powershell-cmdlets"></a>PowerShell-parancsmagok DSC-bővítmény

A DSC-bővítmény kezelésére használt PowerShell-parancsmagok a legjobban interaktív hibaelhárítási és információgyűjtési forgatókönyvekben használhatók. A parancsmagok segítségével csomagolhatja, közzéteheti és figyelheti a DSC-bővítmény központi telepítéseit. A DSC-bővítmény parancsmagjai még nem frissülnek az [alapértelmezett konfigurációs parancsfájllal](#default-configuration-script)való együttműködésre.

A **Publish-AzVMDscConfiguration** parancsmag bevesz egy konfigurációs fájlt, megkeresi a dsc-erőforrásokat, majd létrehoz egy .zip fájlt. A .zip fájl tartalmazza a konfigurációs és DSC-erőforrásokat, amelyek a konfiguráció életbe léptetéséhez szükségesek. A parancsmag helyileg is létrehozhatja a csomagot a *-OutputArchivePath* paraméter használatával. Ellenkező esetben a parancsmag közzéteszi a .zip fájlt blob storage, majd biztonságossá teszi azt egy SAS-jogkivonattal.

A parancsmag által létrehozott .ps1 konfigurációs parancsfájl az archív mappa gyökérében található .zip fájlban található. A modulmappa az erőforrások archív mappájába kerül.

A **Set-AzVMDscExtension** parancsmag a PowerShell DSC-bővítmény által igényelt beállításokat egy virtuális gép konfigurációs objektumba adja.

A **Get-AzVMDscExtension** parancsmag lekéri egy adott virtuális gép DSC-bővítmény állapotát.

A **Get-AzVMDscExtensionStatus** parancsmag lekéri a DSC-bővítménykezelő által elfogadott DSC-konfiguráció állapotát. Ez a művelet egyetlen virtuális számítógépen vagy virtuális gépek egy csoportján hajtható végre.

Az **Remove-AzVMDscExtension** parancsmag eltávolítja a bővítménykezelőt egy adott virtuális gépről. Ez a parancsmag nem távolítja el a konfigurációt, nem távolítja el a WMF-et, és *nem* módosítja a virtuális gép alkalmazott beállításait. Csak a bővítménykezelőt távolítja el. 

Fontos tudnivalók az Erőforrás-kezelő DSC-bővítményparancsmagjairól:

- Az Azure Resource Manager parancsmagjai szinkron.
- A *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*és *Location* paraméterek mind szükségesek.
- *Az ArchiveResourceGroupName* egy választható paraméter. Ezt a paramétert akkor adhatja meg, ha a tárfiók egy másik erőforráscsoporthoz tartozik, mint ahol a virtuális gép jön létre.
- Az **Automatikus frissítés** kapcsolóval automatikusan frissítheti a bővítménykezelőt a legújabb verzióra, ha az elérhetővé válik. Ez a paraméter a WMF új verziójának megjelenésekor újraindulatot okozhat a virtuális gépen.

### <a name="get-started-with-cmdlets"></a>A parancsmagok – első lépések

Az Azure DSC-bővítmény dsc konfigurációs dokumentumok használatával közvetlenül konfigurálhatja az Azure virtuális gépeket a telepítés során. Ez a lépés nem regisztrálja a csomópontot az automation. A csomópont *központilag nem* felügyelt.

A következő példa egy egyszerű példát mutat be egy konfigurációra. Mentse a konfigurációt helyileg iisInstall.ps1 néven.

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

A következő parancsok az iisInstall.ps1 parancsfájlt a megadott virtuális gépre helyezik. A parancsok a konfigurációt is végrehajtják, majd jelentést tesznek az állapotról.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI használható a DSC-bővítmény üzembe helyezéséhez egy meglévő virtuális gépen.

Windows rendszert futtató virtuális gép esetén:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Linuxot futtató virtuális gép esetén:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Az Azure Portal funkciói

A DSC beállítása a portálon:

1. Nyissa meg a virtuális gép.
2. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet.
3. A létrehozott új lapon válassza a **+ Add**, majd a **PowerShell kívánt állapotkonfigurációja lehetőséget.**
4. Kattintson a **Létrehozás** gombra a bővítményinformációs lap alján.

A portál a következő adatokat gyűjti:

- **Konfigurációs modulok vagy parancsfájl:** Ez a mező kötelező (az űrlap nem lett frissítve az [alapértelmezett konfigurációs parancsfájlhoz).](#default-configuration-script) A konfigurációs modulokhoz és parancsfájlokhoz egy .ps1 fájl szükséges, amely konfigurációs parancsfájlt vagy .zip fájlt rendelkezik, amelynek gyökérfájljában .ps1 konfigurációs parancsfájl van. Ha .zip fájlt használ, minden függő erőforrásnak szerepelnie kell a .zip modulmappáiban. A .zip-fájlt az Azure PowerShell SDK-ban található **Publish-AzureVMDscConfiguration -OutputArchivePath** parancsmag használatával hozhatja létre. A .zip fájl feltöltésre kerül a felhasználói blob tárolóba, és egy SAS-jogkivonat biztosítja.

- **Modulminősítésű Konfiguráció neve**: Egy .ps1 fájlban több konfigurációs függvényt is felvehet. Írja be a konfigurációs .ps1 \\ parancsfájl nevét és a konfigurációs függvény nevét. Ha például a .ps1 parancsfájl nak a configuration.ps1 neve van, és a konfiguráció **iisInstall**, írja be a **configuration.ps1\IisInstall parancsot.**

- **Konfigurációs argumentumok:** Ha a konfigurációs függvény argumentumokat vesz fel, akkor itt adja meg azokat **argumentargumentName1=value1,argumentName2=value2**formátumban. Ez a formátum egy másik formátum, amelyben konfigurációs argumentumok fogadhatók el a PowerShell-parancsmagok vagy az Erőforrás-kezelő sablonok.

- **Konfigurációs adatok PSD1 fájl**: Ez a mező nem kötelező. Ha a konfigurációhoz konfigurációs adatfájlra van szükség a .psd1 fájlban, ebben a mezőben jelölje ki az adatmezőt, és töltse fel a felhasználói blobtárolóba. A konfigurációs adatfájlt egy SAS-jogkivonat biztosítja a blob storage-ban.

- **WMF-verzió:** A Windows Management Framework (WMF) azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot a legújabbra állítja, a WMF legújabb verzióját telepíti. Jelenleg a tulajdonság egyetlen lehetséges értéke 4.0, 5.0, 5.1 és legújabb. Ezek relevancia a frissítések hatálya alá tartoznak. Az alapértelmezett érték a **legújabb**.

- **Adatgyűjtés:** Azt határozza meg, hogy a bővítmény gyűjti-e a telemetriai adatokat. További információ: [Azure DSC-bővítmény adatgyűjtés.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)

- **Verzió**: A telepítandó DSC-bővítmény verzióját adja meg. A verziókról további információt a [DSC-bővítmény verzióelőzményei című témakörben talál.](/powershell/scripting/dsc/getting-started/azuredscexthistory)

- **Automatikus frissítés alverzió**: Ez a mező leképezi az **Automatikus frissítés** kapcsolót a parancsmagokban, és lehetővé teszi, hogy a bővítmény a telepítés során automatikusan frissüljön a legújabb verzióra. **Az Igen** utasítást ad a bővítménykezelőnek a legújabb elérhető verzió használatára, a **Nem pedig** a megadott **verzió** telepítését kényszeríti. Ha nem jelöli be az **Igen** és a **Nem** lehetőséget, az megegyezik a **Nem**lehetőség kiválasztásával.

## <a name="logs"></a>Naplók

A bővítmény naplói a következő helyen tárolódnak:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>További lépések

- A PowerShell DSC-ről további információt a [PowerShell dokumentációs központjában](/powershell/scripting/dsc/overview/overview)talál.
- Vizsgálja meg a [DSC-bővítmény Erőforráskezelő sablonját.](dsc-template.md)
- A PowerShell DSC használatával kezelhető további funkciókért és további DSC-erőforrásokért keresse meg a [PowerShell-galériát.](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)
- A bizalmas paraméterek konfigurációkba való átviteléről a [Hitelesítő adatok biztonságos kezelése a DSC-bővítménykezelővel című](dsc-credentials.md)témakörben talál.
