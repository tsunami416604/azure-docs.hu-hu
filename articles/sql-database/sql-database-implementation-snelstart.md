---
title: Az Azure SQL Database Azure esettanulmány - Snelstart |} A Microsoft Docs
description: Ismerje meg hogyan SnelStart az SQL Database használatával gyorsan kibontva szolgáltatásait 1000 új Azure SQL-adatbázis havi díj
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c919a3cb47017d2f65b141e358ab318f4b764627
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713714"
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Az Azure-ban rendelkezik a snelstart szolgáltatásait 1000 új Azure SQL-adatbázis havi díj
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

Hollandia SnelStart teszi népszerű pénzügyi - és a business-kezelési szoftver a kis - és közepes méretű vállalkozások számára (SMB). A személyzet 110 alkalmazottai, beleértve az informatikai részleg 35 55,000 ügyfeleinek szolgált. Asztali szoftverek áthelyezését egy Azure-alapú szoftver--szolgáltatásként (SaaS-) ajánlat, amelyet SnelStart készült a legtöbb beépített szolgáltatásokat, ismerős környezetben használata a C#-ban, és a teljesítmény és méretezhetőség optimalizálása által sem over - felügyelet automatizálása vagy rugalmas készleteket használó, kiépítés alatt vállalkozások. SnelStart használja az Azure biztosít a helyszíni és a felhő között mozgó ügyfelek a rugalmasság.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Miért SnelStart kiegészítő szolgáltatások az asztalról a felhőbe
> "Az Azure-ral működő azt jelenti, hogy még gyorsabban szállíthat szoftvereket, gyorsan reagálni az ügyféligényekre, és méretezni a megoldásokat, amikor az igények növekedésével párhuzamosan."
> 
> – Henry Been, Szoftvertervező
> 
> 

SnelStart futott sikeres szoftvervállalkozások életébe éve, a hagyományos fejlesztési modell használatával: code csomag, terjesztésére, majd ismételje meg. Az idő múlásával a változtatás ütemben nőtt, gyorsabb és gyorsabb. Szabályzat gyakran módosulnak, és az ügyfelek szükség könnyebben módon dolgozza fel a pénzügyi rekordok, és együttműködni a accountants és kormányzati tartani ezeket a módosításokat.

"Az ügyfelek számára a szállítási címhez tartozó szoftver volt költséges és korlátozó," Henry Been, szoftvertervező megfelelően. "Éles, csomagolási és kézbesítési költségek korlátozva lehetett milyen gyakran kiadunk szoftverfrissítési. A csomag frissítéseinek rendszeres szállítmányok kellett, de, amely tette rögzített ügyfeleinknél változó igényeknek. A Microsoft sikerült soha nem biztos lehet abban, hogy ügyfeleink a termék a legújabb verzióra frissíteni. Ezért becsületessége támogatja több verzió, így a támogatási feladat nagyon nehéz jól."

Lett ad hozzá, "kívántuk program és -kiadási frissítések: egy gyorsított lépést, így azt sikerült gyorsabban végezhet innovációt, és hozzon létre új szolgáltatásokat ügyfeleink számára. Is kívántuk ügyfeleink üzleti-címfelügyeleti szükségletek egyszerűsítése érdekében további folyamatok automatizálásához."

A SnelStart a szolgáltatások kiterjesztésére egy felhőalapú SaaS-szolgáltató válva volt a megoldás. Az Azure SQL Database platform segítségével érheti el anélkül, hogy lenne szükség egy infrastruktúra--szolgáltatásként (IaaS) megoldás fő IT-részlegre SnelStart.

A felhőalapú modell azt is lehetővé teszi, hogy a SnelStart hibajavítás és új funkciók gyorsan, anélkül, hogy töltse le és szoftverfrissítésre ügyfeleket. A következők szerint lett, "Using Azure cloud services lehetővé teszi számunkra, hogy a gyors reagáláshoz harmadik felektől származó követelményeinek megváltoztatása. Ahelyett, hogy küldje el az új verzió a több ezer ügyfelünk, hogy tesztkörnyezetéhez harmadik felek által igényelt új formátumba az asztali alkalmazás által küldött adatokat."

## <a name="a-modern-company-with-traditional-roots"></a>A hagyományos gyökerek modern vállalati
SnelStart rajongóiból gyökerek elrendeli 1964, amikor alapítóival indítja el a vállalat egy gyártó zenei eszköz részek a modern, Agilis, csúcskategóriás műszaki cége. A SnelStart szoftvervállalkozások életébe küldetése valóban lépések során a személyi számítógép elterjedésével a 1980-as években a zúzása. A vállalat ideig tartott-e a saját kezébe kérdések jobb megoldás, mint az időben, a könyvelési szoftverek szükség. A vállalat 1982 létrehozott alapját a mi SnelStart nyilvántartási szoftver idővel válnak. A kezdetektől a szoftver esetében az egyszerű és gyors módon lett admired – ennél jóval, hogy a vállalat végül fókusz megváltozott, és reinvented magát egy szoftvercég be.

SnelStart 1995-ben, amely az első Windows-nyilvántartási alkalmazás. Az alkalmazás a Microsoft Visual Basic 1.0-s és a Microsoft Access-adatbázisok, épülő segítségével növelje az ügyfél több mint 5000 felhasználója alap.

Még ma SnelStart egy üzletági (LOB) és a business-felügyeleti alkalmazás holland SMB és az önálló szakgyártók fő szolgáltató. Carlo Kuip, IT-mérnök, feliratú, mint "az a célunk, hogy 100 %-os automation ügyfeleink üzleti-felügyeleti szolgáltatásokat biztosít."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Teljesítmény és a rugalmas készletekkel költség optimalizálása
SnelStart egy rugalmas készletek nagy méretű korai felhasználója volt. Rugalmas készletek segítségével a vállalat korlátozza a költségeket, és hatékonyabban kezelheti a teljesítményre vonatkozó követelmények. A következők szerint lett "rugalmas készletek használatával azt is teljesítmény optimalizálása túlzott kiosztása nélkül az ügyfelek igényeinek megfelelően. Ha lenne kiépítése csúcsterhelés alapján, Igen költséges lenne. Ehelyett megoszthatók az erőforrások között több, alacsony kihasználtságú adatbázisok lehetővé teszi számunkra, hogy hozzon létre egy jól végzi, és költséghatékony megoldást. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Az Azure SQL Database segítségével elkülönítési és biztonsági adatok tárolóba
Az Azure SQL Database lehetővé teszi, hogy a SnelStart egyszerűen és könnyen ügyfelei a helyszíni üzleti-felügyeleti adatok az Azure-bA áthelyezni. Az Azure SQL Database egy kényelmes tároló által biztosított elszigetelésére, a határ hitelesítési, engedélyezési és egyszerű biztonsági mentési és visszaállítási képességekre. Adatbázisok kiválóan fogalmi modell üzleti felügyeleti adja meg. Carlo Kuip, IT-mérnök, megfelelően "elemeket a tároló határán belül elengedhetetlen egy vállalat bizalmas adatokat tartalmaznak, és azok az elemek egy elkülönített adatbázis tárolási tartja őket a megfelelő védelmet biztosít. Hogy kezelheti az adatbázis szintjén engedélyezési, és még automatizálhatja a felügyeleti és az adatbázisok horizontális felskálázás anélkül, hogy az adatbázis-rendszergazdák (adatbázisok) a személyzet."

Az Azure SQL Data Warehouse emellett szerepet játszik a SnelStart biztonsági és felügyeleti történetet segít a vállalat gyűjtsön telemetriai adatokat, például a behatolás felderítése, a felhasználói tevékenység naplózásának és a kapcsolatokat.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure eltávolítja többletterhelést okoz, hogy a fejlesztők idejüket több érték továbbítása
Az Azure-platform-modell infrastruktúra terhelést eltávolítva, és engedélyezve van a C# kezelési kódtárak használatával üzembe helyezések automatizálását a SnelStart. Kuip-állapotokat, "sikerült nő a jelenlegi műveletek a nagyon kicsi személyzet közben egyszerre növeli a méretezhetőséget, a sebesség és a vész helyreállítási lehetőségek az ügyfelek számára. A SHIFT billentyűt a szolgáltatások fejlesztését, új szolgáltatásokkal és funkciókkal tartani az új szabályzat a meglévő kód vagy kódokat módosítása helyett az erőforrások felszabadulnak." Hozzáad, "a kezelés automatizálása, és az SaaS kínál, és üzleti értéket képviselnek az ügyfelek számára anélkül, hogy nagyméretű, befektetés a kezelőszemélyzet sikerült használatával." Például az Azure és a rugalmas készletek használatával SnelStart volt számos új funkciót, beleértve a bankok, ügyféladatok integráció robusztusabb adhat hozzá új számlázási szolgáltatások kisvállalati háttér-ellenőrzéseket és e-mail-szolgáltatások.

> "Csak az első néhány hónapjában 2016, hogy az Azure SQL Database telepítések körülbelül 5,500 több mint 12 000 kibontva, és jelenleg körülbelül 1000-adatbázis havi adunk hozzá."
> 
> – Henry Been, Szoftvertervező
> 
> 

Adatbázis-kezelés további automatikus, a rugalmas feladatok funkció használatával. Kuip államok, mert "nagyon Köszönjük a automatikus felderítés [kiszolgáló] példányára, az SQL DB-adatbázisok." Ez lehetővé teszi a SnelStart nélkül további erőforrásokra a dinamikusan növekvő vásárlói különböző felügyeleti műveletek végrehajtásához.

SnelStart is fejleszti egy API, amely közvetítőként vásárlói adatokat és a harmadik féltől származó szoftverek partnereink által készített alkalmazások között. Kuip államok, mint "az API lehetővé teszi más funkciók felvétele a szoftverek, például a számlák és egyéb dokumentumokat adatbevitel kiküszöbölése megoldás." Ügyfelek már nem kell kézzel írja be a számlákat azok kisvállalati nyilvántartási szoftver; a SnelStart szoftver közvetlenül cserélnek a szükséges információkat. Ez lehetővé teszi az ügyfelek számára, hogy azok üzleti-felügyeleti feladatok és az ökoszisztéma, amely a digitális átalakulást az iparágban egyre inkább csatlakozás.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Azure-szolgáltatások engedélyezése SaaS SnelStart hogyan
Az Azure használatával a SnelStart ügyfeleinek és azok a felhőben zökkenőmentesen több accountants szolgálhat. Például ügyfelek és a accountants használatával információkat oszthat meg a SnelStart ügyfél API-t, az Azure-ban üzemeltetett közvetlen elérésével. Az államok Kuip, "újrafelhasználható szolgáltatások érhetők el az ügyfelek által használt web Apps, és adja meg a közös infrastruktúrát és a functions az ügyfelek által használt külső szoftvert és az ügyfelek üzleti felügyeleti való hozzáférés engedélyezéséhez. Például a termék-konfigurációs lehetőségeket biztosít, a tűzfalszabályok kezelése és hosszú futású folyamatok, például a biztonsági másolatok kezelése."

> Célunk, hogy 100 %-os automation ügyfeleink üzleti-felügyeleti szolgáltatásokat biztosít." 
> 
> – Carlo Kuip, informatikai mérnök
> 
> 

Emellett SnelStart webes szolgáltatások lehetővé teszik, ügyfelek és a accountants segítségével könnyen hozzáférhet az adatok Azure SQL Database rugalmas készletei. Az SaaS-modell, adatbázis a rugalmasság és Azure Resource Manager szolgáltatással párosítva SnelStart fürtméretezési szolgáltatások kiegészítő minden Azure-telepítés biztosít. A megvalósítás teljesen automatizált kezelési kódtárak C# használatával.

![SnelStart architektúra](./media/sql-database-implementation-snelstart/figure1.png)

1. ábra. 2016. június SnelStart rendelkezik több mint 11 000 adatbázisok és rugalmas készletek több mint 50

## <a name="simplicity-from-the-cloud"></a>Egyszerűség a felhőből
Áthelyezése az Azure felhőalapú megoldásokhoz, SnelStart óta módosíthatják a gyors ügyfékökrének növekedése, miközben innovatív funkciókat és szolgáltatásokat kínál. A következők szerint lett, "az Azure-ban is tudunk közel folyamatos frissítések a vásárlókat, a műveleti személyzet bővítése nélkül. Az összes többi nagyszerű Azure-funkciókat kapunk, és – például a méretezhetőséget és a vészhelyreállítással – kötegelve. "

> "Ha a rendszer ténylegesen keresztül redmondban... Hívás érkezett hívnia me egy meghatározott problémára vonatkozó újra Hollandia, a fejlesztők Sikerült beolvasni a Microsoft, hogy a módosítás saját éles környezetben a problémának a megoldásához 48 órán belül."
> 
> – Henry Been, Szoftvertervező
> 
> 

SnelStart is figyelemreméltónak tartja az erős partnerség, létrehoztuk a Microsoft Azure SQL DB csapatával. Mint Kuip állapota "hozzászólások funkciókra van, és mindkét oldalon értékeljük technológiát, amit használata."
A SnelStart azonnali célja, hogy egyre több elégedett ügyfelének alap. Ahogy már állapotai "A műszaki és erőforrás-korlátozások független szoftverszolgáltatóként szolgáltatástípusban nélkül nincs korlátozva, hogy milyen növelhető."

## <a name="more-information"></a>További információ
* Az Azure rugalmas készletek kapcsolatos további információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
* Azure SQL Data Warehouse kapcsolatos további információkért lásd: [SQL Data warehouse-bA](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* A SnelStart kapcsolatos további információkért lásd: [SnelStart](http://www.snelstart.nl).

