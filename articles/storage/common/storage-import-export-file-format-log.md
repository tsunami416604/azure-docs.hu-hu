---
title: Az Azure Import/Export-naplófájlformátum |} A Microsoft Docs
description: Ismerje meg a lépéseket az Import/Export szolgáltatás feladat végrehajtásakor létrehozott naplófájlokat formátumát.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 2ae44edf0d9356000f64ab72fd609f1921cf095c
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316588"
---
# <a name="azure-importexport-service-log-file-format"></a>Az Azure Import/Export szolgáltatás naplófájljainak formátuma
Ha a Microsoft Azure Import/Export szolgáltatás végrehajt egy műveletet az importálási feladat vagy exportálási feladat részeként egy meghajtón, naplók írja a blokkblobok az adott feladathoz társított storage-fiókban.  
  
Nincsenek két naplók írhatók az importálási/exportálási szolgáltatás:  
  
-   Hiba esetén mindig jön létre a hibanaplót.  
  
-   A részletes napló alapértelmezés szerint nincs engedélyezve, de előfordulhat, hogy legyen engedélyezett, ha a `EnableVerboseLog` tulajdonsága egy [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) vagy [feladat tulajdonságainak](/rest/api/storageimportexport/jobs#Jobs_Update) műveletet.  
  
## <a name="log-file-location"></a>Naplófájl helye  
A naplók a tároló vagy a megadott virtuális könyvtár blokkblobok írt a `ImportExportStatesPath` beállítást, amellyel megadhatja azt a `Put Job` műveletet. A hely, amelyhez a naplók írt függ a hitelesítés hogyan van megadva a feladat a megadott együtt `ImportExportStatesPath`. A feladat hitelesítési tárfiókkulcs vagy a tároló SAS (közös hozzáférésű jogosultságkód) lehet megadni.  
  
A tároló vagy a virtuális könyvtár neve lehet vagy kell alapértelmezett neve `waimportexport`, vagy egy másik tároló vagy a megadott virtuális könyvtár neve.  
  
Az alábbi táblázat a lehetséges beállítások:  
  
|Hitelesítési módszer|Hodnota `ImportExportStatesPath`elem|A naplózási Blobok helye|  
|---------------------------|----------------------------------------------|---------------------------|  
|Tárfiók kulcsa|Alapértelmezett érték|Nevű tárolóban `waimportexport`, azaz az alapértelmezett tároló. Példa:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Tárfiók kulcsa|Felhasználó által megadott értéket|A felhasználó által nevű tárolóban. Példa:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|Tároló SAS|Alapértelmezett érték|Nevű virtuális könyvtár `waimportexport`, amely az alapértelmezett nevet, a tároló SAS megadott alatt.<br /><br /> Például, ha a megadott SAS a feladat van `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, akkor válassza a napló helye `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|Tároló SAS|Felhasználó által megadott értéket|A felhasználó, a tároló SAS megadott alatt nevű virtuális könyvtárat.<br /><br /> Például, ha a megadott SAS a feladat van `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, és a megadott virtuális könyvtár neve `mylogblobs`, majd a napló helye lesz `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Meghívásával kérheti le a hibát, és a részletes naplók URL-CÍMÉT a [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) műveletet. A naplók a meghajtó feldolgozás befejeződése után érhetők el.  
  
## <a name="log-file-format"></a>Naplófájlformátum  
Mindkét napló formátuma megegyezik: az események között a merevlemez és a felhasználói fiókhoz blobok másolása közben fellépő XML leírását tartalmazó blob.  
  
A részletes napló a másolási művelet minden egyes blob (az importálási feladat) vagy a fájlt (exportálási feladat), az állapot teljes körű információkat tartalmaz, mivel a hibanapló csak a blobok vagy hibákat észlelt az importálás során fájlok adatait tartalmazza, vagy exportálási feladat.  
  
A részletes napló formátuma alább látható. A hibanapló ugyanazzal a struktúrával rendelkezik, de kiszűri a sikeres műveletek.  

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

A következő táblázat ismerteti az elemek a naplófájl.  
  
|XML-elem|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML-elem|Meghajtó a napló jelöli.|  
|`Version`|Attribútum, karakterlánc|A napló formátuma verziója.|  
|`DriveId`|Karakterlánc|A meghajtó hardver sorozatszáma.|  
|`Status`|Karakterlánc|A meghajtó feldolgozási állapotát. Tekintse meg a `Drive Status Codes` tábla alatt további információt.|  
|`Blob`|Beágyazott XML-elem|Egy blob jelöli.|  
|`Blob/BlobPath`|Karakterlánc|A blob URI azonosítója.|  
|`Blob/FilePath`|Karakterlánc|A meghajtón található a fájl relatív elérési útja.|  
|`Blob/Snapshot`|DateTime|A blob, csak exportálási feladatokhoz pillanatfelvétel-verzió.|  
|`Blob/Length`|Egész szám|A teljes hossza (bájt) a blob.|  
|`Blob/LastModified`|DateTime|A dátum/idő, amelyet a blob, csak exportálási feladatokhoz.|  
|`Blob/ImportDisposition`|Karakterlánc|Importálás rendezése, a blob csak importálási feladatokhoz.|  
|`Blob/ImportDisposition/@Status`|Attribútum, karakterlánc|Az importálás disposition állapota.|  
|`PageRangeList`|Beágyazott XML-elem|Egy lapblobra laptartomány listáját jelöli.|  
|`PageRange`|XML-elem|Tartományt jelöli.|  
|`PageRange/@Offset`|Attribútum, egész szám|A blob a lap a tartomány kezdő eltolása|  
|`PageRange/@Length`|Attribútum, egész szám|Nyomtatási tartomány hossza (bájt).|  
|`PageRange/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a laptartomány.|  
|`PageRange/@Status`|Attribútum, karakterlánc|A laptartomány feldolgozási állapotát.|  
|`BlockList`|Beágyazott XML-elem|Egy blokkblobhoz blokkok listáját jelöli.|  
|`Block`|XML-elem|A blokkolás jelöli.|  
|`Block/@Offset`|Attribútum, egész szám|A blob található blokkot ofszetet.|  
|`Block/@Length`|Attribútum, egész szám|A blokk hossza (bájt).|  
|`Block/@Id`|Attribútum, karakterlánc|A blokk-azonosítót.|  
|`Block/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a blokkot.|  
|`Block/@Status`|Attribútum, karakterlánc|A blokk feldolgozási állapotát.|  
|`Metadata`|Beágyazott XML-elem|A blob metaadatait jelöli.|  
|`Metadata/@Status`|Attribútum, karakterlánc|A blob metaadatainak feldolgozási állapotát.|  
|`Metadata/GlobalPath`|Karakterlánc|A globális metaadatait tartalmazó fájl relatív elérési útja.|  
|`Metadata/GlobalPath/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a globális metaadatait tartalmazó fájl.|  
|`Metadata/Path`|Karakterlánc|A metaadatok fájl relatív elérési útja.|  
|`Metadata/Path/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a metaadatait tartalmazó fájl.|  
|`Properties`|Beágyazott XML-elem|A blob tulajdonságai jelöli.|  
|`Properties/@Status`|Attribútum, karakterlánc|Állapotát, például a fájl nem található, a blobtulajdonságok feldolgozása befejeződött.|  
|`Properties/GlobalPath`|Karakterlánc|A globális tulajdonságok fájl relatív elérési útját.|  
|`Properties/GlobalPath/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a globális tulajdonságok fájl.|  
|`Properties/Path`|Karakterlánc|A tulajdonságok fájl relatív elérési útját.|  
|`Properties/Path/@Hash`|Attribútum, karakterlánc|Base16-kódolású MD5-kivonat a tulajdonságok fájl.|  
|`Blob/Status`|Karakterlánc|A blob feldolgozási állapotát.|  
  
## <a name="drive-status-codes"></a>Meghajtó állapotkódok  
A következő táblázat felsorolja a meghajtó feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A meghajtón befejezte a feldolgozási hibák nélkül.|  
|`CompletedWithWarnings`|A meghajtó figyelmeztetésekkel fejeződött be az importálási dispositions a blobok számára megadott kiszolgálónként egy vagy több blobot a feldolgozása befejeződött.|  
|`CompletedWithErrors`|A meghajtó rendelkezik futása hibával végződött egy vagy több blobot vagy adattömböket.|  
|`DiskNotFound`|Nincs lemez a meghajtón található.|  
|`VolumeNotNtfs`|Az első adatok kötetet a lemezen NTFS formátumú nem szerepel.|  
|`DiskOperationFailed`|Ismeretlen hiba történt a meghajtón műveletek végrehajtása során.|  
|`BitLockerVolumeNotFound`|Nincs a BitLocker encryptable kötet található.|  
|`BitLockerNotActivated`|A BitLocker nincs engedélyezve a köteten.|  
|`BitLockerProtectorNotFound`|A numerikus jelszó kulcsvédőjének nem létezik a köteten.|  
|`BitLockerKeyInvalid`|A megadott numerikus jelszó nem lehet oldani a kötet zárolását.|  
|`BitLockerUnlockVolumeFailed`|Ismeretlen hiba történt a kötet zárolásának feloldása közben.|  
|`BitLockerFailed`|Ismeretlen hiba történt a BitLocker műveletek végrehajtása közben.|  
|`ManifestNameInvalid`|Az Alkalmazásjegyzék-fájl neve érvénytelen.|  
|`ManifestNameTooLong`|Az Alkalmazásjegyzék-fájl név túl hosszú.|  
|`ManifestNotFound`|A jegyzékfájl nem található.|  
|`ManifestAccessDenied`|A jegyzékfájl való hozzáférés megtagadva.|  
|`ManifestCorrupted`|Az Alkalmazásjegyzék-fájl sérült (ekkor a tartalom nem egyezik meg a kivonatoló).|  
|`ManifestFormatInvalid`|A jegyzékfájl tartalom nem felel meg a szükséges formátumban.|  
|`ManifestDriveIdMismatch`|A meghajtó Azonosítóját a jegyzékfájlban nem egyezik meg az egyik olvasási a meghajtóról.|  
|`ReadManifestFailed`|A lemez i/o-hiba történt a jegyzékfájl olvasásakor.|  
|`BlobListFormatInvalid`|Blob lista exportálása blob nem felel meg a szükséges formátumban.|  
|`BlobRequestForbidden`|A BLOB storage-fiókban való hozzáférés le van tiltva. Ez lehet érvénytelen tárfiók-kulcsot vagy a tároló SAS miatt.|  
|`InternalError`|És belső hiba történt a meghajtó feldolgozása közben.|  
  
## <a name="blob-status-codes"></a>BLOB állapotkódok  
A következő táblázat felsorolja a blobok feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A blob feldolgozása hiba nélkül befejeződött.|  
|`CompletedWithErrors`|A blob egy vagy több tartományokat vagy blokkok, metaadatok vagy tulajdonságok hibákkal feldolgozása befejeződött.|  
|`FileNameInvalid`|A fájlnév érvénytelen.|  
|`FileNameTooLong`|A fájl név túl hosszú.|  
|`FileNotFound`|A fájl nem található.|  
|`FileAccessDenied`|A fájlhoz való hozzáférés megtagadva.|  
|`BlobRequestFailed`|Nem sikerült a Blob szolgáltatáskérést, hogy hozzáférjen a blobhoz.|  
|`BlobRequestForbidden`|A Blob szolgáltatás kérelem eléréséhez a blob le van tiltva. Ez lehet érvénytelen tárfiók-kulcsot vagy a tároló SAS miatt.|  
|`RenameFailed`|A blob (az importálási feladat) vagy a fájlra (exportálási feladat) átnevezése sikertelen.|  
|`BlobUnexpectedChange`|Egy váratlan módosítás történt (az exportálási feladatokhoz) a blobbal együtt.|  
|`LeasePresent`|Nincs jelen a blob bérletét.|  
|`IOFailed`|Lemez vagy hálózat i/o-hiba történt a blob feldolgozása során.|  
|`Failed`|Ismeretlen hiba történt a blob feldolgozása során.|  
  
## <a name="import-disposition-status-codes"></a>Importálja a disposition állapotkódok  
Az alábbi táblázat a állapotkódok-importálási disposition feloldásához.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Created`|A blob létrejött.|  
|`Renamed`|A blob / átnevezése importálás disposition át lett nevezve. A `Blob/BlobPath` elem tartalmazza az átnevezett blob URI Azonosítóját.|  
|`Skipped`|A blob ki lett hagyva kiszolgálónként `no-overwrite` disposition importálása.|  
|`Overwritten`|A blob felülírta egy meglévő blob / `overwrite` disposition importálása.|  
|`Cancelled`|Egy korábbi hibája leállította a további feldolgozás céljából, az importálás törlése.|  
  
## <a name="page-rangeblock-status-codes"></a>Tartomány/blokk állapotkódok lap  
A következő táblázat felsorolja a tartományt vagy a blokkolás feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|Az oldal tartomány vagy blokk hibák nélkül feldolgozása befejeződött.|  
|`Committed`|A blokkblob véglegesítése után, de nem a teljes letiltása a listán, mert más blokkolja a sikertelen, vagy helyezze magát teljes tiltólista nem sikerült.|  
|`Uncommitted`|A blokk feltöltött, de nem véglegesített.|  
|`Corrupted`|Az oldal tartomány vagy blokk sérült (ekkor a tartalom nem egyezik meg a kivonatoló).|  
|`FileUnexpectedEnd`|Egy fájl váratlanul véget ért észlelt.|  
|`BlobUnexpectedEnd`|Észlelt egy blob váratlanul véget ért.|  
|`BlobRequestFailed`|A Blob szolgáltatáskérés hozzáféréssel a nyomtatási tartomány vagy blokk nem sikerült.|  
|`IOFailed`|Lemez vagy hálózat i/o-hiba történt az oldal tartomány vagy blokk feldolgozása közben.|  
|`Failed`|Ismeretlen hiba történt az oldal tartomány vagy blokk feldolgozása közben.|  
|`Cancelled`|Egy korábbi hibája leállt, az oldal tartomány vagy blokk további feldolgozás céljából.|  
  
## <a name="metadata-status-codes"></a>Metaadatok állapotkódok  
A következő táblázat felsorolja a blob metaadatainak feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A metaadatok feldolgozása hiba nélkül befejeződött.|  
|`FileNameInvalid`|A metaadat neve érvénytelen.|  
|`FileNameTooLong`|A metaadatok fájl név túl hosszú.|  
|`FileNotFound`|A metaadatfájl nem található.|  
|`FileAccessDenied`|A metaadat-fájlhoz való hozzáférés megtagadva.|  
|`Corrupted`|A metaadat-fájl sérült (ekkor a tartalom nem egyezik meg a kivonatoló).|  
|`XmlReadFailed`|A metaadat-tartalom nem felel meg a szükséges formátumban.|  
|`XmlWriteFailed`|A Metaadatok írása nem sikerült XML.|  
|`BlobRequestFailed`|A Blob használatával hozzáfér a kérelem sikertelen volt.|  
|`IOFailed`|Lemez vagy hálózat i/o-hiba történt a metaadatok feldolgozása során.|  
|`Failed`|Ismeretlen hiba történt a metaadatok feldolgozása során.|  
|`Cancelled`|Egy korábbi hibája leállította a metaadatok további feldolgozás céljából.|  
  
## <a name="properties-status-codes"></a>Tulajdonságok állapotkódok  
A következő táblázat felsorolja a blob tulajdonságai feldolgozása állapotkódjai.  
  
|Állapotkód|Leírás|  
|-----------------|-----------------|  
|`Completed`|A Tulajdonságok befejeződött a feldolgozási hibák nélkül.|  
|`FileNameInvalid`|A tulajdonságok neve érvénytelen.|  
|`FileNameTooLong`|A tulajdonságok fájl név túl hosszú.|  
|`FileNotFound`|A tulajdonságok fájl nem található.|  
|`FileAccessDenied`|A Tulajdonságok fájlhoz való hozzáférés megtagadva.|  
|`Corrupted`|A tulajdonságok fájl sérült (ekkor a tartalom nem egyezik meg a kivonatoló).|  
|`XmlReadFailed`|A Tulajdonságok tartalma nem felel meg a szükséges formátumban.|  
|`XmlWriteFailed`|A Tulajdonságok írása nem sikerült XML.|  
|`BlobRequestFailed`|A Blob tulajdonságai eléréséhez kérelem sikertelen volt.|  
|`IOFailed`|Lemez vagy hálózat i/o-hiba történt a Tulajdonságok feldolgozása során.|  
|`Failed`|Ismeretlen hiba történt a Tulajdonságok feldolgozása során.|  
|`Cancelled`|Egy korábbi hibája leállította a további feldolgozás céljából a tulajdonságok.|  
  
## <a name="sample-logs"></a>Mintanaplók  
Az alábbiakban látható egy példa részletes napló.  
  
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
  
Az alábbiakban látható a megfelelő hibanaplóját.  
  
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

 Az importálási feladat kövesse hibanaplójában importálása a meghajtón nem található egy fájllal kapcsolatos hibát tartalmaz. Vegye figyelembe, hogy további összetevők állapotának `Cancelled`.  
  
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

Exportálási feladat a következő hibanaplóban azt jelzi, hogy a blob tartalma sikeresen meg lett írva a meghajtóra, azonban, hogy hiba történt a blob tulajdonságai exportálása során.  
  
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
  
## <a name="next-steps"></a>További lépések
 
* [A Storage Import/Export REST API](/rest/api/storageimportexport/)
