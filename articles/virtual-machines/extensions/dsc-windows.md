---
title: Azure kívánt állapot konfigurációs kiterjesztés kezelőjének |} Microsoft Docs
description: Töltse fel, és a PowerShell DSC-konfiguráció alkalmazása a DSC-bővítményt használ, egy Azure virtuális gépen
services: virtual-machines-windows
documentationcenter: ''
author: eshaparmar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: esparmar
ms.openlocfilehash: b34314951980f7dbe2269119883dec52a90a0587
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942703"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC-bővítményt

## <a name="overview"></a>Áttekintés

A Windows PowerShell DSC-bővítményt közzétett és a Microsoft támogatja. A bővítmény feltölti, és alkalmazza a PowerShell DSC-konfiguráció egy Azure virtuális gépen. A DSC-bővítményt meghívja a PowerShell DSC életbe léptetni a fogadott DSC-konfiguráció a virtuális Gépen. Ez a dokumentum a támogatott platformok, a konfigurációkat és a Windows DSC virtuálisgép-bővítmény telepítési lehetőségei részletes.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A DSC-bővítményt támogatja a következő operációs rendszer

Windows Server 2016-, Windows Server 2012 R2 rendszerben, a Windows Server 2012, Windows Server 2008 R2 SP1, Windows-ügyfél 7/8.1

### <a name="internet-connectivity"></a>Internetkapcsolat

A DSC-bővítményt, a Windows megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON Azure Resource Manager-sablonok a sémát a DSC-bővítményt beállítási részén látható. 

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

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Powershell.DSC | karakterlánc |
| type | A DSC-BŐL | karakterlánc |
| typeHandlerVersion | 2,73 | int |

### <a name="settings-property-values"></a>Beállítási tulajdonság értékei

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| settings.wmfVersion | karakterlánc | Adja meg a Windows Management Framework, amelyet telepíteni kell a virtuális gép verziója. A "legújabb" tulajdonság beállítása esetén a WMF legfrissebb verzióját telepíti. Ez a tulajdonság csak az aktuális lehetséges értékei "4.0", "5.0" és "legújabb". A lehetséges értékek a frissítések vonatkoznak. Az alapértelmezett érték: "legújabb". |
| settings.configuration.url | karakterlánc | Meghatározza a URL-címét, amelyről letöltheti a DSC konfigurációs zip-fájl. Ha az URL-cím egy SAS-jogkivonat hozzáférést igényel, szüksége lesz a protectedSettings.configurationUrlSasToken tulajdonsága az SAS-token értékét. E tulajdonság megadása kötelező, ha settings.configuration.script és/vagy settings.configuration.function vannak meghatározva.
| settings.configuration.script | karakterlánc | Adja meg a parancsfájl a DSC-konfiguráció definícióját tartalmazó fájl neve. Ez a parancsfájl configuration.url tulajdonság által megadott URL-címről letöltött zip-fájl a gyökérmappában kell lennie. E tulajdonság megadása kötelező, ha settings.configuration.url és/vagy settings.configuration.script vannak meghatározva.
| settings.configuration.function | karakterlánc | Megadja a DSC-konfiguráció nevét. A konfiguráció a parancsfájl configuration.script által meghatározott kell tartoznia. E tulajdonság megadása kötelező, ha settings.configuration.url és/vagy settings.configuration.function vannak meghatározva.
| settings.configurationArguments | Gyűjtemény | Meghatározza bármely továbbítani szeretné a DSC-konfiguráció. Ez a tulajdonság nem lesznek titkosítva.
| settings.configurationData.url | karakterlánc | Meghatározza a URL-címet, amelyről letöltheti a konfigurációs adatfájlt (.pds1) használandó bemenetként a DSC-konfiguráció. Ha az URL-cím egy SAS-jogkivonat hozzáférést igényel, szüksége lesz a protectedSettings.configurationDataUrlSasToken tulajdonsága az SAS-token értékét.
| settings.privacy.dataEnabled | karakterlánc | Engedélyezheti vagy letilthatja a telemetriai adatok gyűjtése. Ez a tulajdonság csak lehetséges értékei "Engedélyezés", "Letiltás", ", vagy $null. Így ez a tulajdonság üres vagy null értékű engedélyezi telemetriai adat
| settings.advancedOptions.forcePullAndApply | logikai érték | Lehetővé teszi, hogy a frissítés és a DSC-konfigurációk kihirdeti, ha a frissítési mód lekéréses DSC-bővítményt.
| settings.advancedOptions.downloadMappings | Gyűjtemény | Határozza meg a másodlagos helyek például WMF és .NET függőségek letöltése

### <a name="protected-settings-property-values"></a>Védett beállítások tulajdonságértékek

| Name (Név) | Adattípus | Leírás
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | karakterlánc | Meghatározza bármely továbbítani szeretné a DSC-konfiguráció. Ez a tulajdonság lesz titkosítva. |
| protectedSettings.configurationUrlSasToken | karakterlánc | Megadja a SAS-jogkivonat a configuration.url által megadott URL-cím elérésére. Ez a tulajdonság lesz titkosítva. |
| protectedSettings.configurationDataUrlSasToken | karakterlánc | Megadja a SAS-jogkivonat a configurationData.url által megadott URL-cím elérésére. Ez a tulajdonság lesz titkosítva. |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Sablonok is épp ezért tökéletes választás, ha a feladás egy vagy több központi telepítési beállításokra van szükség egy vagy több virtuális gépek telepítése. Az OMS-ügynök Virtuálisgép-bővítmény tartalmazó minta Resource Manager sablon megtalálható a [Azure Quick Start gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/dsc-extension-iis-server-windows-vm). 

A JSON-konfiguráció a virtuálisgép-bővítmény a virtuálisgép-erőforrás ágyazhatók egymásba, vagy elhelyezve, a gyökér vagy a legfelső szintű erőforrás-kezelő JSON-sablon. A JSON-konfiguráció elhelyezési erőforrás nevét és típusát. értéke befolyásolja. 

A bővítmény erőforrás beágyazási, amikor bekerül a JSON a `"resources": []` objektum a virtuális gép. A bővítmény JSON elhelyezésekor a sablon gyökerében, az erőforrás nevét a szülő virtuális gép egy hivatkozást tartalmaz, és a típus beágyazott konfigurációját tükrözi.  


## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével az OMS-ügynök Virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre. Cserélje le az OMS-kulcsot és az OMS-azonosító az OMS-munkaterület szerintiek. 

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

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure parancssori felület használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure parancssori felület használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Kiterjesztési csomag letöltésére és telepíteni ezen a helyen, az Azure virtuális gépen
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

A bővítményfájl állapot sub állapota és állapota sikeres/hibakódokat és a hibával kapcsolatos részletes és a egyes kiterjesztések futtatása leírása tartalmazza.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

Bővítmény kimeneti naplókat a rendszer naplózza a következő könyvtárra:

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 1000 | Általános hiba | A bővítmény naplók egyedi kivételt biztosítja ezt a hibát az üzenet |
| 52 | Bővítmény telepítési hiba | Ez a hiba az üzenet az adott kivétel által biztosított |
| 1002 | WMF telepítési hiba | Hiba történt a WMF telepítése. |
| 1004 | Érvénytelen Zip-csomagját | Érvénytelen zip; Hiba történt a zip kicsomagolása |
| 1100 | Argumentum hiba | A felhasználó által megadott bemenetet kapcsolatos problémát jelez. A hiba az üzenet az adott kivétel által biztosított|


### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).