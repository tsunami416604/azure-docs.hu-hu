---
title: Teljesítmény-Finomhangolás Azure SQL Data Warehouse rendezett fürtözött oszlopcentrikus indextel | Microsoft Docs
description: A lekérdezési teljesítmény javítása érdekében érdemes megfontolni, hogy a rendezett fürtözött oszlopcentrikus index használata során milyen javaslatokat és szempontokat kell figyelembe vennie.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985297"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Teljesítmény-Finomhangolás a rendezett fürtözött oszlopcentrikus indextel  

Amikor a felhasználók lekérdezik Azure SQL Data Warehouse egy oszlopcentrikus-tábláját, a-optimalizáló ellenőrzi az egyes szegmensekben tárolt minimális és maximális értékeket.  A lekérdezési predikátum határain kívüli szegmensek nem olvashatók be a lemezről a memóriába.  A lekérdezés gyorsabb teljesítményt érhet el, ha a beolvasandó szegmensek száma és a teljes mérete kicsi.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Rendezett és nem rendezett, fürtözött oszlopcentrikus index 
Alapértelmezés szerint minden olyan Azure-adattárház-tábla esetében, amely index-beállítás nélkül lett létrehozva, a belső összetevő (index Builder) nem rendezett, fürtözött oszlopcentrikus indexet (CCI) hoz létre.  Az egyes oszlopokban lévő összes adathalmaz egy külön CCI sorcsoport-szegmensbe van tömörítve.  Vannak metaadatok az egyes szegmensek érték-tartományán, így a lekérdezési predikátum határain kívüli szegmensek nem olvashatók be a lemezről a lekérdezés végrehajtása során.  A KKU a legmagasabb szintű adattömörítést kínálja, és csökkenti a szegmensek méretét az olvasáshoz, hogy a lekérdezések gyorsabban fussanak. Mivel azonban az index-szerkesztő nem rendezi az adatait, mielőtt tömöríti azokat a szegmensekre, az átfedésben lévő értékekkel rendelkező szegmensek felmerülhetnek, így a lekérdezések több szegmenst olvasnak be a lemezről, és hosszabb időt vesznek igénybe.  

Rendezett CCI létrehozásakor a Azure SQL Data Warehouse motor a megrendelési kulcs (ok) alapján rendezi a memóriában lévő adatmennyiséget, mielőtt az index-szerkesztő tömöríti az index szegmenseket.  A rendezett adatok esetében a szegmens átfedésben van, ami lehetővé teszi a lekérdezések hatékonyabb szegmensének megszüntetését, és így gyorsabb teljesítményt, mivel a lemezről beolvasott szegmensek száma kisebb.  Ha az összes adatmennyiséget egyszerre rendezheti a memóriában, akkor a szegmens átfedése elkerülhető lehet.  Az adatraktár tábláiban nagy mennyiségű adat miatt ez a forgatókönyv nem fordul elő gyakran.  

Egy oszlop szegmens tartományának vizsgálatához futtassa ezt a parancsot a tábla nevével és az oszlop nevével:

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>Adattöltési teljesítmény

A rendezett CCI-táblázatba betöltött betöltési teljesítmény hasonló a particionált táblába betöltött adatmennyiséghez.  
Az adatgyűjtés egy rendezett CCI-táblába több időt vehet igénybe, mint az Adatrendezés miatt nem rendezett CCI-táblázatba való betöltés.  

Az alábbi példa egy olyan teljesítménybeli összehasonlítást mutat be, amely a különböző sémákkal rendelkező táblákba való betöltést hasonlítja
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>Szegmens átfedésének csökkentése
Az alábbiakban megtalálhatja azokat a lehetőségeket, amelyek tovább csökkentik az átfedésben lévő szegmenseket, amikor a CTAS-on vagy egy meglévő táblában található, adatmennyiséggel rendelkező, megrendelt KKU

- Nagyobb erőforrás-osztály használata, amely lehetővé teszi, hogy több adatok legyenek sorba rendezve a memóriában, mielőtt az index-készítő tömöríti azokat a szegmensekre.  Az index szegmensben az adatfizikai hely nem módosítható.  Egy szegmensen vagy szegmensen belül nincs Adatrendezés.  

- Használjon alacsonyabb párhuzamossági fokot (például DOP = 1).  A rendezett CCI-létrehozáshoz használt minden szál az adathalmazon működik, és helyileg rendezi.  Nincs globális rendezés a különböző szálak között rendezve.  A párhuzamos szálak használatával csökkentheti a rendezett CCI létrehozásának idejét, de több átfedésben lévő szegmenst fog létrehozni, mint egyetlen szál használata esetén. 
- A rendezési kulcs (ok) alapján előre rendezheti az adathalmazt, mielőtt betölti azokat Azure SQL Data Warehouse táblákba.

## <a name="create-ordered-cci-on-large-tables"></a>Rendezett CCI létrehozása nagyméretű táblákon
A rendezett CCI létrehozása offline művelet.  A partíciókat nem tartalmazó táblák esetében az adathozzáférés nem lesz elérhető a felhasználók számára, amíg a rendezett CCI-létrehozási folyamat be nem fejeződik.   A particionált táblák esetében, mivel a motor partíció alapján hozza létre a rendezett CCI-partíciót, a felhasználók továbbra is hozzáférhetnek az olyan partíciókban lévő adatbázisokhoz, ahol a rendezett CCI-létrehozás nincs folyamatban.   Ezzel a beállítással minimálisra csökkentheti az állásidőt a nagy táblákon a rendezett CCI-létrehozás során: 

1.  Hozzon létre partíciókat a cél nagyméretű táblán (az A. táblázat néven).
2.  Hozzon létre egy üres rendezett CCI-táblázatot (a B táblázatnak nevezett tábla) ugyanazzal a táblával és partíciós sémával, amely az A. táblázat.
3.  Váltson át egy partíciót az A táblából a B táblázatba.
4.  Futtassa az ALTER INDEX < Ordered_CCI_Index > Újraépítés a B táblázatban a bekapcsolt partíció újraépítéséhez.  
5.  Ismételje meg a 3. és a 4. lépést az A tábla minden partíciója esetében.
6.  Ha az összes partíció át lett állítva az A táblából a B táblába, és újraépítve lett, az A táblázatba vonja át A táblázatot, és nevezze át a B táblát az A táblázatba. 

## <a name="examples"></a>Példák

**EGY. A rendezett oszlopok és a sorrend sorszámának keresése:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Az oszlopok sorszámának módosításához, oszlopok hozzáadásához vagy eltávolításához a sorrend listából, vagy a CCI-ből a rendezett CCI-re való váltáshoz:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).
