---
title: "A Stretch Database áttekintése | Microsoft Docs"
description: "Ismerje meg, hogy a Stretch Database hogyan telepíti át a ritkán használt adatokat átlátható módon és biztonságosan a Microsoft Azure felhőbe."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: c360dc10-a02b-446f-91a0-278358f7a297
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f63900b0a4f32e1bc5a88c068091916bd96aafb8


---
# <a name="stretch-database-overview"></a>A Stretch Database áttekintése
A Stretch Database a ritkán használt adatokat átlátható módon és biztonságosan telepíti át a Microsoft Azure felhőbe.

Ha szeretné azonnal megkezdeni a Stretch Database használatát, olvassa el a [Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása](sql-server-stretch-database-wizard.md) című cikket.

## <a name="what-are-the-benefits-of-stretch-database"></a>Milyen előnyökkel jár a Stretch Database használata?
A Stretch Database a következő előnyöket nyújtja:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Költséghatékony módját \-biztosítja a ritkán használt adatok rendelkezésre állásának
Az SQL Server Stretch Database segítségével dinamikusan kiterjesztheti az Azure-ra a SQL Server ritkán és kevésbé gyakran használt tranzakciós adatait. Az adatai mindig elérhetők és lekérdezhetők lesznek a hálózaton keresztül, a ritkán használt adatok tárolására használt legtöbb megoldástól eltérően. Ezenfelül hosszabb ideig őrizheti meg az adatokat, anélkül, hogy komoly összeget kellene fordítania a nagy méretű táblákra (például az ügyfelek rendelési előzményeit gyűjtő és hasonló táblákra). Az Azure alacsony díjakat kínál, így nem \-szükséges drága helyszíni tárhelyek skálázásával bajlódnia. Ön választja meg a tarifacsomagot, és Ön szabja meg az Azure Portal konfigurációját is, így átfogóan szabályozhatja költségeit. Ezenfelül vertikális le- vagy felskálázást is alkalmazhat, bármikor, amikor csak szüksége van rá. A részletekért látogasson el az [SQL Server Stretch Database díjszabása](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) oldalra.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Nem szükséges hozzá a lekérdezések vagy az alkalmazások módosítása
Zökkenőmentesen elérheti az SQL Server-adatokat függetlenül attól, \-hogy azok a helyszínen vannak vagy ki vannak terjesztve a felhőbe.  Az SQL Server a háttérben gondoskodik az adatok áttelepítéséről – Önnek csupán az adatok tárolási helyét meghatározó szabályzatot kell létrehoznia. A tábla egésze mindig elérhető hálózaton keresztül, így bármikor lekérdezhető. A Stretch Database használatához ráadásul nem kell módosítania a meglévő lekérdezéseket vagy alkalmazásokat sem – az adatok helye tökéletesen átlátható az alkalmazás számára.

### <a name="streamlines-on-premises-data-maintenance"></a>Hatékonnyá teszi a \-helyszíni adatok karbantartását
Csökkentheti az \-adatok helyszíni karbantartási és tárolási igényeit. A helyszíni \-adatokra vonatkozó biztonsági mentési feladatok is felgyorsulnak, és mindig a karbantartási időszakon belül befejeződnek. Az adatok felhőben tárolt részéhez kapcsolódó biztonsági mentési feladatok automatikusan futnak. Így jóval \-kevesebb helyszíni tárterületre van szükség. Az Azure-tárterület használata akár 80%-kal gazdaságosabb lehet, mint a helyszíni \-SSD bővítése.

### <a name="keeps-your-data-secure-even-during-migration"></a>Az áttelepítés során is biztonságban tartja az adatokat
Nyugodt maradhat afelől, hogy a legfontosabb alkalmazásai biztonságosan kerülnek át a felhőbe. Az SQL Server Always Encrypted funkciója még az áttelepítés alatt álló adatok számára is képes titkosítást biztosítani. A Stretch Database ezenfelül a sorszintű biztonságot (RLS) és az SQL Server más biztonsági funkcióit is felhasználja adatai védelmére.

## <a name="what-does-stretch-database-do"></a>Mire való a Stretch Database?
Miután engedélyezi a Stretch Database-t egy SQL Server-példány, egy adatbázis és legalább egy tábla számára, a Stretch Database csendesen megkezdi a ritkán használt adatok áttelepítését az Azure-ba.

* Ha a ritkán használt adatokat egy különálló táblában tárolja, a teljes táblát áttelepítheti.
* Ha a tábla gyakran és ritkán használt adatokat is tartalmaz, meghatározhat egy szűrőfüggvényt az áttelepíteni kívánt sorok kiválasztásához.

**Nem szükséges módosítania meglévő lekérdezéseit és ügyfélalkalmazásait.** Az adatok áttelepítése közben sem veszti el hozzáférését a helyi és a távoli adatokhoz. A távoli lekérdezéseknek van egy kis késése, de ez a késés csak akkor jelentkezik, ha ritkán használt adatokat kérdez le.

**A Stretch Database garantálja, hogy még akkor sem fordul elő adatvesztés**, ha az áttelepítés során hiba történik. Az alkalmazás ezenfelül újrapróbálkozási logikát is tartalmaz az áttelepítés közben fellépő esetleges csatlakozási problémák kezelésére. Az áttelepítés állapotát dinamikus felügyeleti nézetben követheti  nyomon.

Az **adatok áttelepítését akár szüneteltetheti is**, így elháríthatja a helyi kiszolgálón esetleg fellépő hibákat, vagy maximalizálhatja az elérhető hálózati sávszélességet.

![A Stretch Database áttekintése][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>Érdemes Önnek beszereznie a Stretch Database-t?
Ha az alábbi állítások igazak Önre, a Stretch Database segíthet megfelelni a követelményeknek, és megoldani a felmerülő problémákat.

| Ha Ön döntéshozó | Ha Ön adatbázisgazda |
| --- | --- |
| Feladataim közé tartozik a tranzakciós adatok hosszú távú megőrzése. |Az általam használt táblák olyan nagyra nőttek, hogy már nehézséget okoz a kezelésük. |
| Időnként lekérdezést kell végrehajtanom a ritkán használt adatokban. |A felhasználóim azt mondják, hogy hozzáférést szeretnének a ritkán használt adatokhoz, de azokat csak ritkán használják. |
| Vannak olyan (akár régebbi) alkalmazásaim, amelyeket már nem fogok frissíteni. |Folyamatosan több és több tárterületet vásárolok. |
| Szeretnék kevesebbet költeni a tárolásra. |Az SLA keretében nincs lehetőségem ilyen nagy táblák biztonsági mentésére és helyreállítására. |

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Milyen típusú alkalmazások és táblák alkalmasak a Stretch Database segítségével történő áttelepítésre?
A Stretch Database olyan tranzakciós adatbázisokhoz készült, amelyek nagy mennyiségű, ritkán használt adatot tartalmaznak általában kis számú táblában tárolva. Ezek a táblák akár több mint egymilliárd sort is tartalmazhatnak.

Ha használja az SQL Server 2016 historikus tábla funkcióját, a Stretch Database segítségével a kapcsolódó előzménytábla egésze (vagy egy része) áttelepíthető a \-költséghatékony Azure-tárhelyre. További információk: [Manage Retention of Historical Data in System-Versioned Temporal Tables](https://msdn.microsoft.com/library/mt637341.aspx) (Előzményadatok megőrzésének kezelése rendszerverziókkal ellátott historikus táblákban).

Használja az SQL Server 2016 Upgrade Advisor eszközének Stretch Database Advisor funkcióját a Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítására. További információk: [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (A Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítása). A lehetséges hátráltató tényezőkkel kapcsolatos további információk: [Limitations for Stretch Database](sql-server-stretch-database-limitations.md) (A Stretch Database korlátozásai).

## <a name="test-drive-stretch-database"></a>A Stretch Database kipróbálása
**Próbálja ki a Stretch Database-t az AdventureWorks mintaadatbázissal.** Az AdventureWorks mintaadatbázishoz töltse le [innen](https://www.microsoft.com/download/details.aspx?id=49502) legalább az adatbázisfájlt, a mintákat, valamint a parancsfájlokat. Állítsa vissza a mintaadatbázist az SQL Server 2016 tetszőleges példányába, bontsa ki a mintafájlt, majd nyissa meg a Stretch DB mappában található Stretch DB Samples fájlt. A Stretch Database engedélyezése előtt és után futtassa le a fájlban található parancsprogramokat az adatok által elfoglalt hely ellenőrzéséhez, az adatok áttelepítési folyamatának nyomon követéséhez. Ezenkívül a parancsprogramokkal megbizonyosodhat róla, hogy folytathatja-e a meglévő adatok lekérdezését, és felvehet-e új adatokat az áttelepítés előtt és után egyaránt-

## <a name="next-step"></a>Következő lépés
**Azonosítsa a Stretch Database-kompatibilis adatbázisokat és táblákat.** Töltse le az SQL Server 2016 Upgrade Advisor eszközét, és futtassa a Stretch Database Advisor segédprogramot a Stretch Database-zel használható adatbázisok és táblák azonosítására. A Stretch Database Advisor a használatot akadályozó körülményeket is képes felfedni. További információk: [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (A Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítása).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Dec16_HO1-->


