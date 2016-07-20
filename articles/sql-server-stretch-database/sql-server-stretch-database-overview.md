<properties
    pageTitle="A Stretch Database áttekintése | Microsoft Azure"
    description="Ismerje meg, hogyan telepítheti át előzményadatait átlátható és biztonságos módon a Microsoft Azure felhőbe a Stretch Database segítségével."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# A Stretch Database áttekintése

A Stretch Database segítségével átlátható és biztonságos módon telepítheti át előzményadatait a Microsoft Azure felhőbe.

Ha szeretné azonnal megkezdeni a Stretch Database használatát, olvassa el a [Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása](sql-server-stretch-database-wizard.md) című cikket.

## Milyen előnyökkel jár a Stretch Database használata?
A Stretch Database a következő előnyöket nyújtja:

**Garantálja a ritkán használt adatok költséghatékony elérhetőségét** Az SQL Server Stretch Database segítségével dinamikusan archiválhatja a felhőbe gyakran és ritkán használt tranzakciós adatait az SQL Serverről a Microsoft Azure-ba. Az adatai mindig elérhetők és lekérdezhetők lesznek a hálózaton keresztül, a ritkán használt adatok tárolására használt legtöbb megoldástól eltérően. Ezenfelül hosszabb ideig őrizheti meg az adatokat, anélkül, hogy komoly összeget kellene fordítania a nagy méretű táblákra (például az ügyfelek rendelési előzményeit gyűjtő és hasonló táblákra). Az Azure alacsony díjakat kínál, így nem szükséges drága helyszíni tárhelyek skálázásával bajlódnia. Ön választja meg a tarifacsomagot, és Ön szabja meg az Azure portál konfigurációját is, így átfogóan szabályozhatja költségeit. Ezenfelül vertikális le- vagy felskálázást is alkalmazhat, bármikor, amikor csak szüksége van rá. További részletekért látogasson el az [SQL Server Stretch Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) lapra.

**Nem szükséges módosítania a lekérdezéseket és az alkalmazásokat** Problémák nélkül elérheti helyi vagy felhőbe archivált SQL Server-adatait.   Az SQL Server a háttérben gondoskodik az adatok áttelepítéséről – Önnek csupán az adatok tárolási helyét meghatározó szabályzatot kell létrehoznia. A tábla egésze mindig elérhető hálózaton keresztül, így bármikor lekérdezhető. A Stretch Database használatához ráadásul nem kell módosítania a meglévő lekérdezéseket vagy alkalmazásokat sem – az adatok helye tökéletesen átlátható az alkalmazás számára.

**Leegyszerűsíti az adatok helyszíni karbantartását** Adatai kevesebb helyszíni karbantartást és tárterületet igényelnek. Az adatok felhőben tárolt részéhez kapcsolódó biztonsági mentési feladatok automatikusan futnak. A helyszíni adatokra vonatkozó biztonsági mentési feladatok is felgyorsulnak, és mindig a karbantartási időszakon belül befejeződnek. Így jóval kevesebb helyszíni tárterületre van szükség. Az Azure-tárterület használata akár 80%-kal gazdaságosabb lehet, mint a helyszíni SSD bővítése.

**Tudja biztonságban adatait még az áttelepítés időtartama alatt is** Most még legfontosabb alkalmazásait is teljes nyugalommal archiválhatja a felhőbe. Az SQL Server Always Encrypted funkciója még az áttelepítés alatt álló adatok számára is képes titkosítást biztosítani. A Stretch Database ezenfelül a sorszintű biztonságot (RLS) és az SQL Server más biztonsági funkcióit is felhasználja adatai védelmére.

## Mire való a Stretch Database?
Ha engedélyezi a Stretch Database funkciót egy SQL Server-példány, egy adatbázis és legalább egy tábla esetében, az alkalmazás csöndben elkezdi áttelepíteni előzményadatait az Azure-ba.

-   Ha az előzményadatokat különálló táblában tárolja, az egész tábla áttelepíthető.

-   Ha a tábla vegyesen tartalmaz előzményadatokat és aktuális adatokat, szűrőpredikátum meghatározásával válogathatja ki az áttelepítendő sorokat.

A Stretch Database garantálja, hogy még akkor sem fordul elő adatvesztés, ha az áttelepítés során hiba történik. Az alkalmazás ezenfelül újrapróbálkozási logikát is tartalmaz az áttelepítés közben fellépő esetleges csatlakozási problémák kezelésére. Az áttelepítés állapotát dinamikus felügyeleti nézetben követheti  nyomon.

Az adatok áttelepítését akár szüneteltetheti is, így elháríthatja a helyi kiszolgálón esetleg fellépő hibákat, vagy maximalizálhatja az elérhető hálózati sávszélességet.

Nem szükséges módosítania meglévő lekérdezéseit és ügyfélalkalmazásait. Az adatok áttelepítése közben sem veszti el hozzáférését a helyi és a távoli adatokhoz. A távoli lekérdezéseknél tapasztalható némi késés, de csak akkor, ha lekérdezést hajt végre az előzményadatokon.

![A Stretch Database áttekintése][StretchOverviewImage1]

## Érdemes Önnek beszereznie a Stretch Database-t?
Ha az alábbi állítások igazak Önre, a Stretch Database segíthet megfelelni a követelményeknek, és megoldani a felmerülő problémákat.

|Ha Ön döntéshozó|Ha Ön adatbázisgazda|
|------------------------------|-------------------|
|Feladataim közé tartozik a tranzakciós adatok hosszú távú megőrzése.|Az általam használt táblák olyan nagyra nőttek, hogy már nehézséget okoz a kezelésük.|
|Előfordul, hogy lekérdezést kell végrehajtanom az előzményadatokon.|Felhasználóim igénylik az előzményadatok elérését, ugyanakkor felettébb ritkán használják őket.|
|Vannak olyan (akár régebbi) alkalmazásaim, amelyeket már nem fogok frissíteni.|Folyamatosan több és több tárterületet vásárolok.|
|Szeretnék kevesebbet költeni a tárolásra.|Az SLA keretében nincs lehetőségem ilyen nagy táblák biztonsági mentésére és helyreállítására.|

## Milyen típusú alkalmazások és táblák alkalmasak a Stretch Database segítségével történő áttelepítésre?
A Stretch Database elsődleges célja a nagy mennyiségű előzményadatot jellemzően kevés táblában tartalmazó tranzakciós adatbázisok áttelepítése. Ezek a táblák akár több mint egymilliárd sort is tartalmazhatnak.

Ha használja az SQL Server 2016 historikus tábla funkcióját, a Stretch Database segítségével a kapcsolódó előzménytábla egésze (vagy egy része) áttelepíthető a költséghatékony Azure-tárhelyre. További információk: [Manage Retention of Historical Data in System-Versioned Temporal Tables](https://msdn.microsoft.com/library/mt637341.aspx) (Előzményadatok megőrzésének kezelése rendszerverziókkal ellátott historikus táblákban).

Használja az SQL Server 2016 Upgrade Advisor eszközének Stretch Database Advisor funkcióját a Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítására. További információk: [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (A Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítása). A használatot esetleg megakadályozó problémákkal kapcsolatos további információkért lásd: [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) (A Stretch Database felületi korlátozásai, illetve a használatát akadályozó problémák).

## <a name="FAQ"></a>A Stretch Database-zel kapcsolatos gyakori kérdések
**Működik a Stretch Database az &lt;SQL Server különböző funkcióival&gt;?**
-   Az SQL Server bizonyos funkcióinak használata esetében a tábla nem alkalmas a Stretch Database-zel való kezelésre, ezekről a [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) (A Stretch Database felületi korlátozásai, illetve a használatát akadályozó problémák) című cikkbből tudhat meg többet.

-   Azt is megteheti, hogy letölti az SQL Server 2016 Upgrade Advisor eszközt, és futtassa a Stretch Database Advisor segédprogramot a Stretch Database-zel használható adatbázisok és táblák azonosítására. További információk: [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (A Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítása).

**Célozhatok egy másik helyi SQL Server-példányt a Stretch Database-ben?**
Nem. A Stretch Database nem támogatja a helyi SQL Server-példányok távoli végpontként való használatát.

**Letilthatom a Stretch Database-t, és visszahelyezhetem az áttelepített adatokat a helyi táblába?**
Igen. További információk: [Disable Stretch Database and bring back remote data](sql-server-stretch-database-disable.md) (A Stretch Database letiltása, és a távoli adatok visszahelyezése).

## Fogalmak
**Helyi adatbázis**. A helyszíni SQL Server-adatbázis.

**Távoli végpont**. Az adatbázis távoli adatait tartalmazó hely a Microsoft Azure-ban.

**Helyi adatok**. A Stretch Database funkcióval archivált adatbázisokban található olyan adatok, amelyeket a Stretch Database-ben meghatározott táblakonfiguráció alapján a rendszer nem telepít át az Azure-ba.

**Alkalmas adatok**. A Stretch Database funkcióval archivált adatbázisokban található olyan adatok, amelyeket még nem helyezett át a rendszer, de a Stretch Database-ben meghatározott táblakonfiguráció alapján később át fog telepíteni az Azure-ba.

**Távoli adatok**. A Stretch Database funkcióval archivált adatbázisokban található olyan adatok, amelyeket a rendszer már áttelepített az Azure-ba.

## Architektúra
A Stretch Database szerepe, hogy a Microsoft Azure erőforrásait felhasználva átvállalja az archivált adatok tárolásának, illetve a lekérdezések feldolgozásának terheit.

Ha engedélyezi a Stretch Database használatát egy adatbázison, az alkalmazás létrehoz egy biztonságos, hivatkozott kiszolgálódefiníciót a helyszíni SQL Serveren. A hivatkozott kiszolgálódefiníció célja a távoli végpont. Ha a Stretch Database használatát egy adatbázis tábláján engedélyezi, az alkalmazás kiépíti a távoli erőforrásokat, és ha engedélyezett az áttelepítés, megkezdi az alkalmas adatok áttelepítését.

A Stretch Database-engedélyezett táblákra mutató lekérdezések a helyi és a távoli végponton egyaránt automatikusan lefutnak. A Stretch Database az Azure feldolgozási kapacitása révén a lekérdezés újraírásával futtatja le a lekérdezéseket a távoli adatokon. Az újraírás távoli lekérdezés típusú operátorként szerepel az új lekérdezéstervben.

![A Stretch Database architektúrája][StretchOverviewImage2]

## Biztonság és engedélyek

### A Stretch Database engedélyezése és letiltása SQL Server-példányokon
Az adatbázisnak a Stretch Database-zel való használatának megkezdéséhez először az sp\_configure használatával módosítsa a „távoli adatarchívum” példányszintű konfigurációs lehetőséget. Ehhez a művelethez rendszergazdai vagy kiszolgáló-rendszergazdai jogosultságok szükségesek. A funkció engedélyezését követően elvégezheti az adatbázisok konfigurációját a Stretch Database-zel való használatra, áttelepítheti az adatokat, valamint lekérdezheti a távoli végponton tárolt adatokat.

### A Stretch Database engedélyezése és letiltása adatbázisokon vagy táblákon
Az adatbázisoknak a Stretch Database-zel való használatának konfigurálása kizárólag a CONTROL DATABASE (Adatbázis-vezérlés) engedély birtokában lehetséges. Ezenfelül meg kell adnia a távoli végpontra vonatkozó rendszergazdai hitelesítő adatokat is.

Tábláknak a Stretch Database-zel való használatának konfigurálása kizárólag a táblára vonatkozó ALTER (Módosítás) engedély birtokában lehetséges. Ez a művelet csak akkor hajtható végre, ha az adatbázist már beállították a Stretch Database-zel való használatra.

### Az adatok elérése
A Stretch Database mögötti hivatkozott kiszolgálódefiníciót kizárólag rendszerfolyamatok érik el. A felhasználói bejelentkezések nem küldhetnek lekérdezéseket a hivatkozott kiszolgálódefiníción keresztül a távoli végpontra.

A Stretch Database nem módosítja a meglévő adatbázisok engedélymodelljét. A felhasználó bejelentkezések a helyi adatbázison keresztül lekérdezhetik a Stretch Database-engedélyezett táblákban tárolt adatokat. A helyi adatbázis a felhasználó által kezdeményezett műveletek esetében ugyanúgy elvégzi az engedélyek ellenőrzését, mint bármely más objektumnál. Ha a felhasználó jogosult annak a táblának az elérésére, amelyen engedélyezve van a Stretch Database, akkor az adatok fizikai helyétől függetlenül hozzá fog férni minden adathoz, amelynek elérésére engedélyt kapott.

![A Stretch Database adatelérési modellje][StretchOverviewImage3]

## A Stretch Database kipróbálása
**Próbálja ki a Stretch Database-t az AdventureWorks mintaadatbázissal.** Az AdventureWorks mintaadatbázishoz töltse le [innen](https://www.microsoft.com/download/details.aspx?id=49502) legalább az adatbázisfájlt, a mintákat, valamint a parancsfájlokat. Állítsa vissza a mintaadatbázist az SQL Server 2016 tetszőleges példányába, bontsa ki a mintafájlt, majd nyissa meg a Stretch DB mappában található Stretch DB Samples fájlt. A Stretch Database engedélyezése előtt és után futtassa le a fájlban található parancsprogramokat az adatok által elfoglalt hely ellenőrzéséhez, az adatok áttelepítési folyamatának nyomon követéséhez. Ezenkívül a parancsprogramokkal megbizonyosodhat róla, hogy folytathatja-e a meglévő adatok lekérdezését, és felvehet-e új adatokat az áttelepítés előtt és után egyaránt-

## Következő lépés
**Azonosítsa a Stretch Database-kompatibilis adatbázisokat és táblákat.** Töltse le az SQL Server 2016 Upgrade Advisor eszközét, és futtassa a Stretch Database Advisor segédprogramot a Stretch Database-zel használható adatbázisok és táblák azonosítására. A Stretch Database Advisor a használatot akadályozó körülményeket is képes felfedni. További információk: [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (A Stretch Database segítségével jól kezelhető adatbázisok és táblák azonosítása).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png



<!--HONumber=Jun16_HO2-->


