---
title: "Az Azure Import/Export naplófájlformátum |} Microsoft Docs"
description: "További információk a lépéseket az Import/Export szolgáltatás feladat végrehajtásakor létrehozott naplófájlok formátuma."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-log-file-format"></a>Az Azure Import/Export szolgáltatás naplófájlformátum
Amikor a Microsoft Azure Import/Export szolgáltatás hajt végre, az importálási feladat vagy exportálási feladat részeként egy műveletet a meghajtón, írja a naplókat, hogy a feladathoz kapcsolódó tárfiókban blokkblobokat.  
  
Az Import/Export szolgáltatás írható két naplók van:  
  
-   A hibanapló hiba esetén mindig létrejön.  
  
-   A részletes naplózás alapértelmezés szerint nincs engedélyezve, de úgy, hogy engedélyezhető a `EnableVerboseLog` tulajdonságának egy [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) vagy [frissítés Feladattulajdonság](/rest/api/storageimportexport/jobs#Jobs_Update) műveletet.  
  
## <a name="log-file-location"></a>Naplófájl helye  
A naplók írni a tároló vagy a megadott virtuális könyvtár blokkblobokat a `ImportExportStatesPath` beállítás, amely állíthat be egy `Put Job` műveletet. A helyet, amelyhez a naplók írt, attól függ, hogyan hitelesítés van megadva, a feladat a megadott együtt `ImportExportStatesPath`. A feladat hitelesítési a tárfiók kulcsa, vagy a tároló SAS (közös hozzáférésű jogosultságkód) keresztül adható meg.  
  
A tároló vagy a virtuális könyvtár neve lehet, hogy vagy az alapértelmezett nevet: kell `waimportexport`, vagy egy másik tárolóhoz vagy a megadott virtuális könyvtár neve.  
  
Az alábbi táblázatban a lehetséges beállítások:  
  
|Hitelesítési módszer|Az érték `ImportExportStatesPath`elem|Blobok napló helye|  
|---------------------------|----------------------------------------------|---------------------------|  
|Tárfiók kulcsa|Alapértelmezett érték|Nevű tárolót `waimportexport`, amely egy, az alapértelmezett tároló. Példa:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Tárfiók kulcsa|A felhasználó által megadott érték|A felhasználó által nevű tárolót. Példa:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|A tároló SAS|Alapértelmezett érték|Nevű virtuális könyvtár `waimportexport`, amely az alapértelmezett nevet, a biztonsági Társítások megadott tároló alatt.<br /><br /> Például, ha a megadott SAS a folyamat `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, akkor válassza a napló helye`https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|A tároló SAS|A felhasználó által megadott érték|A felhasználó, a biztonsági Társítások megadott tároló alatt nevű virtuális könyvtárat.<br /><br /> Például, ha a megadott SAS a folyamat `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, és a megadott virtuális könyvtár neve `mylogblobs`, akkor válassza a napló helyét `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Az URL-címet a hibaüzeneteket és részletes naplókat meghívásával kérheti le a [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. A naplók a meghajtó feldolgozás befejezése után érhetők el.  
  
## <a name="log-file-format"></a>Naplófájlformátum  
Mindkét napló formátuma megegyezik: blobok a merevlemez-meghajtóról, és a felhasználói fiókhoz közötti másolás közben előforduló eseményeket XML leírását tartalmazó blob.  
  
A részletes naplózás minden egyes blob (hogy az importálás) vagy a fájlt (exportálási feladat), a másolási művelet állapotára vonatkozó részletes adatokat tartalmaz, mivel a hibanapló csak blobok vagy hibákat észlelt az importálási vagy exportálási feladat során fájlok adatait tartalmazza.  
  
A részletes napló formátuma alább látható. A hibanapló hasonló struktúrával rendelkezik, de sikeres műveletek kiszűri.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

A következő táblázat ismerteti a naplófájl az elemek.  
  
|XML-elem.|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML-elem.|A meghajtó napló jelöli.|  
|`Version`|Attribútum, karakterlánc|A naplófájl formátumának verziója.|  
|`DriveId`|Karakterlánc|A meghajtó hardver sorozatszáma.|  
|`Status`|Karakterlánc|A meghajtó feldolgozási állapotát. Tekintse meg a `Drive Status Codes` tábla alább olvashat.|  
|`Blob`|Beágyazott XML-elem.|Egy blob jelöli.|  
|`Blob/BlobPath`|Karakterlánc|A BLOB URI.|  
|`Blob/FilePath`|Karakterlánc|A meghajtón a fájl relatív elérési útja.|  
|`Blob/Snapshot`|Dátum és idő|A blob exportálási feladat csak a pillanatfelvétel-verzió.|  
|`Blob/Length`|Egész szám|A teljes hossza a blob bájtban.|  
|`Blob/LastModified`|Dátum és idő|A dátum/idő, amelyet a blob, csak az exportálási feladat.|  
|`Blob/ImportDisposition`|Karakterlánc|A blob, hogy az importálás csak importálási rendezése.|  
|`Blob/ImportDisposition/@Status`|Attribútum, karakterlánc|Az importálás törlése állapota.|  
|`PageRangeList`|Beágyazott XML-elem.|Az oldalakra vonatkozó blob tartományokat listáját jelöli.|  
|`PageRange`|XML-elem.|Egy laptartomány jelöli.|  
|`PageRange/@Offset`|Attribútum, egész szám|A BLOB a laptartomány kezdő eltolása.|  
|`PageRange/@Length`|Attribútum, egész szám|A lap tartomány hossza bájtban.|  
|`PageRange/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló a lap tartomány.|  
|`PageRange/@Status`|Attribútum, karakterlánc|A laptartomány feldolgozási állapotát.|  
|`BlockList`|Beágyazott XML-elem.|Egy blokkblobhoz tartoznak blokkok listája jelöli.|  
|`Block`|XML-elem.|A blokk jelöli.|  
|`Block/@Offset`|Attribútum, egész szám|A blob eltolása a blokk kezdve.|  
|`Block/@Length`|Attribútum, egész szám|A blokk hossza bájtban.|  
|`Block/@Id`|Attribútum, karakterlánc|A blokk-azonosító.|  
|`Block/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló a blokk.|  
|`Block/@Status`|Attribútum, karakterlánc|A blokk feldolgozási állapotát.|  
|`Metadata`|Beágyazott XML-elem.|A blob metaadatai jelöli.|  
|`Metadata/@Status`|Attribútum, karakterlánc|A blob metaadatai feldolgozásának állapotát.|  
|`Metadata/GlobalPath`|Karakterlánc|A globális metaadatokat relatív elérési útja.|  
|`Metadata/GlobalPath/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló a globális metaadatfájl.|  
|`Metadata/Path`|Karakterlánc|A metaadatok relatív elérési útja.|  
|`Metadata/Path/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló a metaadatfájl.|  
|`Properties`|Beágyazott XML-elem.|A blob tulajdonságai jelöli.|  
|`Properties/@Status`|Attribútum, karakterlánc|Feldolgozása a blob tulajdonságai, például a fájl nem található, állapotának befejeződött.|  
|`Properties/GlobalPath`|Karakterlánc|A globális tulajdonságok relatív elérési útja.|  
|`Properties/GlobalPath/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló a globális tulajdonságok fájl.|  
|`Properties/Path`|Karakterlánc|A tulajdonságok relatív elérési útja.|  
|`Properties/Path/@Hash`|Attribútum, karakterlánc|Base16 kódolású MD5 kivonatoló tulajdonságok fájl.|  
|`Blob/Status`|Karakterlánc|A blob feldolgozási állapotát.|  
  
# <a name="drive-status-codes"></a>Meghajtó állapotkódok  
Az alábbi táblázat a meghajtó feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A meghajtó feldolgozási hiba nélkül befejeződött.|  
|`CompletedWithWarnings`|A meghajtó figyelmeztetésekkel fejeződött be az egy vagy több blobot a blobok megadott importálási dispositions / feldolgozása befejeződött.|  
|`CompletedWithErrors`|A meghajtó egy vagy több blobot, vagy adattömbök befejeződött.|  
|`DiskNotFound`|Nincs lemez a meghajtón található.|  
|`VolumeNotNtfs`|Az első adatok lemezen levő kötetet a nem nem NTFS formátumú.|  
|`DiskOperationFailed`|Ismeretlen hiba történt a meghajtón műveletek végrehajtása során.|  
|`BitLockerVolumeNotFound`|A BitLocker encryptable kötet található.|  
|`BitLockerNotActivated`|A BitLocker nincs engedélyezve a köteten.|  
|`BitLockerProtectorNotFound`|A numerikus jelszó kulcsvédőjének nem létezik a köteten.|  
|`BitLockerKeyInvalid`|A numerikus, a megadott jelszó nem lehet oldani a kötet zárolását.|  
|`BitLockerUnlockVolumeFailed`|Ismeretlen hiba történt a kötet zárolásának feloldására tett kísérlet során.|  
|`BitLockerFailed`|Ismeretlen hiba történt a BitLocker műveletet hajt végre.|  
|`ManifestNameInvalid`|A jegyzékfájl neve érvénytelen.|  
|`ManifestNameTooLong`|A jegyzékfájl neve túl hosszú.|  
|`ManifestNotFound`|A jegyzékfájl nem található.|  
|`ManifestAccessDenied`|A jegyzékfájl való hozzáférés megtagadva.|  
|`ManifestCorrupted`|Az Alkalmazásjegyzék-fájl sérült (a tartalom nem egyezik a a kivonata).|  
|`ManifestFormatInvalid`|A jegyzék tartalom nem felel meg a szükséges formátumnak.|  
|`ManifestDriveIdMismatch`|A jegyzékfájl a meghajtó azonosítója nem egyezik meg a meghajtón egy olvasás.|  
|`ReadManifestFailed`|A lemez i/o-hiba történt a jegyzék olvasása közben.|  
|`BlobListFormatInvalid`|Az Exportálás blob lista blob nem felel meg a szükséges formátumnak.|  
|`BlobRequestForbidden`|A blobot, amely a storage-fiók nem férhet hozzá. Ez akkor lehet érvénytelen tárfiók kulcsa vagy a tároló SAS miatt.|  
|`InternalError`|És belső hiba történt a meghajtó feldolgozása közben.|  
  
## <a name="blob-status-codes"></a>A BLOB állapotkódok  
Az alábbi táblázat a állapotkódok blob feldolgozásra.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A blob feldolgozási hiba nélkül befejeződött.|  
|`CompletedWithErrors`|A blob hibák egy vagy több tartományokat vagy blokkok, metaadatok vagy-tulajdonságok feldolgozása befejeződött.|  
|`FileNameInvalid`|A fájlnév érvénytelen.|  
|`FileNameTooLong`|A fájl neve túl hosszú.|  
|`FileNotFound`|A fájl nem található.|  
|`FileAccessDenied`|A fájlhoz való hozzáférés megtagadva.|  
|`BlobRequestFailed`|A blob eléréséhez a Blob szolgáltatási kérelem sikertelen volt.|  
|`BlobRequestForbidden`|A blob eléréséhez a Blob szolgáltatási kérelem tiltott. Ez akkor lehet érvénytelen tárfiók kulcsa vagy a tároló SAS miatt.|  
|`RenameFailed`|Nem sikerült átnevezni a blobot (hogy az importálás) vagy a fájlt (exportálási feladat).|  
|`BlobUnexpectedChange`|Egy váratlan módosítás történt a blob (exportálási feladat).|  
|`LeasePresent`|A blob megtalálható a címbérlet van.|  
|`IOFailed`|A lemez- vagy hálózati i/o-hiba történt a blob feldolgozása során.|  
|`Failed`|Ismeretlen hiba történt a blob feldolgozása során.|  
  
## <a name="import-disposition-status-codes"></a>Importálás törlése állapotkódok  
Az alábbi táblázat a állapotkódok megoldása érdekében egy importálási törlése.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Created`|A blob létrehozását.|  
|`Renamed`|A blob egy átnevezési importálási törlése át lett nevezve. A `Blob/BlobPath` elem tartalmazza-e az átnevezett blob URI-JÁNAK.|  
|`Skipped`|A blob ki lett hagyva `no-overwrite` importálása törlése.|  
|`Overwritten`|A blob felülírta egy meglévő blob / `overwrite` importálása törlése.|  
|`Cancelled`|Egy korábbi hiba leállt, az importálás témakör további feldolgozása.|  
  
## <a name="page-rangeblock-status-codes"></a>Tartomány/blokk állapotkódok lap  
A következő táblázat a tartományt vagy egy tiltólista feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A nyomtatási tartomány vagy blokk feldolgozási hiba nélkül befejeződött.|  
|`Committed`|A blokk véglegesítve, de nem a teljes blokkot a listán, mert más blokkok sikertelen, vagy helyezze magát teljes tiltólista sikertelen volt.|  
|`Uncommitted`|A blokk feltölteni, de a nem véglegesített.|  
|`Corrupted`|A nyomtatási tartomány vagy blokk sérült (a tartalom nem egyezik a a kivonata).|  
|`FileUnexpectedEnd`|Egy nem várt fájlvégjel történt.|  
|`BlobUnexpectedEnd`|Egy blob váratlan vége történt.|  
|`BlobRequestFailed`|A Blob szolgáltatási kérelem hozzáféréssel a lap tartomány vagy blokk nem sikerült.|  
|`IOFailed`|Lemez- vagy i/o-hiba történt a nyomtatási tartomány vagy blokk feldolgozása közben.|  
|`Failed`|Ismeretlen hiba történt a nyomtatási tartomány vagy blokk feldolgozása közben.|  
|`Cancelled`|Egy korábbi hiba leállt, az oldal tartomány vagy blokk további feldolgozásra.|  
  
## <a name="metadata-status-codes"></a>Metaadatok állapotkódok  
Az alábbi táblázat a állapotkódjai blob metaadatok feldolgozásakor.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A metaadatok feldolgozási hiba nélkül befejeződött.|  
|`FileNameInvalid`|A metaadat neve érvénytelen.|  
|`FileNameTooLong`|A metaadatok fájl neve túl hosszú.|  
|`FileNotFound`|A metaadat-fájl nem található.|  
|`FileAccessDenied`|A metaadatfájl való hozzáférés megtagadva.|  
|`Corrupted`|A metaadat-fájl sérült (a tartalom nem egyezik a a kivonata).|  
|`XmlReadFailed`|A metaadat-tartalom nem felel meg a szükséges formátumnak.|  
|`XmlWriteFailed`|A Metaadatok írása nem sikerült XML.|  
|`BlobRequestFailed`|A metaadatok eléréséhez a Blob szolgáltatási kérelem sikertelen volt.|  
|`IOFailed`|A lemez- vagy hálózati i/o-hiba történt a metaadatok feldolgozásakor a program.|  
|`Failed`|Egy ismeretlen hiba történt a metaadatok feldolgozásakor.|  
|`Cancelled`|Egy korábbi hiba leállt, a metaadatok további feldolgozásra.|  
  
## <a name="properties-status-codes"></a>Tulajdonságok állapotkódok  
A következő táblázat a blob-tulajdonságok feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A Tulajdonságok feldolgozása hiba nélkül befejeződött.|  
|`FileNameInvalid`|A tulajdonságok neve érvénytelen.|  
|`FileNameTooLong`|A tulajdonságok fájl neve túl hosszú.|  
|`FileNotFound`|A tulajdonságok fájl nem található.|  
|`FileAccessDenied`|A Tulajdonságok fájlhoz való hozzáférés megtagadva.|  
|`Corrupted`|A tulajdonságok fájl sérült (a tartalom nem egyezik a a kivonata).|  
|`XmlReadFailed`|A Tulajdonságok tartalom nem felel meg a szükséges formátumnak.|  
|`XmlWriteFailed`|A Tulajdonságok írása nem sikerült XML.|  
|`BlobRequestFailed`|Nem sikerült a Blob szolgáltatási kérelem tulajdonságainak eléréséhez.|  
|`IOFailed`|A lemez- vagy hálózati i/o-hiba történt a Tulajdonságok feldolgozása során.|  
|`Failed`|Ismeretlen hiba történt a Tulajdonságok feldolgozása során.|  
|`Cancelled`|Egy korábbi hiba a tulajdonságok további feldolgozás leállt.|  
  
## <a name="sample-logs"></a>A minta-naplók  
A következő példában látható egy részletes napló.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
A megfelelő hibanapló alább láthatók.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 A kövesse hibanaplóját, hogy az importálás a fájl nem található az importálási meghajtón kapcsolatos hibát tartalmaz. Vegye figyelembe, hogy további összetevők állapota `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

Exportálási feladat a következő hibanaplóban azt jelzi, hogy a blob tartalma írása sikeresen befejeződött a meghajtóra, azonban, hogy hiba történt a blob tulajdonságai exportálása során.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Következő lépések
 
* [Storage Import/Export REST API felülete](/rest/api/storageimportexport/)
