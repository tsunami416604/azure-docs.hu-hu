---
title: Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával
description: Javaslatok és szempontok, amelyeket tudnia kell, ahogy a rendezett fürtözött oszlopcentrikus index a lekérdezési teljesítmény javítása érdekében.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: a5bb048a2368f60a83e70dcd6d1ce663ce70a885
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350922"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával  

Amikor a felhasználók lekérdeznek egy oszlopcentrikus táblát az SQL Analytics szolgáltatásban, az optimalizáló ellenőrzi az egyes szegmensekben tárolt minimális és maximális értékeket.  A lekérdezési predikátum határain kívül lévő szegmenseket a rendszer nem olvassa be lemezről a memóriára.  A lekérdezés ekképpen gyorsabb teljesítményt érhet el, ha az olvasmányok száma és teljes mérete kicsi.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Rendezett és nem rendezett fürtözött oszlopcentrikus index 
Alapértelmezés szerint minden indexbeállítás nélkül létrehozott SQL Analytics-táblához egy belső összetevő (indexszerkesztő) nem rendezett fürtözött oszlopcentrikus indexet (CCI) hoz létre rajta.  Az egyes oszlopokban lévő adatok külön CCI sorcsoportszegmensbe vannak tömörítve.  Az egyes szegmensek értéktartományában metaadatok találhatók, így a lekérdezési predikátum határain kívül lévő szegmensek nem kerülnek olvasásra a lemezről a lekérdezés végrehajtása során.  A CCI a legmagasabb szintű adattömörítést kínálja, és csökkenti a szegmensek méretét az olvasáshoz, így a lekérdezések gyorsabban futnak. Mivel azonban az indexszerkesztő nem rendezi az adatokat, mielőtt szegmensekbe tömörítené őket, egymást átfedő értéktartományokkal rendelkező szegmensek fordulhatnak elő, így a lekérdezések több szegmenst olvasnak a lemezről, és hosszabb időt vesz igénybe a befejezésük.  

Rendezett CCI létrehozásakor az SQL Analytics motor a rendelési kulcs(ok) szerint rendezi a memóriában lévő meglévő adatokat, mielőtt az indexszerkesztő indexszegmensekbe tömöríti azokat.  Rendezett adatok esetén a szegmensátfedések csökkentése csökken, így a lekérdezések hatékonyabb szegmenseltávolítással rendelkeznek, és így gyorsabb a teljesítmény, mivel a lemezről olvasandó szegmensek száma kisebb.  Ha az összes adat egyszerre rendezhető a memóriában, akkor elkerülhető a szegmensátfedés.  Tekintettel az SQL Analytics-táblákban lévő adatok nagy méretére, ez a forgatókönyv nem gyakran fordul elő.  

Ha ellenőrizni szeretné egy oszlop szegmenstartományait, futtassa ezt a parancsot a tábla nevével és oszlopnevével:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> Egy rendezett CCI-táblában az ugyanazon köteg DML-ből vagy adatbetöltési műveletekből származó új adatok az adott kötegen belül vannak rendezve, nincs globális rendezés a táblázatösszes adata között.  A felhasználók a rendezett CCI-t a táblázatban szereplő összes adat rendezéséhez építhetik át.  Az SQL Analytics az oszlopcentrikus index REBUILD egy offline művelet.  Particionált tábla esetén a REBUILD egyszerre egy partícióra történik.  Az újraépítendő partícióadatai "offline" állapotúak, és nem érhetők el, amíg a REBUILD el nem készül az adott partícióra vonatkozóan. 

## <a name="query-performance"></a>Lekérdezési teljesítmény

A lekérdezés teljesítménynyeresége egy rendezett CCI-ből a lekérdezési mintáktól, az adatok méretétől, az adatok rendezésének módjától, a szegmensek fizikai szerkezetétől, valamint a lekérdezés végrehajtásához kiválasztott DWU-tól és erőforrásosztálytól függ.  A megrendelt CCI-tábla tervezésekor a felhasználóknak át kell tekinteniük ezeket a tényezőket, mielőtt kiválasztanák a rendelési oszlopokat.

Az összes ilyen mintával rendelkező lekérdezések általában gyorsabban futnak a rendezett CCI-vel.  
1. A lekérdezések egyenlőségi, egyenlőtlenségi vagy tartomány-predikátumokkal rendelkeznek
1. Az predikátumoszlopok és a megrendelt CCI oszlopok megegyeznek.  
1. Az predikátumoszlopok ugyanabban a sorrendben használatosak, mint a rendezett CCI oszlopok oszlopsora.  
 
Ebben a példában a T1 tábla fürtözött oszlopcentrikus indexe Col_C, Col_B és Col_A sorrendben van rendezve.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

A lekérdezés 1 teljesítménye több hasznot húzhat a rendezett CCI-ből, mint a másik három lekérdezés. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Adatbetöltési teljesítmény

A rendezett CCI-táblába történő adatbetöltés teljesítménye hasonló a particionált táblához.  Az adatok betöltése egy rendezett CCI táblába hosszabb időt vehet igénybe, mint egy nem rendezett CCI tábla az adatrendezési művelet miatt, azonban a lekérdezések gyorsabban futhatnak a rendezett CCI-vel.  

Íme egy példa a különböző sémákat rendelkező táblákba történő betöltési adatok teljesítmény-összehasonlítására.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Íme egy példa a CCI és a rendezett CCI lekérdezési teljesítményének összehasonlítása.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Szegmensátfedés csökkentése

Az egymást átfedő szegmensek száma a rendezendő adatok méretétől, a rendelkezésre álló memóriától és a párhuzamosság (MAXDOP) maximális mértékétől függ a rendezett CCI létrehozása során. Az alábbiakban a rendezett CCI létrehozásakor csökkentheti a szegmensátfedéseket.

- Használja az xlargerc erőforrásosztályt egy magasabb DWU-n, hogy több memóriát engedélyezze az adatok rendezéséhez, mielőtt az indexszerkesztő szegmensekbe tömöríti az adatokat.  Az indexszegmensben az adatok fizikai helye nem módosítható.  Nincs adatrendezés egy szegmensen belül vagy szegmensek között.  

- Rendezett CCI létrehozása MAXDOP = 1 értékkel.  A rendezett CCI-létrehozáshoz használt szál az adatok egy részhalmazán működik, és helyileg rendezi azt.  Nincs globális rendezés a különböző szálak szerint rendezett adatok között.  A párhuzamos szálak használata csökkentheti a rendezett CCI létrehozásának idejét, de több egymást átfedő szegmenst generál, mint egyetlen szál használata.  Jelenleg a MAXDOP beállítás csak a CREATE TABLE AS SELECT paranccsal létrehozott rendezett CCI tábla létrehozásában támogatott.  A rendezett CCI létrehozása CREATE INDEX vagy CREATE TABLE parancsokkal nem támogatja a MAXDOP beállítást. Például:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Az adatokat előre rendezheti a rendezési kulcs(ok) szerint, mielőtt betöltené őket az SQL Analytics-táblákba.


Íme egy példa egy rendezett CCI tábla eloszlására, amelynek a fenti javaslatok szerint nulla szegmensátfedése van. A rendezett CCI-tábla egy DWU1000c adatbázisban jön létre ctas-on keresztül egy 20 GB-os halomtáblából maxdop 1 és xlargerc használatával.  A CCI egy BIGINT oszlopban van megrendezve, ismétlődések nélkül.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Rendezett CCI létrehozása nagy asztalokon
A rendezett kkuk létrehozása offline művelet.  Partíciók nélküli táblák esetén az adatok nem lesznek elérhetők a felhasználók számára, amíg a rendezett CCI létrehozási folyamat be nem fejeződik.   Particionált táblák, mivel a motor létrehozza a rendezett CCI partíció partíció, a felhasználók továbbra is elérhetik az adatokat partíciókat, ahol a rendezett CCI létrehozása nem folyamatban van.   Ezzel a beállítással minimalizálhatja az állásidőt a nagy asztalokon rendezett CCI létrehozása során: 

1.    Hozzon létre partíciókat a cél nagy tábla (Table_A).
2.    Hozzon létre egy üres, rendezett CCI táblát (Table_B) ugyanazzal a táblával és partíciósémával, mint az A table.
3.    Váltás egy partíciót az A tábláról a B táblára.
4.    Futtassa az ALTER INDEX <Ordered_CCI_Index>> on <Table_B> REBUILD PARTITION = <Partition_ID> táblázatban a kapcsolópartíció újraépítéséhez.  
5.    Ismételje meg Table_A a 3.
6.    Miután az összes partíció tátvált Table_A Table_B, és újraépítették, dobja Table_A, és nevezze át Table_B Table_A. 

## <a name="examples"></a>Példák

**A. A megrendelt oszlopok és a rendelési sormező ellenőrzése:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Oszlopsor módosítása, oszlopok hozzáadása vagy eltávolítása a rendelési listából, vagy a CCI-ről a megrendelt CCI-re:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
