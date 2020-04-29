---
title: Egyéni parancsfájlok futtatása Linux rendszerű virtuális gépeken az Azure-ban
description: Linux rendszerű virtuális gépek konfigurációs feladatainak automatizálása az egyéni parancsfájl-bővítmény v1 használatával
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79535408"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Az egyéni Azure script Extension 1. verziójának használata Linux rendszerű virtuális gépekkel

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Az egyéni script Extension 1. verziója letölti és futtatja az Azure Virtual Machines szolgáltatásban futó parancsfájlokat. Ez a bővítmény az üzembe helyezés utáni konfiguráció, a Szoftvertelepítés vagy bármely egyéb konfigurációs/felügyeleti feladat esetén hasznos. A szkripteket letöltheti az Azure Storage-ból vagy más elérhető Internet-helyről, vagy megadhatja a bővítmény futtatókörnyezetét.

Az egyéni szkriptek bővítménye Azure Resource Manager-sablonokkal integrálódik. Azt is futtathatja az Azure CLI, a PowerShell, a Azure Portal vagy az Azure Virtual Machines REST API használatával.

Ez a cikk részletesen ismerteti, hogyan használhatja az egyéni szkriptek bővítményét az Azure CLI-ből, és hogyan futtathatja a bővítményt Azure Resource Manager sablon használatával. Ez a cikk a Linux rendszerekre vonatkozó hibaelhárítási lépéseket is tartalmaz.

Két Linux egyéni parancsfájl-bővítmény létezik:

* 1. verzió – Microsoft. OSTCExtensions. CustomScriptForLinux

* 2. verzió – Microsoft. Azure. Extensions. CustomScript

Az új és a meglévő központi telepítések helyett használja az új verziót ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)). Az új verzió a régi kiváltására készült. A migrálásához így elég megváltoztatni a nevet és a verziót, nincs szükség a bővítménykonfiguráció módosítására.

### <a name="operating-system"></a>Operációs rendszer

Támogatott Linux-disztribúciók:

* CentOS 6,5 és újabb verziók
* Debian 8 és újabb verziók
  * A Debian 8,7 nem az Python2-t a legújabb lemezképekben szállítja, így a CustomScriptForLinux megszakad.
* FreeBSD
* OpenSUSE 13,1 és újabb verziók
* Oracle Linux 6,4 és újabb
* SUSE Linux Enterprise Server 11 SP3 és újabb verziók
* Ubuntu 12,04 és újabb verziók

### <a name="script-location"></a>Parancsfájl helye

A bővítmény használatával az Azure Blob Storage hitelesítő adatait használhatja az Azure Blob Storage eléréséhez. Azt is megteheti, hogy a parancsfájl helye bármilyen lehet, ha a virtuális gép átirányítható erre a végpontra, mint például a GitHub, a belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubot vagy az Azure Storage-t, akkor további tűzfal/hálózati biztonsági csoport portjait kell megnyitnia. Ha például a parancsfájl az Azure Storage-ban található, az Azure NSG Service-címkék használatával engedélyezheti a hozzáférést a [tároláshoz](../../virtual-network/security-overview.md#service-tags).

Ha a parancsfájl egy helyi kiszolgálón található, akkor továbbra is szükség lehet további tűzfal/hálózati biztonsági csoport portjainak megnyitására.

### <a name="tips-and-tricks"></a>Tippek és trükkök

* A bővítmény meghibásodásai legnagyobb arányban a szkriptben található szintaktikai hibák miatt jelentkeznek. Tesztelje, hogy a szkript fut-e hiba nélkül, és lássa el további naplózással a szkriptet, hogy könnyebb legyen a hiba helyét azonosítani.
* Írjon idempotens szkripteket, hogy ne okozzanak változásokat a rendszerben, ha véletlenül többször is futtatják őket.
* Győződjön meg arról, hogy a parancsfájlok futtatásakor nincs szükség felhasználói bevitelre.
* A szkript futtatásához 90 perc van engedélyezve, ami továbbra is a bővítmény sikertelen kiépítését eredményezi.
* Ne helyezzen újraindítást a parancsfájlba, ezért a rendszer a telepített többi bővítménnyel kapcsolatos problémákat okoz, és az újraindítás után a bővítmény nem fog folytatódni az újraindítás után. 
* Ha olyan szkripttel rendelkezik, amely újraindítást eredményez, telepítse az alkalmazásokat, és futtassa a parancsfájlokat stb. Be kell ütemezni az újraindítást egy cron-feladatokkal, vagy olyan eszközök használatával, mint a DSC, a Chef vagy a Puppet Extensions.
* A bővítmény csak egyszer futtatja a parancsfájlt, ha parancsfájlt szeretne futtatni minden rendszerindításkor, használhatja a [Cloud-init rendszerképet](../linux/using-cloud-init.md) , és használhat [parancsfájlokat rendszerindítási](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulként. Azt is megteheti, hogy a parancsfájl segítségével létrehoz egy rendszerszintű szolgáltatási egységet.
* Ha egy parancsfájl futását szeretné ütemezni, a bővítmény használatával hozzon létre egy cron-feladatot.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha egy futó parancsfájl gyakoribb frissítési állapotát szeretné használni, létre kell hoznia a saját megoldását.
* Az egyéni szkriptek bővítménye nem támogatja natív módon a proxykiszolgálók használatát, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlban lévő proxykiszolgálót, például a *curl*-t.
* Ne feledje, hogy a parancsfájlok vagy parancsok által felhasználható nem alapértelmezett címtárbeli helyeknek van logikája a kezeléséhez.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ezt a konfigurációt a konfigurációs fájlokban tárolhatja, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager sablonban. 

A bizalmas adatokat egy védett konfigurációban tárolhatja, amely titkosítva van, és csak a virtuális gépen végez visszafejtést. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kódokat, például jelszót tartalmaz.

Ezeket az elemeket bizalmas adatokként kell kezelni, és meg kell adni a bővítmények által védett beállítások konfigurációjában. Az Azure virtuálisgép-bővítmény védett beállítási adatbeállításai titkosítottak, és csak a célként megadott virtuális gépen lettek visszafejtve.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Tulajdonságértékek

| Name (Név) | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft. OSTCExtensions | sztring |
| type | CustomScriptForLinux | sztring |
| typeHandlerVersion | 1.5 | int |
| fileUris (például) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például) | Python- \<MyPythonScript.py My-param1\> | sztring |
| enableInternalDNSCheck | igaz | logikai |
| storageAccountName (például) | examplestorageacct | sztring |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | sztring |

### <a name="property-value-details"></a>Tulajdonság értékének részletei

* `fileUris`: (opcionális, karakterlánc-tömb) a parancsfájlok URI-listája
* `enableInternalDNSCheck`: (nem kötelező, bool) alapértelmezett értéke TRUE (hamis) – a DNS-ellenőrzés letiltásához false értékre kell állítani.
* `commandToExecute`: (nem kötelező, karakterlánc) a végrehajtandó BelépésiPont-szkript
* `storageAccountName`: (nem kötelező, karakterlánc) a Storage-fiók neve
* `storageAccountKey`: (nem kötelező, karakterlánc) a Storage-fiók elérési kulcsa

A következő értékek a nyilvános és a védett beállításokban is megadhatók, ezért a nyilvános és a védett beállításokban nem kell megadni ezeket az értékeket.

* `commandToExecute`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de erősen ajánlott a védett beállítások használata.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el arra a virtuális gépre, amelyen a parancsfájl végre lesz hajtva.  A védett beállítások titkosítása csak az Azure-ban és a virtuális gépen ismert kulcs használatával történik. A rendszer elküldte a beállításokat a virtuális gépre, például ha a beállítások titkosítva vannak, a virtuális gépen titkosítva lesznek mentve. A titkosított értékek visszafejtéséhez használt tanúsítványt a virtuális gépen tárolja a rendszer, és a beállításokat (ha szükséges) a futtatáskor használja.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal is üzembe helyezhetők. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az egyéni parancsfájl-bővítmény futtatásához Azure Resource Manager sablon központi telepítésekor.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Ezek a tulajdonságok nevei megkülönböztetik a kis-és nagybetűket. Az üzembe helyezési problémák elkerüléséhez használja az itt látható neveket.

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával futtatja az egyéni szkriptek bővítményét, hozzon létre egy konfigurációs fájlt vagy fájlt. Legalább a "commandToExecute" értékkel kell rendelkeznie.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

A parancs beállításait JSON formátumú karakterláncként is megadhatja. Ez lehetővé teszi, hogy a konfiguráció a végrehajtás során legyen meghatározva, külön konfigurációs fájl nélkül.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-no-script-file"></a>Parancsfájl nélküli nyilvános konfiguráció

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Nyilvános és védett konfigurációs fájlok

A parancsfájl URI azonosítójának megadásához egy nyilvános konfigurációs fájlt kell használnia. A futtatni kívánt parancs megadásához egy védett konfigurációs fájlt kell használnia.

Nyilvános konfigurációs fájl:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Védett konfigurációs fájl:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI-parancs:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Hibaelhárítás

Az egyéni szkriptek bővítményének futtatásakor a szkript az alábbi példához hasonló könyvtárba jön létre vagy töltődik le. A parancs kimenete is ebbe a könyvtárba kerül a `stdout` és `stderr` a fájlokba.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

A hibakereséshez először ellenőrizze a Linux-ügynök naplóját, győződjön meg arról, hogy a bővítmény futott, majd ellenőrizze a következőt:

```bash
/var/log/waagent.log
```

Meg kell keresnie a bővítmények végrehajtását, így a következőképpen fog kinézni:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Néhány Megjegyzés:

1. Az engedélyezés az, amikor a parancs elindul.
1. A letöltés az Azure-ból származó CustomScript-bővítmény-csomag letöltésére vonatkozik, nem a fileUris megadott parancsfájl-fájlokra.
1. Azt is megtekintheti, hogy melyik naplófájlba írja a rendszer`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

A következő lépés a naplófájl bejelölése, ez a formátum:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Meg kell keresnie az egyéni végrehajtást, így a következőképpen fog kinézni:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Itt láthatja:

* Az enable parancs indítása ez a napló
* A bővítménynek átadott beállítások
* A kiterjesztés fájljának letöltése és eredménye.
* A futtatandó parancs és az eredmény.

Az egyéni szkriptek bővítményének végrehajtási állapotát az Azure CLI használatával is lekérheti:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimenet a következő szöveghez hasonlít:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>További lépések

A kód, az aktuális problémák és a verziók megtekintéséhez lásd: [CustomScript Extension repo](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
