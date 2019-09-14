---
title: Teljesítmény-Finomhangolás Azure SQL Data Warehouse anyagokkal rendelkező nézetekkel | Microsoft Docs
description: 'A lekérdezési teljesítmény javítása érdekében a következő ajánlásokat és szempontokat érdemes ismernie:'
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 85c2607ae163ab2d29a53440cd65672bdbe0fddf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985349"
---
# <a name="performance-tuning-with-materialized-views"></a>Teljesítmény-Finomhangolás a jelentős nézetekkel 
A Azure SQL Data Warehouseban található anyagbeli nézetek alacsony karbantartási módszert biztosítanak a komplex analitikai lekérdezésekhez, így a lekérdezés módosítása nélkül juthatnak hozzá a gyors teljesítményhez. Ebből a cikkből megtudhatja, hogyan használhatja az általános útmutatást a jelentős nézetek használatával kapcsolatban.


## <a name="materialized-views-vs-standard-views"></a>Az alapvető nézetek és a szabványos nézetek
Azure SQL Data Warehouse támogatja a szabványos és a jelentős nézeteket.  Mindkettő olyan virtuális táblákat hoz létre, amelyek a SELECT kifejezésekkel lettek létrehozva, és a lekérdezéseket logikai táblázatként mutatja  A nézetek összefoglalják a közös adatszámítások összetettségét, és felvesznek egy absztrakt réteget a számítási változásokhoz, így nincs szükség a lekérdezések újraírására.  

A normál nézet minden alkalommal kiszámítja az adatmegjelenítést, amikor a nézet használatban van.  A lemezen nem találhatók adattárolók. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít megszervezni a logikai objektumokat és lekérdezéseket egy adatbázisban.  Normál nézet használatához a lekérdezésnek közvetlen hivatkozást kell tartalmaznia. 

Egy anyagilag megtekinthető nézet előre kiszámítja, tárolja és karbantartja az adataikat Azure SQL Data Warehouse ugyanúgy, mint egy tábla.  A rendszer minden alkalommal nem igényel újraszámítást, amikor egy anyagbeli nézetet használ.  Ezért az olyan lekérdezések, amelyek az összes vagy az adatok egy részhalmazát használják az anyagokban, gyorsabb teljesítményt érhet el.  Még jobb is, ha a lekérdezések egy anyagbeli nézetet is használhatnak közvetlen hivatkozás nélkül, ezért nincs szükség az alkalmazás kódjának módosítására.  

A standard nézetekre vonatkozó követelmények többsége továbbra is érvényes egy anyagbeli nézetre. Az anyag nézet szintaxisának és egyéb követelményeinek részletes ismertetését a következő témakörben találja: [anyagelszámolású nézet létrehozása a Select](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)utasítással.



| Összehasonlítás                     | Nézet                                         | Materialized View             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|Definíció megtekintése                 | Az Azure-adattárházban tárolva.              | Az Azure-adattárházban tárolva.    
|Tartalom megtekintése                    | A rendszer minden alkalommal létrehozta a nézetet, amikor a nézet használatban van.   | Az Azure-adattárházban előre feldolgozva és tárolva a nézet létrehozása során. Frissítve, mert a rendszer az adatokat hozzáadja az alapul szolgáló táblákhoz.                                             
|Adatfrissítés                    | Mindig frissítve                               | Mindig frissítve                          
|Az összetett lekérdezések adatainak megtekintési sebessége     | lassú                                         | Gyors  
|Extra tárterület                   | Nem                                           | Igen                             
|Szintaxis                          | NÉZET LÉTREHOZÁSA                                  | A KIVÁLASZTÁSNAK MEGFELELŐEN HOZZON LÉTRE EGY ANYAGBELI NÉZETET           
     
## <a name="benefits-of-using-materialized-views"></a>A lényeges nézetek használatának előnyei

A megfelelő módon kialakított nézet A következő előnyöket nyújtja:

- Csökkentse az összetett lekérdezések végrehajtási idejét az illesztések és az összesítő függvények esetében. Minél összetettebb a lekérdezés, annál nagyobb a végrehajtás-idő megtakarításának lehetősége. A legtöbb előny akkor érhető el, ha a lekérdezés számítási díja magas, és az eredményül kapott adatkészlet kicsi.  

- Az Azure SQL Data Warehouse-optimalizáló a lekérdezés-végrehajtási tervek javítása érdekében automatikusan felhasználja a központilag telepített anyagokat.  Ez a folyamat olyan felhasználók számára transzparens, akik gyorsabb lekérdezési teljesítményt biztosítanak, és nem igénylik a lekérdezések közvetlen hivatkozását a jelentős nézetekre. 

- Kis karbantartás szükséges a nézetekben.  Egy anyagilag megtekinthető nézet két helyen tárolja az adatokból a kezdeti adatokhoz tartozó fürtözött oszlopcentrikus indexet, a növekményes adatváltozások esetében pedig egy különbözeti tárolót.  Az alaptáblákból származó adatváltozásokat a rendszer szinkron módon automatikusan hozzáadja a Delta-tárolóhoz.  A háttérben futó folyamat (a rekord mozgatója) rendszeres időközönként áthelyezi az adatait a különbözeti tárolóból a nézet oszlopcentrikus indexére.  Ez a kialakítás lehetővé teszi a jelentős nézetek lekérdezését, hogy ugyanazokat az adatokkal adják vissza, mint az alaptáblák közvetlen lekérdezése. 
- A rendszer az alaptáblákból eltérő módon oszthatja meg az adatok egy anyagbeli nézetben.  
- Az anyagilag megtekintett nézetekben lévő adatok ugyanolyan magas rendelkezésre állást és rugalmasságot biztosítanak, mint a hagyományos táblák adatai.  
 
Más adattárház-szolgáltatókkal való összehasonlítás esetén a Azure SQL Data Warehouseban megvalósított, az alábbi előnyökkel jár: 

- Automatikus és szinkron Adatfrissítés az alaptáblákban tárolt adatváltozásokkal. Nincs szükség felhasználói beavatkozásra. 
- Széleskörű összesítő függvények támogatása. Lásd: [anyagelszámolású nézet létrehozása Select (Transact-SQL) néven](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).
- A lekérdezés-specifikus, jelentős megjelenítésre vonatkozó javaslat támogatása.  Lásd: [Magyarázat (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Gyakori alkalmazási helyzetek  

A rendszer általában az alábbi helyzetekben használja az anyagilag látható nézeteket: 

**Javítania kell a nagy méretű adatmennyiséggel kapcsolatos összetett analitikai lekérdezések teljesítményét.**

Az összetett analitikai lekérdezések jellemzően több aggregációs funkciót és táblázatos illesztéseket használnak, így több számítási igényű művelet, például véletlenszerű és illesztések is megtalálhatók a lekérdezés-végrehajtás során.  Ezért a lekérdezések végrehajtása hosszabb ideig tarthat, különösen nagy táblákon.  A felhasználók a gyakori számításokból származó adatokhoz hozhatnak létre egyszerű nézeteket, így nincs szükség újraszámításra, ha az adatok lekérdezésekhez szükségesek, így kevesebb számítási díj és gyorsabb lekérdezési válasz érhető el. 

**Gyorsabb teljesítményre van szükség a lekérdezési módosítások nélkül**

Az adattárházak sémájának és lekérdezésének változásai általában legalább a normál ETL-műveletek és jelentéskészítés támogatásához szükségesek.  A lekérdezések teljesítményének finomhangolása érdekében a felhasználók a lekérdezés teljesítményének növelésével ellensúlyozni tudják a kihasználható nézeteket. A más hangolási lehetőségekkel, például a skálázással és a statisztikai kezeléssel összehasonlítva sokkal kevésbé befolyásolja az éles környezetbeli nézet létrehozását és karbantartását, valamint a potenciális teljesítményt is.

- Az adatbázis-nézetek létrehozása vagy fenntartása nem befolyásolja az alaptáblákon futó lekérdezéseket.
- A lekérdezés-optimalizáló automatikusan használhatja a központilag telepített, az üzembe helyezett nézeteket, anélkül, hogy a lekérdezésre mutató hivatkozásokat kellene használni. Ez a funkció csökkenti a lekérdezés módosításának szükségességét a teljesítmény finomhangolása során. 

**A lekérdezési teljesítmény gyorsabb elvégzéséhez eltérő adatelosztási stratégia szükséges**

Az Azure-adattárház egy elosztott, nagymértékben párhuzamos feldolgozási (MPP) rendszer.   Az adatraktár-táblában lévő adatokat a rendszer a három [terjesztési stratégia](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute) (kivonat, round_robin vagy replikált) egyikével osztja el a 60-csomópontok között.  Az adateloszlás a tábla létrehozási idején van megadva, és a tábla eldobása előtt változatlan marad. A lemezen lévő virtuális tábla által támogatott, a kivonat-és round_robin-adateloszlások által képpontozott nézet.  A felhasználók kiválaszthatnak egy adateloszlást, amely eltér az alaptábláktól, de optimális a nézeteket használó lekérdezések teljesítményéhez.  

## <a name="design-guidance"></a>Tervezési útmutató 

A lekérdezési teljesítmény javítása érdekében az alábbi általános útmutatást követve hozhatja ki a jelentős nézetek használatát:

**Számítási feladatok tervezése**

- Mielőtt megkezdené az alapvető nézetek létrehozását, fontos, hogy alapos ismeretekkel rendelkezzen a számítási feladatokról a lekérdezési minták, a fontosság, a gyakoriság és az eredményül kapott adatok mérete tekintetében.  

- A felhasználók elolvashatják a WITH_RECOMMENDATIONS < SQL_statement > a lekérdezés-optimalizáló által javasolt, anyagilag megtekinthető nézetekhez.  Mivel ezek a javaslatok lekérdezés-specifikusak, az egyetlen lekérdezés előnyeit kihasználó, egy adott számítási feladathoz tartozó más lekérdezések esetében nem lehet optimális.  Értékelje ki ezeket az ajánlásokat a számítási feladatokhoz szükséges szem előtt tartva.  Az ideális, lényeges nézetek a számítási feladatok teljesítményének kihasználása.  

**Vegye figyelembe a gyorsabb lekérdezések és a díjak közötti kompromisszumot.** 

- Minden jelentős nézet esetében a tárolási díj és a megtekintési díj a rekordos küldéssel. Azure SQL Data Warehouse Server-példányon egyetlen rekord található.  Ha túl sok a nagy mennyiségű nézet, a rekordokat kezelő munkaterhelése növekedni fog, és a jelentős méretű nézeteket kihasználó lekérdezések teljesítménye csökkenhet, ha a rekordos megjelenítés nem képes elég gyorsan áthelyezni az adatok indexelési szegmenseit  A felhasználóknak ellenőriznie kell, hogy az összes jelentős nézetből felmerült költségek ellensúlyozzák-e a lekérdezési teljesítmény nyereségét.  Futtassa ezt a lekérdezést a következő adatbázisban található, anyagként szolgáló nézet listájához: 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

A kimutatott nézetek számának csökkentésére szolgáló beállítások: 

- Azonosítsa a számítási feladatok összetett lekérdezései által gyakran használt közös adatkészleteket.  Hozzon létre az adathalmazok tárolására szolgáló, anyagilag megtekinthető nézeteket, hogy az optimalizáló a végrehajtási tervek létrehozásakor építőelemeket használjanak.  

- Dobja el az alacsony kihasználtságú vagy már nem szükséges anyagbeli nézeteket.  A letiltott anyagbeli nézet nem tart fenn, de továbbra is A tárolási költségekkel jár.  

- Összekapcsolhatja az azonos vagy hasonló alaptáblákon létrehozott, az adatok átfedését még akkor is, ha az adatok nem fedik át egymást.  A kifésülő anyagbeli nézetek nagyobb méretű nézetet eredményezhetnek, mint a különálló nézetek összege, azonban a karbantartási költségeket is csökkenteni kell.  Példa:

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

**A teljesítmény finomhangolása nem igényli a lekérdezés módosítását**

Az adatraktár-optimalizáló a lekérdezések teljesítményének növelése érdekében automatikusan felhasználja a telepített, jelentős nézeteket.  Ez a támogatás transzparens módon van alkalmazva olyan lekérdezésekre, amelyek nem hivatkoznak az összesítéseket használó nézetekre és lekérdezésekre, és nem támogatottak az anyagilag létrehozott nézetek létrehozásakor.  Nincs szükség lekérdezési módosításra. Megtekintheti a lekérdezés becsült végrehajtási tervét, és ellenőrizheti, hogy van-e egy anyagbeli nézet.  

**A lényeges nézetek figyelése** 

A rendszer az adatraktárban egy, a fürtözött oszlopcentrikus indextel (CCI) rendelkező táblához hasonlóan egy anyagbeli nézetet tárol.  Az adatok egy anyagbeli nézetből történő beolvasása magában foglalja az index vizsgálatát és a változások a különbözeti tárolóból való alkalmazását.  Ha a különbözeti tárolóban lévő sorok száma túl magas, a lekérdezés egy anyagbeli nézetből való feloldása hosszabb időt vehet igénybe, mint az alaptáblák közvetlen lekérdezése.  A lekérdezési teljesítmény romlásának elkerülése érdekében érdemes futtatni a [DBCC-PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest) a nézet overhead_ratio (total_rows/base_view_row) figyeléséhez.  Ha a overhead_ratio túl magas, gondolja át, hogy a rendszer újraépítse az anyagbeli nézetet, így a különbözeti tároló összes sora át lesz helyezve a oszlopcentrikus indexbe.  

**Anyagelszámolású nézet és eredményhalmaz gyorsítótárazása**

Ez a két funkció a lekérdezési teljesítmény finomhangolásához Azure SQL Data Warehouse egy időben van bevezetve.  Az eredményhalmaz gyorsítótárazásával nagy párhuzamosságot és gyors választ kaphat a statikus adatokra irányuló ismétlődő lekérdezésektől.  A gyorsítótárazott eredmény használatához a lekérdezést kérő gyorsítótárnak egyeznie kell azzal a lekérdezéssel, amely a gyorsítótárat hozta létre.  Emellett a gyorsítótárazott eredményt is a teljes lekérdezésre kell alkalmazni.  A jelentős nézetek lehetővé teszik az alaptáblákban lévő adatváltozásokat.  Az anyagilag megtekintett nézetekben tárolt adatok alkalmazhatók egy adott lekérdezésre.  Ez a támogatás lehetővé teszi, hogy ugyanazokat a különböző lekérdezéseket használja, amelyek a gyorsabb teljesítmény érdekében több számítási feladattal rendelkeznek.

## <a name="example"></a>Példa

Ez a példa egy TPCDS-szerű lekérdezést használ, amely megkeresi azokat az ügyfeleket, akik több pénzt költenek a katalóguson keresztül, mint a boltokban, azonosítják az előnyben részesített ügyfeleket és a   A lekérdezés magában foglalja a legfontosabb 100-rekordok kiválasztását a következő három alkijelölési utasítás közül: SUM () és GROUP BY. 

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

Keresse meg a lekérdezés becsült végrehajtási tervét.  A rendszer 18 Shuffle és 17 összekapcsolási műveletet hajt végre, amelyek több időt vesznek igénybe. Most hozzon létre egy, a három alkijelölési utasításhoz tartozó, egy anyagból álló nézetet.   

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
Vizsgálja meg újra az eredeti lekérdezés végrehajtási tervét.  Most az összekapcsolások száma 17 és 5 között változik, és többé nem áll rendelkezésre shuffle.  Kattintson a terv szűrési művelet ikonjára, annak kimeneti listája megjeleníti az adatokat az alaptáblák helyett az anyagból származó nézetből.  

 ![Plan_Output_List_with_Materialized_Views](media/performance-tuning-materialized-views/output-list.png)

A nagy mennyiségű nézetekkel ugyanaz a lekérdezés sokkal gyorsabban fut a kód módosítása nélkül.  

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).
