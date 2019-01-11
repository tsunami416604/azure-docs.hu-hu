---
title: ETL-helyett tervezése az Azure SQL Data Warehouse ELT |} A Microsoft Docs
description: ETL-helyett betöltésére, adatok vagy az Azure SQL Data Warehouse egy kinyerési, betöltési és átalakítási (ELT) folyamat tervezése.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8407fcdabecbb4f6ed9c0028a4a74916913591ba
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199187"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse PolyBase-adatbetöltés stratégia tervezése

Hagyományos SMP-adattárházak egy kinyerési, átalakítási és betöltési (ETL) folyamat használata az adatok betöltéséhez. Az Azure SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely a előnyeit, a méretezhetőséget és rugalmasságot biztosít a számítási és tárolási erőforrások. Egy kinyerési, betöltési és átalakítási (ELT) folyamat használatával MPP előnyeit, és megszüntetheti a betöltés előtt az adatok átalakításához szükséges erőforrásokat. Az SQL Data Warehouse a Polybase beállítások, például a BCP és az SQL API-BulkCopy beleértve számos betöltése módszereket támogatja a, míg a leggyorsabb és leginkább méretezhető dátum betöltése módja a PolyBase segítségével.  PolyBase technológiája, amely hozzáfér a T-SQL nyelv használatával az Azure Blob storage vagy az Azure Data Lake Store tárolt külső adatokat.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Mi az ELT?

Kinyerési, betöltési, és átalakítási (ELT) egy olyan folyamat, amellyel adatokat a forrásrendszerben kinyert, egy data warehouse-bA betöltött és majd alakította át. 

Az alapvető lépéseken, egy SQL Data warehouse PolyBase ELT végrehajtásához a következők:

1. A forrásadatok kinyerése szövegfájlokba.
2. Megnyitja az adatok Azure Blob storage vagy az Azure Data Lake Store-bA.
3. Készítse elő az adatok betöltésekor.
4. Az adatok betöltéséhez az előkészítési táblák a PolyBase az SQL Data Warehouse-bA. 
5. Az adatok átalakításához.
6. Az adatok beszúrása a termelési táblákba.


Betöltési oktatóanyag: lásd: [bA a PolyBase használatával az adatok betöltése az Azure blob storage-ból az Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

További információkért lásd: [blog. mintázatok betöltése](https://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. A forrásadatok kinyerése szövegfájlokba

A forrásrendszerben adatokat első attól függ, hogy a tárolási helyét.  A cél, hogy az adatokat a PolyBase támogatott elválasztójellel tagolt szöveges fájlok. 

### <a name="polybase-external-file-formats"></a>A PolyBase külső fájlformátum

A PolyBase adatokat tölt be az UTF-8 és UTF-16 kódolású, elválasztójellel tagolt szöveges fájlok. A tagolt szöveges fájlok mellett betölti a Hadoop-fájl formátumok RC fájlt, a ORC és a parquet eszközökben. A PolyBase Gzip és Snappy tömörített fájlokból is betöltheti adatokat. A PolyBase jelenleg nem támogatja a kiterjesztett ASCII, formátum rögzített szélességű és beágyazott formátumban például WinZip, JSON- és XML. Ha az SQL Serverből exportálja, [bcp parancssori eszköz](/sql/tools/bcp-utility) exportálja az adatokat, elválasztójellel tagolt szöveges fájlok.


## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Megnyitja az adatok Azure Blob storage vagy az Azure Data Lake Store-bA

Az adatok kerül az Azure storage-ban, áthelyezheti azt [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md) vagy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Az egyik helyen az adatok szöveges fájlok kell tárolni. A PolyBase betöltheti bármely helyről.

Eszközök és adatok áthelyezése az Azure Storage segítségével szolgáltatásokat:

- [Az Azure ExpressRoute](../expressroute/expressroute-introduction.md) szolgáltatás fokozza a hálózat átviteli sebességét, teljesítmény- és kiszámíthatóságot nyújtanak. Az ExpressRoute egy szolgáltatása, amely az adatok egy dedikált privát kapcsolaton keresztül irányítja az Azure-bA. Az ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül adatokat irányítják. A kapcsolatok több megbízhatóság megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál kínálnak a nyilvános interneten keresztül.
- [AZCopy segédprogram](../storage/common/storage-moving-data.md) helyez át adatokat az Azure Storage a nyilvános interneten keresztül. Ez működik, ha az adatok mérete kisebb, mint 10 TB-ot. Végre betölti az Azcopyval rendszeres időközönként, tesztelje a hálózat sebességétől, láthatja, hogy e elfogadható. 
- [Az Azure Data Factory (ADF)](../data-factory/introduction.md) rendelkezik átjáróval, amely a helyi kiszolgáló is telepíthető. Ezután létrehozhat egy folyamatot az adatok áthelyezése az Azure Storage a helyi kiszolgáló. Tekintse meg a Data Factory-val való használatához az SQL Data Warehouse [adatok betöltése az SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Az adatok előkészítése az betöltése

Szüksége lehet az adatok a storage-fiókban előkészítése és az SQL Data Warehouse-be való betöltés előtt. Adat-előkészítés közben az adatokat a forrás, a szövegfájlok exportálja az adatokat, vagy után az adatok Azure Storage-ban hajtható végre.  Az adatok a folyamat a lehető leghamarabb a legegyszerűbb.  

### <a name="define-external-tables"></a>Külső táblák megadása

Adatok betöltése előtt kell külső táblát határoz meg a data warehouse-ban. A polybase külső táblák és az Azure Storage-adatokkal. Egy külső táblát egy adatbázis nézet hasonlít. A külső tábla tartalmazza a következő tábla sémáját, és az adatraktár kívül tárolt adatokat mutat. 

Külső táblák meghatározása magában foglalja az adatforrást, a szövegfájlok, és a tábladefiníciókat megadása. A T-SQL szintaxist témakörök, amelyre szüksége lesz a következők:
- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql)

Például egy külső-objektumok létrehozása, tekintse meg a [külső táblák létrehozása](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) lépés használata a betöltési oktatóanyag.

### <a name="format-text-files"></a>Szövegfájlok formátuma

Miután a külső objektumok meg vannak adva, kell igazítása a szöveges fájlokat, a külső tábla-és fájlformátum-definíció sorait. A szöveges fájl minden sorában az adatok tábladefiníciója kell igazodva.
A szöveg fájlok:

- Ha az adatok nem relációs forrásból érkezik, akkor átalakítja a nyersanyagokat sorok és oszlopok. Az adatokat relációs és nem relációs forrásból származik-e, hogy az adatokat, amelyek a tábla, amelybe át kívánja betölteni az adatokat az oszlopdefiníciók összhangban vannak lesz átalakítva. 
- Formázza az adatokat a szövegfájlban való megfelelés érdekében az SQL Data Warehouse céltábla oszlopok és adattípusukkal együtt. Adattípusok a külső szöveges fájlok és a data warehouse-tábla között illesztését hatására a rendszer elutasítja a betöltés során sort.
- A szövegfájlban egy lezáró jellel ellátott külön mezőkben.  Ügyeljen arra, használja a karakter, vagy egy karaktersorozat, amely a forrásadatok nem található. Használja a megadott lezáró [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Az adatok betöltéséhez az előkészítési táblák a PolyBase az SQL Data Warehouse-bA

Tanácsos betölteni az adatokat egy előkészítési táblába. Előkészítési táblák hibáinak kezelése a termelési táblákba zavarása nélkül teszi lehetővé. Egy előkészítési táblába is lehetővé teszi, hogy az SQL Data Warehouse MPP használandó adatátalakítások előtt az adatok beszúrása a termelési táblákba.

### <a name="options-for-loading-with-polybase"></a>Beállítások betöltése a polybase használatával

Adatok betöltése a PolyBase, ezek a betöltési beállítások bármelyikét használhatja:

- [A PolyBase T-SQL](load-data-from-azure-blob-storage-using-polybase.md) jól működik, ha az adatait az Azure Blob storage vagy az Azure Data Lake Store van. A legtöbb szabályozhatja a betöltési folyamat biztosítja, de is kell megadni a külső adatok objektumokat. A többi módszer meghatározása a ezeket az objektumokat a háttérben, leképezheti a forrástábla céltáblákba.  A T-SQL-terhelések vezényli, használhatja az Azure Data Factory, az SSIS vagy az Azure functions. 
- [PolyBase az SSIS használatával](/sql/integration-services/load-data-to-sql-data-warehouse) jól működik, ha a forrásadatok van az SQL Server, SQL Server a helyszínen vagy a felhőben. SSIS határozza meg a forrás, cél táblaleképezések, és a terhelés is koordinálja. Ha már rendelkezik SSIS-csomagok, módosíthatja a csomagokat az új data warehouse cél dolgozhat. 
- [PolyBase az Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) egy másik üzletifolyamat-tervező eszköz.  Azt határozza meg a folyamatot, és ütemezi a feladatok. 
- [Az Azure DataBricks PolyBase](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) átviszi az adatokat egy SQL Data Warehouse-táblából Databricks dataframe és/vagy írja az adatokat egy Databricks-adathalmaz egy SQL Data Warehouse-tábla.

### <a name="non-polybase-loading-options"></a>A PolyBase nem betöltési beállítások

Ha az adatok nem kompatibilis a polybase használatával, használhatja [bcp](/sql/tools/bcp-utility) vagy a [kapcsolatot az SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP közvetlenül az SQL Data Warehouse az Azure Blob storage áthaladás nélkül betölt, és csak kis terhelésű szól. Vegye figyelembe, hogy ezek a beállítások az terhelés teljesítménye jelentősen lassabb, mint a PolyBase. 


## <a name="5-transform-the-data"></a>5. Az adatok átalakítása

Bár az előkészítési táblában lévő adatokat, átalakításokat, amely a számítási feladat igényli. Ezután helyezze át az adatokat egy éles táblába.


## <a name="6-insert-the-data-into-production-tables"></a>6. Adatok beszúrása a termelési táblákba

AZOKAT az INSERT... SELECT utasítás áthelyezi az adatokat az átmeneti tárolási tábla a végleges táblát. 

Egy ETL-folyamattal kialakításakor, próbálja meg a folyamat futtatása egy rövid tesztet mintán. Próbálja ki 1000 sor beolvasása a táblából egy fájlba, helyezze át az Azure-ba, és próbálkozzon a betöltené őket az előkészítési táblába. 


## <a name="partner-loading-solutions"></a>Partnermegoldások betöltése

Partnereink többsége rendelkezik megoldások betöltése. További információért lásd: listáját a [megoldáspartnerek](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>További lépések

További útmutatás a betöltéshez: [adatok betöltése útmutató](guidance-for-loading-data.md).


