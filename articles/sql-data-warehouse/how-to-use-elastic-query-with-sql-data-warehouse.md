---
title: "Az Azure SQL Data Warehouse szolgáltatással rugalmas lekérdezési fogalmak |} Microsoft Docs"
description: "Az Azure SQL Data Warehouse szolgáltatással rugalmas lekérdezési fogalmak"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 4c351d88b31adfa3443dd2231f67bb442f2b8fe0
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Rugalmas lekérdezés használata az SQL Data Warehouse szolgáltatással



Az Azure SQL Data Warehouse szolgáltatással rugalmas lekérdezés lehetővé teszi egy Azure SQL Data Warehouse-példányhoz külső táblák segítségével távolról küldött SQL-adatbázis a Transact-SQL írása. Ezzel a szolgáltatással költségmegtakarítások és a helyzettől függően további performant architektúrát biztosít.

Ez a funkció lehetővé teszi, hogy a két elsődleges forgatókönyv:

1. Tartományelkülönítés
2. Távoli lekérdezés végrehajtása

### <a name="domain-isolation"></a>Tartományelkülönítés

Tartományelkülönítés a hagyományos adatközpont forgatókönyvet hivatkozik. Bizonyos esetekben egy szeretné adja meg az adatok logikai tartományának, amelyek a data warehouse számos okból, beleértve a többi bérlőétől elkülönített, de nem kizárólagosan az alsóbb rétegbeli felhasználóknak:

1. Erőforrás-elkülönítést - SQL-adatbázis megfelelően lett optimalizálva, mint az adatraktár fenntartott nagy elemzési lekérdezések némileg eltérő munkaterheléseket szolgáltató egyidejű felhasználók nagy base szolgálnak. Elkülönítési biztosítja, hogy a megfelelő munkaterhelések vannak szolgálja ki a megfelelő eszközökkel.
2. Biztonsági elkülönítés - egy engedélyezett adatok részhalmazát külön-külön segítségével bizonyos sémák külön.
3. Elszigetelés - adjon meg egy minta adatokat, a "playground" felfedezése, mely éles lekérdezések stb.

Rugalmas lekérdezési biztosíthat egyszerűen válassza ki az SQL data warehouse-adatok megfelelő részhalmazát, és helyezze át egy SQL-adatbázispéldány. Továbbá a elkülönítési nem zárja ki a lehetőséget is lehetővé teszi a érdekesebb "cache" forgatókönyvek távoli lekérdezés végrehajtása.

### <a name="remote-query-execution"></a>Távoli lekérdezés végrehajtása

Rugalmas lekérdezési lehetővé teszi, hogy az SQL data warehouse példányán távoli lekérdezés végrehajtása. Egy nyújthatnak a a legjobb SQL-adatbázis és az SQL data warehouse is megadhat, a kiemelt és ritkán használt adatok a két adatbázis között. Felhasználók is megtarthatják az újabb egy SQL-adatbázis, amely ki tud szolgálni, jelentések és átlagos üzleti felhasználók nagy mennyiségű adatot. További adatok vagy számítási szükséges, ha azonban a felhasználó kiszervezése is része egy SQL az adatraktár példánya, ahol a nagyméretű összesítések dolgozható sokkal gyorsabb és hatékonyabb a lekérdezést.



## <a name="elastic-query-overview"></a>Rugalmas lekérdezési áttekintése

Egy rugalmas lekérdezést, hogy az adatok egy SQL-adatfájl belül található adatraktár-adatbázis SQL-példány számára elérhető használható. Rugalmas lekérdezési lehetővé teszi, hogy az SQL-adatbázis lekérdezések által hivatkozott táblákat a távoli SQL data warehouse-példányt. 

Az első lépés, ha egy külső adatforrás-definíciót, amely az adatraktár SQL példánya, amely meglévő felhasználói hitelesítő adatokat használja az SQL data warehouse hivatkozik. Nem szükségesek a távoli példányon az SQL data warehouse. 

> [!IMPORTANT] 
> 
> Az ALTER ANY külső ADATFORRÁS engedéllyel kell rendelkeznie. Ez az engedély megtalálható az ALTER DATABASE engedéllyel. Az ALTER ANY külső ADATFORRÁS engedélyekre van szükség lehet hivatkozni a távoli adatforrásokat.

Ezután egy távoli külső tábla definíciójának létrehozhatunk olyan adatbázis SQL-példányon, amely az SQL data warehouse egy távoli táblára mutat. A külső tábla használó lekérdezés használata esetén a lekérdezés a külső tábla hivatkozik, az a része zajlik SQL data warehouse-példány feldolgozásra. A lekérdezés befejezése után az eredménykészlet küld vissza a hívó SQL-adatbázispéldány. Rövid útmutató egy rugalmas SQL-adatbázis és az SQL data warehouse-lekérdezés beállításának, tekintse meg a [rugalmas lekérdezési konfigurálja az SQL Data Warehouse szolgáltatással][Configure Elastic Query with SQL Data Warehouse].

Az SQL database rugalmas lekérdezés további információkért lásd: a [Azure SQL Database rugalmas lekérdezési áttekintése][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Ajánlott eljárások

### <a name="general"></a>Általános kérdések

- Távoli lekérdezés-végrehajtás használata esetén győződjön meg arról, Ön csak szükséges oszlopok kiválasztása és a megfelelő szűrők alkalmazása. Nem csak nem ezzel a növekedéssel szükséges számítási, de azt is növeli az eredményhalmaz méretét, és ezért adatok mennyisége, amely kell mozgatni a két példányai között.
- Az SQL Data Warehouse, mind az SQL-adatbázis elemzési célokra adatok karbantartása a fürtözött oszlopcentrikus analytiIcal teljesítmény érdekében.
- Győződjön meg arról, hogy a forrástábla a lekérdezés- és adatátviteli particionáltak.
- Győződjön meg arról, SQL adatbázis-példány használt a gyorsítótár particionáltak részletesebb frissítések és a könnyebb felügyeletét. 
- Ideális esetben használja a PremiumRS adatbázisok, mert a fürtözött oszlopcentrikus és a Premium adatbázisokat a kedvezményes áron IO-igényes munkaterhelések fókusszal indexelő analitikai előnyeit biztosítanak.
- Után terhelés esetén szolgáltatás használ a load vagy az életbelépési dátum azonosító oszlopok upserts a gyorsítótár-forrás egységének fenntartására szolgáló SQL adatbázis-példány a. 
- Hozzon létre egy külön felhasználónevet és egy felhasználó SQL adatbázis távoli bejelentkezési hitelesítő adatait a külső adatforrás SQL data warehouse példányában. 

### <a name="elastic-querying"></a>Rugalmas lekérdezése

- Sok esetben egy célszerű olyan típusú felhőbe archivált táblázatot, ahol a tábla egy része van az SQL-adatbázis mint gyorsítótárazott adatokat a teljesítmény és a többi SQL Data Warehouse tárolt adatok kezeléséhez. Szüksége lesz a két objektum van az SQL-adatbázis: a külső tábla belül SQL-adatbázis az SQL Data Warehouse, és a tábla az SQL-adatbázis, a "gyorsítótárazott" része az alaptáblára hivatkozik. Vegye figyelembe a nézetek létrehozásával, a külső táblázat és a gyorsítótárazott része tetején keresztül mely egyesítésekhez egyaránt táblázatok és szűrőket, amelyek adatokat közzétéve külső táblák az SQL-adatbázis és az SQL Data Warehouse-adatok materializált vonatkozik.

  Képzelje el kell tartani az adatok utolsó évben egy SQL-adatbázispéldány. Két tábla tudunk **kiegészítő Rendelések**, táblák, amely hivatkozik, az adatraktár rendeléseket és **dbo. Rendelések** amely jelenti, hogy a legutóbbi év adat tekinthető meg az SQL-adatbázispéldány belül. Helyett kérni a felhasználókat, kívánja-e egy táblát, vagy egy másik lekérdezni, azt nézetet a mindkét tábla utolsó évben partíció pontján felső keresztül kell létrehozni.

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

  Egy nézet előállított úgy tegyük a lekérdezés fordító határozza meg, hogy be kell-e az adatraktár példánya segítségével a felhasználók lekérdezés választ. 

  Nincs általános elküldése, fordítása, fut, és megköveteli az adatok társított minden egyes rugalmas lekérdezése az adatraktár példánya. Lehet, hogy minden rugalmas lekérdezési csökkenti a feldolgozási tárhelyek és erőforrást használ, tudatában.  


- Ha egy részletekbe menően tárhatják be az adatraktár példánya a eredménykészlet további, fontolja meg egy ideiglenes tábla az SQL-adatbázisban a teljesítmény- és szükségtelen erőforrás-használat megelőzése materializálása azt.

### <a name="moving-data"></a>Adatok áthelyezése 

- Ha lehetséges tartsa adatkezelés megkönnyíti a csak a forrástábla úgy, hogy az adatok adatraktár és az adatbázis példányai között könnyen fenntarthatóvá frissítései.
- Helyezze át az adatokat a partíció szinten kiürítése, és töltse ki a minimálisra csökkenthető a lekérdezés a data warehouse szint és az adatok áthelyezése az adatbázis-példány naprakészen tartása a szemantikájú. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Mikor érdemes az Azure Analysis Services vs SQL-adatbázis kiválasztása

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- A kíván használni a gyorsítótárhoz kis lekérdezések nagy számú elküld egy BI eszközzel
- Lekérdezés-késleltetés kell subsecond
- Jártas kidolgozásában kezelése/modellek az Analysis Services 

#### <a name="sql-database"></a>SQL Database

- Le szeretné kérdezni az SQL gyorsítótár adatok
- Távoli végrehajtás egyes lekérdezések van szüksége
- Nagyobb gyorsítótár-követelmények



## <a name="faq"></a>GYIK

K: használható adatbázisok rugalmas készlethez rugalmas lekérdezéssel belül?

V: Igen. SQL-adatbázisok rugalmas készlethez belül rugalmas lekérdezési használhatja. 

K: van-e a tengelysapka hány adatbázisok rugalmas lekérdezés használata?

V: nincs rögzített nyílt hány adatbázisok rugalmas lekérdezési használható. Azonban minden rugalmas lekérdezés (azok a lekérdezések, amelyek az SQL Data Warehouse találati) számít felé a rendes feldolgozási korlátok.

K: vannak rugalmas lekérdezési szerepet játszó DTU-korlátokon?

V: DTU-korlátokon nincsenek bevezetett bármely eltérően a rugalmas lekérdezés. A normál házirend van, úgy, hogy a logikai kiszolgáló DTU-korlátokon rendelkezik megelőzni a véletlen túlköltekezés az. Ha engedélyezi a rugalmas lekérdezés mellett egy SQL Data Warehouse-példányokhoz több adatbázisok, határaiba a kap váratlanul. Ha ez történik, igényelnie a logikai kiszolgáló DTU-korlát növeléséhez. A kvótája által [a támogatási jegy létrehozása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) választja *kvóta* a kérelem típusa

K: használhatok sor szintű biztonsági/dinamikus adatok maszkolása rugalmas lekérdezéssel?

V: az ügyfelek, akik fejlettebb funkciókat az SQL Database szolgáltatást használna ehhez első áthelyezése és az adatok tárolása az SQL-adatbázisban. A külső táblák segítségével lekérdezett adatok jelenleg sorszintű biztonság vagy DDM nem vonatkozik. 

K: írhatok a saját SQL database-példányt az adatraktár-példánya számára?

A: Ez a funkció jelenleg nem támogatott. Látogasson el a [visszajelzés] [ Feedback page] létrehozása/szavazásra erre a funkcióra Ha ez egy olyan beállítás, tekintse meg a jövőben szeretné. 

K: használhatok geometriai/földrajzi hely például a térbeli típusok?

V: tárolhatja a térbeli típusok az SQL Data Warehouse varbinary(max) értékként. Ha ezekben az oszlopokban rugalmas lekérdezéssel, alakíthatja át őket a megfelelő típusú futásidőben.

![a térbeli típusok](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->
