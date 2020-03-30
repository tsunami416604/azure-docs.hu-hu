---
title: Adatok másolása fájlrendszerbe/fájlrendszerből az Azure Data Factory használatával
description: Ismerje meg, hogyan másolhatja az adatokat egy helyszíni fájlrendszerbe és onnan az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d298c83c0c1a0f33f28644e2e467ad5035300221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265934"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Adatok másolása helyszíni fájlrendszerbe és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-file-system-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-file-system.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Fájlrendszer-összekötő v2-ben című témakört.](../connector-file-system.md)


Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása, és egy helyszíni fájlrendszerből. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az intézményi **fájlrendszerből** adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból adatokat másolhat **egy helyszíni fájlrendszerbe:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A Másolási tevékenység nem törli a forrásfájlt, miután sikeresen másolta a célhelyre. Ha a sikeres másolás után törölnie kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez és a folyamatban lévő tevékenység használatához.

## <a name="enabling-connectivity"></a>Csatlakozás engedélyezése
A Data Factory támogatja a helyszíni fájlrendszerhez való csatlakozást az **Adatkezelési átjárón**keresztül. A Data Factory szolgáltatás hoz a helyszíni környezetben telepítenie kell az adatkezelési átjárót, hogy bármely támogatott helyszíni adattárhoz csatlakozzon, beleértve a fájlrendszert is. Az Adatkezelési átjáróról és az átjáró beállításával kapcsolatos részletes útmutatásról az [Adatok áthelyezése a helyszíni források és a felhő között az Adatkezelési átjáróval](data-factory-move-data-between-onprem-and-cloud.md)című témakörben olvashat. Az adatkezelési átjárón kívül nincs más bináris fájl telepítve a helyszíni fájlrendszerrel való kommunikációhoz és a helyszíni fájlrendszerből való kommunikációhoz. Telepítenie kell és használnia kell az adatkezelési átjárót, még akkor is, ha a fájlrendszer az Azure IaaS virtuális gépben található. Az átjáróról az [Adatkezelési átjáró című témakörben](data-factory-data-management-gateway.md)talál részletes információt.

Linuxos fájlmegosztás használatához telepítse a [Samba-t](https://www.samba.org/) a Linux-kiszolgálóra, és telepítse az Adatkezelési átjárót Windows-kiszolgálóra. Az adatkezelési átjáró Linux-kiszolgálóra történő telepítése nem támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot másolási tevékenységgel, amely az adatokat fájlrendszerbe/fájlrendszerből különböző eszközök/API-k használatával helyezi át.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Azure blob storage-ból egy helyszíni fájlrendszerbe, két összekapcsolt szolgáltatást hoz létre a helyszíni fájlrendszer és az Azure-tárfiók és az adat-előállító összekapcsolására. A helyszíni fájlrendszerre jellemző csatolt szolgáltatástulajdonságokról a [Csatolt szolgáltatástulajdonságok](#linked-service-properties) című szakaszban találhatók.
3. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában hozzon létre egy adatkészletet a blob tároló és a bemeneti adatokat tartalmazó mappa megadásához. Emellett egy másik adatkészletet is létrehozhat a fájlrendszer mappájának és fájlnevének megadásához (nem kötelező). A helyszíni fájlrendszerre jellemző adatkészlet-tulajdonságokat az adatkészlet tulajdonságai szakaszban [található.](#dataset-properties)
4. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában a BlobSource-ot forrásként, a FileSystemSink-et pedig a másolási tevékenység fogadójaként használja. Hasonlóképpen, ha a helyszíni fájlrendszerről az Azure Blob Storage-ba másolja a másolási tevékenységet, használja a FileSystemSource és a BlobSink szolgáltatást. A helyszíni fájlrendszerre jellemző másolási tevékenységtulajdonságokat a [Tevékenység tulajdonságainak másolása](#copy-activity-properties) című szakaszban olvassa el. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja.  A Data Factory-entitások JSON-definícióival rendelkező mintákat, amelyek adatok másolására szolgálnak a fájlrendszerbe/fájlrendszerből, lásd a [JSON-példák](#json-examples-for-copying-data-to-and-from-file-system) című cikk ben című szakaszt.

A következő szakaszok a Fájlrendszerre jellemző Data Factory entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
A helyszíni fájlrendszert összekapcsolhatja egy Azure-adat-előállítóval a **helyszíni fájlkiszolgálóhoz** csatolt szolgáltatással. Az alábbi táblázat a helyszíni fájlkiszolgálóhoz csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a típustulajdonság **OnPremisesFileServer**. |Igen |
| gazda |A másolni kívánt mappa gyökérelérési útját adja meg. Használja a " \ ' escape karaktert a karakterlánc speciális karaktereihez. [Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.](#sample-linked-service-and-dataset-definitions) |Igen |
| Userid |Adja meg annak a felhasználónak az azonosítóját, aki hozzáfér a kiszolgálóhoz. |Nem (ha a encryptedCredential lehetőséget választja) |
| jelszó |Adja meg a felhasználó jelszavát (userid). |Nem (ha a encryptedCredential (titkosított hitelesítő adat) lehetőséget választja |
| titkosított hitelesítő adatok |Adja meg a New-AzDataFactoryEncryptValue parancsmag futtatásával beszerezhető titkosított hitelesítő adatokat. |Nem (ha úgy dönt, hogy a használati parancsot és a jelszót egyszerű szövegként adja meg) |
| átjárónév |Megadja annak az átjárónak a nevét, amelyet a Data Factory a helyszíni fájlkiszolgálóhoz való csatlakozáshoz kell használnia. |Igen |


### <a name="sample-linked-service-and-dataset-definitions"></a>Mintacsatolt szolgáltatás- és adatkészlet-definíciók
| Forgatókönyv | Állomás a csatolt szolgáltatásdefinícióban | folderPath az adatkészlet definíciójában |
| --- | --- | --- |
| Helyi mappa az adatkezelési átjárógépen: <br/><br/>Példák:\\ \* D: vagy D:\folder\almappa\\\* |D:\\ \\ (adatkezelési átjáró 2.0-s és újabb verzióihoz) <br/><br/> localhost (korábbi verziókhoz, mint az Adatkezelési átjáró 2.0) |. vagy\\\\mappaalmappa (Adatkezelési átjáró 2.0-s és újabb verzióihoz) \\ \\ <br/><br/>D:\\ \\ vagy\\\\D: mappa\\\\almappa (2.0 alatti átjáróverzió esetén) |
| Távoli megosztott mappa: <br/><br/>\\ \\Példák:\\myserver \\ \\megosztás\\\\ \* \\vagy\\myserver megosztási mappa almappája\\\* |\\\\\\\\myserver\\\\megosztás |. vagy\\\\mappa almappája \\ \\ |

>[!NOTE]
>A felhasználói felületen keresztültörténő szerkesztéskor nem kell`\\`dupla fordított perjelet ( ) megadnia ahhoz, hogy a JSON-on keresztül ily értsen, adja meg az egyetlen fordított perjelet.

### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használata egyszerű szövegben

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Példa: Titkosított hitelesítő adatok használata

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása című](data-factory-create-datasets.md)témakörben található. A JSON adatkészletek szerkezete, rendelkezésre állása és házirendje minden adatkészlettípushoz hasonlóak.

A typeProperties szakasz az adatkészlet egyes típusaiesetében eltérő. Olyan információkat szolgáltat, mint például az adatok helye és formátuma az adattárban. A **FileShare** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |Megadja a mappa részelérési útját. Használja az escape\' karaktert ' a karakterlánc speciális karaktereihez. A helyettesítő karakter szűrő nem támogatott. [Példákat lásd: Minta csatolt szolgáltatás és adatkészlet-definíciók.](#sample-linked-service-and-dataset-definitions)<br/><br/>Ezt a tulajdonságot kombinálhatja **a partitionBy tulajdonsággal,** hogy a mappaelérési utak a szelet kezdő/záró dátum-időpontja alapján legyenek. |Igen |
| fileName |Ha azt szeretné, hogy a táblázat a mappában lévő fájlra hivatkozzon, adja meg a fájl nevét a **folder-ban.** Ha nem ad meg értéket ehhez a tulajdonsághoz, a táblázat a mappában lévő összes fájlra mutat.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenységgyűjtőben, a létrehozott fájl neve a következő formátumú: <br/><br/>`Data.<Guid>.txt`(Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amelyet a mappapatikus ban lévő fájlok egy részhalmazának kijelölésére, nem pedig az összes fájlhoz kell használni. <br/><br/>Az engedélyezett `*` értékek a következők: (több karakter) és `?` (egy karakter).<br/><br/>1. példa: "fileFilter": "*.log"<br/>2. példa: "fileFilter": 2014-1-?. txt"<br/><br/>Ne feledje, hogy a fileFilter bemeneti FileShare adatkészletesetén alkalmazható. |Nem |
| particionáltby |A partitionedBy segítségével dinamikus folderPath/fileName mappát adhat meg az idősorozat-adatokhoz. Egy példa folderPath paraméterezett minden óra az adatok. |Nem |
| Formátum | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa a formátum alatti **típustulajdonságot** ezen értékek egyikére. További információt a [Szövegformátum,](data-factory-supported-file-and-compression-formats.md#text-format) [a Json formátum,](data-factory-supported-file-and-compression-formats.md#json-format) [az Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [az Orc Formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [Parkettaformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) című szakaszban talál. <br><br> Ha a fájlokat fájlalapú tárolók között (bináris másolat) szeretné **másolni,** hagyja ki a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |Nem |
| tömörítés | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**és **ZipDeflate**. Támogatott szintek: **Optimális** és **leggyorsabb**. Lásd: [Fájl- és tömörítési formátumok az Azure Data Factoryban.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

> [!NOTE]
> A fájlnév és a fájlszűrő nem használható egyszerre.

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, dinamikus folderPath és fájlnév adható meg az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, a [Data Factory függvényekkel és a rendszerváltozókkal.](data-factory-functions-variables.md)

Az idősorozat-adatkészletek, az ütemezés és a szeletek további részleteinek megismeréséhez olvassa el az [Adatkészletek létrehozása](data-factory-create-datasets.md), [Ütemezés és végrehajtás,](data-factory-scheduling-and-execution.md) [valamint A folyamatok létrehozása című témakört.](data-factory-create-pipelines.md)

#### <a name="sample-1"></a>1. minta:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában a ({Slice} ) a Slice Start Data Factory rendszerváltozó értékére cserélődik a formátumban (YYYYMMDDHH). A SliceStart a szelet kezdési időpontjára utal. A folderPath minden szeletesetében más. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Ebben a példában a SliceStart évét, hónapját, napját és idejét a folderPath és fileName tulajdonságok által használt különálló változókba bontják ki.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti adatkészletek és házirendek állnak rendelkezésre minden típusú tevékenységek. Mivel a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak.

Másolási tevékenység esetén a források és a fogadók típusától függően változnak. Ha egy helyszíni fájlrendszerből helyez át adatokat, a másolási tevékenység forrástípusát a FileSystemSource mezőre **állítja.** Hasonlóképpen, ha az adatokat egy helyszíni fájlrendszerbe helyezi át, a másolási tevékenység fogadótípusát a FileSystemSink beállításra **állítja.** Ez a szakasz a FileSystemSource és a FileSystemSink által támogatott tulajdonságok listáját tartalmazza.

**A FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| Rekurzív |Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. |Igaz, Hamis (alapértelmezett) |Nem |

**A FileSystemSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior (Másként) |A másolási viselkedést határozza meg, ha a forrás BlobSource vagy FileSystem. |**Hierarchia megőrzése:** Megőrzi a fájlhierarchiát a célmappában. Ez azt illeti, a forrásfájl relatív elérési útja a forrásmappához megegyezik a célfájl célmappához való relatív elérési útvonalával.<br/><br/>**Hierarchia összeolvasztása:** A forrásmappából származó összes fájl a célmappa első szintjén jön létre. A célfájlok automatikusan létrehozott névvel jönnek létre.<br/><br/>**MergeFiles:** A forrásmappából származó összes fájl egyesítése egy fájlba. Ha meg van adva a fájlnév/blobnév, az egyesített fájlnév a megadott név. Ellenkező esetben ez egy automatikusan generált fájlnév. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz a Rekurzív és copyBehavior tulajdonságok különböző értékkombinációinak másolási műveletének eredő viselkedését ismerteti.

| rekurzív érték | copyBehavior érték | Eredményül kapott viselkedés |
| --- | --- | --- |
| igaz |megőrzési hierarchia |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a Mappa1 mappa a forrással megegyező struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5 |
| igaz |összeolvasztási hierarchia |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File5 fájlhoz |
| igaz |mergeFiles |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A fájl1 + File2 + File3 + File4 + File 5 tartalom egyetlen fájlba egyesül, automatikusan generált fájlnévvel. |
| hamis |megőrzési hierarchia |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a Folder1 mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |
| hamis |összeolvasztási hierarchia |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a Folder1 mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |
| hamis |mergeFiles |A Mappa1 forrásmappa esetében a következő<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a Folder1 mappa a következő struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen fájlba egyesül, automatikusan generált fájlnévvel.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 mappát nem veszi fel a rendszer. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Tekintse meg [a fájl- és tömörítési formátumokat az Azure Data Factory cikkében](data-factory-supported-file-and-compression-formats.md) a részletekről.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-példák az adatok fájlrendszerbe és fájlrendszerből történő másolására
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhat adatokat egy helyszíni fájlrendszerbe és az Azure Blob storage-ba. Azonban az azure Data Factory másolási tevékenység használatával *közvetlenül* a források bármelyikéből másolhatja az adatokat a [Támogatott források és fogadók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) listában felsorolt a fogadók bármelyikébe.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Példa: Adatok másolása egy helyszíni fájlrendszerből az Azure Blob storage-ba
Ez a minta bemutatja, hogyan másolhat adatokat egy helyszíni fájlrendszerből az Azure Blob storage-ba. A minta a következő Data Factory entitásokkal rendelkezik:

* [OnPremisesFileServer](#linked-service-properties)típusú csatolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [FileShare](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [A Fájlrendszerforrást](#copy-activity-properties) és a [BlobSinket](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A következő minta óránként másolja az idősorozat-adatokat egy helyszíni fájlrendszerből az Azure Blob storage-ba. Az ezekben a mintákban használt JSON-tulajdonságokat a minták utáni szakaszok ismertetik.

Első lépésként állítsa be az Adatkezelési átjárót az Adatok áthelyezése a [helyszíni források között és a felhő között az Adatkezelési átjáróval](data-factory-move-data-between-onprem-and-cloud.md)című útmutató utasításai nak megfelelően.

**Helyszíni fájlkiszolgálóhoz csatolt szolgáltatás:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Azt javasoljuk, hogy a **userid** és a **jelszó** tulajdonságai helyett használja a **encryptedCredential** tulajdonságot. A csatolt szolgáltatással kapcsolatos részleteket a [Fájlkiszolgáló hoz csatolt szolgáltatás](#linked-service-properties) ban találja.

**Azure Storage-hoz kapcsolódó szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Helyszíni fájlrendszer bemeneti adatkészlete:**

Az adatokat óránként veszi késve egy új fájlból. A folderPath és fileName tulajdonságok meghatározása a szelet kezdési időpontja alapján kerül meghatározásra.

A `"external": "true"` beállítás tájékoztatja a Data Factory-t, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállítóban végzett tevékenység állítja elő.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob tárolási kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Másolási tevékenység egy folyamatban, amelynek fájlrendszerforrása és blobfogadója van:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A json-definícióban a **forrástípus** **Fájlrendszerforrás,** **a fogadó** típusa pedig **BlobSink**lesz.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Példa: Adatok másolása az Azure SQL Database-ből egy helyszíni fájlrendszerbe
A következő minta a következőket mutatja:

* [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) típusú csatolt szolgáltatás.
* [OnPremisesFileServer](#linked-service-properties)típusú csatolt szolgáltatás.
* [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties)típusú bemeneti adatkészlet.
* [FileShare](#dataset-properties)típusú kimeneti adatkészlet.
* [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [FileSystemSink](#copy-activity-properties)programot használó másolási tevékenységgel rendelkező folyamat.

A minta óránként átmásolja az Azure SQL-táblából az idősorozat adatait egy helyszíni fájlrendszerbe. Az ezekben a mintákban használt JSON-tulajdonságokat a minták utáni szakaszok ismertetik.

**Azure SQL Database csatolt szolgáltatás:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Helyszíni fájlkiszolgálóhoz csatolt szolgáltatás:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

A **userid** **és** jelszó tulajdonságok használata helyett azt javasoljuk, hogy a **encryptedCredential** tulajdonságot használja. A csatolt szolgáltatással kapcsolatos részleteket a [Fájlrendszerhez csatolt szolgáltatás](#linked-service-properties) ban találja.

**Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az Azure SQL-ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

A ``“external”: ”true”`` beállítás tájékoztatja a Data Factory-t, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállítóban végzett tevékenység állítja elő.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Helyszíni fájlrendszer kimeneti adatkészlete:**

Az adatokat óránként új fájlba másolja a rendszer. A blob folderPath és fileName fájlneve a szelet kezdési időpontja alapján kerül meghatározásra.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**SQL-forrással és fájlrendszer-fogadóval rendelkező folyamat másolási tevékenysége:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **sqlsource**lesz állítva, a **fogadó** típusa pedig **FileSystemSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolandó adatokat az elmúlt órában.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

A forrásadatkészletből oszlopokat is leképezhet a fogadó-adatkészletből a másolási tevékenységdefinícióban. További információt az [Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakörben talál.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
 Ha meg szeretné tudni, hogy milyen kulcsfontosságú tényezők befolyásolják az azure Data Factory adatmozgatásának (másolási tevékenység) teljesítményét, és különböző módokon optimalizálhatja azt, olvassa el a [Tevékenység másolása teljesítmény- és hangolási útmutatót.](data-factory-copy-activity-performance.md)
