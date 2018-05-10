---
title: Egyéni parancsfájlok futtathatók a Linux virtuális gépek Azure-ban |} Microsoft Docs
description: Az egyéni parancsprogramok futtatására szolgáló bővítmény v2 a Linux virtuális gép konfigurációs feladatok automatizálásához
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: 3977aa16878ea1e2d808376165f551ce5fb8d00f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Az Azure egyéni parancsfájl kiterjesztése 2-es verzió használata a Linux virtuális gépek
A Custom Script bővítmény 2-es verzió tölti le, és futtatja a parancsfájlokat, az Azure virtuális gépeken. A bővítmény akkor hasznos, a telepítés utáni konfigurációjának, a szoftver telepítése vagy a bármely egyéb konfigurációs /-felügyeleti feladatot. Azure Storage vagy más elérhető internetes helyről töltheti parancsfájlok, vagy megadhatja azokat a bővítmény futásidejű. 

Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok. Is futtathatja az Azure parancssori felület, PowerShell, az Azure-portálon vagy az Azure virtuális gépek REST API használatával.

Ez a cikk részletezi az Azure parancssori Felületet az egyéni parancsprogramok futtatására szolgáló bővítmény használatával, és a bővítmény futtatása az Azure Resource Manager-sablon használatával. Ez a cikk a Linux rendszerek a hibaelhárítási lépéseket is biztosítja.


Egyéni parancsfájl-kiterjesztés két Linux van:
* 1 - Microsoft.OSTCExtensions.CustomScriptForLinux verzió
* 2 - Microsoft.Azure.Extensions.CustomScript verzió

Váltson a meglévő és új központi telepítésére használja helyette az új verzió 2. Az új verzió szándék szerint Esőcsepp helyettesíti. Ezért az áttelepítés lehető legkönnyebben módosítása a nevét és verzióját, akkor nem kell módosítania a bővítmény konfigurációja.


### <a name="operating-system"></a>Operációs rendszer

Az egyéni parancsprogramok futtatására szolgáló bővítmény Linux fog futni, a támogatott bővítmény kiterjesztést rendszerbeli, további információkért lásd: a [cikk](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":"123456789",          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa | Adattípus | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Compute.Extensions | karakterlánc |
| type | CustomScript | karakterlánc |
| typeHandlerVersion | 2.0 | int |
| fileUris (például) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például) | Python MyPythonScript.py < saját paraméter1 > | karakterlánc |
| Parancsfájl | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | karakterlánc |
| skipDos2Unix (például) | false | logikai |
| időbélyeg (például) | 123456789 | 32 bites egész szám |
| storageAccountName (például) | examplestorageacct | karakterlánc |
| storageAccountKey (például) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | karakterlánc |

### <a name="property-value-details"></a>A tulajdonság értékének részletei
* `skipDos2Unix`: (nem kötelező, logikai) kihagyja a parancsprogram-alapú fájl URL-címek vagy parancsfájl dos2unix átalakítás.
* `timestamp` Ebben a mezőben csak való a parancsprogram újra végre ez a mező értékének módosításával (nem kötelező, 32 bites egész szám) használja.  Bármely egész érték elfogadható; csak kell eltér az előző értéket.
 * `commandToExecute`: (**szükséges** Ha a parancsfájl nincs beállítva, karakterlánc) a belépési pont parancsfájl végrehajtására. Ehelyett használja ezt a mezőt, ha a parancs tartalmazza a titkos kulcsok, például jelszavakat.
* `script`: (**szükséges** Ha commandToExecute nincs beállítva, karakterlánc) a base64-kódolású (és választhatóan a gzip'ed) szkript/bin/megosztása.
* `fileUris`: (nem kötelező, karakterlánc-tömbben) URL-címéből (oka) t, le kell tölteni.
* `storageAccountName`: (nem kötelező, karakterlánc) storage-fiók nevét. Ha megadja a tároló hitelesítő adatait, az összes `fileUris` az Azure BLOB URL-címeket kell lennie.
* `storageAccountKey`: (nem kötelező, karakterlánc) storage-fiók hozzáférési kulcsának


A következő értékeket a nyilvános vagy védett beállításokat lehet megadni, a bővítmény elutasítják beállításra, amennyiben az alábbi értékeket a nyilvános és a védett beállításai megfelelőek-e.
* `commandToExecute`
* `script`
* `fileUris`

Hibakeresés, de hasznos lehet, hogy nyilvános beállítások használata erősen ajánlott védett beállítások használata.

Nyilvános beállításai küldése nyílt szövegben a virtuális gép, ha a parancsfájl végrehajtása a.  Védett beállítások csak az Azure és a virtuális gép ismert kulccsal titkosított. A beállítások mentése a virtuális Gépet, a küldés, vagyis ha a beállítások titkosított mentett titkosított a virtuális Gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális Gépre, és beállításokat (ha szükséges) futásidőben visszafejtésére szolgáló jelszó.

#### <a name="property-skipdos2unix"></a>Tulajdonság: skipDos2Unix

Az alapértelmezett értéke HAMIS, ami azt jelenti, hogy dos2unix átalakítás **van** végre.

CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, korábbi verzióját szeretné automatikusan átalakítása DOS fájlok UNIX fájlok lefordításával `\r\n` való `\n`. A fordítás továbbra is létezik, és alapértelmezés szerint be van kapcsolva. Az átalakításhoz érvényes fileUris vagy a parancsfájl-beállítást, az alábbi feltételek bármelyike alapján letöltött összes fájlt.

* Ha a kiterjesztés egyike `.sh`, `.txt`, `.py`, vagy `.pl` lesz konvertálva. A parancsfájl-beállítást mindig fog egyezni a feltétel alapján, mert feltételezett, hogy a parancsprogram végrehajtása /bin/sh, és a virtuális Gépre script.sh értékként menti.
* Ha a fájl kezdődik-e `#!`.

A dos2unix átalakítás figyelmen kívül hagyja a skipDos2Unix true értékre állításával.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Tulajdonság: parancsfájl

CustomScript felhasználói parancsfájl végrehajtását támogatja. A parancsprogram beállításait az commandToExecute és fileUris egyesítése egyetlen beállítással. Így ahelyett hogy beállítása az Azure storage vagy a Githubon gist letölthető fájl egyszerűen beállításként is kódolja a parancsfájlt. Parancsfájl kicserélt commandToExecute és fileUris használható.

A parancsfájl **kell** base64 kódolása.  A parancsfájl is **opcionálisan** gzip'ed lehet. A parancsfájl-beállítást a nyilvános vagy védett beállításai használható. A parancsfájl-paraméter adatok maximális mérete 256 KB. Ha a parancsfájl meghaladja ezt a méretet, akkor nem lesznek végrehajtva.

Például a fájl /script.sh/ mentve a következő parancsfájl megadott.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A megfelelő CustomScript parancsfájl beállítás alapul véve a következő parancs volna alakítható ki.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

A parancsprogram is lehet gzip'ed további csökkentése érdekében (a legtöbb esetben) mérete. (CustomScript automatikus-észleli gzip kizárja a tömörítés használatát.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript parancsfájl végrehajtása a következő algoritmust használja.

 1. ASSERT, a parancsfájl értéknek a hossza legfeljebb 256 KB.
 1. Base64 dekódolni a parancsfájl érték
 1. _Kísérlet történt_ való gunzip a base64 dekódolni érték
 1. a dekódolt (és nem kötelezően kibontott) érték írása a lemezre (/ var/lib/waagent/custom-script/#/script.sh)
 1. a parancsfájl _ / bin/sh - c /var/lib/waagent/custom-script/#/script.sh segítségével hajtható végre.


## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az egyéni parancsprogramok futtatására szolgáló bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Itt található, amely tartalmazza az egyéni parancsprogramok futtatására szolgáló bővítmény mintasablon [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/MyPythonScript.py"
      ]  
    }
  }
}
```

>[!NOTE]
>A tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevét itt látható módon.

## <a name="azure-cli"></a>Azure CLI
Amikor az Azure parancssori felület használja az egyéni parancsprogramok futtatására szolgáló bővítmény futtatásához, hozzon létre egy konfigurációs fájl vagy fájlokat. Legalább a "commandToExecute" kell rendelkeznie.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Ha szükséges megadhatja a beállításokat a parancsban JSON formátumú karakterlánc. Ez lehetővé teszi a konfigurációját, és végrehajtása során, és egy külön konfigurációs fájl nem adható meg.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-script-file"></a>Nyilvános parancsfájl-konfiguráció

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Az Azure CLI-parancsot:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Hibaelhárítás
Ha az egyéni parancsprogramok futtatására szolgáló bővítmény fut, a parancsfájl létrehozásakor vagy egy olyan könyvtárba, az alábbi példához hasonló le. A parancs kimenetében is menti a könyvtárba, amely a `stdout` és `stderr` fájlokat.

```bash
/var/lib/waagent/custom-script/download/0/
```

Hibaelhárítás, először ellenőrizze a Linux-ügynök naplóját, győződjön meg arról, a bővítmény futott, ellenőrizze:

```bash
/var/log/waagent.log 
```

A bővítmény végrehajtási kell keresnie, a következőhöz hasonlóan fog kinézni:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Megjegyzés: egyes szempontok:
1. Engedélyezése esetén az, hogy a parancs futásának indításakor.
2. Letöltési vonatkozik, az Azure-ból a CustomScript bővítmény csomag letöltése nem a parancsfájlok fileUris megadott.


Az Azure parancsprogramok futtatására szolgáló bővítmény hoz létre a napló, amely itt található:

```bash
/var/log/azure/custom-script/handler.log
```

A induvidual végrehajtási kell keresnie, a következőhöz hasonlóan fog kinézni:
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>További lépések
A kód, aktuális problémák és verziók, olvassa el [egyéni parancsfájl-kiterjesztés-linux tárház](https://github.com/Azure/custom-script-extension-linux).

