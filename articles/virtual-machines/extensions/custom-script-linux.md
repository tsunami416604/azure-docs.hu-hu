---
title: Egyéni parancsfájlok futtathatók az Azure-ban Linux rendszerű virtuális gépekhez |} A Microsoft Docs
description: Az egyéni szkriptek futtatására szolgáló bővítmény v2 használatával Linux rendszerű virtuális gép konfigurációs feladatok automatizálása
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: b8f343c2293df6a1dec808addf8881c27514fc06
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436648"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Az Azure Custom Script bővítmény 2-es verzió használata Linux rendszerű virtuális gépek
A Custom Script bővítmény 2-es verzió letölti és futtatja a parancsfájlokat, Azure-beli virtuális gépeken. A bővítmény az üzembe helyezés utáni konfigurációs, a szoftver telepítése vagy bármely egyéb konfigurációs/felügyeleti feladat hasznos. Parancsfájlokat tölthet le az Azure Storage vagy egy másik elérhető-e internet-helyre, vagy megadhatja azokat a bővítmény-futtatókörnyezet. 

Az egyéni Szkriptbővítmény integrálható az Azure Resource Manager-sablonok. Is futtathatja, Azure CLI-vel, a PowerShell vagy az Azure Virtual Machines – REST API használatával.

Ez a cikk részletezi, hogyan használhatja az egyéni szkriptek bővítménye az Azure CLI-vel, és a bővítmény futtatása az Azure Resource Manager-sablon használatával. Ez a cikk a Linux rendszerek hibaelhárítási lépéseket is nyújt.


Nincsenek egyéni parancsfájl-kiterjesztés két Linux:
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 - Microsoft.Azure.Extensions.CustomScript

Váltson a meglévő és újonnan üzembe helyezett, használja helyette a 2. új verziója. Az új verzióra van készült protokollkompatibilitását. Ezért az áttelepítés megírásához módosítása a neve és verziója, nem kell módosítani a bővítmény konfigurációját.


### <a name="operating-system"></a>Operációs rendszer

Az egyéni Szkriptbővítmény Linux fog futni a támogatott bővítmény bővítmény rendszerbeli, további információkért lásd: Ez [cikk](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>A parancsprogram helye

A bővítmény használhatja az Azure Blob storage hitelesítő adatai az Azure Blob storage elérésére használhat. Másik lehetőségként a parancsprogram helyét lehet bármelyik where, mindaddig, amíg a virtuális gép irányíthatja a végpontot, például a GitHub, a belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat
Ha le kell töltenie egy parancsfájlt kívülről például a GitHub vagy az Azure Storage, majd további tűzfal/hálózati biztonsági csoportra portokat kell megnyitni. Például ha a parancsfájl található az Azure Storage szolgáltatásban, engedélyezheti a hozzáférés Azure NSG szolgáltatás címkék használatával [tárolási](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Ha a parancsfájl egy helyi kiszolgálón, akkor továbbra is szükség lehet további tűzfal/hálózati biztonsági csoport portokat kell megnyitni.

### <a name="tips-and-tricks"></a>tippek és trükkök
* Ehhez a kiterjesztéshez legmagasabb Hibaarány miatt a parancsfájl tesztelési hiba nélkül fut a parancsfájl szintaxishibáinak és is helyezheti további jelentkezik be, hogy könnyebben megtalálja, ahol nem sikerült a parancsfájl.
* Parancsfájlokat írhat, amelyek idempotensek, így az első futás egynél többször véletlenül, ha módosításokat a rendszer nem okoz.
* Győződjön meg arról, a parancsfájlok nem igényelnek felhasználói bevitel futtatás esetén.
* A parancsfájl futtatása engedélyezett 90 perc, semmit hosszabb egy sikertelen a bővítmény kiépítése eredményez.
* Ne telepítse a parancsfájl belül újraindul, ez lesz okozhat problémát más bővítményeket, amelyek telepítése folyamatban van, és utáni újraindítás, a bővítmény nem továbbra is az újraindítás után. 
* Ha egy parancsfájl, amely hatására az újraindítás, alkalmazásokat telepíthet, és futtassa a parancsfájlok stb. Olyan időszakra ütemezze az újraindítás egy Cron feladat használatával, vagy a DSC, vagy a Chef, Puppet, bővítmények.
* A bővítmény csak akkor fog futni egy parancsfájl egyszer, ha azt szeretné, naplózásra kerül minden rendszerindításkor, a parancsfájl futtatásához használhatja [a cloud-init lemezkép](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) , és egy [parancsfájlokat egy rendszerindító](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. A parancsfájl segítségével azt is megteheti, hozzon létre egy Systemd szolgáltatás egységet.
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
  "type": "Microsoft.Compute/virtualMachines/extensions",
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
      "timestamp":123456789          
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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Compute.Extensions | sztring |
| type | CustomScript | sztring |
| typeHandlerVersion | 2.0 | int |
| fileUris (például:) | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (például:) | python MyPythonScript.py <my-param1> | sztring |
| parancsfájl | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | sztring |
| skipDos2Unix (például:) | false | logikai |
| időbélyegző (például:) | 123456789 | 32 bites egész szám |
| storageAccountName (e.g) | examplestorageacct | sztring |
| storageAccountKey (például:) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sztring |

### <a name="property-value-details"></a>A tulajdonság értéke részletei
* `skipDos2Unix`: (nem kötelező, boolean) dos2unix átalakítás parancsfájlalapú fájl URL-címek vagy parancsfájl kihagyja.
* `timestamp` Ebben a mezőben csak az a mező értékét módosítsa úgy a parancsprogram újra végre-trigger (nem kötelező, a 32 bites egész szám) használatát.  Minden olyan egész értéket az elfogadható; Ez csak különbözőnek kell lennie, mint a korábbi értéket.
 * `commandToExecute`: (**szükséges** , ha a parancsfájl nincs beállítva, string) végrehajtásához a BelépésiPont-szkriptet. Helyette használja ezt a mezőt, ha a parancs tartalmazza a titkos kulcsok, például jelszavakat.
* `script`: (**szükséges** Ha commandToExecute nincs beállítva, string) a base64-kódolású (és opcionálisan a gzip'ed) szkript által/bin/sh.
* `fileUris`: (nem kötelező, csak karakterlánc-tömbben) fájl(ok) le kell tölteni az URL-címeket.
* `storageAccountName`: (nem kötelező, string) a tárfiók nevére. Ha storage hitelesítő adatai, adja meg az összes `fileUris` URL-címeket kell lennie az Azure-Blobok.
* `storageAccountKey`: (nem kötelező, string) storage-fiók hozzáférési kulcsa


A következő értékeket nyilvános vagy védett beállítások is megadhatók, a bővítmény elutasítják semmilyen konfigurálást, ahol az alábbi értékek vannak beállítva, a nyilvános és a védett beállításaiban.
* `commandToExecute`
* `script`
* `fileUris`

Hibakeresés, de hasznos lehet nyilvános beállítások használata erősen ajánlott, hogy védett beállításokat használja.

Nyilvános beállításokat a virtuális gép, ahol a parancsfájl végrehajtása szövegként érkeznek.  Védett beállítások vannak titkosítva, csak az Azure és a virtuális gép ismert kulcsot használ. A beállítások lesznek mentve a virtuális géphez, mivel lettek küldve, vagyis ha a beállítások titkosított menti őket a titkosított a virtuális gépen. A tanúsítvány használatával fejthetők vissza a titkosított értékek tárolja a virtuális gépen, és fejti vissza a futásidejű beállításokat (ha szükséges).

#### <a name="property-skipdos2unix"></a>Tulajdonság: skipDos2Unix

Az alapértelmezett értéke FALSE (hamis), ami azt jelenti, hogy dos2unix átalakítás **van** végrehajtva.

CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, korábbi verzióját szeretné automatikusan átalakítása DOS fájlok UNIX fájlok lefordításával `\r\n` való `\n`. A fordítási továbbra is létezik, és alapértelmezés szerint be van kapcsolva. Ez a konverzió alkalmazza a rendszer fileUris vagy a parancsfájl-beállítást, a következő kritériumok alapján letöltött összes fájlt.

* Ha a bővítmény az egyik `.sh`, `.txt`, `.py`, vagy `.pl` át lesz alakítva. A parancsfájl-beállítást minden esetben egyezni fog a megadott feltételnek, mert a feltételezhető, hogy /bin/sh végrehajtani egy parancsfájlt, és a virtuális gép mentett script.sh.
* Ha a fájl kezdődik `#!`.

Az dos2unix átalakítás is kihagyja a skipDos2Unix igaz értékre állításával.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Tulajdonság: parancsfájl

CustomScript egy felhasználó által definiált parancsfájl végrehajtása támogatja. A parancsprogram beállításait commandToExecute és fileUris egyesítendő egy egyszeri beállítás. Nem állíthatja be az Azure storage- vagy GitHub gist letölthető egy fájl egyszerűen kódolhatja a parancsfájl-beállításként. Parancsfájl kicserélt commandToExecute és fileUris használható.

A parancsfájl **kell** base64 kódolású legyen.  A parancsfájl is **igény szerint** gzip'ed lehet. A parancsfájl-beállítást a nyilvános vagy védett beállítások használható. A parancsfájl-paraméter adatok maximális mérete 256 KB. Ha a parancsfájl meghaladja ezt a méretet, akkor nem lesz végrehajtva.

Ha például a következő parancsfájl menti a fájlt /script.sh/ megadott.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A megfelelő CustomScript parancsfájl-beállítást szeretné állítható össze a következő parancs végrehajtásával.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

A parancsfájl dönthet úgy, hogy gzip'ed további csökkentése érdekében a mérete (a legtöbb esetben). (CustomScript automatikusan észleli a gzip-tömörítés használatát.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript parancsprogram végrehajtása a következő algoritmust használ.

 1. vyhodnocení, a parancsfájl értékének hossza nem haladja meg a 256 KB.
 1. Base64-dekódolást a parancsfájl érték
 1. _Próbálja meg_ gunzip, a Base64-kódolású érték dekódovat.
 1. a dekódolt (és opcionálisan kibontott) értéket írni lemezre (/ var/lib/waagent/custom-script/#/script.sh)
 1. hajtsa végre a parancsfájl-_ / bin/sh – c /var/lib/waagent/custom-script/#/script.sh használatával.


## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémájában az egyéni szkriptek futtatására szolgáló bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható. A mintasablon, amely tartalmazza az egyéni szkriptek bővítménye, itt található [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Ezek a tulajdonságnevek megkülönböztetik a kis-és nagybetűket. Alkalmazástelepítéssel kapcsolatos problémák elkerülése érdekében használja a nevek itt látható módon.

## <a name="azure-cli"></a>Azure CLI
Azure CLI-vel használatakor az egyéni szkriptek futtatására szolgáló bővítmény futtatása hozzon létre egy konfigurációs fájlt vagy fájlokat. Minimális "commandToExecute" kell rendelkeznie.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Opcionálisan megadhatja a beállításokat a parancsban formázott JSON-karakterláncot. Ez lehetővé teszi, hogy a konfigurációt a végrehajtás során, és a egy külön konfigurációs fájlt nélkül adható meg.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-script-file"></a>Parancsfájl-nyilvános konfiguráció

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Hibaelhárítás
Az egyéni Szkriptbővítmény futtatásakor a szkript a létrehozott vagy letöltött, egy olyan könyvtárba, amely az alábbi példához hasonló. A parancs kimenete is ebben a könyvtárban, a mentett `stdout` és `stderr` fájlokat.

```bash
/var/lib/waagent/custom-script/download/0/
```

Hibaelhárítás, először a Linux-ügynök naplóban, győződjön meg arról, a bővítmény futtatta, ellenőrizze:

```bash
/var/log/waagent.log 
```

Bővítmény végrehajtása kell keresnie, a következőhöz hasonlóan kell kinéznie:
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
Megjegyzés: bizonyos szempontok:
1. Engedélyezése esetén az, hogy a parancs futtatásának megkezdése.
2. Letöltési vonatkozik, a CustomScript bővítmény csomag letöltése az Azure-ban, a parancsfájlok nem megadott fileUris.


Az Azure-Szkriptbővítménnyel hoz létre a napló, amely itt található:

```bash
/var/log/azure/custom-script/handler.log
```

Meg kell keresnie az egyes végrehajtását, a következőhöz hasonlóan kell kinéznie:
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>További lépések
A kód, az aktuális problémák és a verziók megtekintéséhez lásd: [egyéni parancsfájl-bővítmény – linux-tárház](https://github.com/Azure/custom-script-extension-linux).

