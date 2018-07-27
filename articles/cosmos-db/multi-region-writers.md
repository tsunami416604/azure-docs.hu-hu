---
title: Az Azure Cosmos DB globális méretekben több főkiszolgálós |} A Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 4911a302bf9055948827a72f2e631663b8be741e
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265658"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális méretekben több főkiszolgálós 
 
Fejlesztés globálisan elosztott kihívást probléma az adatok egységes nézetek világszerte fenntartása közben helyi késése reagáló alkalmazásokat. Ügyfelek globálisan elosztott adatbázisokat, használható, mert a szükséges adatok elérési késést biztosít javítása, magas rendelkezésre állás, garantált vészhelyreállítás biztosítása és az üzleti szükségletek kielégítése céljából. Az Azure Cosmos DB több főkiszolgálós biztosít magas szintű rendelkezésre állás (99,999 %), adatok és a méretezhetőség, a beépített átfogó és rugalmas ütközés feloldása támogatási írni egy számjegyű ezredmásodperces késés. Ezek a szolgáltatások jelentősen egyszerűsítik a globálisan elosztott alkalmazások fejlesztését. Globálisan elosztott alkalmazások esetében több főkiszolgálós támogatást elengedhetetlen. 

![Több főkiszolgálós architektúra](./media/multi-region-writers/multi-master-architecture.png)

Azure Cosmos DB több főkiszolgálós támogatással hajtsa végre írási műveletek az adatok (például gyűjtemények, diagramok, táblákat) bárhol a világon elosztva tárolókon. Bármelyik régióban, az adatbázis-fiókkal társított adatok frissítheti. E adatfrissítések aszinkron módon is propagálása. Gyors és az adatok írási késése amellett több főkiszolgálós is praktikus megoldást kínál feladatátvételi és terheléselosztási problémák. Összefoglalva, az Azure Cosmos DB szolgáltatás írási késése < 10 ms, bárhol a világ, 99,999 %-os írási és olvasási rendelkezésre állás a bárhol a világon, és mindkét méretezhető 99 százalékon írási és olvasási teljesítménye bárhol a világon.   

> [!IMPORTANT]
> Több főkiszolgálós támogatást előzetes verzióban érhető el privát, előzetes verziójának használatához [regisztráció](#sign-up-for-multi-master-support) most.

## <a name="sign-up-for-multi-master-support"></a>Több főkiszolgálós támogatást regisztráljon

Ha már rendelkezik Azure-előfizetéssel, az Azure Portalon a több főkiszolgálós előzetes programhoz, is regisztrálhatnak. Ha még csak most ismerkedik az Azure-ral, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/free), amelynek keretében 12 hónapig ingyen használhatja az Azure Cosmos DB-t. A következő lépéseket igényelhet hozzáférést a több főkiszolgálós előzetes programjában.

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** elemre.  

2. Az új fiók oldalán adja meg az Azure Cosmos DB-fiók nevét, válassza ki az API-t, előfizetést, erőforráscsoportot és helyet.  

3. Ezután válassza ki **regisztráljon az előzetes verzióra még ma** többszörös főkiszolgáló előzetes mező alatti.  

   ![Több főkiszolgálós előzetes regisztráció](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. Az a **regisztráljon az előzetes verzióra még ma** ablaktáblán kattintson a **OK**. A kérelmet, miután a állapota **jóváhagyásra váró** a fiók létrehozása panelen.  

Az kérés benyújtása után, hogy a kérelem jóváhagyását e-mailben értesítést kap. A kérelmek nagy mennyisége miatt egy hét is eltelhet, amíg az e-mail megérkezik. Ezekhez a kérelmekhez nem kell támogatási jegyet leadnia. A kérelmeket azok beérkezési sorrendjében bíráljuk el.

## <a name="a-simple-multi-master-example--content-publishing"></a>Egy egyszerű több főkiszolgálós példa – tartalom közzététele  

Lássunk erre egy való életből vett forgatókönyv leírja, hogyan használható a több főkiszolgálós támogatást az Azure Cosmos DB. Érdemes lehet egy Azure Cosmos DB beépített tartalom közzétételi-platform. Az alábbiakban néhány követelmények megszabásához ezen a platformon egy nagyszerű felhasználói élményt biztosít a kiadók és a fogyasztók számára. 

* A világ minden táján szerzők és a előfizetők vannak elosztva.  

* Szerzők közzé kell tennie a cikkek (írás) a saját helyi (legközelebbi) régióban.  

* Szerzők olvasók/előfizetők cikkeik, a világ különböző pontjain rendelkezik.  

* Előfizetők kell értesítést kaphat, amikor új cikkek.  

* Előfizetők számára, olvassa el a cikkeket a helyi régióból képesnek kell lennie. Akkor is tudnia kell felülvizsgálatok hozzá ezeket a cikkeket.  

* Bárki, beleértve a cikkek szerzőjének kell lennie a helyi régióból cikkek csatolt összes felülvizsgálat képes megtekintése.  

Feltéve, hogy a fogyasztók és cikkeket, több milliárd közzétevőket hamarosan kell, hogy a skála helye való hozzáférés biztosítása mellett problémák leküzdésében. Használati eset az Azure Cosmos DB több főkiszolgálós egy tökéletes jelölt. 

## <a name="benefits-of-having-multi-master-support"></a>Több főkiszolgálós támogatást előnyei 

Globálisan elosztott alkalmazások több főkiszolgálós támogatást elengedhetetlen. Több főkiszolgálós áll [több fő régióban](distribute-data-globally.md) , amely egyaránt részt egy tetszőleges írási modell (aktív-aktív minta), és elérhető legyen a adatok bármikor fektetünk szolgál. Egy adott régióban végzett frissítések más régiók (amelyek viszont a saját fő régiók) aszinkron módon propagálva lesz. Azure Cosmos DB-régiók szerint fő régióban több főkiszolgálós konfigurációban automatikusan operációs éri el az adatokat az összes replika, és ellenőrizze, működik [globális összhangot és adatintegritás](consistency-levels.md). Az alábbi képen egy egyszeri és többszörös főkiszolgáló olvasási/írási replikációja.

![Egyszeri és többszörös főkiszolgáló](./media/multi-region-writers/single-vs-multi-master.png)

Saját végrehajtási több főkiszolgálós terheket hozzáadja a fejlesztők számára. Nagy méretű ügyfelek, akik több főkiszolgálós megvalósítani a saját próbál előfordulhat, hogy töltött órák, a világszerte több főkiszolgálós konfiguráció tesztelése és konfigurálása több száz, és számos mérnökök, amelynek egyetlen feladata, figyelése és karbantartása a több főkiszolgálós külön készlete a replikáció. Létrehozása és kezelése a saját több főkiszolgálós telepítés időt vesz igénybe a, távolabbi magának az alkalmazásnak az innováció erőforrásokat, és sokkal magasabb költségeket eredményez. Az Azure Cosmos DB "out-of-a-box" több főkiszolgálós támogatást nyújt, és a fejlesztők távolít el a terhelést.  

Összefoglalva, több főkiszolgálós az alábbi előnyöket nyújtja:

* **Hatékonyabb vészhelyreállítás, rendelkezésre állás és feladatátvétel írási**– több főkiszolgálós is használható a magas rendelkezésre állású nagyobb mértékben alapvető fontosságú adatbázisok megőrzése érdekében. Például egy több főkiszolgálós database replikálhatja adatokat egy adott régióban található egy feladatátvételi régióban, ha az elsődleges régió kimaradás vagy regionális katasztrófa miatt elérhetetlenné válik. Az ilyen egy feladatátvételi régióban erre a célra egy teljesen működőképes fő régió az alkalmazása támogatásához. Több főkiszolgálós nagyobb "túlélést" védelmet biztosít tartományállapot természeti katasztrófa, áramkimaradások vagy megtámadása, mert régiók fennmaradó képes használni a garantált írás rendelkezésre állási > 99,999 %-os földrajzilag eltérő több főkiszolgálót. 

* **A végfelhasználók számára írási késése továbbfejlesztett** – minél közelebb az adatok (azaz a jövőben kiszolgáló), hogy a végfelhasználók számára, annál jobb lesz a felhasználói élményt. Például akkor, ha az Európai felhasználók, de az adatbázis az Egyesült Államokban és Ausztrália, a hozzáadott késést, körülbelül 140 ms és a megfelelő régiókhoz 300 ms. Késések nem fogadhatók el a kezdéshez számos népszerű játékok, banki követelmények vagy interaktív alkalmazások (webes vagy mobilalkalmazás). Késés jelentős szerepet játszik az ügyfél érzete egy kiváló minőségű felhasználói élményt, és hatással lehetnek egyes észrevehető mértékben felhasználók viselkedését volt bizonyult. Javítja a technológia, és különösen az AR, VR és MR megjelenésével, még több magával ragadó és lifelike élményt, hogy a fejlesztők mostantól kell szigorú késési követelményekkel rendelkező a szoftver rendszerek előállításához. Ezért helyileg elérhető alkalmazások és adatok (az alkalmazások tartalma), akkor az több fontos. Több főkiszolgálós az Azure Cosmos DB a teljesítmény, gyors, mint a rendszeres helyi olvasási és írási és továbbfejlesztett globálisan által a földrajzi elosztás.  

* **Továbbfejlesztett írási méretezhetőség és a lemezírás teljesítménye** – több főkiszolgálós nagyobb átviteli sebességet biztosít, és miközben több konzisztenciamodelleket kínál helyességét a nagyobb kihasználtságát biztosítja, és a biztonsági másolatot készít az SLA-k. 

  ![Több főkiszolgálós méretezési lemezírás teljesítménye](./media/multi-region-writers/scale-write-throughput.png)

* **Kapcsolat nélküli környezetben (például a peremhálózati eszközökön) jobb támogatása** – több főkiszolgálós lehetővé teszi, hogy a felhasználók számára az összes replikálás vagy az adatok egy részét az edge-eszköz kapcsolat nélküli környezetben legközelebb eső régiót. Ebben a forgatókönyvben a jellemzően a sales force automation rendszerek, ahol egy személy hordozható (kapcsolat nélküli eszköz) tárolja-e az egyes értékesítő kapcsolatos adatok egy részét. Fő területek a felhőben találhatók bárhol a világon a másolatot a távoli peremhálózati eszközökre céljaként működhet.  

* **Terheléselosztás** – több főkiszolgálós a terhelés, az alkalmazás között is rebalanced való váltással felhasználók/munkafolyamatok erősen terhelt régióból régióban, ahol betöltési sebesség egyenletesen oszlik. Írás a kapacitást egyszerűen bővíthető hozzáadásával egy új régióban, és néhány írások majd átvált az új régióban. 

* **A kiosztott kapacitás jobban használata** – több főkiszolgálós írási műveltekből és a vegyes számítási feladatok esetén több régióban is telítsük a kiosztott kapacitás...  Bizonyos esetekben terjeszthet olvasási és írási több egyaránt, ezért ki kell építeni kisebb teljesítményt igényel, és több költségmegtakarítást eredményez.  

* **Egyszerűbb és rugalmasabb architektúrákat** -alkalmazások áthelyezése a több főkiszolgálós konfigurációs adatok gyors helyreállíthatóságáról garantált.  Az Azure Cosmos DB elrejtése az összes összetettségét lényegében leegyszerűsítheti az alkalmazás-tervezés és architektúra. 

* **Kockázatmentesen feladatátvétel tesztelése** -feladatátvétel tesztelése nem lesz bármilyen teljesítménycsökkenésről a lemezírás teljesítménye. Több főkiszolgálós minden más régiókban teljes-főkiszolgálót,, így a feladatátvétel nem sok hatással lesz a a lemezírás teljesítménye.  

* **Alacsonyabb teljes költség a Ownership(TCO) és DevOps** -értekezlet-méretezhetőséget, teljesítményt és globális disztribúciót, helyreállításiidő-célkitűzések általában költséges, drága bővítményeket vagy fenntartása a biztonsági mentési infrastruktúra inaktív amíg katasztrófa miatt is. Azure Cosmos DB több főkiszolgálós biztonsági másolatot készít az iparágvezető SLA-k fejlesztők már nem szükséges létrehozását és karbantartását az "integrációs háttérlogikát" magukat, és a biztonságot az alapvető fontosságú számítási feladatok futtatása. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Használati eseteket, ahol több főkiszolgálós támogatást van szükség

Nincsenek számos olyan alkalmazási helyzetek több főkiszolgálós az Azure Cosmos DB-ben: 

* **IoT** – Azure Cosmos DB több főkiszolgálós lehetővé teszi az IoT-adatok feldolgozása egyszerűsített elosztott végrehajtásához. Földrajzilag elosztott edge környezeteknek, amelyek CRDT ütközés ingyenes replikált típusok gyakran kell nyomon követnie az idősorozat-adatok több helyről adatokat. Minden eszköz a is egy legközelebb eső régiót a következő helyen, és a egy eszközt (például egy autó) is utazik, és képes dinamikusan rehomed írni egy másik régióban.  

* **E-kereskedelmi** -e-kereskedelmi forgatókönyvekben nagyszerű felhasználói élményt biztosítva van szüksége, magas rendelkezésre állást és hibatűrést bizonyos hibák bekövetkezése esetén. Abban az esetben, ha meghibásodik egy régióban, a vásárlás kocsik aktív felhasználói munkamenet kívánja listák kell zökkenőmentesen észlelnie kell egy másik régió állapot elvesztése nélkül. Addig a felhasználó által végrehajtott frissítéseket kell megfelelő kezelését (például ad hozzá, és a bevásárlókocsiba távolítja el a hálózaton keresztül helyezze át kell). Több főkiszolgálós Azure Cosmos DB képes kezelni ilyen forgatókönyvek például szabályosan, az a felhasználó szempontból konzisztens nézet fenntartásával aktív régiók közötti zökkenőmentes átmenet. 

* **Csalás/rendellenesség-észlelést** – gyakran olyan alkalmazásokat, amelyek figyelése, vagy felhasználói fiók tevékenysége globálisan terjesztett és kell nyomon követésére több eseményt egyszerre. Létrehozásakor, és a egy felhasználó pontszámok fenntartása, különböző földrajzi régiókból származó műveletek egyidejűleg, hogy a metrikák beágyazott kockázati pontszámok kell frissíteni. Az Azure Cosmos DB is, hogy biztosítsa a fejlesztők nem rendelkezik az alkalmazás szintjén ütközés forgatókönyvek kezeléséhez. 

* **Együttműködés** - alkalmazásokat, amelyek rangsora aszerint cikkeket, például a termékek értékesítése vagy a felhasznált media népszerűsége stb. Népszerűség követési földrajzi régióban bonyolult, lekérheti, különösen akkor, ha jogdíjak kell lennie, fizetős vagy valós idő hirdetési döntéseket kell tenni. Rangsorolás, rendezése és valós időben az Azure Cosmos DB világszerte számos régióban reporting lehetővé teszi a fejlesztőknek, hogy a szolgáltatások minimális erőfeszítéssel és az késleltetések kompromisszumok nélkül. 

* **Szoftverhasználat-mérő** - számbavételi és használat szabályozása (például az API-hívás, tranzakció/másodperc, perc használt) az egyszerűség kedvéért használja az Azure Cosmos DB több főkiszolgálós globálisan implementálható. Beépített ütközésfeloldás biztosítja mindkét számát és a valós idejű rendelet pontosságát. 

* **Személyre szabás** - e műveletek hűségprogramok használatán keresztül például azt a földrajzilag elosztott számlálók vagyunk karbantartása mutat díjazottjai vagy a személyre szabott felhasználói munkamenet végrehajtási megtekinti, a magas rendelkezésre állás és a földrajzilag elosztott egyszerűsített Azure Cosmos DB által biztosított számbavételi lehetővé teszi, hogy alkalmazások Delivery nagy teljesítményű egyszerűség. 

## <a name="conflict-resolution-with-multi-master"></a>Több főkiszolgálós ütközésének feloldása 

Több főkiszolgálós a kihívást gyakran, hogy két (vagy több) replikák ugyanazt a rekordot, előfordulhat, hogy frissíthető egyszerre két vagy több különböző régiókban lévő különböző írók. Egyidejű írások két különböző verziója ugyanazon rekord és beépített ütközésének feloldása nélkül is vezethet, és maga az alkalmazás a inkonzisztencia feloldásához ütközésfeloldás kell végrehajtania.  

**Példa** -tegyük fel, hogy használja az Azure Cosmos DB, az adatmegőrzés áruházban bevásárlókocsi alkalmazásai, és ezt az alkalmazást két régióban üzemel: USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA.  Ha körülbelül egy időben, a felhasználó a San franciscóban ad hozzá egy elemet a bevásárlókocsiba (például egy könyv) során egy készlet felügyeleti folyamatot az USA keleti régiójában érvényteleníti egy másik vásárlási bevásárlókocsi elemet (például egy új telefonszám), hogy egy s válaszul a felhasználók számára upplier értesítés, hogy a kiadási dátum kellett halasztva. A T1 időpontban a vásárlási bevásárlókocsi rekordokat a két régióban eltérőek. Az adatbázist fogja használni a replikáció és ütközésfeloldási eljárás a inkonzisztencia feloldásához, és végül a két a bevásárlókocsi-verziók valamelyikét lesz kiválasztva. Az ütközés feloldása heurisztika több főkiszolgálós adatbázisok (például a legutóbbi írási wins) által leggyakrabban alkalmazott használata esetén nem lehet a felhasználó vagy az alkalmazás előre, mely verziója lesz kiválasztva. Mindkét esetben adatvesztés, vagy váratlan oka lehet. Ha a keleti régió verzió van kijelölve, majd egy új vásárlási elem (azaz egy könyv) a felhasználó által választott elvész, és a nyugati régió van kijelölve, majd az előzőleg kiválasztott elem (azaz phone)-e továbbra is a kosárhoz. Mindkét esetben információ elvész. Végül egy másik folyamat, a vásárlás vizsgálatával bevásárlókocsiba nem determinisztikus viselkedést, valamint hogy történik a T1 és T2 időpont között. Például egy háttérfolyamatot, amely a teljesítési raktár kiválasztja, és frissíti a szállítási bevásárlókocsi szeretne eredményt, amelyek ütköznek a bevásárlókocsi végleges tartalmát. Alternatív 1 valóság válik a folyamat a nyugati régióban fut, annak ellenére, hogy tegyen a kosárba hamarosan előfordulhat, hogy csak egy elem, a könyvet, lenne számítási két elemet a szállítási költségeket. 

Az Azure Cosmos DB valósítja meg a logika az ütköző írások maga az adatbázis motorjában. Kínál az Azure Cosmos DB **átfogó és rugalmas ütközés feloldása támogatási** azzal több ütközés feloldása modellekhez, beleértve az automatikus (adattípusok CRDT ütközés ingyenes replikált), utolsó írás Wins (LWW), és egyéni () Tárolt eljárás) automatikus ütközésfeloldáshoz. Az ütközés feloldása modellek helyességét, és a konzisztencia garanciákat biztosít, és távolítsa el a terhet kell gondolniuk a konzisztencia, rendelkezésre állási, teljesítmény, replikációs késésétől és eseményeket a geo-feladatátvétel összetett kombinációi fejlesztők és régiók közötti írási ütközéseket.  

  ![Mult – fő ütközésének feloldása](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Ütközés feloldása modellek az Azure Cosmos DB által kínált 3 típusú kell. A szemantikát, az ütközés feloldása modellek a következők: 

**Automatikus** – Ez az az alapértelmezett ütközésfeloldási házirend. Ez a házirend kijelölése hatására az Azure Cosmos DB automatikusan feloldani a kiszolgálói oldalon az ütköző frissítései és-végleges-konzisztenciát garantál. Belsőleg az Azure Cosmos DB automatikus ütközésfeloldás kibővítéséhez ütközés-szabad-replikált-adattípusok (CRDTs) az adatbázis motorjában által valósítja meg.  

**Wins-utolsó-írási (LWW)** - időbélyeg-tulajdonság kiválasztása a szabályzat lehetővé teszi a megoldásához vagy alapján ütközések a rendszer által meghatározott szinkronizálva, vagy a rekordok ütköző verziójában definiált egyéni tulajdonság. Az ütközés feloldásának a kiszolgálói oldalon történik, és a verzió a legújabb időbélyeggel a nyertes van kiválasztva.  

**Egyéni** – az alkalmazás által meghatározott ütközés feloldása logikai regisztrálni egy tárolt eljárás regisztrálása. A tárolt eljárás akkor első meghívni, adatbázis-tranzakciók, a kiszolgálói oldalon támogatásával frissítési ütközések észlelése. Ha a beállítást, de a tárolt eljárás regisztrálása sikertelen (vagy ha a tárolt eljárás kivételt jelez, futtatás közben), hozzá az összes az ütköző verziók az ütközések Hírcsatorna-n keresztül és a megoldásukkal külön-külön.  

## <a name="next-steps"></a>További lépések  

Ebben a cikkben az Azure Cosmos DB globálisan elosztott, több főkiszolgálós használata tanulással. Ezután tekintse meg az alábbi forrásanyagokat: 

* [Ismerje meg hogyan támogatja az Azure Cosmos DB a globális terjesztés](distribute-data-globally.md)  

* [További tudnivalók az Azure Cosmos DB az Automatikus feladatátvétel](regional-failover.md)  

* [További tudnivalók az Azure Cosmos DB globális összhangot](consistency-levels.md)  

* Fejlesztés több régióra az Azure Cosmos DB - [SQL API](tutorial-global-distribution-sql-api.md), [MongoDB API-val](tutorial-global-distribution-mongodb.md), vagy [Table API](tutorial-global-distribution-table.md)  
