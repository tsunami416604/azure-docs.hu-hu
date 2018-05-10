---
title: Globális méret Azure Cosmos DB, több főkiszolgálós |} Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 12306b7868fa7fb2321f26657aab81beabb9db35
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális léptékű több főkiszolgálós 
 
Fejlesztés globálisan elosztott alkalmazásokat, amelyek válaszolni helyi késések pedig konzisztens-adatok nézeteinek világszerte karbantartása kihívást probléma. Az ügyfelek globálisan elosztott adatbázisok használható, mert a szükséges adatok elérési késést biztosít javítása, magas rendelkezésre állásának eléréséhez, és győződjön meg arról, garantált vész-helyreállítási és, (4) az üzleti követelményeinek megfelelően. Az Azure Cosmos Adatbázisba több főkiszolgálós biztosít magas szintű rendelkezésre állási (99.999 %), egyjegyű ezredmásodperces késési adatok és a méretezhetőség, beépített átfogó és rugalmas ütközés felbontás támogatása írni. Ezek a funkciók jelentősen leegyszerűsíti globálisan elosztott alkalmazások fejlesztésének. Globálisan elosztott alkalmazásokhoz, több főkiszolgálós támogatás elengedhetetlen. 

![Több főkiszolgálós architektúrája](./media/multi-region-writers/multi-master-architecture.png)

Azure Cosmos DB több főkiszolgálós támogatással írási műveleteket végezheti el az adatok (például gyűjtemények, diagramokat, táblák) bárhol a világon elosztott tárolók. Bármely régióban-adatbázis fiókjához tartozó adatok frissítheti. E adatfrissítések terjeszteni aszinkron módon képes. A gyors hozzáférést és az adatok írási késése mellett, több főkiszolgálós is gyakorlati megoldást kínál a feladatátvétel és terheléselosztás problémák. Összefoglalva, az Azure Cosmos DB kapott írási késése < bárhol a globális, 99.999 % írási és olvasási rendelkezésre állás érdekében bárhol a világon, és mindkét méretezési képességét 99th érték: 10 ms írási és olvasási átviteli bárhol a világon.   

> [!IMPORTANT]
> Több főkiszolgálós funkció előzetes verzióját használja, magán előnézetben [regisztráljon](#sign-up-for-multi-master-support) most.

## <a name="sign-up-for-multi-master-support"></a>Feliratkozás a több főkiszolgálós támogatása

Ha már rendelkezik Azure-előfizetéssel, regisztrálhat az Azure-portálon a több főkiszolgálós preview program csatlakozni. Ha most ismerkedik az Azure-ba, regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/free) hol szerezheti Azure Cosmos DB szabad hozzáférési 12 hónapig. Kövesse az alábbi lépéseket, hogy hozzáférést igényelhessen a több főkiszolgálós programot.

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **hozzon létre egy erőforrást** > **adatbázisok** > **Azure Cosmos DB**.  

2. Az új fiók lapon adja meg az Azure Cosmos DB fiók nevét, válassza ki a API, előfizetés, erőforráscsoportot és helyet.  

3. Ezután válasszon **regisztráljon az előzetes verzióra még ma** Multi Mater Preview mező alatti.  

   ![Regisztrálhat több főkiszolgálós előzetes verziójára](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. Az a **regisztráljon az előzetes verzióra még ma** ablaktáblán kattintson a **OK**. A kérelem elküldése után állapota **jóváhagyásra váró** a fiók létrehozása panelen.  

A kérelem elküldése után meg, hogy a kérelem jóváhagyását e-mailben értesítést fog kapni. A nagy mennyiségű kérést, mert egy héten belül értesítést kell kapnia. Nem kell a kérés teljesítéséhez el egy támogatási jegy létrehozását. Kérelem érkezett volt sorrendben felül kell vizsgálni.

## <a name="a-simple-multi-master-example--content-publishing"></a>Egy egyszerű több főkiszolgálós példa – tartalom közzététele  

A valós forgatókönyvekben, amelyek több főkiszolgálós támogatási használata az Azure Cosmos DB vizsgáljuk meg. Fontolja meg a közzétételi tartalomplatform Azure Cosmos DB épül. Az alábbiakban olyan követelményekkel, amelyeket ezen a platformon meg kell felelnie a kiváló felhasználói élmény a közzétevők és a fogyasztók. 

* Szerzők és a előfizetők megoszlanak a világ minden táján.  

* A helyi (legközelebbi) régióban szerzők (írás) cikkek közzé kell tennie.  

* Szerzők olvasók/előfizetők a cikkek felvételéhez világ különböző pontjain lehet.  

* Előfizetők kell értesítést kap, amikor új cikkek.  

* Előfizetők cikkek olvasni a helyi régió képesnek kell lennie. Is kell adhat hozzá az alábbi cikkek értékelést.  

* Bárki, beleértve a szerző, kattintson az alábbi cikkek cikkek csatlakozó összes értékelést egy helyi területet képes nézet kell lennie.  

Feltéve, hogy a fogyasztók és a közzétevők milliárd cikkeket, több millió hamarosan tudunk skála helység hozzáférés biztosítása mellett a problémák alkalmazásra. Használati eset tökéletes jelölt Azure Cosmos DB többszörös főkiszolgáló. 

## <a name="benefits-of-having-multi-master-support"></a>Több főkiszolgálós támogatási előnyei 

Több főkiszolgálós támogatás elengedhetetlen globálisan elosztott alkalmazások. Több főkiszolgálós áll [több fő régiók](distribute-data-globally.md) , amely egyaránt részt vesz egy írási bárhol modellben (aktív-aktív mintát), és segítségével győződjön meg arról, hogy érhetők el adatok bármikor ha esetleg szükség lenne rá. Egy adott régióban végrehajtott frissítések minden más régiókból, (amelyek viszont a saját fő régiók) aszinkron módon továbbítja. Ennek fő régiók több főkiszolgálós konfigurációban automatikusan működő Azure Cosmos DB régiók működik megfelelő, az összes replika adatainak, és győződjön meg arról [globális konzisztencia és adatintegritás](consistency-levels.md). Az alábbi ábrán egy egyszeres és mult-főkiszolgáló olvasási/írási replikálása.

![Egyszeres és több főkiszolgálós](./media/multi-region-writers/single-vs-multi-master.png)

Végrehajtási többszörös főkiszolgáló saját terheket fejlesztők ad hozzá. Nagy méretű az ügyfelek, akik több főkiszolgálós megvalósítani a saját megpróbálja tarthat több száz óra világszerte több főkiszolgálós konfiguráció tesztelése és konfigurálása, és számos rendelkezik dedikált egyetlen feladatokkal figyelni és karban a több főkiszolgálós mérnökök a replikáció. Létrehozása és kezelése a saját több főkiszolgálós telepítő időt vesz igénybe a, többet innovating magának az alkalmazásnak az erőforrásokat, és sokkal nagyobb költségeket eredményez. Azure Cosmos DB támogatja a több főkiszolgálós "out-of-az-box", és ez a terhelés megszünteti a fejlesztők számára.  

Összefoglalva, több főkiszolgálós a következő előnyöket nyújtja:

* **Vész-helyreállítási jobb, rendelkezésre állást és a feladatátvevő írás**-több főkiszolgálós használható a magas rendelkezésre állású nagyobb mértékben kritikus fontosságú adatbázis megőrzése érdekében. Például több főkiszolgálós adatbázis is replikálja az adatokat egy régióban feladatátvételi régióban, ha az elsődleges régióban kimaradás vagy regionális katasztrófa miatt nem érhető el. Feladatátvételi terület erre a célra egy teljesen működőképes fő területet alkalmazás támogatásához. Több főkiszolgálós jelenítik természeti katasztrófa, áramkimaradások, vagy megtámadása vagy mindkettő nagyobb "túlélést" védelmet nyújt a mivel a földrajzilag eltérő több főkiszolgálók egy garantált írási rendelkezésre állási > 99.999 % a fennmaradó régiók lehetnek. 

* **A végfelhasználók számára írási késése továbbfejlesztett** – minél közelebb a adatok (szolgál), hogy a végfelhasználók számára, annál jobb lesz a felhasználói élmény. Például akkor, ha a felhasználók az Európai rendelkezik, de az adatbázis mérete az Egyesült Államokban és Ausztrália, a hozzáadott várakozási nem körülbelül 140 ms és a megfelelő régiókhoz 300 ms. Késések nem fogadhatók el kezdődnie számos népszerű játékok, banki követelmények vagy interaktív alkalmazások (web- vagy mobilalkalmazás). Késleltetés hatalmas szerepet játszik az ügyfél érzete a kiváló felhasználói élmény, és hatással lehet egyes észrevehető mértékben felhasználók viselkedésének lett bizonyult. Növeli a technológia, és különösen a AR, VR és MR megjelenésével még több hatásos és lifelike feladatait, hogy a fejlesztők most kell szigorú késési követelményekkel rendelkező szoftver rendszerek létrehozásához. Ezért helyileg elérhető alkalmazások és adatok (az alkalmazások tartalmat) rendelkező, több fontos. Az Azure Cosmos Adatbázisba több főkiszolgálós teljesítmény szerint rendszeres helyi olvas és ír a gyors és továbbfejlesztett globálisan által földrajzi terjesztési esetén.  

* **Továbbfejlesztett írási méretezhetősége és teljesítménye** – több főkiszolgálós ad nagyobb átviteli teljesítményt, és nagyobb kihasználtsági több konzisztencia modellek helyességét, miközben biztosítja, hogy és biztonsági mentést készített SLA-k. 

  ![A több főkiszolgálós méretezési teljesítménye](./media/multi-region-writers/scale-write-throughput.png)

* **Jobb támogatása a kapcsolat nélküli környezetben (például peremeszközök)** -több főkiszolgálós lehetővé teszi a felhasználók az összes replikálás vagy az adatok egy részét a peremhálózati eszköz egy leválasztott környezetben legközelebbi régiót. Ebben a forgatókönyvben a jellemző az értékesítési munkatársak automation rendszerek, ahol egy adott hordozható (kapcsolat nélküli eszközt) tárolja az értékesítő kapcsolatos adatok egy részét. A távoli peremeszközök a forráslemez a célként fő régióban a felhőben találhatók bárhol a világon működtetéséhez.  

* **Terheléselosztás** -a több főkiszolgálós a terhelés, az alkalmazás között is rebalanced áthelyezésével felhasználók/munkaterhelések erősen terhelt régióban régiókban, ahol egyenletesen vannak elosztva a terhelés. Az írási kapacitás egyszerűen bővíthető hozzáadásával egy új területet, és néhány írások majd átváltása az új terület. 

* **Kiosztott kapacitást jobban használható** – a több főkiszolgálós írási műveleteket és a vegyes munkaterhelések esetén különféle régiókban is telítsük a kiosztott kapacitást...  Egyes esetekben terjeszthet olvasási és írási több egyaránt, így kevesebb átviteli építeni van szükség, és több költségmegtakarítást vezet.  

* **Egyszerűbb és rugalmasabb architektúrát** -alkalmazások áthelyezése több főkiszolgálós konfigurációs adatok rugalmasság beolvasása garantált.  Azure Cosmos DB elrejtése az összes összetettségét az jelentősen egyszerűsítheti az alkalmazás tervezési és architektúra. 

* **Kockázati mentes feladatátvételi tesztelése** -feladatátvétel tesztelése nem rendelkezik majd romlását teljesítménye. A több főkiszolgálós, minden más régiók a következők teljes-főkiszolgálók, a feladatátvétel nem sok hatással lesz a a teljesítménye.  

* **Alacsonyabb Ownership(TCO) teljes költség- és Devopok** -kielégíti a méretezhetőséget, teljesítményt, globális terjesztési helyreállítási időre vonatkozó célkitűzések általában költséges költséges bővítmények vagy egy biztonsági másolat infrastruktúrájának, amely aktívan csak katasztrófa miatt éri. Azure Cosmos DB több főkiszolgálós biztonsági mentést készített iparágvezető SLA-k, a fejlesztők már nem szükséges létrehozása és karbantartása a "háttér kapcsolás logika" magukat, és lekérése egy nyugodt maradhat afelől fut a kritikus fontosságú munkaterhelésekhez. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Ha van szükség a több főkiszolgálós támogatási használati esetek

Nincsenek számos használati esetek többszörös főkiszolgáló Azure Cosmos DB: 

* **Az IoT** -Azure Cosmos DB több főkiszolgálós lehetővé teszi, hogy egyszerűsített IoT adatfeldolgozási elosztott végrehajtása. Földrajzilag elosztott peremhálózati telepítésekkel CRDT-ütközés mentes replikált adatok típusok gyakran kell idő adatsorozat adatainak követéséhez több helyről. Egyes eszközök is lehet a következő helyen a legközelebbi régiók egyikéhez, és egy eszköz (például egy autó) is utazik, és dinamikusan is rehomed írni egy másik régióban.  

* **Elektronikus kereskedelmi** -kereskedelmi forgatókönyvekben kiváló felhasználói élményt biztosítva van szüksége, magas rendelkezésre állás és a rugalmasság hiba forgatókönyvekre. Abban az esetben, ha nem sikerül egy régiót, a vásárlás kártyák, aktív felhasználói munkamenet kívánja listák kell zökkenőmentesen észlelnie kell egy másik régió szerinti adatvesztés nélkül. Az idő alatt a felhasználó által végrehajtott frissítések kell kezelni megfelelően (például ad hozzá, és eltávolítása a bevásárlókocsiból a hálózaton keresztül helyezze át kell). A több főkiszolgálós Azure Cosmos DB képes kezelni ilyen forgatókönyvek például szabályosan, az a felhasználó szempontból egységes megjelenítése megőrzésével aktív régiók közötti zökkenőmentes átmenet. 

* **Csalás/Anomáliadetektálás** -gyakran olyan alkalmazásokat, amelyek figyelése, vagy felhasználói fiók tevékenység globálisan fel vannak osztva és kell nyomon követésére több eseményt egyidejűleg. Létrehozása, és a felhasználó az pontszámait fenntartása, miközben a különböző földrajzi területek műveletek egyidejűleg frissítenie kell tartani a kockázat metrikák beágyazott pontszámait. Azure Cosmos DB biztosítsák a fejlesztők nem rendelkezik az alkalmazás szintjén ütközés forgatókönyvek kezeléséhez. 

* **Együttműködés** - alkalmazásokat, amelyek rangsora aszerint időben népszerűvé vált a cikkek például pénztári vagy adathordozón felhasznált terméket stb. Követés időben népszerűvé vált a földrajzi régiók közötti bonyolult, kérhet, különösen akkor, ha a jogdíjak kell fizetett vagy valós idő hirdetési döntést kell tenni. Prioritás, rendezése és sok világszerte, valós idejű Azure Cosmos DB régiók közötti reporting lehetővé teszi a fejlesztők számára biztosítanak szolgáltatást minimális erőfeszítéssel és késések fordulnak elő a veszélyeztetése nélkül. 

* **Szoftverhasználat-mérő** - számbavételi és használati szabályozásához (például az API-hívások, tranzakciók/másodperc, perc használt) globálisan implementálható az egyszerűség kedvéért több főkiszolgálós Azure Cosmos DB használatával. Beépített ütközésének feloldása mindkét pontosságát számát, valamint a valós idejű szabályozás biztosítja. 

* **Személyre szabás** - e most karbantartása földrajzilag elosztott számlálók műveleteket végez, például hűség kiváltó mutat díjak vagy személyre szabott felhasználói munkamenet végrehajtási, a magas rendelkezésre állású megtekintése és egyszerűsített földrajzi eloszlása Azure Cosmos DB, által biztosított számbavételi lehetővé teszi, hogy a alkalmazások kézbesítése magas teljesítményt és az egyszerűség kedvéért. 

## <a name="conflict-resolution-with-multi-master"></a>A több főkiszolgálós ütközésének feloldása 

Több master a kérdés esetén gyakran, hogy ugyanazt a rekordot két (vagy több) replikáinak frissítheti egyidejűleg két vagy több különböző régiókban különböző írók tudják-e. Egyidejű írási műveletek azt eredményezheti, hogy két különböző verziója ugyanazon rekord, és beépített ütközésének feloldása nélkül, és magának az alkalmazásnak kell végeznie az ütközések feloldása a inkonzisztencia feloldásához.  

**Példa** -tegyük fel, hogy használt Azure Cosmos DB az állapotmegőrző tárolóban egy vásárlásra szolgáló bevásárlókocsiból alkalmazás és az alkalmazás telepítve van a két régióban: USA keleti régiója, és az USA nyugati régiója.  Ha körülbelül egy időben, a felhasználó a San Francisco egy elem hozzáadása a kosár (például egy könyv) során egy készlet folyamatát a USA keleti régiója érvényteleníti az, hogy a felhasználók egy s válaszul egy másik bevásárlási bevásárlókocsiból elemet (például egy új telefonszám) a kiadási dátum kellett halasztva upplier értesítést. A T1 időpontban a vásárlási bevásárlókocsiból rögzíti a két régió eltérőek. Az adatbázis fogja használni a replikáció és ütközésfeloldási eljárás a inkonzisztencia feloldásához, és végül a kosár két verzióinak valamelyike lesz kiválasztva. Az ütközés feloldása heurisztikus leggyakrabban a több főkiszolgálós adatbázisok (például utolsó írási wins) által alkalmazott használ, nem lesz kiválasztva melyik verzióját becsülhető alkalmazás vagy a felhasználó számára. Mindkét esetben adatok nem vesztek el, vagy váratlan oka lehet. Ha keleti régió verzió van kiválasztva, majd egy új beszerzési elem (Ez azt jelenti, hogy egy könyv) a felhasználó által kiválasztott elvész, és ha a nyugati terület van kiválasztva, majd a korábban kiválasztott elemet (Ez azt jelenti, hogy a telefon) van továbbra is a bevásárlókocsiból. Mindkét módszer esetén információ elvész. Végül tanulmányozza a vásárlás e más folyamat nem determinisztikus viselkedését, valamint hogy lesz a T1 és T2 időpont között kosárhoz. Például háttérfolyamatként, amely a teljesítési raktár kiválasztja, és frissíti a bevásárlókocsiból szállítási költséget kellene eredményt, amelyek ütköznek a bevásárlókocsiból végleges tartalmát. Ha a folyamat nyugati a régióban fut, és alternatív 1 valóságban válik, annak ellenére, hogy a bevásárlókocsiból hamarosan esetleg csak egy elemet, a könyv azt kellene számítási két elemek szállítási költségeket. 

Azure Cosmos-adatbázis valósítja meg az ütköző írások maga az adatbázis motorjában kezelése logikáját. Azure Cosmos DB kínál **átfogó és rugalmas ütközés felbontás támogatása** több ütközés feloldása modellek, beleértve az automatikus felajánlásával (adattípusok CRDT-ütközés mentes replikált), utolsó írás Wins (LWW), és egyéni () A tárolt eljárás) automatikus ütközésfeloldáshoz. Az ütközés feloldása modellek helyességét és a konzisztencia garanciák adja meg, és távolítsa el a terheket a fejlesztők számára kell gondolniuk a konzisztencia, rendelkezésre állási, teljesítmény, replikációs késésétől és a földrajzi-feladatátvétel események összetett kombinációi és kereszt-régió írási ütközések.  

  ![Mult – master ütközésének feloldása](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Ütközés feloldása modellek Azure Cosmos DB által kínált 3 típusú lesz. Az ütközés feloldása modellek szemantikáját a következők: 

**Automatikus** -az alapértelmezett ütközés feloldása szabályzat. Ez a házirend kiválasztásának hatására a kiszolgáló oldalán ütköző frissítések automatikus megoldása és erős-végleges-konzisztencia biztosítja, hogy az Azure Cosmos DB. Belsőleg az Azure Cosmos DB automatikus ütközésének feloldása emelés ütközés-szabad replikált-adatok típusok (CRDTs) az adatbázis motorjában valósítja meg.  

**Wins-utolsó-írás (LWW)** - időbélyeg-tulajdonság kiválasztása, ez a házirend lehetővé teszi a megoldásához vagy alapján ütközések a rendszer által meghatározott szinkronizálva, vagy a rekordok ütköző verziójában definiált egyéni tulajdonság. Az ütközés feloldásának történik, a kiszolgáló oldalán, és a legújabb Timestamp értékkel rendelkező verzióját a győztes választotta.  

**Egyéni** – az alkalmazás által meghatározott ütközés feloldása logika regisztrálni egy tárolt eljárás regisztrálása. A tárolt eljárás akkor beolvasása meghívni egy adatbázis-tranzakciókhoz, a kiszolgáló oldalán támogatásával frissítés ütközések észlelése. Ha a beállítást, de nem sikerült regisztrálni a tárolt eljárás (vagy a tárolt eljárás kivételt futásidőben), összes keresztül az ütközések hírcsatorna egymással ütköző verziók eléréséhez, és egyenként oldható fel.  

## <a name="next-steps"></a>További lépések  

Ebben a cikkben szerzett tapasztalatok globálisan elosztott több főkiszolgálós használata Azure Cosmos DB. Ezután tekintse meg a következőket: 

* [Hogyan támogatja az Azure Cosmos DB a globális terjesztési megismerése](distribute-data-globally.md)  

* [További tudnivalók az Azure Cosmos Adatbázisba automatikus feladatátvétel](regional-failover.md)  

* [További tudnivalók az Azure Cosmos DB globális egységesítése](consistency-levels.md)  

* Több régióba fejlesztést használata az Azure Cosmos DB - [SQL API](tutorial-global-distribution-sql-api.md), [MongoDB API](tutorial-global-distribution-mongodb.md), vagy [tábla API](tutorial-global-distribution-table.md)  