---
title: "Az Azure - Cortana Intelligence megoldássablonban űrtechnikai a prediktív karbantartási |} Microsoft Docs"
description: "A Microsoft a Cortana Intelligence űrtechnikai, segédprogramok és szállítására prediktív karbantartási megoldás sablon."
services: cortana-analytics
documentationcenter: 
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: fboylu
ms.openlocfilehash: aafa395f8c0593d9597f74cd5cd2a41f26897c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>A Cortana Intelligence megoldás sablon alkalmazástervezési űrtechnikai és más vállalatok prediktív karbantartás
## <a name="executive-summary"></a>Vezetői összefoglaló
Prediktív karbantartás költségmegtakarítások nagy mennyiségű unarguable előnyt prediktív elemzés leginkább kért alkalmazások egyike. Ez a forgatókönyv célja egy hivatkozást a prediktív karbantartási megoldásokat, különös tekintettel a főbb használati eseteket.
Ahhoz, hogy megkapja az olvasó a leggyakrabban használt üzleti forgatókönyvek prediktív karbantartási kihívásai jogosult az ilyen megoldások, oldja meg ezeket üzleti problémák, a prediktív modellezési módszereket a szükséges adatok az üzleti problémák megértése kész Ezek az adatok és ajánlott eljárások használata minta megoldás architektúrák megoldás felépítéséhez.
Azt is bemutatja a részletekről a prediktív modellek fejlett például szolgáltatás mérnöki csapathoz, fejlesztési és teljesítmény értékelés modell. Lényegében ez a forgatókönyv egyesíti az üzleti és a szükséges egy sikeres fejlesztési és üzembe helyezésére használt prediktív karbantartási megoldásokat analitikai irányelveket. Ezeket az irányelveket használatával Cortana Intelligence Suite és kifejezetten az Azure Machine Learning a prediktív karbantartási hosszú távú stratégiával a kiindulási pontként kezdeti megoldás létrehozása a célközönség érdekében el kell készíteni. A Cortana Intelligence Suite és az Azure Machine Learning dokumentációs található [Cortana Analytics](http://www.microsoft.com/server-cloud/cortana-analytics-suite/overview.aspx) és [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lapokat.

> [!TIP]
> Ez a megoldás sablon végrehajtási műszaki útmutatóját, lásd: [műszaki útmutató a Cortana Intelligence megoldás sablont prediktív karbantartási](cortana-analytics-technical-guide-predictive-maintenance.md).
> A diagram, amely ezt a sablont az architektúra áttekintése letöltéséhez lásd: [architektúra a Cortana Intelligence megoldás sablon prediktív karbantartási](cortana-analytics-architecture-predictive-maintenance.md).
> 
> 

## <a name="playbook-overview-and-target-audience"></a>Forgatókönyv áttekintése és azon célközönség
Ez a forgatókönyv kihasználják a különböző hátterek és a prediktív karbantartási terület érdeklődési műszaki és a nem technikai jellegű célközönség vannak rendezve. A forgatókönyv két általános vonatkozásaira prediktív karbantartási megoldásokat a különböző típusú és azok részleteit ismerteti. A tartalom elemeket egyaránt kiegyensúlyozott a közönség számára, akik csak a megoldás és milyen típusú alkalmazásokat, valamint ezek a megoldások megvalósításához olyan eszközökre, és ezért a technikai részleteket is ismertetése.

Ez a forgatókönyv tartalma többsége nem feltételezi azt, a korábbi adatok tudományos Tudásbázis vagy szakértői. Azonban a forgatókönyv egyes részei kell némileg tudományos – fogalmak a megvalósítás részletei követve ismeretét. Teljes mértékben kihasználhassa az anyag szakaszt bevezető szintű adatai tudományos ismeretek szükségesek.

A forgatókönyv tartalma bevezetés prediktív karbantartási alkalmazások első felében, hogyan ahhoz, hogy egy prediktív karbantartási megoldás, közös gyűjteménye esetekben használata az üzleti probléma részleteit a körülvevő ezeket az adatokat használja, az esetek és a üzleti előnyök végrehajtási a prediktív karbantartási megoldásokat. Ezek a szakaszok bármely technikai tudásszintjük, a prediktív elemzési tartomány nem feltétlenül szükséges.

A forgatókönyv második felében azt a prediktív karbantartási alkalmazások és megvalósításához a használati esetek ismertetett első felében a forgatókönyv példáit ezek a modellek prediktív modellezési módszereket típusú fedik le. Ezt szemlélteti átnézi adatok előfeldolgozása lépésein, például az adatok címkézés és a szolgáltatás műszaki osztály, a modell kiválasztása, a képzési/tesztelés és a teljesítmény értékelési ajánlott eljárások. Ezek a szakaszok olyan technikai célközönség alkalmas.

## <a name="predictive-maintenance-in-iot"></a>Az IoT prediktív karbantartás
Lehet, hogy a nem ütemezett berendezések állásidő hatását rendkívül ilyen beállítások mellett pusztító a vállalkozások számára. Fontos annak a mező berendezések kihasználtságát és a teljesítmény maximalizálása érdekében fut, és csökkentésével költséges, nem tervezett leállás. Egyszerűen nincs az aktuális üzleti műveletek helyszín megfizethető Várakozás a meg nem történik a. Versenyképesség fenntartása érdekében a vállalatok keressen új módszereket azáltal, hogy eszköz teljesítmény maximalizálásához különféle csatornák gyűjtött adatok felhasználásával. Egyik fontos ilyen adatelemzés módja korábbi minták segítségével előre jelezni jövőbeli eredményt prediktív elemzési módszer használatára. A legnépszerűbb egyik ezek a megoldások nevezik prediktív karbantartási, amely általában lehet meghatározva, de nem kizárólagosan a lehetőségét, hogy egy eszköz hiba a közeljövőben előrejelzésére az, hogy az eszközök az alábbiak biztosíthatók hibák proaktív azonosítása és művelet végrehajtása előtt a hiba történik. Ezek a megoldások szabályszerűségeket hiba, amely a legnagyobb veszély eszközök meghatározásához. A korai problémákat azonosítása korlátozott karbantartási erőforrások telepítése több költséghatékony és minőségének javítása érdekében, valamint ellátási lánc folyamatok segítségével.

Az eszközök internetes hálózatát (IoT) alkalmazások egyre, prediktív karbantartási növekvő figyelmet az iparág az adatgyűjtést, eszközöket és technológiák feldolgozása van jelen, elegendő a készítése, továbbítja, tárolását és elemzését az összes, kötegekben telepítse, vagy a valós idejű adatok. Ilyen technológiák könnyen fejlesztésére és üzembe végpontok közötti megoldások speciális elemzési megoldásairól, prediktív karbantartási megoldásokat biztosító késései a legnagyobb előny az engedélyezése.

Üzleti problémák miatt a váratlan meghibásodások és összetett üzleti környezetekben probléma alapvető okának korlátozott betekintést magas működési kockázat a prediktív karbantartási tartomány közé. Ezek a problémák csoportosíthatók az alábbi üzleti kérdéseket alá:

* Mi az a valószínűség berendezés sikertelen a közeljövőben?
* Mi az a készülék hasznos élettartama?
* Mik az okok hibák és karbantartási műveleteket kell elvégezni a problémák elhárításához?

Ezek a kérdések megválaszolása prediktív karbantartási felügyelniük a vállalatok számára a következőket teheti:

* Működési kockázat csökkentése, és növelheti eszközök megtérülési hibák gyorsabban ahhoz, azok történt
* Csökkentse a szükségtelen időalapú karbantartási műveleteket és a karbantartási költségek szabályozásához
* Általános márka kép javítása, hibás nyilvánosságra vonatkozó és az ügyfél kopásállóság eredményül kapott elveszett értékesítési megszüntetéséhez.
* Készlet által a újrarendelési pont előrejelzésére csökkentésével alacsonyabb készlet költségek
* Csatlakozik a különböző karbantartási problémákat minták felderítése

Prediktív karbantartási megoldásokat biztosíthat a vállalatok számára a fő teljesítménymutatók, például a health pontszámok valós idejű eszköz állapota, becsült hátralévő élettartamát az eszközök, az ajánlás megelőző karbantartás tevékenységek figyeléséhez és becsült rendelés dátumok részek cserélni.

## <a name="qualification-criteria-for-predictive-maintenance"></a>Minősítési feltételek prediktív karbantartás
Fontos, hogy nem minden esetben használja, vagy üzleti problémák hatékonyan megoldhatók prediktív karbantartási emelje ki. Fontos minősítési feltételek tartalmazza-e a probléma prediktív ideiglenesek, egy tiszta elérési útja művelet már leállítja, nehogy hibák, ha a legfontosabb, és előzetesen fel van elegendő quality támogatásához a használati eset adatok érhető el. Itt azt összpontosítani a sikeres prediktív karbantartási megoldás kialakításának követelményeit.

Prediktív modellek létrehozása, amikor előzményadatok használatával betanítani a modellt, majd azonosítani a rejtett mintákat és további azonosíthatja ezeket a mintákat, a jövőbeli adatokban. Ezek a modellek képzett példák szerint a hozzájuk tartozó funkciók áttekintése és előrejelzés célját. A betanított modell várhatóan előrejelzéseket készítsen a célszámítógépen található új példák a szolgáltatások csak megtekintésével. Rendkívül fontos, hogy a modell rögzítése funkciók és előrejelzés célját közötti kapcsolat. Ahhoz, hogy egy hatékony gépi tanulási a modell betanítását, igazolnia kell a betanítási adatok, amelyek ténylegesen előrejelzési, ami azt jelenti, az adatok az előrejelzés célja, hogy pontos előrejelzéseket várt kell végezni a cél felé prediktív power szolgáltatást tartalmaz.

Például ha a cél előre jelezni vonat kerekek hibák, a betanítási adatok kerék szolgáltatásokkal (pl. telemetriai adat tükröző állapota kerekek, távolság, a car terhelés, stb.) kell tartalmaznia. Azonban ha a cél vonaton motort hibák előre jelezni, valószínűleg kell egy másik készlet a betanítási adatok, amelyek motor funkcióiról. A prediktív modellek létrehozása, előtt várhatóan az üzleti szakértői az alkalmazhatóságát követelménynek megismeréséhez, és adja meg a tartomány Tudásbázis ahhoz szükséges, hogy válassza ki a megfelelő részhalmaza adatok elemzésére.

Nincsenek azt keresni, amikor egy üzleti probléma egy prediktív karbantartási megoldás alkalmasnak jogosult három alapvető adatforrások:

1. Hiba előzmények: Prediktív karbantartási alkalmazásokban, sikertelen események általában nagyon ritkán fordul elő. Azonban amikor a hibák előrejelző prediktív modellek létrehozása, az algoritmus kell ismerje meg, a normál működés mintát, valamint a sikertelen mintát, a képzési folyamatot. Ezért fontos, hogy tartalmaz-e a betanítási adatok mindkét kategóriák szereplő példák elegendő ahhoz, hogy ismerje meg, ezek két eltérő mintát. Éppen ezért kérjük, hogy rendelkezik-e elegendő számú hibaesemények adatokat. Sikertelen események karbantartási rekordok megtalálhatók, és részek helyettesítő előzmények vagy a betanítási adatok rendellenességeinek is használható sikertelen a tartomány szakértők által meghatározott.
2. Karbantartási javítási előzmények: Az alapvető prediktív karbantartási megoldásokat adatok forrása a cseréje vonatkozó információkat tartalmazó objektum karbantartási előzményeinek, megelőző karbantartás céljából aktiválja elvégezni, stb. Ezeket az eseményeket rögzíteni, ezek befolyásolják a teljesítménycsökkenés minták, rendkívül fontos, és ezek az információk hiányában félrevezető eredmények okoz.
3. Gép feltételek: Ahhoz, hogy előre jelezni, hogy hány nap (óra, miles, tranzakciók, stb.) a gép tart előtt nem sikerül, feltételezzük, hogy idővel a működése során csökkenti a gép állapotát. Ezért várhatóan az adatokat tartalmazhat idő-változó, amely a korosodási mintát és bármely rendellenességeket rögzítését, amely csökkenéséhez vezet. Az IoT-alkalmazásokhoz a telemetriai adatokat a különböző érzékelők határoz meg egy jó példa. Ahhoz, hogy előre jelezni, ha egy számítógép adott időn belül sikertelen lesz, ideális esetben a kell adatrögzítő megalázó trend a időtartományban, a tényleges esemény előtt.

Emellett szükséges, amely közvetlenül kapcsolódik az üzemi körülmények a cél eszköz előrejelzési adatokat. A cél döntési mindkét üzleti igényeinek, valamint az adatok rendelkezésre állását alapul. A vonat kerék előrejelzés példaként tart, azt is előrejelzése "Ha a kerék fog hiba az" vagy "az egész vonaton állapotra vált, sikertelen". Az elsőt pontosabb összetevő célozza meg, míg a második érték sikertelen volt-e a vonat célozza. A második érték egy általános kérdést, mint az első, így nehezebb a modell létrehozása sokkal több szétszórt adatelemek igénylő. Ezzel ellentétben kerék hibák előrejelzése csak megtekintésével a magas szintű tanítási adatokat próbál nem lehet valósítható meg, nem tartalmaz információt az összetevők szintjén. Ez általában több előre meghatározott hibaesemények mint általánosabb kijelölve.

Egy gyakori kérdést, amely általában kapcsolatba kapcsolatos hiba előzményadatok "hogyan sok hibaesemények szükségesek kell még betanítani a modellt és hány minősül"elegendő"? Nem egyértelmű kérdésre adandó választ hasonlóan a prediktív elemzés több forgatókönyv áll, hogy általában határozzák meg, hogy mi elfogadható az adatok minőségét. Ha a dataset nem tartalmaz, amely kapcsolódik a előrejelzés funkciókat, majd akkor sem, ha sok hibaesemények felépítése helyes modell nem lehet lehetséges. A szokásos megoldás azonban, hogy a további a hibaesemények annál jobb a modell és a nyers becsült, hogy hány hiba példák szükség egy nagyon környezet és az adatok függő mérték. A szakaszban imbalanced adatkészletek kezelése, ahol azt javaslatok nem rendelkezik elegendő hibáinak problémák kezelése tárgyalt probléma.

## <a name="sample-use-cases"></a>Példa használati esetek
Ez a szakasz a prediktív karbantartási használhatók a különböző iparágak például űrtechnikai, segédprogramok és szállítására gyűjteménye összpontosít. Minden szakasz azokat a használati esetek összegyűjtése ezeknek a területeknek az eseményrögzítési, valamint egy üzleti probléma, az adatok az üzleti problémát, és egy prediktív karbantartási megoldás előnyeit körülvevő ismerteti.

### <a name="aerospace"></a>Űrtechnikai
#### <a name="use-case-1-flight-delay-and-cancellations"></a>Használati eset 1: Repülési késleltetés és a sikertelen
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Egy légitársaság arcfelismerési a jelentős járó költségek járatok gépi problémák miatt késik a alatt a fő üzleti problémák. A gépi hiba nem javítható, ha járatok még érvényteleníthető. Ez az nagyon költséges, késések ütemezés és a műveletek, az okok rossz megbízhatóságibesorolás-kezelési és a felhasználói kapcsolatos mellett sok más problémákat a problémák hozzon létre. Légitársaság különösen érdeklik gépi hibák előre előrejelzésére, így csökkenthetik a felhőszolgáltató közötti átviteléhez működés lelassulhat, vagy a sikertelen. Ezekben az esetekben a prediktív karbantartási megoldás célja a hibalehetőségek számát repülőgép késleltetett vagy visszavont, érintett adatforrások, például karbantartási előzményekhez és a felhőszolgáltató közötti átviteléhez az útvonal-információkat alapján előre jelezni. A két fő adatforrásait a használati eset a felhőszolgáltató közötti átviteléhez ágak és a lap naplók. Repülési engedélyezi adatok közé tartoznak az adatait a felhőszolgáltató közötti átviteléhez Útvonaladatok, például a dátum és idő indító és érkezésének, indító és érkezési repülőtéren, stb. Lap naplóadatokat sorozatát tárolja, a karbantartási személyzet hiba és a karbantartás kódokat tartalmaz.

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
A rendelkezésre álló előzményadatok használatával, a prediktív modell lett létrehozva egy több osztályozó algoritmus használatával megjósolható gépi probléma, így ez a késleltetés vagy a felhőszolgáltató közötti átviteléhez megszakítása a következő 24 órában típusa. Az előrejelzés tételével szükséges karbantartási műveletek használható-e a kockázatok csökkentése, amíg a repülőgép javítás alatt áll, és így megakadályozható a lehetséges késést és a sikertelen. Azure Machine Learning webszolgáltatás segítségével, a prediktív modellek zökkenőmentesen és egyszerűen integrálhatják légitársaság meglévő operációs rendszerek. 

#### <a name="use-case-2-aircraft-component-failure"></a>Használati eset 2: Repülőgép összetevők hibája esetén.
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Repülőgép motorok nagyon érzékeny és költséges eszközökre és motor része cserékhez között a légitársaság iparágban általános karbantartási feladatokat. Karbantartási megoldásokat légitársaság gondos felügyeleti összetevő készlet rendelkezésre állásának, és a tervezési igényelnek. Gyűjtse össze a beruházási költségek jelentős csökkenéséhez vezet az eszközintelligencia összetevő megbízhatóság igényt. Ebben az esetben használja a fő adatforrás azzal a feltétellel a repülőgép érzékelők repülőgép olyan információkat, számos gyűjtött telemetriai adatokat. Karbantartási rekordok is használtak összetevőinek meghibásodása esetén történt, ezért a csere történt azonosításához.

##### <a name="business-value-of-the-predictive-model"></a>A prediktív modell üzleti értéket
A többszörös osztály besorolási modell, amely a valószínűsége annak, ha egy bizonyos összetevő miatt nem képes a következő hónapon belül lett létrehozva. Ezek a megoldások alkalmazásával légitársaság is összetevő javítási költségek csökkentése, összetevő-készlet rendelkezésre állásának javítása, csökkentse a kapcsolódó eszközök készlet szintek és javítása karbantartási tervezési.

### <a name="utilities"></a>Segédprogramok
#### <a name="use-case-1-atm-cash-dispense-failure"></a>Használati eset 1: ATM cash eredményeként hiba
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Azok a vezetők eszköz intenzív iparágakban gyakran állapot, hogy a vállalkozások számára elsődleges működési kockázat-e az eszközeik váratlan meghibásodások esetén. Tegyük fel gépek adatközponthoz például banki iparági hibája nagyon gyakori probléma gyakran előfordul. Ilyen jellegű problémák készítése prediktív karbantartási megoldásokat nagyon hasznos az operátorok ilyen gépek. A használati eset előrejelzés probléma lesz kiszámítása a valószínűsége annak, hogy egy ATM cash visszavonása tranzakció megszakadt, lekérdezi miatt például egy dokumentum a probléma megoldásáig a pénzbeli adagoló hibája vagy egy része sikertelen volt. Ebben az esetben a fő adatforrások mérések összegyűjtése közben cash megjegyzések anyagot mérnek érzékelő szivattyútelepek érzékelőinek adatai, és karbantartási rekordok összegyűjtött adott idő alatt. Érzékelőadatok érzékelő értékek egyes tranzakciónként befejeződött, és minden mellőzni Megjegyzés / érzékelő értékek tartalmazza. A érzékelő megadott értékek mérési vastagsága, megjegyzések között lévő hézagokat például vegye figyelembe, hogy érkezési távolság stb. Karbantartási hibakódok és javítási információkat tartalmazza. Ezek hiba esetek azonosítására használt.

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
Két prediktív modelleket előre jelezni a pénzbeli visszavonása tranzakciók hibáinak és az egyes megjegyzések mellőzni tranzakció során hibák lett létrehozva. Mivel tranzakció hibák előre megjósolható, adatközponthoz is kiszolgálásra proaktív ezáltal megakadályozhatja, hogy a hiba lépett fel. Is, a Megjegyzés előrejelzés, ha a tranzakció valószínűleg hiba fog előfordulni, ahhoz, hogy a Megjegyzés miatt teljes eredményeként hiba, elképzelhető, hogy a legjobb, ha a folyamat leállítása és a felhasználói tranzakció nem teljes, nem pedig a karbantartási kiszolgálásra várakozó érkezésére figyelmeztetés Miután a hiba akkor fordul elő, ami azt eredményezheti, hogy nagyobb felhasználói kapcsolatos.

#### <a name="use-case-2-wind-turbine-failures"></a>Használati eset 2: Szél turbinás hibák
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
A környezeti tájékoztatási megjelenítése szél turbinák váltak a fő források, energia generációs egyikét, és általában költségeket millióira dolláros. A legfontosabb összetevők szél turbinák az egyik a kódgenerátor motor, amely rendelkezik a sok érzékelők, amelyek segítségével turbinás feltételek és állapotának figyelése a. Az érzékelő értékek előre jelezni kritikus a fő teljesítménymutatók (KPI-k) a prediktív modell létrehozásához használható értékes információt tartalmaznak, például átlagos idő a szél turbinás összetevőihez. Ebben az esetben használja az adatok több szél turbinák három különböző farm helyeken található származik. Az egyes turbinás száz érzékelők megközelíti a mértékek rögzítve egy évig minden 10 másodperc. Ezek az értékek közé tartozik például hőmérséklet, generátor sebességétől, turbinás power és generátor felszámolására mérések.

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
A prediktív modellek generátorokat és hőmérséklet-érzékelő fennmaradó élettartama becsléséhez lett létrehozva. Előrejelzésére hiba valószínűségét, karbantartási technikusok is, amelyek valószínűleg nem hamarosan időalapú karbantartási rendszerek kiegészíteni gyanús turbinák összpontosítanak.
A prediktív modellek emellett insight kerüljön hozzájárulás különböző tényezők a valószínűsége annak, a hiba, amely segít jobban ismeri a problémát az okozza az üzleti szint.

#### <a name="use-case-3-circuit-breaker-failures"></a>Használati eset 3: áramköri megszakító hibák
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Létrehozás, terjesztési és elektromos energia pénztári elektromosság kialakulása, a gáz műveletek karbantartási annak biztosítása érdekében kiemelt sorok működési garantálja az energia kézbesítését háztartásoknak mindig jelentős mennyiségű igényelnek. Az ilyen műveletek hibája kritikus, szinte minden entitás power problémák jelentkeznek régiókban függvénye. Kör szóhatároló kritikus műveletek, mert azok, amelyek esetén a problémák és rövid Kapcsolatcsoportok akadályozható bármely sérülést kiemelt sorok elektromos áram Kivágás berendezés. Az üzleti probléma ebben az esetben használja az előre megadott karbantartási naplókat, a parancselőzményekben és a műszaki adatok áramköri megszakító hibák.

Három fő adatforrások ebben az esetben, amely tartalmazza az megoldani a problémát, megelőző és rendszeres műveletek karbantartási naplókat, amely tartalmazza az automatikus és manuális parancsok működési adatokat küldeni a kapcsolatcsoport szóhatároló, mint a Megnyitás és Bezárás műveletek és a technikai minden áramköri megszakító például év, a helyet, a modell, a stb tulajdonságainak Specification adatait.

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
Prediktív karbantartási megoldásokat javítási költségek csökkentése és az eszközökre, például a kapcsolatcsoport szóhatároló életciklusát növelése érdekében. Ezek a modellek is segítenek, mivel modellek biztosítják időben figyelmeztetések, amelyek csökkentik a váratlan meghibásodások esetén, amelyek a szolgáltatás kevesebb zavart okozhat az energiagazdálkodási hálózati minőségének javítása.

#### <a name="use-case-4-elevator-door-failures"></a>Használati eset 4: Foglalhatja ajtó hibák
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Legtöbb nagy foglalhatja a vállalatok általában még több millió világszerte futtató felvonók. Ahhoz, hogy egy versenyképes él, azok összpontosítson, a megbízhatóságot, amely fontos információk leginkább az ügyfeleknek. A felvonók felhőhöz történő csatlakoztatásának és foglalhatja érzékelők és rendszerek, adatok összegyűjtése a az eszközök internetes hálózatát, a lehetséges a rajzolási képesek értékes üzleti intelligencia, ami jelentősen javítja a műveletek tett adatok átalakítása prediktív és preemptive karbantartási, amely nincs valami, amely még a versenytársak számára elérhető. Ebben az esetben az üzleti követelményt arra, hogy a Tudásbázis prediktív alkalmazás, amely képes a lehetséges okai ajtó hibák. Három részből foglalhatja statikus szolgáltatások (pl. azonosítók szerződést kötöttek karbantartás gyakorisága, épület típusát stb.), amelyek használati adatait (pl. száma ajtó ciklusokat, átlagos ajtajának bezárása idő stb.) áll ez a megvalósítás szükséges adatokat és Hiba (azaz korábbi hiba rögzíti és okaik) előzményeit.

A multiclass logisztikai regresszió modell előrejelzésének probléma, az integrált statikus szolgáltatások és szolgáltatások használati adatok és a korábbi hiba rekordok osztály feliratok okok Azure Machine Learning segítségével lett létrehozva. A prediktív modell működő hatékonyabbá mező szakemberek által használt mobileszközökön az alkalmazás által felhasznált. Technikus egy foglalhatja kijavításához helyen kerül, ha többé hivatkozhat ezt az alkalmazást az ajánlott okok és a legjobb szükséges karbantartási műveletek lassabbak, mint a lehetséges foglalhatja ajtók javításához.

### <a name="transportation-and-logistics"></a>Szállítási és logisztikai
#### <a name="use-case-1-brake-disc-failures"></a>Használati eset 1: Berendezés lemez hibák
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
Tipikus karbantartási házirendeket járművekről gyűjtött korrekciós és megelőző karbantartás tartalmazza. Javítási karbantartási azt jelenti, hogy a vehicle javítása után hiba történt, amely egy súlyos kellemetlenségért egy váratlan hibás működése miatt illesztőprogram okozhat, és az idő a szerelő meglátogatása kihasználatlan. A legtöbb járművekről gyűjtött egy megelőző karbantartás céljából házirend, amelyhez bizonyos vizsgáló egy ütemezést, amely nem veszik figyelembe a car alrendszereket jelenlegi állapota is vonatkoznak. Ezek a módszerek sem sikeres teljes mértékben kiküszöbölhetők azon problémák. A konkrét használati eset itt egy autó, amely nyomon követi a korábbi vezetői megoldások és más feltételeket, amelyek a car van kitéve a kulcsszava rendszeren telepítve érzékelők keresztül gyűjtött adatok alapján előrejelzés berendezés lemez. Ebben az esetben az egyik legfontosabb adatforrás az érzékelők adatain mérő, például gyorsítás, fékezés kombinációját, befolyásoló tényezők távolság, sebességet, stb. Ezt az információt, amelyhez statikus ezeket az adatokat car funkciók, például súgó build jó előre használható prediktív modellben készlete. Egy másik készlet lényeges információk a hiba adatait, amely erről a rész rendelés adatbázis (ne a tartalék rész rendelési és a mennyiségek autók javítás alatt, a kereskedők).

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
A prediktív megközelítés üzleti értéke jelentős. A prediktív karbantartási rendszer ütemezheti a prediktív modell alapján viszonteladó meglátogatása. A modell van a car és a vezetői előzmények aktuális állapotát jelző csökkent érzékelő adatokat is alapulhat. Ez a megközelítés minimalizálja a váratlan meghibásodások esetén, amely a következő rendszeres karbantartás előtt is előfordulhat kockázatát.
Azt is csökkenti a szükségtelen megelőző karbantartás céljából. Illesztőprogram proaktív módon, hogy a változás kijelzők előfordulhat, hogy erre szükség van néhány hét, és adja meg az adatnak a viszonteladó tájékoztatni. Az osztó sikerült ezután előkészíti az egyes karbantartási csomagot az illesztőprogramhoz előre.

#### <a name="use-case-2-subway-train-door-failures"></a>Használati eset 2: Subway vonat ajtó hibák
##### <a name="business-problem-and-data-sources"></a>*Üzleti probléma és adatforrások*
A fő oka késést és a problémák subway műveletek egyike vonat autók ajtó hibák. Rendkívül fontos előrelátás előrejelzésére, ha egy vonat autó előfordulhat, hogy ajtó hiba vagy tudnak a következő door hiba: a napok számát előrejelzési. Vonat ajtó karbantartás optimalizálása, és csökkentheti a vonat lefelé időtartamát lehetőséget biztosít.

#### <a name="data-sources"></a>Adatforrások
A használati eset a három adatforrások 

* **eseményadatok betanítása**, vagyis előzményrekordjaira vonat események, 
* **karbantartási** karbantartási, munkahelyi rendelés és rugalmasságtípusokat, valamint prioritás kódok, például  
* **rögzíti a hibákat**.

##### <a name="business-value-of-the-predictive-model"></a>*A prediktív modell üzleti értéket*
Két modell előre jelezni a következő nap hiba valószínűség bináris osztályozás és nap vége: regressziós használatával hiba lett létrehozva. A korábbi esetekben hasonló, a modellek létrehozása minőségének javítása és az ügyfelek elégedettségének növelése hozzájárul a rendszeres karbantartási rendszerek rengeteg lehetőséget.

## <a name="data-preparation"></a>Adatok előkészítése
### <a name="data-sources"></a>Adatforrások
A közös adatelemek a prediktív karbantartási problémákat a következőképpen összegezhető:

* Hiba előzmények: egy számítógép vagy a gépen összetevő hibája előzményeit.
* Karbantartási napló: a gép, pl. hibakódok, előző karbantartási tevékenységek vagy összetevő cserékhez javítási előzményeit.
* A gép feltételek és a használati: pl. adatok gép üzemi körülmények összegyűjtése az érzékelők.
* Számítógép-szolgáltatások: a gép, pl. motor mérete, a szolgáltatások gyártmány, és a modell, helyét.
* Operátor szolgáltatások: az operátor részeit, pl. nemek közötti, korábbi tapasztalatok.

Lehetséges, és általában az, hogy hiba előzmények szerepel-e különleges hibakódok vagy tartalék részek rendelés dátumai formájában például karbantartási előzmények esetben. Ezekben az esetekben hibák kiolvasható a karbantartási adatokból. Emellett különböző üzleti tartományok rendelkezhetnek más adatforrások hiba mintákat, amelyek nem szerepelnek a listán itt kimerítően befolyásoló különböző. Ezek a tartomány szakértői tanácsadás, amikor a prediktív modellek kell azonosítani.

Néhány példa a fenti adatelemek a használati esetek a következők:

Hiba előzmények: késleltetés dátumok, repülőgép összetevő hibája dátumok és típusok, ATM cash visszavonása tranzakció sikertelen, vonat/foglalhatja ajtó hibák, berendezés lemez helyettesítő rendelés dátumok, szél turbinás hiba dátumok és áramköri megszakító parancs hibák küzdelemre.

Karbantartási előzmények: repülési hibanaplókat, ATM tranzakció hibanaplókat, például karbantartási típusa, rövid leírás stb és áramköri megszakító karbantartási rekordok karbantartási rekordok betanításához.

A gép feltételek és a használati: repülési továbbítja, és időpontokat, repülőgép motorok, érzékelő értékek ATM tranzakciókból származó érzékelő adatokat betanítása események adatai, a szél turbinák, anyag- és csatlakoztatott autók érzékelő értékek.

Számítógép-szolgáltatások: áramköri megszakító műszaki specifikációk például feszültség szintek, ellenőrizze, a model, a motor például földrajzi hely meghatározásának vagy car funkciót mérete, kulcsszava típusok, éles létesítmény stb.

A fenti adatforrások megadott, a két fő adattípusok prediktív karbantartási tartomány azt láthatja a következők historikus és statikus adatok.
Hiba az előzmények gép feltételek származnak javítása előzmények, használati esetek többségében az időbélyegek minden adat gyűjtemény időpontját jelzi. Gép operátor szolgáltatásokkal és általában olyan statikus óta általában a műszaki adatok a gépeket vagy üzemeltető ismertetik. Lehetséges, hogy ezeket a funkciókat változnak az idők, és ha igen azok kell kezelni időbélyegzővel adatforrások.

### <a name="merging-data-sources"></a>Az egyesítés adatforrások
Beolvasása szolgáltatás műszaki osztály vagy szalagcímkézési folyamat bármilyen típusú, előtt kell előkészítenie a képernyőn, a szolgáltatások létrehozásához szükséges az adatok. A végső célja, hogy a rekord létrehozása minden alkalommal egységéhez minden eszköz, a címkéket és szolgáltatásai kell a gépbe tanulási algoritmus. Ahhoz, hogy készítse elő a tiszta végső adatkészlet, néhány előre feldolgozásra lépéseket kell tenni. Első lépésként-felosztása időegységek, ahol minden rekordot tartozik egy időegységet az adott eszköz számára az adatok gyűjteményét időtartama. Adatgyűjtés is lehet osztani műveletek, például más egységek azonban az egyszerűség érdekében a többi magyarázatokat használjuk az egységeket.

A következő mértékegység ideig lehet a másodperc, perc, óra, nap, hónap, ciklus miles vagy az adatok előkészítése és a változások attól függően, hogy a hatékonyság tranzakciók be a másik vagy egyéb tényezők idő egységből származó az eszköz a feltételek konkrét a tartományhoz. Más szóval időegységét nem kell lehet ugyanaz, mint számos adatgyűjtési gyakoriságát adatok esetekben előfordulhat, hogy jelenjen meg a másik az egyik eltérést. Például ha hőmérséklet értékek 10 másodpercenként volt gyűjtenek, egy 10 másodperc az egész elemzéshez időegységét kiadási inflates példák száma további adatok megadása nélkül. Jobb stratégia lehet akár egy óráig példaként átlagos használja.

A korábbi szakaszban leírt lehetséges adatforrások példa általános sémák a következők:

Karbantartási rekordok: ezek a végre karbantartási műveleteket rögzíti. A nyers karbantartási adatok rendszerint előre időbélyeg karbantartási tevékenységeket elvégezte akkori információkat és az eszköz azonosítója. Ilyen nyers adatok esetén karbantartási tevékenységek kell minden egyes kategória, amely megfelel a karbantartási művelet kategorikus oszlopokba fordítani. Az alapszintű adatok séma karbantartási rekordok eszköz azonosítója, az idő és a karbantartási művelet oszlopot tartalmazhat.

Hiba a rekordok: ezek a hibák vagy a hiba oka telepítésnek előrejelzés célját tartozó a rekordok. Ezek lehetnek egyes hibakódok vagy hibák által meghatározott üzleti feltétel meghatározott események. Néhány esetben az adatok több hibakódok hibák iránt, amelyek megfelelnek közé tartoznak. Nem minden olyan előrejelzési célját, más hibák általában használhatók összefügghetnek a kivizsgált hibák funkciókra összeállításához. Az alapszintű adatok-sémát a sikertelen műveletet tartalmazhat eszköz azonosítója, az idő és a hiba vagy a hiba oszlopok Ha OK érhető el.

Számítógép-feltételek: ezek a lehetőleg valós idejű figyelési adatok üzemi körülmények kapcsolatos adatok. Például ajtó hibák ajtó megnyitása és záró idő is ajtók aktuális feltételről helyes mutatók. Az alapszintű adatok sémát, a gép feltételek eszköz azonosítója, időt és az állapot értéke oszlopot tartalmazhat.

Számítógép és az operátorral adatok: számítógép-és operátor egyesíthető egy séma azonosítására, mely eszköz mely tulajdonságok eszköz és az operátorral együtt üzemeltető azon műveleteket végezni. Például egy autó általában tulajdonában van egy illesztőprogram attribútumokkal kora, például befolyásoló tényezők élmény stb. Ha ezek az adatok idővel változik, az adatok is tartalmaznia kell egy time oszlopa, és adatokat különböző szolgáltatás generációs idő szerint kell kezelni. Az alapszintű adatok sémát, a gép feltételek eszköz azonosítója, eszköz, operátor azonosító operátor szolgáltatásokkal és tartalmazhat.

A végső tábla címkézés és a szolgáltatás létrehozása előtt gép feltételek tábla hiba a rekordok eszköz azonosítója és időt tartalmazó mezőiben az összekötő balra válthat ki. Ez a táblázat majd lehetne illeszteni eszköz azonosítója karbantartási rekord és az időt mezők, és végül gép és az operátorral együtt szolgáltatásokat biztosító eszköz azonosítója. Az első bal oldali illesztés elhagyja hiba oszlop null értékeket számítógép esetén a normál működés, normál művelet egy mutató értékének imputálni kell ezeket. A hiba oszlop címkék a prediktív modell létrehozásához használt.

### <a name="feature-engineering"></a>Jellemzőkiemelés
Az első lépés a modellezési szolgáltatás műszaki osztály. A szolgáltatás generációs lényege, fogalmilag leíró, és a készülék állapota feltétel absztrakt korábbi összegyűjtött adatok ki, hogy mikor használ egy adott időpontban. A következő szakaszban a prediktív karbantartási és hogyan a címkézés történik a módszer használható technikák típusú áttekintést nyújtunk. A pontos technika, amely kell használni az adatok és az üzleti probléma függ. Azonban az alábbiakban funkció mérnöki módszerek használható alapjául a szolgáltatások létrehozásához. Az alábbi arról lesz szó lag funkciókat, amelyek a időbélyegeket, és statikus adatforrásokból létrehozott statikus szolgáltatások adatforrásokból kell kialakítani, és adja meg a használati esetek említett példákat követik.

#### <a name="lag-features"></a>Lag szolgáltatások
Már említettük, a prediktív karbantartás rendszerint előzményadatokat előre Timestamp minden adat gyűjtemény időpontját jelzi. Számos módon szolgáltatások létrehozásának időbélyegzővel adatokkal adatait. Ebben a szakaszban arról lesz szó a prediktív karbantartási használt módszerek közül. Azonban hogy nincs korlátozva ezeket a módszereket. Mivel szolgáltatás mérnöki csapathoz egy prediktív modellezési legtöbb kreatív területéhez tekinthető, szolgáltatások létrehozásához sok más módon lehet. Itt nyújtunk néhány általános technikákat.

##### <a name="rolling-aggregates"></a>*Működés közbeni összesítések*
Az egyes rekordokhoz, egy eszköz azt a "W", amely a száma, amelyek a korábbi összesítések számítási szeretnénk időegységekkel méretének működés közbeni ablakban válassza ki. Majd számítási működés közbeni összesített funkciók a dátum előtt, hogy a rekord W időszakok. Működés közbeni összesítések példákat is működés közbeni, számát, azt jelenti, szórás szorzataként, a szórás szorzataként alapján kiugró, CUSUM méri, időszak minimális és maximális értékeket. Egy másik érdekes módszer akkor trend, teljesítményt és az szint módosítása algoritmusokkal, amely a rendellenességek észlelését anomáliadetektálási észlelési algoritmusokat adatok rögzítéséhez.

A bemutatóban lásd: 1. ábra, ahol azt felel meg az egyes Munkaegységek a kék sorok idő az adott eszköz számára rögzített érzékelő adatok, és jelölje meg a működés közbeni átlagos szolgáltatás számítását W = 3 t a rekordok<sub>1</sub> és t<sub>2</sub> csoportosításokat rendre zöld és narancssárga jelzi.

![1. ábra. Működés közbeni összesített szolgáltatások](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

1. ábra. Működés közbeni összesített szolgáltatások

Példaként repülőgép összetevő meghibásodása esetén a múlt héten, az elmúlt három napban és utolsó napja értékeket érzékelő volt létrehozásához használt működés közbeni azt jelenti, szórás és sum szolgáltatások. Hasonlóképpen a ATM hibák, a nyers érzékelő értékek és a működés közbeni azt jelenti, középérték, a tartomány, a szórás szorzataként használták kiugró túl három szórás szorzataként, a felső és alsó CUMSUM funkciók száma.

Repülési késleltetés előrejelzés hiba múlt héten szereplő szolgáltatások létrehozásához használt módszert. Vonat ajtó hibák az események számát utolsó napján, az előző 2 hétben események számát, valamint a számát is események az elmúlt 15 napban varianciáját lag szolgáltatások létrehozásához használt. Azonos számbavételi karbantartási-események lett megadva.

Emellett W válassza háttérszínnek által megadott nagyon nagy (pl. év), akkor nézze meg a teljes előzmények számbavételi minden karbantartási rögzíti, például egy eszköz lehet hibák stb. amíg a rekord idején. Ez a módszer az utolsó három évig áramköri megszakító hibák leltárhoz lett megadva. Is vonat hibák, az összes karbantartási események számlált rögzítheti a hosszú távú karbantartási hatások szolgáltatás létrehozásához.

##### <a name="tumbling-aggregates"></a>*Átfedésmentes összesítések*
Minden egyes referenciakonfigurációjához címkézett rekord, azt válasszon egy ablak méretének "W -<sub>k</sub>" a számát vagy a windows méretű "W" lag szolgáltatások létrehozásához szeretnénk k esetén. "-k" rögzítheti a hosszú távú teljesítménycsökkenést minták sok vagy néhány rövid távú hatások rögzítésére is tárolható. Használjuk majd k átfedésmentes windows W -<sub>k</sub> , W -<sub>(k-1)</sub>,..., W -<sub>2</sub> , W -<sub>1</sub> rekord időpontja előtt időszakokra összesített szolgáltatások létrehozásához és idő (lásd a 2. ábra). Ezek is állapotmodelljeiig windows rekord szinten egy időegységet, amely nem rögzíti a 2. ábrán, de az ötletére azonos módon ábra 1 where t<sub>2</sub> bemutatják a működés közbeni hatása is használható.

![2. ábra Összesített szolgáltatások átfedésmentes](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

2. ábra Összesített szolgáltatások átfedésmentes

Tegyük fel, a szél turbinák, L = 1 és k = 3 hónapig minden felső és alsó kiugró használatával az elmúlt 3 hónapban lag szolgáltatások létrehozásához használt.

#### <a name="static-features"></a>Statikus szolgáltatások
Ezek a műszaki adatok a készülék, például a gyártás dátum modellszámát, hely, stb. Míg lag szolgáltatások többnyire numerikus ideiglenesek, statikus szolgáltatás általában válik a modellek kategorikus változók. Egy példa, áramköri megszakító tulajdonságait, például feszültség, aktuális és power specifikációk átalakító típusok együtt az áramforrásokból stb használták. Berendezés lemez hibák kulcsszava típusú kerekek úgy, mintha csak azok készült vagy acélból használták, a statikus funkciók némelyike.

Szolgáltatás létrehozása során például a hiányzó értékeket vagy normalizálási kezelése néhány más fontos lépést kell végrehajtani. Számos módon hiányzó érték imputálási, és adatok normalizálási, amelyek itt nem esik szó. Azonban célszerű próbálja meg előrejelzés teljesítmény növekedését lehetséges, hogy különböző módszereket.

A végső szolgáltatás táblában mérnöki lépéseket a korábbi szakaszban tárgyalt funkció kell hasonlítania az alábbi példa adatkulcsokat, ha idő egysége egy nap után:

| Eszköz azonosítója | Time | A szolgáltatás oszlopok | Címke |
| --- | --- | --- | --- |
| 1 |Naponta 1 | | |
| 1 |2 nap | | |
| ... |... | | |
| 2 |Naponta 1 | | |
| 2 |2 nap | | |
| ... |... | | |

## <a name="modeling-techniques"></a>Modellezési módszereket
A prediktív karbantartási gyakran alkalmazó üzleti kérdéseket, amely előfordulhat, hogy az a prediktív modellezési terv számos különböző szögek kell válaszadásra nagyon hatékony tartomány. A következő szakaszokban lévő nyújtunk fő technikák, amely a modell különböző üzleti kérdéseket is prediktív karbantartási megoldás segítségével. Habár az Hasonlóságok, minden modellnek eltérően összeállításával címke, amely részletesen ismerteti. Egy kísérő erőforrásként hivatkozhat, amely megtalálható a mintakísérleteket belül Azure Machine Learning a prediktív karbantartási sablon. Az online anyag a sablonnak az alábbi hivatkozásokat erőforrások szakaszában vannak megadva. Láthatja, hogyan néhány funkció mérnöki módszert kapcsolatban a fentiekben ismertetett és a modellezési technika, amely a következő szakaszokban ismertetett előrejelzése repülőgép motor hibák Azure Machine Learning segítségével is vonatkozik.

### <a name="binary-classification-for-predictive-maintenance"></a>A prediktív karbantartási bináris osztályozás
Bináris osztályozási prediktív karbantartási szolgál annak, hogy egy jövőbeli időtartamon belül kimaradása előre jelezni. Az adott időszakban határozza meg, és az üzleti szabályok és az elvégzendő az adatok alapján. Néhány közös idő minimális átfutási idő kell vásárolnia kárt összetevők vagy telepítéséhez karbantartási erőforrás próbálja megoldani a problémát, amely valószínűleg az adott időszakba karbantartási feladatok elvégzéséhez szükséges idő valószínűleg lecseréli tartalék részei lesznek. A jövőbeli horizon időszak "X" nevezzük.

Ahhoz, hogy bináris osztályozási, igazolnia kell a pozitív és negatív telepítésnek példákat kétféle azonosításához. Minden egyes példája egy rekordot, amely egy időegységet fogalmilag leíró, és az adott szolgáltatás mérnöki használatával előzmény és más adatforrások korábban leírt időegységét legfeljebb üzemi körülmények absztrakt eszköz tartozik. A prediktív karbantartási bináris osztályozási környezetében pozitív típus jelent a hibák (címke 1), és negatív típus jelent a normál működés (címke = 0) ahol feliratai kategorikus típusú. A célja, hogy a modell, amely azonosítja, sikertelen vagy megfelelően működik-e a következő belül minden új példa található X időegységet.

#### <a name="label-construction"></a>Címke létrehozása
Ahhoz, hogy a kérdés megválaszolásához prediktív modellek létrehozása "Mi az a valószínűsége annak, hogy az eszköz nem sikerül, a következő időegységekkel X?", címkézés történik az eszköz és a címkézés őket "készül az nem", a meghibásodás előtt véve X rögzíti (címke = 1) összes címkézés közben  egyéb bejegyzéseket "normál" (címke = 0). Ezzel a módszerrel a címkék (lásd a 3. ábra) kategorikus változók.

![3. ábra. A bináris osztályozási címkézés](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

3. ábra. A bináris osztályozási címkézés

Repülési késést és a sikertelen, az X választja ki a rendszer egy nap késések előrejelzése 24 órán belül. Hibák előtt 24 órán belül minden repülőútra 1s lett címkézve. ATM cash eredményeként a hibák, két bináris besorolási modell lett létrehozva, előre jelezni a következő 10 percen tranzakció hibája valószínűségét és előre jelezni a hiba a következő 100 kiegészítő jelentési valószínűségét. Minden tranzakció, és ismételje meg a hiba az utolsó 10 percen belül vannak címkézve 1. az első modell. És a hibák a legutóbbi 100 megjegyzésekben mellőzni összes megjegyzések második modell 1 lett címkézve. Áramköri megszakító hibák a feladat, hogy a valószínűsége annak, hogy a következő áramköri megszakító parancs sikertelen, amelyben eset X van kiválasztva egy jövőbeli parancs kell előre jelezni. Vonat ajtó hibák a bináris osztályozási modell előre jelezni sikertelen a következő 7 napon belül lett létrehozva. X szél turbinás hibák, mint 3 hónapos választott.

Turbinás szél és esetek is használja ugyanazokat az adatokat élettartamuk fennmaradó előre regressziós elemzés, de egy másik szalagcímkézési stratégia a következő szakaszban ismertetett felügyelniük ajtó betanításához.

### <a name="regression-for-predictive-maintenance"></a>A prediktív karbantartási regressziós
Prediktív karbantartási regressziós modell segítségével számítási hasznos élettartama (Szabályainak) egy eszköz, amely van definiálva, mennyi ideig, amely az eszköz működési előtt a következő hiba történik. Ugyanaz, mint a bináris osztályozási, minden egyes példa: egy rekord, amely egy adott eszköz számára időegység "Y" tartozik. Azonban regressziós összefüggésében célja található, amely kiszámítja a fennmaradó hasznos élettartama minden új példa egy folyamatos számot, amely az időtartam, a hiba előtt hátralévő modell. Ebben az időszakban bizonyos számú többszöröse az Y hívása. Minden egyes példa fennmaradó használatuk során, ami számítható ki, hogy például a következő hiba előtt hátralévő idő mérésével is rendelkezik.

#### <a name="label-construction"></a>Címke létrehozása
A kérdés megadott, "Mi az a készülék hasznos élettartama?
", felirataihoz, ha a a regressziós modell lehet létrehozni a meghibásodás előtt minden rekordot, és hány időegységekkel továbbra is a következő hiba előtt kiszámításával címkézés őket. Ezzel a módszerrel a feliratai folyamatos változók (lásd a 4. ábra).

![4. ábra. A regresszió címkézés](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

4. ábra. A regresszió címkézés

Eltér a bináris osztályozási regressziós, az eszközök esetleges hibákat, az adatok nélkül nem használható modellezési állapotalapú hiba ponttá címkézés történik, és a számítást nincs lehetőség anélkül, hogy tudnák, hogy mennyi ideig az eszköz megélték előtt Hiba történt. Ezzel a problémával legjobb túlélési Analysis nevű másik statisztikai technikával.
Nem fogjuk túlélési elemzés megvitatni a forgatókönyv miatt a potenciális komplikációk esetleg felmerülő a technika, például az idő-változó adatok rendszeres időközönként a prediktív karbantartási használati esetek alkalmazásakor.

### <a name="multi-class-classification-for-predictive-maintenance"></a>A prediktív karbantartási besorolást több osztály
Több osztály besorolás prediktív karbantartási segítségével előre jelezni jövőbeli két eredményt. Az első címtárra egy eszköz hozzárendelése a több lehetséges időszakokra, hiba időtartományt adhat az egyes eszközök egyikét. A második érték a több alapvető okok egyike miatt egy jövőbeli időszakban a hibák valószínűségét azonosításához. Amely lehetővé teszi, hogy a karbantartási személyzet az ennek az információnak a problémák kezeléséhez előre rendelkeznek. Egy másik osztály több modellezési technika összpontosít a legvalószínűbb okának meghatározása egy adott hibát. Ez lehetővé teszi a javaslatok szükséges ahhoz, hogy javítsa ki a hibát a felső karbantartási műveleteket kell megadni.
Azzal, hogy a legfelső szintű rangsorolt listáját hatására és a kapcsolódó javítási műveletek  
lehet, hogy hatékonyabb hiba után az első javítási műveletek végrehajtása a technikusok.

#### <a name="label-construction"></a>Címke létrehozása
A két kérdéseket, amelyek adott "Mi az a valószínűsége annak, hogy egy eszköz nem sikerül, a következő"aZ"időegység"a"az időszakok száma" és "Mi az a valószínűsége annak, hogy az eszköz nem sikerül, a következő probléma miatt időegységekkel X" P<sub>i</sub>" hol található az "i" a a számos ok kiválthatja, címkézés megtörtént a következő módon azokat a módszereket.

Az első kérdést címkézés végezhető el aZ rögzíti egy eszköz a meghibásodás előtt véve, és a címkézés őket gyűjtő idő (3Z, 2Z, Z) használatával a feliratok "szokásos" módon más rekordok címkézés közben (címke = 0). Ezzel a módszerrel a címkéje kategorikus változó (lásd a 5. ábra).

![5. ábra. Idő előrejelzés multiclass besorolási címkék](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

5. ábra. Idő előrejelzés multiclass besorolási címkék

A második kérdést a címkézés végezhető el az eszköz és a címkézés azokat a meghibásodás előtt véve X rekordok "hamarosan P probléma miatt sikertelenek<sub>i</sub>" (címke = P<sub>i</sub>) "szokásos" módon más rekordok címkézés közben (címke = 0). Ezzel a módszerrel a feliratai kategorikus változók (lásd a 6. ábrát).

![6. ábra. A legfelső szintű OK előrejelzés multiclass besorolás címkézés](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

6. ábra. A legfelső szintű OK előrejelzés multiclass besorolás címkézés

A modell hozzárendel egy hiba valószínűség miatt egyes P<sub>i</sub> és hibák nélkül valószínűségét. Ezeket a valószínűség nagyságrendet is rendezett engedélyezi a problémák, amelyek fordulhat elő, a jövőben előrejelzését. Repülőgép összetevő hibája használati eset multiclass besorolás hibát lett felépítve. Ez lehetővé teszi, hogy a hiba oka a következő hónap belül előforduló két különböző nyomás szelepösszetevők valószínűség előrejelzését.

Ajánló karbantartási műveletek hiba után, a címkézés nincs szükség egy jövőbeli horizon ki kell. Ennek az oka, hogy a modell a jövőben nem becslése hiba, de az imént becslése az a legvalószínűbb okának után a hiba már elvégzett. A harmadik esetet, amikor a cél előre megadott előzményadatokra vonatkozóan üzemi körülmények között a hiba oka foglalhatja ajtó hibák tartozik. Ez a modell majd segítségével előre jelezni az a legvalószínűbb okait után hiba történt. Ez a modell egy fő előnyt az jelenti, hogy könnyen problémák diagnosztizálásához és megoldásához, amelyek egyébként szükséges élmény év alatt érkezett a tapasztalatlan technikusok segít.

## <a name="training-validation-and-testing-methods-in-predictive-maintenance"></a>Képzési, érvényesítése és a vizsgálati prediktív karbantartás alatt áll
A prediktív karbantartás, egyéb megoldást terület időbélyegzővel adatokat tartalmazó hasonló a tipikus betanításra és tesztelésre rutin kell figyelembe venni a idő különböző szempontok jobban generalize nem látható jövőbeli adatokon.

### <a name="cross-validation"></a>Kereszt-ellenőrzési
Sok gépi tanulási algoritmusok hiperparamétereket, amely jelentősen módosíthatja modell teljesítmény számos függ. Ezek hiperparamétereket optimális értékét nem vannak számított automatikusan, a modell betanításakor, de az adatok tudósok kell megadni. Számos módon, hogy a rendszer hiperparamétereket helyes értékeket. A leggyakrabban használt egyik "k hajtott kereszt-ellenőrzési", amely felosztja a példák véletlenszerűen "-k" modellrészt lesz. Az egyes hiperparamétereket értékek tanulási algoritmus futtatási k alkalommal. Minden egyes ismétlés esetén az aktuális modellrészek szereplő példák érvényesítési készletként használt, betanítási készletként használt példák a többi. Példák és a metrikák a algoritmus szerelvények arra az esetre vonatkoznak érvényesítési példák keresztül. Ez a ciklus az egyes hyperparameter értékek végén azt a k-teljesítmény metrika értékek átlaga számítási, és kattintson hyperparameter értékek, amelyek a legjobb átlagos teljesítmény.

Ahogy korábban említettük, a prediktív karbantartási problémákat, adatok, amelyek az adatok több forrásból származó események idősor formájában van. Ezeket a rekordokat rendelhető szerinti címkézését a rekord vagy például idején. Ezért ha azt ossza fel a dataset véletlenszerűen képzési és érvényesítési beállítása, a példák későbbinek időben érvényesítési példák némelyike. Az eredmény becslése jövőbeli teljesítmény hyperparameter értékek előtt modell betanítása lett érkező adatok alapján. Ezek becsléseket túlságosan optimista, lehet, különösen akkor, ha az idősorozat nem álló, és azok viselkedését változnak az idők. Ennek eredményeképpen választott hyperparameter értékek optimális lehet.

Jobb úgy, hogy a helyes értékeket hiperparamétereket ossza fel a példák képzési és érvényesítési időfüggő módon állítsa be úgy, hogy az összes érvényesítési többek között az összes példák mint időben későbbi. Ezt követően hiperparamétereket értékének minden készlete esetében az algoritmus betanítása keresztül betanítási készlete, mérjük modell teljesítmény keresztül érvényesítési ugyanazokat és hyperparameter értékek, amelyek megjelenítik a legjobb teljesítmény érdekében válassza. Idősorozat adatok nem álló, miután a tanítási és érvényesítés ossza fel a jobb jövőbeli "modell teljesítményt az választhatják véletlenszerűen kereszt-ellenőrzési értékeket vezethet által választott hyperparameter értékek idővel fejlődésének.

A végső modell betanítása tanulási algoritmus keresztül teljes adatokat, és a legjobb hyperparameter értékek felosztása vagy a kereszt-ellenőrzési képzési és érvényesítés használatával hozza létre.

### <a name="testing-for-model-performance"></a>A modell teljesítmény tesztelése
A modell létrehozása után kell a jövőbeli teljesítményét, az új adatok becslése. A legegyszerűbb becslés a betanítási adatok keresztül lehet a modell teljesítményétől. De ez a becslés túlságosan optimista, mert a modell a teljesítmény becsült használt adatok van szabva. A jobb becsült hyperparameter értékek teljesítmény metrika számított az érvényesítési készleten, vagy egy átlagos teljesítmény metrika történik a kereszt-ellenőrzési lehet. De ugyanezen okból, mint korábban is hangsúlyoztuk, ezek becsléseket továbbra is túlságosan optimista. Több reális megközelítések modell teljesítményének méréséhez kell.

Egyik módja az adatok véletlenszerűen felosztása tanítási, érvényesítése és tesztelési beállítása. A képzés és érvényesítési beállítása segítségével értéket hiperparamétereket, és azokat a modell betanításához. A modell teljesítményétől a teszt készleten mérése történik.

Egy másik megfelelő prediktív karbantartási módja a példák felosztása tanítási, érvényesítése és tesztelési időfüggő módon úgy, hogy minden teszt többek között az összes képzési és az érvényesítés példák mint időben későbbi. A felosztás után modell és a teljesítmény mérési ugyanaz a fentebb leírt módon történik.

Idősorozat találhatók a helyhez kötött és könnyen előrejelzése mindkét megközelítés jövőbeli teljesítmény hasonló becsléseket hoz létre. De ha idősorozat nem helyhez kötött és/vagy nehezen becsülhető, a második megközelítés hozza létre első egy jövőbeli teljesítmény modell becslése.

### <a name="time-dependent-split"></a>Időfüggő felosztása
Ajánlott eljárásként ebben a szakaszban vesszük időfüggő vegyes megvalósításához részletes bemutatása. Azt az időfüggő kétirányú valószínűségét közötti betanítási és tesztkészleteket, azonban pontosan ismertetik az azonos logikai időfüggő felosztása tanítási és érvényesítési beállítása alkalmazni kell.

Tegyük fel például különböző érzékelők mérték időbélyegzővel események adatfolyam kell. Tanítási és tesztelési példák, valamint a címkék funkcióit több eseményeket tartalmazó korlátozta keresztül vannak definiálva.
Bináris osztályozási, például szolgáltatás mérnöki és modellezési módszereket fejezeteiben bővebben ismertetjük, a szolgáltatások a múltbeli események alapján hozzák létre, és a feliratok "X" a jövőben időegységekkel belül jövőbeni események alapján hozzák létre. Így például szalagcímkézési időkeretétől előre később majd az időtartam szolgáltatása. Az időfüggő valószínűségét időben, ahol azt, amelyek akár előzményadatok használatával bennünket hiperparamétereket modell betanításához azt ki egy olyan pontot. Jövőbeli címkék, amelyekre nem térnek a képzés küszöbérték a betanítási adatok kiszivárgásának elkerülésére címke példák X kell a legújabb időkeretet választjuk egység a képzés határidő előtt. 7. ábra minden teli kör jelöli, amelynek a szolgáltatások és a feliratok arra az esetre vonatkoznak a fent leírt módszer utolsó szolgáltatás adatkészlet egy sort. Az ábrán látható, amelyek miatt a rekordokat, amelyek kell képzési és egy tesztelési időfüggő vegyes megvalósításához X = 2 és W = 3:

![7. ábra. Ossza fel a bináris osztályozási időfüggő](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

7. ábra. Ossza fel a bináris osztályozási időfüggő

A zöld négyzetes határoz meg a képzés használható mértékegységét tartozó bejegyzéseket. Ahogy korábban, a végső funkció tábla minden egyes képzési példa generálja megnézi 3 időszakokat a szolgáltatás és a címkézési előtt a képzés nap lezárási 2 jövőbeli időszak lejárt. Nem állítható be, ha adott például 2 jövőbeli időszak bármely részét a képzés küszöbérték felett marad is, mivel feltételezzük, hogy nincs látható, a képzési küszöbérték felett marad a képzés példák használunk. A korlátozás miatt fekete példák mutatják be, amelyeket nem lehet felhasználni a tanítási adathalmazt végső címkézett DataSet rögzíti. Ezeket a rekordokat nem használható az adatok, vagy mert a képzés lezárási előtt kerül, és azok szalagcímkézési korlátozta részben függ a képzés időkeretet, amely nem lehet a helyzet, szeretnénk teljesen külön korlátozta a címkézés ellenőrzése modell betanítására, és tesztelésére címke információk adatszivárgás megelőzése érdekében.

Ez a módszer lehetővé teszi, hogy az átfedésben lévő az adatok között a modell betanítására és tesztelésére példák, amelyek megközelíti a képzés lezárási szolgáltatás létrehozásánál használható. Attól függően, hogy az adatok rendelkezésre állását egy még inkább súlyos elválasztási bármelyikével nem a TesztKészlet belüli W mértékegységét képzési világos példák is elvégezhető.

A munkahelyen észleltünk, hogy élettartama fennmaradó előrejelzéséhez használt regressziós modell több súlyosan érinti a kiszivárgásának probléma, és rendkívül overfitting véletlenszerű valószínűségét használatával vezet. Ehhez hasonlóan regressziós problémák, a felosztás kell lennie, levágási képzési előtt hibákkal rendelkező eszközök tartozó rekordokat kell használni a képzési és a küszöbérték használandó tesztelési beállítása után a hibákkal rendelkező eszközök.

Egy általános módszer esetén a modell betanítására és tesztelésére adatai azonos egy másik fontos ajánlott eljárás, eszköz-azonosító szerint valószínűségét, hogy nincs a rendszerben képzési használt eszközök használhatók tesztelési, mert a vizsgálati lényege, győződjön meg arról, hogy ha egy új eszköz használt t o ellenőrizze előrejelzéseket a, a modell reális eredményét mutatja.

### <a name="handling-imbalanced-data"></a>Imbalanced adatok kezelése
Besorolási problémákat Ha nincsenek további példákat a többi, mint egy osztály az adatokat, különállónak imbalanced. Ideális esetben szeretnénk elég képviselői minden osztály rendelkezik a betanítási adatok különböző osztályú megkülönböztetésére. Ha egy osztály 10 %-nál kisebb az adatokat, azt is tegyük fel például, hogy az adatok imbalanced és a underrepresented dataset kisebbségi osztály nevezzük. Drasztikusan sok esetben találtunk mások például használatával összevetve csak alkotó 0,001 % adatpontjainak Ha egy osztály súlyosan underrepresented imbalanced adatkészletek. Osztály egyensúlyhiány hibák belül hol áll általában ritka előfordulások élettartamát az eszközök a kisebbségi osztály példák alkotó csalások felderítéséhez, a hálózati behatolás és a prediktív karbantartási sok tartományokban probléma.

Osztály egyensúlyhiány esetén a legtöbb szabványos tanulási algoritmusok teljesítmény sérül, minimálisra csökkenthető a teljes Hibaarány irányulnak. Például az adatkészlet 99 % negatív osztály példák és 1 % pozitív osztály példák azt eléréséhez 99 % pontossága egyszerűen címkézés negatív összes példányát. Azonban ez misclassifies meg minden pozitív példák, így az algoritmus nem hasznos egy nagyon nagy a pontosság metrika azonban. Hagyományos értékelési metrikák általános pontossága a Hibaarány, például következésképpen imbalanced tanulási esetén nem elegendőek el. Más mutatókat, például a pontosság, visszaírási, F1 pontszámokat és módosított ROC görbék használt értékelések esetén imbalanced adatkészleteket, amelyek az értékelési-metrikák szakaszban tárgyalt költség.

Van azonban néhány módszereket, amelyek segítenek a orvoslása érdekében osztály egyensúlyhiány probléma. Két fő mintavételt technikák és költség-és nagybetűket tanulás.

#### <a name="sampling-methods"></a>Mintavételi
Módosítása a DataSet adatkészlet egyes mechanizmusok által ahhoz, hogy adjon meg egy elosztott terhelésű adatkészlet áll mintavételt imbalanced tanulási metódusokon. Habár számos különböző mintavételi technikák, véletlenszerű-e legtöbb nagyon egyszerű ők oversampling és a mintavételi.

Egyszerűen is említettük, véletlenszerű oversampling véletlenszerűen kijelölése kisebbségi osztályból, ezekben a példákban replikálása, és a tanítási adathalmazt hozzáadását. Ez a kisebbségi osztály a teljes példák száma, végül elosztása a különböző osztályokhoz példák száma. Egy veszélye oversampling, hogy egyes példák több példánya okozhat a osztályozó túl adott overfitting vezető válik.
Magas képzési pontossága eredményezne, de előfordulhat, hogy az adatok tesztelés nem látható a teljesítmény nagyon alacsony. Ezzel ellentétben a mintavételi véletlenszerű kijelölése véletlenszerűen többsége osztály, és ezek a példák eltávolítása a tanítási adathalmazt. Azonban példák eltávolítása a legtöbb osztály okozhat a teljesíti a legtöbb osztály kapcsolatos fontosabb fogalmakra osztályozó. Hibrid mintavételi ahol túl van-e mintavételezett kisebbségi osztály és a legtöbb osztály van a mintát egy időben egy másik életképes megközelítés. Nincsenek a sok más kifinomultabb mintavételi módszerek állnak rendelkezésre, és osztály egyensúlyhiány mintavételi módszerei egy állandó figyelmet és a hozzájárulások fogadása sok csatornák népszerű kutatási területre. Döntse el, hogy a leghatékonyabb azokra különböző módszer használata általában a kutatás és kísérlet az adatok tudósok marad, és nagymértékben függ a tulajdonságai. Ezenkívül fontos, győződjön meg arról, hogy csak a mintavételi alkalmazása a képzés állítva, de nem a vizsgálat beállítása.

#### <a name="cost-sensitive-learning"></a>Bizalmas tanulási költsége
A prediktív karbantartás hibák, amelyek a kisebbségi osztály az érdekesebb normál példák mint, és így célja a hibáiról algoritmus teljesítménye általában a fókusz. Ez általában a egyenlőtlen elvesztése vagy különböző osztályokhoz, amelynek helytelenül előrejelzésére negatív pozitív is költség elemek misclassifying aszimmetrikus költségeinek említett több, mint ez fordítva is igaz. A kívánt osztályozó tudják biztosítják a magas előrejelzés pontosságát keresztül kisebbségi osztály a legtöbb a(z) pontosságára súlyosan veszélyeztetése nélkül.

Számos módon ez érhető el. Egy magas téves besorolás kisebbségi osztály az rendelve, és megpróbálja minimálisra csökkenthető a teljes, a probléma az eltérő elveszti a hatékonyan kezelni. Néhány gépi tanulási algoritmusok használja ezt az elképzelést eredendően például SVMs (támogatási vektoros gépek) pozitív és negatív példák költségét ahol építhető képzési idő alatt. Ehhez hasonlóan kiemelési módszerek használják, és általában megjelenítése a megfelelő teljesítmény imbalanced adatok, például a súlyozott döntési esetén algoritmus.

## <a name="evaluation-metrics"></a>Értékelési-metrikák
A korábban említett osztály egyensúlyhiány gyenge teljesítményt okoz, algoritmusok általában a legtöbb osztály példák jobb a kisebbségi osztály esetek költség, a teljes téves besorolás hiba sokkal akkor javul, ha a legtöbb osztály helytelenül lett címkézve besorolása. Ez kis visszaírási díjszabás okoz, és egy nagyobb probléma kerül, ha üzleti téves riasztásokat költsége nagyon nagy. Pontosság a legnépszerűbb metrika egy osztályozó teljesítmény leíró használatos. Azonban a fentiekben leírtak szerint pontossága nem helyettesíti, és nem tükrözik egy osztályozó funkció valódi teljesítményét, mert nagyon érzékeny adatok terjesztéseket. Ehelyett más értékelési-metrikák segítségével mérheti fel imbalanced tanulási problémákat. Ezekben az esetekben pontosság, visszaírási és F1 pontszámok kell lennie a kezdeti mérni kívánt nézze meg a prediktív karbantartási modell teljesítmény kiértékelése során. A prediktív karbantartás visszaírási díjszabás jelöl, a sikertelen teszt készletében hány sikerült helyesen azonosítani a modell. A nagyobb visszaírási sebesség jelenti azt a modell sikeres elfogja a true hibák. Pontosság metrika téves riasztásokat, ha alacsonyabb pontosság díjszabás magasabb téves riasztásokat felel meg a aránya vonatkozik. F1 pontszám pontosság és a visszaírási díjszabás ajánlott érték 1 alatt, és legrosszabb 0 figyelembe veszi.

Ezenkívül bináris osztályozási, decile táblák és növekedési diagram olyan nagyon informatív teljesítmény kiértékelése során. Ezeket csak az a pozitív osztály (hibák) koncentrálhat, és adja meg az algoritmus teljesítmény összetettebb képe mint mi a ROC (fogadó működő Fullnosc) görbén csak egy rögzített működési pont megnézzük látható.
Decile táblák nyerhetők rendelési hibák számított a modell küszöb előtt döntse el, hogy a végső címkén az előre jelzett valószínűség szerint a vizsgálat példák. A rendezett minták majd deciles (azaz a 10 % minták a legnagyobb valószínűség és majd 20 %, 30 % és így tovább) vannak csoportosítva. Minden decile igaz pozitív arányát és annak véletlenszerű baseline (azaz 0,1, 0,2..) hányadosa számítástechnikai által egy megbecsülheti, hogyan algoritmus teljesítményét minden decile módosulnak. Növekedési decile értékek megrajzolásához úgy, hogy decile igaz pozitív sebessége és a véletlenszerű igaz pozitív arány pár összes deciles jól használható. Az első deciles általában az eredményeket a fókusz óta itt látható a legnagyobb növekedését. Első deciles reprezentatív "veszélyben", a prediktív karbantartási használatakor is látható.

## <a name="sample-solution-architecture"></a>A minta megoldás architektúrája
A prediktív karbantartási megoldás telepítése, amikor azt is egy teljes körű megoldás, amely biztosítja a folyamatos ciklust adatfeldolgozást, az adatok tárolási modell betanítási, szolgáltatás-létrehozás, előrejelzés és az eredmények együtt egy riasztást a képi megjelenítés például egy eszköz irányítópult figyelési mechanizmus létrehozásakor. Azt szeretnénk, későbbi insights biztosít a felhasználó folyamatos automatikus módon adatok folyamat. Az ilyen egy IoT-adatok adatcsatorna példa prediktív karbantartási architektúrája alábbi 8. ábrán látható. Az architektúra valós idejű telemetriai eseményközpontnak streamelési adatok tárolására szolgáló gyűjti. Ezek az adatok a valós idejű feldolgozási adatok, például a szolgáltatás-létrehozás a stream Analytics van okozhatnak. Eredmények jelennek meg az irányítópult és a szolgáltatások majd szolgálnak a prediktív modell webszolgáltatás hívására. Egy időben feldolgozott adatokat is előzmény-adatbázisban tárolják, és összefésüli a külső adatforrások, például a helyszíni adatok adatbázisok létrehozása a modellezési példák.
Ugyanazt az adatraktárak kötegelt pontozási példák és az eredmények újra használható prediktív jelentéseket készítünk az irányítópulton való tárolására használható.

![8. ábra. Példa megoldásarchitektúra prediktív karbantartás](./media/cortana-analytics-playbook-predictive-maintenance/example-solution-architecture-for-predictive-maintenance.png)

8. ábra. Példa megoldásarchitektúra prediktív karbantartás

További információ a összetevői a architektúra tekintse meg [Azure](https://azure.microsoft.com/) dokumentációját.

