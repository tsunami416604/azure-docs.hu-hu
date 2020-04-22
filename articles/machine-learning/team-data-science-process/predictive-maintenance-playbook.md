---
title: Azure AI útmutató a prediktív karbantartási megoldásokhoz - Csapatadat-elemzési folyamat
description: Az adatelemzés átfogó leírása, amely több vertikális iparágban is prediktív karbantartási megoldásokat működtet.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 301e2be0c8b971a0236de6a8b5c8bd5f278c3aee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686747"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI útmutató a prediktív karbantartási megoldásokhoz

## <a name="summary"></a>Összefoglalás

A prediktív karbantartás (**PdM**) a prediktív elemzések népszerű alkalmazása, amely számos iparágban segíti a vállalkozásokat a magas eszközkihasználás és a működési költségek megtakarításának elérésében. Ez az útmutató a [Microsoft Azure AI platformtechnológia](https://azure.microsoft.com/overview/ai-platform) használatával a PdM-megoldások sikeres fejlesztéséhez és üzembe helyezéséhez üzleti és elemzési irányelveket és ajánlott eljárásokat ismerteti.

Kezdetnek ez az útmutató bemutatja az iparág-specifikus üzleti forgatókönyvek és a folyamat minősítése ezeket a forgatókönyveket a PdM. Az adatkövetelmények és a PdM-megoldások létrehozásához használt modellezési technikák is rendelkezésre állnak. Az útmutató fő tartalma az adatelemzési folyamat – beleértve az adatok előkészítésének lépéseit, a szolgáltatástervezést, a modelllétrehozást és a modell-operationalizációt. Ezek nek a kulcsfontosságú fogalmaknak a kiegészítéseként ez az útmutató a PdM-alkalmazások fejlesztésének felgyorsításához szükséges megoldássablonokat sorolja fel. Az útmutató arra is rámutat, hogy hasznos képzési források az orvos, hogy többet tudjon meg a a mi mögött az adattudomány. 

### <a name="data-science-guide-overview-and-target-audience"></a>Adatelemzési útmutató áttekintése és célközönsége
Az útmutató első fele ismerteti a tipikus üzleti problémákat, a PdM megvalósításának előnyeit a problémák megoldásához, és felsorol néhány gyakori használati esetet. Az üzleti döntéshozók (BDM-ek) számára előnyös ez a tartalom. A második felében ismerteti az adattudomány mögött PdM, és egy listát a PdM-megoldások segítségével az ebben az útmutatóban vázolt elveket. Azt is előírja, tanulási utak és mutatók képzési anyag. A technikai döntéshozók (TDM-ek) hasznosnak fogják találni ezt a tartalmat.

| Kezdje ... | Ha ön ... |
|:---------------|:---------------|
| [A prediktív karbantartás üzleti esete](#business-case-for-predictive-maintenance) |üzleti döntéshozó (BDM), amely csökkenteni szeretné az állásidőt és a működési költségeket, és javítani szeretné a berendezések kihasználtságát |
| [Adattudomány a prediktív karbantartáshoz](#data-science-for-predictive-maintenance) |technikai döntéshozó (TDM), amely kiértékeli a PdM-technológiákat, hogy megértse a prediktív karbantartás egyedi adatfeldolgozási és AI-követelményeit |
| [Megoldássablonok prediktív karbantartáshoz](#solution-templates-for-predictive-maintenance)|szoftvertervező vagy AI-fejlesztő, aki gyorsan fel akar állni egy demóval vagy egy |
| [Képzési források a prediktív karbantartáshoz](#training-resources-for-predictive-maintenance) | a fentiek bármelyikét vagy mindegyikét, és szeretné megtanulni az adattudomány, az eszközök és a technikák mögött megalapozó fogalmakat.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó előfeltételek
A BDM-tartalom nem számít arra, hogy az olvasó rendelkezik előzetes adatelemzési ismeretekkel. A TDM-tartalom esetében a statisztika és az adattudomány alapvető ismerete hasznos. Az Azure Data és a AI-szolgáltatások, Python, R, XML és JSON ismerete ajánlott. Az AI-technikák Python- és R-csomagokban vannak megvalósítva. A megoldássablonok azure-szolgáltatások, fejlesztői eszközök és SDK-k használatával valósulnak meg.

## <a name="business-case-for-predictive-maintenance"></a>A prediktív karbantartás üzleti esete

A vállalkozásoknak szükségük van a kritikus berendezések rekedése csúcshatékonyságés hasznosítás megvalósítani a tőkebefektetések megtérülését. Ezek az eszközök a repülőgép-hajtóművektől, turbináktól, liftektől vagy ipari hűtőktől - amelyek milliókba kerülnek - egészen a mindennapi berendezésekig, mint a fénymásolók, kávéfőzők vagy vízhűtők.
- Alapértelmezés szerint a legtöbb vállalkozás _korrekciós karbantartásra_támaszkodik, ahol az alkatrészeket akkor és akkor cserélik le, amikor meghibásodnak. A korrekciós karbantartás biztosítja, hogy az alkatrészek teljes használata (ezért nem pazarolja az alkatrészek élettartamát), de az üzemidő, a munkaerő és a nem tervezett karbantartási követelmények (munkaidőn kívül vagy kényelmetlen helyeken) a vállalkozás költségeibe kerülnek.
- A következő szinten a vállalkozások _megelőző karbantartást_gyakorolnak, ahol meghatározzák egy alkatrész hasznos élettartamát, és fenntartják vagy kicserélik azt a hiba előtt. A megelőző karbantartás elkerüli a nem tervezett és katasztrofális hibákat. De a magas költségek a tervezett állásidő, alul-kihasználása az alkatrész során hasznos élettartama, és a munkaerő továbbra is fennáll.
- A _prediktív karbantartás_ célja a korrekciós és a megelőző karbantartás közötti egyensúly optimalizálása az alkatrészek _időbeli_ cseréjének lehetővé tétele mellett. Ez a megközelítés csak akkor helyettesíti ezeket az összetevőket, ha hiba közelében vannak. Az alkatrészek élettartamának meghosszabbításával (a megelőző karbantartáshoz képest) és a nem tervezett karbantartási és munkaerőköltségek csökkentésével (a korrekciós karbantartással szemben) a vállalkozások költségmegtakarítást és versenyelőnyöket érhetnek el.

## <a name="business-problems-in-pdm"></a>Üzleti problémák a PdM-ben
A vállalkozások magas működési kockázattal szembesülnek a váratlan hibák miatt, és korlátozott betekintést nyerhetnek az összetett rendszerek problémáinak kiváltó okaiba. Néhány kulcsfontosságú üzleti kérdés:

- Észlelheti a berendezések vagy a rendszer teljesítményében vagy működésében fellépő rendellenességeket.
- Tippelje meg, hogy egy eszköz meghibásodhat-e a közeljövőben.
- Egy eszköz fennmaradó hasznos élettartamának becslése.
- Azonosítsa egy eszköz meghibásodásának fő okait.
- Azonosítsa, hogy milyen karbantartási műveleteket kell elvégezni, mikor, egy eszközön.

A PdM tipikus céljelentései a következők:

- A kritikus fontosságú berendezések működési kockázatának csökkentése.
- Növelje az eszközök megtérülési rátáját a hibák bekövetkezéselőtti előrejelzésével.
- A karbantartás költségeinek szabályozása a just-in-time karbantartási műveletek engedélyezésével.
- Alacsonyabb ügyféllemorzsolódás, javítja a márka arculatát és az elveszett eladásokat.
- Csökkentse a készletköltségeket a készletszintek csökkentésével az újrarendelési pont előrejelzésével.
- Fedezze fel a különböző karbantartási problémákhoz kapcsolódó mintákat.
- KPI-k (fő teljesítménymutatók) biztosítása, például az eszközfeltételek állapotpontszámai.
- Az eszközök hátralévő élettartamának becslése.
- Javasoljuk, hogy időben karbantartási tevékenységek.
- Csak az időben történő készletlehetővé tétele az alkatrészek cseréjére vonatkozó rendelési dátumok becslésével.

Ezek a célutasítások a következők kiindulópontjai:

- _adatszakértőket_ konkrét prediktív problémák elemzésére és megoldására.
- _felhőalapú építészek és fejlesztők_ számára, hogy egy végponttól végpontig megoldást találjanak.

## <a name="qualifying-problems-for-predictive-maintenance"></a>A prediktív karbantartás minősítési problémái
Fontos hangsúlyozni, hogy nem minden használati esetek vagy üzleti problémák lehet hatékonyan megoldani PdM. A probléma kiválasztásakor három fontos minősítő kritériumot kell figyelembe venni:

- A problémának prediktív jellegűnek kell lennie; vagyis kell lennie egy célnak vagy egy eredménynek, amelyet meg kell jósolni. A problémának egyértelmű cselekvési úttal kell rendelkeznie a hibák megelőzésére, amikor észlelik őket.
- A problémának rendelkeznie kell a berendezés működési előzményeiről , amelyek _jó és rossz eredményeket is tartalmaznak_. A rossz eredmények enyhítésére tett intézkedéseknek is rendelkezésre kell állniuk e rekordok részeként. Hibajelentések, karbantartási naplók a teljesítmény romlása, javítás, és a csere naplók is fontos. Ezen túlmenően, javításokat végzett, hogy javítsa őket, és a csere nyilvántartások is hasznos.
- A feljegyzett előzményeknek tükröződniük kell a _vonatkozó_ adatokban, amelyek elegendő minőségűek _ahhoz,_ hogy alátámasztsák a használati esetet. Az adatok relevanciájáról és elégségesssedéséről a [prediktív karbantartásra vonatkozó adatok követelményei](#data-requirements-for-predictive-maintenance)című témakörben talál további információt.
- Végül, az üzleti kell domain szakértők, akik egyértelműen megértsék a problémát. Tisztában kell lenniük a belső folyamatokkal és gyakorlatokkal, hogy az elemző megérthesse és értelmezhetje az adatokat. Lehetővé kell tenni nekik továbbá, hogy szükség esetén elhajthassák a meglévő üzleti folyamatok szükséges módosításait, hogy szükség esetén segítsék a problémákra vonatkozó megfelelő adatok gyűjtését.

## <a name="sample-pdm-use-cases"></a>PdM-használati esetek mintája
Ez a szakasz a PdM használati esetek gyűjteményére összpontosít, amelyek több iparágból, például az aerovilágból, a közművekből és a szállításból származnak. Minden szakasz egy üzleti problémával kezdődik, és ismerteti a PdM előnyeit, az üzleti problémát övező releváns adatokat, és végül a PdM-megoldás előnyeit.

| Üzleti probléma | A PdM előnyei |
|:-----------------|-------------------|
|**Légi közlekedés**      |                   |
|_Járatkésés és járattörlések_ mechanikai problémák miatt. Azok a hibák, amelyek időben nem javíthatók, a járatok törlését, valamint az ütemezés és a műveletek megszakítását okozhatják. |A PdM-megoldások meg tudják jósolni annak a valószínűségét, hogy egy légi jármű mechanikai hibák miatt késik vagy törlődik.|
|_A repülőgép-hajtóművek alkatrészének meghibásodása_: A repülőgép-hajtómű alkatrészcserei a légiközlekedési ágazat leggyakoribb karbantartási feladatai közé tartoznak. A karbantartási megoldások gondosan megkövetelik az alkatrészek készleteinek rendelkezésre állását, szállítását és tervezését|Az alkatrészek megbízhatóságára vonatkozó információk összegyűjtése a beruházási költségek jelentős csökkenéséhez vezet.|
|**Pénzügy** |                         |
|_Az ATM-hiba_ gyakori probléma a bankszektoron belül. A probléma itt az, hogy számoljon be annak a valószínűsége, hogy egy ATM készpénzfelvételi tranzakció megszakad miatt papír elakadás vagy részben nem a készpénzadagoló. A tranzakciós hibák előrejelzései alapján az ATM-ek proaktív módon kiszolgálhatók a hibák előfordulásának megakadályozása érdekében.| Ahelyett, hogy lehetővé tenné a gép sikertelen félúton egy tranzakció, a kívánatos alternatíva az, hogy a program a gép megtagadja szolgáltatás alapján az előrejelzés.|
|**Energia** |                          |
|_Szélturbina meghibásodások_: Szélturbinák a fő energiaforrás a környezettudatos országokban / régiókban, és jár a magas tőkeköltségek. A szélturbinák egyik legfontosabb összetevője a generátormotor, amelynek meghibásodása hatástalanná teszi a turbinát. Az is nagyon drága megjavítani.|Az olyan KPI-k előrejelzése, mint az MTTF (a meghibásodásig töltött átlagos idő) segíthet az energiavállalatoknak megelőzni a turbinameghibásodásokat, és minimális állásidőt biztosíthat. Hiba valószínűsége tájékoztatja a technikusok, hogy figyelemmel kíséri turbinák, amelyek valószínűleg nem hamarosan, és a menetrend idő-alapú karbantartási rendszerek. A prediktív modellek betekintést nyújtanak a különböző tényezőkbe, amelyek hozzájárulnak a kudarchoz, ami segít a technikusoknak jobban megérteni a problémák kiváltó okait.|
|_Megszakító hibák_: A villamos energia elosztása az otthonok és a vállalkozások megköveteli, hogy a távvezetékek mindenkor működőképesek legyenek az energiaszállítás biztosítása érdekében. Az áramkör-megszakítók segítenek korlátozni vagy elkerülni az elektromos vezetékek károsodását túlterhelés vagy kedvezőtlen időjárási körülmények között. Az üzleti probléma itt az, hogy megjósolni megszakító hibák.| A PdM megoldások segítenek csökkenteni a javítási költségeket és növelni a berendezések, például az áramkör-megszakítók élettartamát. Segítenek az elektromos hálózat minőségének javításában a váratlan hibák és a szolgáltatás megszakításainak csökkentésével.|
|**Szállítás és logisztika** |    |
|_Liftajtó meghibásodások:_ A nagy felvonótársaságok teljes körű szolgáltatást nyújtanak több millió funkcionális felvonó számára világszerte. A felvonóbiztonság, a megbízhatóság és az állásidő a fő szempont ügyfeleik számára. Ezek a vállalatok nyomon követik ezeket és számos más attribútumot az érzékelőkön keresztül, hogy segítsék őket a korrekciós és megelőző karbantartásban. A liftben a legkiemelkedőbb ügyfélprobléma a liftajtók hibás működése. Az üzleti probléma ebben az esetben az, hogy egy tudásbázis prediktív alkalmazás, amely előre jelzi az ajtó hibák lehetséges okait.| A felvonók tőkebefektetések potenciálisan 20-30 éves élettartamra. Tehát minden potenciális értékesítés rendkívül versenyképes lehet; ezért a szolgáltatással és a támogatással kapcsolatos elvárások magasak. A prediktív karbantartás előnyt biztosíthat ezeknek a vállalatoknak a versenytársaikkal szemben a termék- és szolgáltatáskínálatukban.|
|_Kerékhibák_: A kerékmeghibásodások teszik ki a vonatkisiklások felét, és milliárdokba kerülnek a globális vasúti ágazat számára. Kerék meghibásodások is okoz sínek romlik, néha okoz a vasúti törés idő előtt. A vasúti szünetek katasztrofális eseményekhez, például kisiklásokhoz vezetnek. Az ilyen esetek elkerülése érdekében a vasutak figyelemmel kísérik a kerekek teljesítményét, és megelőző módon helyettesítik azokat. Az üzleti probléma itt a kerék hibák előrejelzése.| A kerekek prediktív karbantartása segít a kerekek éppen időben való cseréjében |
|_Metró vonat ajtó hibák_: A fő oka a késések metró műveletek ajtó hibák a vonat autók. Az üzleti probléma itt az, hogy megjósolni vonat ajtó hibák.|Az ajtómeghibásodás korai felismerése, vagy az ajtómeghibásodásig eltelt napok száma segít az üzletnek a vonatajtó szervizelési menetrendjének optimalizálásában.|

A következő rész bekerül a részleteket, hogyan lehet megvalósítani a PdM előnyeit fent tárgyalt.

## <a name="data-science-for-predictive-maintenance"></a>Adattudomány a prediktív karbantartáshoz

Ez a szakasz általános útmutatókat tartalmaz az adatelemzési elvek és a PdM gyakorlat. Célja, hogy segítsen a TDM, megoldás tervező, vagy a fejlesztő megérteni az előfeltételeket és a folyamat létrehozásának end-to-end AI alkalmazások PdM. Ezt a szakaszt a [prediktív karbantartáshoz készült megoldássablonokban](#solution-templates-for-predictive-maintenance)felsorolt bemutatók és próbasablonok áttekintésével együtt olvashatja el. Ezután használhatja ezeket az alapelveket és ajánlott eljárásokat a PdM-megoldás azure-beli megvalósításához.

> [!NOTE]
> Ez az útmutató nem célja, hogy tanítsa az olvasó adattudomány. A [prediktív karbantartásra](#training-resources-for-predictive-maintenance)vonatkozó képzési forrásokról szóló szakaszban számos hasznos forrás áll rendelkezésre. Az útmutatóban felsorolt [megoldássablonok](#solution-templates-for-predictive-maintenance) bemutatják az egyes PdM-problémákra vonatkozó AI-technikák némelyikét.

## <a name="data-requirements-for-predictive-maintenance"></a>A prediktív karbantartásra vonatkozó adatkövetelmények

Bármely tanulás sikere attól függ, hogy (a) milyen minőségű, amit tanítanak, és (b) a tanuló képessége. A prediktív modellek a korábbi adatokból tanulnak mintákat, és a megfigyelt minták alapján bizonyos valószínűséggel előre jelzik a jövőbeli eredményeket. A modell prediktív pontossága a betanítási és tesztelési adatok relevanciájától, elégségességététől, valamint minőségétől függ. Az új adatok, amelyek "pontozott" ezzel a modellel rendelkeznie kell a funkciók és a séma, mint a betanítási/tesztelési adatokat. Az új adatok jellemzőjellemzőinek (típusa, sűrűsége, eloszlása és így tovább) meg kell egyezniük a betanítási és tesztelési adatkészletek jellemzőivel. A hangsúly ebben a szakaszban az ilyen adatkövetelmények.

### <a name="relevant-data"></a>Releváns adatok

Először is, az adatoknak relevánsnak kell lenniük _a probléma szempontjából._ Tekintsük a _kerék meghibásodása_ esetén a fent tárgyalt - a képzési adatok tartalmaznia kell kapcsolatos funkciók a kerék műveleteket. Ha a probléma az volt, hogy megjósolni a hiba a _vontatási rendszer_, a képzési adatok nak ki kell terjednie az összes különböző alkatrészek a vontatási rendszer. Az első eset egy adott összetevőt céloz meg, míg a második esetben egy nagyobb alrendszer meghibásodását. Az általános ajánlás az, hogy a nagyobb alrendszerek helyett az egyes összetevőkre vonatkozó előrejelzési rendszereket tervezzenek, mivel az utóbbiak több szétszórt adatból fognak rendelkezni. A tartományszakértőnek (lásd: [A prediktív karbantartás ra vonatkozó problémák minősítése)](#qualifying-problems-for-predictive-maintenance)segítenie kell az elemzéshez tartozó adatok legrelevánsabb részhalmazainak kiválasztásában. A vonatkozó adatforrásokat részletesebben tárgyaljuk a [prediktív karbantartásra való felkészülés](#data-preparation-for-predictive-maintenance)ben .

### <a name="sufficient-data"></a>Elegendő adat
A hibaelőzmények adataival kapcsolatban gyakran két kérdést tesznek fel: (1) "Hány hibaesemény szükséges egy modell betanításához?" (2) "Hány rekordot kell "elégnek" tekinteni?" Nincsenek végleges válaszok, de csak a hüvelykujj szabályai. Az (1) esetében a hibaesemények száma nagyobb, jobb a modell. A (2) és a hibaesemények pontos száma az adatoktól és a megoldandó probléma kontextusátó környezettől függ. De a másik oldalon, ha egy gép nem túl gyakran, akkor az üzleti fogja helyettesíteni, ami csökkenti a hiba példányok. Itt is fontos a domain szakértő útmutatása. Vannak azonban olyan módszerek, hogy megbirkózzanak a _ritka események_kérdésével . Ezeket a [Kiegyensúlyozatlan adatok kezelése](#handling-imbalanced-data)című szakaszban tárgyaljuk.

### <a name="quality-data"></a>Minőségi adatok
Az adatok minősége kritikus – minden előrejelző attribútumértéknek _pontosnak_ kell lennie a célváltozó értékével együtt. Az adatminőség jól tanulmányozott terület a statisztikák és az adatkezelés terén, és így nem tartozik az útmutató hatálya alá.

> [!NOTE]
> A minőségi adatok biztosításához számos erőforrás és vállalati termék áll rendelkezésre. A hivatkozásminta az alábbiakban található:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning ( Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning) (Feltáró adatbányászat és adattisztítás), Wiley, 2003.
> - [Feltáró adatok elemzése, Wikipédia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Mennyiségi adatok tisztítása nagy adatbázisok](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Bevezetés az adatok tisztítása r](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Adatok előkészítése prediktív karbantartáshoz

### <a name="data-sources"></a>Adatforrások

A prediktív karbantartás releváns adatforrásai többek között a következők:
- Hiba előzmények
- Karbantartási/javítási előzmények
- A gép működési feltételei
- A berendezés metaadatai

#### <a name="failure-history"></a>Hiba előzmények
A hibaesemények ritkák a PdM-alkalmazásokban. Azonban előrejelzési modellek létrehozásakor az algoritmusnak meg kell tanulnia egy összetevő normál működési mintáját, valamint a hibamintákat. Így a betanítási adatoknak elegendő számú példát kell tartalmazniuk mindkét kategóriából. Karbantartási nyilvántartások és alkatrész csere előzmények jó forrásai a hiba események megkereséséhez. Néhány tartományismeretek segítségével a betanítási adatok anomáliák is definiálhatók hibákként.

#### <a name="maintenancerepair-history"></a>Karbantartási/javítási előzmények
Egy eszköz karbantartási előzményei a kicserélt alkatrészekre, az elvégzett javítási tevékenységekre stb. Ezek az események rögzítik a lebomlási mintákat. E fontos információk hiánya a betanítási adatokban félrevezető modelleredményekhez vezethet. A hibaelőzmények a karbantartási előzményekben speciális hibakódként vagy az alkatrészek rendelési dátumaiként is megtalálhatók. A hibamintákat befolyásoló további adatforrásokat a tartományszakértőknek kell megvizsgálniuk és biztosítaniuk.

#### <a name="machine-operating-conditions"></a>A gép működési feltételei
A működés alatt lévő berendezés érzékelőalapú (vagy egyéb) adattovábbítási adatai fontos adatforrást szolgálnak. A PdM egyik legfontosabb feltételezése, hogy a gép állapotának csökkenése a rutinművelet során az idő múlásával romlik. Az adatok várhatóan időváltozó funkciókat tartalmaznak, amelyek rögzítik ezt az elévítési mintát, és minden olyan anomáliát, amely leépüléshez vezet. Az adatok időbeli aspektusa szükséges az algoritmus, hogy megtanulják a hiba és a nem sikertelen minták idővel. Ezen adatpontok alapján az algoritmus megtanulja megjósolni, hogy egy gép hány egységnyi idővel folytathatja a munkát, mielőtt meghibásodna.

#### <a name="static-feature-data"></a>Statikus szolgáltatásadatok
A statikus szolgáltatások a berendezés metaadatai. Ilyenek például a berendezés gyártmánya, típusa, gyártása, a szolgáltatás kezdő dátuma, a rendszer elhelyezkedése és egyéb műszaki előírások.

Példák a [pdm-használati esetek mintájára](#sample-pdm-use-cases) vonatkozó adatokra az alábbiakban táblázatos formában:

| Kis- és nagybetű használata | Példák a vonatkozó adatokra |
|:---------|---------------------------|
|_Járatkésés és járattörlések_ | Repülési útvonalra vonatkozó információk repülési lábak és oldalnaplók formájában. A repülési szakasz adatai tartalmazzák az útvonal részleteit, például az indulás/érkezés dátumát, időpontját, repülőterét, átálltatásait stb. Az oldalnapló a földi karbantartó személyzet által rögzített hiba- és karbantartási kódok sorozatát tartalmazza.|
|_A repülőgép motoralkatrészei meghibásodottak_ | A légi jármű érzékelőitől gyűjtött adatok, amelyek információt szolgáltatnak a különböző részek állapotáról. A karbantartási rekordok segítenek azonosítani, hogy mikor történtek az összetevők hibái, és mikor cserélték ki őket.|
|_ATM-hiba_ | Érzékelő leolvasások minden egyes tranzakció (letétbe készpénz / csekk) és a készpénz kiadása. Információk a megjegyzések közötti résmérésről, a jegyzet vastagságáról, a jegyzet érkezési távolságáról, az attribútumok ellenőrzéséről stb. Karbantartási feljegyzések, amelyek hibakódokat, javítási információkat tartalmaznak, amikor utoljára újratöltötték a pénzkiadót.|
|_Szélturbina meghibásodása_ | Az érzékelők figyelik a turbina feltételeit, például a hőmérsékletet, a szélirányt, a termelt energiát, a generátor sebességét stb. Az adatokat több szélturbinából gyűjtik a különböző régiókban található szélerőműparkokból. Általában minden turbina lesz több érzékelő mért átmosó mérések egy meghatározott időintervallumban.|
|_Megszakító hibák_ | Karbantartási naplók, amelyek korrekciós, megelőző és rendszeres műveleteket tartalmaznak. Működési adatok, amelyek tartalmazzák az áramkör-megszakítóknak küldött automatikus és manuális parancsokat, például a nyitott és záró műveleteket. Eszköz metaadatai, például a gyártás időpontja, helye, modellje stb. Megszakító specifikációk, mint a feszültségszintek, a földrajzi helymeghatározás, a környezeti feltételek.|
|_A liftajtó meghibásodása_| A lift metaadatai, például a felvonó típusa, a gyártott dátum, a karbantartási gyakoriság, az épület típusa és így tovább. Működési információk, mint például az ajtóciklusok száma, az átlagos ajtózárási idő. Hibaelőzmények okokkal.|
|_Kerékhibák_ | Érzékelő adatok, amelyek mérik a kerékgyorsulást, a fékezési eseteket, a vezetési távolságot, a sebességet stb. Statikus információ a kerekek, mint a gyártó, gyártott dátum. A rendelési dátumokat és mennyiségeket nyomon követő részrendelési adatbázisból származó hibaadatok.|
|_Metró vonat ajtó hibák_ | Ajtónyitási és -zárási idők, egyéb működési adatok, mint például a vonatajtók aktuális állapota. A statikus adatok közé tartoznak az eszközazonosító, az idő és a feltételérték oszlopai.|

### <a name="data-types"></a>Adattípusok
A fenti adatforrások alapján a PdM-tartományban megfigyelt két fő adattípus a következő:

- _Időbeli adatok:_ működési telemetriai adatok, gépfeltételek, munkarendelés-típusok, prioritáskódok, amelyek időbélyegekkel rendelkeznek a rögzítés időpontjában. A hiba, a karbantartási/javítási és a használati előzmények is időbélyegeket társítanak az egyes eseményekhez.
- _Statikus adatok_: A gép és a kezelői funkciók általában statikusak, mivel a gépek vagy a kezelői jellemzők műszaki előírásait írják le. Ha ezek a funkciók idővel változhatnak, időbélyegekkel is rendelkezniük kell.

A prediktor- és célváltozókat a használt algoritmustól függően [előkell dolgozni/numerikus, kategorikus és más adattípusokká](https://www.statsdirect.com/help/basics/measurement_scales.htm) kell alakítani.

### <a name="data-preprocessing"></a>Adatok előfeldolgozása
A _szolgáltatástervezés_előfeltételeként készítse elő a különböző adatfolyamokból származó adatokat egy séma összeállításához, amelyből könnyen hozhat létre funkciókat. Az adatokat először rekordtáblaként jelenítse meg. A tábla minden sora egy betanítási példányt jelöl, az oszlopok pedig _előrejelző_ funkciókat (más néven független attribútumokat vagy változókat). Úgy rendezheti az adatokat, hogy az utolsó oszlop(ok) a _cél_ (függő változó). Minden egyes betanítási példányhoz rendeljen hozzá egy _címkét_ az oszlop értékeként.

Időbeli adatok esetén ossza fel az érzékelő adatok időtartamát időegységekre. Minden rekordnak egy eszköz időegységéhez kell tartoznia, _és különböző információkat kell nyújtania._ Az időegységek meghatározása az üzleti igények alapján történik másodpercek, percek, órák, napok, hónapok és így tovább. Az _időegységnek nem kell megegyeznie az adatgyűjtés gyakoriságával_. Ha a frekvencia magas, előfordulhat, hogy az adatok nem mutatnak jelentős különbséget az egyik egység között. Tegyük fel például, hogy a környezeti hőmérsékletet 10 másodpercenként gyűjtötték össze. Ha ugyanazt az intervallumot használja a betanítási adatokhoz, az csak növeli a példák számát anélkül, hogy további információkat nyújtana. Ebben az esetben egy jobb stratégia az lenne, hogy az adatok átlagát 10 perc alatt, vagy egy óra alatt az üzleti indoklás alapján.

Statikus adatok esetén
- _Karbantartási rekordok:_ A nyers karbantartási adatok rendelkeznek egy eszközazonosítóval és időbélyeggel, amely tartalmazza az adott időpontban végrehajtott karbantartási tevékenységekadatait. A karbantartási tevékenységeket _kategorikus_ oszlopokká alakíthatja, ahol minden kategórialeíró egyedileg egy adott karbantartási műveletre van leképezve. A karbantartási rekordok sémája magában foglalja az eszközazonosítót, az időt és a karbantartási műveletet.

- _Hibarekordok_: A hibák vagy hibák okai meghatározott hibakódként vagy adott üzleti feltételek által meghatározott hibaeseményként rögzíthetők. Azokban az esetekben, amikor a berendezés több hibakód, a domain szakértő segít azonosítani azokat, amelyek relevánsak a cél változó. A fennmaradó hibakódok vagy feltételek segítségével _olyan előrejelző_ funkciókat hozhat létre, amelyek korrelálnak ezekkel a hibákkal. A hibarekordok sémája magában foglalja az eszközazonosítót, az időt, a hibát vagy a hiba okát – ha rendelkezésre áll.

- _Gép- és operátori metaadatok:_ Egyesítse a gép és az operátor adatait egyetlen sémába, hogy egy eszközt társítson az üzemeltetőjéhez, valamint azok attribútumait. A gépi feltételek sémája magában foglalja az eszközazonosítót, az eszközfunkciókat, az operátor-azonosítót és az operátori funkciókat.

Az adatok előzetes feldolgozási lépései közé tartozik _a hiányzó értékek kezelése_ és az attribútumértékek _normalizálása._ A részletes vita túlmutat ezen útmutató hatályán - lásd a következő szakaszban néhány hasznos hivatkozást.

A fenti előre feldolgozott adatforrások a helyén, a végső átalakítás előtt szolgáltatás mérnöki, hogy csatlakozzon a fenti táblák alapján az eszköz azonosítóját és időbélyeg. Az eredményül kapott tábla null értékekkel rendelkezik a hibaoszlophoz, amikor a gép normál üzemmódban van. Ezeket a null értékeket a normál működés jelzője imputálhatja. Ezzel a hibaoszloppal címkéket hozhat létre _a prediktív modellhez._ További információt a [prediktív karbantartás modellezési technikáiról szóló szakaszban talál.](#modeling-techniques-for-predictive-maintenance)

## <a name="feature-engineering"></a>Jellemzőkiemelés
A szolgáltatástervezés az adatok modellezése előtti első lépés. Az adatelemzési folyamatban betöltött szerepét [itt ismerteti](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkció_ a modell prediktív attribútuma – például hőmérséklet, nyomás, rezgés és így tovább. A PdM, szolgáltatás mérnöki magában foglalja a gép állapotának absztrakt egy korábbi adatok at egy jókora időtartamú. Ebben az értelemben különbözik a társaik, például a távoli figyelés, anomália észlelése és a hiba észlelése. 

### <a name="time-windows"></a>Időablakok
A távfelügyelet magában foglalja az _időponttal_bekövetkező események bejelentését . Anomáliadetektálási modellek kiértékeli (pontszám) bejövő adatfolyamok a nomádok, mint a pontok időben. A hibaészlelés a hibákat meghatározott típusúnak minősíti, mivel azok időbeli időpontban fordulnak elő. Ezzel szemben a PdM a hibák előrejelzését jelenti egy _későbbi időszakban_, a számítógép _korábbi időszakon_keresztüli viselkedését reprezentáló funkciók alapján. A PdM esetében az egyes időpontokból származó szolgáltatásadatok túl zajosak ahhoz, hogy prediktívek legyenek. Így az egyes szolgáltatások adatait az adatpontok időablakok szerinti összesítésével kell _kiegyenlíteni._

### <a name="lag-features"></a>Lag funkciók
Az üzleti követelmények határozzák meg, hogy a modellnek milyen messzire kell előre jeleznie a jövőben. Ez az időtartam viszont segít meghatározni, hogy "milyen messze kell a modellnek kinéznie", hogy ezeket az előrejelzéseket eltudja készíteni. Ezt a "visszatekintési " időszakot _késésnek_nevezik , és az ebben a késési időszakban tervezett funkciókat _lag funkcióknak_nevezik . Ez a szakasz ismerteti a késés funkciók, amelyek időbélyegekkel és a szolgáltatás létrehozása statikus adatforrásokból lehet létrehozni. Lag funkciók jellemzően _numerikus_ jellegű.

> [!IMPORTANT]
> Az ablak mérete kísérletezéssel történik, és egy tartományszakértő segítségével kell véglegesíteni. Ugyanez a kikötés vonatkozik a késésjellemzőinek kiválasztására és meghatározására, azok összesítésére és az ablakok típusára.

#### <a name="rolling-aggregates"></a>Gördülő aggregátumok
Egy eszköz minden egyes rekordja esetében egy "W" méretű gördülő ablak ot választ az összesítések kiszámításához szükséges időegységek számaként. A késésfunkciók at ezután a rekord _dátuma előtti_ W-időszakok alapján számítják ki. Az 1. A W=3 méretű ablak jellemzőértékeinek gördülő átlagát jelölik. A gördülő átlagot a t<sub>1</sub> (narancssárga) és t<sub>2</sub> (zöld) időbélyeggel rendelkező összes rekordra számítja a számítás. A W értéke az adatok jellegétől függően általában percben vagy órában van. De bizonyos problémák, szedés egy nagy W (mondjuk 12 hónap) nyújthat az egész történetét egy eszköz, amíg az idő a rekord.

![1. ábra Működés közbeni összesítő funkciók](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

1. ábra Működés közbeni összesítő funkciók

Az időablakon keresztüli gördülő összesítések például a számlálás, az átlag, a CUMESUM (kumulatív összeg) mérték, a min/max értékek. Ezenkívül gyakran használnak varianciát, szórást és az N szóráson túli kiugró értékek számát. Az ebben az útmutatóban szereplő [használati esetekre](#sample-pdm-use-cases) alkalmazható aggregátumok az alábbiakban találhatók. 
- _Járatkésés:_ hibakódok száma az elmúlt nap/hét során.
- _A repülőgép motorjának meghibásodása:_ a gördülő érték, a szórás és az összeg az elmúlt nap, hét stb. Ezt a metrikát az üzleti tartomány szakértőjével együtt kell meghatározni.
- _ATM-hibák:_ gördülő eszközök, medián, tartomány, szórások, három szóráson túli kiugró értékek száma, felső és alsó CUMESUM.
- _Metró vonat ajtó hibák_: Események száma az elmúlt nap, hét, két hét stb.
- _Megszakító hibák_: Hiba számít az elmúlt héten, év, három év, stb

A PdM egy másik hasznos technikája a trendváltozások, a csúcsok és a szintváltozások rögzítése olyan algoritmusokkal, amelyek észlelik az adatok anomáliáit.

#### <a name="tumbling-aggregates"></a>Bukdácsoló aggregátumok
Egy eszköz minden címkézett rekordjához _w-<sub>k</sub> _ méretű ablak ot határoz nak meg, ahol _k_ a _W_méretű ablakok száma. Az aggregátumok ezután _a W-k, W-<sub>(k-1),</sub>..., W-2 ,<sub>2</sub><sub>W-1</sub> _ _katogó ablakokon_ _keresztül_ jönnek létre a rekord időbélyegét megelőző időszakokra. _k_ lehet egy kis szám, hogy rögzítse a rövid távú hatások, vagy nagy számban, hogy rögzítse a hosszú távú bomlási mintákat. (lásd a 2. ábrát).

![2. ábra Bukdácsoló összesített funkciók](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

2. ábra Bukdácsoló összesített funkciók

A w=1 és a k=3 segítségével például a szélturbinák használati esetének késleltetési jellemzői hozhatók létre. Ezek azt jelentik, a lag minden az elmúlt három hónapban a felső és alsó kiugró.

### <a name="static-features"></a>Statikus jellemzők

A berendezések műszaki specifikációi, mint például a gyártás időpontja, a modellszám, a helyszín, néhány példa a statikus jellemzőkre. Ezeket _a modellezéshez kategorikus_ változóként kezeli katarol. Néhány példa a megszakító használati esetére: feszültség, áram, teljesítménykapacitás, transzformátor típusa és áramforrás. A kerék meghibásodása esetén a gumiabroncs keréktárcsák típusa (ötvözet vs acél) egy példa.

Az eddig tárgyalt adat-előkészítési erőfeszítéseknek az adatok az alábbiak szerint történő szervezéséhez kell vezetniük. A betanítási, tesztelési és érvényesítési adatoknak rendelkezniük kell ezzel a logikai sémával (ebben a példában az idő napok egyegységben jelenik meg).

| Eszköz azonosítója | Time | \<Jellemzőoszlopok> | Címke |
| ---- | ---- | --- | --- |
| A123 |1. nap | . . . | . |
| A123 |2. nap | . . . | . |
| ...  |...   | . . . | . |
| B234 |1. nap | . . . | . |
| B234 |2. nap | . . . | . |
| ...  |...   | . . . | . |

A szolgáltatástervezés utolsó lépése a célváltozó **címkézése.** Ez a folyamat a modellezési technikától függ. A modellezési technika viszont az üzleti problémától és a rendelkezésre álló adatok jellegétől függ. A címkézést a következő szakasz tárgyalja.

> [!IMPORTANT]
> Az adatok előkészítése és a szolgáltatástervezés ugyanolyan fontosak, mint a modellezési technikák a sikeres PdM-megoldások hoz. A domain szakértő és az orvos kell befektetni jelentős időt érkezik a megfelelő funkciók és adatok a modell. Egy kis minta sok könyvet a funkció mérnöki az alábbiakban felsorolt:
> - Pyle, D. Adatbányászati adatok előkészítése (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists (Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists( O'Reilly, 2018).
> - Dong, G. Liu, H. (Szerkesztők), Gépi tanulás és adatelemzés funkciómérnöki szolgáltatása (Chapman & Hall/CRC Adatbányászati és Tudásfelfedezés sorozat), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modellezési technikák a prediktív karbantartáshoz

Ez a szakasz ismerteti a pdm problémák fő modellezési technikáit, valamint azok egyedi címkeépítési módszereit. Figyelje meg, hogy egyetlen modellezési technika használható a különböző iparágakban. A modellezési technika az adatelemzési problémával van párosítva, nem pedig az aktuális adatok kontextusával.

> [!IMPORTANT]
> A hibaesetek címkéinek kiválasztása és a címkézési stratégia  
> a szakterület-szakértővel konzultálva kell meghatározni.

### <a name="binary-classification"></a>Bináris osztályozás
A bináris osztályozás annak _a valószínűségét jelzi, hogy egy berendezés egy későbbi időszakon belül meghibásodik_ - az úgynevezett _jövőbeli horizontidőszak X_. Az X-et az üzleti probléma és a kéznél lévő adatok határozzák meg, a domain szakértővel konzultálva. Példák:
- _minimális átfutási idő_ szükséges az összetevők cseréjéhez, karbantartási erőforrások üzembe helyezéséhez, karbantartás elvégzéséhez, hogy elkerülje az adott időszakban valószínűleg előforduló problémát.
- a probléma _bekövetkezése_ előtt bekövetkező események minimális száma.

Ebben a technikában kétféle képzési példát azonosítanak. Egy pozitív példa, _amely hibát jelez_, címkével = 1. Negatív példa, amely a normál műveleteket jelzi, a címke = 0. A célváltozó, és így a címkeértékek _kategorikusak._ A modellnek minden új példát úgy kell azonosítania, mint amely valószínűleg sikertelen vagy normálisan működik a következő X időegységekben.

#### <a name="label-construction-for-binary-classification"></a>Címkeszerkezet bináris osztályozáshoz
A kérdés itt az, hogy "Mi a valószínűsége annak, hogy az eszköz nem fog sikerülni a következő X időegységben?" A kérdés megválaszolásához az X rekordokat az eszköz meghibásodása előtt "sikertelen" címkével (címke = 1) címkézze fel, és az összes többi rekordot "normálisnak" címkézze fel (címke =0). (lásd a 3. ábrát).

![3. ábra Címkézés bináris osztályozáshoz](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

3. ábra Címkézés bináris osztályozáshoz

Egyes használati esetek címkézési stratégiáját az alábbiakban soroljuk fel.
- _Járatkésések_: X lehet választani, mint egy nap, hogy megjósolni késések a következő 24 órában. Ezután minden olyan járat, amely a meghibásodások előtt 24 órán belül van megjelölve 1-ként.
- _ATM készpénzadagolási hibák_: A cél lehet meghatározni a tranzakció sikertelensvalószínűségét a következő egy órában. Ebben az esetben a hiba előző órájában végrehajtott összes tranzakció 1-ként van címkézve. A hiba valószínűségének előrejelzéséhez a következő N bankjegyekkel kapcsolatos hibák esetén a hiba utolsó N-jegyzetein belül kiosztott bankjegyek 1-ként vannak megjelölve.
- _Megszakító hibák_: A cél lehet megjósolni a következő megszakító parancs hiba. Ebben az esetben X-et választják ki egy jövőbeli parancsnak.
- _A vonat ajtóhibái_: Az X két napként választható.
- _Szélturbina meghibásodások_: X lehet választani, mint két hónap.

### <a name="regression-for-predictive-maintenance"></a>Regresszió a prediktív karbantartáshoz
A regressziós modellek _egy eszköz hátralévő hasznos élettartamának (RUL) kiszámítására szolgálnak._ RUL definíció szerint az az idő, amíg egy eszköz működik, mielőtt a következő hiba bekövetkezik. Minden betanítási példa egy rekord, amely egy eszköz _nY_ időegységéhez tartozik, ahol _n_ a többszörös. A modellnek minden új példa rul-ját _folyamatos számként_kell kiszámítania . Ez a szám a hiba előtt hátralévő időt jelöli.

#### <a name="label-construction-for-regression"></a>Címkeszerkezet regresszióhoz
A kérdés itt a következő: "Mi a fennmaradó hasznos élettartama (RUL) a berendezés?" A hiba előtti minden rekordesetében a címkét úgy kell kiszámítani, hogy hány idő van hátra a következő hiba előtt. Ebben a módszerben a címkék folytonos változók. (Lásd a 4. ábrát)

![4. ábra Regresszió címkézése](./media/predictive-maintenance-playbook/labelling-for-regression.png)

4. ábra Regresszió címkézése

Regresszió esetén a címkézés hibapontra hivatkozva történik. A számítás nem lehetséges anélkül, hogy tudnánk, mennyi ideig maradt fenn az eszköz a hiba előtt. Így a bináris besorolással ellentétben az adatok hibái nélküli eszközök nem használhatók modellezéshez. Ez a probléma a legjobban foglalkozik egy másik statisztikai technika nevű [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). De lehetséges szövődmények merülhetnek fel, amikor ezt a technikát a PdM használati esetekben, amelyek magukban foglalják az időváltozó adatok gyakori időközönként. A túléléselemzéssel kapcsolatos további információkért tekintse meg [ezt az egyszemélyes hívót.](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)

### <a name="multi-class-classification-for-predictive-maintenance"></a>Többosztályos besorolás a prediktív karbantartáshoz
A PdM-megoldásokban többosztályos osztályozási technikák használhatók két forgatókönyv esetén:
- Két _jövőbeli kimenetel_előrejelzése : Az első eredmény egy eszköz _meghibásodásáig szükséges idő tartománya._ Az eszköz több lehetséges időszak egyikéhez van hozzárendelve. A második eredmény annak valószínűsége , hogy a következő időszakban _a többszörös kiváltó okok egyike_miatt nem sikerül . Ez az előrejelzés lehetővé teszi a karbantartó személyzet számára, hogy figyelje a tüneteket, és megtervezze a karbantartási ütemterveket.
- Egy adott hiba _legvalószínűbb kiváltó okának_ előrejelzése. Ez az eredmény a megfelelő karbantartási műveleteket javasolja a hiba kijavítása érdekében. A kiváltó okok rangsorolt listája és az ajánlott javítások segíthetnek a technikusoknak abban, hogy a hiba után rangsorolják a javítási műveleteket.

#### <a name="label-construction-for-multi-class-classification"></a>Címkeszerkezet többosztályos osztályozáshoz
A kérdés itt a következő: "Mi a valószínűsége annak, hogy egy eszköz nem a következő _nZ_ időegységek, ahol _n_ az időszakok száma?" A kérdés megválaszolásához az nZ-t az eszköz meghibásodása előtt rögzíti az időgyűjtők (3Z, 2Z, Z) használatával. Címkézze fel az összes többi rekordot "normál" címkével (címke = 0). Ebben a módszerben a célváltozó _kategorikus_ értékeket tartalmaz. (Lásd az 5. ábrát).

![5. ábra Hibaidő-előrejelzési címkék a többosztályos besoroláshoz](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

5. ábra Címkézés többosztályos besoroláshoz a hibaidő előrejelzéséhez

A kérdés itt az: "Mi a valószínűsége annak, hogy az eszköz nem a következő X időmiatt kiváltó ok / probléma _P<sub>i?"</sub>_ ahol _én_ a lehetséges kiváltó okok száma. A kérdés megválaszolásához az X címke az eszköz meghibásodása előtt _"a P<sub>i"</sub>_(címke = _P<sub>i)</sub>_ miatt hamarosan sikertelen lesz. Címkézze fel az összes többi rekordot "normálisnak" (címke = 0). Ebben a módszerben a címkék kategorikusak (lásd a 6. ábrát).

![6. ábra Kiváltó okok előrejelzései a többosztályos besoroláshoz](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

6. ábra Címkézés többosztályos besoroláshoz a kiváltó okok előrejelzéséhez

A modell hozzárendeli a hiba valószínűsége miatt minden _P<sub>i,</sub> _ valamint annak valószínűsége, hogy nincs hiba. Ezek a valószínűségek nagyságrendszerint rendezhetők, hogy lehetővé tegyék a jövőben leginkább előforduló problémák előrejelzését.

A kérdés itt a következő: "Milyen karbantartási műveleteket javasol egy hiba után?" A kérdés megválaszolása érdekében a _címkézéshez nincs szükség jövőbeli horizont ra,_ mert a modell nem jósolja meg a hibát a jövőben. Ez csak megjósolni a legvalószínűbb kiváltó _oka, ha a hiba már megtörtént_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Képzési, érvényesítési és tesztelési módszerek a prediktív karbantartáshoz
A [csapat adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biztosítja a modell train-test-validate ciklus teljes lefedettségét. Ez a szakasz a PdM egyedi aspektusait tárgyalja.

### <a name="cross-validation"></a>Keresztellenőrzés
A [keresztellenőrzés](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) célja, hogy a betanítási fázisban a modell "tesztelésére" adatkészletet határozzon meg. Ezt az adatkészletet _érvényesítési készletnek nevezzük._ Ez a módszer segít korlátozni a problémákat, például _a túlszerelést,_ és betekintést nyújt abba, hogy a modell hogyan általánosítja a független adatkészletet. Azaz egy ismeretlen adatkészlet, ami lehet egy valós probléma. A PdM képzési és tesztelési rutinjának figyelembe kell vennie az idő különböző aspektusait, hogy jobban általánosítsa a láthatatlan jövőbeli adatokat.

Számos gépi tanulási algoritmus számos olyan hiperparamétertől függ, amelyek jelentősen módosíthatják a modell teljesítményét. Ezeknek a hiperparamétereknek az optimális értékeit a modell betanításakor a rendszer nem számítja ki automatikusan. Ezeket az adattudósnak kell meghatároznia. A hiperparaméterek jó értékeinek megkeresésére többféleképpen lehet jó értékeket találni.

A leggyakoribb a _k-fold kereszt-ellenőrzés,_ amely véletlenszerűen osztja a példákat _k_ redőkre. A hiperparaméterek minden egyes értékkészletéhez futtassa a _k-idő_ tanulási algoritmust. Minden iteráció, használja a példákat az aktuális szeres, mint egy érvényesítési készlet, és a többi példa, mint egy betanítási készlet. Az algoritmus betanítása a betanítási példákat, és kiszámítani a teljesítmény mutatók at érvényesítési példák. Ennek a ciklusnak a végén számítsa ki a _k_ teljesítménymutatók átlagát. A hiperparaméter-értékek minden egyes készletéhez válassza ki azokat, amelyek a legjobb átlagos teljesítménnyel rendelkeznek. A feladat a választott hiperparaméterek gyakran kísérleti jellegű.

A PdM-problémákban az adatok több adatforrásból származó események idősorozataként kerülnek rögzítésre. Ezek a nyilvántartások a címkézés időpontjától függően rendelhetők. Ezért ha az adatkészlet _véletlenszerűen_ van felosztva betanítási és érvényesítési készletre, _néhány betanítási példa később lehet, mint néhány érvényesítési példa._ A hiperparaméter-értékek jövőbeli teljesítményét a modell betanítása _előtt_ megérkezett adatok alapján kell megbecsülni. Ezek a becslések túlságosan optimisták lehetnek, különösen akkor, ha az idősorok nem helyhez kötöttek és idővel fejlődnek. Ennek eredményeképpen a kiválasztott hiperparaméter-értékek nem optimálisak lehetnek.

Az ajánlott módszer a példák felosztása betanítási és érvényesítési készlet _időfüggő_ módon, ahol az összes érvényesítési példák későbbi, mint az összes képzési példák. Minden egyes hiperparaméter-értékkészlethez tanítsa be az algoritmust a betanítási adatkészleten keresztül. Mérje meg a modell teljesítményét ugyanazon az érvényesítési készleten keresztül. Válassza ki a legjobb teljesítményt mutató hiperparaméterértékeket. A betanítási/érvényesítési felosztásáltal választott hiperparaméterértékek jobb jövőbeli modellteljesítményt eredményeznek, mint a keresztellenőrzéssel véletlenszerűen kiválasztott értékek.

A végleges modell a tanulási algoritmus betanításával érhető el a teljes betanítási adatok a legjobb hiperparaméter értékek használatával.

### <a name="testing-for-model-performance"></a>A modell teljesítményének tesztelése
A modell létrehozása után szükség van az új adatok jövőbeli teljesítményének becslésére. Egy jó becslés az érvényesítési készleten számított hiperparaméter-értékek teljesítménymetrikája, vagy a keresztérvényesítésből számított átlagos teljesítménymetrika. Ezek a becslések gyakran túlságosan optimisták. Előfordulhat, hogy a vállalkozás gyakran rendelkezik további irányelvekkel a modell tesztelésére vonatkozóan.

A PdM ajánlott módja a példák felosztása betanítási, érvényesítési és tesztelési adatkészletek _időfüggő_ módon. Minden teszt példát kell később, mint az összes betanítási és érvényesítési példákat. A felosztás után hozza létre a modellt, és mérje a teljesítményét a korábban leírtak szerint.

Ha az idősorok helyhez kötöttek és könnyen előre jelezhetők, mind a véletlenszerű, mind az időfüggő megközelítések hasonló becsléseket generálnak a jövőbeli teljesítményről. De amikor az idősorok nem helyhez kötöttek és/vagy nehezen megjósolandóak, az időfüggő megközelítés reálisabb becsléseket fog generálni a jövőbeli teljesítményről.

### <a name="time-dependent-split"></a>Időfüggő felosztás
Ez a szakasz az időfüggő felosztás megvalósításának ajánlott eljárásait ismerteti. Az alábbiakban ismertetjük az edzések és a tesztkészletek közötti időfüggő kétirányú felosztást.

Tegyük fel, hogy egy adatfolyam időbélyeggel események, például a különböző érzékelők mérések. Határozza meg a betanítási és tesztelési példák jellemzőit és címkéit több eseményt tartalmazó időkereteken keresztül. A bináris besoroláshoz például hozzon létre múltbeli eseményeken alapuló funkciókat, és hozzon létre címkéket a jövőbeli események alapján az "X" időegységeken belül a jövőben (lásd a [szolgáltatástervezési](#feature-engineering) és modellezési technikákról szóló szakaszokat). Így egy példa címkézési időkerete később jön, mint a jellemzőinek időkerete.

Az időfüggő felosztáshoz válassza ki a _T<sub>c</sub> betanítási időt,_ ahol a modell betanításához a korábbi adatokkal hangolt hiperparaméterek et T<sub>c-ig.</sub> Annak érdekében, hogy a t<sub>c-n</sub> túli jövőbeli címkék ne szivárogjanak be a betanítási adatokba, válassza ki a legutóbbi időt, amikor a betanítási példákat X egységnek kell címkézni a T<sub>c</sub>előtt. A 7. Az ábrán az X=2 és W=3 képzési és tesztelési készletei bemutatásának rekordjai láthatók:

![7. ábra Időfüggő felosztás a bináris osztályozáshoz](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

7. ábra Időfüggő felosztás a bináris osztályozáshoz

A zöld négyzetek a betanításhoz használható időegységekhez tartozó rekordokat jelölik. Minden egyes betanítási példa a szolgáltatásgenerálás elmúlt három időszakának, valamint a T<sub>c</sub>előtti címkézés két jövőbeli időszakának figyelembevételével jön létre. Ha a két jövőbeli időszak bármely része meghaladja a T<sub>c-t</sub>, ezt a példát zárja ki a képzési adatkészletből , mert a T<sub>c-n</sub>túl nem láthatóvá válnak .

A fekete négyzetek a végleges címkével ellátott adatkészlet rekordjait jelölik, amelyeket a betanítási adatkészletben nem szabad használni, tekintettel a fenti megkötésre. Ezeket a nyilvántartásokat a vizsgálati adatokban sem használjuk fel, mivel a T<sub>c</sub>előtt vannak. Ezen túlmenően, a címkézési időkeretek részben függ a képzési időkeret, ami nem ideális. A betanítási és tesztelési adatoknak külön címkézési időkeretekkel kell rendelkezniük a címkeinformációk szivárgásának megelőzése érdekében.

Az eddig tárgyalt technika lehetővé teszi az átfedést a képzési és tesztelési példák között, amelyek időbélyeggel rendelkeznek a T<sub>c</sub>közelében. A nagyobb elkülönítés elérésének megoldása a T<sub>c</sub> W időegységein belüli példák kizárása a vizsgálati készletből. Az ilyen agresszív felosztás azonban az adatok bőséges rendelkezésre állásától függ.

Regressziós modellek előrejelzésére használt RUL súlyosabbhatással van a szivárgási probléma. A véletlenszerű felosztási módszer használata extrém túlilleszkedéshez vezet. Regressziós problémák esetén a felosztásnak olyannak kell lennie, hogy a T<sub>c-t</sub> megelőzően a képzési készletbe való bekerülés előtt az eszközökhöz tartozó rekordok at. A levágásután meghibásodott eszközök rekordjai bekerülnek a tesztkészletbe.

A betanítási és tesztelési adatok felosztásának másik ajánlott eljárása az eszközazonosító szerint felosztás használata. A felosztásnak olyannak kell lennie, hogy a betanítási készletben használt eszközök egyike sem használható a modell teljesítményének teszteléséhez. Ezzel a megközelítéssel a modellnek nagyobb esélye van arra, hogy reálisabb eredményeket érjen el új eszközökkel.

### <a name="handling-imbalanced-data"></a>Kiegyensúlyozatlan adatok kezelése
Besorolási problémák esetén, ha több példa van egy osztályra, mint a többire, az adatkészlet _kiegyensúlyozatlan._ Ideális esetben a betanítási adatokban az egyes osztályok elegendő képviselői előnyben részesülnek a különböző osztályok közötti különbségtétel hez. Ha egy osztály kevesebb, mint az adatok 10%-a, az adatok kiegyensúlyozatlannak minősülnek. Az alulreprezentált osztályt _kisebbségi osztálynak nevezik._

Számos PdM-probléma ilyen kiegyensúlyozatlan adatkészletekkel szembesül, ahol az egyik osztály súlyosan alulreprezentált a másik osztályhoz vagy osztályokhoz képest. Bizonyos esetekben a kisebbségi osztály az összes adatpontnak csak 0,001%-át teheti ki. Az osztály kiegyensúlyozatlansága nem egyedi a PdM-ben. Más tartományok, ahol a hibák és anomáliák ritka előfordulások hasonló problémával szembesülnek, például a csalások észlelése és a hálózati behatolás. Ezek a kudarcok alkotják a kisebbségi osztály példáit.

Az adatok osztálykiegyensúlyozatlansága miatt a legtöbb szabványos tanulási algoritmus teljesítménye veszélybe kerül, mivel céljuk az általános hibaarány minimalizálása. 99%-ban negatív és 1%-os pozitív példákkal rendelkező adatkészletek esetén a modell 99%-os pontossággal jeleníthető meg, ha az összes példányt negatívként címkézi. De a modell tévesen osztályozza az összes pozitív példát; tehát egyenletes ha -a pontosság van magas, a algoritmus van nem egy hasznos egy. Következésképpen a hagyományos értékelési mérőszámok, például _a hibaarány általános pontossága_ nem elegendőek a kiegyensúlyozatlan tanuláshoz. Ha kiegyensúlyozatlan adatkészletekkel szembesül, a modell kiértékeléséhez más mérőszámok at használnak:
- Pontosság
- Visszahívás
- F1 pontszámok
- Költséggel korrigált ROC (vevő működési jellemzői)

Ezekről a mérőszámokról további információt a [modellkiértékelés](#model-evaluation)című témakörben talál.

Vannak azonban olyan módszerek, amelyek segítenek orvosolni osztály egyensúlyhiány probléma. A két legfontosabb a _mintavételi technikák_ és _a költségérzékeny tanulás._

#### <a name="sampling-methods"></a>Mintavételi módszerek
A kiegyensúlyozatlan tanulás mintavételi módszerek használatával módosítja a betanítási adatkészletet egy kiegyensúlyozott adatkészletre. A vizsgálati készletre nem kell mintavételi módszereket alkalmazni. Bár számos mintavételi technika létezik, a legtöbb egyenes _enyvesk véletlenszerű túlmintavételezés_ és _mintavétel alatt állnak._

_A véletlenszerű túlmintavételezés_ magában foglalja egy véletlenszerű minta kiválasztását a kisebbségi osztályból, ezek a példák replikálása és a betanítási adatkészlethez való hozzáadása. Következésképpen a kisebbségi osztályban nőtt a példák száma, és végül egyensúlyba hozza a különböző osztályok példáinak számát. A túlmintavételezés hátránya, hogy bizonyos példák több példánya túl specifikussá teheti az osztályozót, ami túlilleszkedéshez vezethet. A modell nagy betanítási pontosságot mutathat, de a nem látott vizsgálati adatokon nyújtott teljesítménye nem optimális lehet.

Ezzel szemben _a mintavétel idomok_ véletlenszerű kiválasztása véletlenszerű mintát a többségi osztályból, és eltávolítja ezeket a példákat a betanítási adatkészletből. Ha azonban eltávolítja a példákat a többségi osztályból, előfordulhat, hogy az osztályozó kihagyja a többségi osztályra vonatkozó fontos fogalmakat. A _hibrid mintavételezés,_ ha a kisebbségi osztály túlmintavételezett, és a többségi osztály egyidejűleg alulmintavételezik, egy másik életképes megközelítés.

Sok kifinomult mintavételi technikákat. A választott technika az adatelemzési tulajdonságoktól és az adattudós iteratív kísérleteinek eredményeitől függ.

#### <a name="cost-sensitive-learning"></a>Költségérzékeny tanulás
A PdM-ben a kisebbségi osztályt alkotó hibák a szokásos példáknál nagyobb érdeklődést mutatnak. Tehát a hangsúly elsősorban az algoritmus teljesítményét a hibákat. A pozitív osztály negatív osztályként való helytelen előrejelzése többe kerülhet, mint fordítva. Ezt a helyzetet általában egyenlőtlen veszteségnek vagy aszimmetrikus költségnek nevezik, ha az elemeket különböző osztályokba sorolják be. Az ideális osztályozónak nagy előrejelzési pontosságot kell biztosítania a kisebbségi osztályfelett, anélkül, hogy veszélyeztetné a többségi osztály pontosságát.

Ennek az egyensúlynak többféle módja van. Az egyenlőtlen veszteség problémájának csökkentése érdekében rendeljen magas költséget a kisebbségi osztály téves besorolásához, és próbálja meg minimalizálni a teljes költséget. Az olyan algoritmusok, mint _az SVM-ek (Support Vector Machines)_ eredendően alkalmazzák ezt a módszert, lehetővé téve a pozitív és negatív példák betanításának költségét a betanítás során. Hasonlóképpen, a kiemelési módszerek, például _a kiemelt döntési fák_ általában jó teljesítményt mutatnak a kiegyensúlyozatlan adatokkal.

## <a name="model-evaluation"></a>A modell kiértékelése
A téves besorolás jelentős problémát jelent a PdM-forgatókönyvek esetében, ahol a téves riasztások költsége a vállalkozás számára magas. Például a légi jármű földeléséről a hajtómű meghibásodásának helytelen előrejelzése alapján hozott döntés megzavarhatja a menetrendeket és az utazási terveket. Ha egy gépet offline állapotba hoz egy futószalagról, az bevételkieséshez vezethet. Így a modell értékelése a megfelelő teljesítménymérőkkel az új vizsgálati adatokkal szemben kritikus fontosságú.

A PdM-modellek kiértékeléséhez használt tipikus teljesítménymutatókat az alábbiakban tárgyaljuk:

- [A pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision) az osztályozó teljesítményének leírására használt legnépszerűbb mérőszám. A pontosság azonban érzékeny az adateloszlásokra, és nem hatékony intézkedés a kiegyensúlyozatlan adatkészletekkel rendelkező forgatókönyvek esetében. Ehelyett más metrikákat használ. Eszközök, mint [a zavartmátrix](https://en.wikipedia.org/wiki/Confusion_matrix) számítási és oka a pontossága a modell.
- A PdM modellek [pontossága](https://en.wikipedia.org/wiki/Precision_and_recall) a téves riasztások sebességére vonatkozik. A modell kisebb pontossága általában nagyobb mértékű téves riasztásnak felel meg.
- [A visszahívási](https://en.wikipedia.org/wiki/Precision_and_recall) arány azt jelzi, hogy a modell hány hibát azonosított helyesen a tesztkészletben. A magasabb visszahívási arány azt jelenti, hogy a modell sikeresen azonosítja a valódi hibákat.
- [Az F1 pontszám](https://en.wikipedia.org/wiki/F1_score) a pontosság és a visszahívás harmonikus átlaga, értéke 0 (legrosszabb) és 1 (legjobb) között mozog.

A bináris osztályozáshoz
- [Vevő működési görbék (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is népszerű metrika. A ROC-görbékben a modell teljesítményét a ROC egyetlen rögzített működési pontja alapján értelmezi a rendszer.
- De a PdM problémák, _decile táblázatok_ és _lift grafikonok_ informatívabb. Csak a pozitív osztályra (hibákra) összpontosítanak, és összetettebb képet adnak az algoritmus teljesítményéről, mint a ROC-görbék.
  - _A decile táblák_ tesztpéldákkal jönnek létre a hiba valószínűségek csökkenő sorrendjében. A megrendelt mintákat ezután deciles-ekbe csoportosítják (a legnagyobb valószínűséggel a minták 10%-a, majd 20%, 30% és így tovább). Az arány (valódi pozitív arány) /(véletlenszerű alapvonal) minden decile segít megbecsülni az algoritmus teljesítményét minden decile. A véletlenszerű alapérték 0,1, 0,2 és így tovább.
  - [A növekedési diagramok](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) a decile valódi pozitív arányt ábrázolják, szemben az összes deciles véletlenszerű valódi pozitív rátát. Az első deciles általában a hangsúly az eredmények, mivel azt mutatják, a legnagyobb nyereséget. Az első deciles is tekinthető reprezentatív "veszélyben", ha használják a PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modellüzembe működés prediktív karbantartáshoz

Az adatelemzési gyakorlat előnye csak akkor valósul meg, ha a betanított modell működőképessé válik. Ez azt, hogy a modellt az üzleti rendszerekbe kell telepíteni, hogy új, korábban nem látott adatokon alapuló előrejelzéseket készítsen.  Az új adatoknak pontosan meg kell felelniük a betanított modell _modellaláírásának_ két módon:
- az új adatok minden logikai példányában (például egy táblázatban egy sorban) jelen kell lennie.
- az új adatokat előre fel kell dolgozni, és minden egyes funkciót úgy kell megtervezni, pontosan ugyanúgy, mint a betanítási adatokat.

A fenti folyamat számos módon szerepel a tudományos és ipari irodalomban. De a következő kijelentések ugyanazt jelentik:
- _Új adatok pontozása_ a modell használatával
- _A modell alkalmazása_ új adatokra
- A modell _működőképessé tétele_
- _A_ modell üzembe helyezése
- _A modell futtatása új_ adatokkal

Ahogy korábban említettük, a PdM modell-üzembe idoszakai eltérnek a társaiktól. Az anomáliaészlelést és a hibaészlelést tartalmazó forgatókönyvek általában _online pontozási_ (más néven valós _idejű pontozást) valósítanak_meg. Itt a modell minden bejövő rekordot _felszámít,_ és egy előrejelzést ad vissza. Anomália detektálása esetén az előrejelzés azt jelzi, hogy anomália történt (Példa: Egyosztályos SVM). A hibaészlelése, lenne a hiba típusa vagy osztálya.

Ezzel szemben a PdM _kötegelt pontozást_foglal magában. A modellaláírásnak való megfelelés érdekében az új adatok funkcióit a betanítási adatokkal azonos módon kell megtervezni. Az új adatokra jellemző nagy adatkészletek esetében a szolgáltatások az időablakokban összesítve vannak, és kötegben vannak pontozva. A kötegelt pontozás általában olyan elosztott rendszerekben történik, mint a [Spark](https://spark.apache.org/) vagy az [Azure Batch.](https://docs.microsoft.com/azure/batch/batch-api-basics) Van néhány alternatíva - mindkettő nem optimális:
- Az adatfolyam-adatmotorok támogatják az összesítést a memóriában lévő ablakokon keresztül. Így lehet azzal érvelni, hogy támogatják az online pontozás. De ezek a rendszerek alkalmasak a sűrű adatok szűk ablakok az idő, vagy ritka elemek szélesebb ablakok. Előfordulhat, hogy nem méretezhető képpen a sűrű adatok szélesebb időablakok, apdm-forgatókönyvek látható.
- Ha a kötegelt pontozás nem érhető el, a megoldás az online pontozás adaptálása az új adatok kis kötegekben történő kezeléséhez.

## <a name="solution-templates-for-predictive-maintenance"></a>Megoldássablonok prediktív karbantartáshoz

Az útmutató utolsó szakasza az Azure-ban megvalósított PdM-megoldássablonok, oktatóanyagok és kísérletek listáját tartalmazza. Ezek a PdM-alkalmazások bizonyos esetekben perceken belül telepíthetők egy Azure-előfizetésbe. Használhatók a koncepció próbabemutatóiként, homokozókként, hogy kísérletezzenek alternatívákkal, vagy gyorsítóként a tényleges éles implementációkhoz. Ezek a sablonok az [Azure AI-galériában](https://gallery.azure.ai) vagy az [Azure GitHubon](https://github.com/Azure)találhatók. Ezek a különböző minták idővel bekerülnek ebbe a megoldássablonba.

| # | Cím | Leírás |
|--:|:------|-------------|
| 2 | [Azure prediktív karbantartási megoldássablon](https://github.com/Azure/AI-PredictiveMaintenance) | Egy nyílt forráskódú megoldássablon, amely bemutatja az Azure ML-modellezést és egy teljes Azure-infrastruktúrát, amely képes támogatni a prediktív karbantartási forgatókönyveket az IoT távoli figyelésének környezetében. |
| 3 | [Mélytanulás a prediktív karbantartáshoz](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Az Azure Notebook az LSTM (hosszú rövid távú memória) hálózatok (az ismétlődő neurális hálózatok egy osztálya) prediktív karbantartáshoz való használatával szolgáló bemutató megoldással, [a minta](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)blogbejegyzésével.|
| 4 | [Prediktív karbantartási modellezési útmutató r-ben](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | PdM modellezési útmutató parancsfájlokkal R.|
| 5 | [Azure prediktív karbantartás a repülőgépipar számára](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Az egyik első PdM-megoldássablonok alapján Az Azure ML 1.0-s a repülőgépek karbantartása. Ez az útmutató ebből a projektből származik. |
| 6 | [Azure AI-eszközkészlet az IoT Edge-hez](https://github.com/Azure/ai-toolkit-iot-edge) | AI az IoT Edge-ben a TensorFlow használatával; az eszközkészlet deep learning-modelleket csomagol az Azure IoT Edge-kompatibilis Docker-tárolókban, és ezeket a modelleket REST API-ként teszi elérhetővé.
| 7 | [Azure IoT prediktív karbantartás](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS – előre konfigurált megoldás. Repülőgép-karbantartási PdM-sablon az IoT Suite segítségével. [Egy másik dokumentum](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) és [forgatókönyv](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) ugyanahhoz a projekthez kapcsolódik. |
| 8 | [Prediktív karbantartási sablon az SQL Server R Services használatával](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Az R-szolgáltatásokon alapuló fennmaradó hasznos élettartam-forgatókönyv bemutatója. |
| 9 | [Prediktív karbantartási modellezési útmutató](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | A repülőgép-karbantartási adatkészlet funkció, amelyet az R használatával [terveztek kísérletekhez](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) és [adatkészletekhez,](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) valamint [az Azure-jegyzetfüzetek](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) és [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) az AzureML 1.0-s sorozatban|

## <a name="training-resources-for-predictive-maintenance"></a>Képzési források a prediktív karbantartáshoz

A Microsoft Azure tanulási útvonalakat kínál a PdM-technikák mögött megalapult fogalmakhoz, a tartalom és az általános AI-fogalmak és gyakorlat betanítása mellett.

| Képzési forrás  | Rendelkezésre állás |
|:-------------------|--------------|
| [A PdM tanulási útvonala a fák és a véletlenszerű erdő használatával](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Nyilvános | 
| [A PdM tanulási útvonala a Deep Learning használatával](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Nyilvános |
| [AI-fejlesztő az Azure-ban](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Nyilvános |
| [Microsoft AI Iskola](https://aischool.microsoft.com/learning-paths) | Nyilvános |
| [Azure AI-tanulás a GitHubról](https://github.com/Azure/connectthedots/blob/master/readme.md) | Nyilvános |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Nyilvános |
| [Microsoft AI YouTube webinárierek](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Nyilvános |
| [Microsoft AI-megjelenítés](https://channel9.msdn.com/Shows/AI-Show) | Nyilvános |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerek |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partnerek |

Ezen túlmenően, ingyenes MOOCS (masszív nyílt online tanfolyamok) a AI kínálnak online akadémiai intézmények, mint a Stanford és az MIT, és más oktatási cégek.
