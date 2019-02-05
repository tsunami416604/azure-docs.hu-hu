---
title: Az Azure Import/Export szolgáltatásjegyzékének fájlformátuma |} A Microsoft Docs
description: Ismerje meg a formátum a meghajtó jegyzékfájl, amely ismerteti az Azure Blob storage-blobok és a egy, az importálás a meghajtón lévő fájlok közötti vagy exportálási feladatot az Import/Export szolgáltatás.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692476"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Az Azure Import/Export szolgáltatásjegyzékének fájlformátuma
A meghajtó jegyzékfájl közötti az Azure Blob storage-blobok és a egy importálási vagy exportálási feladatot tartalmazó meghajtón lévő fájlok hozzárendelését ismerteti. Az importálási művelet az Alkalmazásjegyzék-fájl jön létre a meghajtó előkészítési folyamat részeként, és az eszközön tárolt, a meghajtó az Azure-adatközpontban való elküldése előtt. Az exportálási művelet során a jegyzékfájl létrehozása és az Azure Import/Export szolgáltatás által az eszközön tárolt.  
  
Mindkét importálása és exportálása feladatok, a meghajtó jegyzékfájl van az eszközön tárolt importálni vagy exportálni; azt nem lesznek továbbítva a szolgáltatás segítségével minden olyan API-művelet.  
  
A következőkben egy meghajtó jegyzékfájl általános formátumát:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>XML-elemek és attribútumok manifest

Az adatok elemek és attribútumok a meghajtó jegyzékfájl XML formátum a következő táblázatban vannak megadva.  
  
|XML-elem|Typo|Leírás|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Legfelső szintű elem|A jegyzékfájl gyökérelem. A fájlban minden más elemeket is, ez az elem alatt.|  
|`Version`|Attribútum, karakterlánc|Az Alkalmazásjegyzék-fájl verzióját.|  
|`Drive`|Beágyazott XML-elem|A jegyzékfájl minden meghajtó tartalmazza.|  
|`DriveId`|String|A meghajtó meghajtó egyedi azonosítója. A meghajtó azonosítóját megtalálható a meghajtón, a hozzá tartozó sorozatszámok lekérdezésével. Meghajtó sorozatszáma általában a meghajtó, valamint a külső van nyomtatva. A `DriveID` elemének szerepelnie kell minden `BlobList` elem a jegyzékfájlban.|  
|`StorageAccountKey`|String|Importálási feladat, ha, és csak akkor szükséges `ContainerSas` nincs megadva. A feladathoz hozzárendelt a az Azure storage-fiók kulcsát.<br /><br /> Ez az elem nem szerepel a jegyzékfájl-exportálási művelet.|  
|`ContainerSas`|String|Importálási feladat, ha, és csak akkor szükséges `StorageAccountKey` nincs megadva. A tároló SAS a blobokat a feladathoz hozzárendelt eléréséhez. Lásd: [Put feladat](/rest/api/storageimportexport/jobs) annak formátum. Ez az elem nem szerepel a jegyzékfájl-exportálási művelet.|  
|`ClientCreator`|String|Adja meg az ügyfél, amely létrejön az XML-fájlt. Ez az érték nem értelmezi az Import/Export szolgáltatás által.|  
|`BlobList`|Beágyazott XML-elem|Része az importálási vagy exportálási feladatot blobok listáját tartalmazza. Blob lista minden egyes blob metaadatait és a Tulajdonságok fájlmegosztások.|  
|`BlobList/MetadataPath`|String|Választható. Itt adhatja meg a lemezen, amelyek a blobok a blob lista az importálási művelet lesz beállítva alapértelmezett metaadatait tartalmazó fájl relatív elérési útját. Ezeket a metaadatokat a blob-blob alapon igény szerint felülbírálható.<br /><br /> Ez az elem nem szerepel a jegyzékfájl-exportálási művelet.|  
|`BlobList/MetadataPath/@Hash`|Attribútum, karakterlánc|A metaadatfájl Base16-kódolású MD5 kivonatoló értékét adja meg.|  
|`BlobList/PropertiesPath`|String|Választható. Itt adhatja meg a relatív elérési út egy fájlt a lemezre, amely tartalmazza az alapértelmezett tulajdonságokat, amelyek a blobok a blob lista az importálási művelet lesz beállítva. Ezek a tulajdonságok a blob-blob alapon igény szerint felülbírálható.<br /><br /> Ez az elem nem szerepel a jegyzékfájl-exportálási művelet.|  
|`BlobList/PropertiesPath/@Hash`|Attribútum, karakterlánc|A tulajdonságok fájl Base16-kódolású MD5 kivonatoló értékét adja meg.|  
|`Blob`|Beágyazott XML-elem|Minden blob lista minden egyes blob kapcsolatos információt tartalmazza.|  
|`Blob/BlobPath`|String|A relatív URI-t a blob verziótól kezdve a tároló nevét. Ha a blob gyökérszintű tárolóban, kell kezdődnie `$root`.|  
|`Blob/FilePath`|String|Relatív elérési útját adja meg a fájlt a meghajtón található. Export-feladatok a blob elérési út lesz használható lehetőség szerint; a fájl elérési útja *például*, `pictures/bob/wild/desert.jpg` exportálja az `\pictures\bob\wild\desert.jpg`. Az NTFS-nevek-korlátozások miatt azonban egy blobot egy fájlba az alábbihoz hasonló a blob elérési útja nem elérési exportálható.|  
|`Blob/ClientData`|String|Választható. Az ügyfél megjegyzéseket tartalmaz. Ez az érték nem értelmezi az Import/Export szolgáltatás által.|  
|`Blob/Snapshot`|DateTime|Export-feladatok nem kötelező. Megadja az exportált blob-pillanatkép pillanatkép azonosítóját.|  
|`Blob/Length`|Egész szám|Adja meg a blob teljes hossza (bájt). Az érték lehet egy blokkblobhoz akár 200 GB-os és a egy lapblobra akár 1 TB. Egy lapblobra ezt az értéket az 512 többszörösének kell lennie.|  
|`Blob/ImportDisposition`|String|Nem kötelező az importálási feladatokhoz, export-feladatok nincs megadva. Ez adja meg, hogyan az Import/Export szolgáltatás kezelje az importálási feladatokhoz eset, egy blobot, ugyanazzal a névvel már létezik. Ez az érték a import-jegyzékfájlból elhagyása esetén az alapérték `rename`.<br /><br /> Ehhez az elemhez az értékek a következők:<br /><br /> -   `no-overwrite`: Ha egy cél blob már létezik azonos nevű, kihagyja az importálási művelet importálja ezt a fájlt.<br />-   `overwrite`: Minden olyan meglévő forrásblobot teljesen felülírja az újonnan importált fájl.<br />-   `rename`: Az új blob egy módosított névvel fog feltölteni.<br /><br /> Az átnevezési szabály a következőképpen történik:<br /><br /> – Ha a blob neve nem tartalmaz egy pont követ, más néven jön létre hozzáfűzésével `(2)` eredeti nevéhez; Ha az új nevet is ütközik egy meglévő blob nevét, majd `(3)` helyére a rendszer hozzáfűzi `(2)`; és így tovább.<br />– Ha a blob neve tartalmaz egy pont követ, a legutóbbi pont a következő rész számít a bővítmény nevét. A fenti eljárással hasonló `(2)` kerül beillesztésre oda, mielőtt megpróbál létrehozni egy új nevet; Ha az új név továbbra is ütközik egy meglévő blob nevét, majd a szolgáltatás utolsó pont `(3)`, `(4)`, és így tovább, amíg nem található egy nem ütköző név.<br /><br /> Néhány példa:<br /><br /> A blob `BlobNameWithoutDot` neve lesz:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> A blob `Seattle.jpg` neve lesz:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Beágyazott XML-elem|Lapblob szükséges.<br /><br /> Az importálási művelet, a bájttartományok listája az importálandó fájl határoz meg. Minden egyes laptartomány-eltolási és a forrásfájl, amely leírja az oldal, és a egy MD5-kivonat a régió írja le. A `Hash` attribútum lap tartományának megadása kötelező. A szolgáltatás ellenőrzi, hogy a blobban lévő adatok kivonata megegyezik-e az oldal a tartományból számított MD5-kivonat. Tetszőleges számú laptartomány használható egy importálandó fájl írja le, és a teljes mérete legfeljebb 1 TB. Minden laptartomány eltolás által kell következniük, és nincs átfedés engedélyezett.<br /><br /> Az exportáláshoz a művelet, bájttartományok a blob, a meghajtó exportálja egy halmazát határozza meg.<br /><br /> A tartományokat együtt csak egy blob vagy az alárendelt tartományokat is vonatkozhat.  A fájl nem hatálya alá eső összes fennmaradó részét várt, és a tartalom nem definiált.|  
|`PageRange`|XML-elem|Tartományt jelöli.|  
|`PageRange/@Offset`|Attribútum, egész szám|Az eltolás az átvitel fájlban és a blob, ahol a megadott tartományban kezdődik. Ez az érték az 512 többszörösének kell lennie.|  
|`PageRange/@Length`|Attribútum, egész szám|Nyomtatási tartomány hosszát adja meg. Ez az érték 512 és legfeljebb 4 MB többszöröse kell lennie.|  
|`PageRange/@Hash`|Attribútum, karakterlánc|Megadja a Base16-kódolású MD5-kivonat értéke a nyomtatási tartomány.|  
|`BlockList`|Beágyazott XML-elem|Szükséges egy blokkblobhoz elnevezett blokkokkal.<br /><br /> Az importálási művelet a tiltólistához adja meg Azure Storage-bA importálni kívánt blokkokra. Az exportálási művelet a tiltólistához adja meg, ahol minden egyes rendelkezik tárolja a fájlt az exportálási lemezen. Minden egyes írja le a fájl és a egy blokk hossza; eltolás minden egyes továbbá egy blokk ID attribútum neve, és a egy MD5-kivonat, a blokk tartalmaz. Akár 50 000 blokkot blob leírására használható.  Az összes blokkokat kell következniük eltolását, és együtt le kell fednie a fájl teljes körét *azaz*, ott nem lehet hézag blokkok között. Ha a blob nem legfeljebb 64 MB-ot, a blokk azonosítók minden egyes vagy az összes hiányzó vagy az összes aktuális kell lennie. Blokk-azonosítók Base64-kódolású karakterláncok lehetnek szükségesek. Lásd: [Blokkelraktározási](/rest/api/storageservices/put-block) blokk azonosítók további követelményeket.|  
|`Block`|XML-elem|A blokkolás jelöli.|  
|`Block/@Offset`|Attribútum, egész szám|Ha a megadott blokk kezdődik eltolását.|  
|`Block/@Length`|Attribútum, egész szám|A bájtok számát adja meg a blokkban; Ez az érték legfeljebb 4 MB-ot kell lennie.|  
|`Block/@Id`|Attribútum, karakterlánc|Megadja egy karakterlánc, amely a Blokkazonosítót az adatblokk számára.|  
|`Block/@Hash`|Attribútum, karakterlánc|Megadja a blokk Base16-kódolású MD5-kivonat.|  
|`Blob/MetadataPath`|String|Választható. A metaadatfájl relatív elérési útját megadja. Az importálás során a metaadatokat a cél blob van beállítva. A blob metaadatainak az exportálási művelet során a meghajtón a metaadatfájl tárolódik.|  
|`Blob/MetadataPath/@Hash`|Attribútum, karakterlánc|Itt adhatja meg a blob metaadatait tartalmazó fájl Base16-kódolású MD5-kivonat.|  
|`Blob/PropertiesPath`|String|Választható. Itt adhatja meg a tulajdonságok fájl relatív elérési útját. Az importálás során a tulajdonságok a cél BLOB vannak beállítva. Az exportálási művelet során a blobtulajdonságok a Tulajdonságok fájlt azon a meghajtón vannak tárolva.|  
|`Blob/PropertiesPath/@Hash`|Attribútum, karakterlánc|Itt adhatja meg a blob tulajdonságai fájl Base16-kódolású MD5-kivonat.|  
  
## <a name="next-steps"></a>További lépések
 
* [A Storage Import/Export REST API](/rest/api/storageimportexport/)
