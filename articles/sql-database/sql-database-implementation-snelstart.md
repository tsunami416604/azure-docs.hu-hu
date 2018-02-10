---
title: "Az Azure SQL adatbázis Azure esettanulmány - Snelstart |} Microsoft Docs"
description: "Hogyan SnelStart SQL adatbázist használ az üzleti szolgáltatások 1000 új Azure SQL-adatbázisok havi gyakorisággal gyorsan kibontva megismerése"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: a5100941675b6bdd1934bfcabe10172b4949fff9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Az Azure-SnelStart gyorsan bővített 1000 új Azure SQL-adatbázisok havi gyakorisággal az üzleti szolgáltatás
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart Hollandia népszerű pénzügyi - és üzleti-kezelési szoftver a kis - és közepes méretű vállalkozások számára (SMB) hoz. A személyzet 110 alkalmazottak, beleértve az informatikai részleg 35 által kiszolgált 55,000 ügyfeleinek. Asztali szoftverek áthelyezése egy szoftver,--szolgáltatás (SaaS) épülő Azure ajánlat, által az SnelStart végrehajtott beépített szolgáltatások többsége megszokott környezetben a C# segítségével, és a teljesítmény és méretezhetőség optimalizálása sem felett vagy alatt-átadásának rugalmas készleteket használó vállalatok által felügyeletének automatizálására. SnelStart Azure segítségével biztosítja a helyszíni és a felhő közötti áthelyezése ügyfelek rugalmasság.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Miért SnelStart kibővített szolgáltatások az asztalról a felhőbe
> "Az Azure-ral működő azt jelenti, hogy azt is kézbesíti szoftver gyorsabb, gyorsan reagálni az ügyfél terhelésekig és méretezhető megoldás, amikor iránti igények kielégítése érdekében."
> 
> – Henry lett, a szoftver felelős mérnök
> 
> 

SnelStart lefutott egy sikeres szoftver üzleti évig, egy hagyományos fejlesztési modellel: code csomagot, küldje el, majd ismételje meg. Adott idő alatt, a módosítás következményeivel tempójában nőtt, gyorsabb és gyorsabb. Szabályzat gyakran változó, és ügyfelek pénzügyi rekordok feldolgozásához, és együttműködik a könyvelők és kormányzati tartani ezeket a módosításokat a könnyebb módja szükséges.

"Vevők szállítási szoftver volt költséges és korlátozó," Henry lett, a szoftver felelős mérnök megfelelően. "Éles csomagolás és szállítási költségek korlátozott azt sikerült a gyakoriságát kibocsátási szoftver. Le kellett rendszeres szállítására csomag frissítéseinek, de, amely teszik a felhasználók változó igényeihez rögzített. Azt sikerült soha nem biztos lehet benne, hogy ügyfeleink a termék a legújabb verzióra frissíteni. Ezért le kellett támogatja több verzióit, így nagyon nehéz lenne, valamint a támogatási feladat."

Nincs ad hozzá, "program és a kiadás frissítéseket egy gyorsított, hogy meg akartunk lépést, így azt nem sikerült innovációját gyorsabb és az ügyfelek érdekében új szolgáltatások létrehozására. Is meg akartunk egy módszerre, amellyel a felhasználók üzleti-címfelügyeleti szükségletek egyszerűsítése érdekében további folyamatok automatizálása."

A SnelStart az volt a megoldás számára a szolgáltatások egy felhőalapú Szolgáltatottszoftver-szolgáltató váljon. Az Azure SQL Database platform segített SnelStart érheti el a fő az informatikai részleg terhelését, amely egy infrastruktúra--a-szolgáltatásként (IaaS) megoldás lenne szükség nélkül.

A felhő-modell azt is lehetővé teszi, hogy a SnelStart javítsa ki a hibákat, és új lehetőségeket biztosítanak, gyorsan, anélkül, hogy az ügyfelek letöltése és szoftverfrissítésre kellene. A következők szerint van, a "Using Azure cloud services csomag lehetővé teszi, hogy gyorsan a változó követelmények harmadik felek által kezelhető. Küldje el az új verzió ügyfelek több ezer ahelyett, hogy módosíthatja harmadik felek által igényelt új formátumok az asztali alkalmazás által küldött adatokat."

## <a name="a-modern-company-with-traditional-roots"></a>A hagyományos gyökérrel rendelkező modern vállalati
SnelStart szerény gyökerek 1964, amikor a is indítja el a vállalati zenei szolgáltatás részek a gyártó a elrendeli a modern, a gyors, a csúcstechnológiai üzleti. A szív SnelStart szoftver üzleti valóban lépések során a személyi számítógép elterjedése 1980-as években, a zúzása. A vállalat jobb megoldás, mint a nyilvántartási szoftverrel időpontjában, szükségesek, így a saját kezébe kérdések által. A vállalat 1982, mi végül válna SnelStart nyilvántartási szoftver építkezve hozott létre. A Start és a szoftver az egyszerűség és sebesség lett admired –, hogy a vállalati végül fókusz megváltozott, és magát a szoftver vállalatba reinvented túl nagy.

SnelStart 1995-ben jelent meg az első könyvelés alkalmazás Windows. Az alkalmazás, a Microsoft Visual Basic 1.0-s és a Microsoft Access-adatbázisok épül segített nő az ügyfél több mint 5000 felhasználója alap.

Napjainkban SnelStart egy fő szolgáltató holland SMB és az önálló dinamikus irányuló üzleti-felügyeleti alkalmazás és az üzletági (LOB). Carlo Kuip, informatikai rendszerfejlesztők szerint, mert "célunk 100 %-os automation üzleti-felügyeleti szolgáltatások adhatunk ügyfeleink számára."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Teljesítmény és a rugalmas készletek költséget optimalizálása
SnelStart rugalmas készletek nagyméretű korai alkalmazó volt. Rugalmas készletek segít a vállalat költségek korlátozására és a teljesítményre vonatkozó követelmények hatékonyabban kezelheti. A következők szerint van "rugalmas készletek használatával azt is optimalizálhatja teljesítményét a túlzott kiosztása nélkül a felhasználók igényeit. Ha le kellett kiépítése csúcsterhelés alapján, elég költséges lenne. Ehelyett megoszthatók az erőforrások között, több alacsony kihasználtságú adatbázisok lehetővé teszi, hogy is végez, és költséghatékony megoldás létrehozása. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Az Azure SQL-adatbázisok súgó containerize elkülönítési és biztonsági adatait
Az Azure SQL Database lehetővé teszi, hogy a SnelStart egyszerűen és könnyen áthelyezése ügyfelek helyszíni üzleti-felügyeleti adatokat az Azure-bA. Az Azure SQL Database egy kényelmes tároló, amely elszigetelésére, a határ hitelesítési, engedélyezési és egyszerű biztonsági mentési és visszaállítási képességeket biztosítja. Adatbázisok jól alkalmazható fogalmi modell üzleti felügyeleti adja meg. Megfelelően Carlo Kuip, informatikai rendszerfejlesztők "tartalmazhatnak bizalmas adatokat, amelyek a kritikus fontosságú üzleti a tároló határon belül, és egy elkülönített adatbázis tárolási elemeket biztosít védelme. Azt is fiókonkénti az adatbázis szintjén, és anélkül, hogy az adatbázis-rendszergazdák (DBAs) személyzeti még automatikus kezelését és ezeknek az adatbázisoknak a kibővített."

Az SQL Data Warehouse is szerepet játszik a SnelStart biztonságot és felügyeletet szövegegység hogy segítséget nyújt a vállalati gyűjt telemetrikus adatokat, például a behatolás-észlelés, a felhasználó naplózása és a kapcsolatokat.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure terhet eltávolítja az, hogy a fejlesztők is időt több érték továbbítása
Az Azure platformon modell infrastruktúra terhelés eltávolítva, és C# kezelési kódtárakat telepítések automatizálásához SnelStart engedélyezve. Kuip szerint, mert "sikerült és egyidejűleg növelni a méretezhetőséget, a sebesség és a vész helyreállítási beállítások érhetők el az ügyfelek az aktuális műveletek nagyon kis személyzet nő. A shift erőforrások új szolgáltatások és funkciók helyett csak a meglévő kódot tartani az új szabályoknak vagy kódokat frissítése felszabadulnak szolgáltatások fejlesztéséhez." Hozzáad, "Ha felügyeletének automatizálására, ezzel az ajánlat lehet több érték képes biztosítani az ügyfelek számára anélkül, hogy a műveleti személyzet nagy beruházásokat SaaS." Például az Azure és a rugalmas készletek használatával SnelStart tudta felvenni új szolgáltatások, például bankokhoz, sokkal hatékonyabban ügyféladatok integrációja számos új számlázási szolgáltatások kisvállalati átvilágítást és e-mailek szolgáltatások.

> "Csak az első néhány hónappal 2016, azt az Azure SQL Database telepítések körülbelül 5,500 legfeljebb 12 000 kibontva, és azt jelenleg visszaigazolása havonta körülbelül 1000 adatbázisok."
> 
> – Henry lett, a szoftver felelős mérnök
> 
> 

Adatbázis-felügyeleti további automatizálható, ha a rugalmas feladatok szolgáltatással. Kuip szerint, mert "magas Köszönjük adatbázisok az SQL DB [kiszolgáló] példányán automatikus felderítését." Ez lehetővé teszi a felügyeleti műveletek végrehajtása a dinamikusan növekvő vásárlói bázisunk nélkül többletterhelést között SnelStart.

SnelStart is fejleszti az API-k, amelyek a felhasználói adatok és a harmadik féltől származó szoftverek partnerek által létrehozott alkalmazások közötti közvetítőként működik. Kuip állapotok, mint "Ez az API lehetővé teszi más gyártók funkció hozzáadása szoftvereket, például az adatbevitel számlák és egyéb dokumentumokat kiküszöbölése." Az ügyfelek már nem kell manuálisan írja be a számlák azok kisvállalati nyilvántartási szoftver; a SnelStart szoftver közvetlenül cserélnek a szükséges információkat. Ez lehetővé teszi az ügyfelek az üzleti-felügyeleti feladatok és az ökoszisztéma, amely az iparág digitális átalakítása az egyre inkább csatlakozni.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Hogyan Azure-szolgáltatások engedélyezése SaaS SnelStart
Azure használatával SnelStart ki tud szolgálni az ügyfelek és a könyvelők többet zökkenőmentesen a felhőben. Például az ügyfelek és a könyvelők információkat oszthat meg az Azure-on tárolt SnelStart tartozó ügyfél API közvetlen elérésével. Kuip állapota "újrafelhasználható szolgáltatásokról az ügyfélkapcsolati web apps, és adja meg a közös infrastruktúrát és hozzáférhet a vállalati felügyeleti az ügyfelek és a harmadik felek szoftvereivel, az ügyfelek által használt funkciók. Például a termék-konfiguráció képességek biztosítása, tűzfal-szabályok kezelése és a hosszú futású folyamatokat, például biztonsági másolatok kezelése. "

> Célunk 100 %-os automation üzleti-felügyeleti szolgáltatások adhatunk ügyfeleink számára." 
> 
> – Carlo Kuip, informatikai felelős mérnök
> 
> 

Emellett SnelStart webszolgáltatások engedélyezése az ügyfelek és a könyvelők könnyen hozzáférés az Azure SQL Database rugalmas készletek adatokhoz. A Szolgáltatottszoftver modell, adatbázis a rugalmasság és az Azure Resource Manager alapján kialakulhat SnelStart biztosít minden Azure-telepítés kiegészítő méretezhetőség szolgáltatásokkal. A megvalósítás teljesen automatizált kezelési kódtárakat C# használatával.

![SnelStart architektúra](./media/sql-database-implementation-snelstart/figure1.png)

1. ábra. 2016. június SnelStart rendelkezik, több mint 11 000 adatbázisok és több mint 50 rugalmas készletek

## <a name="simplicity-from-the-cloud"></a>Egyszerűség a felhőből
Egy Azure felhőalapú megoldás áthelyezését, SnelStart óta támogatja a gyors felhasználói növekedési kínálja a új szolgáltatások és szolgáltatások. A következők szerint van, "az Azure-ral, azt biztosíthat közelében folyamatos frissítések adhatunk ügyfeleink, a műveleti személyzet kiterjesztése nélkül. És azt minden más kiváló Azure szolgáltatás – például a méretezhetőséget és katasztrófa-helyreállítás – kötegelve. "

> "Ha a rendszer ténylegesen keresztül Redmondban található irodájába... Hívás érkezett hívnia kérek a megadott problémáról újra Hollandia, a fejlesztőtől származó. Sikerült megszerezni a Microsoft képes biztosítani a változás az éles környezetben a probléma megoldására 48 órán belül."
> 
> – Henry lett, a szoftver felelős mérnök
> 
> 

SnelStart is figyelemreméltónak tartja az erős partnerség azokat már a Microsoft Azure SQL Database-csapattal fejlesztett. Mint Kuip állapota "beszélgetéseket van a szolgáltatások, és mindkét oldalon értékeljük technológia, amely valami használata."
A közvetlen SnelStart a célja egyre több adatra meg ügyfelének alap. Mivel állapotai "Nélkül a műszaki és erőforrás-korlátozások, mint egy ISV volt korlátozva van milyen távolságban azt is növekszik."

## <a name="more-information"></a>További információ
* Az Azure rugalmas készletek kapcsolatos további információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
* Azure SQL Data Warehouse kapcsolatos további információkért lásd: [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* SnelStart kapcsolatos további információkért lásd: [SnelStart](http://www.snelstart.nl).

