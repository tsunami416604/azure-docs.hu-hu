---
title: Az Azure SQL Database használatával magas rendelkezésre állású szolgáltatás tervezése |} A Microsoft Docs
description: További információ az alkalmazás-tervezés az Azure SQL Database használatával magas rendelkezésre állású szolgáltatások.
keywords: vész-helyreállítási, a vész-helyreállítási megoldások, a alkalmazás az adatok biztonsági mentése, a georeplikáció, a felhő üzleti folytonossági tervezése
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f1c228802bd0a2e65321a3abe47b87845f5f86a0
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092613"
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Az Azure SQL Database használatával magas rendelkezésre állású szolgáltatások tervezése

Összeállításakor, és az Azure SQL Database magas rendelkezésre állású szolgáltatások telepítése, használata [feladatátvételi csoportok és az aktív georeplikáció](sql-database-geo-replication-overview.md) katasztrofális hibák és a regionális üzemkimaradások utáni helyreállításon hibatűrést biztosít. Emellett lehetővé teszi a gyors helyreállítást a másodlagos adatbázisok. Ez a cikk gyakori alkalmazásminták koncentrál, és ismerteti ezek előnyeit és hátrányait skálán. Aktív georeplikáció rugalmas készletekkel kapcsolatos információkért lásd: [rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Ha prémium szintű és az üzletileg kritikus adatbázisok és rugalmas készletek használ, akkor is használhatja őket rugalmas a regionális üzemkimaradások utáni helyreállításon zóna redundáns üzembe helyezési konfiguráció átalakításával. Lásd: [zónaredundáns adatbázisok](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>1. példa: Két Azure-régió használata az üzletmenet-folytonossági minimális állásidővel
Ebben a forgatókönyvben az alkalmazások a következő jellemzőkkel rendelkeznek: 
*   Alkalmazás aktív egy Azure-régióban
*   Minden adatbázis-munkamenetek szükséges olvasási és írási hozzáférés (RW) adatokhoz
*   Webes rétegbeli és adatrétegbeli a kell a késés és a forgalom költségek csökkentése érdekében közös elhelyezésű 
*   Alapvetően állásidőre-e egy magasabb szintű üzleti kockázat ezekhez az alkalmazásokhoz, mint az adatvesztés

Ebben az esetben az alkalmazás üzembe helyezési topológiát optimalizált regionális katasztrófák kezelése, ha az összes alkalmazás-összetevők együttesen kell a feladatátvételi. Az alábbi ábrán ez a topológia. A földrajzi redundancia céljából az alkalmazás erőforrásai települnek a és b régió B régióban lévő erőforrásokat azonban nem használhatók mindaddig, amíg A régió nem sikerül. Egy feladatátvételi csoportot kezelheti az adatbázis-kapcsolat, a replikáció és a feladatátvétel két régió között van konfigurálva. A web service mindkét régióban van konfigurálva, az olvasási és írási figyelő keresztül az adatbázis eléréséhez  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** (1). A TRAFFIC manager használatára van beállítva [prioritásos útválasztási mód](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Az Azure traffic manager](../traffic-manager/traffic-manager-overview.md) során ez a cikk csak illusztrációs célokat szolgál. Minden terheléselosztási megoldás, amely támogatja a prioritásos útválasztási mód is használhatja.    
>

Az alábbi ábrán ez a konfiguráció a leállás előtt:

![1. forgatókönyv. A konfiguráció a leállás előtt.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Az elsődleges régióban kimaradás után a az SQL Database szolgáltatás észleli, hogy az elsődleges adatbázis nem érhető el, és elindítja a feladatátvételt a másodlagos régióba, az Automatikus feladatátvétel házirend (1) a paraméterei alapján. Az alkalmazás SLA függően konfigurálhat egy türelmi időszak, amely a szolgáltatáskiesés megszüntetése után észlelését, és magát a feladatátvétel között eltelt idő vezérli. Akkor lehet, hogy a traffic manager a végpont feladatátvételt kezdeményez, a feladatátvételi csoport az adatbázis a feladatátvétel aktiválása előtt. Ebben az esetben a webalkalmazás nem azonnal újra az adatbázishoz. De az ezt a lehetőséget, amint az adatbázis-feladatátvétel befejezése automatikusan fog sikerülni. Ha a sikertelen régió visszaállítása, és ismét online, a régi elsődleges automatikusan újracsatlakozik, egy új másodlagos. Az alábbi ábra a konfigurációt a feladatátvételt követően.
 
> [!NOTE]
> A feladatátvétel után végrehajtott összes tranzakciók során az újracsatlakozás elvesznek. A feladatátvétel befejezése után a B régióban az alkalmazás is képes csatlakozzon újra, és indítsa újra a felhasználói kérelmek feldolgozásához. A webes alkalmazás, mind az elsődleges adatbázis most B régióban és ugyanott maradnak. n>

![1. forgatókönyv. Feladatátvétel utáni konfigurációja](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Ha kimaradás B régióban történik, az elsődleges és a másodlagos adatbázis között a replikációs folyamatot felfüggesztett lekérdezi, de a kettő közötti kapcsolat továbbra is változatlan marad (1). Kezelt forgalom észleli, hogy a régió b kapcsolat megszakad, és jelöli meg a végpont webalkalmazás 2 (2) csökkentett teljesítményű. Ebben az esetben nem működik az alkalmazás teljesítményét, de az adatbázis elérhető lesz, és ezért magasabb fennáll a kockázata, adatvesztés esetben A régió nem sikerül egymás után.

> [!NOTE]
> Vész-helyreállítási javasoljuk, hogy az alkalmazás központi telepítésének korlátozott konfigurációt két régióban is. Ennek az oka az Azure-régiócsoportok többsége csak két régióban van. Ez a konfiguráció nem nyújt védelmet az alkalmazás mindkét régióban egyidejű katasztrofális hibája esetén. Egy ilyen hibát nem túl valószínű esetben, akkor helyreállíthatja az adatbázisokat a harmadik régiót használatával [geo-visszaállítás művelet](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Amint a szolgáltatáskimaradás megszűnése van, a másodlagos adatbázis automatikusan újraszinkronizálja az elsődleges. A szinkronizálás során az elsődleges teljesítményét befolyásolja. Adatmennyiség óta a feladatátvételt az új elsődleges függ az adott hatását. A következő ábra szemlélteti a másodlagos régióban leállás:

![1. forgatókönyv. A másodlagos régió kimaradás után konfiguráció.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A kulcs **előnyeit** , ebben a kialakítási mintában a:

* Az azonos webalkalmazás mindkét régiók, régióspecifikus konfigurálása nélkül telepíti, és nem igényel további logikára, amely kezelnie a feladatátvételt. 
* Alkalmazások teljesítményének nem befolyásolják a webes alkalmazás feladatátvétel és az adatbázis mindig közös elhelyezésű.

A fő **kompromisszum** , hogy az alkalmazás-erőforrások, a B régió az esetek többségében kihasználva.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>2. forgatókönyv: Azure-régióban a legnagyobb adatok megőrzése az üzletmenet-folytonossági
Ez a beállítás akkor egygépes alkalmazásokhoz, a következő jellemzőkkel:

* Az adatvesztés a nagy üzleti kockázat. Az adatbázis feladatátvételi csak akkor használható végső megoldásként, ha a szolgáltatáskimaradás elhárítása után egy Katasztrofális hiba okozta.
* Az alkalmazás műveletek csak olvasható és írható-olvasható módot támogat, és a egy ideig működhet "csak olvasható módban".

Ebben a mintában az alkalmazás csak olvasható módra vált az olvasási és írási kapcsolatok elindulását időtúllépési hibát jelez. A webalkalmazás mindkét régióban üzemel, és az írási-olvasási figyelői végpont kapcsolatot és a csak olvasási figyelői végpont (1) másik kapcsolatot. A Traffic manager-profilt kell használnia [prioritású útválasztás](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Figyelési végpontját](../traffic-manager/traffic-manager-monitoring.md) engedélyezni kell számára az alkalmazás végpontjának minden régióban (2).

A következő ábra szemlélteti ezt a konfigurációt a leállás előtt:

![2. forgatókönyv. A konfiguráció a leállás előtt.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Amikor a traffic manager régióba A kapcsolódási hibát észlel, automatikusan átvált a felhasználói adatforgalmat az alkalmazáspéldány régióban B. Ezzel a mintával fontos, hogy beállította a türelmi időszak adatvesztéssel elég nagy értéket, például 24 óra. Ez biztosítja, hogy a rendszer letiltja az adatvesztés a Ha idő alatt teljesítményköltségeket csökkenti a szolgáltatáskimaradás elhárítása után. A webalkalmazás régióban B aktiválásakor az olvasási és írási műveletek kezdenek. Ezen a ponton, át kell váltania a csak olvasható módra (1). Ebben a módban a kérések útválasztása automatikusan a másodlagos adatbázis. Ha a szolgáltatáskimaradás elhárítása után egy Katasztrofális hiba okozta, nagy valószínűséggel, már nem mérsékelhetők a türelmi időszak. Amikor lejár a feladatátvételi csoport eseményindítók a feladatátvételt. Miután az olvasási és írási figyelő elérhetővé válik, és a kapcsolataikat leállítása sikertelen volt (2). A következő ábra szemlélteti a helyreállítási folyamat két szakaszban.

> [!NOTE]
> A szolgáltatáskimaradás, az elsődleges régióban teljesítményköltségeket csökkenti a türelmi időszak, ha a traffic manager észleli a hálózati kapcsolat a visszaállítást az elsődleges régióban, és vált vissza a felhasználói adatforgalmat az alkalmazáspéldány régióban rögzíti. Adott alkalmazáspéldány folytatja, és az elsődleges adatbázis használatával régióban leírtak szerint az előző ábrán egy írható-olvasható módban működik.
>

![2. forgatókönyv. Vész-helyreállítási szakaszokat.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Kimaradás B régióban történik, ha a traffic manager észleli azt, a web-app-2 végpontot, a B régió és jelek csökken, mert (1). Addig is a feladatátvételi csoport átvált a csak olvasható figyelőt, hogy A régió (2). A szolgáltatáskimaradás nem befolyásolja a végfelhasználói élményt, de az elsődleges adatbázishoz ki van téve a szolgáltatáskimaradás közben. A következő ábra szemlélteti a másodlagos régióban hiba:

![2. forgatókönyv. A másodlagos régió szolgáltatáskimaradás.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Miután teljesítményköltségeket csökkenti a szolgáltatáskimaradás, a másodlagos adatbázis a rendszer azonnal szinkronizálja az elsődleges és a csak olvasási figyelői visszakapcsol a másodlagos régió b-adatbázis A szinkronizálás során az elsődleges teljesítményét némileg érinthetik szinkronizálni kell adatok mennyiségétől függően.

Ebben a kialakításban rendelkezik több **előnyeit**:

* Az átmeneti kimaradásainak során nem szükséges az adatvesztés.
* Állásidő csak mennyi idő alatt a traffic manager észleli a csatlakozási hiba, amely konfigurálható függ.

A **kompromisszum** , hogy az alkalmazás tudja, csak olvasható módban kell lennie.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>3. forgatókönyv: Alkalmazás áthelyezheti egy másik földrajzi adatvesztés nélkül, és közel állásidő nélkül 
Ebben a forgatókönyvben az alkalmazás a következő jellemzőkkel rendelkezik: 
* A végfelhasználók elérhetik az alkalmazást a különböző földrajzi
* Az alkalmazás tartalmaz, amelyek nem függnek a teljes szinkronizálás a legújabb frissítéseit csak olvasható számítási feladatokhoz
* Írási hozzáférés az adatokhoz való támogatnia kell a felhasználók többsége számára azonos földrajzi helyen található 
* Olvasási késései kritikus következményekkel járnak a végfelhasználói élmény 


Annak érdekében, hogy biztosítania kell, hogy, hogy a felhasználó-eszköz kórháza **mindig** csatlakozik a csak olvasható műveletekhez, például böngészési adatok esetén az üzembe helyezett alkalmazás analytics stb. Mivel az OLTP-műveletek feldolgozása azonos földrajzi helyen **legtöbb esetben**. Például a nap időszakban OLTP műveletek feldolgozása azonos földrajzi helyen, de az kikapcsolt órában, sikerült feldolgozni a különböző földrajzi. A felhasználói tevékenység többnyire akkor fordul elő, a munkaidő alatt, ha garantálhatja az optimális teljesítmény érdekében a felhasználók többsége a legtöbb esetben. Az alábbi ábrán ez a topológia látható. 
 
Minden egyes földrajzi, amelyekben jelentős használati értéket igény szerint az alkalmazás-erőforrásokat kell telepíteni. Például ha az alkalmazás aktívan használja az Egyesült Államokban, Európai Unió és Délkelet-Ázsia az alkalmazást kell telepíteni az összes alábbi földrajzi területek számára. Az elsődleges adatbázis érdemes lehet dinamikusan átváltani egy földrajzi a következő munkaidőt végén. Ezt a módszert nevezik "hajtsa végre a sun". Az OLTP-munkaterhelés mindig csatlakozik az adatbázishoz az olvasási és írási figyelő keresztül  **&lt;feladatátvételi csoportnév&gt;. database.windows.net** (1). A csak olvasható munkaterhelés csatlakozik a helyi adatbázis az adatbázis-kiszolgálói végpont közvetlenül használatával  **&lt;kiszolgálónév&gt;. database.windows.net** (2). A TRAFFIC manager van beállítva a [Teljesítménycentrikus útválasztási mód](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Ez biztosítja, hogy a felhasználó eszköze csatlakozik-e a webszolgáltatás a legközelebb eső régióban. A TRAFFIC manager végpont figyelési minden webes szolgáltatás végpontját (3) engedélyezve kell beállítani.

> [!NOTE]
> A feladatátvételi csoport konfigurációja határozza meg, hogy melyik régióban szolgál a feladatátvételhez. Mivel az új elsődleges egy másik földrajzi OLTP, mind a csak olvasható számítási feladatok hosszabb késéssel feladatátvételt eredményez addig, amíg az érintett régió újra online állapotba kerül.
>

![3. forgatókönyv. Az USA keleti régiójában elsődleges konfigurációt.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

(Például a helyi idő 23 óra) a nap végén az aktív adatbázisok a legközelebbi régió (Észak-Európa) kell állítani. Ez a feladat automatizálható a [Azure szolgáltatásunk](../scheduler/scheduler-intro.md).  A feladat az alábbi lépésekből áll:
* Váltson a feladatátvételi csoport elsődleges kiszolgáló (1) rövid feladatátvételi Észak-Európa
* Távolítsa el a feladatátvételi csoport közötti, USA keleti Régiójában és Észak-Európa
* Hozzon létre egy új feladatátvételi csoportot, ugyanazzal a névvel, de Észak-Európa és Kelet-Ázsia (2) között. 
* Adja hozzá az elsődleges Észak-Európában és Kelet-Ázsiában lévő másodlagos, a feladatátvételi csoport (3).


A következő ábra szemlélteti a tervezett feladatátvétel után az új konfigurációt:

![3. forgatókönyv. Váltás az elsődleges, Észak-Európa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Egy kimaradás például Észak-Európában történik, ha az adatbázis automatikus feladatátvételi a feladatátvételi csoporthoz, ami hatékonyan áthelyezése a következő régiónak, ütemezett (1) az alkalmazás által kezdeményezett.  Ebben az esetben az USA keleti régiója nem az egyetlen fennmaradó másodlagos régióban, amíg az Észak-Európa újra online állapotba kerül. A fennmaradó két régió szolgál az ügyfelek összes három régióban szerepkörök között. Az Azure scheduler válaszokban rendelkezik. A fennmaradó régiók további felhasználói forgalom kérhet Európában, mert az alkalmazás teljesítményét kihatással van, nem csak további késés, hanem egy nő a felhasználói kapcsolatok száma szerint. Amint a szolgáltatáskimaradás elhárítása után teljesítményköltségeket csökkenti az Észak-Európa, a másodlagos adatbázis azonnal szinkronizálva van az aktuális elsődleges. A következő ábra szemlélteti egy kimaradás az Észak-Európa:

![3. forgatókönyv. Észak-Európában szolgáltatáskimaradás.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Ha a végfelhasználói élmény Európai csökken, mert a hosszú várakozási idő csökkentéséhez. Amihez meg kell proaktív módon üzembe helyezni egy alkalmazás másolása és létrehozni a másodlagos adatbázis (oka) egy másik helyi régióban (Nyugat-Európa) Észak-Európában offline alkalmazáspéldány helyett. Az utóbbi újra online állapotba kerül eldöntheti e Nyugat-Európa használatának folytatásához, vagy távolítsa el az alkalmazást a másolatát, és váltson vissza az Észak-Európa, használatával
>

A kulcs **előnyöket** , ez a kialakítás a:
* A csak olvasható alkalmazás számítási feladatait a WC-k régióban mindig fér hozzá. 
* Az olvasási és írási alkalmazás számítási feladatait a legközelebb eső régióban az időszakban a legmagasabb tevékenység minden egyes földrajzi fér hozzá.
* Az alkalmazás több régióban üzemel, mert azt egy jelentős állásidő nélkül régiót adatvesztést hibatűrését. 

De van néhány **kompromisszumot kínál a**:
* Regionális kimaradás hosszabb késés hatással lehet a földrajzi eredményez. Az alkalmazás különböző földrajzi által kiszolgált írási-olvasási és az írásvédett számítási feladatokhoz. 
* A csak olvasható számítási feladatokat egy másik végpont az egyes régiókban kell kapcsolódnia. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Üzleti folytonosság tervezési: Válasszon egy alkalmazás tervezése felhőalapú vészhelyreállítással
Az egyes adott felhőalapú vész-helyreállítási stratégiát egyesítése, vagy bővítheti az alkalmazás igényeinek leginkább megfelelő ezek a tervezési minták is.  Ahogy korábban említettük, a választott stratégia az SLA-t szeretne nyújtani az ügyfelek és az alkalmazás telepítési topológia alapján történik. Segít a döntést, a következő táblázat összehasonlítja a választási lehetőségek helyreállításipont-célkitűzés (RPO) és a becsült helyreállítási idejének (ERT) alapján.

| Mintázat | HELYREÁLLÍTÁSI IDŐKORLÁT | ERT |
|:--- |:--- |:--- |
| Aktív-passzív telepítési vész-helyreállítási közös elhelyezésű adatbázis-hozzáférést |Olvasási és írási hozzáférése < 5 mp |Hiba észlelés ideje + a DNS-Élettartamot |
| Aktív-aktív központi telepítés alkalmazás terheléselosztásra |Olvasási és írási hozzáférése < 5 mp |Hiba észlelés ideje + a DNS-Élettartamot |
| Aktív-passzív üzembe helyezés az adatok megőrzése |Csak olvasási hozzáféréssel < 5 mp | Csak olvasási hozzáféréssel = 0 |
||Olvasási és írási hozzáférése = nulla | Olvasási és írási hozzáférése = hiba észlelés ideje + adatvesztéssel türelmi időszak |
|||

## <a name="next-steps"></a>További lépések
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md)
* Georeplikációs és feladatátvételi csoportok kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md)  
* Aktív georeplikáció rugalmas készletekkel kapcsolatos információkért lásd: [rugalmas készletek vészhelyreállítási stratégiái](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
