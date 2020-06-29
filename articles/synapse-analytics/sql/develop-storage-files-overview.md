---
title: A Storage-ban található fájlok elérése az SQL on-demand (előzetes verzió) használatával a szinapszis SQL-ben
description: Leírja a tárolási fájlok lekérdezését az SQL on-demand (előzetes verzió) típusú, a szinapszis SQLon belüli erőforrásainak használatával.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4b6331977cc2237801b84647e4edeb5d789cb9e8
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482462"
---
# <a name="accessing-external-storage-in-synapse-sql"></a>Külső tároló elérése a szinapszis SQL-ben

Ez a dokumentum azt ismerteti, hogyan olvasható be a felhasználó az Azure Storage-ban tárolt fájlokból származó adatokból a szinapszis SQL-ben (igény szerinti és készlet). A felhasználók a következő beállításokkal férhetnek hozzá a tárolóhoz:

- [OpenRowset](develop-openrowset.md) függvény, amely alkalmi lekérdezéseket tesz lehetővé az Azure Storage-ban található fájlokon.
- Külső [tábla](develop-tables-external-tables.md) , amely a külső fájlok készletére épülő, előre definiált adatstruktúra.

A felhasználó [különböző hitelesítési módszereket](develop-storage-files-storage-access-control.md) használhat, például az Azure ad áteresztő hitelesítést (az alapértelmezett Azure ad-rendszerbiztonsági tag esetében) és az SAS-hitelesítést (az SQL-rendszerbiztonsági tag esetében alapértelmezés szerint).

## <a name="openrowset"></a>OPENROWSET

A [OpenRowset](develop-openrowset.md) függvény lehetővé teszi a felhasználó számára, hogy beolvassa a fájlokat az Azure Storage-ból.

### <a name="query-files-using-openrowset"></a>Fájlok lekérdezése a OPENROWSET használatával

A OPENROWSET lehetővé teszi a felhasználók számára az Azure Storage-beli külső fájlok lekérdezését, ha azok hozzáférnek a tárolóhoz. A szinapszis SQL igény szerinti végponthoz csatlakozó felhasználónak a következő lekérdezéssel kell elolvasnia az Azure Storage-ban tárolt fájlok tartalmát:

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

A felhasználók a következő hozzáférési szabályok használatával férhetnek hozzá a tárolóhoz:

- Az Azure AD User-OPENROWSET a hívó Azure AD-identitását fogja használni az Azure Storage vagy a hozzáférés-tárolók névtelen hozzáféréssel való eléréséhez.
- SQL-felhasználó – a OPENROWSET névtelen hozzáféréssel fogja elérni a tárolót.

Az SQL-rendszerbiztonsági tag a OPENROWSET használatával közvetlenül is lekérdezheti az SAS-tokenekkel vagy a munkaterület felügyelt identitásával védett fájlokat. Ha egy SQL-felhasználó végrehajtja ezt a függvényt, a HITELESÍTő adatok megváltoztatására jogosult felhasználónak létre kell hoznia egy kiszolgáló-hatókörű hitelesítő adatot, amely megfelel a függvény URL-címének (a tároló neve és tárolója alapján), és a hitelesítő adatokra vonatkozó jogosultságot kapott a OPENROWSET függvény hívója számára:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

Ha nincs olyan kiszolgálói szintű HITELESÍTő adat, amely megfelel az URL-címnek vagy az SQL-felhasználónak nincs hivatkozása erre a hitelesítő adatra, a rendszer a hibaüzenetet adja vissza. Az SQL-rendszerbiztonsági tag nem tud megszemélyesíteni néhány Azure AD-identitást.

> [!NOTE]
> A OPENROWSET ezen verziója az alapértelmezett hitelesítéssel történő gyors és egyszerű adatelemzéshez készült. A megszemélyesítés vagy a felügyelt identitás kihasználása érdekében használja a következő szakaszban ismertetett adatforrással rendelkező OPENROWSET.

### <a name="querying-data-sources-using-openrowset"></a>Adatforrások lekérdezése az OPENROWSET használatával

A OPENROWSET lehetővé teszi a felhasználó számára, hogy lekérdezze a külső adatforráson elhelyezett fájlokat:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.csv',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'csv') as rows
```

Az adatbázis-VEZÉRLÉSi engedéllyel rendelkező felhasználónak létre kell hoznia egy adatbázis-HATÓKÖRű HITELESÍTő adatot, amelyet a rendszer a tároló és a külső ADATFORRÁS eléréséhez fog használni, amely megadja az adatforrás és a hitelesítő adatok URL-címét:

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

Az ADATBÁZIShoz kötődő HITELESÍTő adatok a hivatkozott adatforráson (jelenleg SAS és felügyelt identitás) lévő fájlok elérését határozzák meg.

A hívónak a következő engedélyek egyikével kell rendelkeznie a OPENROWSET függvény végrehajtásához:

- A OPENROWSET végrehajtásának egyik engedélye:
  - A TÖMEGES művelet felügyelete lehetővé teszi a bejelentkezést a OPENROWSET függvény végrehajtásához.
  - Az adatbázis TÖMEGES MŰVELETének felügyelete lehetővé teszi, hogy az adatbázis-hatókörű felhasználó végrehajtsa a OPENROWSET függvényt.
- HIVATKOZik az adatbázis HATÓKÖRön belüli HITELESÍTő ADATAIra a külső adatforrásban hivatkozott hitelesítő adathoz.

#### <a name="accessing-anonymous-data-sources"></a>Névtelen adatforrások elérése

A felhasználó létrehozhat külső adatforrást olyan HITELESÍTő adatok nélkül, amely a nyilvános elérésű tárolóra hivatkozik, vagy az Azure AD átadó hitelesítést használja:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>KÜLSŐ TÁBLA

A táblázat olvasására jogosult felhasználók külső fájlokat is elérhet az Azure Storage-mappák és-fájlok készletén létrehozott külső tábla használatával.

A [külső tábla létrehozásához szükséges engedélyekkel](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) rendelkező felhasználó (például CREATE TABLE és a hitelesítő adatok vagy az adatbázis HATÓKÖRű HITELESÍTŐ adatainak módosítása) a következő parancsfájllal hozhat létre táblázatot az Azure Storage-adatforráson:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

A CONTROL DATABASE engedéllyel rendelkező felhasználónak létre kell hoznia egy adatbázis-HATÓKÖRű HITELESÍTő adatot, amelyet a rendszer a tároló és a külső ADATFORRÁS eléréséhez fog használni, amely megadja az adatforrás és a hitelesítő adatok URL-címét:

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

Az adatbázis-HATÓKÖRrel rendelkező hitelesítő adatok a hivatkozott adatforráson lévő fájlok elérését határozzák meg.

### <a name="reading-external-files-with-external-table"></a>Külső fájlok olvasása külső TÁBLÁZATtal

A külső tábla lehetővé teszi az adatok olvasását az adatforráson keresztül hivatkozott fájlokból a szabványos SQL SELECT utasítás használatával:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

A hívónak az alábbi engedélyekkel kell rendelkeznie az információk olvasásához:
- Engedély kiválasztása külső táblában
- Az adatbázis HATÓKÖRén belüli HITELESÍTő adatokra HIVATKOZik, ha az adatforrás HITELESÍTő adatokkal rendelkezik

## <a name="permissions"></a>Engedélyek

A következő táblázat a fent felsorolt műveletekhez szükséges engedélyeket sorolja fel.

| Lekérdezés | Szükséges engedélyek|
| --- | --- |
| OPENROWSET (BULK) adatforrás nélkül | A TÖMEGES rendszergazdai SQL-bejelentkezés FELÜGYELETéhez HIVATKOZÁSokkal rendelkező HITELESÍTő adatok szükségesek:: \<URL> sas által védett tároló esetén |
| OPENROWSET (TÖMEGES) adatforrással hitelesítő adatok nélkül | TÖMEGES RENDSZERGAZDA FELÜGYELETE |
| OPENROWSET (TÖMEGES) adatforrással, hitelesítő adatokkal | A TÖMEGES RENDSZERGAZDAI HIVATKOZÁS ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADATAINAK KEZELÉSE |
| KÜLSŐ ADATFORRÁS LÉTREHOZÁSA | AZ ADATBÁZIS-HATÓKÖRÖN BELÜLI HITELESÍTŐ ADATOKRA HIVATKOZÓ KÜLSŐ ADATFORRÁS MÓDOSÍTÁSA |
| KÜLSŐ TÁBLA LÉTREHOZÁSA | CREATE TABLE, BÁRMILYEN SÉMA MÓDOSÍTÁSA, BÁRMILYEN KÜLSŐ FÁJLFORMÁTUM MÓDOSÍTÁSA, BÁRMILYEN KÜLSŐ ADATFORRÁS MÓDOSÍTÁSA |
| KIVÁLASZTÁS KÜLSŐ TÁBLÁBÓL | TÁBLA KIVÁLASZTÁSA |
| CETAS | Táblázat létrehozásához – CREATE TABLE bármely séma módosítása bármilyen adatforrásra + bármilyen külső FÁJLFORMÁTUMra módosítható. Adatok beolvasása: rendszergazdai TÖMEGES műveletek + REFERENCIÁk a hitelesítő adatokhoz, vagy a lekérdezés + R/W engedéllyel rendelkező tábla/nézet/függvény tábla kijelölése a tároláshoz |

## <a name="next-steps"></a>További lépések

Most már készen áll a folytatásra a következő cikkekkel:

- [Adatlekérdezés a Storage szolgáltatásban](query-data-storage.md)

- [CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [Beágyazott típusok lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)
