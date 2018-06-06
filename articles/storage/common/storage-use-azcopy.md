---
title: Másolja, vagy helyezze át az adatok Azure Storage az AzCopy Windows |} Microsoft Docs
description: Windows-segédprogram az AzCopy segítségével áthelyezi vagy másolja az adatokat, vagy a blob, table és a fájl. Adatok másolása az Azure Storage a helyi fájlokból, vagy másolja az adatokat belül vagy tárfiókok között. Adatok áttelepítése egyszerű Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: seguler
ms.openlocfilehash: 430979cf197138a9e239eba74e50e9f97d96cbf6
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757604"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Adatátvitel az AzCopy a Windows rendszeren
AzCopy egy parancssori segédprogram, és a Microsoft Azure Blob, a fájl és a tábla tárolási, adat másolása az optimális teljesítményének készült egyszerű parancsok használatával. Az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja.  

AzCopy, letöltheti a két verziója van. Windows AzCopy parancssori lehetőséget kínál a Windows stílusát. [AzCopy Linux](storage-use-azcopy-linux.md) ajánlat POSIX-stílusú parancssori kapcsolók Linux platformon célozza. Ez a cikk a Windows AzCopy ismerteti.

## <a name="download-and-install-azcopy-on-windows"></a>Töltse le és telepítse az AzCopy Windows rendszeren

### <a name="latest-preview-version-v800"></a>Legújabb előzetes verziójával (v8.0.0)
Töltse le a [az AzCopy Windows a legújabb előzetes verziójával](http://aka.ms/downloadazcopypr). Jelen előzetes verziójában teljesítményének jelentős növelése és a csomagok .NET Core telepítésében kínál.

#### <a name="azcopy-on-windows-80-preview-release-notes"></a>AzCopy a Windows 8.0-s Preview kibocsátási megjegyzései
- A legújabb verzió a TABLE szolgáltatás már nem támogatott. Tábla exportálása szolgáltatását használja, ha a stabil verzió letöltéséhez.
- .NET Core 2.1-val készült, és minden .NET Core függőség most már a telepítés vannak csomagolva.
- Teljesítményének jelentős növelése egyaránt le- és feltöltése forgatókönyvek

### <a name="latest-stable-version-v710"></a>Legújabb stabil verzióját (v7.1.0)
Töltse le a [Windows AzCopy legújabb stabil verziójának](http://aka.ms/downloadazcopy).

### <a name="post-installation-step"></a>Telepítés utáni lépés

Miután telepítette a AzCopy a Windows, a telepítő használatával, nyisson meg egy parancsablakot, és keresse meg az AzCopy telepítési könyvtárára a számítógép - ha a `AzCopy.exe` végrehajtható található. Ha szükséges, az AzCopy telepítési hely a fájlrendszerbeli elérési is hozzáadhat. Alapértelmezés szerint AzCopy telepítés `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` vagy `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Az első AzCopy parancs írása

Az AzCopy parancs alapvető szintaxisa:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Az alábbi példák bemutatják, különféle forgatókönyvekhez, amik másolása adatok és a Microsoft Azure-Blobokkal, fájlok és táblák esetében. Tekintse meg a [AzCopy paraméterek](#azcopy-parameters) részletes leírását az egyes mintában használt paraméterek szakaszban.

## <a name="download-blobs-from-blob-storage"></a>A Blob-tároló blobok letöltése

Töltse le a blobok AzCopy használatával számos módon vizsgáljuk meg.

### <a name="download-a-single-blob"></a>Egy blob letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Vegye figyelembe, hogy ha a mappa `C:\myfolder` nem létezik, AzCopy hoz létre, és a letöltési `abc.txt ` az új mappába.

### <a name="download-a-single-blob-from-the-secondary-region"></a>A másodlagos régióban egyetlen blob letöltése

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Vegye figyelembe, hogy engedélyezve van a másodlagos régióba eléréséhez írásvédett georedundáns tárolás kell rendelkeznie.

### <a name="download-all-blobs-in-a-container"></a>Minden, a tárolóban lévő blobok letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Tegyük fel, a következő blobot a megadott tárolóban találhatók:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

A könyvtár a letöltési művelet után `C:\myfolder` a következő fájlokat tartalmazza:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Ha nem adja meg a beállítás `/S`, nincs BLOB letöltődnek.

### <a name="download-blobs-with-a-specific-prefix"></a>A megadott előtaggal blobok letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Tegyük fel, a következő blobok találhatók a megadott tárolóban. A előtaggal kezdődő összes BLOB `a` lesznek letöltve:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

A letöltési mappa művelet után `C:\myfolder` a következő fájlokat tartalmazza:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Az előtag a virtuális könyvtárban, a blob nevének első részét képező vonatkozik. A fenti példában a virtuális könyvtár nem egyezik a megadott előtagot, így azt nem töltődik le. Emellett ha a beállítás `/S` nincs megadva, az AzCopy nem tölti le a blobokat.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Állítsa be az exportált fájlokat lehet ugyanaz, mint a forrás utolsó módosításának időpontja

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Blobok emellett kizárja a letöltési művelet az utolsó módosításának ideje alapján. Például, ha ki szeretné zárni a blobok, amelyek utolsó módosításának ideje azonos vagy újabb, mint a célfájl, vegye fel a `/XN` lehetőséget:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Ha ki szeretné zárni a blobok, amelyek utolsó módosításának ideje azonos vagy régebbi, mint a célfájl, vegye fel a `/XO` lehetőséget:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Blobok feltöltése a Blob storage

Töltse fel a blobok AzCopy használatával számos módon vizsgáljuk meg.

### <a name="upload-a-single-blob"></a>Egy blob feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Egy virtuális könyvtárat egy blob feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Ha a megadott virtuális könyvtár nem létezik, az AzCopy feltölti a fájlt a virtuális könyvtár nevét (*pl.*, `vd/abc.txt` a fenti példában).

### <a name="upload-all-blobs-in-a-folder"></a>A mappában található összes BLOB feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Beállítás megadása `/S` feltölt Blob storage rekurzív módon, ami azt jelenti, hogy minden almappa és a fájlok feltöltése, valamint a megadott könyvtár tartalmát. Például a mappában találhatók a következő fájlok feltételezik `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

A tároló a feltöltési művelet után a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Ha nem adja meg a beállítás `/S`, AzCopy töltse fel a rekurzív módon. A tároló a feltöltési művelet után a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Egy adott minta megfelelő blobok feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Tegyük fel, a következő fájlok mappában találhatók `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

A tároló a feltöltési művelet után a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Ha nem adja meg a beállítás `/S`, AzCopy csak feltölti a blobok, amelyek nem találhatók a virtuális könyvtárban:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Adjon meg egy cél blob MIME tartalomtípus

Alapértelmezés szerint az AzCopy beállítja egy cél blobot tartalomtípusa `application/octet-stream`. 3.1.0 verziójával kezdve explicit módon megadhatja a tartalomtípus keresztül beállítás `/SetContentType:[content-type]`. Ez a szintaxis a feltöltési művelet állítja be a content-type összes BLOB.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Ha megad `/SetContentType` , érték nélküli AzCopy állítja be, minden egyes blob vagy a fájl tartalomtípusa alapján a fájl kiterjesztése.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>A Blob storage BLOB másolása

Számos módon másolja a blobok egyik helyről egy másikra nézzük AzCopy használatával.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Egy tároló másik tárfiókon belül egyetlen blob másolása 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Ha egy blobba egy tárfiókon belül másol egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Egy blob másolása egy tárfiókot a másikba

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

A blob Storage-fiókok között másolásakor egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>A másodlagos régióba egyetlen blob másolja az elsődleges régióban

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Vegye figyelembe, hogy engedélyezve van a másodlagos tároló elérésére írásvédett georedundáns tárolás kell rendelkeznie.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Másolhat egy blob és a pillanatképek egy tárfiók egy másikra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

A másolási művelet után a céltároló tartalmaz a blob és a pillanatképek. Ha a fenti példában a blob két pillanatképekkel rendelkezik, a tároló tartalmazza a következő blob és a pillanatképek:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Egy tároló összes BLOB másolása másik tárolási fiókot 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Beállítás megadása /S feltölti a tartalmát a megadott tároló rekurzív módon. Lásd: [egy mappában található összes BLOB feltöltése](#upload-all-blobs-in-a-folder) például és további információt.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Szinkron módon másolhat blobokat egy tárfiók egy másikra

AzCopy alapértelmezés szerint aszinkron módon másolja az adatokat két tárolási végpontok közötti. Ezért a másolási művelet fut a háttérben, amely nem szolgáltatásiszint-szerződés szempontjából hogyan gyors rendelkezik tartalékolt sávszélesség tartalékkapacitását használja, a blob másolása, és az AzCopy rendszeresen ellenőrzi a példány állapotát, amíg nem fejeződött be, vagy a másolása nem sikerült.

A `/SyncCopy` lehetőség biztosítja, hogy a másolási művelet lekérdezi az egységes sebesség. AzCopy a szinkron másolatot helyi memória a megadott forrás másolása a blobok letöltése, és feltöltheti a Blob storage cél végez.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` További kilépő költség képest aszinkron másolási hozhat létre, az ajánlott módszer az, hogy egy Azure virtuális gép, amely ugyanabban a régióban, a forrás tárolási fiókkal kilépő költségek elkerülése érdekében használja ezt a beállítást.

## <a name="download-files-from-file-storage"></a>A File storage a fájlok letöltése

Töltse le a fájlokat a AzCopy számos módon vizsgáljuk meg.

### <a name="download-a-single-file"></a>Egyetlen fájl letöltése

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Ha a megadott forrás az Azure fájlmegosztások, akkor meg kell adnia a fájl pontos nevét (*pl.* `abc.txt`) egyetlen fájl letöltéséhez vagy beállítást adja meg `/S` a megosztás rekurzív módon található összes fájl letöltéséhez. Adjon meg egy fájl mintát és a beállítás próbál `/S` hiba együtt eredményez.

### <a name="download-all-files-in-a-directory"></a>A könyvtárban található összes fájl letöltése

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Vegye figyelembe, hogy a rendszer nem tölti le üres mappák.

## <a name="upload-files-to-an-azure-file-share"></a>Fájlok feltöltése az Azure fájlmegosztások

Töltse fel az AzCopy használatával számos módon vizsgáljuk meg.

### <a name="upload-a-single-file"></a>Egyetlen fájl feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>A mappában lévő összes fájl feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Vegye figyelembe, hogy az üres mappák nem töltődött fel.

### <a name="upload-files-matching-a-specific-pattern"></a>Egy adott minta egyező fájlok feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>A File storage a fájlok másolása

Fájlok másolása az Azure fájlmegosztások AzCopy használatával számos módon vizsgáljuk meg.

### <a name="copy-from-one-file-share-to-another"></a>Egy fájlmegosztás másolása a másikba

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor fájlmegosztások között másolhat egy fájlt egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Az Azure fájlmegosztások átmásolhatja Blob-tároló

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor másolhat egy fájlt a blobra, fájlmegosztásról egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>A Blob storage blob másolása az Azure fájlmegosztások

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor másol egy fájlt egy blobba egy fájlmegosztást, egy [kiszolgálóoldali másolatot](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) művelet.

### <a name="synchronously-copy-files"></a>Szinkron módon történik a fájlok másolása

Megadhatja a `/SyncCopy` adatok másolása a File Storage a File Storage, File Storage-Blob Storage és File Storage Blob Storage szinkron módon beállításnál AzCopy ezt hajtja végre az adatok letöltése a helyi memória, és töltse fel újra cél. Standard kilépő költség érvényes.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Amikor a File storage másol a Blob storage, az alapértelmezett blob típushoz-e a blokkblob; a felhasználó megadhatja a beállítás `/BlobType:page` módosíthatja a blob típusára.

Vegye figyelembe, hogy `/SyncCopy` hozhat létre további kilépő költségek aszinkron másolási képest. Az ajánlott módszer a kapcsoló használatához a Azure virtuális gépen, amely kilépő költségek elkerülése érdekében a forrás tárfiók ugyanabban a régióban.

## <a name="export-data-from-table-storage"></a>A Table storage adatok exportálása

Vessen egy pillantást Azure Table storage használatának AzCopy történő exportálását.

### <a name="export-a-table"></a>Tábla exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy ír a megadott célmappát jegyzékfájlt. A jegyzékfájl az importálási folyamat szerepel állapítsa meg a szükséges adatok helyét és adatellenőrzése. A jegyzékfájl alapértelmezés szerint használja a következő elnevezés szabály szerint:

    <account name>_<table name>_<timestamp>.manifest

Felhasználói is adja meg a beállítást `/Manifest:<manifest file name>` beállítása a jegyzékfájl neve.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>A Table storage az Exportálás felosztása több fájl

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy használ egy *kötet index* a megosztott adatok fájlnevekben segítségével különböztetheti meg egymástól több fájlt. A kötet index két részből áll egy *partíciós kulcs tartományindexszel* és egy *vegyes fájl index*. Mindkét indexek nulla alapú.

A partíciós kulcs tartományindexszel értéke 0, ha a felhasználó nem adja meg a beállítás `/PKRS`.

Tegyük fel például, AzCopy két adatfájlok állít elő, miután a felhasználó határozza meg a beállítás `/SplitSize`. Az eredményül kapott fájl nevének lehet:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Vegye figyelembe, hogy a beállítás értékének a legkisebb lehetséges `/SplitSize` 32 MB-nál. Ha egy konkrét célhoz Blob-tároló, AzCopy az adatfájl felosztja a után annak mérete eléri a blob mérete korlátozást (200 GB-os), függetlenül attól, hogy lehetőséget `/SplitSize` a felhasználó által meghatározott.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>A JSON- vagy CSV adatfájlformátumának tábla exportálása

Alapértelmezés szerint az AzCopy táblák exportálása JSON-adatfájljait. Megadhatja, hogy a beállítás `/PayloadFormat:JSON|CSV` JSON vagy a fürt megosztott kötetei szolgáltatás a táblák exportálása.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

A fürt megosztott kötetei szolgáltatás az adattartalom formátuma megadásakor AzCopy is, ami egy séma kiterjesztésű fájlt `.schema.csv` minden olyan adatfájl esetében.

### <a name="export-table-entities-concurrently"></a>Táblaentitásokat egyidejűleg exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy elindítja a párhuzamos műveletek entitások exportálása, amikor a felhasználó határozza meg a beállítás `/PKRS`. Egyes műveletek egy kulcs partíciótartomány exportálja.

Vegye figyelembe, hogy a párhuzamos műveletek számát is beállítás által vezérelt `/NC`. AzCopy Processzormagok száma használja, mint az alapértelmezett érték `/NC` táblaentitásokat, másolásakor akkor is, ha `/NC` nincs megadva. Amikor a felhasználó határozza meg a beállítás `/PKRS`, AzCopy használja a két érték közül a kisebbet,-partíció kulcstartományokkal és implicit vagy explicit módon megadott párhuzamos műveletek – a párhuzamos műveletek elindításához számának meghatározásához. További tudnivalókért írja be a `AzCopy /?:NC` a parancssorból.

### <a name="export-a-table-to-blob-storage"></a>A Blob storage tábla exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy JSON adatfájlt a következő elnevezési konvenció blob tárolóba állít elő:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

A létrehozott JSON-fájl a következő minimális metaadat az adattartalom formátuma. Ez az adattartalom formátuma a részletekért lásd: [az adattartalom formátuma Table szolgáltatási műveletek](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Ne feledje, hogy ha exportál táblák blobokat, AzCopy helyi ideiglenes fájlokat tölti le a táblaentitásokat majd feltölti a blob entitásokból. Ezek ideiglenes fájlokat az alapértelmezett elérési úttal rendelkező napló fájl mappába kerülnek "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", lehetőséget adhat meg/a napló módosítása [napló-fájlok és mappák] Z: fájl mappa helyét, és így módosíthatja az ideiglenes fájlok helyén. Az ideiglenes fájlok mérete, amelyekről a táblaentitásokat és a méretet, a megadott beállítás /SplitSize bár ideiglenes adatokat törli a fájlt a helyi lemez azonnal Miután feltöltötte a blobra mutató adatok győződjön meg arról, hogy elég helyi szabad lemezterület a ideiglenes fájlokat tárolja a törlés előtt.

## <a name="import-data-into-table-storage"></a>Adatok importálása a Table storage

Vessen egy pillantást adatok importálása az Azure Table storage használatának AzCopy.

### <a name="import-a-table"></a>A tábla importálása

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

A beállítás `/EntityOperation` azt jelzi, hogyan entitások beszúrására a táblában. Lehetséges értékek:

* `InsertOrSkip`: A meglévő entitás kihagyja vagy szúr be egy új entitást, ha a tábla nem létezik.
* `InsertOrMerge`: Egyesíti a meglévő entitás vagy szúr be egy új entitást, ha a tábla nem létezik.
* `InsertOrReplace`: A felváltja meglévő entitás vagy szúr be egy új entitást, ha a tábla nem létezik.

Vegye figyelembe, hogy a beállítás nem adható meg `/PKRS` importálási forgatókönyvben. Az Exportálás kapcsolót kell megadnia a forgatókönyvben eltérően `/PKRS` párhuzamos műveletek megkezdéséhez AzCopy párhuzamos műveletek alapértelmezés szerint elindul egy tábla importálásakor. Az alapértelmezett száma párhuzamos műveletek indítása megegyezik a Processzormagok; száma azonban megadhat egyidejű lehetőséggel különböző számú `/NC`. További tudnivalókért írja be a `AzCopy /?:NC` a parancssorból.

Ügyeljen arra, hogy AzCopy csak támogatja a JSON, CSV nem importálása. AzCopy nem támogatja a felhasználó által létrehozott JSON tábla származó és fájlok manifest. Az AzCopy tábla exportálása mindkét fájl kell származnia. Hibák elkerülése érdekében adjon módosítsa az exportált JSON és jegyzékfájl.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Entitások importálása egy táblába a Blob-tároló

Tegyük fel, a Blob-tároló tartalmazza a következő: az Azure-tábla és a hozzá tartozó jegyzékfájl jelölő A JSON-fájlt.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Entitások importálása egy táblába a jegyzékfájl az adott blob tároló a következő parancsot futtathatja:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Más AzCopy szolgáltatások

Vessen egy pillantást néhány más szolgáltatások AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Csak másolja az adatokat, a cél nem létezik

A `/XO` és `/XN` paraméterek lehetővé teszik a régebbi vagy újabb forrás erőforrások másolását, illetve kizárása. Ha szeretné, amelyek nem léteznek a cél a forrás-erőforrások másolása, mindkét paraméter az AzCopy parancs adhat meg:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Vegye figyelembe, hogy ez nem támogatott, ha a cél- és egy tábla.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Egy válaszfájl segítségével adja meg a parancssori paraméterek

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

AzCopy parancssori paramétereket is megadhat a egy válaszfájlt. AzCopy dolgozza fel a paraméterek a fájlban, ha a parancssorban megadott lett hajt végre a fájl tartalma közvetlen helyettesítés.

Egy válaszfájl nevű feltételezik `copyoperation.txt`, amely tartalmazza a következő sorokat. Minden egyes AzCopy paraméter adható meg egy sorba

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

vagy külön sorok:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy sikertelen lesz, ha a paraméter osztani két sort, az itt látható a `/sourcekey` paraméter:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Több fájl használható a válasz parancssori paraméterek megadása

Egy válaszfájl nevű feltételezik `source.txt` , amely megadja, hogy a forrás-tároló:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

És egy válaszfájlt nevű `dest.txt` egy célmappát, amely meghatározza a fájlrendszer:

    /Dest:C:\myfolder

És egy válaszfájlt nevű `options.txt` , amely megadja, hogy az AzCopy beállítások:

    /S /Y

AzCopy hívni a válasz fájlok, amelyek találhatók a könyvtárban található `C:\responsefiles`, használja ezt a parancsot:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy végrehajtja ezt a parancsot, ahogy bármilyen ha tartalmazza a minden egyes paraméter a parancssorban:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Adja meg a közös hozzáférésű jogosultságkód (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Azt is megadhatja egy SAS URI tárolón:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Napló fájlmappa

Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi, hogy a napló fájl megtalálható-e az alapértelmezett mappába, vagy hogy keresztül ez a beállítás a megadott mappa létezik. A napló fájl nem létezik egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl nem létezik, AzCopy ellenőrzi, hogy a parancssor, amely a megadott megegyezik-e a parancssorban a napló fájlban. Ha a két parancssorokat egyeznek, AzCopy folytatja a teljes műveletet. Ha nem egyeznek, vagy felülírja napló új művelet indítása és az aktuális művelet megszakítására kéri.

Ha szeretné a naplófájl alapértelmezett helyet használja:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Ha a beállítás nincs megadva `/Z`, vagy adja meg a beállítás `/Z` anélkül, hogy a mappa elérési útja, a fent látható AzCopy fájlt hoz létre a napló az alapértelmezett helyen, amely `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Ha a napló fájl már létezik, majd AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha szeretne egy egyéni napló elérési útját adja meg:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Ebben a példában a napló fájlt hoz létre, ha még nem létezik. Ha létezik, majd AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha folytatni szeretné az AzCopy műveletet:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Ebben a példában újraindítja a legutóbbi műveletet, és nem sikerült végrehajtani.

### <a name="generate-a-log-file"></a>A naplófájl létrehozása

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Ha a beállítás megadja `/V` anélkül, hogy a fájl elérési útját a részletes naplózás, majd AzCopy hozza létre a naplófájlt, amely alapértelmezett helyen `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Ellenkező esetben egy naplófájlt is létrehozhat egy egyéni helyen:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Vegye figyelembe, hogy ha a beállítás a következő relatív elérési utat ad meg `/V`, például a `/V:test/azcopy1.log`, akkor a részletes napló létrehozása az aktuális munkakönyvtárban belül egy almappát `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Adja meg elindítani a párhuzamos műveletek száma

A beállítás `/NC` egyidejű másolási műveletek számát adja meg. Alapértelmezés szerint az AzCopy bizonyos száma párhuzamos műveletek az adatok átvitel átviteli sebesség növelése elindul. A tábla műveleteket párhuzamos műveletek száma megegyezik rendelkezik processzorok száma. A Blob és a fájl műveleteket, párhuzamos műveletek számát 8 alkalommal az a szám egyezzen processzorral rendelkezik. AzCopy kis sávszélességű hálózaton keresztül futtatja, ha kevesebb /NC hibáját okozta. erőforrás konkurencia elkerülése érdekében a is megadhat.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>AzCopy futtassa az Azure Storage emulatorban

AzCopy ellen is futtathatja a [Azure Storage Emulator](storage-use-emulator.md) a blobok:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Akkor is futtatható táblák:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Automatikusan meghatározni a BLOB tartalom típusa

AzCopy tartalomtípusa egy blobot, amely tárolja a bővítmény fájlhozzárendelést tartalomtípus JSON-fájl alapján határozza meg. A JSON-fájl neve AzCopyConfig.json, és az AzCopy könyvtárban található. Ha még nem szerepel a listában fájltípust fűzheti a leképezés a JSON-fájlba:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>AzCopy paraméterek

AzCopy paramétereinek az alábbiakban található. Is beírhatja a Súgó a parancssorból a következő parancsok egyikét az AzCopy segítségével:

* AzCopy részletes parancssori segítséget: `AzCopy /?`
* További információt az AzCopy paramétert: `AzCopy /?:SourceKey`
* A parancssori példák: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Source: "forrás"

Megadja a forrás adatait, amelynek be kell másolni. A forrás lehet egy fájl rendszer könyvtár, egy blob-tároló, blob virtuális könyvtár, egy tárolófájl-megosztás, tároló fájl könyvtár, vagy egy Azure-tábla.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="destdestination"></a>/ Cél: "cél"

Megadja azt a helyet, másolja. A cél lehet fájl rendszer könyvtár, egy blob-tároló, egy blob virtuális könyvtár, egy tárolói fájlmegosztást, egy tárolási könyvtárának vagy egy Azure-tábla.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="patternfile-pattern"></a>/ Mintát: "fájl-minta"

Adja meg a fájl mintát, amely azt jelzi, mely fájlokat másolni. A /Pattern paraméter viselkedését az adatok helye és a rekurzív mód beállítása jelenléte határozza meg. Rekurzív mód keresztül /s beállítás van megadva.

Ha a megadott forrás egy könyvtárat a fájlrendszer, majd szabványos helyettesítő karakterek érvényes, és a megadott fájl minta egyezik a könyvtárban lévő fájlokra vonatkozóan. Ha beállítása/s van megadva, majd AzCopy is megfelel a megadott minta elleni almappákban alatt a könyvtárban található összes fájl.

Ha a megadott forrás egy blob-tároló vagy virtuális könyvtárat, majd helyettesítő karakterek nem érvényesek. Ha a beállítás /s kapcsoló meg van adva, majd AzCopy a megadott fájl mintát blob előtagjaként értelmezi. Ha a beállítás /s kapcsoló nincs megadva, majd AzCopy pontos blob nevekkel fájlminta megegyezik.

Ha a megadott forrás az Azure fájlmegosztások, akkor állítsa adja meg a pontos fájl (pl. abc.txt) egyetlen fájlba másolni, vagy adja meg a beállítás a a megosztás rekurzív módon másolja az összes fájlt a/s. Adja meg mindkét fájl mintát és beállítás /S együtt eredmény hiba történt kísérlet.

AzCopy használja a kis-és nagybetűket megfelelő, ha a/Source a blob-tároló vagy a blob virtuális könyvtár, és használja, minden más esetben azonban nem megfelelő.

Az alapértelmezett fájl minta használható, ha nincs fájl mintát *.* egy olyan fájlhelyre rendszer vagy egy Azure Storage helyének egy üres előtag. Több fájl minták megadása nem támogatott.

**Alkalmazandó:** Blobok, fájlok

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Adja meg a tárfiók hívóbetűjét a cél az erőforráshoz.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

OLVASÁSI és írási jogosultsággal a cél egy közös hozzáférésű Jogosultságkód (SAS) határozza meg, (ha van ilyen). Helyezze a dupla idézőjelek között, SAS tartalmaz, így előfordulhat, hogy speciális parancssori karaktereket.

Ha a célként megadott erőforrás a blob-tároló, a fájlmegosztás vagy a táblát, vagy adja meg ezt a beállítást, a SAS-jogkivonat követ, vagy a cél blob tároló, a fájlmegosztás vagy a tábla URI, anélkül, hogy ez a beállítás részeként is megadhat az SA-kat.

Ha a forrás és cél mindkét blobokat, majd a cél blob, a forrás blob tárfiókon belül kell lennie.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Adja meg a tárfiók hívóbetűjét a forrás-erőforrás.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Adja meg a közös hozzáférésű Jogosultságkód OLVASÁSI és lista engedélyekkel a forrás (ha van ilyen). Helyezze a dupla idézőjelek között, SAS tartalmaz, így előfordulhat, hogy speciális parancssori karaktereket.

Ha a forrás erőforrás egy blob-tároló, és nem egy kulcs, és SAS-kód nem áll, majd a blob-tároló írásvédett keresztül névtelen hozzáférés.

Ha a forrás egy fájlmegosztás vagy a tábla, meg kell adni egy kulcs- vagy SAS-kód.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="s"></a>/S

Meghatározza a másolási műveletek rekurzív módját. Rekurzív módban AzCopy blobokkal vagy a fájlokat, amelyek megfelelnek a megadott fájl mintát, beleértve az almappákat másolja át.

**Alkalmazandó:** Blobok, fájlok

### <a name="blobtypeblock--page--append"></a>/ BlobType: "block" |} "lap" |} "append"

Megadja, hogy a cél blob egy blokkblob, oldalakra vonatkozó blob vagy hozzáfűző blob. Ez a beállítás csak egy blob feltölteni kívánt esetén alkalmazható. Ellenkező esetben hiba történik. Ha a cél egy blobot, és ez a beállítás nincs megadva, alapértelmezés szerint az AzCopy egy blokkblob hoz létre.

**Alkalmazandó:** Blobok

### <a name="checkmd5"></a>/ CheckMD5

Kiszámítja az MD5 kivonatoló letöltött adatok, és ellenőrzi, hogy a blob tárolja az MD5 kivonatoló vagy a fájl Content-MD5 tulajdonsága egyezést mutat a kiszámított kivonatát. Az MD5-ellenőrzése ki van kapcsolva, alapértelmezés szerint, meg kell adnia ezt a beállítást, végezze el az MD5 ellenőrzését, amikor az adatok letöltése.

Figyelje meg, hogy az Azure Storage nem garantálja, hogy naprakész állapotban-e az MD5 kivonatoló a blob vagy a fájl tárolja. Feladata ügyfél frissítése az MD5, amikor a blob vagy a fájl módosítása.

AzCopy a Content-MD5 tulajdonság az Azure blob vagy a fájl mindig beállítása után ismét feltölteni a szolgáltatást.  

**Alkalmazandó:** Blobok, fájlok

### <a name="snapshot"></a>/Snapshot

Azt jelzi, hogy pillanatképet továbbít. Ez a beállítás csak akkor érvényes, ha a forrás, a blob.

Az átvitt blob pillanatképek átnevezi a következő formátumban: .extension blob-név (pillanatkép-time)

Alapértelmezés szerint a pillanatképek nem kerülnek.

**Alkalmazandó:** Blobok

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Kimeneti részletes üzenetek fájlba.

Alapértelmezés szerint a részletes naplófájl neve a AzCopyVerbose.log `%LocalAppData%\Microsoft\Azure\AzCopy`. Ezt a lehetőséget egy meglévő helyét adja meg, ha a részletes naplózás fűz hozzá a fájlhoz.  

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

A művelet folytatása napló fájl mappáját adja meg.

AzCopy mindig támogatja a Folytatás, ha egy művelet megszakadt.

Ha ez a beállítás nincs megadva, vagy egy mappa elérési útját nélkül van megadva, majd AzCopy hoz létre a naplófájl az alapértelmezett helyen, amely % LocalAppData%\Microsoft\Azure\AzCopy.

Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi, hogy a napló fájl megtalálható-e az alapértelmezett mappába, vagy hogy keresztül ez a beállítás a megadott mappa létezik. A napló fájl nem létezik egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl nem létezik, AzCopy ellenőrzi, hogy a parancssor, amely a megadott megegyezik-e a parancssorban a napló fájlban. Ha a két parancssorokat egyeznek, AzCopy folytatja a teljes műveletet. Ha nem egyeznek, vagy felülírja napló új művelet indítása és az aktuális művelet megszakítására kéri.

A napló fájl törlődik a művelet sikeres befejezését követően.

Vegye figyelembe, hogy a Folytatás, az AzCopy egy korábbi verziójával létrehozott napló fájlból egy művelet nem támogatott.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="parameter-file"></a>/@:"Parameter-File"

Megadja a paramétereket tartalmazó fájlt. AzCopy dolgozza fel a paramétereket a fájlt a, mintha csak a parancssorban megadott lett.

A válaszfájlban több paramétert meg egyetlen vonal, vagy adja meg az egyes paramétereket külön sorban tüntettük. Vegye figyelembe, hogy az egyes paraméter nem terjedhetnek többsoros.

Válasz tartalmazhatnak a # karakterrel kezdődő sorok megjegyzések.

Válasz több fájl is megadható. Vegye figyelembe azonban, hogy az AzCopy nem támogatja a beágyazott válaszfájlok.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="y"></a>/Y

Letiltja az összes AzCopy megerősítést kér. Ezt a lehetőséget is lehetővé teszi az adatok feltöltése forgatókönyvek esetén csak írható SAS-tokenje használatát, ha nincs megadva a /XO és /XN.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="l"></a>/ L

Megadja a listázási művelet csak; adatot nem másolódik.

AzCopy értelmezi használja ezt a beállítást a szimuláció futtatásához e nélkül a parancssor/l és számát, hogy hány objektumok kerülnek beállításnál megadhatja, hogy mely objektumok kereséséhez egyszerre /V kerülnek a részletes napló beállítás.

Ez a beállítás viselkedése is határozza meg az adatok helye és a rekurzív mód beállítás/s és a fájl minta beállítás /Pattern jelenlétét.

AzCopy, ez a hely LISTÁJÁT, és OLVASÁSI engedélyre van szüksége, ez a beállítás használata esetén.

**Alkalmazandó:** Blobok, fájlok

### <a name="mt"></a>/MT

Beállítja a letöltött fájl utolsó módosításának ideje azonosnak kell lennie a forrás blob vagy a fájl.

**Alkalmazandó:** Blobok, fájlok

### <a name="xn"></a>/XN

Nem tartalmazza egy újabb forráserőforrásnak. Az erőforrás nem másolódik, ha a forrás utolsó módosítási időpontjának azonos vagy újabb, mint a cél.

**Alkalmazandó:** Blobok, fájlok

### <a name="xo"></a>/XO
Nem tartalmazza egy régebbi forráserőforrásnak. Az erőforrás nem másolódik, ha a forrás utolsó módosítási időpontjának azonos vagy régebbi, mint a cél.

**Alkalmazandó:** Blobok, fájlok

### <a name="a"></a>/A

Csak a Archiválandó fájlok feltöltését.

**Alkalmazandó:** Blobok, fájlok

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Csak a megadott attribútumok közül bármelyik rendelkező fájlok feltöltését.

A rendelkezésre álló attribútumok a következők:

* R = csak olvasható fájlokat
* A = archiválásra kész
* S rendszerfájlok =
* H = Rejtett fájlok
* C = tömörített fájlok
* N = normál fájlok
* E titkosított fájlok =
* T ideiglenes fájlok =
* O = Offline fájlok
* I = nem indexelt fájlok

**Alkalmazandó:** Blobok, fájlok

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Olyan fájlra, amely a megadott attribútumok közül bármelyik nem tartalmazza.

A rendelkezésre álló attribútumok a következők:

* R = csak olvasható fájlokat
* A = archiválásra kész
* S rendszerfájlok =
* H = Rejtett fájlok
* C = tömörített fájlok
* N = normál fájlok
* E titkosított fájlok =
* T ideiglenes fájlok =
* O = Offline fájlok
* I = nem indexelt fájlok

**Alkalmazandó:** Blobok, fájlok

### <a name="delimiterdelimiter"></a>/ Elválasztó karakter: "elválasztót".

Azt jelzi, hogy az elválasztó karaktert, amely korlátozza a virtuális könyvtárak egy blob neve.

Alapértelmezés szerint az AzCopy által használt / elválasztó karakterként. Azonban AzCopy támogatja a közös karakter használatát (például a @, #, vagy %) elválasztó. Ha meg kell adnia egy, a következő speciális karaktereket a parancssorban, tegye idézőjelek közé foglalt a fájl nevét.

Ez a beállítás csak akkor alkalmazható blobok letöltése.

**Alkalmazandó:** Blobok

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Megadja a párhuzamos műveletek számát.

AzCopy alapértelmezés szerint elindul bizonyos száma párhuzamos műveletek adatok átvitel növelhető. Ne feledje, hogy nagy száma párhuzamos műveletek alacsony sávszélességű környezetben előfordulhat, hogy ne terhelje tovább a hálózati kapcsolat tiltása a műveletek teljes befejezését. Párhuzamos műveletek alapján tényleges rendelkezésre álló hálózati sávszélesség szabályozását.

A párhuzamos műveletek felső határa 512.

**Alkalmazandó:** blobokat, fájlok, táblák

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Megadja, hogy a `source` erőforrás elérhető a helyi fejlesztési környezetben, a storage emulator-beli blob.

**Alkalmazandó:** Blobok, táblák

### <a name="desttypeblob--table"></a>/ DestType: "Blob" |} "Table"

Megadja, hogy a `destination` erőforrás elérhető a helyi fejlesztési környezetben, a storage emulator-beli blob.

**Alkalmazandó:** Blobok, táblák

### <a name="pkrskey1key2key3"></a>/ PKRS: "key1 #key2 #key3 #..."

Felosztja a tábla adatexportálási párhuzamosan, ami növeli az exportálási művelet sebességének engedélyezése kulcs partíciótartomány.

Ha ez a beállítás nincs megadva, majd AzCopy segítségével egyetlen szálon táblaentitásokat exportálása. Például, ha a felhasználó határozza meg a /PKRS: "aa #bb", akkor az AzCopy három párhuzamos műveletek kezdődik.

Egyes műveletek exportálja egy három partíció kulcstartományokkal, alább látható módon:

  [első partíciókulcs, aa)

  [aa, bb)

  [bb, utolsó partíciókulcs]

**Alkalmazandó:** táblák

### <a name="splitsizefile-size"></a>/ SplitSize: "fájl mérete"

Itt adhatja meg az exportált fájlt, osztott mérete MB-ban, a minimális megengedett érték 32.

Ha ez a beállítás nincs megadva, AzCopy egy fájlba exportálja tábla adatai.

Ha a tábla adatainak exportálása egy blobba, és az exportált fájl mérete eléri a 200 GB-os korlátot a blob-mérethez, majd AzCopy felosztja az exportált fájlt, akkor is, ha ez a beállítás nincs megadva.

**Alkalmazandó:** táblák

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" |} "InsertOrMerge" |} "InsertOrReplace"

Megadja a tábla importálása működéshez.

* InsertOrSkip - kihagyja a meglévő entitás vagy szúr be egy új entitást, ha a tábla nem létezik.
* InsertOrMerge - egyesíti a meglévő entitás vagy szúr be egy új entitást, ha a tábla nem létezik.
* InsertOrReplace - lecseréli a meglévő entitás vagy szúr be egy új entitást, ha a tábla nem létezik.

**Alkalmazandó:** táblák

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

A jegyzékfájl megadja a tábla exportálása és az importálási művelet.

Ez a beállítás nem kötelező, az exportálás során, AzCopy előre definiált nevű jegyzékfájlt állít elő, ha ez a beállítás nincs megadva.

Ez a beállítás akkor szükséges az adatfájlok helyének az importálási művelet során.

**Alkalmazandó:** táblák

### <a name="synccopy"></a>/SyncCopy

Azt jelzi, hogy szinkron módon történik a blobok vagy két Azure Storage-végpontok közötti fájlok másolása.

Alapértelmezés szerint AzCopy kiszolgálóoldali aszinkron másolatot használja. Adja meg ezt a beállítást, amely letölti a blobokat vagy -fájlok helyi memória, és feltölti Azure Storage szinkron másolatot végrehajtásához.

Ezt a beállítást is használhatja, amikor Blob-tároló, a File storage belül, vagy a File storage vagy fordítva blobtárolóból belül a fájlok másolása.

**Alkalmazandó:** Blobok, fájlok

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Megadja a MIME content-type cél blobokkal vagy a fájlokat.

AzCopy állítja be az egy blob vagy a fájl tartalomtípusa application/octet-stream alapértelmezés szerint. Beállíthatja az összes BLOB vagy fájlok tartalomtípusa explicit megadása ehhez a beállításhoz tartozó értéket.

Ha megadja ezt a beállítást érték nélküli, majd AzCopy beállítja minden egyes blob vagy a fájl tartalomtípusa alapján a fájl kiterjesztése.

**Alkalmazandó:** Blobok, fájlok

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" |} "CSV"

Megadja a tábla exportált adatok fájl formátumát.

Ha ez a beállítás nincs megadva, alapértelmezés szerint AzCopy exportálja tábla adatfájl JSON formátumban.

**Alkalmazandó:** táblák

## <a name="known-issues-and-best-practices"></a>Ismert problémák és ajánlott eljárások

Vessen egy pillantást, néhány olyan ismert problémákat és ajánlott eljárások.

### <a name="limit-concurrent-writes-while-copying-data"></a>Adatok másolása egyidejű írási műveletek korlátozása

AzCopy rendelkező fájlokat vagy a BLOB másolása esetén vegye figyelembe, hogy egy másik alkalmazás módosítja az adatok másolása, amíg. Ha lehetséges győződjön meg arról, hogy a másolt adatok nem áll módosítás alatt a másolási művelet során. Például, ha egy Azure virtuális géphez társított virtuális merevlemez, győződjön meg arról, hogy más alkalmazás nem jelenleg írás a virtuális merevlemezhez. Egy jó úgy ehhez, hogy az erőforrás másolandó lízing. Alternatív megoldásként először hozza létre a virtuális merevlemez pillanatképet, és másolja a pillanatkép.

A blobok vagy a fájlok írása közben másolja őket, hogy más alkalmazások nem megakadályozása, majd vegye figyelembe, hogy az idő, a feladat befejeződik, a másolt erőforrások már nincs a forrás-erőforrások teljes paritás.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Az MD5 FIPS előírásainak megfelelő algoritmusok engedélyezése az AzCopy amikor Ön "FIPS előírásainak megfelelő algoritmusok használata titkosításhoz, kivonatoláshoz és aláíráshoz."

AzCopy alapértelmezés szerint a .NET-MD5 végrehajtása használatával kiszámítja az MD5, objektumok másolásakor, de olyan biztonsági követelményekkel, amelyeket AzCopy FIPS előírásainak megfelelő MD5 beállításnak az engedélyezéséhez szükség van.

Létrehozhat egy app.config fájl `AzCopy.exe.config` tulajdonsággal `AzureStorageUseV1MD5` , tegye a AzCopy.exe tartalékoljon.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

For property "AzureStorageUseV1MD5":

* Az alapértelmezett érték, az AzCopy igaz - .NET MD5 végrehajtására használ.
* Hamis – AzCopy FIPS előírásainak megfelelő MD5 algoritmust használ.

Alapértelmezés szerint a Windows a FIPS előírásainak megfelelő algoritmusok le vannak tiltva. Módosíthatja a házirend-beállítás a számítógépre. A Futtatás ablakba (Windows + R), írja be a secpol.msc megnyitásához a **helyi biztonsági házirend** ablak. Az a **biztonsági beállítások** ablakban, keresse meg **biztonsági beállítások** > **helyi házirendek** > **biztonságibeállítások**. Keresse meg a **rendszer-kriptográfia: FIPS előírásainak megfelelő algoritmusok használata titkosításhoz, kivonatoláshoz és aláíráshoz** házirend. A házirendjében a megjelenített érték megjelenítéséhez kattintson duplán a **biztonsági beállítás** oszlop.

## <a name="next-steps"></a>További lépések

További, az Azure Storage szolgáltatással és az AzCopyval kapcsolatos adatokat a következő erőforrások nyújtanak:

### <a name="azure-storage-documentation"></a>Az Azure Storage-dokumentáció:
* [A Microsoft Azure Storage bemutatása](../storage-introduction.md)
* [A .NET-Blob-tároló használata](../blobs/storage-dotnet-how-to-use-blobs.md)
* [File storage .NET használata](../storage-dotnet-how-to-use-files.md)
* [A Table storage a .NET használatával](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Tárfiókok létrehozása, kezelése vagy tárfiók törlése](../storage-create-storage-account.md)
* [Adatok áthelyezése az AzCopyval Linux rendszeren](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Az Azure Storage blogbejegyzések:
* [Introducing Azure Storage adatátviteli könyvtár megtekintés](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Bevezetéséről szinkron másolatot és testreszabott tartalom típusa](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Általános rendelkezésre állási az AzCopy 3.0 és a tábla és a fájl-támogatással rendelkező az AzCopy 4.0 előzetes bejelentése](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: A felügyeleti teendők központjaként másolással optimalizált](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Írásvédett georedundáns tárolás támogatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Adatátvitelt újraindítható móddal és SAS-jogkivonat](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Kereszt-fiók másolási Blob használatával](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Feltöltése/fájlok letöltése Azure Blobokra vonatkozó](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
