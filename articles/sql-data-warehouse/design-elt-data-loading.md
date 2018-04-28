---
title: ETL, helyett Azure SQL Data Warehouse ELT kialakítása |} Microsoft Docs
description: Helyett ETL alakítson ki az adatok vagy az Azure SQL Data Warehouse feltöltését egy kivonatot, betöltés és átalakítás (ELT) folyamatot.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5ceb8cfd8efea66dbf17b8c522316b9a010e437d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Kinyerési, betöltés és átalakítás (ELT) az Azure SQL Data Warehouse tervezése

Helyett a kinyerési, átalakítási és betöltési (ETL) tervezze meg az adatok betöltése az Azure SQL Data Warehouse egy kivonatot, betöltés és átalakítás (ELT) folyamat. Ez a cikk be módjai alakítson ki egy ELT folyamatot, amely az Azure data warehouse-bA helyezi át az adatokat.

## <a name="what-is-elt"></a>Mi az az ELT?

Átalakítás (ELT) extract, Load, és egy folyamatot, amely az adatok áthelyezése egy forrásrendszerből a rendeltetési adatraktárra. Ez a folyamat történik rendszeresen, például óránként vagy naponta, lekérni az újonnan létrehozott adat az adatraktárba. Az ideális forrásból származó adatok eléréséhez adatraktár módja az adatok betöltése az SQL Data Warehouse PolyBase használó ELT folyamatai fejlesztéséhez.

ELT tölt be először, és az adatokat, majd átalakítások, mivel a kinyerési, átalakítási és betöltési (ETL) átalakítja az adatok betöltése az előtt. Az adatok átalakítására, mielőtt be van töltve a saját erőforrások rendelkezésre bocsátása költsége helyett ETL ELT végrehajtása menti. Az SQL Data Warehouse használatakor ELT kihasználja az MPP rendszer végrehajthatja az átalakításokat.

Bár vannak ELT végrehajtásához az SQL Data Warehouse számos változata, az alábbiak alapvető lépéseket:  

1. Bontsa ki a forrásadatok szöveg fájlokba.
2. Megnyílik az adatok Azure Blob-tároló vagy az Azure Data Lake Store.
3. Készítse elő az adatok betöltésekor.
2. Adatok betöltése az SQL Data Warehouse PolyBase használatával átmeneti tárolási táblákból.
3. Alakítsa át az adatokat.
4. Az adatok beszúrása éles táblákba.


Betöltés oktatóanyagok esetén lásd: [használja a PolyBase adatok betöltése az Azure blob storage az Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

További információkért lásd: [minták blog betöltése](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Beállítások betöltése a polybase-zel

A PolyBase olyan technológia, amely hozzáfér az adatok a T-SQL nyelv keresztül az adatbázison kívül. Adatok betöltése az SQL Data Warehouse a legjobb módja. A polybase-zel az adatok a számítási csomópontok számára közvetlenül az adatforrásból párhuzamosan tölti be. 

Adatok betöltése a PolyBase, ezek betöltését beállításokat használhatja.

- [PolyBase-T-SQL](load-data-from-azure-blob-storage-using-polybase.md) jól működik, ha az adatok Azure Blob-tároló vagy az Azure Data Lake Store van. A betöltés folyamat a legtöbb ellenőrzést ad meg, de rendelkeznie kell a külső objektumok adhatók meg. A többi módszer határozza meg ezeket az objektumokat a háttérben, a forrástábla leképezése céltábla.  Levezényelni a T-SQL terhelés, az Azure Data Factory, SSIS vagy az Azure functions is használhat. 
- [PolyBase az SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) jól működik, ha a forrásadatok az SQL Server, SQL Server helyi, illetve a felhőben van-e. SSIS cél tábla leképezéseket a forrás definiálja, és is koordinálja a terhelés. Ha már rendelkezik SSIS-csomagok, módosíthatja a csomagokat az új adatok adatraktár cél együttműködni. 
- [Azure Data Factory (ADF) PolyBase](sql-data-warehouse-load-with-data-factory.md) egy másik eszköz a vezénylési.  Meghatározza egy folyamatot, és feladatok ütemezi. 
- [PolyBase az Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) továbbítja az adatokat az SQL Data Warehouse-táblából egy Databricks dataframe és/vagy egy Databricks dataframe adatokat ír az SQL Data Warehouse tábla.

### <a name="polybase-external-file-formats"></a>A PolyBase külső fájlformátum

A PolyBase adatokat tölt az UTF-8 és UTF-16 kódolású tagolt szövegfájlok. A tagolt szövegfájlok, valamint betölti a Hadoop-fájl formátumok RC fájl ORC és Parquet. A PolyBase adatok betöltése a Gzip és klassz kis tömörített fájlok. A PolyBase jelenleg nem támogatja a kiterjesztett ASCII, rögzített szélességű formátum és beágyazott formátumú például WinZip JSON vagy XML.

### <a name="non-polybase-loading-options"></a>A PolyBase nem betöltése beállítások
Ha az adatok nem kompatibilis a PolyBase, használhatja [bcp](/sql/tools/bcp-utility) vagy a [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP Azure Blob Storage tárolóban keresztül közvetlenül az SQL Data Warehouse betölti, és csak kis terhelések számára készült. Vegye figyelembe, ezek a beállítások betöltése teljesítménye jelentősen lassabb, mint a PolyBase. 


## <a name="extract-source-data"></a>Forrásadatok kinyerése

A forrásrendszerben kívüli adatok attól függ, hogy a forrás.  A cél, hogy az adatok áthelyezése a tagolt szövegfájlok rendszerbe. Ha SQL Server használ, akkor használhatja [bcp parancssori eszköz](/sql/tools/bcp-utility) exportálja az adatokat.  

## <a name="land-data-to-azure-storage"></a>Az Azure storage föld adatok

Megnyílik az adatokat az Azure-tárfiókba, a áthelyezheti úgy, hogy [Azure Blob Storage tárolóban](../storage/blobs/storage-blobs-introduction.md) vagy [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Az egyik helyen az adatok szövegfájlok be kell tárolni. A Polybase bármely helyről tud betölteni.

Ezek azok az eszközök és adatok áthelyezése az Azure Storage segítségével szolgáltatások.

- [Az Azure ExpressRoute](../expressroute/expressroute-introduction.md) szolgáltatás növeli a hálózat átviteli sebességét, a teljesítmény és kiszámíthatóságot. ExpressRoute olyan szolgáltatás, amely továbbítja a dedikált titkos kapcsolaton keresztül az adatok az Azure-bA. Az ExpressRoute-kapcsolatok nem útvonal-adatok a nyilvános interneten keresztül. A kapcsolatok kínál további megbízhatóságát, gyorsabb sebességű, kisebb késések és nagyobb biztonságot nyújtana tipikus kapcsolatok a nyilvános interneten keresztül.
- [AZCopy segédprogram](../storage/common/storage-moving-data.md) adatok áthelyezése az Azure Storage a nyilvános interneten keresztül. Ez működik, ha az adatok mérete legfeljebb 10 TB. Az AZCopy rendszeresen terhelések végrehajtásához tesztelése a hálózati sebességet meg, hogy elfogadható. 
- [Az Azure Data Factory (ADF)](../data-factory/introduction.md) egy átjárót, amelyek telepítése a helyi kiszolgálón. Majd áthelyezni az adatokat a helyi kiszolgáló Azure Storage legfeljebb egy folyamatot is létrehozhat. Tekintse meg a Data Factory használatához az SQL Data Warehouse szolgáltatással [adatok betöltése az SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).

## <a name="prepare-data"></a>Adatok előkészítése

Szükség lehet előkészítése és a tárfiókban lévő adatok törlése előtt az SQL Data Warehouse betöltése során. Adatok előkészítése közben a az forrást, mivel az adatok exportálása szövegfájlok, vagy után az adatokat az Azure Storage hajtható végre.  A legegyszerűbb az adatokat a folyamat a lehető legkorábban működik.  

### <a name="define-external-tables"></a>Adja meg a külső táblákhoz
Adatok betöltése előtt kell definiálni az adatraktár külső táblákon. A polybase külső táblák segítségével határozza meg, és az adatok elérését az Azure Storage. A külső tábla hasonlít normál táblákhoz. A fő különbség a külső tábla mutat kívül az adatraktárban tárolt adatok. 

Külső táblák meghatározása magában foglalja a megadása az adatforrás, a szövegfájlok, és a definíciói formátuma. A T-SQL-szintaxis témakörök, amelyre szüksége lesz a következők:
- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql)

Külső objektumok létrehozására láthat példát, tekintse meg a [külső táblák létrehozása](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) . lépés: az betöltése oktatóanyag.

### <a name="format-text-files"></a>Szövegfájlok formátuma

Miután a külső objektumok meghatározása, úgy kell igazítania, a szövegfájlok, és a külső tábla és -fájlformátum definíciója a sorokat. Az adatfájl egyes soraiban szövegfájl kell igazodnak a tábla definíciójában.

A szöveg fájlok:

- Ha az adatok érkezik a nem relációs adatforrás, akkor irányítópulttá sorok és oszlopok. Az adatok relációs és nem relációs forrásból származik, hogy az adatok való megfelelés érdekében a tábla, amelybe azt tervezi, hogy az adatok betöltése az oszlopdefiníciók kell alakítani. 
- A szöveges fájlt való megfelelés érdekében az SQL Data Warehouse céltábla oszlopok és adattípusok formázása. A külső szövegfájlok adattípusok és a data warehouse tábla között a hibás illesztés hibákat okoz a sorokat a rendszer elutasítja a betöltése közben.
- A szövegfájl, amelynek a lezáró külön mezőket.  Győződjön meg arról, egy karakter vagy egy karaktersorozat, amely nem található a forrásban lévő használatára. A megadott lezáró használja [külső FÁJLFORMÁTUM létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Betöltés előkészítési táblába
Adatok lekérése az adatraktárba, az jól használható első betöltés az adatok egy átmeneti táblába. Az átmeneti táblázat segítségével hibák kezelheti a termelési táblák zavarása nélkül, és visszaállítási műveletek az éles táblán küszöbölhető. Az átmeneti táblázat is biztosít arra, hogy az SQL Data Warehouse átalakítások futtatása előtt az adatok beszúrása éles táblákat.

Betöltése a következő T-SQL, futtassa a [létrehozása TABLE AS kiválasztása (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) T-SQL-utasításban. Ez a parancs eredménye egy olyan select utasításon szúr be egy új tábla. Az utasítás kiválaszt egy külső táblát, importálja a külső adatokat. 

A következő példában kiegészítő Dátum, a külső tábla. Minden sor egy új táblába dbo importálása. Az adatok.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Az adatok átalakítása
Közben az átmeneti tárolási tábla, hajtsa végre, amely a számítási feladat által igényelt átalakításokat. Ezután lépjen az adatok egy éles táblába.

## <a name="insert-data-into-production-table"></a>Adatok beszúrása a termelési táblába

AZ INSERT... SELECT utasításban az átmeneti tárolási tábla az adatok áthelyezése az állandó tábla. 

Az ETL-folyamat tervezéséhez, futtassa a folyamat kis teszt mintán. Próbálja 1000 sor kibontása a táblából fájlba, helyezze az Azure-ba, és ismételje meg az átmeneti tárolási tábla betöltése során. 

## <a name="partner-loading-solutions"></a>Partnermegoldások betöltése
Partnereink számos rendelkezik megoldások betöltésekor. További tudnivalókért lásd: listáját a [megoldási partnerek](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>További lépések
Útmutatás feltöltését, lásd: [útmutató adatok betöltése a](guidance-for-loading-data.md).


