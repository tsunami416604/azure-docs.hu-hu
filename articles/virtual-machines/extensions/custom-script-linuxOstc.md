---
title: Egyéni parancsfájlok futtathatók az Azure-ban Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Az egyéni szkriptek futtatására szolgáló bővítmény v1 használatával Linux rendszerű virtuális gép konfigurációs feladatok automatizálása
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
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: b88d850b708a10d0e0fdff2f54b68cb9b39988f5
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059478"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Az Azure egyéni parancsfájl bővítmény 1. verzió használata Linux rendszerű virtuális gépek

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Az egyéni szkript bővítmény verziója 1 letölti és futtatja a parancsfájlokat, Azure-beli virtuális gépeken. A bővítmény az üzembe helyezés utáni konfigurációs, a szoftver telepítése vagy bármely egyéb konfigurációs/felügyeleti feladat hasznos. Parancsfájlokat tölthet le az Azure Storage vagy egy másik elérhető-e internet-helyre, vagy megadhatja azokat a bővítmény-futtatókörnyezet.

Az egyéni Szkriptbővítmény integrálható az Azure Resource Manager-sablonok. Is futtathatja, Azure CLI-vel, PowerShell, az Azure Portalon vagy az Azure Virtual Machines – REST API használatával.

Ez a cikk részletezi, hogyan használhatja az egyéni szkriptek bővítménye az Azure CLI-vel, és a bővítmény futtatása az Azure Resource Manager-sablon használatával. Ez a cikk a Linux rendszerek hibaelhárítási lépéseket is nyújt.

Nincsenek egyéni parancsfájl-kiterjesztés két Linux:

* 1. verzió – Microsoft.OSTCExtensions.CustomScriptForLinux

* 2. verzió – Microsoft.Azure.Extensions.CustomScript

Váltson a meglévő és újonnan üzembe helyezett új verziója használatára ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) helyett. Az új verzióra van készült protokollkompatibilitását. Ezért az áttelepítés megírásához módosítása a neve és verziója, nem kell módosítani a bővítmény konfigurációját.

### <a name="operating-system"></a>Operációs rendszer

Támogatott Linux-Disztribúciók:

* CentOS 6.5-ös vagy újabb
* Debian 8 és újabb
  * Debian 8.7 nem tartalmaz Python2 a legújabb képek, amelyek CustomScriptForLinux működésképtelenné válik.
* FreeBSD
* Az OpenSUSE 13.1 és magasabb
* Oracle Linux 6.4-es és újabb verziók
* SUSE Linux Enterprise Server 11 SP3 vagy újabb
* Ubuntu 12.04 és magasabb

### <a name="script-location"></a>A parancsprogram helye

A bővítmény használhatja az Azure Blob storage hitelesítő adatai az Azure Blob storage elérésére használhat. Másik lehetőségként a parancsprogram helyét lehet bármelyik where, mindaddig, amíg a virtuális gép irányíthatja a végpontot, például a GitHub, a belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat

Ha le kell töltenie egy parancsfájlt kívülről például a GitHub vagy az Azure Storage, majd további tűzfal/hálózati biztonsági csoportra portokat kell megnyitni. Például ha a parancsfájl található az Azure Storage szolgáltatásban, engedélyezheti a hozzáférés Azure NSG szolgáltatás címkék használatával [tárolási](../../virtual-network/security-overview.md#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, akkor továbbra is szükség lehet további tűzfal/hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök

* Ehhez a kiterjesztéshez legmagasabb Hibaarány miatt a parancsfájl tesztelési hiba nélkül fut a parancsfájl szintaxishibáinak és is helyezheti további jelentkezik be, hogy könnyebben megtalálja, ahol nem sikerült a parancsfájl.
* Parancsfájlokat írhat, amelyek idempotensek, így az első futás egynél többször véletlenül, ha módosításokat a rendszer nem okoz.
* Győződjön meg arról, a parancsfájlok nem igényelnek felhasználói bevitel futtatás esetén.
* A parancsfájl futtatása engedélyezett 90 perc, semmit hosszabb egy sikertelen a bővítmény kiépítése eredményez.
* Ne telepítse a parancsfájl belül újraindul, ez lesz okozhat problémát más bővítményeket, amelyek telepítése folyamatban van, és utáni újraindítás, a bővítmény nem továbbra is az újraindítás után. 
* Ha egy parancsfájl, amely hatására az újraindítás, alkalmazásokat telepíthet, és futtassa a parancsfájlok stb. Olyan időszakra ütemezze az újraindítás egy Cron feladat használatával, vagy a DSC, vagy a Chef, Puppet, bővítmények.
* A bővítmény csak akkor fog futni egy parancsfájl egyszer, ha azt szeretné, naplózásra kerül minden rendszerindításkor, a parancsfájl futtatásához használhatja [a cloud-init lemezkép](../linux/using-cloud-init.md) , és egy [parancsfájlokat egy rendszerindító](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. A parancsfájl segítségével azt is megteheti, hozzon létre egy Systemd szolgáltatás egységet.
* Ha azt szeretné, meghatározhatja, mikor egy parancsprogram futtatása, a bővítmény egy Cron feladat létrehozásához használjon.
* A szkript fut, amikor egy "átviteli" bővítmény állapotát az Azure Portalon vagy a parancssori felület csak akkor jelenik meg. Ha azt szeretné, hogy a futó parancsfájl gyakoribb ügyfélállapot-frissítés, szüksége lesz a saját megoldást hozhat létre.
* Egyéni szkriptek futtatására szolgáló bővítmény nem támogatja natív módon proxykiszolgálókat, azonban egy fájlátviteli eszköz, amely támogatja a proxykiszolgálók a parancsfájlban, például használhatja *Curl*.
* Vegye figyelembe, hogy a parancsfájlok vagy parancsok függő előfordulhat, hogy nem alapértelmezett directory-helyeken, a logikai ennek kezelése rendelkezik.

## <a name="extension-schema"></a>Bővítményséma

Az egyéni Szkriptbővítmény konfigurációját meghatározza többek között a parancsprogram helye és a futtatandó parancsot. Konfigurációs fájlban tárolhatja az ebben a konfigurációban, adja meg azt a parancssorból, vagy adja meg az Azure Resource Manager-sablon. 

Egy védett konfigurációban, amely titkosított, és csak a virtuális gépen belül visszafejteni bizalmas adatokat tárolhat. A védett konfiguráció akkor hasznos, ha a végrehajtás parancs tartalmazza a titkos kulcsokat például a jelszó.

Ezeket az elemeket kell kezelni, mint a bizalmas adatok és a bővítmények védett beállítás konfigurációjában megadott. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen.

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.OSTCExtensions | sztring |
| type | CustomScriptForLinux | sztring |
| typeHandlerVersion | 1.5 | int |
| fileUris (például:) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például:) | Python MyPythonScript.py \<saját param1\> | sztring |
| enableInternalDNSCheck | true | logikai |
| storageAccountName (például:) | examplestorageacct | sztring |
| storageAccountKey (például:) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sztring |

### <a name="property-value-details"></a>A tulajdonság értéke részletei

* `fileUris`: (nem kötelező, csak karakterlánc-tömbben) a parancsfájlok uri listája
* `enableInternalDNSCheck`: (nem kötelező, logikai) alapértelmezett értéke igaz, hamis értékre van állítva, a DNS-ellenőrzés letiltása.
* `commandToExecute`: (nem kötelező, string) a belépési pont parancsfájl végrehajtása
* `storageAccountName`: (nem kötelező, string) a tárfiók neve
* `storageAccountKey`: (nem kötelező, string) storage-fiók hozzáférési kulcsa

A következő értékeket a nyilvános vagy védett beállítások beállítható, nyilvános és a védett beállítás kell nem rendelkezik ezekkel az értékekkel, set alatt.

* `commandToExecute`

Hibakeresés, de hasznos lehet nyilvános beállítások használata erősen ajánlott, hogy védett beállításokat használja.

Nyilvános beállításokat a virtuális gép, ahol a parancsfájl végrehajtása szövegként érkeznek.  Védett beállítások vannak titkosítva, csak az Azure és a virtuális gép ismert kulcsot használ. A beállítások lesznek mentve a virtuális géphez, mivel lettek küldve, vagyis ha a beállítások titkosított menti őket a titkosított a virtuális gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális gépen, és fejti vissza a futásidejű beállításokat (ha szükséges).

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémájában az egyéni szkriptek futtatására szolgáló bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható.

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
>Ezek a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevek itt látható módon.

## <a name="azure-cli"></a>Azure CLI

Azure CLI-vel használatakor az egyéni szkriptek futtatására szolgáló bővítmény futtatása hozzon létre egy konfigurációs fájlt vagy fájlokat. Minimális "commandToExecute" kell rendelkeznie.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Opcionálisan megadhatja a beállításokat a parancsban formázott JSON-karakterláncot. Ez lehetővé teszi, hogy a konfigurációt a végrehajtás során, és a egy külön konfigurációs fájlt nélkül adható meg.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-no-script-file"></a>Nyilvános konfiguráció nincs parancsfájlt

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

Egy nyilvános konfigurációs fájl használatával adja meg a parancsfájl URI azonosítója. Egy védett konfigurációs fájl használatával adja meg a futtatandó parancsot.

Nyilvános konfigurációs fájlt:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Védett konfigurációs fájlt:  

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

Az egyéni Szkriptbővítmény futtatásakor a szkript a létrehozott vagy letöltött, egy olyan könyvtárba, amely az alábbi példához hasonló. A parancs kimenete is ebben a könyvtárban, a mentett `stdout` és `stderr` fájlokat.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Hibaelhárítás, először a Linux-ügynök naplóban, győződjön meg arról, a bővítmény futtatta, ellenőrizze:

```bash
/var/log/waagent.log
```

Bővítmény végrehajtása kell keresnie, a következőhöz hasonlóan kell kinéznie:

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

Megjegyzés: bizonyos szempontok:

1. Engedélyezése esetén az, hogy a parancs futtatásának megkezdése.
1. Letöltési vonatkozik, a CustomScript bővítmény csomag letöltése az Azure-ban, a parancsfájlok nem megadott fileUris.
1. Emellett megtekintheti azt írja ki, hogy melyik naplófájlban`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
`

Következő lépés az, hogy go-ellenőrzés a naplófájlt, ez a következő formátumban:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Meg kell keresnie az induvidual végrehajtását, a következőhöz hasonlóan kell kinéznie:

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

Itt láthatja:

* Ez a napló engedélyezése parancs kezdési.
* A bővítmény átadott beállítások
* A bővítmény letöltése a fájl- és az eredmény az adott.
* A parancs futtatása és az eredmény.

Azure CLI-vel is lekérhet az egyéni Szkriptbővítmény végrehajtási állapotát:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A kimenet hasonlít az alábbi szöveget:

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>További lépések

A kód, az aktuális problémák és a verziók megtekintéséhez lásd: [CustomScript bővítménnyel tárház](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
