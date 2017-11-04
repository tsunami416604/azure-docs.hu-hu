---
title: "Az Azure Import/Export jegyzékfájl formátuma |} Microsoft Docs"
description: "További tudnivalók a meghajtó jegyzékfájlt használ, amely ismerteti az Azure Blob Storage tárolóban lévő blobok és meghajtón van egy importálási közötti leképezést formátumát, vagy exportálja az Import/Export szolgáltatás feladat."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f3119e1c-2c25-48ad-8752-a6ed4adadbb0
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c1857eb94fba13c30e7f07669616f5d0ab9953f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure Import/Export service manifest-fájl formátuma
A meghajtó jegyzékfájl Azure Blob Storage tárolóban lévő blobok és egy importálási vagy exportálási feladat magában foglaló meghajtón lévő fájlok között hozzárendelését ismerteti. Az importálási művelet az Alkalmazásjegyzék-fájl jön létre a előkészítési folyamat része, és tárolt a meghajtót a meghajtó az Azure-adatközpontba történő elküldése előtt. Az exportálási művelet során a jegyzékfájl létrehozása és az Azure Import/Export szolgáltatás a meghajtón tárolja.  
  
Mindkét importálni és exportálni a feladatokat, a meghajtó jegyzékfájl-meghajtón az importálási vagy exportálási; a szolgáltatás segítségével minden API-művelet nem továbbküldi azt.  
  
A következő meghajtó jegyzékfájlt általános formátumát mutatja be:  
  
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

## <a name="manifest-xml-elements-and-attributes"></a>Jegyzék XML-elemek és attribútumok

Az adatok elemek és attribútumok meghajtó jegyzék XML formátumú meg van adva a következő táblázat.  
  
|XML-elem.|Típus|Leírás|  
|-----------------|----------|-----------------|  
|`DriveManifest`|Legfelső szintű elem|Az Alkalmazásjegyzék-fájl gyökérelemének. A fájlban minden más elemeket is, ez az elem alatt.|  
|`Version`|Attribútum, karakterlánc|A jegyzékfájl verzióját.|  
|`Drive`|Beágyazott XML-elem.|Minden olyan meghajtó jegyzékfájljának tartalmazza.|  
|`DriveId`|Karakterlánc|A meghajtó meghajtó egyedi azonosítója. A meghajtó azonosítóját a sorozatszám meghajtóra lekérdezésével található. A meghajtó sorozatszám általában nyomtatva, valamint a meghajtó külső. A `DriveID` elemnek kell szerepelnie minden `BlobList` elem a jegyzékfájlban.|  
|`StorageAccountKey`|Karakterlánc|Importálási feladatok, ha, és csak akkor, ha szükséges `ContainerSas` nincs megadva. A fiók kulcsát az Azure storage-fiók a feladathoz társított.<br /><br /> Ez az elem nem szerepel a jegyzék az exportálásba való belefoglalásra.|  
|`ContainerSas`|Karakterlánc|Importálási feladatok, ha, és csak akkor, ha szükséges `StorageAccountKey` nincs megadva. A tároló SAS a feladathoz társított BLOB eléréséhez. Lásd: [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) annak formátum. Ez az elem nem szerepel a jegyzék az exportálásba való belefoglalásra.|  
|`ClientCreator`|Karakterlánc|Megadja az ügyfélnek, amely az XML-fájl létrehozása. Ez az érték nem az Import/Export szolgáltatás értelmezi.|  
|`BlobList`|Beágyazott XML-elem.|Az importálás részét képezik, vagy exportálja feladat BLOB listáját tartalmazza. Minden egyes blob egy blob listában megosztja a metaadatok és a tulajdonságok.|  
|`BlobList/MetadataPath`|Karakterlánc|Választható. A lemezen, a blobok az importálási művelet blob listában beállított alapértelmezett metaadatait tartalmazó fájl relatív elérési út megadása A metaadatok a blob-blob alapon opcionálisan felülbírálható.<br /><br /> Ez az elem nem szerepel a jegyzék az exportálásba való belefoglalásra.|  
|`BlobList/MetadataPath/@Hash`|Attribútum, karakterlánc|A metaadatfájl Base16 kódolású MD5 kivonatoló értéke határozza meg.|  
|`BlobList/PropertiesPath`|Karakterlánc|Választható. A lemezen, a blobok az importálási művelet blob listában beállított alapértelmezett tulajdonságait tartalmazó fájl relatív elérési út megadása Ezeket a tulajdonságokat a blob-blob alapon opcionálisan felülbírálható.<br /><br /> Ez az elem nem szerepel a jegyzék az exportálásba való belefoglalásra.|  
|`BlobList/PropertiesPath/@Hash`|Attribútum, karakterlánc|A tulajdonságok fájl Base16 kódolású MD5 kivonatoló értékét adja meg.|  
|`Blob`|Beágyazott XML-elem.|Minden egyes blob listában blob adatait tartalmazza.|  
|`Blob/BlobPath`|Karakterlánc|A relatív URI-Azonosítót a blobra mutató verziótól kezdve a tároló neve. Ha a blob legfelső szintű tárolója, kell kezdődnie `$root`.|  
|`Blob/FilePath`|Karakterlánc|Megadja a meghajtón a fájl relatív elérési útja. Az exportálási feladatok a blob elérési út lesz használható lehetőség szerint; a fájl elérési útja *pl.*, `pictures/bob/wild/desert.jpg` exportálva lesznek az `\pictures\bob\wild\desert.jpg`. A nevek NTFS-korlátozások miatt azonban egy blobot egy fájl elérési útja nem hasonlítanak a blob elérési út exportálható.|  
|`Blob/ClientData`|Karakterlánc|Választható. Az ügyfél megjegyzéseket tartalmaz. Ez az érték nem az Import/Export szolgáltatás értelmezi.|  
|`Blob/Snapshot`|Dátum és idő|Exportálási feladat esetén nem kötelező. Megadja az exportált blob pillanatkép a pillanatkép azonosítója.|  
|`Blob/Length`|Egész szám|Adja meg a teljes hossza a blob bájtban. Az érték lehet egy blokkblobhoz tartoznak 200 GB-os és 1 TB az oldalakra vonatkozó blob. Az oldalakra vonatkozó blob Ez az érték 512 többszörösének kell lennie.|  
|`Blob/ImportDisposition`|Karakterlánc|Az importálási feladatok, nincs megadva az exportálási feladat nem kötelező. Azt határozza meg, hogyan az Import/Export szolgáltatás kezelje az eset, hogy az importálás ahol blob ugyanazzal a névvel már létezik. Ha ez az érték nem szerepel az importálás jegyzék, az alapértelmezett érték: `rename`.<br /><br /> Ehhez az elemhez értékek a következők:<br /><br /> -   `no-overwrite`: Ha a cél blob már létezik azonos nevű, az importálási művelet kihagyja ezt a fájlt importálja.<br />-   `overwrite`: Bármilyen meglévő cél blob teljesen felülírja az újonnan importált fájl.<br />-   `rename`: Az új blob módosított néven lesz feltöltve.<br /><br /> Az átnevezési szabály a következőképpen történik:<br /><br /> -Ha a blob neve nem tartalmaz olyan pont, más néven hozzáfűzésével létrejön `(2)` az eredeti blob neve; Ha az új név is ütközik egy meglévő blob neve, majd `(3)` helyett a rendszer hozzáfűzi `(2)`; és így tovább.<br />-Ha a blob nevének pontot tartalmaz, a része, a legutóbbi pont a következő a neve minősül. A fenti eljárással hasonló `(2)` kerül beillesztésre, mielőtt megpróbál létrehozni egy új nevet; Ha az új név továbbra is ütközik egy meglévő blob-nevet, majd a szolgáltatás a legutóbbi pont `(3)`, `(4)`, és így tovább, amíg nem ütköző név található.<br /><br /> Néhány példa:<br /><br /> A blob `BlobNameWithoutDot` átnevezésével:<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> A blob `Seattle.jpg` átnevezésével:<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|Beágyazott XML-elem.|Az oldalakra vonatkozó blob szükséges.<br /><br /> Az importálás műveletet, a bájttartományok listája az importálandó fájl határozza meg. Az eltolás és a hossz a forrásfájl, amely leírja az oldalon, az MD5 kivonatoló régió együtt minden laptartomány írja le. A `Hash` egy laptartomány attribútumot kell megadni. A szolgáltatás ellenőrzi, hogy a kivonat a blob adatainak megegyezik-e a számított MD5 kivonatoló a lap tartományból. Az importálandó fájl írják le, a teljes mérete legfeljebb 1 TB-os tetszőleges számú tartományokat is használható. Az összes lap tartomány eltolása kell rendezve, és nem a rendszer átfedéseket engedélyezettek.<br /><br /> Az Exportálás művelet, egy kumulatív bájttartományok BLOB, hogy a meghajtó exportált.<br /><br /> A tartományokat együtt vonatkozhat egy blob vagy a fájl csak részleges tartományokat.  A fájl nem fedi le minden laptartomány fennmaradó részét egy várható üzenet, és lehet, hogy a benne lévő tartalom nem definiált.|  
|`PageRange`|XML-elem.|Egy laptartomány jelöli.|  
|`PageRange/@Offset`|Attribútum, egész szám|Az eltolás az átviteli fájl-és a blob, ahol a megadott tartományban kezdődik. Ez az érték 512 többszörösének kell lennie.|  
|`PageRange/@Length`|Attribútum, egész szám|A lap tartomány hosszát határozza meg. Ez az érték legfeljebb 4 és 512 MB többszörösének kell lennie.|  
|`PageRange/@Hash`|Attribútum, karakterlánc|A nyomtatási tartomány Base16 kódolású MD5 kivonatoló értékét adja meg.|  
|`BlockList`|Beágyazott XML-elem.|Az elnevezett blokkolja a blokkblob szükséges.<br /><br /> Az importálási művelet a tiltólistához adja meg egy állít elő, amelyeknek az Azure Storage importálható. Az exportálási művelet a tiltólistához adja meg, ahol minden blokk rendelkezik tárolódnak a fájlt az exportálási lemezen. Minden blokk le a fájl-és a blokk hossza; eltolás valamennyi blokkja továbbá egy blokk-azonosító attribútum neve, és tartalmazza a kért blokkra vonatkozó az MD5 kivonatoló. Írja le a blob akár 50 000 blokkot is használható.  Blokkok kell lehetnek rendezve eltolás, és együtt közé tartozik a fájl, a teljes tartomány *azaz*, csatlakoznia kell térköz nélkül blokkolja. Ha a blob legfeljebb 64 MB, a blokk azonosítók minden egyes vagy összes hiányzó vagy az összes aktuális kell lennie. Blokk azonosítók kell lenni a Base64 kódolású karakterlánc. Lásd: [Put blokk](/rest/api/storageservices/put-block) további azonosítók kért blokkra vonatkozó követelményeket.|  
|`Block`|XML-elem.|A blokk jelöli.|  
|`Block/@Offset`|Attribútum, egész szám|Ha a megadott blokk kezdődik eltolását.|  
|`Block/@Length`|Attribútum, egész szám|A bájtok számát adja meg a blokk; Ez az érték legfeljebb 4MB lehet.|  
|`Block/@Id`|Attribútum, karakterlánc|Megadja egy karakterlánc, amely a Blokkazonosítót, a blokk.|  
|`Block/@Hash`|Attribútum, karakterlánc|A blokk Base16 kódolású MD5 kivonatának megadása.|  
|`Blob/MetadataPath`|Karakterlánc|Választható. A metaadatfájl relatív elérési út megadása Az importálás során a metaadatokat be a cél blob. Az exportálási művelet során a blob metaadatait a metaadatok fájl a meghajtón tárolja.|  
|`Blob/MetadataPath/@Hash`|Attribútum, karakterlánc|A blob metaadatait tartalmazó fájl Base16 kódolású MD5 kivonatának megadása.|  
|`Blob/PropertiesPath`|Karakterlánc|Választható. A tulajdonságok fájl relatív elérési útját adja meg. Az importálás során a célkiszolgáló blobot tulajdonságai vannak beállítva. Az exportálási művelet során a blob tulajdonságokat a meghajtón a Tulajdonságok fájlban tárolja.|  
|`Blob/PropertiesPath/@Hash`|Attribútum, karakterlánc|A blob tulajdonságai fájl Base16 kódolású MD5 kivonatának megadása.|  
  
## <a name="next-steps"></a>Következő lépések
 
* [Storage Import/Export REST API felülete](/rest/api/storageimportexport/)
