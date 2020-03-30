---
title: Egyéni parancsfájlok futtatása Linuxos virtuális gépeken az Azure-ban
description: Linuxos virtuálisgép-konfigurációs feladatok automatizálása az egyéni parancsfájl-bővítmény v1 használatával
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535408"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Az Azure Custom Script Extension 1-es verziója Linux-alapú virtuális gépekkel

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Az egyéni parancsfájl-bővítmény 1-es verziója letölti és futtatja a parancsfájlokat az Azure virtuális gépeken. Ez a bővítmény a telepítés utáni konfigurációhoz, a szoftvertelepítéshez vagy bármely más konfigurációs/felügyeleti feladathoz hasznos. Letöltheti a parancsfájlokat az Azure Storage-ból vagy más elérhető internetes helyről, vagy megadhatja őket a bővítmény futásidejéhez.

Az egyéni parancsfájl-bővítmény integrálható az Azure Resource Manager-sablonokkal. Azure CLI, PowerShell, az Azure Portal vagy az Azure Virtual Machines REST API használatával is futtathatja.

Ez a cikk ismerteti, hogyan használhatja az Azure CLI egyéni parancsfájl-bővítményét, és hogyan futtathatja a bővítményt egy Azure Resource Manager-sablon használatával. Ez a cikk a Linux-rendszerek hibaelhárítási lépéseit is ismerteti.

Két Linux egyéni parancsfájl-bővítmény létezik:

* 1.verzió - Microsoft.OSTCExtensions.CustomScriptForLinux

* 2.-as verzió - Microsoft.Azure.Extensions.CustomScript

Váltson új és meglévő központi telepítéseket az új verzió ([Microsoft.Azure.Extensions.CustomScript)](custom-script-linux.md)használatához. Az új verzió a régi kiváltására készült. A migrálásához így elég megváltoztatni a nevet és a verziót, nincs szükség a bővítménykonfiguráció módosítására.

### <a name="operating-system"></a>Operációs rendszer

Támogatott Linux disztribúciók:

* CentOS 6.5 és újabb
* Debian 8 és újabb
  * A Debian 8.7 nem szállít Python2-t a legújabb képeken, ami megtöri a CustomScriptForLinux-ot.
* FreeBSD
* OpenSUSE 13.1 és újabb
* Oracle Linux 6.4 és újabb
* SUSE Linux Enterprise Server 11 SP3 és újabb verziók
* Ubuntu 12.04 és újabb

### <a name="script-location"></a>Parancsfájl helye

A bővítmény segítségével használhatja az Azure Blob storage hitelesítő adatait, az Azure Blob storage eléréséhez. Másik lehetőségként a parancsfájl helye lehet olyan, ahol, mindaddig, amíg a virtuális gép útvonala, hogy a végpont, például a GitHub, belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubot vagy az Azure Storage-ot, akkor további tűzfal-/hálózati biztonsági csoportportokat kell megnyitni. Ha például a parancsfájl az Azure Storage-ban található, engedélyezheti a hozzáférést az Azure NSG szolgáltatáscímkék [tárolóhoz](../../virtual-network/security-overview.md#service-tags)használatával.

Ha a parancsfájl helyi kiszolgálón található, akkor további tűzfal-/hálózati biztonságicsoport-portokat kell megnyitni.

### <a name="tips-and-tricks"></a>Tippek és trükkök

* A bővítmény meghibásodásai legnagyobb arányban a szkriptben található szintaktikai hibák miatt jelentkeznek. Tesztelje, hogy a szkript fut-e hiba nélkül, és lássa el további naplózással a szkriptet, hogy könnyebb legyen a hiba helyét azonosítani.
* Írjon idempotens szkripteket, hogy ne okozzanak változásokat a rendszerben, ha véletlenül többször is futtatják őket.
* Győződjön meg arról, hogy a parancsfájlok futtatásához nincs szükség felhasználói beavatkozásra.
* A parancsfájl futtatásához 90 min s engedélyezett, a hosszabb idő a bővítmény sikertelen biztosítását eredményezi.
* Ne tegye újraindítás belül a forgatókönyvet, ez problémákat okoz más kiterjesztések, amelyek telepítése, és a post reboot, a kiterjesztés nem folytatódik az újraindítás után. 
* Ha van egy script, ami miatt újraindul, majd alkalmazásokat telepíteni, és fuss szkriptek, stb Az újraindítást cronfeladathasználatával, vagy olyan eszközökkel kell ütemezni, mint a DSC vagy a Chef, Puppet bővítmények.
* A bővítmény csak egyszer futtat egy parancsfájlt, ha minden rendszerindításkor parancsfájlt szeretne futtatni, akkor használhatja a [felhő-init lemezképet,](../linux/using-cloud-init.md) és használhatja a [Parancsfájlok rendszerindításon ként modult.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Másik lehetőségként a parancsfájl segítségével systemd szolgáltatási egységet hozhat létre.
* Ha azt szeretné ütemezni, hogy mikor fog futni egy parancsfájl, a bővítmény segítségével hozzon létre egy Cron-feladatot.
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha egy futó parancsfájl gyakoribb állapotfrissítéseit szeretné, saját megoldást kell létrehoznia.
* Az egyéni parancsfájl-kiterjesztés nem támogatja natívmódon a proxykiszolgálókat, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlon belüli proxykiszolgálókat, például *a Curl*.
* Ne feledje, hogy a parancsfájlok vagy parancsok nem alapértelmezett könyvtárhelyeket, amelyeken a parancsfájlok vagy parancsok támaszkodhatnak, legyen logika imitátora ennek kezeléséhez.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni parancsfájl-bővítmény konfigurációja olyan dolgokat határoz meg, mint a parancsfájl helye és a futtatandó parancs. Ezt a konfigurációt tárolhatja konfigurációs fájlokban, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager-sablonban. 

A bizalmas adatokat védett konfigurációban tárolhatja, amely titkosított, és csak a virtuális gépen belül van visszafejtve. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kulcsokat, például egy jelszót tartalmaz.

Ezeket az elemeket bizalmas adatként kell kezelni, és a bővítmények védett beállítási konfigurációjában meg kell adni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza.

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

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.OSTCExtensions | sztring |
| type | CustomScriptForLinux | sztring |
| typeHandlerVersion | 1.5 | int |
| fileUris (pl. | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (pl. | python \<MyPythonScript.py én-param1\> | sztring |
| enableInternalDNSCheck | igaz | logikai |
| storageAccountName (pl. | példatárolásacct | sztring |
| storageAccountKey (pl. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg=== | sztring |

### <a name="property-value-details"></a>Ingatlan értékének részletei

* `fileUris`: (nem kötelező, string array) a szkriptek uri listája
* `enableInternalDNSCheck`: (nem kötelező, bool) alapértelmezett értéke True, értéke Hamis a DNS-ellenőrzés letiltásához.
* `commandToExecute`: (nem kötelező, karakterlánc) a végrehajtandó entrypoint parancsfájl
* `storageAccountName`: (nem kötelező, karakterlánc) a tárfiók neve
* `storageAccountKey`: (nem kötelező, karakterlánc) a tárfiók hozzáférési kulcsa

A következő értékek nyilvános vagy védett beállításokban állíthatók be, ezeket az értékeket nem állíthatja be nyilvános és védett beállításokban.

* `commandToExecute`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de erősen ajánlott a védett beállítások használata.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el a virtuális gépnek, ahol a parancsfájl végrehajtása megtörténik.  A védett beállítások titkosítva vannak egy csak az Azure és a virtuális gép által ismert kulcs használatával. A beállításokat a rendszer az elküldéskor menti a virtuális gépre, azaz ha a beállítások titkosítva vannak, akkor a virtuális gépen titkosítottan kerülnek mentésre. A titkosított értékek visszafejtéséhez használt tanúsítvány a virtuális gép tárolja, és a beállítások visszafejtésére (ha szükséges) futásidőben.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager-sablonban használható az egyéni parancsfájl-bővítmény futtatásához az Azure Resource Manager-sablon üzembe helyezése során.

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
>Ezek a tulajdonságnevek a kis- és nagybetűket nem érzékenyékelik. A telepítési problémák elkerülése érdekében használja az itt látható neveket.

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI-t használja az egyéni parancsfájl-bővítmény futtatásához, hozzon létre egy konfigurációs fájlt vagy fájlokat. Legalább a "commandToExecute" paraparancsot kell rendelkeznie.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

A parancs beállításait JSON formátumú karakterláncként is megadhatja. Ez lehetővé teszi a konfiguráció megadása a végrehajtás során, és anélkül, hogy egy külön konfigurációs fájlt.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-no-script-file"></a>Nyilvános konfiguráció parancsfájl fájl nélkül

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Nyilvános és védett konfigurációs fájlok

Nyilvános konfigurációs fájl segítségével adhatja meg a parancsfájl URI-ját. A futtatandó parancs megadásához védett konfigurációs fájlt használhat.

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

Azure CLI parancs:

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

Az egyéni parancsfájl-bővítmény futtatásakor a parancsfájl a következő példához hasonló könyvtárba jön létre vagy töltődik le. A parancs kimenetét is menti `stdout` `stderr` ebbe a könyvtárba és fájlokat.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

A hibaelhárításhoz először ellenőrizze a Linux-ügynöknaplót, győződjön meg róla, hogy a bővítmény futott, ellenőrizze:

```bash
/var/log/waagent.log
```

Meg kell keresni a kiterjesztés végrehajtását, akkor fog kinézni:

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

Néhány megjegyzés:

1. Az engedélyezés az, amikor a parancs elindul.
1. A letöltés a CustomScript-bővítménycsomag Azure-ból való letöltésére vonatkozik, nem pedig a fileUris-ban megadott parancsfájlokra.
1. Azt is láthatja, hogy melyik naplófájlt írja ki`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Következő lépés az, hogy menjen egy ellenőrizze a naplófájlt, ez a formátum:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Meg kell keresni az egyes végrehajtás, akkor fog kinézni:

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

Itt látható:

* Az Enable parancs indítása ez a napló
* A bővítménynek átadott beállítások
* A kiterjesztés letöltő reszelő reszelő és a eredmény -ból amit.
* A futtatott parancs és az eredmény.

Az egyéni parancsfájl-bővítmény végrehajtási állapotát az Azure CLI használatával is lekérheti:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimenet a következő szövegnek tűnik:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>További lépések

A kód, az aktuális problémák és verziók megtekintéséről a [CustomScript-bővítmény tártárja](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)látható.
