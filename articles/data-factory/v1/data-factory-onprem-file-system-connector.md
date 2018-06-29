---
title: Adatok másolása az Azure Data Factory használatával fájlrendszer |} Microsoft Docs
description: Ismerje meg az adatok másolása és egy helyszíni fájlrendszer az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ab38c689cb6445bc85a942fc350c2a1f5de7912
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047043"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Adatok másolása és egy helyszíni fájlrendszer az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-onprem-file-system-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-file-system.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [fájlrendszer-összekötőt, a V2](../connector-file-system.md).


Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory belőle egy helyszíni fájlrendszer adatok másolása. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **egy helyi fájl rendszerből** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **egy helyszíni fájlrendszerhez**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Másolási tevékenység nem törli a forrásfájl, miután sikerült átmásolni a cél. Ha a forrásfájl törlése után sikeres másolatot van szüksége, létrehozhat egy egyéni törölje a fájlt, és az adatcsatorna használja a tevékenységet. 

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
Adat-előállító támogatja a csatlakozást egy helyszíni fájlrendszer keresztül érkező vagy oda irányuló **az adatkezelési átjáró**. A helyszíni környezetben a Data Factory szolgáltatásnak bármely támogatott helyszíni adattár, beleértve a fájlrendszer való kapcsolódáshoz telepítenie kell az adatkezelési átjáró. Az adatkezelési átjáró és az átjáró beállításának lépéseit, lásd: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró a felhő közötti](data-factory-move-data-between-onprem-and-cloud.md). Az adatkezelési átjáró kívül más bináris fájlokat telepítve kell lennie egy helyszíni fájlrendszer érkező vagy oda irányuló kommunikációra. Telepítse, és az adatkezelési átjáró használja, akkor is, ha a fájlrendszer Azure IaaS virtuális gépen. Az átjáró kapcsolatos részletes információkért lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md).

Linux fájlmegosztás használatához telepítenie [Samba](https://www.samba.org/) a Linux-kiszolgálón, telepítse az adatkezelési átjáró a Windows server. Az adatkezelési átjáró telepítése egy Linux-kiszolgálón nem támogatott.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat, vagy az operációs rendszer különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például ha a másolt adatok az az Azure blob storage egy helyszíni fájlrendszerhez, hoz létre a helyszíni fájlrendszerben és az Azure storage-fiók összekapcsolása a data factory két társított szolgáltatások. Egy helyszíni fájlrendszer jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.
3. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példában az előző lépésben említett hozzon létre egy adatkészlet adja meg a blob-tároló és a bemeneti adatokat tartalmazó mappát. És hoz létre egy másik dataset adja meg a mappát és a fájl nevét a fájlrendszert (nem kötelező). A helyszíni fájlrendszer adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában BlobSource forrás-és FileSystemSink akár használhatja a fogadó a másolási tevékenységhez. Hasonlóképpen a helyszíni fájlrendszer az Azure Blob Storage másolása, használható FileSystemSource és BlobSink a másolási tevékenység. A másolási tevékenység tulajdonságai a helyszíni fájlrendszer jellemző, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Másolja az adatokat, az operációs rendszer használt adat-előállító entitások JSON-definíciók minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-file-system) című szakaszát.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások fájlrendszerre JSON-tulajdonságok:

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
Egy helyszíni fájlrendszer hozzákapcsolhatja egy az Azure data factory, a **a helyi fájlkiszolgáló** társított szolgáltatás. A következő táblázat ismerteti, amelyek a helyszíni fájl kiszolgálóhoz kapcsolódó szolgáltatásra vonatkozó JSON-elemek szerepelnek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a type tulajdonság értéke **OnPremisesFileServer**. |Igen |
| gazdagép |Adja meg a legfelső szintű mappa elérési útját, amelyet szeretne másolni. Az escape-karakter használata "\" a speciális karakterek a karakterláncban. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. |Igen |
| felhasználói azonosítóját |Adja meg a felhasználó, aki hozzáfér a kiszolgáló Azonosítóját. |Nem (Ha úgy dönt, hogy encryptedCredential) |
| jelszó |Adja meg a felhasználó (userid) jelszavát. |Nem (Ha úgy dönt, hogy encryptedCredential |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat kaphat a New-AzureRmDataFactoryEncryptValue parancsmag futtatásával. |Nem (Ha úgy dönt, hogy adja meg a felhasználói azonosítót és jelszót a szövegként) |
| gatewayName |Megadja a Data Factory kell csatlakozni a helyi fájlkiszolgálón használó átjáró nevét. |Igen |


### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és a dataset definíciók
| Forgatókönyv | A társított szolgáltatás definíciójának üzemeltetéséhez | Az adatkészlet-definícióban folderPath |
| --- | --- | --- |
| Az adatkezelési átjáró gépen helyi mappában: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |D:\\ \\ (az adatok felügyeleti átjáró 2.0-s és újabb verziók) <br/><br/> a localhost (korábbi verzióihoz mint adatok felügyeleti átjáró 2.0-s) |. \\ \\ vagy mappa\\\\almappa (az adatok felügyeleti átjáró 2.0-s és újabb verziók) <br/><br/>D:\\ \\ vagy D:\\\\mappa\\\\almappa (az átjáró verziója alatt 2.0-s) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |\\\\\\\\myserver\\\\megosztása |. \\ \\ vagy mappa\\\\almappa |

>[!NOTE]
>Szerzői felhasználói felületén keresztül, ha nem kell a felhasználótól, dupla fordított perjel (`\\`) karaktert, mint amikor JSON keresztül, adjon meg egy fordított perjel.

### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használatával egyszerű szöveges

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

### <a name="example-using-encryptedcredential"></a>Példa: Encryptedcredential használatával

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
Illetve meghatározásához adatkészletek rendelkezésre álló tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében.

A typeProperties szakaszban nem egyezik az adatkészlet egyes típusú. Például a hely és az adattár adatok formátuma adatokat tartalmazza. A typeProperties szakasz az adatkészlet típusú **fájlmegosztási** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Adja meg a részleges elérési útja a mappához. Az escape-karakter használata "\' speciális karakterek a karakterláncban. Helyettesítő karakter szűrő nem támogatott. Lásd: [minta kapcsolódó szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ez a tulajdonság a kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg ehhez a tulajdonsághoz értéket, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Ha **Fájlnév** nincs megadva egy kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl neve nem a következő formátumban: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt, amely minden fájl helyett a fájlok Tárolónév részhalmazának kiválasztására szolgál. <br/><br/>Két érték engedélyezett: `*` (több karaktert) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "* .log"<br/>2. példa: "fileFilter": 2014 - 1-?. txt"<br/><br/>Vegye figyelembe, hogy fileFilter egy bemeneti fájlmegosztási adatkészlet esetében alkalmazható. |Nem |
| partitionedBy |PartitionedBy segítségével adjon meg egy dinamikus folderPath/fájlnevet idősorozat adatok. Példa: az adatok óránkénti paraméteres folderPath. |Nem |
| Formátumban | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Állítsa be a **típus** tulajdonság a formátuma a következő értékek egyikét. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok. <br><br> Ha azt szeretné, hogy **másolja a fájlokat-van** közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a Formátum szakasz mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| tömörítés | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők: **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimal** és **leggyorsabb**. Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> Nem használható egyszerre fájlnév és fileFilter.

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használatával
Az előző szakaszban említett, megadhat egy dinamikus folderPath és a fájlnév idő adatsorozat adatokhoz a **partitionedBy** tulajdonság, [adat-előállító funkciók és a rendszer változók](data-factory-functions-variables.md).

További részleteket a idősorozat adatkészleteket, az ütemezés és a szeletek ismertetése: [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában {szelet} váltja fel a Data Factory rendszerváltozó SliceStart formátumban (YYYYMMDDHH) értékét. A szelet kezdete SliceStart hivatkozik. A folderPath nem azonos az egyes szeletek. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. példa:

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

Ebben a példában év, hónap, nap és SliceStart idején ki kell olvasni a külön változókat, amelyek a fájlnév és a folderPath tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti adatkészletek és házirendek érhetők el. Mivel a tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység tevékenységek minden típusának függenek.

A másolási tevékenység során két érték források és mosdók típusától függően. Egy helyszíni fájlrendszerből helyez át adatokat, ha a forrás típusa beállítása a másolási tevékenység **FileSystemSource**. Hasonlóképpen, ha egy helyszíni fájlrendszerhez helyez át adatokat, beállítása a fogadó típusa a másolási tevékenység **FileSystemSink**. Ez a témakör FileSystemSource és FileSystemSink által támogatott tulajdonságokról.

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, illetve csak a megadott mappát. |IGAZ, hamis (alapértelmezés) |Nem |

**FileSystemSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| copyBehavior |Másolás viselkedését határozza meg, ha az adatforrás BlobSource vagy a fájlrendszer. |**PreserveHierarchy:** őrzi meg a fájl hierarchia a célmappában. Ez azt jelenti, hogy a forrásfájl, a forrásmappához relatív elérési út ugyanaz, mint a relatív a cél elérési útja a célként megadott mappába.<br/><br/>**FlattenHierarchy:** minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/>**Mergefiles típusú:** egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl neve/blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben egy automatikusan létrehozott nevét. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet kombinációk a rekurzív és a copyBehavior tulajdonság értéktartománya.

| rekurzív érték | copyBehavior érték | Viselkedésről |
| --- | --- | --- |
| true |preserveHierarchy |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A célmappa mappa1 forrásaként azonos struktúrájú jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlba, egy fájl automatikusan létrehozott névvel egyesítve lesznek. |
| false |preserveHierarchy |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A tároló mappa mappa1 jön létre az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |
| false |flattenHierarchy |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A tároló mappa mappa1 jön létre az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |
| false |mergeFiles |A forrásmappa mappa1 az alábbi struktúrával,<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A tároló mappa mappa1 jön létre az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítődnek fájl automatikusan létrehozott névvel egy fájlba.<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem felvételre. |

## <a name="supported-file-and-compression-formats"></a>Támogatott formátumú és tömörítés
Lásd: [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikk részletei.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Az adatok másolása a JSON-példák fájlrendszer
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot a [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása egy a helyszíni fájlrendszerben és az Azure Blob Storage tárolóban mutatnak. Adatok másolása azonban *közvetlenül* bármelyik sem a felsorolt nyelő források [támogatott források és mosdók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenység az Azure Data Factory használatával.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Példa: Adatok másolása az egy helyszíni fájlrendszer az Azure Blob-tároló
Ez a példa bemutatja, hogyan egy helyszíni fájlrendszer adatok másolása az Azure Blob storage. A minta a következő adat-előállító entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesFileServer](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [fájlmegosztási](#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Az alábbi minta másol idősorozat adatokat egy helyszíni fájlrendszer az Azure Blob storage óránként. A JSON-tulajdonságok ezeket a mintákat a használt a szakaszok ismertetik a minta után.

Első lépésként állítsa be az adatkezelési átjáró szerint utasításait [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró a felhő közötti](data-factory-move-data-between-onprem-and-cloud.md).

**A helyi fájlkiszolgáló társított szolgáltatáshoz:**

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

Azt javasoljuk, a **encryptedCredential** tulajdonság inkább a **userid** és **jelszó** tulajdonságait. Lásd: [fájlkiszolgáló társított szolgáltatás](#linked-service-properties) részleteiről a társított szolgáltatás.

**Az Azure tárolás társított szolgáltatásának:**

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

**A helyi rendszer bemeneti adatkészlet fájl:**

Adatok van felvett egy új fájl óránként. A fájlnév és a folderPath tulajdonság alapján a szelet kezdési idejét határozza meg.  

Beállítás `"external": "true"` adat-előállító tájékoztatja, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

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

**Az Azure Blob storage kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában részeit.

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

**A másolási tevékenység során a fájlrendszer és a Blob fogadó folyamat:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **FileSystemSource**, és **fogadó** típusúra **BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Példa: Adatok másolása az Azure SQL-adatbázis egy helyszíni fájlrendszerhez
A következő példában:

* A társított szolgáltatás típusa [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* A társított szolgáltatás típusa [OnPremisesFileServer](#linked-service-properties).
* Egy bemeneti adatkészlet típusú [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Egy kimeneti adatkészlet típusú [fájlmegosztási](#dataset-properties).
* A másolási tevékenység által használt az adatcsatorna [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) és [FileSystemSink](#copy-activity-properties).

A minta-idősoros adatok egy Azure SQL tábla másolja egy helyi fájlrendszer minden órában. A JSON-tulajdonságok ezeket a mintákat a használt szakaszok ismertetik a minta után.

**A társított szolgáltatásnak Azure SQL Database:**

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

**A helyi fájlkiszolgáló társított szolgáltatáshoz:**

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

Azt javasoljuk, a **encryptedCredential** tulajdonság használata helyett a **userid** és **jelszó** tulajdonságait. Lásd: [fájlrendszer társított szolgáltatás](#linked-service-properties) részleteiről a társított szolgáltatás.

**Az Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy tábla "MyTable" Azure SQL-ben, és egy idősorozat adatok "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás ``“external”: ”true”`` adat-előállító tájékoztatja, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

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

**A helyszíni rendszer kimeneti adatkészlet fájl:**

Adatokat egy új fájlt másolja minden órában. A folderPath és a blob fájlnevét határozza meg a kezdési időt a szelet alapján.

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

**A másolási tevékenység során egy SQL-forrás és fogadó fájlrendszer folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlSource**, és a **fogadó** típusúra **FileSystemSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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


A másolási tevékenység definíciójának fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat is leképezheti. További információkért lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
 Az adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálása azt a teljesítményt befolyásoló legfontosabb tényezők kapcsolatos további tudnivalókért lásd: a [másolási tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md).
