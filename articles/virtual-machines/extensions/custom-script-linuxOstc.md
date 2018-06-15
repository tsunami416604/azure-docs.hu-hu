---
title: Egyéni parancsfájlok futtathatók a Linux virtuális gépek Azure-ban |} Microsoft Docs
description: Az egyéni parancsprogramok futtatására szolgáló bővítmény v1 használatával Linux virtuális gép konfigurációs feladatok automatizálásához
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: eac64a5b456eb040bcb1ac01c3c86dfde0847e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942773"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Az Azure egyéni parancsfájl kiterjesztése 1-es használata a Linux virtuális gépek
Az egyéni parancsfájl bővítmény verzió 1 tölti le, és futtatja a parancsfájlokat, az Azure virtuális gépeken. A bővítmény akkor hasznos, a telepítés utáni konfigurációjának, a szoftver telepítése vagy a bármely egyéb konfigurációs /-felügyeleti feladatot. Azure Storage vagy más elérhető internetes helyről töltheti parancsfájlok, vagy megadhatja azokat a bővítmény futásidejű. 

Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok. Is futtathatja az Azure parancssori felület, PowerShell, az Azure-portálon vagy az Azure virtuális gépek REST API használatával.

Ez a cikk részletezi az Azure parancssori Felületet az egyéni parancsprogramok futtatására szolgáló bővítmény használatával, és a bővítmény futtatása az Azure Resource Manager-sablon használatával. Ez a cikk a Linux rendszerek a hibaelhárítási lépéseket is biztosítja.


Egyéni parancsfájl-kiterjesztés két Linux van:
* 1 - Microsoft.OSTCExtensions.CustomScriptForLinux verzió
* 2 - Microsoft.Azure.Extensions.CustomScript verzió

Váltson a meglévő és új központi telepítésére az új verziót használja ([Microsoft.Azure.Extensions.CustomScript](\custom-script-linux.md)) helyett. Az új verzió szándék szerint Esőcsepp helyettesíti. Ezért az áttelepítés lehető legkönnyebben módosítása a nevét és verzióját, akkor nem kell módosítania a bővítmény konfigurációja.

 

### <a name="operating-system"></a>Operációs rendszer
Támogatott Linux Terjesztéseket:

- CentOS 6.5-ös vagy újabb
- Debian 8 és újabb rendszer
    - Debian 8.7 nem tartalmaz Python2 a legújabb képek, amely CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13.1 és újabb rendszer
- Oracle Linux 6.4-es és újabb rendszer
- SUSE Linux Enterprise Server 11 SP3 és újabb rendszer
- Ubuntu 12.04 és újabb rendszer

### <a name="script-location"></a>Parancsfájl helyét

A bővítmény használhatja az Azure Blob storage hitelesítő adatok Azure Blob-tároló elérésére használhat. Alternatív megoldásként a parancsfájl helyét lehet bármely where, mindaddig, amíg a virtuális gép irányíthatja a végpontot, például a github webhelyen, a belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat
Ha le kell töltenie a külsőleg a Githubból vagy az Azure Storage például egy parancsfájlt, majd további tűzfal/hálózati biztonsági csoportjának portokat kell megnyitni. Például ha a parancsfájl az Azure Storage található, engedélyezheti a Azure NSG szolgáltatás címkék segítségével érhető [tárolási](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, akkor lehet szükség további tűzfal/hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök
* Ehhez a kiterjesztéshez legmagasabb hibaaránya okozza-e a parancsfájl, a parancsfájl hiba nélkül fut teszt szintaktikai hibákat, és is be további, a parancsfájl használatával könnyebben találja, ahol sikertelen bejelentkezés.
* Az idempotent, amelyek parancsfájlokat írhat, az beszerzése futásuk egynél többször véletlenül, ha rendszer módosítása nem okoz.
* Győződjön meg arról, a parancsfájlok nem igényelnek felhasználói beavatkozást futtatásakor.
* A parancsfájl futtatásához engedélyezett 90 perc, semmit hosszabb ideig sikertelen nyújtását a bővítmény eredményez.
* A parancsfájl belül újraindítások ne helyezze, ennek hatására problémákat más kiterjesztésű fájl, amelyek telepítése folyamatban van, és utáni újraindítás, a bővítmény nem fog az újraindítás után. 
* Ha olyan parancsfájlt, amely hatására a számítógép újraindítása, alkalmazásokat telepíteni, és futtassa a parancsfájlok stb. Az újraindítás Cron feladat használatával, vagy eszközökkel, például vagy a DSC, Chef, Puppet, bővítmények kell ütemezni.
* A kiterjesztés csak akkor fog futni egy parancsfájl egyszer, ha szeretné parancsprogram futtatása minden rendszerindításkor, akkor [felhő inicializálás kép](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) , és egy [parancsfájlok / rendszerindító](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. A parancsfájl segítségével azt is megteheti, hozzon létre egy Systemd szolgáltatás egységet.
* Ha szeretne ütemezni, amikor a parancsprogram futni fog, a bővítmény Cron feladat létrehozásához kell használnia. 
* Csak akkor jelenik meg a "átviteli" bővítmény állapotát az Azure-portálon vagy a parancssori felület, a parancsfájl futása közben. Ha azt szeretné, hogy a futó parancsfájl gyakoribb állapotfrissítéseit, szüksége lesz a saját megoldás létrehozásához.
* Egyéni parancsprogramok futtatására szolgáló bővítmény natív módon támogatja a proxykiszolgálót, azonban a fájl adatátviteli eszközzel, mint a parancsfájl-proxy kiszolgálók támogató *Curl*. 
* Ügyeljen a nem alapértelmezett directory-helyeken a parancsfájlok vagy parancsok hivatkozhatnak, kell kezelni a programot.



## <a name="extension-schema"></a>Bővítményséma

Az egyéni parancsprogramok futtatására szolgáló bővítmény konfigurációja határozza meg, többek között a parancsfájl helyét és a futtatandó parancsot. Ebben a konfigurációban tárolható konfigurációs fájlok, a parancssorban adja meg azt, vagy adja meg az Azure Resource Manager-sablonok. 

Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belüli visszafejteni bizalmas adatokat is tárolhatja. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs tartalmazza a titkos kulcsokat például a jelszót.

Legyen bizalmas adatokat a rendszer ezeket az elemeket, és a bővítmények védett beállítás konfigurációjában megadott. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen.

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

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.OSTCExtensions | karakterlánc |
| type | CustomScriptForLinux | karakterlánc |
| typeHandlerVersion | 1.5 | int |
| fileUris (például) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például) | Python MyPythonScript.py < saját paraméter1 > | karakterlánc |
| enableInternalDNSCheck | true | logikai |
| storageAccountName (például) | examplestorageacct | karakterlánc |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | karakterlánc |

### <a name="property-value-details"></a>A tulajdonság értékének részletei
* `fileUris`: (nem kötelező, karakterlánc-tömbben) a parancsfájl uri listája
* `enableInternalDNSCheck`: (nem kötelező, logikai) alapértelmezett értéke igaz, a DNS-ellenőrzés letiltásához állítsa hamis értékre.
* `commandToExecute`: (nem kötelező, karakterlánc) a belépési pont parancsprogram végrehajtására szolgáló
* `storageAccountName`: (nem kötelező, karakterlánc) storage-fiók neve
* `storageAccountKey`: (nem kötelező, karakterlánc) storage-fiók hozzáférési kulcsának

A következő értékeket a nyilvános vagy védett beállításokat lehet megadni, nem lehet ezeket az értékeket set alatt található nyilvános és a védett beállításait.
* `commandToExecute`

Hibakeresés, de hasznos lehet, hogy nyilvános beállítások használata erősen ajánlott védett beállítások használata.

Nyilvános beállításai küldése nyílt szövegben a virtuális gép, ha a parancsfájl végrehajtása a.  Védett beállítások csak az Azure és a virtuális gép ismert kulccsal titkosított. A beállítások mentése a virtuális Gépet, a küldés, vagyis ha a beállítások titkosított mentett titkosított a virtuális Gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális Gépre, és beállításokat (ha szükséges) futásidőben visszafejtésére szolgáló jelszó.


## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. 


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
>A tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevét itt látható módon.

## <a name="azure-cli"></a>Azure CLI
Amikor az Azure parancssori felület használja az egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához, hozzon létre egy konfigurációs fájl vagy fájlokat. Legalább a "commandToExecute" kell rendelkeznie.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Ha szükséges megadhatja a beállításokat a parancsban JSON formátumú karakterlánc. Ez lehetővé teszi a konfigurációját, és végrehajtása során, és egy külön konfigurációs fájl nem adható meg.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-no-script-file"></a>Nyilvános nincs parancsfájl-konfiguráció

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Nyilvános és a védett konfigurációs fájlok

Egy nyilvános konfigurációs fájl segítségével adja meg a parancsfájl URI azonosítója. Egy védett konfigurációs fájl segítségével adja meg a futtatandó parancsot.

Nyilvános konfigurációs fájlban:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Védett konfigurációs fájlban:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Az Azure CLI-parancsot:

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
Ha az egyéni parancsprogramok futtatására szolgáló bővítmény fut, a parancsfájl létrehozásakor vagy egy olyan könyvtárba, az alábbi példához hasonló le. A parancs kimenetében is menti a könyvtárba, amely a `stdout` és `stderr` fájlokat. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Hibaelhárítás, először ellenőrizze a Linux-ügynök naplóját, győződjön meg arról, a bővítmény futott, ellenőrizze:

```bash
/var/log/waagent.log 
```

A bővítmény végrehajtási kell keresnie, a következőhöz hasonlóan fog kinézni:
```text
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
Megjegyzés: egyes szempontok:
1. Engedélyezése esetén az, hogy a parancs futásának indításakor.
2. Letöltési vonatkozik, az Azure-ból a CustomScript bővítmény csomag letöltése nem a parancsfájlok fileUris megadott.
3. Megtekintheti, hogy mely naplófájl a "/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log" írásakor.

Következő lépés az, hogy egy ellenőrzést nyissa meg a naplófájlt, ez a formátum:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

A induvidual végrehajtási kell keresnie, a következőhöz hasonlóan fog kinézni:
```text
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
* Ez a napló engedélyezése parancs kezdési.
* A bővítmény átadott beállítások
* A bővítmény letöltése a fájl- és eredményét.
* A parancs futtatása és az eredményt.

Az egyéni parancsprogramok futtatására szolgáló bővítmény végrehajtási állapotát is Azure parancssori felület használatával kérheti le:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimeneti néz ki a következő szöveget:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>További lépések
A kód, aktuális problémák és verziók, olvassa el [CustomScript bővítménnyel tárház](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

