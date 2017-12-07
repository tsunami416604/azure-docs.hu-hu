---
title: "Ajánlott eljárások az Azure SQL Data Warehouse-hoz | Microsoft Docs"
description: "Javaslatok és ajánlott eljárások, amelyeket érdemes tudni az Azure SQL Data Warehouse-megoldások fejlesztésekor. Ezek segítségével a megoldások jobban sikerülhetnek."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: f24dc2600bec8b7086ee34a960e777a8a1b288ad
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Ajánlott eljárások az Azure SQL Data Warehouse-hoz
Ez a cikk számos ajánlott eljárás gyűjteménye, amelyek segítenek optimális teljesítményt kihozni az Azure SQL Data Warehouse-ból.  A cikkben található fogalmak némelyike könnyen elmagyarázható, míg mások összetettebb fogalmak, amelyeket csak nagy vonalakban mutatunk be.  A cikk célja, hogy némi alapszintű útmutatással és a legfontosabb dolgok hangsúlyozásával segítse Önt az adattárház létrehozásában.  Minden szakasz röviden bemutat egy fogalmat, majd részletesebb cikkekhez irányít át, amelyek a fogalmat alaposabban is körüljárják.

Ha csak most kezdi el használni az Azure SQL Data Warehouse-t, ne hagyja, hogy ez a cikk megijessze.  A témakörök nagyjából fontossági sorrendben követik egymást.  Ha kezdetnek csak az első néhány fogalommal foglalkozik, az tökéletesen elegendő.  Miután már jobban megismerte és kényelmesebben tudja használni az SQL Data Warehouse-ot, folytassa néhány újabb fogalommal.  Nem fog sokáig tartani, amíg a dolgok világosan összeállnak.

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által
Az SQL Data Warehouse egyik legfőbb jellemzője, hogy ha nem használja, felfüggesztheti a szolgáltatást, ezzel leállítva a számítási erőforrások számlázását.  Egy másik fontos jellemző az erőforrások méretezhetősége.  A felfüggesztés és a méretezés az Azure Portalon keresztül vagy PowerShell-parancsok használatával lehetséges.  Ezen funkciók elsajátítása nagy mértékben csökkentheti az adattárházzal kapcsolatos kiadásait, amikor azt épp nem használja.  Ha azt szeretné, hogy az adattárháza folyamatosan rendelkezésre álljon, érdemes lehet azt a legkisebb méretre, a DW100-ra skálázni a felfüggesztés helyett.

Lásd még: [Számítási erőforrások használatának felfüggesztése][Pause compute resources], [Számítási erőforrások használatának folytatása][Resume compute resources], [Számítási erőforrások méretezése].

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt
Ha felfüggeszti vagy méretezi az SQL Data Warehouse-t, a felfüggesztési vagy méretezési kérelem elindításakor a színfalak mögött a lekérdezések megszakadnak.  Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni.  **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.**  A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása.  Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa.  Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [Tranzakciók megismerése][Understanding transactions], [Tranzakciók optimalizálása][Optimizing transactions]

## <a name="maintain-statistics"></a>Statisztikák karbantartása
Az SQL Serverrel ellentétben az SQL Data Warehouse nem észleli, hozza létre vagy frissíti az oszlopok statisztikáit, hanem ezek manuális karbantartása szükséges.  Noha mindezen a jövőben változtatni tervezünk, egyelőre az SQL Data Warehouse-csomagok optimalizálása érdekében saját kezűleg kell karbantartania a statisztikákat.  Az optimalizáló által létrehozott csomagok csak annyira jók, amennyire az elérhető statisztikák azok.  **A statisztikák megismerésének egyik legjobb módja a mintastatisztikák létrehozása minden oszlopról.**  Ugyanilyen fontos a statisztikák frissítése is, mivel az adatokban jelentős változások történhetnek.  Ennek hagyományos módszere, ha naponta vagy minden betöltés után frissíti a statisztikákat.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, lehet, hogy körültekintőbben kell kiválasztania, mely oszlopok rendelkezzenek statisztikákkal vagy melyek igényelnek gyakori frissítést.  Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legnagyobb előnnyel az jár, ha az összekapcsolások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit kéri le.**

Lásd még a [táblastatisztikák kezelésével][Manage table statistics], a [CREATE STATISTICS][CREATE STATISTICS] és az [UPDATE STATISTICS][UPDATE STATISTICS] utasítással foglalkozó témaköröket.

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe
A kis táblák INSERT utasítással végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is kielégítheti az igényeit, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Ha azonban egy nap során több ezer vagy több millió sort kell betöltenie, észreveheti, hogy az egyszeres INSERT utasítások nem feltétlenül bizonyulnak elegendőnek.  Ehelyett érdemes olyan folyamatokat kifejleszteni, amelyek egy fájlba írnak, amelyet egy időről időre aktiválódó másik folyamat rendszeresen betölt.

Lásd még: [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével
Az SQL Data Warehouse az adatok több eszközzel (például az Azure Data Factoryval, a PolyBase-el és a BCP-vel) végzett betöltését és exportálását is támogatja.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  Amikor nagy mennyiségű adatot tölt be vagy exportál, vagy gyors teljesítményre van szükség, a PolyBase a legjobb választás.  A PolyBase úgy van kialakítva, hogy kihasználja az SQL Data Warehouse MPP (Massively Parallel Processing, nagymértékben párhuzamos feldolgozási) architektúráját, így bármely más eszköznél sokkal gyorsabban tölti be és exportálja az adatokat.  A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók.  **A CTAS használata minimalizálja a tranzakciók naplózásának mértékét, és ez az adatok betöltésének leggyorsabb módja.**  Az Azure Data Factory is támogatja a PolyBase-betöltéseket.  A PolyBase többféle fájlformátumot támogat, beleértve a Gzip-fájlokat is.  **A gzip szöveges fájlok használatakor az átviteli teljesítmény maximalizálása érdekében tördelje a fájlokat 60 vagy több fájllá a betöltés párhuzamosságának növelése érdekében.**  A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat.

Lásd még: [Adatok betöltése][Load data], [Útmutató a PolyBase használatához][Guide for using PolyBase], [Azure SQL Data Warehouse betöltési minták és stratégiák][Azure SQL Data Warehouse loading patterns and strategies], [Adatok betöltése az Azure Data Factoryvel][Load Data with Azure Data Factory], [Adatok áthelyezése az Azure Data Factoryvel][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése
Míg a PolyBase, más néven a külső táblák használata lehet az adatok betöltésének leggyorsabb módja, lekérdezésekhez nem ez a legjobb megoldás. Az SQL Data Warehouse PolyBase-táblák egyelőre még csak az Azure-blob fájlokat támogatják. Ezekhez a fájlokhoz nem tartoznak külön számítási erőforrások.  Ennek eredményeképpen az SQL Data Warehouse nem tudja kiszervezni a munkát, és be kell olvasnia a teljes fájlt úgy, hogy betölti a tempdb adatbázisba.  Ezért ha több olyan lekérdezése is van, amelyek ennek a fájlnak az adatait kérik le, érdemesebb egyszer betölteni az adatokat, és úgy beállítani a lekérdezéseket, hogy a helyi táblát használják.

Lásd még a [PolyBase használatára vonatkozó útmutatót][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  Ez a magyarázat éppen csak bepillantást nyújt a témakörre vonatkozó tudnivalókba. Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi hivatkozások további információt tartalmaznak arról, hogyan javíthatja a teljesítményt az elosztási oszlop kiválasztása, valamint arról, hogyan határozhat meg elosztott táblát a CREATE TABLES utasítás WITH záradékának használatával.

Lásd még a [táblák áttekintésével][Table overview], [a táblaelosztással][Table distribution], [a táblaelosztás kiválasztásával][Selecting table distribution], a [CREATE TABLE][CREATE TABLE] és a [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasítással foglalkozó témaköröket.

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása nagyon hatásos eszköz lehet az adatok partícióváltáson keresztüli megőrzéséhez vagy a vizsgálatok partíciókizárással végzett optimalizálásához, a túl sok partíció lelassíthatja a lekérdezéseket.  Az SQL Serveren jól működő részletesebb particionálási stratégia nem feltétlenül működik hasonló hatékonysággal az SQL Data Warehouse-ban.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a háttérben az SQL Data Warehouse 60 adatbázisba particionálja az adatait, így ha 100 partícióval rendelkező táblát hoz létre, ez 6000 partíciót eredményez.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával][Table partitioning] foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  Ha például olyan INSERT utasítása van, amelynek a futtatása várhatóan 1 órát vesz igénybe, ha megoldható, ossza 4, egyenként 15 percig futó részre az INSERT utasítást.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  A nem particionált táblák esetén érdemes lehet CTAS utasítással kiírni a táblában megőrizni kívánt adatokat a DELETE használata helyett.  Noha egy CTAS utasítás ugyanannyi időt vesz igénybe, sokkal biztonságosabban futtatható művelet, mivel minimális tranzakciónaplózást igényel, és szükség esetén gyorsan megszakítható.

Lásd még a [tranzakciók megismerésével][Understanding transactions], a [tranzakciók optimalizálásával][Optimizing transactions], a [tábla particionálásával][Table partitioning], valamint a [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] és [Create table as select (CTAS)][Create table as select (CTAS)] utasításokkal foglalkozó témaköröket.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
Ha a DDL meghatározásakor az adatokat támogató legkisebb adattípust használja, nagyban javíthatja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével][Table overview], a [tábla adattípusaival][Table data types], valamint a [CREATE TABLE][CREATE TABLE] utasítással foglalkozó témaköröket.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz
Amikor ideiglenesen tárol adatokat az SQL Data Warehouse-ban, a halomtábla használata a teljes folyamatot felgyorsíthatja.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába.  Azzal is tovább gyorsíthatja a folyamatot, ha az adatokat állandó tároló helyett ideiglenes táblába tölti.  Az ideiglenes táblák „#” karakterrel kezdődnek, és csak az őket létrehozó munkamenet által érhetők el, így csak korlátozott esetekben használhatók.   A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

Lásd még az [ideiglenes táblákkal][Temporary tables], illetve a [CREATE TABLE][CREATE TABLE] és [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasításokkal foglalkozó témaköröket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
A fürtözött oszlopcentrikus indexek az adatok tárolásának leghatékonyabb módjai közé tartoznak az SQL Data Warehouse-ban.  Alapértelmezés szerint a táblák az SQL Data Warehouse-ban fürtözött oszlopcentrikusként jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel][Table indexes] foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait][Causes of poor columnstore index quality] ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  Mivel fontos az oszlopcentrikus szegmensek minősége, érdemes lehet olyan felhasználói azonosítókat használni, amelyek az adatbetöltéshez kapcsolódó közepes vagy nagy erőforrásosztályba tartoznak. Alacsonyabb [szolgáltatásszintek](performance-tiers.md#service-levels) használata esetén érdemes nagyobb erőforrásosztályt hozzárendelni a betöltést végző felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem küldenek adatokat tömörített oszlopcentrikus szegmensbe addig, amíg nincs több mint 1 millió sor táblánként és az SQL Data Warehouse-táblák nincsenek 60 táblává particionálva, az oszlopcentrikus táblák használata nem célravezető a lekérdezéseknél, ha a tábla kevesebb, mint 60 millió sorral rendelkezik.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel][Table indexes], az [oszlopcentrikus indexek áttekintésével][Columnstore indexes guide] és az [oszlopcentrikus indexek újjáépítésével][Rebuilding columnstore indexes] foglalkozó témaköröket.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében
Az SQL Data Warehouse erőforráscsoportokat használ arra, hogy memóriát foglaljon le a lekérdezésekhez.  Először minden felhasználó kis erőforrásosztályhoz van rendelve, amely elosztásonként 100 MB memóriát biztosít.  Mivel mindig 60 elosztás van és mindegyik elosztás minimum 100 MB-ot kap, a rendszeren belül összesen 6000 MB, vagyis majdnem 6 GB memória van lefoglalva.  Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Néhány lekérdezésnél, például amelyek csak vizsgálati műveleteket végeznek, ezeknek a használata nem jár további előnnyel.  A nagyobb erőforrásosztályok használata hatással van az egyidejűség mértékére. Ezt érdemes figyelembe venni, mielőtt az összes felhasználót nagy erőforrásosztályba helyezné.

Lásd még az [egyidejűséggel és a számítási feladatok kezelésével][Concurrency and workload management] foglalkozó témakört.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség mértékének növelése érdekében
Ha azt látja, hogy megnőtt a felhasználói lekérdezések késleltetése, előfordulhat, hogy a felhasználók nagyobb erőforrásosztályban futnak és sok egyidejű helyet foglalnak le, így más lekérdezések sorban állásra kényszerülnek.  Ha a `SELECT * FROM sys.dm_pdw_waits` parancs futtatásakor a rendszer sorokat ad vissza, láthatja, hogy a felhasználók lekérdezései sorban állnak.

Lásd még az [egyidejűséggel és a számítási feladatok kezelésével][Concurrency and workload management], valamint a [sys.dm_pdw_waits][sys.dm_pdw_waits] elemmel foglalkozó témakört.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel
Az SQL Data Warehouse több DMV-vel is rendelkezik, amelyekkel a lekérdezések futtatása figyelhető meg.  A megfigyeléssel foglalkozó alábbi témakör részletes útmutatót szolgáltat ahhoz, hogyan nézheti meg egy futtatott lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást.

Lásd még a [számítási feladatok DMV-vel végzett megfigyelésével][Monitor your workload using DMVs], valamint a [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] és [sys.dm_pdw_waits][sys.dm_pdw_waits] elemekkel foglalkozó témaköröket.

## <a name="other-resources"></a>Egyéb erőforrások
Lásd még az általános problémákat és megoldásokat tartalmazó, [hibaelhárítással][Troubleshooting] foglalkozó témakört.

Ha nem találta meg a cikkben, amit keresett, próbálkozzon az oldal bal oldalán található „Dokumentumok keresése” mezővel. Az itt megadott kifejezésre a rendszer az összes Azure SQL Data Warehouse-dokumentumban rákeres.  Az [Azure SQL Data Warehouse MSDN fórum][Azure SQL Data Warehouse MSDN Forum] azért jött létre, hogy kérdéseket tehessen fel más felhasználóknak és az SQL Data Warehouse termékcsoporttal foglalkozó csapatnak.  Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább a Stack Overflow-ra vonatkozó kérdéseket szeretne feltenni, van egy [Azure SQL Data Warehouse Stack Overflow fórumunk][Azure SQL Data Warehouse Stack Overflow Forum] is.

A szolgáltatással kapcsolatos kéréseit az [Azure SQL Data Warehouse visszajelzési][Azure SQL Data Warehouse Feedback] oldalán küldheti be.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Load data]: ./sql-data-warehouse-overview-load.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ./sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Számítási erőforrások méretezése]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/
