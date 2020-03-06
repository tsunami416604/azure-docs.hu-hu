---
title: Az Azure kívánt állapotának konfigurációs bővítményének kezelője
description: PowerShell DSC-konfiguráció feltöltése és alkalmazása Azure-beli virtuális gépen DSC-bővítmény használatával
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383363"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-bővítmény

## <a name="overview"></a>Áttekintés

A Windows PowerShell DSC-bővítményét a Microsoft közzétette és támogatja. A bővítmény feltölti és egy PowerShell DSC-konfigurációt alkalmaz egy Azure-beli virtuális gépen. A DSC-bővítmény meghívja a PowerShell DSC-t, hogy meghozza a kapott DSC-konfigurációt a virtuális gépen. Ez a dokumentum részletesen ismerteti a Windows rendszerhez készült DSC virtuálisgép-bővítmény támogatott platformokat, konfigurációkat és üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC-bővítmény a következő operációs rendszereket támogatja

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1/10

### <a name="internet-connectivity"></a>Internetkapcsolat

A Windowshoz készült DSC-bővítmény megköveteli, hogy a célként megadott virtuális gép képes legyen kommunikálni az Azure-val és a konfigurációs csomag (. zip fájl) helyét, ha az az Azure-on kívüli helyen van tárolva. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a DSC-bővítmény beállítások részének sémáját mutatja egy Azure Resource Manager sablonban. 

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | dátum |
| publisher | Microsoft.Powershell.DSC | sztring |
| type | DSC | sztring |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>Beállítások tulajdonság értékei

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| settings.wmfVersion | sztring | Meghatározza a Windows felügyeleti keretrendszer azon verzióját, amelyet telepíteni kell a virtuális gépre. Ha ezt a tulajdonságot "Latest" értékre állítja, a rendszer a WMF legújabb verzióját telepíti. Ennek a tulajdonságnak csak az aktuális lehetséges értékei: "4,0", "5,0" és "Latest". Ezek a lehetséges értékek a frissítések tárgya. Az alapértelmezett érték a "Latest". |
| settings.configuration.url | sztring | Meghatározza azt az URL-címet, amelyből le szeretné tölteni a DSC-konfiguráció zip-fájlját. Ha a megadott URL-címnek SAS-tokenre van szüksége a hozzáféréshez, a Protectedsettingsfromkeyvault. configurationUrlSasToken tulajdonságot az SAS-jogkivonat értékére kell állítania. Ezt a tulajdonságot kötelező megadni, ha a Settings. Configuration. script és/vagy Settings. Configuration. Function definiálva van.
| settings.configuration.script | sztring | A DSC-konfiguráció definícióját tartalmazó parancsfájl fájlnevét adja meg. A szkriptnek a Configuration. URL tulajdonság által megadott URL-címről letöltött zip-fájl gyökérkönyvtárában kell lennie. Ezt a tulajdonságot kötelező megadni, ha a Settings. Configuration. URL és/vagy Settings. Configuration. script definiálva van.
| settings.configuration.function | sztring | Megadja a DSC-konfiguráció nevét. A nevű konfigurációnak szerepelnie kell a Configuration. script által meghatározott parancsfájlban. Ezt a tulajdonságot kötelező megadni, ha a Settings. Configuration. URL és/vagy Settings. Configuration. Function definiálva van.
| settings.configurationArguments | Gyűjtemény | Meghatározza a DSC-konfigurációnak átadandó paramétereket. Ez a tulajdonság nem lesz titkosítva.
| settings.configurationData.url | sztring | Meghatározza azt az URL-címet, amelyből le szeretné tölteni a DSC-konfiguráció bemenetként használandó konfigurációs adatait (. pds1). Ha a megadott URL-címnek SAS-tokenre van szüksége a hozzáféréshez, a Protectedsettingsfromkeyvault. configurationDataUrlSasToken tulajdonságot az SAS-jogkivonat értékére kell állítania.
| settings.privacy.dataEnabled | sztring | Engedélyezheti vagy letilthatja a telemetria-gyűjteményt. A tulajdonság egyetlen lehetséges értéke az "Enable", a "Disable", a "vagy a $null. Ha a tulajdonságot üresen hagyja, vagy NULL értékűre, akkor a telemetria engedélyezi
| settings.advancedOptions.forcePullAndApply | Bool | Ezzel a beállítással növelhető a bővítmények használatának a kiterjesztése a Azure Automation DSC-vel rendelkező csomópontok regisztrálásához.  Ha az érték `$true`, a bővítmény a sikeres vagy sikertelen visszatérés előtt megvárja a konfiguráció első futtatását a szolgáltatásból.  Ha az érték $falsere van állítva, akkor a bővítmény által visszaadott állapot csak azt jelzi, hogy a csomópont regisztrálva lett-e a Azure Automation állapot-konfigurációban, és a csomópont-konfiguráció nem lesz futtatva a regisztráció során.
| settings.advancedOptions.downloadMappings | Gyűjtemény | Alternatív helyszínek meghatározása a függőségek, például a WMF és a .NET letöltéséhez

### <a name="protected-settings-property-values"></a>Védett beállítások tulajdonságértékek

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sztring | Meghatározza a DSC-konfigurációnak átadandó paramétereket. Ez a tulajdonság titkosítva lesz. |
| protectedSettings.configurationUrlSasToken | sztring | Meghatározza a Configuration. URL által definiált URL-cím elérésére szolgáló SAS-tokent. Ez a tulajdonság titkosítva lesz. |
| protectedSettings.configurationDataUrlSasToken | sztring | Megadja az configurationData. URL által meghatározott URL-cím elérésére szolgáló SAS-tokent. Ez a tulajdonság titkosítva lesz. |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok.
A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a telepítés utáni konfigurációt igénylik.
A Windows DSC-bővítményét tartalmazó Resource Manager-sablonok az [Azure gyorskonfigurálás](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)-katalógusban találhatók.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény letöltése és üzembe helyezése az Azure-beli virtuális gépen erre a helyre
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

A kiterjesztési állapot fájlja tartalmazza az alállapotot és az állapot sikerességi/hibakódokat, valamint az egyes bővítmények részletes hibáját és leírását.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

A bővítmények kimeneti naplói a következő könyvtárba vannak naplózva:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 1000 | Általános hiba | Az ehhez a hibához tartozó üzenetet a bővítmények naplófájljaiban megadott kivétel jeleníti meg |
| 52 | Bővítmény telepítési hibája | A hiba üzenetét a megadott kivétel ismerteti |
| 1002 | WMF telepítési hiba | Hiba történt a WMF telepítésekor. |
| 1004 | Érvénytelen zip-csomag | Érvénytelen zip; Hiba történt a zip kicsomagolásakor |
| 1100 | Argumentum hibája | A felhasználó által megadott bemenet hibáját jelzi. A hiba üzenetét a megadott kivétel ismerteti|


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
