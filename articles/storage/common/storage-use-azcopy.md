---
title: Adatok másolása vagy áthelyezése az Azcopyval Azure Storage a Windows |} A Microsoft Docs
description: Windows-segédprogram az AzCopy segítségével áthelyezi vagy másolja az adatokat, vagy a blob, tábla és fájl tartalma. Adatok másolása az Azure Storage a helyi fájlokból vagy adatmásolás belül vagy tárfiókok között. Egyszerűen migrálhatja az adatokat az Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 08/13/2018
ms.author: seguler
ms.component: common
ms.openlocfilehash: c0672ddb3e6791fae3b9b8c04e9ff98827c9e22f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256731"
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Adatok áthelyezése az AzCopy az Windows rendszeren
Az AzCopy egy parancssori segédprogram, és a Microsoft Azure Blob, fájl és Table storage, az adatok másolása szolgál az optimális teljesítmény érdekében tervezett egyszerű parancs használatával. Az adatokat egy fájlrendszer és egy tárfiók, illetve több tárfiók között is másolhatja.  

Nincsenek töltheti le, Azcopynak két verziója. Az AzCopy Windows az Windows stílusú parancssori lehetőségeket kínál. [AzCopy linuxon](storage-use-azcopy-linux.md) POSIX stílusú parancssori lehetőségeket kínál Linux platformokra. Ez a cikk ismerteti az AzCopy Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Töltse le és telepítse az AzCopy Windows

### <a name="latest-version-v81"></a>Legújabb verzióját (v8.1)
Töltse le a [Windows az AzCopy legújabb verzióját](https://aka.ms/downloadazcopy).

#### <a name="azcopy-on-windows-81-release-notes"></a>Az AzCopy Windows 8.1 kibocsátási megjegyzései
- TABLE Storage-szolgáltatás a legújabb verzió már nem támogatott. Tábla exportálása szolgáltatását használja, ha az AzCopy 7.3 verzió letöltéséhez.
- A .NET Core 2.1 használatával létrehozott, és minden .NET Core-függőség most már a telepítés vannak csomagolva.
- OAuth hitelesítési támogatás hozzáadva. Használat ```azcopy login``` , jelentkezzen be az Azure Active Directory használatával.

### <a name="azcopy-with-table-support-v73"></a>Az Azcopy (v7.3) tábla támogatásával
Töltse le a [tábla támogatásával, az AzCopy 7.3](https://aka.ms/downloadazcopynet).

### <a name="post-installation-step"></a>Telepítés utáni lépés

Az AzCopy Windows, a telepítő a telepítés után nyisson meg egy parancsablakot, és keresse meg a számítógép -, az AzCopy telepítési könyvtárára, ahol a `AzCopy.exe` végrehajtható fájl található. Ha szükséges, hozzáadhat az AzCopy telepítési hely a rendszer elérési útjához. Alapértelmezés szerint az AzCopy telepítve van a `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` vagy `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Az első AzCopy-parancs írása

Az AzCopy-parancsokat alapvető szintaxisa:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Az alábbi példák bemutatják, számos forgatókönyv esetében az adatok másolása a és a Microsoft Azure-Blobok, fájlok és táblák esetében. Tekintse meg a [AzCopy paraméterek](#azcopy-parameters) egyes mintában használt paraméterek részletes ismertetése a következő szakaszban.

## <a name="download-blobs-from-blob-storage"></a>Blobokat a Blob storage-ból

Lássunk erre többféle módon tölthet le blobokat az AzCopy használatával.

### <a name="download-a-single-blob"></a>Egy blob letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Vegye figyelembe, hogy ha a mappa `C:\myfolder` nem létezik, az AzCopy létrehozza, és a letöltési `abc.txt ` az új mappába.

### <a name="download-a-single-blob-from-the-secondary-region"></a>A másodlagos régióból egy blob letöltése

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Vegye figyelembe, hogy rendelkeznie kell olvasási hozzáférésű georedundáns tárolás engedélyezve van a másodlagos régióba eléréséhez.

### <a name="download-all-blobs-in-a-container"></a>A tárolóban lévő összes BLOB letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Tegyük fel, a következő BLOB található a megadott tároló:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

A letöltési művelet a címtár után `C:\myfolder` a következő fájlokat tartalmazza:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Ha nem adja meg a beállítás `/S`, nem BLOB letöltődnek.

### <a name="download-blobs-with-a-specific-prefix"></a>Egy adott előtaggal rendelkező blobok letöltése

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Tegyük fel, a megadott tárolóban található a következő blobokhoz. A előtaggal kezdődő összes BLOB `a` letöltött:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

A mappa a letöltési művelet után `C:\myfolder` a következő fájlokat tartalmazza:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Az előtag vonatkozik a virtuális könyvtárban, amely a blob nevének első részét képezi. A fenti példában a virtuális könyvtár nem egyezik a megadott előtag, így nem tölti le. Ezenkívül ha a beállítás `/S` nincs megadva, az AzCopy nem tölti le azokat a blobokat.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Állítsa be az exportált fájlokat ugyanaz, mint a forrás BLOB utolsó módosítás időpontja

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Emellett kizárhatók a blobok a letöltési művelet a legutóbbi módosításuk ideje alapján. Például, ha ki szeretné zárni a blobok, amelynek az utolsó módosítás időpontja, azonos vagy újabb, mint a célfájlt, adja hozzá a `/XN` lehetőséget:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Ha ki szeretné zárni a blobok, amelynek az utolsó módosítás időpontja, azonos vagy régebbi, mint a célfájlt, adja hozzá a `/XO` lehetőséget:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Blobok feltöltése a Blob storage

Tekintsük át az AzCopy segítségével BLOB feltöltése több módszert is.

### <a name="upload-a-single-blob"></a>Egy blob feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Ha a célként megadott tároló nem létezik, az AzCopy létrehozza, majd feltölti a fájlt a tárolóba.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Egy virtuális könyvtárat egy blob feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Ha a megadott virtuális könyvtár nem létezik, az AzCopy feltölti a fájlt a virtuális könyvtár neve (*például*, `vd/abc.txt` a fenti példában).

### <a name="upload-all-blobs-in-a-folder"></a>Egy mappa összes blobjának feltöltéséhez

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Beállítás megadása `/S` tölt fel a megadott könyvtár tartalmának a Blob storage rekurzív módon, ami azt jelenti, hogy minden almappa és a fájlok is feltöltődnek. Például tegyük fel, a következő fájlok mappában található `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

A feltöltési művelet után a tárolót a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Ha nem adja meg a beállítás `/S`, az AzCopy nem tölt fel rekurzív módon. A feltöltési művelet után a tárolót a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Egy adott minta megfelelő blobok feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Tegyük fel, a következő fájlok mappában található `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

A feltöltési művelet után a tárolót a következő fájlokat tartalmazza:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Ha nem adja meg a beállítás `/S`, az AzCopy csak feltölti a blobokat, amelyekre a virtuális könyvtár nem található:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Adja meg a cél blob a MIME-tartalom típusa

Alapértelmezés szerint az AzCopy beállítja a tartalom típusa a cél blob `application/octet-stream`. Verziójától kezdve 3.1.0, explicit módon megadhatja a tartalomtípus keresztül lehetőséget `/SetContentType:[content-type]`. Ez a szintaxis egy feltöltési művelet beállítja az összes tartalom típusa.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Ha megad `/SetContentType` egy érték nélkül AzCopy állítja be, minden egyes blob vagy fájl tartalom típusa szerint a fájl kiterjesztése.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Blob Storage-blobok másolása

Nézzük többféleképpen másolhat át blobokat egyik helyről a másikra, az AzCopy használatával.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Egy blob másolása több tároló között ugyanazon a tárfiókon belül 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Amikor egy blob egy tárfiókon belül másolja egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Egy blob másolása egyik tárfiókból a másikba

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Amikor egy blob Storage-fiókok között másolja egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>A másodlagos régióból egy blob átmásolása az elsődleges régió

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Vegye figyelembe, hogy rendelkeznie kell olvasási hozzáférésű georedundáns tárolás engedélyezve van a másodlagos tároló eléréséhez.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Másolja egy blobot, és annak pillanatképei egyik tárfiókból a másikba

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

A másolási művelet után a cél tároló tartalmazza, a blob és annak pillanatképei. Ha a fenti példában a blob két pillanatképekkel rendelkezik, a tároló tartalmazza a következő blob és a pillanatképekhez:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Tárolóban lévő összes BLOB másolása egy másik tárfiókba 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Beállítás megadása /S tölt fel a tartalmát a megadott tároló rekurzív módon. Lásd: [egy mappa összes blobjának feltöltéséhez](#upload-all-blobs-in-a-folder) további információt és példákat.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Szinkron módon másolja blobokat egyik tárfiókból a másikba

Alapértelmezés szerint az AzCopy adatokat másol a két tárolási végpontok közötti aszinkron módon történik. Ezért a másolási művelet fut a háttérben, amely nem tartozik SLA szempontjából hogyan gyors tartalékolt sávszélesség-kapacitást használatával másolja egy blobot, és AzCopy rendszeres időközönként ellenőrzi a másolási állapotát, amíg nem fejeződött be, vagy a másolás nem sikerült.

A `/SyncCopy` beállítással biztosíthatja, hogy a másolási művelet lekérdezi az egységes sebesség. Az AzCopy a szinkron másolatot a blobok másolása a megadott forrás helyi memória letöltésével, és feltölti őket a Blob storage cél hajt végre.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` További kimenő forgalmi költségek képest aszinkron példányt hozhat létre, az ajánlott módszer az, hogy ezt a beállítást használja az Azure virtuális gép, amely a forrás tárfiókban kimenő forgalmi költségek elkerülése érdekében ugyanabban a régióban.

## <a name="download-files-from-file-storage"></a>Fájlok letöltése a File storage-ból

Nézzük meg, töltse le a fájlokat az AzCopy segítségével többféle módon.

### <a name="download-a-single-file"></a>Egyetlen fájl letöltése

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Ha a megadott forrás egy Azure-fájlmegosztást, akkor kell adnia a fájl pontos nevét (*például* `abc.txt`) egyetlen fájl letöltéséhez, vagy adja meg a beállítás `/S` a megosztás rekurzív módon található összes fájl letöltéséhez. Adjon meg egy Fájlmintát és a beállítás próbál `/S` hiba együtt eredményez.

### <a name="download-all-files-in-a-directory"></a>Egy könyvtárban található összes fájl letöltése

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Vegye figyelembe, hogy a rendszer nem tölti le a mappa üres.

## <a name="upload-files-to-an-azure-file-share"></a>Fájlok feltöltése az Azure-fájlmegosztások

Fájlok feltöltése AzCopy használatával több módot is tekintsük át.

### <a name="upload-a-single-file"></a>Egyetlen fájl feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Egy mappa összes fájljának feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Vegye figyelembe, hogy üres mappák nem lesznek feltöltve.

### <a name="upload-files-matching-a-specific-pattern"></a>Egy adott minta egyező fájlok feltöltése

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Fájlok másolása a File storage-ban

Lássunk erre többféle módon másolja a fájlokat az Azure-fájlmegosztások az AzCopy használatával.

### <a name="copy-from-one-file-share-to-another"></a>Másolja egy fájlmegosztásról egy másikra

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor a fájlmegosztások, között másolhat egy fájlt egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>A Blob storage Azure-fájlmegosztás másolása

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor másolhat egy fájlt a blobba, fájlmegosztásról egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Blob másolása Blob storage-ból az Azure-fájlmegosztások

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Amikor másol egy fájlt egy blob egy fájlmegosztást, egy [kiszolgálóoldali másolási](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) műveletet hajtja végre.

### <a name="synchronously-copy-files"></a>Szinkron módon történik a fájlok másolása

Megadhatja a `/SyncCopy` másolhat adatokat a File Storage, File Storage, File Storage a Blob Storage és File Storage a Blob Storage-ból szinkron módon beállításra kattint, az AzCopy ezt teszi az adatok letöltése a helyi memória, és töltse fel újra cél. Standard szintű kimenő forgalmi költségek vonatkozik.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Amikor másol a File storage a Blob storage, az alapértelmezett blob típus-e a blokkblobok; a felhasználó megadhatja a beállítás `/BlobType:page` a blob cél típusának módosítása.

Vegye figyelembe, hogy `/SyncCopy` további adatkimeneti költségek képest aszinkron példányt hozhat létre. Az ajánlott módszer, hogy ez a beállítás az Azure virtuális Gépen, amely a forrás tárfiókban kimenő forgalmi költségek elkerülése érdekében ugyanabban a régióban található.

## <a name="export-data-from-table-storage"></a>Adatok exportálása a Table storage-ból

Vessünk egy pillantást az adatok exportálása az AzCopy használatával az Azure Table storage-ból.

### <a name="export-a-table"></a>Tábla exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

Az AzCopy ír a jegyzékfájlt a megadott célmappát. A jegyzékfájl keresse meg a szükséges adatok fájlokat, és végezze el az adatellenőrzésen. az importálási folyamat használatos. A jegyzékfájl alapértelmezés szerint használja az alábbi elnevezési szabályt követik:

    <account name>_<table name>_<timestamp>.manifest

Felhasználói is megadhat a beállítás `/Manifest:<manifest file name>` a jegyzékfájl nevének megadása.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Több fájl exportálását a Table storage-ból felosztása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

Használja az AzCopy egy *kötet index* különbséget tenni több fájlt osztott adatok fájl nevében szereplő. A kötet index két részből áll egy *kulcstartományhoz index particionálásához* és a egy *split fájl index*. Mindkét indexek nulláról induló tartoznak.

A partíciós kulcs tartományindexszel értéke 0, ha a felhasználó nem adja meg a beállítás `/PKRS`.

Tegyük fel például, az AzCopy két adatfájlok állít elő, miután a felhasználó adja meg a beállítás `/SplitSize`. Az eredményül kapott adatok fájlnevek lehetnek:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Vegye figyelembe, hogy a beállítás értékét a legkisebb lehetséges `/SplitSize` 32 MB lehet. Ha a megadott célpartícióra a Blob storage, az AzCopy bontja az adatfájl annak mérete eléri a blob mérete (200GB) után, függetlenül attól, hogy lehetőséget `/SplitSize` a felhasználó által lett megadva.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportál egy táblát a JSON vagy CSV fájl formátuma

Alapértelmezés szerint az AzCopy exportálja táblák JSON-adatfájljait. Megadhatja, hogy a beállítás `/PayloadFormat:JSON|CSV` JSON vagy CSV exportálhatja a táblákat.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

A fürt megosztott kötetei szolgáltatás adattartalom formátuma megadásakor AzCopy is állít elő kiterjesztéssel egy sémafájlt `.schema.csv` minden olyan adatfájl esetében.

### <a name="export-table-entities-concurrently"></a>Táblaentitások egyidejűleg exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

Az AzCopy elindítja az entitások exportálása, amikor a felhasználó adja meg a beállítás az egyidejű művelet `/PKRS`. Minden művelet egy partíciókulcs-tartományok exportálja.

Vegye figyelembe, hogy a párhuzamos műveletek számát is beállítás által vezérelt `/NC`. Az AzCopy a Processzormagok száma használja, mint az alapértelmezett érték `/NC` példatípust az táblaentitások, akkor is, ha `/NC` nincs megadva. Amikor a felhasználó adja meg a beállítás `/PKRS`, a két érték közül a kisebbiknek használja az AzCopy - partíció kulcstartományokkal és implicit és explicit módon megadott párhuzamos műveletek – a párhuzamos műveletek elindításához számának meghatározásához. További információkért írja be a `AzCopy /?:NC` a parancssorból.

### <a name="export-a-table-to-blob-storage"></a>Blob Storage-tábla exportálása

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

Az AzCopy egy JSON-adatok fájlt a blobtárolóba, a következő elnevezési konvenciót hoz létre:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

A létrehozott JSON-fájlt a következő szükséges metaadatokat a payload formátuma. Az adattartalom formátuma a részletekért lásd: [adattartalom formátuma a Table Service műveletek](https://msdn.microsoft.com/library/azure/dn535600.aspx).

Vegye figyelembe, hogy a csomag táblák, blobok exportálásakor az AzCopy a táblaentitások letölti a helyi ideiglenes fájlokat, és ezután feltölti ezeket az entitásokat is a blob. Ideiglenes adatfájlokat kerüljenek, a napló az alapértelmezett elérési utat a mappához, "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>", megadhatja, hogy a beállítás/a napló módosítása [napló-fájlok és mappák] Z: mappába fájlt, és így módosíthatja az ideiglenes adatokat fájlok helyét. Az ideiglenes adatokat fájlok mérete, amelyekről a tábla-entitások és a beállítás /SplitSize a megadott mérete bár ideiglenes adatokat törli a fájlt a helyi lemez azonnal után ezt töltenek fel blob: Ellenőrizze, hogy rendelkezik-e elég helyi Ezen adatok ideiglenes fájlok tárolására, törlés előtt lemezterület.

## <a name="import-data-into-table-storage"></a>Adatok importálása a Table storage-bA

Vessünk egy pillantást adatok importálása az AzCopy használatával az Azure Table storage-bA.

### <a name="import-a-table"></a>Tábla importálása

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

A beállítás `/EntityOperation` azt jelzi, hogy hogyan entitások beszúrni a táblába. Lehetséges értékek:

* `InsertOrSkip`: Egy létező entitásba kihagyja vagy szúr be egy új entitást, ha a tábla nem létezik.
* `InsertOrMerge`: Egy létező entitásba egyesíti, vagy szúr be egy új entitást, ha a tábla nem létezik.
* `InsertOrReplace`: Váltja fel egy meglévő entitásra, vagy szúr be egy új entitást, ha a tábla nem létezik.

Vegye figyelembe, hogy a beállítás nem adható meg `/PKRS` importálás forgatókönyvben. Az exportálás, amelyben a kapcsolót kell megadnia a forgatókönyvben eltérően `/PKRS` párhuzamos műveletek indításához az AzCopy az egyidejű művelet alapértelmezés szerint elindul egy tábla importálásakor. A lépések az egyidejű művelet alapértelmezett száma egyenlő; a Processzormagok száma azonban különböző számú párhuzamos lehetőséggel megadhatja `/NC`. További információkért írja be a `AzCopy /?:NC` a parancssorból.

Vegye figyelembe, hogy az AzCopy csak támogatja a JSON-t, nem CSV importálása. Az AzCopy nem támogatja a felhasználó által létrehozott JSON-ból tábla importálja és manifest fájlt. Az AzCopy tábla export mindkét fájlt kell származnia. Hibák elkerülése érdekében ne módosítsa az exportált JSON vagy jegyzékfájl.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Entitások importálása egy táblába a Blob storage-ból

Tegyük fel, a Blob-tároló tartalmazza a következő: egy JSON-fájlt, amely egy Azure-tábla és a hozzájuk tartozó Alkalmazásjegyzék-fájl.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Entitások importálása egy táblába a blob-tárolóban az Alkalmazásjegyzék-fájl a következő parancsot futtathatja:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Más AzCopy-funkciók

Vessünk egy pillantást néhány egyéb AzCopy funkció.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Csak másolja az adatokat, amely nem létezik a célhelyen

A `/XO` és `/XN` paraméterek régebbi vagy újabb forrás erőforrások kizárása való másolását, illetve lehetővé teszik. Ha azt szeretné, hogy nem létezik a célhelyen forrás erőforrások másolása csak, mindkét paraméter is megadhat az AzCopy-parancsban:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Vegye figyelembe, hogy ez nem támogatott, ha a cél- és a egy táblát.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Egy válaszfájl használatával adja meg a parancssori paraméterek

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Az AzCopy parancssori paramétereket is megadhat a egy válaszfájlt. AzCopy dolgozza fel a paramétereket a fájlban, ha a parancssorban megadott lett a fájl tartalmával közvetlen helyettesítés végrehajtása.

Tegyük fel, egy válasz fájlt `copyoperation.txt`, a következő sorokat tartalmazó. Minden egyes AzCopy paraméter adható meg ugyanabban a sorban

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

vagy külön sorokat:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

Az AzCopy sikertelen lesz, ha a paraméter két sort elosztja az itt látható módon a `/sourcekey` paramétert:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Adja meg a parancssori paraméterek több válasz fájl használatával

Tegyük fel, egy válasz fájlt `source.txt` , amely meghatározza, hogy a forrás-tároló:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

És a egy válasz fájlt `dest.txt` egy célmappát, amely meghatározza a fájlrendszer:

    /Dest:C:\myfolder

És a egy válasz fájlt `options.txt` , amely meghatározza, hogy az AzCopy lehetőségei:

    /S /Y

Szeretne hívásokat indítani az AzCopy a válasz fájlok, amelyek mindegyike egy könyvtár találhatók `C:\responsefiles`, használja a következő parancsot:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

Az AzCopy végrehajtja ezt a parancsot, ugyanúgy, mintha minden a parancssorban az egyes paramétereket tartalmazza:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Adja meg a közös hozzáférésű jogosultságkód (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

A tároló URI-t is megadhat egy SAS:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Napló fájlmappa

Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi egy journal-fájlt az alapértelmezett mappában megtalálható-e, vagy hogy megtalálható-e ezt a beállítást keresztül megadott mappába. A naplófájl nem létezik az egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl létezik, AzCopy ellenőrzi, hogy adjon meg parancssori megegyezik-e a naplófájl parancssorában. Ha két parancssorok egyezik, az AzCopy folytatja a művelet nem fejeződött be. Ha nem egyeznek, vagy felülírja az új művelet indítása és az aktuális művelet visszavonása a naplófájl kéri.

Ha szeretné használni a naplófájl alapértelmezett helye:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Ha a beállítás nincs megadva `/Z`, vagy adja meg a beállítás `/Z` anélkül, hogy a mappa elérési útja, ahogyan fentebb, az AzCopy hoz létre a naplófájl az alapértelmezett helyen, amely `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Ha a napló fájl már létezik, az AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha szeretne egy egyéni napló helyét adja meg:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Ebben a példában a naplófájl hoz létre, ha azt nem létezik. Ha létezik, az AzCopy folytatja a műveletet, a napló-fájl alapján.

Ha azt szeretné, az AzCopy művelet folytatásához:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Ebben a példában folytatja az utolsó művelet, amely nem sikerült befejezni.

### <a name="generate-a-log-file"></a>A naplófájl létrehozása

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Ha a beállítás megad `/V` anélkül, hogy egy fájl elérési útját a részletes naplóba, majd AzCopy hoz létre a naplófájl alapértelmezett helye, amely `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Ellenkező esetben egy naplófájlt is létrehozhat egy egyéni helyre:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Vegye figyelembe, hogy ha a beállítás a következő relatív elérési utat ad meg `/V`, mint például `/V:test/azcopy1.log`, akkor a részletes napló jön létre egy almappát belül az aktuális munkakönyvtárban `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Adja meg, indítsa el a párhuzamos műveletek száma

A beállítás `/NC` egyidejű másolási műveletek számát adja meg. Alapértelmezés szerint az AzCopy egy bizonyos számú párhuzamos műveletek az adatok átvitele az átviteli sebesség növelése indul. A tábla műveletek a párhuzamos műveletek száma egyenlő rendelkezik processzorok száma. A Blobok és fájlok műveleteket, a párhuzamos műveletek száma egyenlő rendelkezik processzorok száma 8-szorosának. Ha az AzCopy egy kis sávszélességű hálózaton keresztül futtat, kevesebb /NC erőforrás verseny által okozott hiba elkerülése érdekében a is megadhat.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Az AzCopy futtatása az Azure Storage emulatorban

Futtatható AzCopy ellen a [Azure Storage Emulator](storage-use-emulator.md) blobok esetében:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Is futtathatja, a táblák:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

### <a name="automatically-determine-content-type-of-a-blob"></a>Automatikusan meghatározni a Blob tartalmának típusa

Az AzCopy egy blob egy JSON-fájlt, amely tárolja a tartalomtípus kiterjesztés fájlleképezés alapján tartalom típusa határozza meg. A JSON-fájl neve AzCopyConfig.json, és az AzCopy könyvtárban található. Ha egy fájl-típust, amely nem szerepel a listában a leképezés fűzze hozzá a JSON-fájlba:

```
{
  "MIMETypeMapping": {
    ".myext": "text/mycustomtype",
    .
    .
  }
}
```     

## <a name="azcopy-parameters"></a>Az AzCopy-paraméterek

Paramétereket az AzCopy az alábbiakban tekintheti át. Is beírhatja a parancssorból segítséget a következő parancsok egyikét az AzCopy használatával:

* Az AzCopy a részletes parancssori súgó: `AzCopy /?`
* További információt az AzCopy paramétert: `AzCopy /?:SourceKey`
* A parancssori példák: `AzCopy /?:Sample`

### <a name="sourcesource"></a>/ Source: "forrás"

Megadja a forrásadatokat, ahonnan másolja. A forrás lehet fájl rendszer könyvtár, egy blob-tároló, blob virtuális könyvtár, egy tárolói fájlmegosztást, -tárolási fájl könyvtára vagy az Azure-táblát.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="destdestination"></a>/ Cél: "cél"

Itt adhatja meg a célhelyre másolni. A cél lehet fájl rendszer könyvtár, egy blob-tároló, blob virtuális könyvtár, egy tárolói fájlmegosztást, -tárolási fájl könyvtára vagy az Azure-táblát.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="patternfile-pattern"></a>/ Mintát: "fájlminta"

Itt adhatja meg, amely jelzi, hogy mely fájlok másolása egy Fájlmintát. /Pattern paraméter viselkedését határozza meg a helyet, a forrásadatok és a rekurzív mód beállítás jelenlétét. Rekurzív mód keresztül /s beállítás van megadva.

Ha a megadott forrás egy könyvtárat a fájlrendszerben, majd szabványos helyettesítő érvényben vannak, és a megadott fájl minta egyezik a könyvtárban lévő fájlokra vonatkozóan. Ha lehetőség /S meg van adva, majd az AzCopy is megegyezik a megadott minta almappákban alatt található a címtárban található összes fájl ellen.

Ha a megadott forrás blob-tároló vagy virtuális könyvtárat, majd a helyettesítő karakterek nem érvényesek. Ha a beállítás /S meg van adva, majd az AzCopy egy blob előtagként a megadott fájl minta értelmezi. Ha a beállítás /S nincs megadva, majd az AzCopy pontos blob-nevek alapján a fájl minta illeszkedik.

Ha a megadott forrás egy Azure-fájlmegosztást, akkor kell vagy adja meg a pontos nevére (pl. abc.txt) egyetlen fájl másolása, vagy adja meg a beállítás az összes fájl másolása a fájlmegosztás rekurzív módon /S. Adja meg mindkét fájl minta és a beállítás /S együtt eredmény hiba történt kísérlet.

Az AzCopy használja a kis-és nagybetűket megfelelő, a/Source egy blob-tárolóba vagy a blob virtuális könyvtárat, és használja, minden más esetben nem megfelelő.

Az alapértelmezett fájl mintázat használatos, ha nincs Fájlmintát megadott *.* a rendszer helye vagy egy üres előtag egy Azure Storage-helyhez. Több fájlminták megadása nem támogatott.

**Alkalmazható:** Blobok, fájlok

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Itt adhatja meg a tárfiók-kulcsot a cél erőforrás.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Adja meg a közös hozzáférésű Jogosultságkód (SAS) az OLVASÁSI és írási engedélyekkel a cél (ha van). Tegyük az idézőjelekkel együtt a SAS, mert előfordulhat, hogy parancssori speciális karaktereket tartalmaz.

Ha a cél erőforrás egy blob-tárolóba, fájlmegosztás vagy táblázat, megadhatja ezt a beállítást a SAS-jogkivonat követ, vagy megadhatja a SAS blob céltárolóját, fájlmegosztás vagy a táblázat URI-t, anélkül, hogy ez a beállítás részeként.

Ha a forrás- és mindkét blobokat, majd a forrásblobot ugyanazt a tárfiókot, a forrás blob belül kell lennie.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Itt adhatja meg a tárfiók-kulcsot a forrás-erőforrás.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Adja meg a közös hozzáférésű Jogosultságkód OLVASÁSI és a lista engedélyekkel a forrás (ha van). Tegyük az idézőjelekkel együtt a SAS, mert előfordulhat, hogy parancssori speciális karaktereket tartalmaz.

Ha az adatforrás erőforrás egy blob-tárolóba, és a egy kulcsot és egy SAS nem áll rendelkezésre, majd a blob-tároló elolvasva névtelen hozzáférést.

Ha a forrás, fájlmegosztás vagy tábla, egy kulccsal vagy SAS meg kell adni.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="s"></a>/S

Meghatározza a másolási műveletek rekurzív módját. Rekurzív módban AzCopy másolja át a blobokat, vagy a fájlokat, amelyek megfelelnek a megadott fájl minta, almappák lévőket is beleértve.

**Alkalmazható:** Blobok, fájlok

### <a name="blobtypeblock--page--append"></a>/ BlobType: "letiltás" |} "lap" |} "hozzáfűzése"

Megadja, hogy a cél blob blokkblob, lapblob és hozzáfűző blob. Ez a beállítás csak egy blobot tölt fel esetén alkalmazható. Ellenkező esetben hiba jön létre. Ha a cél egy blobot, és ez a beállítás nincs megadva, alapértelmezés szerint az AzCopy egy blokkblobot hoz létre.

**Alkalmazható:** Blobok

### <a name="checkmd5"></a>/ CheckMD5

Egy MD5-kivonat, a letöltött adatok kiszámolja, és ellenőrzi, hogy az MD5-kivonat a blobban tárolt vagy a fájl tartalma-MD5 tulajdonsága egyezést mutat a kiszámított kivonatát. Az MD5-ellenőrzést ki van kapcsolva, alapértelmezés szerint meg kell adnia ezt a beállítást, elvégzi a MD5-ellenőrzést, amikor adatokat tölti le, így.

Vegye figyelembe, hogy az Azure Storage nem garantálja, hogy naprakész állapotban-e tárolni a blob- vagy MD5-kivonat. Feladata ügyfél frissítéséhez az MD5-tel, a blob- vagy módosításakor.

AzCopy után feltölteni a a szolgáltatás mindig egy Azure blob- vagy a tartalom-MD5 tulajdonságát állítja be.  

**Alkalmazható:** Blobok, fájlok

### <a name="snapshot"></a>/Snapshot

Azt jelzi, hogy pillanatképeket átviteléhez. Ez a beállítás csak akkor érvényes, ha a forrás, egy blobot.

Az átvitt blobpillanatképeket átnevezi a következő formátumban: .extension blob-name (pillanatkép-time)

Alapértelmezés szerint a pillanatképek nem másolódnak.

**Alkalmazható:** Blobok

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Üzeneteket jelenít meg részletes állapota egy naplófájlba.

Alapértelmezés szerint a részletes naplófájl neve a AzCopyVerbose.log `%LocalAppData%\Microsoft\Azure\AzCopy`. Ha megadja ezt a lehetőséget egy meglévő fájl helyét, a rendszer hozzáfűzi a részletes napló a fájlt.  

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

A művelet folytatása napló fájl mappáját adja meg.

Az AzCopy mindig támogatja folytatását, ha a művelet megszakadt.

Ha ez a beállítás nincs megadva, vagy egy mappa elérési útja nélkül van megadva, majd az AzCopy az alapértelmezett hely, amely a(z) % LocalAppData%\Microsoft\Azure\AzCopy a napló fájlt hoz létre.

Minden alkalommal, amikor egy parancs kiadni az AzCopy, ellenőrzi egy journal-fájlt az alapértelmezett mappában megtalálható-e, vagy hogy megtalálható-e ezt a beállítást keresztül megadott mappába. A naplófájl nem létezik az egyik helyen sem, ha az AzCopy kezeli a művelet új, és létrehoz egy új naplófájl.

Ha a napló fájl létezik, AzCopy ellenőrzi, hogy adjon meg parancssori megegyezik-e a naplófájl parancssorában. Ha két parancssorok egyezik, az AzCopy folytatja a művelet nem fejeződött be. Ha nem egyeznek, vagy felülírja az új művelet indítása és az aktuális művelet visszavonása a naplófájl kéri.

A naplófájl a művelet sikeres befejezését követően törlődik.

Vegye figyelembe, hogy egy korábbi, az AzCopy által létrehozott napló-fájlból egy művelet folytatása nem támogatott.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="parameter-file"></a>/@:"Parameter-File"

Megadja a paramétereket tartalmazó fájlt. Az AzCopy dolgozza fel a paramétereket a fájlban, ugyanúgy, mintha a parancssorban megadott lett.

Egy válasz fájlban is ugyanabban a sorban adja meg a több paraméter, vagy adja meg az egyes paramétereket külön sorban tüntettük. Vegye figyelembe, hogy egy adott paraméter nem ívelhet át több sort.

Válasz tartalmazhatnak a # szimbólum kezdődő megjegyzések sorokat.

Megadhatja, hogy több választ fájlt. Vegye azonban figyelembe, hogy az AzCopy nem támogatja a beágyazott válasz fájlokat.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="y"></a>/Y

Letiltja az összes AzCopy megerősítési kérések. Ezt a lehetőséget is lehetővé teszi az adatok feltöltése forgatókönyvek esetén csak írási SAS-tokeneket használatát, ha nincs megadva a /XO és /XN.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="l"></a>/ L

Adja meg a listázási művelet csak; adatot nem másolódik.

Az AzCopy értelmezi használja ezt a beállítást, mint a szimuláció futtatása az e nélkül a parancssor/l és az eseményszámok objektumok kerülnek beállításra kattint, /V egy időben, ellenőrizze, hogy mely objektumok másolása a részletes napló az lehetőséget is megadhat.

Ez a beállítás viselkedését is határozza meg a helyet, a forrásadatok és a rekurzív mód beállítás /S és a fájl a minta lehetőség /Pattern jelenlétét.

Az AzCopy a forrás helye a lista, és OLVASÁSI engedélyre van szüksége, ezen lehetőség használatakor.

**Alkalmazható:** Blobok, fájlok

### <a name="mt"></a>/MT

Beállítja a letöltött fájl utolsó módosításának ideje azonos legyen a forrás blob vagy fájl.

**Alkalmazható:** Blobok, fájlok

### <a name="xn"></a>/XN

Újabb adatforrás erőforrás nem tartalmazza. Az erőforrás nem másolódik, ha a forrás utolsó módosítási időpontjának azonos vagy újabb, mint a cél.

**Alkalmazható:** Blobok, fájlok

### <a name="xo"></a>/XO
Nem tartalmazza egy régebbi forrás-erőforrást. Az erőforrás nem másolódik, ha a forrás utolsó módosítási időpontjának azonos vagy régebbi, mint a cél.

**Alkalmazható:** Blobok, fájlok

### <a name="a"></a>/A

Csak a archiválandó fájlokat tölt fel.

**Alkalmazható:** Blobok, fájlok

### <a name="iarashcnetoi"></a>/ IA: [RASHCNETOI]

Csak a megadott attribútumok beállítása a fájlokat tölt fel.

Elérhető attribútumok a következők:

* Az R = csak olvasható fájlokat
* A = archiválásra kész
* S = rendszerfájlok
* H = Rejtett fájlok
* C = tömörített fájlok
* N = normál fájlok
* E titkosított fájlok =
* T ideiglenes fájlok =
* O = Offline fájlok
* E fájlok nem indexelt =

**Alkalmazható:** Blobok, fájlok

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Kizárja a fájlokat, amelyek a megadott attribútumok csoport.

Elérhető attribútumok a következők:

* Az R = csak olvasható fájlokat
* A = archiválásra kész
* S = rendszerfájlok
* H = Rejtett fájlok
* C = tömörített fájlok
* N = normál fájlok
* E titkosított fájlok =
* T ideiglenes fájlok =
* O = Offline fájlok
* E fájlok nem indexelt =

**Alkalmazható:** Blobok, fájlok

### <a name="delimiterdelimiter"></a>/ Elválasztó karakter: "elválasztót"

Azt jelzi, hogy az elválasztó karakter, a blob nevében virtuális könyvtárak elválasztására használható.

Az AzCopy használ alapértelmezés szerint / elválasztó karaktert. Azonban az AzCopy támogatja a közös karakter (például a @, #, vagy %) elválasztóként. Ha kell egyet a parancssorban a következő speciális karaktereket, tegye a fájl nevét, az idézőjelekkel együtt.

Ez a beállítás csak akkor alkalmazható blobok letöltéséhez.

**Alkalmazható:** Blobok

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Megadja a párhuzamos műveletek számát.

Alapértelmezés szerint az AzCopy elindítja egy bizonyos számú párhuzamos műveletek az adatok átvitel átviteli sebesség növelése érdekében. Vegye figyelembe, hogy nagy számú kis sávszélességű környezetben egyidejű műveletet túlterhelhetik futó a hálózati kapcsolatot, és megakadályozza a műveletek teljesen befejeződését. Szabályozás egyidejű művelet tényleges rendelkezésre álló hálózati sávszélesség alapján.

A párhuzamos műveletek felső határa 512.

**Alkalmazható:** Blobok, fájlok, táblák

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Megadja, hogy a `source` erőforrás egy blobot a helyi fejlesztési környezetben, a storage emulator futó érhető el.

**Alkalmazható:** Blobok, táblák

### <a name="desttypeblob--table"></a>/ DestType: "Blob" |} "Table"

Megadja, hogy a `destination` erőforrás egy blobot a helyi fejlesztési környezetben, a storage emulator futó érhető el.

**Alkalmazható:** Blobok, táblák

### <a name="pkrskey1key2key3"></a>/ PKRS: "1. kulcs #key2 key3 #. …"

Felosztja a partíciókulcs-tartományok történő párhuzamos, ami növeli az exportálási művelet sebességének tábla adatexportálás engedélyezéséhez.

Ha ez a beállítás nincs megadva, majd az AzCopy segítségével egyetlen szálból táblaentitások exportálása. Például, ha a felhasználó /PKRS adja meg: "aa #bb", majd az AzCopy elindul mindhárom egyidejű művelet.

Minden művelet exportálja egy három partíció kulcstartományokkal, ahogy az alábbi:

  [első partíciókulcsot, aa)

  [aa, bb)

  [bb partíciós kulccsal]

**Alkalmazható:** táblák

### <a name="splitsizefile-size"></a>/ SplitSize: "fájlméret"

Itt adhatja meg az exportált fájlt felosztás mérete (MB), az engedélyezett minimális értéknél 32.

Ha ez a beállítás nincs megadva, az AzCopy táblaadatok egyetlen fájlba exportálja.

Ha a tábla adatok exportálva lettek egy blobot, és az exportált fájl mérete eléri a 200 GB-os korlátot, a blob mérete, majd az AzCopy bontja az exportált fájlt, akkor is, ha ez a beállítás nincs megadva.

**Alkalmazható:** táblák

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/ EntityOperation: "InsertOrSkip" |} "InsertOrMerge" |} "InsertOrReplace"

Itt adhatja meg a tábla importálása működéshez.

* InsertOrSkip – kihagyja a egy létező entitásba, vagy szúr be egy új entitást, ha a tábla nem létezik.
* InsertOrMerge - egyesítése egy létező entitásba, vagy szúr be egy új entitást, ha a tábla nem létezik.
* InsertOrReplace - váltja fel egy meglévő entitásra, vagy szúr be egy új entitást, ha a tábla nem létezik.

**Alkalmazható:** táblák

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

A jegyzékfájl megadja a tábla exportálása és az importálási művelet.

Ez a beállítás akkor választható, az exportálási művelet közben, az AzCopy egy előre meghatározott nevű jegyzékfájl állít elő, ha ez a beállítás nincs megadva.

Az adatfájlok kereséséhez az importálási művelet során ezt a beállítást kötelező megadni.

**Alkalmazható:** táblák

### <a name="synccopy"></a>/SyncCopy

Azt jelzi, hogy szinkron módon történik a blobok és a két Azure Storage-végpontok közötti fájlok másolása.

Alapértelmezés szerint az AzCopy kiszolgálóoldali aszinkron példányt használ. Adja meg ezt a beállítást, hajtsa végre a szinkron másolatot, amely letölti a blobokat vagy fájlok helyi memória, és ezután feltölti őket az Azure Storage.

Ezt a lehetőséget is használhatja, ha a Blob Storage, File storage belül, vagy fordítva vagy a File Storage a Blob storage-ból a fájlok másolása.

**Alkalmazható:** Blobok, fájlok

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Adja meg a cél blobok és fájlok MIME-tartalom típusa.

Az AzCopy állítja be a tartalom típusa egy blob- vagy az application/octet-stream alapértelmezés szerint. Beállíthatja a tartalomtípus minden blobok vagy fájlok kifejezetten ehhez a beállításhoz tartozó érték megadásával.

Ha érték nélkül ez a beállítás adja meg, majd az AzCopy beállítja minden egyes blob vagy fájl tartalom típusa szerint a fájl kiterjesztése.

**Alkalmazható:** Blobok, fájlok

### <a name="payloadformatjson--csv"></a>/ PayloadFormat: "JSON" |} FÜRT MEGOSZTOTT KÖTETEI "SZOLGÁLTATÁS"

Itt adhatja meg a táblázat az exportált adatok fájl formátumát.

Ha ez a beállítás nincs megadva, alapértelmezés szerint az AzCopy exportálja tábla adatok fájlját JSON formátumban.

**Alkalmazható:** táblák

## <a name="known-issues-and-best-practices"></a>Ismert problémák és ajánlott eljárások

Vessünk egy pillantást, néhány ismert problémák és ajánlott eljárások.

### <a name="limit-concurrent-writes-while-copying-data"></a>Adatok másolása közben egyidejű írások korlátozása

Blobok vagy az Azcopyval fájlok másolása esetén vegye figyelembe, hogy egy másik alkalmazás módosítja az adatokat másolja, amíg. Ha lehetséges győződjön meg arról, hogy az adatok másolása nem áll módosítás alatt a másolási művelet során. Például ha egy Azure virtuális géphez társított virtuális Merevlemezét másolja, győződjön meg arról, hogy más alkalmazások nem a virtuális merevlemez jelenleg írása. Egy jó módja a naplóbérbeadási kell másolni az erőforrás. Azt is megteheti először hozzon létre egy pillanatképet a VHD-t, és másolja a pillanatkép.

Ha más alkalmazásokat a blobok és fájlok írása, amíg folyamatban van a másolás nem akadályozhatja meg, majd vegye figyelembe, hogy a ideje, a feladat befejeződik, az átmásolt erőforrások előfordulhat, hogy többé nem kell a forrás-erőforrások teljes paritás.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>AzCopy MD5 FIPS előírásainak megfelelő algoritmusok engedélyezése során, a "használata a FIPS előírásainak megfelelő algoritmusok a titkosításhoz, kivonatoláshoz és aláíráshoz."

Alapértelmezés szerint az AzCopy .NET MD5 végrehajtása használja az MD5 kiszámításához objektumok másolásakor, de néhány biztonsági követelmények, amely a FIPS előírásainak megfelelő MD5 beállításnak az engedélyezéséhez AzCopy kell.

Létrehozhat egy app.config fájl `AzCopy.exe.config` tulajdonsággal rendelkező `AzureStorageUseV1MD5` és az AzCopy.exe feltöltési.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

For property "AzureStorageUseV1MD5":

* Az alapértelmezett érték, az AzCopy igaz - használja a .NET MD5 végrehajtására.
* Hamis – AzCopy a FIPS előírásainak megfelelő MD5 algoritmust használja.

A Windows alapértelmezés szerint a FIPS előírásainak megfelelő algoritmusok le vannak tiltva. Ezzel a szabályzatbeállítással módosíthatja a gépen. A Futtatás ablakba (Windows + R), írja be a secpol.msc megnyitásához a **helyi biztonsági házirend** ablak. Az a **biztonsági beállítások** ablakában navigáljon a **biztonsági beállítások** > **helyi házirendek** > **vonatkozóbiztonságibeállítások**. Keresse meg a **rendszer-kriptográfia: a titkosításhoz, kivonatoláshoz és aláíráshoz használható FIPS előírásainak megfelelő algoritmusok** házirend. Kattintson duplán az érték jelenik meg a szabályzatot a **biztonsági beállítás** oszlop.

## <a name="next-steps"></a>További lépések

További, az Azure Storage szolgáltatással és az AzCopyval kapcsolatos adatokat a következő erőforrások nyújtanak:

### <a name="azure-storage-documentation"></a>Az Azure Storage-dokumentáció:
* [A Microsoft Azure Storage bemutatása](../storage-introduction.md)
* [A Blob storage a .NET használatával](../blobs/storage-dotnet-how-to-use-blobs.md)
* [File storage a .NET használatával](../storage-dotnet-how-to-use-files.md)
* [A Table storage a .NET használatával](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Hogyan létrehozása, kezelése vagy törlése](../storage-create-storage-account.md)
* [Adatok áthelyezése az AzCopyval Linux rendszeren](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Az Azure Storage-blogbejegyzések:
* [Introducing Azure Storage adatátviteli könyvtár Adatelőnézet](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [Az AzCopy: Bemutatkozik a szinkron másolása és testre szabott tartalom típusa](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [Az AzCopy: Általános rendelkezésre állás az AzCopy 3.0 és az előzetes kiadásban az AzCopy 4.0 támogatásával, tábla és fájl bejelentése](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Nagyméretű példány esetekre optimalizált](https://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Írásvédett georedundáns tárolás támogatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [Az AzCopy: Adatátvitel újraindítható üzemmódban és SAS-jogkivonat](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [Az AzCopy: Kereszt-fiók másolás Blob használatával](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [Az AzCopy: Azure-Blobok feltöltése/letöltése fájlok](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
