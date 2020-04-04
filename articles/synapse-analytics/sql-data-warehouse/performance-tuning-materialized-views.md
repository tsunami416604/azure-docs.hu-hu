---
title: Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel
description: Javaslatok és szempontok, amelyeket akkor kell tudnia, amikor materializált nézeteket használ a lekérdezés teljesítményének javítása érdekében.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28dbe66d27ac75bee2ceecd160ead3dea6538034
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633815"
---
# <a name="performance-tuning-with-materialized-views"></a>Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel

A synapsze-i SQL-készlet ben materializált nézetek alacsony karbantartási módszert biztosítanak az összetett analitikus lekérdezések gyors teljesítményéhez lekérdezésmódosítás nélkül. Ez a cikk ismerteti a materializált nézetek használatával kapcsolatos általános útmutatást.

Az Azure SQL Data Warehouse materializált nézetei alacsony karbantartási igényű módszert biztosítanak az összetett analitikus lekérdezésekhez, hogy gyors teljesítményt nyújtsanak a lekérdezésmódosítása nélkül. Ez a cikk ismerteti a materializált nézetek használatával kapcsolatos általános útmutatást.

## <a name="materialized-views-vs-standard-views"></a>Materializált nézetek és szabványos nézetek

Az SQL-készlet támogatja a szabványos és materializált nézeteket.  Mindkettő SELECT kifejezéssel létrehozott virtuális tábla, amelyet logikai táblaként mutatnak be a lekérdezéseknek.  Nézetek beágyazza a gyakori adatszámítás összetettségét, és adjunk hozzá egy absztrakciós réteget a számítási módosításokhoz, így nincs szükség a lekérdezések újraírására.  

A szabványos nézet minden alkalommal kiszámítja az adatokat, amikor a nézetet használja.  Nincsenek adatok a lemezen. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít az adatbázis logikai objektumainak és lekérdezéseinek rendszerezésében.  Szabványos nézet használatához a lekérdezésnek közvetlenül hivatkoznia kell rá.

A materializált nézet előre kiszámítja, tárolja, és az adatokat az SQL-készletben tartja, mint egy tábla.  Materializált nézet használatközben nincs szükség újraszámításra.  Ezért azok a lekérdezések, amelyek az adatok egészét vagy részhalmazát használják materializált nézetekben, gyorsabb teljesítményt érhetnek el.  Még jobb, lekérdezések használhatják a materializált nézet anélkül, hogy közvetlen hivatkozást, így nem kell módosítani az alkalmazás kódját.  

A szabványos nézetre vonatkozó követelmények többsége továbbra is érvényes a materializált nézetre. A materializált nézet szintaxisával és egyéb követelményeivel kapcsolatos részletekért olvassa el [a MATERIALIZÁLT NÉZET LÉTREHOZÁSA A HOGY SELECT LEHETŐSÉGET.](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)

| Összehasonlítás                     | Nézet                                         | Materialized View
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Meghatározás megtekintése                 | SQL készletben tárolva.              | SQL készletben tárolva.
|Tartalom megtekintése                    | A nézet minden egyes alkalommal történő létrehozása.   | A nézet létrehozása során előre feldolgozott és az SQL-készletben tárolt. Frissítve, ahogy az adatok hozzáadódnak az alapul szolgáló táblákhoz.
|Adatfrissítés                    | Mindig frissítve                               | Mindig frissítve
|A nézetadatok összetett lekérdezésekből való beolvasásának gyorsasága     | Lassú                                         | Gyors  
|Extra tárhely                   | Nem                                           | Igen
|Szintaxis                          | NÉZET LÉTREHOZÁSA                                  | MATERIALIZÁLT NÉZET LÉTREHOZÁSA KIJELÖLÉSKÉNT

## <a name="benefits-of-using-materialized-views"></a>A materializált nézetek használatának előnyei

A megfelelően megtervezett materializált nézet a következő előnyökkel járhat:

- Csökkentse a joinokkal és az összesítő függvényekkel rendelkező összetett lekérdezések végrehajtási idejét. Minél összetettebb a lekérdezés, annál nagyobb a végrehajtási időmegtakarítás lehetősége. A legtöbb előny akkor érhető el, ha a lekérdezés számítási költsége magas, és az eredményül kapott adatkészlet kicsi.  
- Az SQL-készletben lévő optimalizáló automatikusan használhatja az üzembe helyezett materializált nézeteket a lekérdezésvégrehajtási tervek javításához.  Ez a folyamat átlátható a gyorsabb lekérdezési teljesítményt biztosító felhasználók számára, és nem igényel lekérdezéseket a materializált nézetekre való közvetlen hivatkozáshoz.
- A nézetek karbantartásának alacsony megkövetelése.  Az alaptáblák összes növekményes adatváltozása automatikusan szinkron módon kerül a materializált nézetekbe.  Ez a kialakítás lehetővé teszi a materializált nézetek lekérdezését, hogy ugyanazokat az adatokat adják vissza, mint az alaptáblák közvetlen lekérdezése.
- A materializált nézetben lévő adatok az alaptábláktól eltérően terjeszthetők.  
- A materializált nézetekben lévő adatok ugyanolyan magas rendelkezésre állást és rugalmassági előnyöket kapnak, mint a normál táblák adatai.  

Az SQL-készletben megvalósított materializált nézetek a következő további előnyöket is biztosítják:

Más adattárház-szolgáltatókkal összehasonlítva az Azure SQL Data Warehouse-ban megvalósított materializált nézetek a következő további előnyöket is biztosítják:

- Automatikus és szinkron adatfrissítés az alaptáblák adatváltozásaival. Nincs szükség felhasználói műveletre.
- Széles körű összesítő függvénytámogatás. Lásd: [MATERIALIZÁLT NÉZET LÉTREHOZÁSA SELECT(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)néven.
- A lekérdezés-specifikus materializált nézet javaslat támogatása.  Lásd: [EXPLAIN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Gyakori forgatókönyvek  

A materializált nézeteket általában a következő esetekben használják:

**Javítani kell az összetett analitikus lekérdezések teljesítményét a nagy méretű adatokkal szemben**

Az összetett analitikus lekérdezések általában több összesítési függvényt és táblaillesztést használnak, ami több számítási nehéz műveletet eredményez, például véletlen sorrendű és illesztéseket a lekérdezés végrehajtásában.  Ezért ezek a lekérdezések hosszabb időt vesz igénybe, különösen a nagy táblák.  

A felhasználók materializált nézeteket hozhatnak létre a lekérdezések közös számításaiból visszaadott adatokhoz, így nincs szükség újraszámításra, ha a lekérdezések szükségesek ezekhez az adatokhoz, ami alacsonyabb számítási költséget és gyorsabb lekérdezési választ tesz lehetővé.

**Gyorsabb teljesítményre van szükség a lekérdezések nem vagy minimális módosítása nélkül**

Séma és lekérdezési változások sql készletek általában minimálisra, hogy támogassa a rendszeres ETL-műveletek és a jelentéskészítés.  A lekérdezésteljesítmény hangolására a személyek használhatnak materializált nézeteket, ha a nézetek által viselt költségeket ellensúlyozhatja a lekérdezési teljesítmény növekedése.

Más finomhangolási lehetőségekhez, például a méretezéshez és a statisztikakezeléshez képest sokkal kevésbé hatásos termelési változás a materializált nézet létrehozásához és fenntartásához, és a potenciális teljesítménynövekedés is magasabb.

- Materializált nézetek létrehozása vagy karbantartása nincs hatással az alaptáblákon futó lekérdezésekre.
- A lekérdezés-optimalizáló automatikusan használhatja az üzembe helyezett materializált nézeteket közvetlen nézethivatkozás nélkül a lekérdezésben. Ez a funkció csökkenti a lekérdezések teljesítményhangolásának módosításának szükségességét.

**Különböző adatterjesztési stratégiára van szükség a gyorsabb lekérdezési teljesítmény érdekében**

Az SQL-készlet egy elosztott, masszívan párhuzamos feldolgozási (MPP) rendszer.   Az SQL-készlettáblában lévő adatok 60 csomópont között [oszlanak](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) meg a három terjesztési stratégia (kivonat, round_robin vagy replikált) egyikének használatával.  

Az adatelosztás a tábla létrehozási idejében van megadva, és változatlan marad, amíg a táblát el nem dobják. Materializált nézet, hogy egy virtuális tábla a lemezen támogatja a kivonatot és round_robin adateloszlások.  A felhasználók választhatnak olyan adateloszlást, amely eltér az alaptábláktól, de optimális a nézeteket leginkább használó lekérdezések teljesítményéhez.  

## <a name="design-guidance"></a>Tervezési útmutató

Az alábbiakban a materializált nézetek nek a lekérdezési teljesítmény javítása érdekében történő használatával kapcsolatos általános útmutatást olvashatja:

**Tervezés a munkaterheléshez**

Mielőtt elkezdené létrehozni a materializált nézeteket, fontos, hogy a lekérdezési minták, a fontosság, a gyakoriság és az eredményül kapott adatok mérete tekintetében alapos ismeretekkel tekintsen a munkaterhelésről.  

A felhasználók futtathatják a EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> a lekérdezésoptimalizáló által ajánlott materializált nézetekhez.  Mivel ezek a javaslatok lekérdezés-specifikus, egy materializált nézet, amely egyetlen lekérdezés előnyeit nem lehet optimális más lekérdezések ugyanabban a számítási feladatban.  

Értékelje ki ezeket a javaslatokat a számítási feladatok igényeinek szem előtt tartva.  Az ideális materializált nézetek azok, amelyek a számítási feladatok teljesítményét szolgálják.  

**Legyen tudatában a gyorsabb lekérdezések és a költségek közötti kompromisszumnak**

Minden egyes materializált nézethez adattárolási költség és a nézet karbantartásának költsége áll fenn.  Ahogy az adatok változnak az alaptáblákban, a materializált nézet mérete nő, és fizikai szerkezete is megváltozik.  A lekérdezési teljesítmény romlásának elkerülése érdekében az SQL-készletmotor minden egyes materializált nézetet külön-külön tart meg.  

A karbantartási munkaterhelés nagyobb lesz, ha a materializált nézetek száma és az alaptábla módosításai nőnek.   A felhasználóknak ellenőrizniük kell, hogy az összes materializált nézetből eredő költségek ellensúlyozhatók-e a lekérdezés teljesítménynövekedésével.  

Ezt a lekérdezést futtathatja az adatbázis materializált nézeteinek listájához:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

A materializált nézetek számának csökkentésére adott lehetőségek:

- Azonosítsa a számítási feladatok összetett lekérdezései által gyakran használt gyakori adatkészleteket.  Materializált nézeteket hozhat létre az adatkészletek tárolásához, hogy az optimalizáló építőelemként használhassa őket végrehajtási tervek létrehozásakor.  

- Dobja el az alacsony használatú vagy már nem szükséges materializált nézeteket.  A letiltott materializált nézet nem marad meg, de továbbra is tárolási költségekkel jár.  

- Az azonos vagy hasonló alaptáblákon létrehozott materializált nézetek kombinálása akkor is, ha adataik nem fedik át egymást.  A materializált nézetek fésülése nagyobb méretet eredményezhet, mint a különálló nézetek összege, azonban a nézet karbantartási költségének csökkentenie kell.  Példa:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nem minden teljesítményhangoláshoz szükséges a lekérdezés módosítása**

Az SQL-készletoptimalizáló automatikusan használhatja az üzembe helyezett materializált nézeteket a lekérdezési teljesítmény javítása érdekében.  Ez a támogatás transzparens módon vonatkozik azokra a lekérdezésekre, amelyek nem hivatkoznak azokra a nézetekre és lekérdezésekre, amelyek nem támogatott összesítéseket használnak a materializált nézetek létrehozásában.  Nincs szükség lekérdezésmódosításra. A lekérdezés becsült végrehajtási tervének ellenőrzéséhez ellenőrizheti, hogy a program program materializált nézetet használ-e.  

**Materializált nézetek figyelése**

A materializált nézet ugyanúgy tárolódik az SQL-készletben, mint egy fürtözött oszlopcentrikus indexszel (CCI) rendelkező tábla.  Az adatok materializált nézetből történő olvasása magában foglalja a CCI indexszegmensek beolvasását és az alaptáblákn végrehajtott növekményes módosítások alkalmazását. Ha a növekményes módosítások száma túl magas, a lekérdezés feloldása materializált nézetből hosszabb időt vehet igénybe, mint az alaptáblák közvetlen lekérdezése.  

A lekérdezési teljesítmény csökkenésének elkerülése érdekében célszerű a [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) futtatásával a nézet overhead_ratio (total_rows / max(1, base_view_row)).  A felhasználóknak rebuild a materializált nézet, ha a overhead_ratio túl magas.

**Materializált nézet- és eredményhalmaz gyorsítótárazása**

Ez a két szolgáltatás az SQL-készletben körülbelül ugyanabban az időben kerül bevezetésre a lekérdezésteljesítmény-hangoláshoz.  Az eredményhalmaz-gyorsítótárazás a statikus adatokkal szembeni ismétlődő lekérdezések magas egyidejűsítéséhez és gyors válaszaihoz használható.  

A gyorsítótárazott eredmény használatához a gyorsítótárat kérő lekérdezés űrlapjának meg kell egyeznie a gyorsítótárat eredményező lekérdezéssel.  Ezenkívül a gyorsítótárazott eredménynek a teljes lekérdezésre vonatkoznia kell.  

A materializált nézetek lehetővé teszik az alaptáblák adatváltozásait.  A materializált nézetekben lévő adatok egy lekérdezés egy darabára alkalmazhatók.  Ez a támogatás lehetővé teszi, hogy ugyanazokat a materializált nézeteket használják a különböző lekérdezések, amelyek megosztják a gyorsabb teljesítményt.

## <a name="example"></a>Példa

Ez a példa egy TPCDS-szerű lekérdezést használ, amely megkeresi azokat az ügyfeleket, akik több pénzt költenek katalóguson keresztül, mint az üzletekben, azonosítják az előnyben részesített vevőket és a származási országukat.   A lekérdezés magában foglalja a TOP 100 rekordok kiválasztását az UNION-ból három sub-SELECT utasításból, amelyek a SZUM() és a GROUP BY függvényt is magukban foglalják.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Ellenőrizze a lekérdezés becsült végrehajtási tervét.  Jelenleg 18 shuffles és 17 csatlakozik műveletek, amelyek több időt vesz igénybe, hogy végre. Most hozzunk létre egy materializált nézetet a három sub-SELECT utasítás mindegyikéhez.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Ellenőrizze újra az eredeti lekérdezés végrehajtási tervét.  Most az illesztések száma 17-ről 5-re változik, és már nincs véletlen.  Kattintson a szűrő művelet ikonra a tervben, a Kimeneti lista azt mutatja, hogy az adatok at a materializált nézetekből olvassa be az alaptáblák helyett.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Materializált nézetek esetén ugyanaz a lekérdezés sokkal gyorsabban fut kódmódosítás nélkül.  

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [Synapse SQL-készlet fejlesztésének áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
