---
title: Merevlemezek előkészítése az Azure Import/Export importálási feladat - v1 |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő a merevlemezek importálási feladatokhoz az Azure Import/Export szolgáltatás létrehozása a WAImportExport v1 eszközzel.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 861b3302e065689a4ea9c0df0879f9c0df12e619
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526946"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Merevlemezek előkészítése importálási feladatokhoz
Egy vagy több merevlemezek előkészítése importálási feladatokhoz, kövesse az alábbi lépéseket:

-   Azon adatok importálása a Blob szolgáltatásba

-   Azonosítsa a blobok a Blob service-ben és a cél virtuális könyvtárak

-   Határozza meg, hány meghajtót kell

-   Másolja az adatokat az egyes a merevlemez-meghajtók

 Egy munkafolyamat-minta, lásd: [munkafolyamat-minta a merevlemezek szolgáltatás előkészítése importálási feladatokhoz való](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Az importálandó adatok azonosítása
 Az első lépés az importálási feladat létrehozása feladata annak megállapítása, mely könyvtárak és fájlok importálni kívánja. Ez lehet könyvtárainak listáját, egyedi fájlok listáját, vagy ezeket a kettő kombinációját. Ha egy könyvtárat tartalmaz, a címtárat és annak alkönyvtáraiban található összes fájl lesz az importálási feladat részeként.

> [!NOTE]
>  Mivel alkönyvtárak foglalt rekurzív módon ha egy szülő directory részét képezi, adja meg a csak a szülőkönyvtárat. Sem is határozza meg, annak alkönyvtáraiba.
>
>  Jelenleg a Microsoft Azure Import/Export eszközzel rendelkezik-e a következő korlátozást: Ha egy címtárat egy merevlemezen tartalmazhat több adatot tartalmaz, majd a címtárban kell tördelhető kisebb könyvtárak. Például ha egy könyvtárat 2,5 TB-nyi adatot tartalmaz, és a merevlemez-területtel csak 2 TB-os, majd meg kell a 2,5 TB könyvtár felosztása kisebb könyvtárak. Ez a korlátozás kibocsátásokban megtörténik az eszköz egy újabb verziójában.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Azonosítsa a kívánt rendeltetési helyeket, a blob service-ben
 Minden könyvtár vagy importálni kívánt fájlt a cél virtuális könyvtárat vagy blobot az Azure Blob service, azonosítania kell. Ezeken a célokon fogja használni az Azure Import/Export eszköz bemeneteként. Vegye figyelembe, hogy a könyvtárakat a következő perjel karakterrel kell tagolt "/".

 Az alábbi táblázat néhány példa a blob-tárolók:

|Forrás-fájl vagy könyvtár|Cél blob vagy virtuális könyvtár|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Határozza meg, hány meghajtót van szükség
 Ezután meg kell határoznia:

-   Az adatok tárolásához szükséges merevlemez-meghajtók száma.

-   A könyvtárak és/vagy a merevlemez-meghajtóról minden egyes másolt önálló fájlok.

 Győződjön meg arról, hogy tárolja az adatokat, át kell merevlemez-meghajtók száma.

## <a name="copy-data-to-your-hard-drive"></a>Másolja az adatokat a merevlemezen
 Ez a szakasz azt ismerteti, hogyan hívhat meg egy vagy több merevlemezek az adatok másolása az Azure Import/Export eszköz. Minden alkalommal, amikor az Azure Import/Export eszköz hívja, hozzon létre egy új *munkamenet másolása*. Minden meghajtó, amelyre a Másolás; legalább egy másolási munkamenet létrehozása bizonyos esetekben szükség lehet egynél több példány munkamenet másolása az adatok egyetlen meghajtóval. Az alábbiakban néhány oka, hogy szükség lehet a több példány munkamenetet:

-   Létre kell hoznia egy külön példányt munkamenetet mindegyik meghajtóról másolt.

-   A másolási munkamenet másolhatja vagy egyetlen címtárban, vagy egy blobot a meghajtó. Ha több címtár, több blobok vagy a kettő kombinációjával másol, szüksége több példány-munkamenetek létrehozását.

-   Megadhatja a tulajdonságok és metaadatok, amelyek a blobok, az importálási feladat részeként importálva lesz beállítva. A Tulajdonságok vagy a metaadatokat, amelyek adja meg, ha a másolási munkamenet összes BLOB másolási munkamenet által megadott érvényes lesz. Ha meg szeretné határozni a különböző tulajdonságok vagy néhány BLOB metaadatai, szüksége egy külön példányt munkamenet létrehozásához. Lásd: [a tulajdonságok és metaadatok az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import-v1.md)további információt.

> [!NOTE]
>  Ha több gépet ismertetett követelményeknek megfelelő rendelkezik [beállítás mentése az Azure Import/Export eszköz](storage-import-export-tool-setup-v1.md), a másoláshoz adatok párhuzamosan több merevlemez-meghajtók, ez az eszköz egy példányát futtató minden számítógépen.

 Mindegyik merevlemez, amely előkészíti az Azure Import/Export eszközzel az eszköz létre fog hozni egy egyetlen naplófájl. Az összes meghajtó az importálási feladat létrehozásához szüksége lesz az adatbázisnapló-fájlok. A naplófájl is használható meghajtó előkészítés folytatásához, ha az eszköz megszakad.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Importálási feladat az Azure Import/Export eszköz szintaxisa
 Meghajtók előkészítése importálási feladatokhoz, hívja meg az Azure Import/Export eszközzel együtt a **PrepImport** parancsot. Mely paraméterek is függ, hogy ez-e az első másolás munkamenet, vagy egy későbbi másolási munkamenet.

 Az első másolás munkamenet-meghajtó használatához néhány további paraméter adja meg a tárfiók kulcsát; a célként megadott meghajtóbetűjel; e a meghajtó formátumban kell lenniük; a meghajtó titkosítani kell-e, és ha igen, a BitLocker-kulcsot. és a naplózási könyvtár. Íme egy könyvtárat vagy akár egyetlen fájlt átmásolni egy kezdeti másolatot munkamenet szintaxisa:

 **Először másolja a munkamenet egyetlen címtárban másolása**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Először másolja a munkamenet egyetlen fájl másolása**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 Az ezt követő másolási-munkamenetekben nem kell megadnia a kezdeti paramétereket. Íme egy könyvtárat vagy akár egyetlen fájlt másolja későbbi másolási munkamenetet szintaxisa:

 **Másolás későbbi munkamenetek egyetlen címtárban másolása**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Egyetlen fájl másolása későbbi másolási munkamenetek**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Az első másolás munkamenet merevlemez-meghajtó paraméterei
 Fájlok másolása a merevlemezen, az Azure Import/Export eszköz minden egyes futtatásakor az eszköz létrehoz egy másolási munkamenet. Minden példány munkamenet egyetlen címtárban vagy akár egyetlen fájlt másol egy merevlemezen. A másolási munkamenet állapotának íródik a naplófájl. Ha a másolási munkamenet megszakad (például miatt a rendszer az áramellátás megszakadása), azt újra futtatni az eszközt, és a naplófájl a parancssorban megadásával folytathatók.

> [!WARNING]
>  Ha megad a **/formázása** az első másolás munkamenet paramétert, a meghajtó formázva lesz, és a meghajtón található összes adat törlődik. Javasoljuk, hogy használjon üres meghajtókon csak a Másolás-munkamenetet.

 Az első másolás munkamenet minden meghajtó parancs paraméterei különböző, mint a parancsok másolás későbbi munkamenet során. Az alábbi táblázat a további paraméterek, amelyek az első másolás munkamenet érhető el:

|Parancssori paraméter|Leírás|
|-----------------------------|-----------------|
|**/SK:**< StorageAccountKey\>|`Optional.` A tárfiók kulcsát, amelyhez a rendszer importálja az adatokat a tárfiók. Meg kell adni vagy **/sk:**< StorageAccountKey\> vagy **/csas:**< ContainerSas\> parancsban.|
|**/csas:**< ContainerSas\>|`Optional`. A tároló SAS használatával importálja az adatokat a tárfiók. Meg kell adni vagy **/sk:**< StorageAccountKey\> vagy **/csas:**< ContainerSas\> parancsban.<br /><br /> Ez a paraméter értéke a tároló nevét, egy kérdőjelet (?) és a SAS-jogkivonatot kell kezdődnie. Példa:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Az engedélyeket, akár az URL-cím vagy egy tárolt hozzáférési házirendben megadott, tartalmaznia kell olvasási, írási és törlési az importálási feladatokhoz, és olvasási, írási és a lista az export-feladatok.<br /><br /> Ha ez a paraméter meg van adva, az importált vagy exportált összes blobot a tárolóban, a közös hozzáférésű jogosultságkód található a megadott kell lennie.|
|**/ t:**< TargetDriveLetter\>|`Required.` A cél merevlemez-meghajtó a jelenlegi példány munkamenet a záró kettőspont nélkül meghajtóbetűjelet.|
|**Format**|`Optional.` Adja meg ezt a paramétert, ha a meghajtó kell formázni; Ellenkező esetben hagyja azt. Az eszköz formázza a meghajtón, mielőtt a rendszer kér megerősítést konzolról. A jóváhagyás le, adja meg a /silentmode paramétert.|
|**/silentmode**|`Optional.` Adja meg ezt a paramétert a megerősítést a targert meghajtón le.|
|**/encrypt**|`Optional.` Amikor a meghajtót a BitLocker még nem titkosítva, és titkosítani kell az eszköz a megadott ezt a paramétert. Ha a meghajtó már a Bitlockerrel titkosított, majd hagyja ki ezt a paramétert, és adja meg a `/bk` paramétert, a meglévő BitLocker-kulcs megadása.<br /><br /> Ha megad a `/format` paramétert, akkor is meg kell adnia a `/encrypt` paraméter.|
|**/BK:**< BitLockerKey\>|`Optional.` Ha `/encrypt` van adva, hagyja ki ezt a paramétert. Ha `/encrypt` van megadva, rendelkeznie kell már titkosította a meghajtó bitlockerrel van. Ez a paraméter használatával adja meg a BitLocker-kulcsot. BitLocker-titkosítást az összes merevlemezek importálási feladatokhoz szükség.|
|**/ logdir:**< LogDirectory\>|`Optional.` A naplózási könyvtár részletes naplók, valamint az ideiglenes fájlok tárolására szolgáló könyvtár megadása Ha nincs megadva, a naplózási könyvtár az aktuális könyvtárban lesz.|

### <a name="parameters-required-for-all-copy-sessions"></a>Összes másolat munkamenetet szükséges paramétereket
 A naplófájl a merevlemez-meghajtóra vonatkozó összes másolási munkamenetet állapotát tartalmazza. Az importálási feladat létrehozásához szükséges információkat is tartalmaz. Mindig meg kell adnia egy naplófájl, amikor fut az Azure Import/Export eszköz, valamint a példány a munkamenet-azonosító:

|||
|-|-|
|Parancssori paramétert|Leírás|
|**/j:**< JournalFile\>|`Required.` A naplófájl elérési útja. Minden olyan meghajtó pontosan egy journal-fájlt kell rendelkeznie. Vegye figyelembe, hogy a naplófájl nem kell lennie a cél-meghajtón. A napló fájlkiterjesztés `.jrn`.|
|**/ ID:**< munkamenet-azonosító\>|`Required.` A munkamenet-azonosító egy másolási munkamenet azonosítja. Győződjön meg, hogy egy megszakított másolási munkamenet pontos helyreállítási szolgál. Másolás munkamenetben másolt fájlok esetében a munkamenet-Azonosítót a célmeghajtó után nevű könyvtárat vannak tárolva.|

### <a name="parameters-for-copying-a-single-directory"></a>A másolási egyetlen címtárban paraméterek
 Ha egyetlen címtárban másol, az alábbi szükséges és választható paramétereket a alkalmazni:

|Parancssori paramétert|Leírás|
|----------------------------|-----------------|
|**/srcdir:**< SourceDirectory\>|`Required.` A forráskönyvtár a célmeghajtó kell másolni a fájlokat tartalmazó. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie.|
|**/dstdir:**< DestinationBlobVirtualDirectory\>|`Required.` A Windows Azure storage-fiókban a cél virtuális könyvtár elérési útja. Előfordulhat, hogy a virtuális könyvtár, vagy már nem létezik.<br /><br /> Megadhat egy tárolót, vagy egy blob előtagot, például `music/70s/`. A célkönyvtárban duplikátum a tároló neve, amelyet által előre dőlő perjellel kell kezdődnie. a "/", és szükség esetén belefoglalhatja a előfordulhat, hogy a blob virtuális könyvtár nem végződik "/".<br /><br /> Ha a cél tároló a legfelső szintű tárolót, pontosan meg kell adni a legfelső szintű tárolót, többek között a perjelet, mint `$root/`. Mivel a gyökérszintű tárolóban található blobok nem szerepelhet "/" a nevében szerepel a forráskönyvtárban alkönyvtáraiban nem lesznek másolva a célkönyvtárban duplikátum esetén a gyökérszintű tárolóban.<br /><br /> Ügyeljen arra, érvényes a tároló nevének megadása a cél virtuális könyvtárak és blobok esetén. Ne feledje, hogy a tároló neve csak kisbetűket tartalmazhatnak. Tároló elnevezési szabályait, lásd: [elnevezése és a hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/ Törlése:**< átnevezése&#124;nem írja felül&#124;felülírása >|`Optional.` Meghatározza a viselkedését, amikor egy blob a megadott címmel már létezik. Ez a paraméter érvényes értékei a következők: `rename`, `no-overwrite` és `overwrite`. Vegye figyelembe, hogy ezek az értékek kis-és nagybetűket. Ha nem ad meg értéket, az alapértelmezett érték `rename`.<br /><br /> Ez a paraméter által megadott könyvtárban lévő összes fájlt érint számára megadott érték a `/srcdir` paraméter.|
|**/ BlobType:**< BlockBlob&#124;PageBlob >|`Optional.` A cél blobok blob típusát határozza meg. Érvényes értékek a következők: `BlockBlob` és `PageBlob`. Vegye figyelembe, hogy ezek az értékek kis-és nagybetűket. Ha nem ad meg értéket, az alapértelmezett érték `BlockBlob`.<br /><br /> A legtöbb esetben `BlockBlob` ajánlott. Ha megad `PageBlob`, minden fájl a könyvtárban hossza 512, a lapblobok oldal méretét többszöröse kell legyen.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` A fájl elérési útját a tulajdonság a cél blobok számára. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|
|**/ Metaadatfájl:**< metaadatfájl\>|`Optional.` Az a cél blobok metaadatait tartalmazó fájl elérési útja. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|

### <a name="parameters-for-copying-a-single-file"></a>Egyetlen fájl másolása paraméterei
 Ha egyetlen fájlba másol, az alábbi szükséges és választható paramétereket a alkalmazni:

|Parancssori paramétert|Leírás|
|----------------------------|-----------------|
|**/srcfile:**< SourceFile\>|`Required.` A másolandó fájl teljes elérési útja. A könyvtár elérési útjának abszolút elérési útnak (nem relatív elérési utat) kell lennie.|
|**/dstblob:**< DestinationBlobPath\>|`Required.` A Windows Azure storage-fiókban a cél-blob elérési útja. Előfordulhat, hogy a blob, vagy már nem létezik.<br /><br /> Adja meg a blob neve elején a tároló nevét. A blob neve nem kezdődhet "/" vagy a tárfiók nevét. Blob elnevezési szabályait, lásd: [elnevezése és a hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Ha a cél tároló a legfelső szintű tárolót, explicit módon meg kell `$root` a tárolót, mint például a `$root/sample.txt`. Vegye figyelembe, hogy a gyökérszintű tárolóban található blobok nem szerepelhet "/" nevükben.|
|**/ Törlése:**< átnevezése&#124;nem írja felül&#124;felülírása >|`Optional.` Meghatározza a viselkedését, amikor egy blob a megadott címmel már létezik. Ez a paraméter érvényes értékei a következők: `rename`, `no-overwrite` és `overwrite`. Vegye figyelembe, hogy ezek az értékek kis-és nagybetűket. Ha nem ad meg értéket, az alapértelmezett érték `rename`.|
|**/ BlobType:**< BlockBlob&#124;PageBlob >|`Optional.` A cél blobok blob típusát határozza meg. Érvényes értékek a következők: `BlockBlob` és `PageBlob`. Vegye figyelembe, hogy ezek az értékek kis-és nagybetűket. Ha nem ad meg értéket, az alapértelmezett érték `BlockBlob`.<br /><br /> A legtöbb esetben `BlockBlob` ajánlott. Ha megad `PageBlob`, minden fájl a könyvtárban hossza 512, a lapblobok oldal méretét többszöröse kell legyen.|
|**/ PropertyFile:**< PropertyFile\>|`Optional.` A fájl elérési útját a tulajdonság a cél blobok számára. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|
|**/ Metaadatfájl:**< metaadatfájl\>|`Optional.` Az a cél blobok metaadatait tartalmazó fájl elérési útja. Lásd: [Import/Export szolgáltatás metaadat és a Tulajdonságok fájlformátum](../storage-import-export-file-format-metadata-and-properties.md) további információt.|

### <a name="resuming-an-interrupted-copy-session"></a>A megszakított másolási munkamenet folytatása
 Ha a másolási munkamenet bármilyen okból megszakad, folytathatja az eszköz csak a megadott journal-fájl futtatásával:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Csak a legutóbbi másolási munkamenet, ha rendellenesen, tudja folytatni.

> [!IMPORTANT]
>  A másolási munkamenet folytatásakor ne módosítsa a forrás adatok fájlok és könyvtárak hozzáadásával vagy eltávolításával a fájlokat.

### <a name="aborting-an-interrupted-copy-session"></a>A megszakított másolási munkamenet megszakítása
 Ha egy példány munkamenet megszakad, és nem lehetséges (például, ha a forráskönyvtár bizonyult nem érhető el) folytatásához, a jelenlegi munkamenet, hogy azt vissza lesz állítva, és az új példány munkamenetek indíthatók el kell megszakít:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Csak az utolsó másolás munkamenet, ha rendellenesen, is megszakítja. Vegye figyelembe, hogy az első másolás munkamenet-meghajtó nem szakítható meg. Ehelyett újra kell indítania a másolási munkamenet együtt egy új naplófájl.

## <a name="next-steps"></a>További lépések

* [Az Azure Import/Export eszköz telepítése](storage-import-export-tool-setup-v1.md)
* [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Használt gyakori parancsok gyors áttekintése](storage-import-export-tool-quick-reference-v1.md) 
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
