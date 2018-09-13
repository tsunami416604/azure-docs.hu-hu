---
title: Rugalmas lekérdezés – hozzáférés az adatokhoz az Azure SQL Data Warehouse, Azure SQL Database-ből |} A Microsoft Docs
description: Ismerje meg, ajánlott eljárások a rugalmas lekérdezés adatok elérését az Azure SQL Data Warehouse, Azure SQL Database-ből.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: d861e1d4cd891e1f1e1be3209ae4dfdbf4420165
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718298"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Ajánlott eljárások az Azure SQL Database rugalmas lekérdezése a adatok elérését az Azure SQL Data Warehouse
Ismerje meg, ajánlott eljárások a rugalmas lekérdezés adatok elérését az Azure SQL Data Warehouse, Azure SQL Database-ből. 

## <a name="what-is-an-elastic-query"></a>Mi az a rugalmas lekérdezés?
Az a rugalmas lekérdezés lehetővé teszi, hogy a T-SQL és a külső táblák segítségével írhat egy lekérdezést az Azure SQL Database, az Azure SQL data warehouse távolról küldött. Ezzel a funkcióval biztosít költségmegtakarítást és további nagy teljesítményű architektúrák, a forgatókönyvtől függően.

Ez a funkció lehetővé teszi, hogy a két fő forgatókönyv:

1. Tartományelkülönítés
2. Távoli lekérdezés végrehajtása

### <a name="domain-isolation"></a>Tartományelkülönítés

Tartományelkülönítés a klasszikus adatok adatközpont forgatókönyv hivatkozik. Bizonyos esetekben egy érdemes az adatok logikai tartomány nyújtson az alsóbb rétegbeli felhasználók, amelyek a különböző okok miatt, beleértve az adatraktár többi elszigetelt, de nem korlátozódik:

1. Erőforrás-elkülönítést – SQL database nagy alapjául szolgáló némileg különböző számítási feladatok, mint a nagy méretű elemzési lekérdezést, amely az adatraktár számára van fenntartva egyidejű felhasználók kiszolgálása érdekében optimalizáltuk. Elkülönítés biztosítja, hogy a megfelelő számítási feladatok szolgáltatja a megfelelő eszközöket.
2. Biztonsági elkülönítés – külön-külön-külön az egyes sémák keresztül egy engedéllyel rendelkező adatok részhalmazát.
3. Elszigetelés – adjon meg egy minta adatkészletet, egy "playground" fedezheti fel az éles lekérdezések stb.

Rugalmas lekérdezés is lehetővé teszi, hogy egyszerűen válassza ki az SQL data warehouse-adatok részhalmazainak, és helyezze át egy SQL database-példányban. Továbbá az elkülönítés nem zárja ki is ezekben az érdekesebb "gyorsítótár" forgatókönyveket lehetővé tevő távoli lekérdezés végrehajtása.

### <a name="remote-query-execution"></a>Távoli lekérdezés végrehajtása

Rugalmas lekérdezés lehetővé teszi a távoli lekérdezés végrehajtása a egy SQL data warehouse-példányhoz. A legjobb, az SQL database és az SQL data warehouse által a két adatbázis között a gyakran és ritkán használt adatok elkülönítését egy képes használni. Felhasználók tartani tudja a legfrissebb adatokat egy SQL-adatbázis, amely képes teljesíteni, jelentések és átlagos az üzleti felhasználók nagy mennyiségű belül. Azonban több adat vagy számítási van szükség, amikor egy felhasználó kiszervezheti a lekérdezés, amelyen nagyméretű összesítések feldolgozható, sokkal gyorsabban és hatékonyabban SQL data warehouse példányra részét.

## <a name="elastic-query-process"></a>Rugalmas lekérdezés folyamata
Az a rugalmas lekérdezés segítségével adatok belül egy SQL data warehouse az SQL database-példányok számára elérhetővé tenni. Rugalmas lekérdezés lehetővé teszi, hogy egy SQL database-ből lekérdezések által hivatkozott táblákat a távoli SQL data warehouse-példányt. 

Az első lépés, ha egy külső adatforrás-definíciót, amely az SQL data warehouse-példányhoz, meglévő felhasználói hitelesítő adatokat használó belül az SQL data warehouse hivatkozik. Módosítások nem szükségesek a távoli példányon az SQL data warehouse. 

> [!IMPORTANT] 
> 
> Az ALTER ANY EXTERNAL DATA SOURCE engedéllyel kell rendelkeznie. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY EXTERNAL DATA SOURCE engedélyekre van szükség lehet hivatkozni a távoli adatforrásokat.

Ezután hozzon létre egy SQL-adatbázispéldányban, amely a távoli táblára mutat abban az SQL data warehouse egy távoli külső tábla definíciójában. Ha egy lekérdezést használ egy külső táblát, feldolgozásra a lekérdezés a külső tábla hivatkozó részének megkapja az SQL data warehouse-példányhoz. A lekérdezés befejeztével az eredményhalmaz küld vissza a hívó az SQL database-példány. Az SQL database és az SQL data warehouse között a rugalmas lekérdezés beállításának rövid útmutató: a [rugalmas lekérdezés konfigurálása az SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Az SQL database Elastic Query további információkért lásd: a [Azure SQL Database rugalmas lekérdezés – áttekintés][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Ajánlott eljárások
Ajánlott eljárások segítségével hatékonyan elastic query használata.

### <a name="general"></a>Általános kérdések

- Távoli lekérdezés végrehajtása használata esetén győződjön meg arról, Ön csak szükséges oszlopok kiválasztásával, és a megfelelő szűrők alkalmazása. Nem csak nem ezzel a növekedéssel szükséges számítási, de azt is méretét is megnöveli az eredményhalmaz, ezért az adatok mennyisége kell helyezhetők át a két példánnyal.
- A fürtözött oszlopcentrikus analytiIcal teljesítmény adatok elemzési célokból az SQL Data Warehouse és az SQL Database karbantartása.
- Győződjön meg arról, hogy a forrástábla a lekérdezés- és adatátviteli vannak particionálva.
- Győződjön meg arról, az SQL database-példányok gyorsítótárként használt vannak particionálva, engedélyezheti a részletesebb frissítések és könnyebb kezelhetőség. 
- Ideális esetben használja a PremiumRS-adatbázisok, mert fürtözött oszlopcentrikus indexelés és a hangsúly ezúttal az i/o-igényes számítási feladatokhoz a prémium adatbázisok kedvezményes áron analitikai előnyeinek tartalmaznak.
- Után betöltődik a terhelés vagy hatályba lépés dátuma azonosító oszlop a gyorsítótár-forrás integritásának fenntartása a SQL Database-példányok upserts használatára. 
- Egy külön és felhasználó létrehozásához az SQL adattárház-példány számára az SQL database távoli bejelentkezéshez megadott hitelesítő adatokkal a külső adatforrásban. 

### <a name="elastic-querying"></a>Rugalmas lekérdezése

- Sok esetben egy érdemes egy kiterjesztett táblára, ahol a táblázat egy része van az SQL Database mint teljesítmény érdekében az SQL Data Warehouse tárolja az adatok a rest-tel gyorsítótárazott adatok típusú kezelése. Az SQL Database két objektum szükséges: egy külső táblát az SQL Database, amely az SQL Data warehouse-ba, és a "gyorsítótárazott" részét a tábla az SQL Database az alaptáblára hivatkozik. Fontolja meg a nézetek létrehozásával keresztül felső részén a gyorsítótárazott része a tábla és a külső tábla mely egyaránt táblázatok, és alkalmazza a szűrőket, amelyek a tényleges táblán alapuló SQL Database és az SQL Data Warehouse adatok külső táblák keresztül közzétett adatokat.

  Tegyük fel, hogy meg szeretné tartani a legutóbbi évnyi adat egy SQL database-példányban. A **kiegészítő Rendelések** táblahivatkozásokat az adatraktár orders táblákat. A **dbo. Rendelések** jelenti. a legutóbbi év adat tekinthető meg az SQL database-példány belül. Ahelyett, hogy kívánja-e a egy olyan táblát, vagy a másik lekérdezést kell megadniuk, a nézet létrehozása felső részén a mindkét táblázatot a legutóbbi év partíció ponton keresztül.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Nézet előállított úgy tegyük a lekérdezés fordító határozza meg, hogy kell-e a felhasználók lekérdezés választ a data warehouse-példányhoz használja. 

  Nincs elküldése, fordítás, futtatása és mozgó adatokat az adattárházpéldányt a minden rugalmas lekérdezéshez társított, általános. Lehet, hogy minden egyes rugalmas lekérdezés beleszámít az egyidejű helyet foglalnak le, és erőforrást használ, tudatában.  


- Ha részletes elemzést egy, a data warehouse-példányhoz az eredménykészlet további, materializálása azt egy ideiglenes tábla az SQL Database teljesítményét, a szükségtelen erőforrás-használat megelőzése érdekében érdemes.

### <a name="moving-data"></a>Adatok áthelyezése 

- Ha lehetséges tartsa meg az adatkezelést egyszerűbb, mivel csak a forrástábla úgy, hogy a frissítések állnak a data warehouse és az adatbázis-példányok között könnyen fenntartható.
- Adatok áthelyezése a partíció szintjén a kiürítési, és kitöltés szemantikáját, hogy minimálisra csökkentheti a lekérdezés az adatokon adatraktár-szintű és és naprakész állapotban tarthatja az adatbázispéldányt áthelyezett adatok mennyisége. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Ha az Azure Analysis Services és az SQL Database kiválasztása

Az Azure Analysis Services:

- Azt tervezi, hogy a gyorsítótár használata, amely a nagy számú kis lekérdezéseket küld Üzletiintelligencia-eszköze
- Meg kell subsecond Lekérdezések késése
- Kezelés/fejlesztésébe modellek az Analysis Services hasznosítani 

Használja az Azure SQL adatbázis-mikor:

- Szeretné lekérdezni a gyorsítótárazott adatok SQL-lel
- Távoli végrehajtás van szüksége bizonyos lekérdezések
- Nincsenek nagyobb gyorsítótár-követelmények

## <a name="faq"></a>GYIK

K: használhatok adatbázisokat rugalmas lekérdezés a rugalmas készletben lévő?

V: Igen. SQL-adatbázisok, rugalmas készletben lévő rugalmas lekérdezés használhatja. 

K: van egy rugalmas lekérdezés használható hány adatbázist tartozó napi korlát?

V: nincs nem rögzített cap hány adatbázist a rugalmas lekérdezés is használható. Azonban minden egyes Elastic Query (lekérdezés, nyomja le az SQL Data Warehouse) beleszámít normál egyidejűségi korlátját.

K: az Elastic Query érintett DTU korlátozva van?

V: DTU-korlátokon nincsenek bármely eltérően az Elastic Query szinttel. A normál házirend van, úgy, hogy a logikai kiszolgálók DTU korlátokkal rendelkeznek megelőzni a véletlen túlköltekezés körülményeket. Ha engedélyezi a rugalmas lekérdezés mellett egy SQL Data Warehouse-példányokhoz több adatbázist, váratlanul előfordulhat, hogy eléri a korlátot. Ha ez történik, a logikai kiszolgáló dtu-k korlátjának növelését, kérelmet kell benyújtania. Növelheti a kvóta által [támogatási jegy létrehozása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) és kiválasztásával *kvóta* a kérelem típusaként

K: használhatok sor szintű biztonsági/dinamikus adatok maszkolása a rugalmas lekérdezés?

V: ügyfelek, akik szeretne használni a speciális biztonsági funkciók az SQL Database teheti első áthelyezésével és az adatok tárolása az SQL Database-ben. A külső táblák használatával lekérdezett adatok jelenleg sorszintű biztonság vagy DDM nem vonatkozik. 

K: I írhat saját SQL database-példány a data warehouse-példányhoz?

V: jelenleg ez a funkció nem támogatott. Látogasson el a [Visszajelzésküldő oldala] [ Feedback page] , hozzon létre vagy szavazzon ezt a funkciót, ha ez a funkció, a jövőbeli szeretné. 

K: használhatok térbeli típusok, pl. geometry és geography?

V: tárolhatók térbeli az SQL Data Warehouse, a varbinary(max) értéket. Amikor ezeket az oszlopokat rugalmas lekérdezés használatával lekérdezheti, átválthat őket a megfelelő típusú futásidőben.

![Térbeli típusok](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


