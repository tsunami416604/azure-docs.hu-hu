---
title: "Másolja, vagy helyezze át az adatok Azure Storage az AzCopy Linux |} Microsoft Docs"
description: "Linux-segédprogram az AzCopy segítségével áthelyezi vagy másolja az adatokat, vagy a blob és a fájl tartalmát. Adatok másolása az Azure Storage a helyi fájlokból, vagy másolja az adatokat belül vagy tárfiókok között. Adatok áttelepítése egyszerű Azure Storage."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: seguler
ms.openlocfilehash: 2fd89684176cd832b656dae8c8f94a6f1ccbbbe8
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Adatátvitel az AzCopy Linux rendszeren

AzCopy egy parancssori segédprogram, és a Microsoft Azure Blob, a fájl és a tábla tárolási, adat másolása az optimális teljesítményének készült egyszerű parancsok használatával. Másolhat adatokat a fájlrendszer és a storage-fiók, vagy tárfiókok között.  

AzCopy, letöltheti a két verziója van. AzCopy Linux ajánlat POSIX-stílusú parancssori kapcsolók Linux platformon célozza .NET Core keretrendszerrel épül. [A Windows AzCopy](../storage-use-azcopy.md) a .NET-keretrendszer épül, és ez biztosítja a Windows stílus parancssori kapcsolókat. Ez a cikk a Linux AzCopy ismerteti.

## <a name="download-and-install-azcopy"></a>Töltse le és telepítse az AzCopy
### <a name="installation-on-linux"></a>Linux-telepítés

A cikk Ubuntu különböző kiadásaiban parancsokat tartalmaz.  Használja a `lsb_release -a` erősítse meg az terjesztésű kiadású és concero kódnevű parancsot. 

AzCopy Linux szükséges a .NET Core framework (verzió: 2.0) a platformon. A telepítési utasításokat tekintse meg a [.NET Core](https://www.microsoft.com/net/download/linux) lap.

Tegyük fel most telepítse a .NET Core Ubuntu 16.04. A legfrissebb telepítési útmutató a Microsoft [.NET Core Linux](https://www.microsoft.com/net/download/linux) telepítési oldal.


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.2
```

A .NET Core telepítése után töltse le és telepítse az AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

A kibontott fájlokat is távolítható el, ha az AzCopy Linux rendszeren telepítve van. Másik lehetőségként nem jogosult felügyelő, ha is futtathatja a kibontott mappában található a "azcopy" parancsfájl segítségével AzCopy. 


## <a name="writing-your-first-azcopy-command"></a>Az első AzCopy parancs írása
Az AzCopy parancs alapvető szintaxisa:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Az alábbi példák bemutatják a különböző forgatókönyvek másolása adatok és a Microsoft Azure-BLOB és a fájlok. Tekintse meg a `azcopy --help` menü egyes mintában használt paraméterek részletes leírását.

## <a name="blob-download"></a>BLOB: letöltése
### <a name="download-single-blob"></a>Egy blob letöltése

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Ha a mappa `/mnt/myfiles` nem létezik, AzCopy létrehozza, és letölti `abc.txt ` az új mappába. 

### <a name="download-single-blob-from-secondary-region"></a>A másodlagos régióba egyetlen blob letöltése

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Vegye figyelembe, hogy írásvédett georedundáns tárolás engedélyezve kell rendelkeznie.

### <a name="download-all-blobs"></a>Töltse le az összes BLOB

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Tegyük fel, a következő blobot a megadott tárolóban találhatók:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

A könyvtár a letöltési művelet után `/mnt/myfiles` a következő fájlokat tartalmazza:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Ha nem adja meg a beállítás `--recursive`, nincs blob le lesznek töltve.

### <a name="download-blobs-with-specified-prefix"></a>A megadott előtag blobok letöltése

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Tegyük fel, a következő blobok találhatók a megadott tárolóban. A előtaggal kezdődő összes BLOB `a` letöltődnek.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

A letöltési mappa művelet után `/mnt/myfiles` a következő fájlokat tartalmazza:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Az előtag a virtuális könyvtárban, a blob nevének első részét képező vonatkozik. A fenti példában a virtuális könyvtár nem egyezik a megadott előtagot, így nincs blob letöltődik. Emellett ha a beállítás `--recursive` nincs megadva, az AzCopy nem tölti le a blobokat.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Állítsa be az exportált fájlokat lehet ugyanaz, mint a forrás utolsó módosításának időpontja

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Blobok emellett kizárja a letöltési művelet az utolsó módosításának ideje alapján. Például, ha ki szeretné zárni a blobok, amelyek utolsó módosításának ideje azonos vagy újabb, mint a célfájl, vegye fel a `--exclude-newer` lehetőséget:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Vagy ha ki szeretné zárni a blobok, amelyek utolsó módosítás időpontja nem azonos vagy régebbi, mint a célfájl, vegye fel a `--exclude-older` lehetőséget:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>BLOB: feltöltése
### <a name="upload-single-file"></a>Töltse fel egy fájlból

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Ha a megadott célhely tároló nem létezik, az AzCopy létrehozása, és a fájlt tölt be.

### <a name="upload-single-file-to-virtual-directory"></a>Egy fájlból töltse fel a virtuális könyvtár

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Ha a megadott virtuális könyvtár nem létezik, az AzCopy feltölti a fájlt a virtuális könyvtárat a blob nevének (*pl.*, `vd/abc.txt` a fenti példában).

### <a name="upload-all-files"></a>Minden fájl feltöltése

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Beállítás megadása `--recursive` feltölt Blob storage rekurzív módon, ami azt jelenti, hogy minden almappa és a fájlok feltöltése, valamint a megadott könyvtár tartalmát. Például a mappában találhatók a következő fájlok feltételezik `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

A tároló a feltöltési művelet után a következő fájlokat tartalmazza:

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

Tegyük fel, a következő fájlok mappában találhatók `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

A tároló a feltöltési művelet után a következő fájlokat tartalmazza:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Ha a beállítás `--recursive` nincs megadva, az AzCopy kihagyja alkönyvtár lévő fájlok:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Adjon meg egy cél blob MIME tartalomtípus
Alapértelmezés szerint az AzCopy beállítja egy cél blobot tartalomtípusa `application/octet-stream`. Azonban közvetlenül megadhatja a tartalomtípus keresztül beállítás `--set-content-type [content-type]`. Ez a szintaxis a feltöltési művelet állítja be a content-type összes BLOB.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Ha a beállítás `--set-content-type` egy érték nélkül van megadva, majd az AzCopy állítja be, minden egyes blob vagy a fájl tartalomtípusa alapján a fájl kiterjesztése.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>BLOB: másolása
### <a name="copy-single-blob-within-storage-account"></a>Másolja át egy blob Storage-fiókban

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Ha--másolatának szinkronizálása beállítás nélkül egy blobot másol egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-single-blob-across-storage-accounts"></a>Másolja át egy blob Storage-fiókok között

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Ha--másolatának szinkronizálása beállítás nélkül egy blobot másol egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Egy blob másolása másodlagos régióba elsődleges régió

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Vegye figyelembe, hogy írásvédett georedundáns tárolás engedélyezve kell rendelkeznie.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Egy blob másolási és a pillanatképek tárfiókok között

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

A másolási művelet után a céltároló tartalmaz a blob és a pillanatképek. A tároló a következő blob és a pillanatképek tartalmazza:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Szinkron módon másolni BLOB Storage-fiókok
AzCopy alapértelmezés szerint aszinkron módon másolja az adatokat két tárolási végpontok közötti. Ezért a másolási művelet fut a háttérben, amelynek nincs szolgáltatásiszint-szerződés szempontjából hogyan gyors blob kapacitás tartalékolt sávszélesség használatával kell másolni. 

A `--sync-copy` lehetőség biztosítja, hogy a másolási művelet lekérdezi az egységes sebesség. AzCopy a szinkron másolatot helyi memória a megadott forrás másolása a blobok letöltése, és feltöltheti a Blob storage cél végez.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy`További kilépő költség képest aszinkron másolási hozhat létre. Az ajánlott módszer, hogy ezt a beállítást használja egy Azure virtuális gép, és a kimenő forgalom költségek elkerülése érdekében forrás tárfiók ugyanabban a régióban található.

## <a name="file-download"></a>Fájl: letöltése
### <a name="download-single-file"></a>Töltse le egy fájlból

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Ha a megadott forrás az Azure fájlmegosztások, akkor meg kell adnia a fájl pontos nevét (*pl.* `abc.txt`) egyetlen fájl letöltéséhez vagy beállítást adja meg `--recursive` a megosztás rekurzív módon található összes fájl letöltéséhez. Adjon meg egy fájl mintát és a beállítás próbál `--recursive` hiba együtt eredményez.

### <a name="download-all-files"></a>Minden fájl letöltése

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Vegye figyelembe, hogy a rendszer nem tölti le üres mappák.

## <a name="file-upload"></a>Fájl: feltöltése
### <a name="upload-single-file"></a>Töltse fel egy fájlból

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

Vegye figyelembe, hogy az üres mappák nem töltődött fel.

### <a name="upload-files-matching-specified-pattern"></a>Töltse fel a megadott mintának megfelelő fájlok

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Fájl: másolása
### <a name="copy-across-file-shares"></a>Fájlmegosztások másolni

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Amikor fájlmegosztások között másolhat egy fájlt egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-from-file-share-to-blob"></a>Megosztás és a blob másolása

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Amikor másolhat egy fájlt a blobra, fájlmegosztásról egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-from-blob-to-file-share"></a>Fájlmegosztás a blob másolása

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Másolhat egy fájlt blobból fájlmegosztáshoz, amikor egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="synchronously-copy-files"></a>Szinkron módon történik a fájlok másolása
Megadhatja a `--sync-copy` beállítás adatokat másolni a File Storage a File Storage, File Storage-Blob Storage és a Blob Storage a File Storage szinkron módon történik. AzCopy futtatja ezt a műveletet az adatok letöltése a helyi memória, és majd ismét feltölteni a cél. Ebben az esetben a szabványos kilépő költség érvényes.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Amikor másol a File Storage Blob Storage, az alapértelmezett blob típushoz blokkblob, felhasználói beállítást adja meg `--blob-type page` módosíthatja a blob típusára. Rendelkezésre álló típusok `page | block | append`.

Vegye figyelembe, hogy `--sync-copy` további költségeket, aszinkron másolási való hasonlítás kilépő hozhat létre. Az ajánlott módszer, hogy ezt a beállítást használja egy Azure virtuális gép, és a kimenő forgalom költségek elkerülése érdekében forrás tárfiók ugyanabban a régióban található.

## <a name="other-azcopy-features"></a>Más AzCopy szolgáltatások
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Csak másolja az adatokat, a cél nem létezik
A `--exclude-older` és `--exclude-newer` paraméterek lehetővé teszik a régebbi vagy újabb forrás erőforrások másolását, illetve kizárása. Ha szeretné, amelyek nem léteznek a cél a forrás-erőforrások másolása, mindkét paraméter az AzCopy parancs adhat meg:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>A konfigurációs fájlt használja a parancssori paraméterek megadása

```azcopy
azcopy --config-file "azcopy-config.ini"
```

AzCopy parancssori paramétereket is megadhat a konfigurációs fájlt. AzCopy dolgozza fel a paraméterek a fájlban, ha a parancssorban megadott lett hajt végre a fájl tartalma közvetlen helyettesítés.

Tegyük fel, a konfigurációs fájl nevű `copyoperation`, amely tartalmazza a következő sorokat. Minden egyes AzCopy paraméter egy sorba adható meg.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

vagy külön sorok:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy sikertelen lesz, ha a paraméter osztani két sort, az itt látható a `--source-key` paraméter:

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

Azt is megadhatja egy SAS URI tárolón:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Napló fájlmappa
Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi, hogy a napló fájl megtalálható-e az alapértelmezett mappába, vagy hogy keresztül ez a beállítás a megadott mappa létezik. A napló fájl nem létezik egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl nem létezik, AzCopy ellenőrzi, hogy a parancssor, amely a megadott megegyezik-e a parancssorban a napló fájlban. Ha a két parancssorokat egyeznek, AzCopy folytatja a teljes műveletet. Ha nem egyeznek, a AzCopy felszólítja a felhasználót vagy felülírja ezt a napló fájlt egy új művelet indítása és az aktuális művelet megszakítására.

Ha szeretné a naplófájl alapértelmezett helyet használja:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Ha a beállítás nincs megadva `--resume`, vagy adja meg a beállítás `--resume` anélkül, hogy a mappa elérési útja, a fent látható AzCopy fájlt hoz létre a napló az alapértelmezett helyen, amely `~\Microsoft\Azure\AzCopy`. Ha a napló fájl már létezik, majd AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha szeretne egy egyéni napló elérési útját adja meg:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Ebben a példában a napló fájlt hoz létre, ha még nem létezik. Ha létezik, majd AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha azt szeretné, az AzCopy művelet folytatásához ismételje meg ugyanezt a parancsot. AzCopy Linux majd megerősítést kér fogja kérni:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Kimeneti részletes naplókat

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Adja meg elindítani a párhuzamos műveletek száma
A beállítás `--parallel-level` egyidejű másolási műveletek számát adja meg. Alapértelmezés szerint az AzCopy bizonyos száma párhuzamos műveletek az adatok átvitel átviteli sebesség növelése elindul. A párhuzamos műveletek száma rendelkezik processzorok száma nyolc alkalommal. AzCopy kis sávszélességű hálózaton keresztül futtatja, ha sikertelen a erőforrás konkurencia elkerülése érdekében a--párhuzamos szintű kevesebb is megadhat.

>[!TIP]
>AzCopy paraméterek teljes listájának megtekintéséhez tekintse meg a "azcopy – súgó" menü.

## <a name="known-issues-and-best-practices"></a>Ismert problémák és ajánlott eljárások
### <a name="error-net-sdk-20-is-not-found-in-the-system"></a>Hiba: A .NET SDK 2.0-s nem található a rendszerben.
AzCopy attól függ, hogy a .NET SDK 2.0-s verziójától kezdve az AzCopy 7.0-s verzió. Korábbi verziói AzCopy használt .NET Core 1.1. Ha hibaüzenet jelenik meg, amely meghatározza, hogy, hogy a .NET Core 2.0 nincs telepítve a rendszeren, szükség lehet telepíteni vagy frissíteni a használatával a [.NET Core telepítési utasításokat](https://www.microsoft.com/net/learn/get-started/linuxredhat).

### <a name="error-installing-azcopy"></a>Hiba történt az AzCopy telepítése
Ha hibát tapasztal az AzCopy telepítési, megpróbálhatja újból futtatni a bash parancsfájlok használatát a kibontott AzCopy `azcopy` mappa.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Adatok másolása egyidejű írási műveletek korlátozása
AzCopy rendelkező fájlokat vagy a BLOB másolása esetén vegye figyelembe, hogy egy másik alkalmazás módosítja az adatok másolása, amíg. Ha lehetséges győződjön meg arról, hogy a másolt adatok nem áll módosítás alatt a másolási művelet során. Például, ha egy Azure virtuális géphez társított virtuális merevlemez, győződjön meg arról, hogy más alkalmazás nem jelenleg írás a virtuális merevlemezhez. Egy jó úgy ehhez, hogy az erőforrás másolandó lízing. Alternatív megoldásként először hozza létre a virtuális merevlemez pillanatképet, és másolja a pillanatkép.

A blobok vagy a fájlok írása közben másolja őket, hogy más alkalmazások nem megakadályozása, majd vegye figyelembe, hogy az idő, a feladat befejeződik, a másolt erőforrások már nincs a forrás-erőforrások teljes paritás.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Futtassa az AzCopy egy példány egy számítógépen.
AzCopy célja, hogy felgyorsítsa az adatok átvitele a gép erőforrás-felhasználás, azt javasoljuk, hogy csak egy AzCopy példány egy számítógépen futtatja, és adja meg a beállítást `--parallel-level` Ha több egyidejű műveletek van szüksége. További tudnivalókért írja be a `AzCopy --help parallel-level` a parancssorból.

## <a name="next-steps"></a>További lépések
Azure Storage és AzCopy kapcsolatos további információkért lásd a következőket:

### <a name="azure-storage-documentation"></a>Az Azure Storage-dokumentáció:
* [Az Azure Storage bemutatása](../storage-introduction.md)
* [Tárfiók létrehozása](../storage-create-storage-account.md)
* [A Tártallózó alkalmazással blobok kezelése](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Az Azure parancssori felület 2.0 használatával az Azure Storage](../storage-azure-cli.md)
* [Blob storage-ának C++ használata](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [How to use Blob storage from Java (A Blob Storage használata Javával)](../blobs/storage-java-how-to-use-blob-storage.md)
* [How to use Blob storage from Node.js (A Blob Storage használata Node.js-sel)](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [How to use Blob storage from Pythonnal (A Blob Storage használata Pythonnal)](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Az Azure Storage blogbejegyzések:
* [AzCopy lévő Linux előzetes bejelentése](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Introducing Azure Storage adatátviteli könyvtár megtekintés](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Bevezetéséről szinkron másolatot és testreszabott tartalom típusa](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Általános rendelkezésre állási az AzCopy 3.0 és a tábla és a fájl-támogatással rendelkező az AzCopy 4.0 előzetes bejelentése](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: A felügyeleti teendők központjaként másolással optimalizált](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Írásvédett georedundáns tárolás támogatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Adatátvitelt újraindítható móddal és SAS-jogkivonat](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Kereszt-fiók másolási Blob használatával](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Feltöltése/fájlok letöltése Azure Blobokra vonatkozó](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

