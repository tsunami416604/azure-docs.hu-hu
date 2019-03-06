---
title: Adatok másolása és- tárolókról az Azure Data Factory használatával fájlrendszer |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat, és a egy helyi fájlrendszerből Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 40086924731876dc44d9651ca46814149dba52f0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432780"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Adatok másolása a és a egy helyi fájlrendszerből Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-onprem-file-system-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-file-system.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [fájlrendszer-összekötő a v2-ben](../connector-file-system.md).


Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával másolja ki az adatok egy helyszíni fájlrendszer. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **egy helyi fájlrendszerből** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **egy helyszíni fájlrendszerhez**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájl, miután sikerült a célhelyre másolja. Ha a forrásfájl törlése után a sikeres másolási van szüksége, hozzon létre egy egyéni tevékenységet, törölje a fájlt, és használja a tevékenységet a folyamat.

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory támogatja, és a egy helyi fájlrendszerből keresztül csatlakozó **adatkezelési átjáró**. Telepítenie kell az adatkezelési átjárót a Data Factory szolgáltatás minden támogatott helyszíni adattár, többek között a fájlrendszer csatlakozni a helyszíni környezetben. Az adatkezelési átjáró és az átjáró lépésenkénti kapcsolatban lásd: [adatok áthelyezése a felhőbe, az adatkezelési átjáróval és a egy helyszíni forrásra](data-factory-move-data-between-onprem-and-cloud.md). Az adatkezelési átjáró más bináris fájlokat is való kommunikációra, és a egy helyi fájlrendszerből telepíteni kell. Telepítse, és az adatkezelési átjárót használja, akkor is, ha a fájlrendszer az Azure IaaS virtuális Gépen. Az átjáró kapcsolatos részletes információkért lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md).

Linux fájlmegosztás használatához telepítse [Samba](https://www.samba.org/) a Linux-kiszolgálón, és telepítse az adatkezelési átjáró Windows-kiszolgálón. Az adatkezelési átjáró telepítése egy Linux-kiszolgálón nem támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok és-tárolókról a fájlrendszer áthelyezéséhez a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat.
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha másolt adatokat egy Azure blob storage-ból egy helyszíni fájlrendszerhez, létrehozhat két társított szolgáltatást a helyi fájlrendszer és az Azure storage-fiók összekapcsolása a data factory. Egy helyszíni fájlrendszerhez jellemző társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett megadja a blobtárolót és a bemeneti adatokat tartalmazó mappát egy adatkészletet hoz létre. És adja meg a mappát és fájlnevet a fájlrendszert (nem kötelező) egy másik adatkészletet hoz létre. A helyi fájlrendszer adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
4. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük BlobSource a forrás és a FileSystemSink fogadóként esetében használja a másolási tevékenység. Hasonlóképpen, a helyi fájlrendszerből másolása az Azure Blob Storage, FileSystemSource és a használata BlobSink a másolási tevékenység. Másolási tevékenység tulajdonságai a helyi fájlrendszerbe jellemző, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások másolhat adatokat egy fájlrendszer/használt JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-file-system) című szakaszát.

A következő szakaszok a fájlrendszerre adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletek:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az Azure data factory egy helyszíni fájlrendszer kapcsolat a **helyi fájlkiszolgáló** társított szolgáltatást. Az alábbi táblázat ismerteti az On-Premises File Server társított szolgáltatásának adott JSON-elemek.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |Győződjön meg arról, hogy a type tulajdonság értéke **OnPremisesFileServer**. |Igen |
| gazdagép |Megadja a gyökér elérési útja a másolni kívánt mappa. Használja az escape-karaktert "\" a speciális karakterek a karakterláncban. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. |Igen |
| felhasználói azonosító |Adja meg a felhasználó, aki hozzáfér a server azonosítója. |Nem (Ha úgy dönt, hogy encryptedCredential) |
| jelszó |Adja meg a jelszót a felhasználó (felhasználóazonosító). |Nem (Ha úgy dönt, hogy encryptedCredential |
| encryptedCredential |Adja meg a titkosított hitelesítő adatokat, amelyeket lekérhet a New-AzDataFactoryEncryptValue parancsmag futtatásával. |Nem (Ha úgy dönt, hogy adja meg a felhasználói azonosítót és jelszót a szövegként) |
| átjáró neve |Itt adhatja meg, amelyet a Data Factory a helyszíni fájl-kiszolgálóhoz való csatlakozáshoz használnia kell az átjárója nevére. |Igen |


### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és az adatkészlet-definíciók
| Forgatókönyv | A társított szolgáltatás definíciójában üzemeltetése | Az adatkészlet-definícióban folderPath |
| --- | --- | --- |
| Helyi mappa adatkezelési átjárót a gépen: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |D:\\ \\ (a Data Management Gateway 2.0-s és újabb verziók) <br/><br/> a localhost (a Data Management Gateway 2.0, mint a korábbi verziók) |. \\ \\ vagy mappa\\\\almappát (a Data Management Gateway 2.0-s és újabb verziók) <br/><br/>D:\\ \\ vagy D:\\\\mappa\\\\almappát (az átjáró 2.0-s verzió) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |\\\\\\\\myserver\\\\megosztása |. \\ \\ vagy mappa\\\\almappa |

>[!NOTE]
>Ha létrehozási felhasználói felületen keresztül, nem szükséges bemeneti dupla fordított perjelet (`\\`) karaktert, mint amikor JSON-n keresztül, adjon meg egy fordított perjel.

### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Egyszerű szöveges felhasználónév és jelszó használatával

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
Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében.

A typeProperties szakasz eltér az egyes adatkészlet. Biztosít információkat, például a hely és az adattárban lévő adatok formátumát. A typeProperties szakasz az adatkészlet típusa **FileShare** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| folderPath |Itt adhatja meg azt a mappát a adatútvonalának. Használja az escape-karaktert "\' a karakterláncban szereplő speciális karakterek. Helyettesítő karaktert tartalmazó szűrő nem támogatott. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat.<br/><br/>Ennek a tulajdonságnak kombinálhatja **partitionBy** szeretné, hogy a mappa elérési utak alapján szelet kezdő és záró dátum-idő. |Igen |
| fileName |Adja meg a fájl nevét a **folderPath** Ha azt szeretné, hogy a tábla egy adott fájlra a mappában. Ha nem ad meg semmilyen értéket ehhez a tulajdonsághoz, a tábla a mappában lévő összes fájlt mutat.<br/><br/>Amikor **fileName** nincs megadva a kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl neve a következő formátumban van: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nem |
| fileFilter |Adjon meg egy szűrőt használt összes fájlja helyett a folderPath lévő fájlok egy adott sorkészletét jelölik ki. <br/><br/>Engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "* .log"<br/>2. példa: "fileFilter": 2014-1-?.txt"<br/><br/>Vegye figyelembe, hogy fileFilter a fájlmegosztás a bemeneti adatkészletek vonatkozik. |Nem |
| partitionedBy |PartitionedBy segítségével adja meg a dinamikus folderPath/fileName idősorozat-adatok. Ilyen például az adatok óránkénti paraméteres folderPath. |Nem |
| Formátum | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [Json formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquetformátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszokat. <br><br> Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban. |Nem |
| A tömörítés | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők: **Optimális** és **leggyorsabb**. Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

> [!NOTE]
> Fájlnév és fileFilter egyidejűleg nem használható.

### <a name="using-partitionedby-property"></a>PartitionedBy tulajdonság használatával
Az előző szakaszban ismertetett módon, megadhat egy dinamikus folderPath és fájlnevét, idősorozat-adatokat a **partitionedBy** tulajdonság, [Data Factory-függvények, és a rendszerváltozók](data-factory-functions-variables.md).

Idősorozat-adatkészletek, ütemezési és szeletek kapcsolatos további információk megismeréséhez tekintse meg [adatkészletek létrehozása](data-factory-create-datasets.md), [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md), és [folyamatok létrehozása](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában {szelet} helyére a Data Factory rendszerváltozóhoz SliceStart formátumban (YYYYMMDDHH) értékét. Kezdő időpont a szelet SliceStart hivatkozik. A folderPath eltér az egyes szeletekhez. Például: wikidatagateway/wikisampledataout/2014100103 vagy wikidatagateway/wikisampledataout/2014100104.

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

Ebben a példában év, hónap, nap és SliceStart idején kicsomagolja a folderPath és a fileName tulajdonság használó külön változókba.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti adatkészleteket és szabályzatokat minden típusú tevékenységek érhetők el. Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ.

A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is. Ha adatokat helyez át egy helyszíni fájlrendszer, a forrás típusaként állítsa be a másolási tevékenység **FileSystemSource**. Hasonlóképpen, ha adatokat helyez át egy helyszíni fájlrendszerhez, állítsa be a fogadó típusa a másolási tevékenység **FileSystemSink**. Ez a szakasz a FileSystemSource és FileSystemSink által támogatott tulajdonságok listáját tartalmazza.

**FileSystemSource** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a rekurzív |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. |TRUE, False (alapértelmezett) |Nem |

**FileSystemSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| a copyBehavior |A másolási viselkedés határozza meg, ha a forrás BlobSource vagy fájlrendszer. |**PreserveHierarchy:** Megőrzi a hierarchiája a célmappában. Hogy a relatív elérési útját a forrásfájl, a megadott forrásmappához ugyanaz, mint a célmappában cél fájl relatív elérési útját.<br/><br/>**FlattenHierarchy:** Minden fájl a forrásmappából az első szintű célmappában jönnek létre. A cél fájlt hoz létre egy automatikusan létrehozott névvel.<br/><br/>**MergeFiles:** Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve és a blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>a rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet a rekurzív és a copyBehavior tulajdonság értékei különböző kombinációihoz.

| a rekurzív érték | a copyBehavior érték | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 szerkezete ugyanaz, mint a forrás hozzon létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlt egy automatikusan létrehozott fájl néven egyesítve. |
| false |preserveHierarchy |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 az alábbi struktúra használatával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |flattenHierarchy |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 az alábbi struktúra használatával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |
| false |mergeFiles |A forrásmappa mappa1 az alábbi struktúra használatával<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 az alábbi struktúra használatával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 tartalma egyesítve egy fájlt egy automatikusan létrehozott nevét.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem mértékének. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
Lásd: [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md) cikkben talál.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-példák az adatok másolása a fájlrendszer
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével hozzon létre egy folyamatot a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Ezek bemutatják, hogyan másolhat adatokat egy a helyszíni rendszer és az Azure Blob storage. Azonban adatokat másolja *közvetlenül* egyetlen forrás sem szerepel a fogadóként [forrásként és fogadóként támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) másolási tevékenységgel az Azure Data Factory használatával.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Példa: Az Azure Blob storage-adatokat másol egy helyszíni fájlrendszer
Ez a példa bemutatja, hogyan másolhat adatokat egy helyi fájlrendszerből az Azure Blob storage. A minta az alábbi Data Factory-entitások rendelkezik:

* A társított szolgáltatás típusa [OnPremisesFileServer](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [FileShare](#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Az alábbi minta idősorozat-adatokat másol egy helyszíni fájlrendszer az Azure Blob storage-óránként. Ezek a minták a használt JSON-tulajdonságokat a szakaszok ismertetik a minta után.

Első lépésként állítsa be az adatkezelési átjáró az utasításoknak [adatok áthelyezése a felhőbe, az adatkezelési átjáróval és a egy helyszíni forrásra](data-factory-move-data-between-onprem-and-cloud.md).

**A helyi fájlkiszolgáló-beli társított szolgáltatást:**

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

Javasoljuk, hogy használja a **encryptedCredential** tulajdonság inkább a **userid** és **jelszó** tulajdonságai. Lásd: [fájlkiszolgáló társított szolgáltatás](#linked-service-properties) részleteit, ez a társított szolgáltatás.

**Az Azure Storage társított szolgáltatást:**

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

**A helyszíni rendszer bemeneti adatkészlet fájlt:**

Adatok felülettől egy új fájlt minden órában. A fileName és a folderPath tulajdonság a szelet kezdő időpontja alapján vannak meghatározva.

Beállítás `"external": "true"` adat-előállító tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, az év, hónap, nap és a kezdési időpont órás részeit.

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

**Egy másolási tevékenységgel rendelkező fájlrendszer forrásaként és fogadó Blob folyamatot:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és az óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **FileSystemSource**, és **fogadó** típusa **BlobSink**.

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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Példa: Adatok másolása az Azure SQL Database-ből egy helyszíni fájlrendszerhez
Az alábbi mintában látható:

* A társított szolgáltatás típusa [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* A társított szolgáltatás típusa [OnPremisesFileServer](#linked-service-properties).
* Típusú bemeneti adatkészlet [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Egy kimeneti adatkészletet típusú [FileShare](#dataset-properties).
* Egy folyamatot egy másolási tevékenységgel, amely használja [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) és [FileSystemSink](#copy-activity-properties).

A minta idősorozat-adatokat másol egy Azure SQL-táblát egy helyszíni fájlrendszerhez óránként. A minta után szakaszokban ezeket a mintákat a használt JSON-tulajdonságokat ismerteti.

**Az Azure SQL Database-beli társított szolgáltatást:**

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

**A helyi fájlkiszolgáló-beli társított szolgáltatást:**

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

Javasoljuk, hogy használja a **encryptedCredential** tulajdonság használata helyett a **userid** és **jelszó** tulajdonságai. Lásd: [fájlrendszer társított szolgáltatás](#linked-service-properties) részleteit, ez a társított szolgáltatás.

**Az Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy táblát "MyTable" az Azure SQL-ben, és a egy idősorozat-adatok "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás ``“external”: ”true”`` adat-előállító tájékoztatja, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

**A helyszíni rendszer kimeneti adatkészlet fájlt:**

Adatokat egy új fájlba másolja óránként. A folderPath és a blob nevét határozza meg a szelet kezdő időpontja alapján.

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

**Az SQL-forrás és fogadó fájlrendszer egy folyamatot egy másolási tevékenységgel:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és az óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlSource**, és a **fogadó** típusa **FileSystemSink**. Az SQL-lekérdezést, amely meg van adva a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

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

A másolási tevékenységhez tartozó definíció a fogadó-adatkészlet-oszlop a forrásadatkészlet oszlopok is leképezheti. További információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
 Adatáthelyezés (másolási tevékenység) az Azure Data Factory és optimalizálhatja azt hibakeresésének módjai teljesítményét befolyásoló legfontosabb tényezők kapcsolatos további információkért tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md).
