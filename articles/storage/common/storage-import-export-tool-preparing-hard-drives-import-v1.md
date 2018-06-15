---
title: Merevlemez-meghajtók előkészítése az Azure Import/Export importálási feladat - 1-es verzió |} Microsoft Docs
description: Ismerje meg, hogyan készíti elő az Azure Import/Export szolgáltatás importálási feladat létrehozása a WAImportExport v1 eszközzel merevlemez-meghajtókat.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874093"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Merevlemezek előkészítése importálási feladatokhoz
Az importálási feladat egy vagy több merevlemezek előkészítéséhez kövesse az alábbi lépéseket:

-   A Blob szolgáltatás az importálni kívánt adatok azonosítása

-   Azonosítsa a cél virtuális könyvtárak és blobot, amely a Blob szolgáltatás

-   Határozza meg, hány meghajtót kell

-   Másolja az adatokat az egyes a merevlemez-meghajtók

 Lásd: a minta-munkafolyamat [előkészítése merevlemezek egy importálási feladatnak a minta-munkafolyamat](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Az adatok, importálandók azonosítása
 Az importálási feladat létrehozásának első lépése, hogy határozzák meg, mely könyvtárak és fájlok importálni kívánja. Ez lehet könyvtárak listája, egyedi fájlok listáját, vagy ezeket a kettő kombinációja. Része egy könyvtárat, ha minden fájl a könyvtárban és annak alkönyvtáraiban lesz az importálási feladat részeként.

> [!NOTE]
>  Mivel alkönyvtárak belefoglalt rekurzív módon ha egy szülőkönyvtárában megtalálható, adja meg, csak a szülőkönyvtárat. Nem is megadhatja alkönyvtáraiban.
>
>  Jelenleg a Microsoft Azure Import/Export eszköz a következő korlátozás van érvényben: Ha egy könyvtárat a merevlemez-meghajtóról tartalmazhat több adatot tartalmaz, majd a könyvtárban kell kisebb könyvtárak lehet osztani. Például ha egy könyvtárat 2,5 TB adatot tartalmaz, és a merevlemez-területtel csak 2TB, majd szeretné a 2,5 TB directory felosztása kisebb könyvtárak. Ez a korlátozás kiiktatása a későbbi verzióját az eszköz.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>A kívánt rendeltetési helyeket, a blob szolgáltatás azonosítása
 Minden könyvtár vagy fájl importálni kívánt kell azonosítani a cél virtuális könyvtárat vagy a blob az Azure Blob szolgáltatásban. Az Azure Import/Export eszköz bemeneteként ezeken a célokon fogja használni. Vegye figyelembe, hogy a könyvtárak a perjel karakterrel kell tagolva "/".

 A következő táblázatban néhány példa a blob-tárolók:

|Forrás-fájl vagy könyvtár|Cél blob vagy virtuális könyvtár|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|

## <a name="determine-how-many-drives-are-needed"></a>Határozza meg, hány meghajtót van szükség
 Ezután meg kell határoznia:

-   Az adatok tárolásához szükséges a merevlemez-meghajtók száma.

-   A könyvtárak és/vagy az önálló fájlok, amelyet a program az egyes a merevlemez-meghajtón.

 Győződjön meg arról, hogy rendelkezik-e szüksége lesz a viszi adatokat tároló a merevlemez-meghajtók száma.

## <a name="copy-data-to-your-hard-drive"></a>A merevlemez-meghajtóról adatok másolása
 Ez a szakasz ismerteti, hogyan hívhatja meg az Azure Import/Export eszköz másolja az adatokat legalább egy merevlemez-meghajtókat. Minden alkalommal, amikor az Azure Import/Export eszköz hívja, hozzon létre egy új *munkamenet másolása*. Minden meghajtó, amelyre a Másolás; legalább egy példány munkamenet létrehozása egyes esetekben szükség lehet másolni az összes adat egyetlen meghajtóval egynél több példány munkamenet. Az alábbiakban néhány ok, amiért az, hogy szükség lehet a több példány munkamenetet:

-   Létre kell hoznia egy külön példányát munkamenet minden meghajtó, amelyet másol.

-   A másolási munkamenet másolhatja vagy egy könyvtár, vagy egyetlen blob a meghajtó. Ha több címtárral, több blobok vagy mindkettőt másolni, szüksége több példány-munkameneteket hozzon létre.

-   Megadhatja a tulajdonságok és a metaadatokat, amelyek a blobok importálása az importálási feladat részeként állítható be. A Tulajdonságok vagy a metaadatokat, amelyek másolási munkamenet megadása, hogy másolatot munkamenet által meghatározott összes BLOB fog vonatkozni. Ha meg szeretné határozni a más tulajdonságokkal vagy néhány BLOB metaadatai, szüksége lesz egy külön példányát a munkamenet létrehozásához. Lásd: [a tulajdonságok és az importálási folyamat során metaadatok](storage-import-export-tool-setting-properties-metadata-import-v1.md)további információt.

> [!NOTE]
>  Ha rendelkezik-e több gép leírt követelményeknek megfelelő [beállítás mentése az Azure Import/Export eszköz](storage-import-export-tool-setup-v1.md), párhuzamosan több merevlemez adatok a gépén való futtatásával az eszköz egy példányát minden egyes másolhatja.

 Az egyes merevlemez-meghajtóról, amely előkészítheti az Azure Import/Export eszközzel az eszköz létrehoz egy egyetlen napló fájlt. Az összes, a meghajtók, az importálási feladat létrehozásához szüksége lesz az adatbázisnapló-fájlok. A naplófájl meghajtó előkészítése folytatni, ha az eszköz megszakad is használható.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Hogy az importálás az Azure Import/Export eszköz szintaxisa
 Meghajtók előkészítése az importálási feladat, hívja meg az Azure Import/Export eszközt a **PrepImport** parancsot. Mely paraméterek is függ, hogy ez-e az első másolás munkamenet, vagy egy későbbi másolási munkamenet.

 Az első másolás munkamenet meghajtó használatához néhány további paraméter adja meg a tárfiók hívóbetűjét; a célként megadott meghajtóbetűjel; hogy a meghajtó formázva; hogy a meghajtó titkosítva kell lennie, és ha igen, a BitLocker-kulcsot. és a naplózási könyvtár. Íme egy kezdeti másolatot munkamenetet a következőre a könyvtárat vagy akár egyetlen fájlt másolja szintaxisa:

 **Először másolja a munkamenet csak egy könyvtárból másolása**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Először másolja az munkamenet egyetlen fájl másolása**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 A következő másolási-munkamenetekben nem kell megadnia a kezdeti paramétereket. Íme egy könyvtárat vagy akár egyetlen fájlt másolja ezt követő másolási munkamenetet szintaxisa:

 **További másolási munkamenetek egyetlen könyvtár másolása**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **További másolási munkamenetek egyetlen fájl másolása**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Az első másolás munkamenet a merevlemez-meghajtóról paraméterek
 Minden alkalommal, amikor az Azure Import/Export eszköz fájlok másolása a merevlemez-meghajtóról, az eszköz létrehoz egy másolatot munkamenet. Minden példány munkamenet merevlemez-meghajtóra másolja át egyetlen könyvtárat vagy akár egyetlen fájlt. A másolási munkamenet állapota a napló fájl írása. Ha a Másolás munkamenet megszakad (például mert a rendszer az áramellátás megszakadása miatt), akkor az eszköz újra és megadhatja a napló fájlt a parancssorból folytathatók.

> [!WARNING]
>  Ha megadja a **/formázása** az első másolás munkamenet paraméter, a meghajtó formázva lesz, és a meghajtón lévő összes adat törlődik. Javasoljuk, használjon üres meghajtók csak a Másolás munkamenet.

 Az első másolás munkamenet minden meghajtó parancs különböző paraméterei nem felelnek meg a parancsok további másolási munkamenetek igényel. Az alábbi táblázat a további paraméterek, amelyek az első másolás munkamenet érhetők el:

|Parancssori paraméter|Leírás|
|-----------------------------|-----------------|
|**/SK:**< StorageAccountKey\>|`Optional.`A tárfiók hívóbetűjét az adatok Importálja a tárfiók. Meg kell adni vagy **/sk:**< StorageAccountKey\> vagy **/csas:**< ContainerSas\> parancsban.|
|**/csas:**< ContainerSas\>|`Optional`. A tároló SAS-t, a tárolási fiók importálja az adatokat. Meg kell adni vagy **/sk:**< StorageAccountKey\> vagy **/csas:**< ContainerSas\> parancsban.<br /><br /> Ez a paraméter értéke a tároló neve követi a kérdőjel (?) és a SAS-jogkivonatot kell kezdődnie. Példa:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Az engedélyeket, hogy az URL-címet vagy egy tárolt hozzáférési házirendben megadott, tartalmaznia kell olvasási, írási és törlési importálási feladatok, és olvasási, írási és lista exportálási feladatok.<br /><br /> Ha ez a paraméter meg van adva, az importált vagy exportált összes BLOB a közös hozzáférésű jogosultságkódot megadott tároló tartományba kell esnie.|
|**/ t:**< TargetDriveLetter\>|`Required.`A meghajtóbetűjelet, a cél merevlemez a jelenlegi példány munkamenetre, a záró kettőspont nélkül.|
|**Format**|`Optional.`Adja meg ezt a paramétert, ha a meghajtó kell formázni; Ellenkező esetben hagyja ki ezt az. Mielőtt az eszköz a meghajtó formátumú, akkor arra fogja kérni a konzolról megerősítést. A megerősítő letiltásához, adja meg a /silentmode paramétert.|
|**/silentmode**|`Optional.`Adja meg ezt a paramétert ne jelenjen meg többé a megerősítést a targert meghajtón.|
|**/ titkosítása**|`Optional.`Ez a paraméter megadott, a meghajtón a BitLocker még nincs titkosítva, és a eszköz által titkosítani kell. Ha a meghajtó már a Bitlockerrel titkosított, majd kihagyja ezt a paramétert, és adja meg a `/bk` paraméter, így a meglévő BitLocker-kulcsot.<br /><br /> Ha megadja a `/format` paraméter, akkor is meg kell adnia a `/encrypt` paraméter.|
|**/BK:**< BitLockerKey\>|`Optional.`Ha `/encrypt` van megadva, hagyja ki ezt a paramétert. Ha `/encrypt` van megadva, telepíteni kell már titkosított a meghajtón a BitLocker szolgáltatással. Ez a paraméter használatával adja meg a BitLocker-kulcsot. BitLocker-titkosítást az importálási feladatok minden merevlemezét szükség.|
|**/ logdir:**< LogDirectory\>|`Optional.`A naplózási könyvtár határozza meg azt, hogy részletes naplókat, valamint az ideiglenes fájlok tárolására. Ha nincs megadva, a naplózási könyvtár az aktuális könyvtárban lesz.|

### <a name="parameters-required-for-all-copy-sessions"></a>Az összes másolási munkamenetek szükséges paraméterek
 A naplófájl a merevlemez-meghajtóról minden példány munkamenetek állapotát tartalmazza. Az importálási feladat létrehozásához szükséges információkat is tartalmaz. Mindig meg kell adnia egy napló fájlt, ha fut az Azure Import/Export eszköz, valamint egy másolatot munkamenet-azonosító:

|||
|-|-|
|Parancssori paraméter|Leírás|
|**/j:**< JournalFile\>|`Required.`A naplófájl elérési útja. Minden olyan meghajtó meg kell adni pontosan egy napló fájlt. Vegye figyelembe, hogy a naplófájl nem kell lennie a cél-meghajtón. A napló fájl kiterjesztése `.jrn`.|
|**/ID:**< munkamenet-azonosító\>|`Required.`A munkamenet-azonosító egy másolás munkamenet azonosítja. A pontos egy megszakított másolási munkamenet-helyreállítás szolgál. Másolás munkamenetben másolt fájlok nevű után a munkamenet-azonosító a célként megadott meghajtón tárolják.|

### <a name="parameters-for-copying-a-single-directory"></a>Egyetlen könyvtár másolására paraméterek
 Másolja át egy könyvtár, alkalmazza a következő szükséges és választható paraméterek:

|Parancssori paraméter|Leírás|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.`A célmeghajtó átmásolni a fájlokat tartalmazó forráskönyvtárat. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie.|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.`A Windows Azure-tárfiókban lévő a cél virtuális könyvtár elérési útja. Előfordulhat, hogy a virtuális könyvtár, vagy előfordulhat, hogy már nem létezik.<br /><br /> Megadhatja, hogy a tároló, vagy egy blob előtagja, például `music/70s/`. A célkönyvtár a tároló neve követ perjellel kell kezdődnie. "/", és előfordulhat, hogy választhatóan blob virtuális könyvtár nem végződik "/".<br /><br /> Ha a céltárolója a legfelső szintű tárolója, meg kell adnia a legfelső szintű tárolója, többek között a perjelet, mint `$root/`. Mivel a gyökérszintű tárolóban blobok nem tartalmazhatnak "/" a név a forráskönyvtárban alkönyvtáraiban nem kerülnek a legfelső szintű tároló esetén a célkönyvtárat.<br /><br /> Ne feledje el érvényes tároló-neveket használja a cél virtuális címtárak vagy blobot megadásakor. Ne feledje, hogy a tároló nevének kisbetűnek kell lennie. A tároló elnevezési szabályait, lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Szabályozó:**< átnevezése &#124; nem írható felül &#124; felülírása >|`Optional.`Meghatározza, ha a megadott címmel blob már létezik. Ez a paraméter érvényes értékei: `rename`, `no-overwrite` és `overwrite`. Ne feledje, hogy ezek az értékek kis-és nagybetűket. Ha nincs érték megadva, az alapértelmezett érték `rename`.<br /><br /> Ez a paraméter hatással van a megadott könyvtárban található összes fájl számára megadott érték a `/srcdir` paraméter.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`A cél blobok blob típusát határozza meg. Érvényes értékek a következők: `BlockBlob` és `PageBlob`. Ne feledje, hogy ezek az értékek kis-és nagybetűket. Ha nincs érték megadva, az alapértelmezett érték `BlockBlob`.<br /><br /> A legtöbb esetben `BlockBlob` ajánlott. Ha megad `PageBlob`, minden fájl a könyvtárban hosszát 512, egy lap lapblobokra méretét többszörösének kell lennie.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`A fájl elérési útját tulajdonság a cél BLOB objektumokhoz. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`A cél blobokat a metaadatok fájl elérési útja. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|

### <a name="parameters-for-copying-a-single-file"></a>Paraméterek egy fájl másolása
 Egyetlen fájl másolása, alkalmazza a következő szükséges és választható paraméterek:

|Parancssori paraméter|Leírás|
|----------------------------|-----------------|
|**/srcfile:**< SourceFile\>|`Required.`A másolandó fájl teljes elérési útja. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie.|
|**/dstblob:**< DestinationBlobPath\>|`Required.`A Windows Azure-tárfiókba forrásblobot elérési útja. Előfordulhat, hogy a blob, vagy előfordulhat, hogy már nem létezik.<br /><br /> Adja meg a blob nevének elején a tároló neve. A blob neve nem kezdődhet "/" vagy a tárfiók nevét. A blob elnevezési szabályait, lásd: [elnevezési és hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Ha a céltárolója a legfelső szintű tárolója, explicit módon meg kell `$root` a tárolót, mint például a `$root/sample.txt`. Vegye figyelembe, hogy a gyökérszintű tárolóban blobok nem tartalmazhatnak "/" a név.|
|**/ Szabályozó:**< átnevezése &#124; nem írható felül &#124; felülírása >|`Optional.`Meghatározza, ha a megadott címmel blob már létezik. Ez a paraméter érvényes értékei: `rename`, `no-overwrite` és `overwrite`. Ne feledje, hogy ezek az értékek kis-és nagybetűket. Ha nincs érték megadva, az alapértelmezett érték `rename`.|
|**/ BlobType:**< BlockBlob &#124; PageBlob >|`Optional.`A cél blobok blob típusát határozza meg. Érvényes értékek a következők: `BlockBlob` és `PageBlob`. Ne feledje, hogy ezek az értékek kis-és nagybetűket. Ha nincs érték megadva, az alapértelmezett érték `BlockBlob`.<br /><br /> A legtöbb esetben `BlockBlob` ajánlott. Ha megad `PageBlob`, minden fájl a könyvtárban hosszát 512, egy lap lapblobokra méretét többszörösének kell lennie.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.`A fájl elérési útját tulajdonság a cél BLOB objektumokhoz. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|
|**/ MetadataFile:**< MetadataFile\>|`Optional.`A cél blobokat a metaadatok fájl elérési útja. Lásd: [Import/Export service metaadatok és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|

### <a name="resuming-an-interrupted-copy-session"></a>Egy megszakított másolási munkamenet folytatása
 Ha a Másolás munkamenet bármilyen okból megszakad, folytathatja az eszköz csak megadott napló fájl futtatásával:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Csak a legutóbbi másolási munkamenet, ha rendellenesen, folytathatók.

> [!IMPORTANT]
>  A másolási munkamenet visszatértekor ne módosítsa a forrás adatok fájlok és könyvtárak hozzáadásával vagy eltávolításával a fájlokat.

### <a name="aborting-an-interrupted-copy-session"></a>Egy megszakított másolási munkamenet megszakítása
 Ha egy másolás munkamenet megszakad, és nincs lehetőség (Ha például forráskönyvtárat bizonyult nem érhető el) folytatni, kell megszakítás a jelenlegi munkamenet, hogy azt vissza lesz vonva, és új másolási munkamenetek indíthatók el:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Csak az utolsó másolás munkamenethez, ha a rendellenesen, is megszakítja. Vegye figyelembe, hogy megszakítása nem lehetséges az első másolás munkamenet meghajtóhoz. Ehelyett újra kell indítani a a másolási munkamenet egy új napló-fájllal.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Import/Export eszköz beállítása](storage-import-export-tool-setup-v1.md)
* [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [A gyakran használt parancsok rövid összefoglalása](storage-import-export-tool-quick-reference-v1.md) 
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
