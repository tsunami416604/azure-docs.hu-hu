---
title: Egyéni parancsfájlok futtatása Linuxos virtuális gépeken az Azure-ban
description: Linuxos virtuálisgép-konfigurációs feladatok automatizálása az egyéni parancsfájl-bővítmény v2 használatával
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: b75b232c048a1ea49256b12ce1b65c4bd87a1cf0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535442"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Az Azure Custom Script Extension 2. verziójának használata Linux rendszerű virtuális gépekkel
Az egyéni parancsfájl-bővítmény 2-es verziója letölti és futtatja a parancsfájlokat az Azure virtuális gépeken. Ez a bővítmény a telepítés utáni konfigurációhoz, a szoftvertelepítéshez vagy bármely más konfigurációs/felügyeleti feladathoz hasznos. Letöltheti a parancsfájlokat az Azure Storage-ból vagy más elérhető internetes helyről, vagy megadhatja őket a bővítmény futásidejéhez. 

Az egyéni parancsfájl-bővítmény integrálható az Azure Resource Manager-sablonokkal. Azure CLI, PowerShell vagy az Azure Virtual Machines REST API használatával is futtathatja.

Ez a cikk ismerteti, hogyan használhatja az Azure CLI egyéni parancsfájl-bővítményét, és hogyan futtathatja a bővítményt egy Azure Resource Manager-sablon használatával. Ez a cikk a Linux-rendszerek hibaelhárítási lépéseit is ismerteti.


Két Linux egyéni parancsfájl-bővítmény létezik:
* 1.verzió - Microsoft.OSTCExtensions.CustomScriptForLinux
* 2.-as verzió - Microsoft.Azure.Extensions.CustomScript

Váltson új és meglévő központi telepítéseket az új 2-es verzió használatához. Az új verzió a régi kiváltására készült. A migrálásához így elég megváltoztatni a nevet és a verziót, nincs szükség a bővítménykonfiguráció módosítására.


### <a name="operating-system"></a>Operációs rendszer

A Custom Script Extension for Linux fog futni a kiterjesztés támogatott kiterjesztés operációs rendszer, további információkért lásd ezt a [cikket](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Parancsfájl helye

A bővítmény segítségével használhatja az Azure Blob storage hitelesítő adatait, az Azure Blob storage eléréséhez. Másik lehetőségként a parancsfájl helye lehet olyan, ahol, mindaddig, amíg a virtuális gép útvonala, hogy a végpont, például a GitHub, belső fájlkiszolgáló stb.

### <a name="internet-connectivity"></a>Internetkapcsolat
Ha külsőleg le kell töltenie egy parancsfájlt, például a GitHubot vagy az Azure Storage-ot, akkor további tűzfal-/hálózati biztonsági csoportportokat kell megnyitni. Ha például a parancsfájl az Azure Storage-ban található, engedélyezheti a hozzáférést az Azure NSG szolgáltatáscímkék [tárolóhoz](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)használatával.

Ha a parancsfájl helyi kiszolgálón található, akkor további tűzfal-/hálózati biztonságicsoport-portokat kell megnyitni.

### <a name="tips-and-tricks"></a>Tippek és trükkök
* A bővítmény meghibásodásai legnagyobb arányban a szkriptben található szintaktikai hibák miatt jelentkeznek. Tesztelje, hogy a szkript fut-e hiba nélkül, és lássa el további naplózással a szkriptet, hogy könnyebb legyen a hiba helyét azonosítani.
* Írjon idempotens szkripteket, hogy ne okozzanak változásokat a rendszerben, ha véletlenül többször is futtatják őket.
* Győződjön meg arról, hogy a parancsfájlok futtatásához nincs szükség felhasználói beavatkozásra.
* A parancsfájl futtatásához 90 min s engedélyezett, a hosszabb idő a bővítmény sikertelen biztosítását eredményezi.
* Ne tegye újraindítás belül a forgatókönyvet, ez problémákat okoz más kiterjesztések, amelyek telepítése, és a post reboot, a kiterjesztés nem folytatódik az újraindítás után. 
* Ha van egy script, ami miatt újraindul, majd alkalmazásokat telepíteni, és fuss szkriptek, stb Az újraindítást cronfeladathasználatával, vagy olyan eszközökkel kell ütemezni, mint a DSC vagy a Chef, Puppet bővítmények.
* A bővítmény csak egyszer futtat egy parancsfájlt, ha minden rendszerindításkor parancsfájlt szeretne futtatni, akkor használhatja a [felhő-init lemezképet,](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) és használhatja a [Parancsfájlok rendszerindításon ként modult.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Másik lehetőségként a parancsfájl segítségével systemd szolgáltatási egységet hozhat létre.
* Ha azt szeretné ütemezni, hogy mikor fog futni egy parancsfájl, a bővítmény segítségével hozzon létre egy Cron-feladatot. 
* Amikor a szkript fut, az Azure Portalon vagy a CLI-n a bővítmény „átmeneti” állapotát fogja látni. Ha egy futó parancsfájl gyakoribb állapotfrissítéseit szeretné, saját megoldást kell létrehoznia.
* Az egyéni parancsfájl-kiterjesztés nem támogatja natívmódon a proxykiszolgálókat, azonban használhat olyan fájlátviteli eszközt, amely támogatja a parancsfájlon belüli proxykiszolgálókat, például *a Curl*. 
* Ne feledje, hogy a parancsfájlok vagy parancsok nem alapértelmezett könyvtárhelyeket, amelyeken a parancsfájlok vagy parancsok támaszkodhatnak, legyen logika imitátora ennek kezeléséhez.
*  Egyéni parancsfájl üzembe helyezésekor az éles VMSS-példányok azt javasoljuk, hogy a json sablonon keresztül, és tárolja a parancsfájltár-fiók, ahol a SAS-jogkivonat felett. 


## <a name="extension-schema"></a>Bővítményséma

Az egyéni parancsfájl-bővítmény konfigurációja olyan dolgokat határoz meg, mint a parancsfájl helye és a futtatandó parancs. Ezt a konfigurációt tárolhatja konfigurációs fájlokban, megadhatja a parancssorban, vagy megadhatja egy Azure Resource Manager-sablonban. 

A bizalmas adatokat védett konfigurációban tárolhatja, amely titkosított, és csak a virtuális gépen belül van visszafejtve. A védett konfiguráció akkor hasznos, ha a végrehajtási parancs titkos kulcsokat, például egy jelszót tartalmaz.

Ezeket az elemeket bizalmas adatként kell kezelni, és a bővítmények védett beállítási konfigurációjában meg kell adni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
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
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> managedIdentity tulajdonság **nem** használható a storageAccountName vagy storageAccountKey tulajdonságokkal együtt

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | dátum |
| közzétevő | Microsoft.Compute.Extensions | sztring |
| type | CustomScript | sztring |
| typeHandlerVersion | 2.1 | int |
| fileUris (pl. | https://github.com/MyProject/Archive/MyPythonScript.py | tömb |
| commandToExecute (pl. | python \<MyPythonScript.py my-param1> | sztring |
| szkriptet. | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZiGGCHQGGDXBncmFkZSAteQo = | sztring |
| skipDos2Unix (pl. | hamis | logikai |
| időbélyeg (pl. | 123456789 | 32 bites egész szám |
| storageAccountName (pl. | példatárolásacct | sztring |
| storageAccountKey (pl. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg=== | sztring |
| managedIdentity (pl. | { } vagy { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } vagy { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json objektum |

### <a name="property-value-details"></a>Ingatlan értékének részletei
* `apiVersion`: A legfrissebb apiVersion megtalálható a [Resource Explorer](https://resources.azure.com/) vagy az Azure CLI segítségével a következő parancs`az provider list -o json`
* `skipDos2Unix`: (nem kötelező, logikai) hagyja dos2unix átalakítása script-alapú fájl URL-eket vagy script.
* `timestamp`(nem kötelező, 32 bites egész szám) csak a parancsfájl újbóli futtatásához használja a mező értékének módosításával.  Bármely egész érték elfogadható; csak az előző értéknél eltérő lehet.
* `commandToExecute`: (**szükséges,** ha a parancsfájl nincs beállítva, karakterlánc) a végrehajtandó belépési pont parancsfájl. Akkor használja ezt a mezőt, ha a parancs titkokat, például jelszavakat tartalmaz.
* `script`: (**szükséges,** ha a commandToExecute nincs beállítva, karakterlánc)egy base64 kódolt (és opcionálisan gzip'ed) parancsfájl, amelyet a /bin/sh hajt végre.
* `fileUris`: (nem kötelező, karakterlánctömb) a letöltendő fájl(ok) URL-címei.
* `storageAccountName`: (nem kötelező, karakterlánc) a tárfiók neve. Ha megadja a tárolási `fileUris` hitelesítő adatokat, az Összes nek URL-nek kell lennie az Azure Blobs számára.
* `storageAccountKey`: (nem kötelező, karakterlánc) a tárfiók hozzáférési kulcsa
* `managedIdentity`: (nem kötelező, json objektum) a [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) fájl(ok) letöltéséhez
  * `clientId`: (nem kötelező, karakterlánc) a felügyelt identitás ügyfélazonosítója
  * `objectId`: (nem kötelező, karakterlánc) a felügyelt identitás objektumazonosítója


A következő értékek nyilvános vagy védett beállításokban állíthatók be, a bővítmény elutasít minden olyan konfigurációt, ahol az alábbi értékek nyilvános és védett beállításokban is be vannak állítva.
* `commandToExecute`
* `script`
* `fileUris`

A nyilvános beállítások használata hasznos lehet a hibakereséshez, de erősen ajánlott a védett beállítások használata.

A nyilvános beállításokat a rendszer tiszta szövegként küldi el a virtuális gépnek, ahol a parancsfájl végrehajtása megtörténik.  A védett beállítások titkosítva vannak egy csak az Azure és a virtuális gép által ismert kulcs használatával. A beállításokat a rendszer az elküldéskor menti a virtuális gépre, azaz ha a beállítások titkosítva vannak, akkor a virtuális gépen titkosítottan kerülnek mentésre. A titkosított értékek visszafejtéséhez használt tanúsítvány a virtuális gép tárolja, és a beállítások visszafejtésére (ha szükséges) futásidőben.

#### <a name="property-skipdos2unix"></a>Tulajdonság: skipDos2Unix

Az alapértelmezett érték hamis, ami azt jelenti, hogy **a** dos2unix átalakítás végrehajtása történik.

A CustomScript korábbi verziója, a Microsoft.OSTCExtensions.CustomScriptForLinux automatikusan konvertálja a `\r\n` DOS fájlokat UNIX fájlokká a rendszerre történő `\n`fordítással. Ez a fordítás továbbra is létezik, és alapértelmezés szerint be van kapcsolva. Ez az átalakítás a fileUris-ból letöltött összes fájlra vagy a parancsfájl-beállításra vonatkozik az alábbi feltételek alapján.

* Ha a bővítmény `.sh`a `.txt` `.py`, `.pl` , vagy konvertálódik. A parancsfájl-beállítás mindig megfelel ennek a feltételnek, mert a /bin/sh kapcsolóval végrehajtott parancsfájlnak tekintendő, és script.sh a virtuális gépen.
* Ha a fájl `#!`a programmal kezdődik.

A dos2unix átalakítás kihagyható a skipDos2Unix true értékre állításával.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Tulajdonság: script

A CustomScript támogatja a felhasználó által definiált parancsfájlok végrehajtását. A commandToExecute és fileUris egyetlen beállításba történő egyesítéséhez szükséges parancsfájlbeállítások. Ahelyett, hogy be kellene állítania egy fájlt az Azure storage-ból vagy a GitHub-t, egyszerűen kódolhatja a parancsfájlt beállításként. Parancsfájl használható a commandToExecute és fileUris cseréjére.

A parancsfájlnak base64 kódolásúnak **kell** lennie.  A szkript **opcionálisan** gzip'ed. A parancsfájl-beállítás nyilvános vagy védett beállításokban használható. A parancsfájlparaméter adatainak maximális mérete 256 KB. Ha a parancsfájl meghaladja ezt a méretet, akkor nem kerül végrehajtásra.

Például, mivel a következő parancsfájlt mentette a fájlba /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

A megfelelő CustomScript parancsfájl-beállítás a következő parancs kimenetének átvételével épül fel.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

A szkript opcionálisan gzip'ed tovább csökkenti a méretet (a legtöbb esetben). (A CustomScript automatikusan észleli a gzip tömörítés használatát.)

```sh
cat script | gzip -9 | base64 -w 0
```

A CustomScript a következő algoritmust használja a parancsfájlok végrehajtásához.

 1. a parancsfájl értékének hossza nem haladja meg a 256 KB-ot.
 1. base64 dekódolja a parancsfájl értékét
 1. _kísérlet_ gunzip a base64 dekódolt érték
 1. írja be a dekódolt (és opcionálisan kibontott) értéket lemezre (/var/lib/waagent/custom-script/#/script.sh)
 1. a parancsfájl végrehajtása _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh használatával.

####  <a name="property-managedidentity"></a>Tulajdonság: managedIdentity

A CustomScript (2.1-es verziótól) támogatja a [felügyelt identitást](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) a "fileUris" beállításban megadott URL-címekről történő fájlletöltéshez. Lehetővé teszi a CustomScript számára az Azure Storage privát blobjainak vagy tárolóinak elérését anélkül, hogy a felhasználónak olyan titkos kulcsokat kellene átadnia, mint a SAS-jogkivonatok vagy a tárfiók kulcsai.

A szolgáltatás használatához a felhasználónak hozzá kell adnia egy [rendszer-hozzárendelt](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) vagy [felhasználó által hozzárendelt](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitást a virtuális géphez vagy a VMSS-hez, ahol a CustomScript várhatóan futni fog, és [biztosítania kell a felügyelt identitás hozzáférést az Azure Storage-tárolóhoz vagy -blobhoz.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)

A rendszer által hozzárendelt identitás használata a cél vm/VMSS, állítsa be a "managedidentity" mező egy üres json-objektum. 

> Példa:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

A felhasználó által hozzárendelt identitás használata a cél vm/VMSS, konfigurálja a "managedidentity" mező az ügyfélazonosító vagy a felügyelt identitás objektumazonosítóját.

> Példák:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity tulajdonság **nem** használható a storageAccountName vagy storageAccountKey tulajdonságokkal együtt

## <a name="template-deployment"></a>Sablonalapú telepítés
Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager-sablonban használható az egyéni parancsfájl-bővítmény futtatásához az Azure Resource Manager-sablon üzembe helyezése során. Az egyéni parancsfájl-bővítményt tartalmazó mintasablon itt, a [GitHubon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)található.


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
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
>Ezek a tulajdonságnevek a kis- és nagybetűket nem érzékenyékelik. A telepítési problémák elkerülése érdekében használja az itt látható neveket.

## <a name="azure-cli"></a>Azure CLI
Ha az Azure CLI-t használja az egyéni parancsfájl-bővítmény futtatásához, hozzon létre egy konfigurációs fájlt vagy fájlokat. Legalább a "commandToExecute" paraparancsot kell rendelkeznie.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

A parancs beállításait JSON formátumú karakterláncként is megadhatja. Ez lehetővé teszi a konfiguráció megadása a végrehajtás során, és anélkül, hogy egy külön konfigurációs fájlt.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure parancssori felületi (CLI) példák

#### <a name="public-configuration-with-script-file"></a>Nyilvános konfiguráció parancsfájlfájllal

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI parancs:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Hibaelhárítás
Az egyéni parancsfájl-bővítmény futtatásakor a parancsfájl a következő példához hasonló könyvtárba jön létre vagy töltődik le. A parancs kimenetét is menti `stdout` `stderr` ebbe a könyvtárba és fájlokat.

```bash
/var/lib/waagent/custom-script/download/0/
```

A hibaelhárításhoz először ellenőrizze a Linux-ügynöknaplót, győződjön meg róla, hogy a bővítmény futott, ellenőrizze:

```bash
/var/log/waagent.log 
```

Meg kell keresni a kiterjesztés végrehajtását, akkor fog kinézni:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Néhány megjegyzés:
1. Az engedélyezés az, amikor a parancs elindul.
2. A letöltés a CustomScript-bővítménycsomag Azure-ból való letöltésére vonatkozik, nem pedig a fileUris-ban megadott parancsfájlokra.


Az Azure Script Extension naplót hoz létre, amely itt található:

```bash
/var/log/azure/custom-script/handler.log
```

Meg kell keresni az egyes végrehajtás, akkor fog kinézni:

```output
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>További lépések
A kód, az aktuális problémák és verziók megtekintéséhez lásd: [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).

