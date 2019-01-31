---
title: Másolja, vagy az adatok áthelyezése az Azure Storage az Azcopyval Linux rendszeren |} A Microsoft Docs
description: Linux-segédprogram az AzCopy segítségével áthelyezi vagy másolhat blob és a fájl tartalmát a. Adatok másolása az Azure Storage a helyi fájlokból vagy adatmásolás belül vagy tárfiókok között. Egyszerűen migrálhatja az adatokat az Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 1059dec80dcca80380b58aa8057162679496adc6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467373"
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Adatok áthelyezése az Azcopyval Linux rendszeren

Az AzCopy egy parancssori segédprogram, és a Microsoft Azure Blob- és File storage, az adatok másolása szolgál az optimális teljesítmény érdekében tervezett egyszerű parancs használatával. Az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja.  

Nincsenek töltheti le, Azcopynak két verziója. AzCopy linuxon célozza meg benne POSIX stílusú parancssori lehetőségeket kínál Linux platformokról. [Az AzCopy Windows](../storage-use-azcopy.md) Windows stílusú parancssori lehetőségeket kínál. Ez a cikk ismerteti az AzCopy linuxon. 

> [!NOTE]  
> AzCopy 7.2 verzió kezdődően a .NET Core függőségei vannak csomagolva az AzCopy-csomaggal. Ha a 7,2 verzióját használja, vagy később már nem telepítenie kell a .NET Core előfeltételként.

## <a name="download-and-install-azcopy"></a>Töltse le és telepítse az Azcopyval

### <a name="installation-on-linux"></a>Telepítés Linux rendszeren

> [!NOTE]
> Előfordulhat, hogy a jelen kiemelt .NET Core 2.1 függőségek telepítenie kell [.NET Core előfeltételeit cikk](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) függően a disztribúció. 
>
> RHEL 7 disztribúciók függőségei ICU és libunwind telepítése: ```yum install -y libunwind icu```

Az AzCopy telepítése Linux rendszeren (v7.2 vagy újabb) olyan egyszerű, mintha a tar csomag kicsomagolásához használt, és a telepítési szkript futtatásához. 

**RHEL 6-alapú disztribúciókon**: [letöltési hivatkozás](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Más Linux-disztribúciók**: [letöltési hivatkozás](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

AzCopy linuxon telepítése után eltávolíthatja a kibontott fájlokat. Azt is megteheti, ha nem rendelkezik SUPERUSER felhasználói jogosultságai is futtathatja `azcopy` a kibontott mappát a shell script azcopy használatával.

### <a name="alternative-installation-on-ubuntu"></a>Alternatív telepítési Ubuntu rendszeren

**Ubuntu 14.04**

A Microsoft Linux termék tárház apt forrás hozzáadása, és telepítse az AzCopy:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

A Microsoft Linux termék tárház apt forrás hozzáadása, és telepítse az AzCopy:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
sudo apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Az első AzCopy-parancs írása
Az AzCopy-parancsokat alapvető szintaxisa:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Az alábbi példák bemutatják az adatok másolása a és a Microsoft Azure-Blobok és fájlok a különböző forgatókönyvekben. Tekintse meg a `azcopy --help` menü egyes mintában használt paraméterek részletes leírását.

## <a name="blob-download"></a>Blob: Letöltés
### <a name="download-single-blob"></a>Letölt egy blobot

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Ha a mappa `/mnt/myfiles` nem létezik, az AzCopy létrehozza, és letölti a `abc.txt ` az új mappába. 

### <a name="download-single-blob-from-secondary-region"></a>Másodlagos régióból egy blob letöltése

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Vegye figyelembe, hogy rendelkeznie kell olvasási hozzáférésű georedundáns tárolás engedélyezve van.

### <a name="download-all-blobs"></a>Összes BLOB letöltése

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Tegyük fel, a következő BLOB található a megadott tároló:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

A letöltési művelet a címtár után `/mnt/myfiles` a következő fájlokat tartalmazza:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Ha nem adja meg a beállítás `--recursive`, a blob nem lesz letöltve.

### <a name="download-blobs-with-specified-prefix"></a>Töltse le a megadott előtaggal rendelkező blobok

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Tegyük fel, a megadott tárolóban található a következő blobokhoz. A előtaggal kezdődő összes BLOB `a` letöltődnek.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

A mappa a letöltési művelet után `/mnt/myfiles` a következő fájlokat tartalmazza:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Az előtag vonatkozik a virtuális könyvtárban, amely a blob nevének első részét képezi. A fenti példában a virtuális könyvtár nem egyezik a megadott előtag, így nem blob letöltése. Ezenkívül ha a beállítás `--recursive` nincs megadva, az AzCopy nem tölti le azokat a blobokat.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Állítsa be az exportált fájlokat ugyanaz, mint a forrás BLOB utolsó módosítás időpontja

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Emellett kizárhatók a blobok a letöltési művelet a legutóbbi módosításuk ideje alapján. Például, ha ki szeretné zárni a blobok, amelynek az utolsó módosítás időpontja, azonos vagy újabb, mint a célfájlt, adja hozzá a `--exclude-newer` lehetőséget:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Vagy ha ki szeretné zárni a blobok, amelynek az utolsó módosítás időpontja nem azonos vagy régebbi, mint a célfájlt, adja hozzá a `--exclude-older` lehetőséget:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: Feltöltés
### <a name="upload-single-file"></a>Egyetlen fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba.

### <a name="upload-single-file-to-virtual-directory"></a>Virtuális könyvtár egyetlen fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Ha a megadott virtuális könyvtár nem létezik, az AzCopy feltölti a fájlt a virtuális könyvtárat a blob neve (*például*, `vd/abc.txt` a fenti példában).

### <a name="redirect-from-stdin"></a>A stdin átirányítása

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Minden fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Beállítás megadása `--recursive` tölt fel a megadott könyvtár tartalmának a Blob storage rekurzív módon, ami azt jelenti, hogy minden almappa és a fájlok is feltöltődnek. Például tegyük fel, a következő fájlok mappában található `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

A feltöltési művelet után a tárolót a következő fájlokat tartalmazza:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Ha a beállítás `--recursive` nincs megadva, csak a következő három fájlok feltöltése:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Töltse fel a megadott mintának megfelelő fájlok

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Tegyük fel, a következő fájlok mappában található `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

A feltöltési művelet után a tárolót a következő fájlokat tartalmazza:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Ha a beállítás `--recursive` nincs megadva, az AzCopy kihagyja az alkönyvtárat lévő fájlok:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Adja meg a cél blob a MIME-tartalom típusa
Alapértelmezés szerint az AzCopy beállítja a tartalom típusa a cél blob `application/octet-stream`. Azonban explicit módon megadhatja a tartalomtípus keresztül lehetőséget `--set-content-type [content-type]`. Ez a szintaxis egy feltöltési művelet beállítja az összes tartalom típusa.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Ha a beállítás `--set-content-type` egy érték nélkül van megadva, majd az AzCopy állítja be, minden egyes blob vagy fájl tartalom típusa szerint a fájl kiterjesztése.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

### <a name="customizing-the-mime-content-type-mapping"></a>A MIME-tartalom típusa leképezés testreszabása
Az AzCopy egy fájl kiterjesztése tartalomtípushoz térképét tartalmazó konfigurációs fájlt használ. Testre szabhatja ezt a hozzárendelést, és adjon hozzá új párokat. A leképezés a következő helyen található:  ```/usr/lib/azcopy/AzCopyConfig.json```

## <a name="blob-copy"></a>Blob: Másolás
### <a name="copy-single-blob-within-storage-account"></a>Egyetlen a tárfiókban található blobba másolja.

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Amikor--szinkronizálási másolási beállítás nélkül blobot másol egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-single-blob-across-storage-accounts"></a>Egyetlen blob másolása tárfiókok között

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Amikor--szinkronizálási másolási beállítás nélkül blobot másol egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Egyetlen blob másolása másodlagos régióból az elsődleges régióba

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Vegye figyelembe, hogy rendelkeznie kell olvasási hozzáférésű georedundáns tárolás engedélyezve van.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Egyetlen blobhoz, és annak pillanatképei másolása tárfiókok között

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

A másolási művelet után a cél tároló tartalmazza, a blob és annak pillanatképei. A tároló tartalmazza a következő blob és annak pillanatképei:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Szinkron módon történik az tárfiókban található blobok másolása
Alapértelmezés szerint az AzCopy adatokat másol a két tárolási végpontok közötti aszinkron módon történik. Ezért a másolási művelet fut a háttérben használja, amely nem tartozik SLA szempontjából hogyan gyors blob tartalékolt sávszélesség-kapacitást kell másolni. 

A `--sync-copy` beállítással biztosíthatja, hogy a másolási művelet lekérdezi az egységes sebesség. Az AzCopy a szinkron másolatot a blobok másolása a megadott forrás helyi memória letöltésével, és feltölti őket a Blob storage cél hajt végre.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` További kimenő forgalmi költségek képest aszinkron példányt hozhat létre. Az ajánlott módszer, hogy ez a beállítás az Azure virtuális Gépen, és a forrás tárfiókban kimenő forgalmi költségek elkerülése érdekében ugyanabban a régióban található.

## <a name="file-download"></a>Fájl: Letöltés
### <a name="download-single-file"></a>Egyetlen fájl letöltése

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Ha a megadott forrás egy Azure-fájlmegosztást, akkor kell adnia a fájl pontos nevét (*például* `abc.txt`) egyetlen fájl letöltéséhez, vagy adja meg a beállítás `--recursive` a megosztás rekurzív módon található összes fájl letöltéséhez. Adjon meg egy Fájlmintát és a beállítás próbál `--recursive` hiba együtt eredményez.

### <a name="download-all-files"></a>Minden fájl letöltése

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Vegye figyelembe, hogy a rendszer nem tölti le minden olyan üres mappát.

## <a name="file-upload"></a>Fájl: Feltöltés
### <a name="upload-single-file"></a>Egyetlen fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Minden fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Vegye figyelembe, hogy bármely üres mappák nem töltött fel.

### <a name="upload-files-matching-specified-pattern"></a>Töltse fel a megadott mintának megfelelő fájlok

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Fájl: Másolás
### <a name="copy-across-file-shares"></a>Másolja a fájlmegosztások között

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Amikor a fájlmegosztások, között másolhat egy fájlt egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-from-file-share-to-blob"></a>A blob-fájlmegosztás másolása

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Amikor másolhat egy fájlt a blobba, fájlmegosztásról egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-from-blob-to-file-share"></a>Fájlmegosztás blob másolása

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Másolhat egy fájlt a blobból a fájlmegosztáshoz, amikor egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="synchronously-copy-files"></a>Szinkron módon történik a fájlok másolása
Megadhatja a `--sync-copy` beállítás adatokat másol a File Storage File Storage, File Storage a Blob Storage és a Blob Storage, File Storage szinkron módon történik. AzCopy az adatok letöltése a helyi memória, és majd feltölteni a cél futtathatja ezt a műveletet. Ebben az esetben a standard szintű kimenő forgalmi költségek vonatkozik.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Amikor másol a File Storage a Blob Storage, az alapértelmezett typ objektu blob blokkblob típusú, felhasználó úgy adhat meg a beállítás `--blob-type page` a blob cél típusának módosítása. Rendelkezésre álló típusok `page | block | append`.

Vegye figyelembe, hogy `--sync-copy` további kimenő forgalmi költségek összehasonlításával aszinkron példány létrehozására. Az ajánlott módszer, hogy ez a beállítás az Azure virtuális Gépen, és a forrás tárfiókban kimenő forgalmi költségek elkerülése érdekében ugyanabban a régióban található.

## <a name="other-azcopy-features"></a>Más AzCopy-funkciók
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Csak másolja az adatokat, amely nem létezik a célhelyen
A `--exclude-older` és `--exclude-newer` paraméterek régebbi vagy újabb forrás erőforrások kizárása való másolását, illetve lehetővé teszik. Ha azt szeretné, hogy nem létezik a célhelyen forrás erőforrások másolása csak, mindkét paraméter is megadhat az AzCopy-parancsban:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Egy konfigurációs fájl használatával adja meg a parancssori paraméterek

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Az AzCopy parancssori paramétereket is megadhat a konfigurációs fájlt. AzCopy dolgozza fel a paramétereket a fájlban, ha a parancssorban megadott lett a fájl tartalmával közvetlen helyettesítés végrehajtása.

Tegyük fel, nevű konfigurációs fájllal `copyoperation`, a következő sorokat tartalmazó. Minden egyes AzCopy paraméter adható meg ugyanabban a sorban.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

vagy külön sorokat:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

Az AzCopy sikertelen lesz, ha a paraméter két sort elosztja az itt látható módon a `--source-key` paramétert:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Adja meg a közös hozzáférésű jogosultságkód (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

A tároló URI-t is megadhat egy SAS:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Napló fájlmappa
Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi egy journal-fájlt az alapértelmezett mappában megtalálható-e, vagy hogy megtalálható-e ezt a beállítást keresztül megadott mappába. A naplófájl nem létezik az egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl létezik, AzCopy ellenőrzi, hogy adjon meg parancssori megegyezik-e a naplófájl parancssorában. Ha két parancssorok egyezik, az AzCopy folytatja a művelet nem fejeződött be. Ha nem egyeznek, a AzCopy figyelmezteti a felhasználót, vagy felülírja a naplófájl elindítani egy új műveletet, vagy szakítsa meg a jelenlegi műveletet.

Ha szeretné használni a naplófájl alapértelmezett helye:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Ha a beállítás nincs megadva `--resume`, vagy adja meg a beállítás `--resume` anélkül, hogy a mappa elérési útja, ahogyan fentebb, az AzCopy hoz létre a naplófájl az alapértelmezett helyen, amely `~\Microsoft\Azure\AzCopy`. Ha a napló fájl már létezik, az AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha szeretne egy egyéni napló helyét adja meg:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Ebben a példában a naplófájl hoz létre, ha azt nem létezik. Ha létezik, az AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha szeretne egy AzCopy üzemkész, ismételje meg ugyanezt a parancsot. AzCopy linuxon majd a megerősítési fogja kérni:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Kimeneti részletes naplók

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Adja meg, indítsa el a párhuzamos műveletek száma
A beállítás `--parallel-level` egyidejű másolási műveletek számát adja meg. Alapértelmezés szerint az AzCopy egy bizonyos számú párhuzamos műveletek az adatok átvitele az átviteli sebesség növelése indul. A párhuzamos műveletek száma megegyezik rendelkezik processzorok száma nyolc alkalommal. Ha az AzCopy egy kis sávszélességű hálózaton keresztül futtat, erőforrás verseny által okozott hiba elkerülése érdekében a--párhuzamos szintű kevesebb is megadhat.

>[!TIP]
>AzCopy paraméterek teljes listájának megtekintéséhez, tekintse meg a "azcopy--help" menüben.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Az AzCopy 7.1-es és korábbi verzióiban telepítésének lépései

AzCopy linuxon (7.1 és régebbi verziók esetén) van szükség a .NET Core keretrendszert. A telepítési utasításokat is a [.NET Core telepítés](https://www.microsoft.com/net/core#linuxubuntu) lapot.

Például első lépésként telepítse a .NET Core az Ubuntu 16.10. A legfrissebb telepítési útmutatójában talál [Linux rendszeren a .NET Core](https://www.microsoft.com/net/core#linuxubuntu) telepítési lapja.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Amint telepítette a .NET Core, töltse le és telepítse az AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

AzCopy linuxon telepítése után eltávolíthatja a kibontott fájlokat. Ha nem rendelkezik SUPERUSER felhasználói jogosultságai, is is futtathatja `azcopy` a kibontott mappát a shell script azcopy használatával.

## <a name="known-issues-and-best-practices"></a>Ismert problémák és ajánlott eljárások
### <a name="error-installing-azcopy"></a>Hiba történt az AzCopy telepítése
Az AzCopy telepítési problémák merülnek fel, ha lehetséges, hogy próbálja meg futtatni a bash-szkript használatával a kinyert az AzCopy `azcopy` mappát.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Adatok másolása közben egyidejű írások korlátozása
Blobok vagy az Azcopyval fájlok másolása esetén vegye figyelembe, hogy egy másik alkalmazás módosítja az adatokat másolja, amíg. Ha lehetséges győződjön meg arról, hogy az adatok másolása nem áll módosítás alatt a másolási művelet során. Például ha egy Azure virtuális géphez társított virtuális Merevlemezét másolja, győződjön meg arról, hogy más alkalmazások nem a virtuális merevlemez jelenleg írása. Egy jó módja a naplóbérbeadási kell másolni az erőforrás. Azt is megteheti először hozzon létre egy pillanatképet a VHD-t, és másolja a pillanatkép.

Ha más alkalmazásokat a blobok és fájlok írása, amíg folyamatban van a másolás nem akadályozhatja meg, majd vegye figyelembe, hogy a ideje, a feladat befejeződik, az átmásolt erőforrások előfordulhat, hogy többé nem kell a forrás-erőforrások teljes paritás.

### <a name="running-multiple-azcopy-processes"></a>Több AzCopy folyamat fut
A megadása, hogy más-más mappákat használja egyetlen ügyfél AzCopy több folyamatot is futtathat. Egyetlen napló mappa több AzCopy-folyamatokhoz történő használata nem támogatott.

1. folyamat:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2. folyamat:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>További lépések
További, az Azure Storage szolgáltatással és az AzCopyval kapcsolatos adatokat a következő erőforrások nyújtanak:

### <a name="azure-storage-documentation"></a>Az Azure Storage-dokumentáció:
* [A Microsoft Azure Storage bemutatása](../storage-introduction.md)
* [Tárfiók létrehozása](../storage-create-storage-account.md)
* [Blobok kezelése a Storage Explorerrel](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Using the Azure CLI with Azure Storage (Az Azure parancssori felülete és az Azure Storage együttes használata)](../storage-azure-cli.md)
* [A c++ segítségével Blob storage használata](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [How to use Blob storage from Java (A Blob Storage használata Javával)](../blobs/storage-java-how-to-use-blob-storage.md)
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Blob storage from Pythonnal (A Blob Storage használata Pythonnal)](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Az Azure Storage-blogbejegyzések:
* [Az AzCopy a Linuxos előzetes bejelentése](https://azure.microsoft.com/blog/announcing-azcopy-on-linux-preview/)
* [Introducing Azure Storage adatátviteli könyvtár Adatelőnézet](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Bemutatkozik a szinkron másolása és testre szabott tartalom típusa](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Általános rendelkezésre állás az AzCopy 3.0 bejelentése, valamint előzetes támogatásával, tábla és fájl AzCopy 4.0-s verziója](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Nagyméretű példány esetekre optimalizált](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Írásvédett georedundáns tárolás támogatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Adatok áthelyezése az újraindítható üzemmódban és SAS-jogkivonat](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: A Blob másolásához cross-fiók használatával](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Fájlok feltöltése/letöltése az Azure-Blobok](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
