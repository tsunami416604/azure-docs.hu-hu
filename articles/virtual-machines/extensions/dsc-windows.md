---
title: Azure Desired State Configuration bővítmény kezelő |} A Microsoft Docs
description: Töltse fel, és a egy PowerShell DSC-konfiguráció alkalmazása a DSC-bővítmény használata Azure virtuális gép
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: b9e96473a6f66dcbc675da1553deaed4ad61b249
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630931"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC bővítmény

## <a name="overview"></a>Áttekintés

A Windows PowerShell DSC bővítmény közzétett és a Microsoft támogatja. A bővítmény tölt fel, és a egy Azure-beli virtuális gépen a PowerShell DSC-konfiguráció vonatkozik. A DSC-bővítmény életbe léptetni a fogadott DSC-konfiguráció a virtuális gépen a PowerShell DSC beérkező hívások. Ez a dokumentum részletesen, a támogatott platformok, a konfigurációk és a Windows DSC virtuálisgép-bővítmény az üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC-bővítmény támogatja a következő operációs rendszer

A Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows ügyfél 7/8.1

### <a name="internet-connectivity"></a>Internetkapcsolat

A Windows a DSC-bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON Azure Resource Manager-sablon a DSC-bővítmény beállítási részén sémáját jeleníti meg. 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.73",
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
            "forcePullAndApply": false
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
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Powershell.DSC | sztring |
| type | DSC | sztring |
| typeHandlerVersion | 2,73 | int |

### <a name="settings-property-values"></a>Beállítási tulajdonság értékei

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| settings.wmfVersion | sztring | Határozza meg a Windows Management Framework a virtuális gépre telepítendő verzióját. Ez a tulajdonság "legújabb" beállítás telepíti a WMF legtöbb frissített verzióját. Ez a tulajdonság csak az aktuális lehetséges értékei: 4.0', "5.0" és "legújabb". Ezek lehetséges értékek a következők vonatkoznak a frissítéseket. Az alapértelmezett érték: "legújabb". |
| settings.configuration.url | sztring | Itt adhatja meg, ahonnan letölthető a DSC konfigurációs zip-fájl URL-címét. Ha a megadott URL-cím egy SAS-token hozzáférést igényel, szüksége lesz a protectedSettings.configurationUrlSasToken tulajdonsága az SAS-token értékét. A tulajdonságot kötelező megadni, ha settings.configuration.script és/vagy settings.configuration.function van definiálva.
| settings.configuration.script | sztring | Megadja a fájl nevét, a parancsfájl, amely tartalmazza a DSC-konfiguráció definíciójának. Ez a szkript a gyökérmappában található azon a configuration.url tulajdonság által megadott URL-címéről letöltött zip-fájlt kell lennie. A tulajdonságot kötelező megadni, ha settings.configuration.url és/vagy settings.configuration.script van definiálva.
| settings.configuration.function | sztring | Megadja a DSC-konfiguráció nevét. A parancsfájl configuration.script által meghatározott tartalmaznia kell a nevű-konfigurációt. A tulajdonságot kötelező megadni, ha settings.configuration.url és/vagy settings.configuration.function van definiálva.
| settings.configurationArguments | Gyűjtemény | Határozza meg a paramétereket, adja át szeretné a DSC-konfiguráció. Ez a tulajdonság nem lesznek titkosítva.
| settings.configurationData.url | sztring | Adja meg az URL-cím, ahonnan letölthető a konfigurációs adatfájl (.pds1) való használatra a DSC-konfiguráció bemenetként. Ha a megadott URL-cím egy SAS-token hozzáférést igényel, szüksége lesz a protectedSettings.configurationDataUrlSasToken tulajdonsága az SAS-token értékét.
| settings.privacy.dataEnabled | sztring | Engedélyezheti vagy letilthatja a telemetriai adatok gyűjtése. Ez a tulajdonság csak lehetséges értékei a következők "Engedélyezése", "Letiltás", ", vagy $null. És ez a tulajdonság üres vagy null értékű lesz telemetria engedélyezése
| settings.advancedOptions.forcePullAndApply | Logikai | Lehetővé teszi a DSC-bővítmény frissítése és a DSC-konfigurációk kihirdeti, amikor a frissítési mód lekéréses.
| settings.advancedOptions.downloadMappings | Gyűjtemény | Határozza meg a másodlagos helyek függőségeit, például a .NET és a WMF letöltése

### <a name="protected-settings-property-values"></a>Védett beállításainak Vlastnost értékek

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | sztring | Határozza meg a paramétereket, adja át szeretné a DSC-konfiguráció. Ez a tulajdonság lesznek titkosítva. |
| protectedSettings.configurationUrlSasToken | sztring | Adja meg a SAS-jogkivonat a configuration.url által meghatározott URL-címet. Ez a tulajdonság lesznek titkosítva. |
| protectedSettings.configurationDataUrlSasToken | sztring | Adja meg a SAS-jogkivonat a configurationData.url által meghatározott URL-címet. Ez a tulajdonság lesznek titkosítva. |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok ideálisak, egy vagy több üzembe helyezés utáni konfigurálásra igénylő virtuális gépek üzembe helyezésekor. Egy mintául szolgáló Resource Manager-sablon, amely tartalmazza az OMS-ügynök Virtuálisgép-bővítmény találhatók a [Azure gyors üzembe helyezési katalógus](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. 

A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép. Helyezi el a JSON-bővítmény a sablonban gyökérmappájában, amikor az erőforrás neve a szülő virtuális gép egy hivatkozást tartalmaz, és a típus a beágyazott konfigurációját tükrözi.  


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével az OMS-ügynök Virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. Cserélje le az OMS kulcs és az OMS-azonosítója együtt származik az OMS-munkaterülethez. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.73 --protected-settings '{}' \
  --settings '{}'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Kiterjesztési csomag letöltése és ezen a helyen, az Azure virtuális gépen telepített
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

A bővítményfájl állapot sub állapota és sikeres/meghiúsult állapotkódok együtt a részletes hibaüzeneteket és a leírása a egyes kiterjesztések, futtassa tartalmazza.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Bővítmény kimeneti naplóit a rendszer naplózza a következő könyvtárban:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 1000 | Általános hiba | Az üzenet a hiba a bővítmény naplók az adott kivétel által biztosított |
| 52 | Bővítmény telepítési hiba | Ennek a hibának az üzenet az adott kivétel által biztosított |
| 1002 | A WMF-telepítési hiba | Hiba történt a WMF telepítése. |
| 1004 | Érvénytelen Zip-csomagját | Érvénytelen tömörített; Hiba történt a zip-fájl kicsomagolása |
| 1100 | Argument hiba | A bemeneti adatok, a felhasználó által megadott problémát jelez. A hiba az üzenet az adott kivétel által biztosított|


### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).