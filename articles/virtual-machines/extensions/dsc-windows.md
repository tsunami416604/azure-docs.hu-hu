---
title: Az Azure kívánt állapotkonfigurációs bővítménykezelője
description: PowerShell-DSC-konfiguráció feltöltése és alkalmazása Egy Azure virtuális gépen a DSC-bővítmény használatával
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 592c731d1851ac36cf9b57864750df0603b6c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253961"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-bővítmény

## <a name="overview"></a>Áttekintés

A Windows PowerShell DSC-bővítményt a Microsoft közzéteszi és támogatja. A bővítmény feltölti és alkalmazza a PowerShell DSC-konfiguráció egy Azure virtuális gép. A DSC-bővítmény a PowerShell DSC-be hívja a virtuális gép fogadott DSC-konfigurációjának életbe léptetéséhez. Ez a dokumentum részletezi a Windows DSC virtuálisgép-bővítmény támogatott platformjait, konfigurációit és üzembe helyezését.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC extension a következő operációs rendszer

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Ügyfél 7/8.1/10

### <a name="internet-connectivity"></a>Internetkapcsolat

A Windows DSC-bővítmény megköveteli, hogy a cél virtuális gép képes kommunikálni az Azure-ral és a konfigurációs csomag (.zip fájl) helyét, ha az Azure-on kívüli helyen van tárolva. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON egy Azure Resource Manager-sablonban a DSC-bővítmény beállítási részének sémáját jeleníti meg. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | dátum |
| közzétevő | Microsoft.Powershell.DSC | sztring |
| type | DSC | sztring |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Beállítások tulajdonság értékei

| Név | Adattípus | Leírás
| ---- | ---- | ---- |
| beállítások.wmfVerzió | sztring | A Windows Management Framework azon verzióját adja meg, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot "legújabbra" állítja, a WMF legfrissebb verziója települ. A tulajdonság egyetlen aktuális lehetséges értéke a "4.0", "5.0" és "latest". Ezek relevancia a frissítések hatálya alá tartoznak. Az alapértelmezett érték a "legújabb". |
| settings.configuration.url | sztring | Itt adhatja meg azt az URL-címet, ahonnan a DSC konfigurációs zip-fájlját le szeretné tölteni. Ha a megadott URL-cím hez sas-jogkivonat szükséges a hozzáféréshez, a protectedSettings.configurationUrlSasToken tulajdonságot a SAS-jogkivonat értékére kell beállítania. Erre a tulajdonságra akkor van szükség, ha a settings.configuration.script és/vagy a settings.configuration.function definiálva van.
| settings.configuration.script | sztring | Megadja a DSC-konfiguráció definícióját tartalmazó parancsfájl fájlnevét. Ennek a parancsfájlnak a configuration.url tulajdonság által megadott URL-címről letöltött zip-fájl gyökérmappájában kell lennie. Erre a tulajdonságra akkor van szükség, ha a settings.configuration.url és/vagy a settings.configuration.script definiálva van.
| settings.configuration.function | sztring | Megadja a DSC-konfiguráció nevét. A megnevezett konfigurációnak a configuration.script által definiált parancsfájlban kell lennie. Erre a tulajdonságra akkor van szükség, ha a settings.configuration.url és/vagy a settings.configuration.function definiálva van.
| settings.configurationArguments | Gyűjtemény | Meghatározza azokat a paramétereket, amelyeket át szeretne adni a DSC-konfigurációnak. Ez a tulajdonság nem lesz titkosítva.
| settings.configurationData.url | sztring | Megadja azt az URL-címet, amelyről a konfigurációs adatokat (.pds1) szeretné letölteni a DSC-konfiguráció bemeneteként. Ha a megadott URL-cím hez SAS-jogkivonat szükséges a hozzáféréshez, a protectedSettings.configurationDataUrlSasToken tulajdonságot a SAS-jogkivonat értékére kell beállítania.
| settings.privacy.dataEnabled | sztring | Engedélyezi vagy letiltja a telemetriai adatok gyűjteményét. A tulajdonság egyetlen lehetséges értéke az "Enable", a "Disable", a ", vagy $null. Ha üresen vagy null értéken hagyja ezt a tulajdonságot, az engedélyezi a telemetriai adatokat.
| settings.advancedOptions.forcePullAndApply | Logikai | Ez a beállítás a bővítmény használatával való együttműködés élményének fokozására szolgál az Azure Automation DSC-vel való csomópontok regisztrálásához.  Ha az `$true`érték , a bővítmény megvárja a szolgáltatásból lekért konfiguráció első futtatását, mielőtt a sikeres/sikertelen értéket ad vissza.  Ha az érték $false értékre van állítva, a bővítmény által visszaadott állapot csak arra vonatkozik, hogy a csomópont sikeresen regisztrálva volt-e az Azure Automation-állapot konfigurációjával, és a csomópont konfigurációja nem fog futni a regisztráció során.
| settings.advancedOptions.downloadMappings | Gyűjtemény | Alternatív helyeket határoz meg a függőségek , például a WMF és a .NET letöltéséhez

### <a name="protected-settings-property-values"></a>Védett beállítások tulajdonságértékei

| Név | Adattípus | Leírás
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sztring | Meghatározza azokat a paramétereket, amelyeket át szeretne adni a DSC-konfigurációnak. Ez a tulajdonság titkosítva lesz. |
| protectedSettings.configurationUrlSasToken | sztring | Megadja a configuration.url által definiált URL-cím eléréséhez a SAS-jogkivonatot. Ez a tulajdonság titkosítva lesz. |
| protectedSettings.configurationDataUrlSasToken | sztring | Megadja a configurationData.url által definiált URL-cím eléréséhez a SAS-jogkivonatot. Ez a tulajdonság titkosítva lesz. |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők.
A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek üzembe helyezés után konfigurációt igényelnek.
Az [Azure Gyorskatalógusban](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)található egy erőforrás-kezelősablon, amely tartalmazza a Windows DSC-bővítményt.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítménycsomag letöltése és üzembe helyezése erre a helyre történik az Azure virtuális gépén
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

A kiterjesztés állapotfájlja tartalmazza az alállapotot és az állapotsikeres/hibakódokat, valamint az egyes bővítmények futtatásának részletes hibáját és leírását.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

A bővítmény kimeneti naplója a következő könyvtárba kerül:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentésük

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 1000 | Általános hiba | A hiba üzenetét a bővítménynaplókban szereplő kivétel adja meg. |
| 52 | Bővítmény telepítési hibája | A hibához tartozó üzenetet az adott kivétel |
| 1002 | Wmf telepítési hiba | Hiba történt a WMF telepítésekor. |
| 1004 | Érvénytelen zip-csomag | Érvénytelen zip ; Hiba a zip kicsomagolásakor |
| 1100 | Argumentumhiba | A felhasználó által megadott bemeneti probléma. A hiba üzenetét az adott kivétel|


### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
