---
title: A prediktív karbantartási megoldásokat Azure AI útmutatója |} Microsoft Docs
description: A prediktív karbantartási megoldásokat több függőleges iparágakban megoldásaira épül, adattudomány átfogó leírást.
services: machine-learning
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: e069a7d16fa56f2c7590edbda8339182835ef367
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837259"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>A prediktív karbantartási megoldásokat Azure AI útmutatója

## <a name="summary"></a>Összegzés

A prediktív karbantartási (**PdM**) olyan népszerű alkalmazás prediktív elemzés, amelyek segítségével a vállalatok számára a különböző iparágak magas eszköz kihasználtságának eléréséhez és a működési költségek megtakarítását. Ez az útmutató egyesíti az üzleti és analitikai iránymutatásokat és gyakorlati tanácsok sikeresen fejlesztésére és PdM megoldások segítségével telepítheti a [AI a Microsoft Azure platform](https://azure.microsoft.com/overview/ai-platform) technológia.

Első Ez az útmutató bemutatja, iparág-specifikus üzleti forgatókönyvek és ezek a forgatókönyvek jogosító PdM folyamatán. A követelmények és a modellezési módszereket PdM megoldások is rendelkezésre állnak. A fő az útmutató tartalma adatok tudományos folyamat – beleértve az adatok előkészítése, a szolgáltatás mérnöki csapathoz, a modell létrehozása és a modell operationalization lépésein. Ezeket a kulcsfontosságú fogalmakat kiegészítése, ez az útmutató számos olyan megoldást sablonok PdM alkalmazásfejlesztés felgyorsítása érdekében. Az útmutató hasznos képzési erőforrásokra vonatkozó további információt a adattudomány mögött AI gyakorló is mutat. 

### <a name="data-science-guide-overview-and-target-audience"></a>Adatok tudományos útmutató áttekintése és azon célközönség
Ez az útmutató az első felében tipikus üzleti problémák, a PdM a problémák megoldására végrehajtási előnyeit ismerteti, és néhány gyakori alkalmazási esetei sorolja fel. Az Üzleti döntéshozóknak (BDMs) ki ezt a tartalmat a előnyeit. A második fele a adattudomány mögött PdM ismerteti, és ezeket az alapelveket a jelen útmutatóban vázolt használatával készített PdM megoldások listáját tartalmazza. Tanulási útvonalai és oktatóanyag mutató hivatkozások is biztosít. Műszaki döntéshozók (TDMs) számára a tartalom.

| Kezdje... | Ha Ön... |
|:---------------|:---------------|
| [Üzleti esetben prediktív karbantartás](#Business-case-for-predictive-maintenance) |állásidő és a működési költségek csökkentése érdekében, és növeli a készülék kihasználtságát szeretnék üzleti döntéshozó (BDM) |
| [A prediktív karbantartási Adattudomány](#Data-Science-for-predictive-maintenance) |műszaki döntéshozó (TDM) kiértékelése technológiákkal PdM megtudhatja, hogy az egyedi adatok feldolgozása és a prediktív karbantartási AI követelményei |
| [A prediktív karbantartási megoldás sablonok](#Solution-templates-for-predictive-maintenance)|a szoftver felelős mérnök vagy AI fejlesztői gyorsan készíthet egy bemutató és egy-a-koncepció igazolása passzív keresése |
| [A prediktív karbantartási oktatóanyagok](#Training-resources-for-predictive-maintenance) | a fenti részének vagy egészének és tudhat meg a adattudomány, eszközöket és technikákat eligazodást fogalmakat.

### <a name="prerequisite-knowledge"></a>Előfeltételek Tudásbázis
A BDM tartalom nem várja el a korábbi adatok tudományos, hogy az olvasó. A TDM tartalom statisztikák és adatelemzéssel vonatkozó általános ismeretekre bizonyulhat hasznosnak. Azure Data AI, Python, R, XML, és szolgáltatások JSON ismerete ajánlott. AI technikák Python és R csomagban valósíthatók meg. Megoldás sablonok valósíthatók meg Azure-szolgáltatások, a fejlesztői eszközök és az SDK-k használatával.

## <a name="business-case-for-predictive-maintenance"></a>Üzleti esetben prediktív karbantartás

Vállalatok számára szükséges kritikus berendezések maximális hatékonyság és a visszatérési megvalósításához nagy befektetések kihasználtsági futnia kell. Ezek az eszközök repülőgép motorok, turbinák, felvonók vagy ipari hőmérsékletű -, amely költségeket millióira - fénymásolókról, kávé gépek vagy Vízhűtők mindennapi készülékek le terjedhet.
- Alapértelmezés szerint a legtöbb vállalat támaszkodnak _javítási karbantartási_, ahol részek helyett és ha azt elmulasztják. Javítási karbantartásnak részek teljesen használt (tehát nem általi lefoglalását összetevő élettartam), de költségek az üzleti állásidő, munka és tervezett karbantartást követelmények (ki óra vagy kényelmetlen helyek).
- A következő szint vállalatok számára ajánlott: _megelőző karbantartás céljából_, amennyiben azok egy részére hasznos élettartamát határozza meg és karbantartása, vagy cserélje le a hiba előtt. Megelőző karbantartás céljából Ezzel elkerülheti a nem ütemezett és végzetes hibák. De ütemezett üzemszünet magas költségek, hiány használat előtt használja, és továbbra is tartalmazó teljes élettartamuk összetevő tartani.
- Az adatdeduplikálás _prediktív karbantartási_ javítási és megelőző karbantartás engedélyezésével közötti egyensúly optimalizálása _JIT_ cseréje az összetevők. Ezek megközelítik hiba ezt a módszert csak ezek az összetevők helyettesíti. Összetevő lifespans (képest megelőző karbantartás céljából) kiterjesztésével és tervezett karbantartást és a munka költségek csökkentését (keresztül javítási karbantartási), vállalatok képes arra, hogy költséghatékony és versenyképes előnyeit.

## <a name="business-problems-in-pdm"></a>A PdM üzleti problémák
Vállalatok szembesülhetnek magas működési kockázat, váratlan hibák miatt és a problémák az alapvető ok betekintést összetett rendszerek korlátozott. A fő üzleti kérdéseket a következők:

- Rendellenességek észlelését berendezések vagy a rendszer teljesítményét és funkciót.
- Előre jelezni, hogy egy eszköz a közeljövőben meghiúsulhat.
- Egy eszköz a fennmaradó élettartama becslése.
- A fő oka sikertelen volt-e az eszköz azonosítására.
- Azonosítsa a karbantartási műveleteket kell végeznie, akkor, ha az eszköz.

Tipikus cél nyilatkozatai PdM a következők:

- Kritikus kritikus berendezések működési kockázat csökkentése.
- Megnövelheti az eszközök megtérülési rátát előrejelzésére hibák, mielőtt bekövetkeznének.
- Karbantartási költségek szabályozásához just-in-time karbantartási műveletek engedélyezése.
- Ügyfél kopásállóság alacsonyabb, a márka lemezképet, és elvesznek forgalmi javítása.
- Alacsonyabb készlet költségeket csökkenti a készlet által az újrarendelési előrejelzésére mutasson.
- Csatlakozik a különböző karbantartási problémákat minták felderítése.
- Adja meg a KPI-k (fő teljesítménymutatók) állapotfigyelő pontszámait mint Eszközállapotok.
- Becsült hátralévő eszközök élettartamát.
- Javasoljuk, időben karbantartási tevékenységeket.
- Csak az idő készlet engedélyezéséhez részek helyettesítő rendelés dátumai becslése.

A cél kimutatásai a kiindulási pont:

- _adatszakértőkön_ elemzéséhez és prediktív konkrét problémák megoldására.
- _a felhő mérnökök és a fejlesztők_ hozzáfoghat egy teljes körű megoldás.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Jogosult a prediktív karbantartási kapcsolatos problémák
Fontos, hogy nem minden esetben használja, vagy üzleti problémák hatékonyan megoldhatók PdM emelje ki. Nincsenek három fontos megfelelő feltételeket, amelyek a probléma kiválasztása során figyelembe kell venni:

- A probléma-nak kell lennie a prediktív jellegű; Ez azt jelenti, hogy kell lennie a cél- vagy előre az eredmény. A probléma lehet egy egyszerű elérési útja hibák megelőzése, azok észlelése esetén végrehajtott műveletet.
- A probléma, amely tartalmazza a készülék működési előzményeinek rekorddal kell rendelkeznie _jó vagy rossz eredményekkel_. Hibás eredményekkel mérséklésére műveletek is biztosítani kell az ezeket a rekordokat részeként. Hibajelentések, karbantartási rögzít teljesítménycsökkenés, javítsa ki, és cserélje le naplókat is fontos. Ezenkívül, és helyettesítő rekordok javítása érdekében végzett javítások is hasznosak.
- A rögzített előzmények tükröződnek _vonatkozó_ adatokat _elegendő_ elég quality támogatásához a használati eset. Adatok relevanciájának és megfelelősége kapcsolatos további információkért lásd: [prediktív karbantartási követelményeit](#Data-requirements-for-predictive-maintenance).
- Végül az üzleti pontosan ismeri a probléma tartomány szakértők kell rendelkeznie. A belső folyamatokat és eljárásokat kell az elemző megértéséhez, valamint az adatok értelmezéséhez segítséget tud nyújtani a tisztában kell lennie. Akkor is kell tudni elvégezni a szükséges módosításokat a meglévő üzleti folyamatok jobb adatgyűjtés a problémák, szükség esetén.

## <a name="sample-pdm-use-cases"></a>A minta PdM használati esetek
Ez a szakasz a különböző iparágak például űrtechnikai segédprogramok vagy szállítására PdM használati esetek gyűjteménye összpontosít. Az egyes szakaszokon kezdődik-e egy üzleti probléma, és a PdM, a vonatkozó adatok az üzleti problémát, és végül a PdM megoldás előnyeit körülvevő előnyeit ismerteti.

| Üzleti probléma | Számos előnyt biztosít az PdM |
|:-----------------|-------------------|
|**Repülési**      |                   |
|_Késleltetés és a törlések repülési_ gépi kapcsolatos problémák miatt. Hibák, nem javítható időben megszakítandó járatok okozhatnak, és megzavarhatják ütemezés és a műveletek. |PdM megoldásokat képes előre jelezni késleltetett vagy a gépi hibák miatt megszakítva repülőgép valószínűségét.|
|_Repülőgép motor részek hiba_: repülőgép motor része cserékhez belül a légitársaság iparági általános karbantartási feladatokat közé tartoznak. Karbantartási megoldásokhoz szükséges, hogy az összetevő-készlet rendelkezésre állásának, és a tervezési gondos felügyeleti|Gyűjtse össze a beruházási költségek jelentős csökkenéséhez vezet az eszközintelligencia összetevő megbízhatóság igényt.|
|**Pénzügyi** |                         |
|_ATM hiba_ belül a banki iparági gyakori probléma. A probléma jelentheti a valószínűsége annak, hogy egy ATM cash visszavonása tranzakció megszakadt, lekérdezi a pénzbeli adagoló egy dokumentum a probléma megoldásáig részben vagy egészben hiba miatt. A tranzakció sikertelen előrejelzéseket alapján, adatközponthoz is kiszolgálásra proaktív megakadályozhatja, hogy a hiba lépett fel a.| Hanem engedélyezi a gép félúton keresztül a tranzakció sikertelen, a kívánatos esetben programot a gép visszautasítja a szolgáltatás az előrejelzés alapján.|
|**Energiafogyasztás** |                          |
|_Turbinás hibák szél_: szél turbinák környezeti felel országokban fő energia forrásai, és nagy beruházási költségek vonatkozhat. A szél turbinák nyilvános kulcsokra épülő a kódgenerátor motor. a hiba Ez a beállítás a turbinás hatástalan. Egyúttal nagyon költséges javításához.|Előrejelzése KPI-k, például az energiavállalatok MTTF (átlagos idő) segítségével turbinás hibák megelőzése, és minimális állásidő érdekében. Hiba valószínűség tájékoztatja a figyelheti, hogy hamarosan sikertelen turbinák technikusok, valamint ütemezheti a karbantartás ideje-alapú rendszerek. A prediktív modellek adjon meg másik tényező befolyásolja a hiba, amely segítségével a technikusok betekintést jobb megértése érdekében az esetleges problémák okait.|
|_Áramköri megszakító hibák_: kiemelt sorok mindig garantálja az energia kézbesítési működőképes legyen terjesztés házak és vállalatok számára. Kör szóhatároló szert: csökkenthetjük vagy power való megosztása kárt elkerülése sorok során túl van terhelve vagy káros időjárás feltételeket. Itt az üzleti probléma előre jelezni az áramköri megszakító hibák.| PdM megoldások javítási költségek csökkentése és eszközökre, például a kapcsolatcsoport szóhatároló élettartamát növelése érdekében. Váratlan meghibásodások és szolgáltatásmegszakadások csökkentésével az energiagazdálkodási hálózati minőségének javítása érdekében azokat.|
|**Szállítási és logisztikai** |    |
|_Foglalhatja ajtó hibák_: nagy foglalhatja vállalatok tartalmazza a működési felvonók világszerte több millió teljes verem szolgáltatást. Foglalhatja biztonsági, megbízhatósági és hasznos üzemidő fő fontos szempont, az ügyfelek számára. Ezeknek a vállalatoknak keresztül érzékelők, a javítási és megelőző karbantartás segítségével nyomon követni ezek, és számos egyéb attribútumai. Az egy foglalhatja a leginkább szembetűnő ügyfél probléma foglalhatja ajtók hibásan működik. Az üzleti probléma ebben az esetben arra, hogy a Tudásbázis prediktív alkalmazás, amely képes a lehetséges okai ajtó hibák.| Felvonók potenciálisan 20-30 év élettartamot beruházási beruházásokat. Így minden lehetséges értékesítést lehet versenyhelyzetében; ezért elvárásainak- és támogatási szolgálatához a rendszer magas. A prediktív karbantartási is adjon meg a vállalatoktól előnyös használhatóságát termékeikben versenytársaik, majd szolgáltatásajánlatok.|
|_Hibák kerék_: hibák fiók kerék fele az összes derailments betanítása és a globális oldalon iparágat milliárd költsége. Kerék hibákat is okozhat a sínek romlani, néha, amely túl korán hibájához oldalon. Oldalon oldaltörések katasztrofális esemény, például derailments vezethet. Ilyen esetekben elkerülése érdekében vasutak kerekek teljesítményének figyeléséhez, és cserélje le őket megelőző módon. Itt az üzleti probléma az előrejelzés kerék hibák.| Kerekek prediktív karbantartási segít just-in-time csere |
|_Subway vonat ajtó hibák_: egy fő subway műveletek késést oka, hogy a vonat autók ajtó hibák. Itt az üzleti probléma a vonat ajtó hibák előre jelezni.|Korai tájékoztatási ajtó hiba vagy ajtó hibát, amíg a napok számát segít a Karbantartás ütemezése ajtó betanítása üzleti optimalizálás.|

A következő szakasz részletesen ismerteti a kapcsolatban a fentiekben ismertetett PdM előnyök megvalósításához a lekérdezi.

## <a name="data-science-for-predictive-maintenance"></a>A prediktív karbantartási Adattudomány

Ez a szakasz tudományos alapelvek és eljárás általános útmutatást nyújt a PdM a. Célja, hogy egy TDM érdekében megoldásokért felelős mérnök vagy egy fejlesztő megérteni az Előfeltételek és a végpontok közötti AI alkalmazások PdM kiépítése során. Ez a szakasz a bemutatók áttekintése és olvasható, és a koncepció igazolása sablonok felsorolt [prediktív karbantartási megoldás sablonok](#Solution-templates-for-predictive-maintenance). Ezen alapelvek és ajánlott eljárásokat használhatja az Azure-ban a PdM megoldás megvalósításához.

> [!NOTE]
> Ez az útmutató nem célja az olvasó Adattudomány mutatja meg. Számos hasznos források megadta-e további információ a részben [oktatóanyagok prediktív karbantartási](#Training-resources-for-predictive-maintenance). A [megoldás sablonok](#Solution-templates-for-predictive-maintenance) az útmutatóban szereplő néhány, az alábbi AI eljárások bizonyos PdM problémák bemutatása.

## <a name="data-requirements-for-predictive-maintenance"></a>A prediktív karbantartási követelményeit

A learning sikere attól függ, milyen tanított folyamatban van a a minőségének és (b) a tanuló képességét. Prediktív modelleket ismerje meg az előzményadatok minták, és előre jelezni jövőbeli eredményt bizonyos valószínűséggel ezen megfigyelt minták alapján. A modellek prediktív pontossága attól függ, a alkalmazhatóságát megfelelősége és tanítási és tesztelési adatok. "Pontozza a mennyiségeket" a modell segítségével új adatok ugyanazokat a szolgáltatásokat és séma kell rendelkeznie a tanítási és tesztelési célú adatként. Új adatokat a szolgáltatás jellemzőit (típus, sűrűség, terjesztési és így tovább) kell egyeznie a tanítási és tesztelési adathalmazokat. Ez a szakasz az ilyen adatok követelmények célja.

### <a name="relevant-data"></a>A vonatkozó adatokat

Először az adatok-nak kell lennie _a problémára vonatkozó_. Vegye figyelembe a _hiba kerék_ tárgyalt-és nagybetűhasználattal újabb - a betanítási adatok tartalmaznia kell a kerék műveleteivel kapcsolatos funkciók. Ha a probléma előre jelezni sikertelen volt a _vontatási rendszer_, mint amelyek a különböző összetevők vontatási rendszer rendelkezik a betanítási adatok. Az első esetben egy adott összetevőre célozza meg, míg a második esetben célozza nagyobb alrendszer hibája. Az általános javasoljuk, hogy előrejelzés rendszerek nagyobb alrendszereket helyett meghatározott összetevőkre vonatkozó tervezési, mivel ez utóbbi fog rendelkezik több megvédheti adatait. A tartomány szakértő (lásd: [befolyásoló problémákat prediktív karbantartási](#Qualifying-problems-for-predictive-maintenance)) kiválasztásának lépésein. az adatok elemzéséhez legfontosabb részhalmazának segítségül. Nagyobb részletes ismertetése a releváns adatforrásokkal [adatok előkészítése az prediktív karbantartási](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Elegendő adatot
Gyakran feltett kérdéseket tekintetében előzmények adatai: (1) "hány sikertelen események szükségesek a modell betanításához?" (2) "rekordok minősül"elegendő"?" Nincsenek nem végleges válaszokat, de csak szabályok megoldás. (1), a sikertelen események száma több jobb a modell. (2) és a hibaesemények pontos számát határozza meg az adatokat, majd a megoldani kívánt probléma környezetében. De a tükrözés oldalán, ha egy gép meghibásodik túl gyakran majd az üzleti váltják fel őket, amely csökkenti a sikertelen példányok. Itt ebben az esetben a tartományból szakértői útmutatást fontos. Van azonban a probléma a folyamatosan növekvő adatmennyiségnek módszerek _ritka események_. A szakasz ismerteti [kezelnek imbalanced adatokat](#Handling-imbalanced-data).

### <a name="quality-data"></a>Minőségi adatok
Az adatok minőségének kritikus – minden előrejelzőjének attribútum értéke lehet _pontos_ együtt a cél változó értékét. Az adatminőségi statisztikák és adatok felügyeleti egy jól vizsgált területre, és emiatt ki, az útmutató hatókörén.

> [!NOTE]
> Több erőforrást és vállalati termékek minőségének adatok továbbítására is van. Hivatkozások mintát lejjebb tekinthetők meg:
> - Dasu, T, a Johnson, a T., felderítő adatbányászat, és az adatok tisztítása, Wiley, 2003.
> - [Felderítő adatelemzés, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, mennyiségi adatok nagy adatbázisokhoz tisztítása](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der keresés, M, bevezetés az adatok r tisztítása](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Adatok előkészítése az prediktív karbantartás

### <a name="data-sources"></a>Adatforrások

A prediktív karbantartási releváns adatforrásokkal tartalmazza, azonban nem csak:
- Hiba előzmények
- Karbantartási javítási előzmények
- Gép üzemi körülmények között
- Berendezések metaadatok

#### <a name="failure-history"></a>Hiba előzmények
Sikertelen események ritkák PdM alkalmazásokban. Azonban amikor a előrejelzési modellek létrehozása, az algoritmus kell egy összetevő normál működési mintát, valamint a sikertelen minták megismerése. Így a betanítási adatok mindkét kategóriák példák elegendő számú kell tartalmaznia. Karbantartási rögzíti és részek helyettesítő előzmények szolgálnak sikertelen események kereséséhez. Néhány tartomány Tudásbázis segítségével a betanítási adatok rendellenességeinek is adható meg hibák.

#### <a name="maintenancerepair-history"></a>Karbantartási javítási előzmények
Egy eszköz karbantartási előzmények azokról az összetevőkről cseréje esetén javítási tevékenységét stb részleteit tartalmazza. Ezek az események rögzítése teljesítménycsökkenést minták. Ez a betanítási adatok számára elengedhetetlen információ hiányában félrevezető modell eredmények vezethet. Hiba előzmények a karbantartási előzmények belül is találhatók a különleges hibakódok vagy rendelés dátumai részeit. További adatforrások hiba minták befolyásoló legyen a vizsgált és tartomány szakértők által biztosított.

#### <a name="machine-operating-conditions"></a>Gép üzemi körülmények között
A készülék műveletben érzékelő alapján (vagy egyéb) streamadatok egy fontos adatforrás. A PdM a kulcs feltételezése a, hogy a számítógép rendszerállapotát időbeli mindennapos működése során csökkenti. Az adatok várhatóan idő-változó szolgáltatásokat tartalmazhat, rögzítheti a korosodási mintát, és bármely rendellenességeket, amelyek a csökkenéséhez vezet. A historikus szempontja, hogy az adatok az algoritmus a hibát, és a hibát nem jelentő kombinációját további adott idő alatt szükség. Az ilyen adatpontok alapján, az algoritmus megtanulja előre jelezni, hány további időegységekkel a gép folytathatja a munkát, mielőtt sikertelen.

#### <a name="static-feature-data"></a>Statikus szolgáltatás
Statikus funkciók a készülék metaadatait. Többek között a készülék típusa, a modell, a gyártott dátumát, indítsa el a szolgáltatás dátuma, a rendszer, és egyéb technikai specifikációk helyét.

Példák vonatkozó adatokat a [minta PdM használati esetekben](#Sample-PdM-use-cases) listáját az alábbi táblázatban:

| Használati eset | Példák vonatkozó adatok |
|:---------|---------------------------|
|_Repülési késleltetés és a sikertelen_ | Útvonal-információkat repülési repülési ágak és a lap naplókat. Repülési engedélyezi adatok tartalmazza a útválasztási indító/érkezési dátum, idő, repülőtéri, layovers stb. Lap napló rögzíti az alapoktól karbantartási személyzet hiba és karbantartási kódok sorozata tartalmazza.|
|_Repülőgép motor részek hiba_ | Adatok összegyűjtése az érzékelők a repülőgép információval szolgálnak a különböző részeinek feltétellel. Karbantartási rekordok összetevő hibák történtek, és azok helyettesítve azonosításához.|
|_ATM hiba_ | Minden tranzakció (cash/ellenőrzés helyezésekor) érzékelő értékek és cash kiadása. Információk résnek mérés megjegyzések között, vegye figyelembe a vastagsága, vegye figyelembe a érkezési távolság, ellenőrizze attribútumok stb. Karbantartási azt jelzi, hogy adja meg a hibakódok, a javítási információkat, és a fizetési adagoló utoljára újratöltése esetén.|
|_Szél turbinás hiba_ | Az érzékelők figyelése turbinás körülmények például hőmérséklet, szél irányát, energia, generátor sebesség stb. A különböző régiókban található szél farmok több szél turbinák adatokat gyűjtött. Minden turbinás rendszerint kell több érzékelő értékek mérések továbbítását rögzített időközönként.|
|_Áramköri megszakító hibák_ | Karbantartási naplófájlokat, amelyek megoldani a problémát, megelőző és rendszeres műveletek közé tartozik. Működési adatok, amely tartalmazza a kapcsolatcsoport szóhatároló, mint a Megnyitás és Bezárás műveletek küldött automatikus és manuális parancsokat. Eszköz metaadatait, például a dátum előállítása, a hely, a modell, stb. Áramköri megszakító specifikációk például feszültség szintek, a földrajzi hely, a környezeti feltételek.|
|_Foglalhatja ajtó hibák_| A metaadatok foglalhatja például típusát foglalhatja, gyártott dátum, karbantartás gyakorisága, épület típus, és így tovább. Működési adatok például ajtó ciklusok, átlagos ajtajának bezárása idő száma. Az okok előzmények hiba.|
|_Kerék hibák_ | A érzékelőadatait, hogy intézkedések törlés gyorsulás, fékezési példányok, vezetői távolság, sebesség stb. Statikus adatait a kerekeket, például a gyártó, gyártott dátum. Adatai részét adatbázisból következtetni rendelési és a mennyiségek nyomon.|
|_Subway vonat ajtó hibák_ | Ajtó nyitó és záró idő szerint, egyéb működési adatok, például vonat ajtók az aktuális állapot. Statikus adatok eszköz azonosítója, időpont és feltétel értéke oszlopot tartalmazhat.|

### <a name="data-types"></a>Adattípusok
A fenti adatforrások megadott, a két fő adattípusok megfigyelt PdM a tartományban a következők:

- _A historikus adatok_: működési telemetriai adatokat, a gép feltételek, a munkahelyi rendelés típusok, prioritású kód, amely a rögzítés időpontjában fog rendelkezni az időbélyegeket. Hiba, a karbantartási/repair és a használati előzmények is minden eseményhez tartozó időbélyegzőket.
- _Statikus adatok_: gép operátor szolgáltatásokkal és általában statikusak óta leírják a műszaki adatok a gépeket vagy operátor attribútumok. Ezek a funkciók módosulhatnak adott idő alatt, ha van továbbá a hozzájuk kapcsolódó időbélyegzőket.

Előrejelzőjének és a cél változók csal Előfeldolgozott/átalakítja az [numerikus kategorikus és más adattípusok](https://www.statsdirect.com/help/basics/measurement_scales.htm) attól függően, hogy a használt algoritmus.

### <a name="data-preprocessing"></a>Adatok előfeldolgozása
Hogy előfeltételeként _jellemzőkiemelés_, készítse elő a különböző adatfolyamok sikerült létrehozni a séma, ahonnan könnyen összeállíthat szolgáltatások adatait. Az adatok megjelenítése először a rekordok táblázatként. A tábla minden sorára egy képzési példányát képviselik, és az oszlop felel meg a _előrejelzőjének_ (más néven független attribútumok változók) szolgáltatásokat. Az adatok rendezése úgy, hogy az utolsó oszlop(ok) a _cél_ (függő változó). Képzési feltünteti hozzárendelése egy _címke_ ebben az oszlopban értékeként.

A historikus adatok időegységek érzékelőadatait időtartama felosztani. Minden rekordot kell tartoznia, az adott eszköz számára egy időegységet _és kínáljon különböző információkat_. Időegységek Többszörösök másodperc, perc, óra, nap, az üzleti igények alapján vannak definiálva, hónap, és így tovább. Időegység _nem feltétlenül kell egyeznie a használatra vonatkozó adatok gyűjtésének gyakorisága_. A gyakoriság értéke magas, ha az adatok nem lehetséges, hogy megjelenítése bármely jelentős különbség az egyik a másikra. Tegyük fel például, hogy környezeti hőmérséklet gyűjtötte a program minden 10 másodperc. További információk biztosítása nélkül csak használja ugyanazt az intervallum betanítási adatok inflates példák száma. Ebben az esetben a jobb stratégia lehet használni az adatok több mint 10 percig, illetve egy órával az üzleti indoklásának alapuló.

A statikus adatok
- _Karbantartási rekordok_: nyers karbantartási egy eszköz azonosítója és a Timestamp típusú karbantartási adott időben egy adott időpontban végzett tevékenységeket a adatokkal rendelkezik. Átalakítás karbantartási tevékenységek _kategorikus_ oszlopok, ahol minden kategória leíró egyedi van leképezve a megadott karbantartási művelet. A séma karbantartási rekordok tartalmazhat eszköz azonosítója, az idő és a karbantartási műveletet.

- _Hiba a rekordok_: hibák vagy a hiba oka adott hibakódok vagy adott üzleti feltételek által meghatározott hibaesemények lehet rögzíteni. Azokban az esetekben, ahol a készülék már több hibakódok a tartomány szakértői kell azonosításához, amelyek a célváltozó megfelelő megfelelően. A fennmaradó hibakódok vagy feltételek használatával hozható létre _előrejelzőjének_ funkciókat, amelyek ilyen hibához függ. A sémát a sikertelen műveletet tartalmazhat eszköz azonosítója, az idő, a hiba vagy a hiba okát is megadva - ha rendelkezésre áll.

- _Számítógép és az operátorral metaadatok_: a számítógép és az operátorral adatok egyesítése egy séma rendelhet hozzá eszközt az operátorral együtt annak attribútumai. A séma, a gép feltételeket eszköz azonosítója, eszköz, operátor azonosító operátor szolgáltatásokkal és tartalmazhat.

Más adatok előfeldolgozása lépések közé tartoznak a _hiányzó értékek kezelése_ és _normalizálási_ az attribútumértékek. Részletes túlmutat a jelen útmutató – bizonyos hasznos hivatkozásokat a következő szakaszban talál.

A fenti rendelkező Előfeldolgozott adatforrások helyen, a végső átalakítása előtt a szolgáltatás mérnöki csapathoz, hogy a fenti táblák időbélyeg és eszköz azonosítója alapján. Az eredményül kapott tábla kellene hiba oszlop null értékeket, a normál működés számítógép esetén. A null értékeket is lehet imputált történő normál működéséhez. A hiba oszlop használatával létrehozhat _címkék a prediktív modell_. További információkért lásd [modellezési módszereket prediktív karbantartási](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Jellemzőkiemelés
A szolgáltatás az első lépés előtt az adatok modellezését szó. A szerepkör az adatok tudományos folyamatban [itt leírt](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _szolgáltatás_ prediktív attribútumot a modell - például hőmérséklet, nyomás, vibráció és így tovább. PdM szolgáltatás mérnöki csapathoz foglal magában, absztrakt módon megjelenítve a számítógép állapotának méretezhető időtartama alatt gyűjtött előzményadatokat keresztül. Ebben az értelemben nem egyezik, például a távoli megfigyelési, anomáliadetektálás és tárhelyhiba-észlelés a társaktól. 

### <a name="time-windows"></a>Windows indításakor
Az eseményeket a jelentés tartalmazza az elvégzendő figyelési távoli _között eltelt időt_. Anomáliadetektálási észlelési modellek időben bejövő adatfolyamok (pontszám) jelző rendellenességeket frissítésétől pontok adatok kiértékeléséhez. Tárhelyhiba-észlelés kell lennie az adott típusok pontok ideje a előforduló hibák osztályozza. Ezzel szemben PdM magában foglalja a hibák alatt becslése egy _későbbi időszakra_funkciókat, amelyek megfelelnek a gépek működését keresztül alapján _korábbi időszakra vonatkozó_. PdM, a szolgáltatás egyes pontokról idő esetén túl zajos prediktív kell. Így az adatok az egyes szolgáltatásokhoz kell lennie _smoothened_ adatpontok összesítésével idő windows keresztül.

### <a name="lag-features"></a>Lag szolgáltatások
Az üzleti követelmények határozza meg, milyen távolságban a modellnek az elkövetkező előre jelezni. Ennek az időtartamnak segítségével határozza meg, "milyen távolságban vissza a modell már meg" ezek előrejelzéseket készítsen a. A "keresése vissza" időszak neve a _lag_, és a szolgáltatások fejthetők vissza a késés időszakban nevezzük _szolgáltatások lag_. Ez a szakasz ismerteti, amelyek lag funkciókra adatforrásokból az időbélyegeket és a szolgáltatás létrehozása statikus adatforrásokból lehet létrehozni. Lag funkciók jellemzően _numerikus_ jellegűek.

> [!IMPORTANT]
> Az ablak mérete kísérletezhet határozzák meg, és kell véglegesítésre szakértői tartomány segítségével. Azonos szerint tárolja a kijelölés és lag szolgáltatások, az összesítések és windows típusú definíció.

#### <a name="rolling-aggregates"></a>Működés közbeni összesítések
Az egyes rekordokhoz, egy eszköz a működés közbeni ablak méretének "W" választja időegységekkel száma az összesítések kiszámításához. Lag szolgáltatások majd kiszámítása a W időszakok _időpontja előtt_ , hogy a rekord. 1. ábrán kék vonalak megjelenítése érzékelő értékeket az eszközhöz tartozó minden időegység rögzítve. A működés közbeni szolgáltatás értékek átlaga keresztül egy ablak méretének W = 3 jelöl azokat. T tartományában időbélyegek az összes rekordot keresztül számított mozgóátlaga<sub>1</sub> (narancs) a t<sub>2</sub> (a zöld). A W értéke általában percben vagy az adatok természetétől függően óra. De bizonyos problémák nagy W (azaz 12 hónap) kiadási biztosíthat az eszköz teljes előzményeinek a rekord a időpontig.

![1. ábra Működés közbeni összesített szolgáltatások](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) 1. ábra. Működés közbeni összesített szolgáltatások

Működés közbeni összesítések egy olyan időkeretet keresztül többek között a száma, átlagos, CUMESUM (halmozott összege) intézkedések, minimális és maximális értékeket. Ezenkívül variancia, szórás és száma túl N szórás szorzataként kiugró gyakran használják. Az alkalmazható összesítések példái a [eseteinek](#Sample-PdM-use-cases) a jelen útmutató az alábbiakban találhatók. 
- _Késleltetés repülési_: hibakódok keresztül az elmúlt nap vagy hét számát.
- _Repülőgép motor része hiba_: működés közbeni azt jelenti, szórás és sum az elmúlt nap, hét stb. Ez a mérőszám a vállalati tartomány szakértői együtt kell meghatározni.
- _ATM hibák_: működés közbeni azt jelenti, középérték, tartomány, szórás szorzataként, három szórás szorzataként, a felső és alsó CUMESUM túl kiugró száma.
- _Subway vonat ajtó hibák_: elmúlt nap során, hetente, kéthetente stb események száma.
- _Áramköri megszakító hibák_: hiba száma keresztül múlt héten, év, három év stb.

A PdM egy másik hasznos módszer akkor rögzítheti a trend módosítások, a teljesítményt és a algoritmusokkal, amely a rendellenességek észlelését adatok megváltozik.

#### <a name="tumbling-aggregates"></a>Átfedésmentes összesítések
Az egyes feliratú rekord egy eszköz, egy ablak méretének _W -<sub>k</sub>_  van definiálva, ahol _k_ méretű windows száma _W_. Összesítések létrehozása keresztül _k_ _átfedésmentes windows_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  előtt egy olyan rekordot időbélyeg időszakokra. _k_ rögzítheti a rövid távú hatások kevés vagy hosszú távú teljesítménycsökkenést minták rögzítéséhez sok lehet. (lásd a 2. ábra).

![2. ábra Összesített szolgáltatások átfedésmentes](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) 2. ábra. Összesített szolgáltatások átfedésmentes

Például lag szolgáltatások, a szél turbinák használati eset előfordulhat, hogy létrehozza és W = 1 k = 3. Az egyes felső és alsó kiugró használatával az elmúlt három hónap lag azt tartalmazzák.

### <a name="static-features"></a>Statikus szolgáltatások

Műszaki adatok előállítása, a modell számának, a helyre, például a dátum a készülék példák statikus szolgáltatásokat. Akkor számít _kategorikus_ modellezési változókat. Néhány példa a áramköri megszakító használati eset: feszültség, aktuális, energiafogyasztási kapacitás, típusú átalakító, és áramforrásról. Kerék hibák kulcsszava kerekek (acélötvözet vs) típusú csak példaként szolgál.

Az eddig tárgyalt adatok előkészítése erőfeszítéseket a folyamatban az alább látható módon vannak rendszerezve data vezethetnek. Képzési, tesztelése és érvényesítési adatok a logikai séma (Ez a példa bemutatja idő nap egységben) kell rendelkeznie.

| Eszközazonosító | Time | <Feature Columns> | Címke |
| ---- | ---- | --- | --- |
| A123 |Naponta 1 | . . . | . |
| A123 |2 nap | . . . | . |
| ...  |...   | . . . | . |
| B234 |Naponta 1 | . . . | . |
| B234 |2 nap | . . . | . |
| ...  |...   | . . . | . |

A szolgáltatás műszaki osztály utolsó lépése a **címkézés** a cél változó. A folyamat be nem függ a modellezési módszer. Viszont a modellezési technika az üzleti probléma és a rendelkezésre álló adatok jellegétől függ. A következő szakaszban tárgyalt címkézését.

> [!IMPORTANT]
> Adatok előkészítése, a szolgáltatás fejlesztés olyan fontos, mint a modellezési technikák sikerül sikeres PdM megoldást találni. A tartomány szakértő és a gyakorló kell fektetnek jelentős mennyiségű időt a megfelelő szolgáltatásokat és a modell adatait. A szolgáltatás műszaki osztály sok könyvek egy kis mintát az alábbiak:
> - Pyle, D. adatok előkészítése az adatbányászati (az Morgan Kaufmann számozási adatok felügyeleti rendszerekhez), 1999
> - Zheng, A., Casari A. szolgáltatás mérnöki csapathoz, a Machine Learning szolgáltatáshoz: elvek és Adatszakértőkön, O'Reilly, 2018 technikákat.
> - Dong, G. Liu H. A szolgáltatás (szerkesztőt), műszaki osztály gépi tanulási és adatelemzés (Chapman & Hall/CRC adatbányászat és Tudásbázis felderítési adatsorozat), a CRC nyomja le az ENTER 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>A prediktív karbantartási modellezési módszereket

Ez a szakasz ismerteti a fő modellezési módszereket, a PdM problémákat, az adott címke konstrukció módszerek együtt. Figyelje meg, hogy különböző iparágak egyetlen modellezési módszer segítségével. A modellezési technika az tudományos problémára, mint az adatok az elvégzendő keretében párosítva van.

> [!IMPORTANT]
> Címkék hiba esetekben és a szalagcímkézési stratégia kiválasztása  
> a tartomány szakértői egyeztetve kell meghatározni.

### <a name="binary-classification"></a>bináris osztályozás
Bináris osztályozási szolgál _annak, hogy berendezés jövőbeli időn belül nem sikerül előrejelzése_ - hívott a _jövőbeli horizon időszak X_. X határozza meg az üzleti problémát és az adatok kezelésére, a tartomány szakértői egyeztetve. Például a következők:
- _Minimális átfutási idő_ szükséges összetevők használ, karbantartási erőforrásokat, a karbantartás egy, valószínűleg az adott időköz során előforduló problémák elkerülése érdekében.
- _események minimális száma_ , amely akkor fordulhat elő, mielőtt a probléma akkor fordul elő.

Ezzel a módszerrel a példák két típusú jelöli. Egy pozitív példában _hiba azt jelzi, amely_, címke = 1. A negatív példa, amely megadja, hogy a normál működés, címkével = 0. A célváltozó, és ezért a címke értéke, _kategorikus_. A modell kell azonosítania minden egyes új példa, sikertelen vagy általában a következő X időegységek működnek.

#### <a name="label-construction-for-binary-classification"></a>Bináris besorolási címke létrehozása
Itt a kérdés: "Mi az a valószínűsége annak, hogy az eszköz meghiúsul a következő X időegységekkel?" Ezt a kérdést, Címke X rekordok "készül sikertelen" az eszköz a meghibásodás előtt válaszolnia (címke = 1), és minden egyéb rögzíti, hogy a "szokásos" címke (címke = 0). (lásd a 3. ábra).

![3. ábra A bináris osztályozási címkézés](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) 3. ábra. A bináris osztályozási címkézés

Stratégia címkézését a használati esetek néhány példát alább láthatók.
- _Késések repülési_: X 1 nap, akkor választható, előre jelezni késések 24 órán belül. Majd a hibák előtt 24 órán belül minden repülőútra feliratú 1.
- _ATM cash eredményeként hibák_: lehet, hogy A célja meghatározni a következő egy óra tranzakció hibája valószínűségét. Ebben az esetben, és ismételje meg a hiba az elmúlt egy órában belül minden tranzakciók vannak címkézve 1. Hiba valószínűség előre a következő N pénznem keresztül az mellőzni, megjegyzések belül, ha nem az utolsó N megjegyzések mellőzni összes megjegyzések 1 címkével.
- _Áramköri megszakító hibák_: lehet, hogy a cél a következő áramköri megszakító parancshiba előre jelezni. Ebben az esetben X kell lennie egy jövőbeli parancs van kiválasztva.
- _Ajtó hibák betanítása_: X akkor választható, mint két nap.
- _Turbinás hibák szél_: X akkor választható, mint két hónapig.

### <a name="regression-for-predictive-maintenance"></a>A prediktív karbantartási regressziós
Regressziós modell használt _a fennmaradó élettartama (Szabályainak) egy eszköz számítási_. Szabályainak az, hogy mennyi ideig előtt a következő hiba történik az eszköz működőképességét típusúként van definiálva. Minden egyes képzési példa: egy rekord, amely egy időegységet tartozik _nY_ eszköz, ahol _n_ a többszöröse. A modell számítható ki, minden új példában Szabályainak egy _folyamatos szám_. Ez a szám azt jelzi, hogy a hiba előtt hátralévő idő: időtartama.

#### <a name="label-construction-for-regression"></a>Címke konstrukció visszavonták
Itt a kérdés: "Mi az hasznos élettartama (Szabályainak) a készülék?" Az egyes rekordokhoz a meghibásodás előtt kiszámíthatja a címke a következő hiba hátralévő idő egységek száma. Ezzel a módszerrel a feliratai folyamatos változók. (Lásd a 4. ábra:)

![4. ábra A regressziós címkézés](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) 4. ábra. A regresszió címkézés

Az regressziós címkézés végezhető el a hiba pont alapján. A számítási nincs lehetőség anélkül, hogy tudnák, mennyi ideig az eszköz rendelkezik megélték a hiba előtt. Így ezzel szemben a bináris osztályozási eszközök esetleges hibákat, az adatok nélkül nem használható modellezési. A legjobb kérdését egy másik statisztikai technikával nevű [túlélési Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Azonban lehetséges komplikációk lépnek fel ezzel a módszerrel, például az idő-változó adatok gyakorisággal PdM használati esetek alkalmazásakor. Túlélési elemzési további információkért lásd: [ezen egy-személyhívó](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>A prediktív karbantartási besorolást több osztály
Több osztály besorolás technikák is használható két olyan eset a PdM megoldások:
- Előre jelezni _két jövőbeli eredményt_: első eredménye _sikertelenségét időtartományt_ az adott eszköz számára. Az eszköz van rendelve egy több lehetséges idő alatt. A második eredménye a hibák valószínűségét oka az, hogy egy jövőbeli időszakban _hatására a több legfelső szintű egyik_. Az előrejelzés lehetővé teszi, hogy a karbantartási személyzet figyelendő tünetei és terv ütemtervük.
- Előre jelezni _a legvalószínűbb okának_ egy adott hiba. Az eredmény azt javasolja, hogy a hiba elhárításához karbantartási műveletek megfelelő halmazát. Kiváltó okokért és az ajánlott javítási rangsorolt listáját segítségével a technikusok azok javítási műveletek rangsorolhatja meghibásodás után.

#### <a name="label-construction-for-multi-class-classification"></a>Több osztály besorolási címke létrehozása
Itt a kérdés: "Mi az a valószínűsége annak, hogy egy eszköz meghiúsul a következő _nZ_ időegységekkel ahol _n_ időszakok száma?" Ez a kérdés megválaszolásához címke nZ rögzíti egy eszköz gyűjtő idő (3Z, 2Z, Z) használatával a meghibásodás előtt. Minden más címke "normál" rögzíti (címke = 0). Ezzel a módszerrel a célváltozó rendelkezik _kategorikus_ értékeket. (Lásd az 5. ábra).

![5. ábra Idő előrejelzés multiclass besorolási címkék](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) 5. ábra. Idő előrejelzés több osztály besorolási címkék

Itt a kérdés: "Mi az a valószínűsége annak, hogy az eszköz meghiúsul a következő alapvető ok/probléma miatt időegységekkel X _P<sub>i</sub>_?" Ha _i_ kiválthatja száma. Ezt a kérdést, Címke X rögzíti az eszköz a meghibásodás előtt válaszolnia "hamarosan alapvető ok miatt sikertelenek _P<sub>i</sub>_" (címke = _P<sub>i</sub>_). Minden más rögzíti, hogy a "szokásos" címke (címke = 0). A metódusban feliratainak is kategorikus (lásd a 6. ábrát).

![6. ábra Legfelső szintű OK előrejelzés multiclass besorolási címkék](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) 6. ábra. A legfelső szintű OK előrejelzés több osztály besorolás címkézés

A modell hozzárendel miatt minden probléma valószínűség _P<sub>i</sub>_  és hibák nélkül valószínűségét. Ezeket a valószínűség nagyságrendet is rendezett engedélyezi a problémák, amelyek fordulhat elő, a jövőben előrejelzését.

Itt a kérdés: "karbantartási műveleteket tegye javasolnak meghibásodás után?" Ez a kérdés megválaszolásához címkézés _nem szükséges egy jövőbeli horizon ki kell_, mert a modell a jövőben nem becslése sikertelen. Az imént becslése a legvalószínűbb okának _után a hiba már elvégzett_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Képzési, érvényesítése és a vizsgálati prediktív karbantartás
A [Team adatok tudományos folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biztosít a modell train-teszt-érvényesítése ciklusban teljes körét. Ez a szakasz ismerteti a PdM egyedi szempontokat.

### <a name="cross-validation"></a>Kereszt-ellenőrzési
Az adatdeduplikálás [kereszt-ellenőrzési](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) határozza meg a "teszt" a modell a tanítási fázisban az adatkészletet. Ezen adatok nevezzük a _érvényesítési set_. A technika megkönnyíti korlát problémák, mint például _overfitting_ és lehetőséget ad meg, hogyan fogja generalize a modell egy független adatkészletben egyet. Ez azt jelenti, hogy egy ismeretlen beállítása, amely a valós probléma lehet. A PdM a képzés és tesztelési rutin kell figyelembe kell venni a idő különböző szempontokat is jobban generalize nem látható jövőbeli adatokon.

Sok gépi tanulási algoritmusok hiperparamétereket, amely jelentősen módosíthatja a modell teljesítmény számos függ. Vannak ezen hiperparamétereket optimális értékét a modell betanításakor nem automatikusan számítja. Az adatok tudósok kell megadni. Számos módon, hogy a rendszer hiperparamétereket helyes értékeket.

A leggyakoribb még _k hajtott kereszt-ellenőrzési_ , amely felosztja a véletlenszerűen be példákat _k_ modellrészt. Minden egyes értékhalmazt hiperparamétereket, futtassa az alábbi parancsot a tanulási algoritmus _k_ alkalommal. Minden egyes ismétlés használata a példák az aktuális modellrészek az érvényesítés, és a többi példák mint képzési készletként. Az algoritmus betanítása példák keresztül, és a metrikák számítási érvényesítési példák keresztül. Ez a ciklus végén számítási átlaga _k_ teljesítménymutatók. Az egyes hyperparameter értékek válassza ki a legjobb átlagos teljesítmény melyeket. Hiperparamétereket kiválaszthatják legtöbbször kísérleti jellegűek.

A PdM problémák adatok ügyfélként, amely az adatok több forrásból származó események idősor. Ezeket a rekordokat a címkézés időpontja szerint rendezhető. Ezért ha a dataset van szétosztva _véletlenszerűen_ képzési és -ellenőrzés be van állítva, a _néhány a példák mint érvényesítési példák némelyike időben későbbi lehet_. Néhány érkező adatok alapján hyperparameter értékek jövőbeli teljesítmény fog becsült _előtt_ modell betanítása volt. Ezek becsléseket túlságosan optimista, lehet, különösen akkor, ha az idősor nincs helyhez kötött és idővel fejlődésének. Emiatt a kiválasztott hyperparameter értékek optimálisnál lehet.

Az ajánlott módja a példák felosztása tanítási és a beállított érvényességi egy _időfüggő_ módon, ahol az összes érvényesítési többek között az összes példák mint időben későbbi. Az egyes hyperparameter értékek betanítását az algoritmus a tanítási adathalmazt keresztül. A modell teljesítmény mérésére érvényesítési ugyanazokat keresztül. A legjobb teljesítményt megjelenítő hyperparameter értékeinek kiválasztása A kereszt-ellenőrzési által véletlenszerűen választott értékekkel jövőbeli modell jobb teljesítményt eredményez tanítási és érvényesítés felosztás által választott Hyperparameter értékeket.

A végső modell betanítása tanulási algoritmus a legjobb hyperparameter értékek teljes betanítási adatok által hozhatók létre.

### <a name="testing-for-model-performance"></a>A modell teljesítmény tesztelése
Miután egy modelljei épülnek, az új adatok a jövőbeni teljesítményét becsült szükség. Egy jó a teljesítmény metrika hyperparameter értékek kiszámított az érvényesítési készleten, vagy egy átlagos teljesítmény metrika történik a kereszt-ellenőrzési becslése. Ezek becsléseket általában túlságosan optimista. Az üzleti gyakran előfordulhat néhány további iránymutatás hogyan szeretnék tesztelni a modellt.

Az ajánlott módszer a PdM, hogy a példák felosztása képzési, érvényesítés, és tesztadatok állít be egy _időfüggő_ módon. Minden teszt példák a képzés és érvényesítési példák időben későbbinek kell lennie. A felosztás után a modell generálásához, és mérheti a teljesítményét a fentebb leírt módon.

Ha idősorozat helyhez kötött és könnyen előre jelezni, véletlenszerű és időfüggő megközelítések jövőbeli teljesítmény hasonló becsléseket hoz létre. De ha idősorozat nem helyhez kötött és/vagy nehezen becsülhető, az időfüggő módszerrel hoz létre a jövőbeli teljesítmény modell becslése.

### <a name="time-dependent-split"></a>Időfüggő felosztása
Ez a szakasz ismerteti az időfüggő vegyes megvalósításához ajánlott eljárások. Egy időfüggő kétirányú felosztása tanítási és tesztelési között az alábbiakban.

Tegyük fel például különböző érzékelők mérték időbélyegzővel események adatfolyam. Adja meg a szolgáltatások és a feliratok tanítási és tesztelési példák több eseményeket tartalmazó időkeret keresztül. Például bináris osztályozási múltbeli események alapján funkciók létrehozása, és a jövőbeni események típuson belüli "X" a jövőben időegységekkel alapján címkék létrehozása (lásd a a [jellemzőkiemelés](#Feature-engineering) és [modellezési technikák](#Modeling-techniques-applied-to-PdM-use-cases)). Így például szalagcímkézési időkerete szolgáltatása időkeretet később származnak.

Időfüggő valószínűségét, válasszon egy _levágási idő T betanítása<sub>c</sub>_  , ahol a modell betanításához a beállított, legfeljebb T előzményadatok használatával hiperparamétereket<sub>c</sub>. Jövőbeli címkék, amelyekre nem térnek T kiszivárgásának elkerülésére<sub>c</sub> azokat a betanítási adatok, válassza ki a címke példák X kell legújabb idő előtt T egységek<sub>c</sub>. A 7. ábrán látható példát minden négyzet jelenti. a rekord a következő adatkészletben, ahol szolgáltatásait és címkék vannak számított fent leírt módon. Az ábrán látható, az azt jelzi, hogy a képzés és egy tesztelési X = 2 és W = 3 kell kísérhet:

![7. ábra Ossza fel a bináris osztályozási időfüggő](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) 7. ábra. Ossza fel a bináris osztályozási időfüggő

A zöld négyzetes határoz meg a képzés használható mértékegységét tartozó bejegyzéseket. Minden egyes képzési példa generálja figyelembe véve az elmúlt három szolgáltatás létrehozásához, és a két jövőbeli időszak előtt T címkézési<sub>c</sub>. Ha a két jövőbeli időszakok bármely részét T túl van<sub>c</sub>, adott példa kizárása a tanítási adathalmazt, mert nem látható feltételezett túl T<sub>c</sub>.

A fekete négyzetes határoz meg a rekordokat a végső címkézett adatkészlet, amelyeket nem lehet felhasználni a tanítási adathalmazt, megadva a fenti korlátozás. Ezeket a rekordokat nem lesz is alá vonni adatok, mert azok előtt T<sub>c</sub>. Emellett a szalagcímkézési időkeret részben függ a képzés időtartományban, ideális, amelyek nem. Tanítási és tesztelési adatokat kell külön szalagcímkézési időkeret címke információk adatszivárgás megelőzése érdekében.

Az eddig tárgyalt módszer lehetővé teszi, hogy a modell betanítására és tesztelésére példák, amelyek közel T időbélyegeket közötti átfedés<sub>c</sub>. Példák belüli W mértékegységét t kizárandó egy megoldást, a nagyobb elkülönítése<sub>c</sub> a vizsgálat beállítása. De ilyen egy agresszív vegyes attól függ, hogy adatok rendelkezésre állását.

Szabályainak előrejelzésére használt regressziós modell több súlyosan érinti a kiszivárgásának probléma. A véletlenszerű vegyes metódussal szélsőséges túlzott méretezés vezet. Regressziós problémák, a felosztás kell úgy, hogy a rekordok előtt T hibákkal rendelkező eszközök tartozó<sub>c</sub> lépjen be a gyakorlókészlethez. A megszakítási után hibákkal rendelkező eszközök rögzíti a TesztKészlet kísérhet.

Esetén a modell betanítására és tesztelésére adatai azonos egy másik ajánlott eljárás, hogy használja a felosztás által eszköz azonosítója. A felosztás kell lennie, az eszközök a gyakorlókészlethez egyike sem szerepel a modell teljesítményének tesztelése. Ezzel a megközelítéssel a modellnek van egy új eszközök biztosítható a modell eredmények jobb esélyét.

### <a name="handling-imbalanced-data"></a>Imbalanced adatok kezelése
A besorolási problémák, ha nincsenek további példákat a többi, mint egy osztály az adatkészlet, különállónak _imbalanced_. Ideális esetben minden egyes osztály a betanítási adatok elég képviselőinek előnyben részesített ahhoz, hogy más osztályok megkülönböztetését. Ha egy osztály 10 %-nál kisebb az adatokat, az adatok imbalanced kell tekinteni. A underrepresented osztály egy _kisebbségi osztály_.

Sok PdM problémák szembesülhetnek ilyen imbalanced adatkészleteket, ha egy osztály súlyosan underrepresented a más osztály vagy osztályok képest. Bizonyos esetekben a kisebbségi osztály jelenthet a teljes adatpontok csak 0,001 %. Nincs egyedi PdM osztály egyensúlyhiány. Más tartományokban, amelyben hibák és rendellenességeket is ritka előfordulások szembesülhetnek hasonló problémát, csalások felderítéséhez és hálózati behatolás példákat. Ezek a hibák jött létre a kisebbségi osztály példák.

Osztály egyensúlyhiány adatok, a legtöbb szabványos tanulási algoritmusok teljesítményének biztonsága sérül, mivel minimalizálása érdekében a teljes Hibaarány irányulnak. Az adatkészlet 99 % negatív és 1 % pozitív példák egy modell által negatív példányait címkék 99 % pontossága hozzá jeleníthető meg. De a modell nem sorolja be az összes pozitív példák; így akkor is, ha az adatok pontosságát magas, az algoritmus nem hasznos egy. Következésképpen hagyományos értékelési-metrikák többek között _Hibaarány általános pontosságának_ imbalanced learning nem elégségesek. Imbalanced adatkészletek szemben, más metrikákkal modell kiértékelése használják:
- Pontosság
- Visszahívás
- F1 pontszámok
- Költség igazítva ROC (fogadó működési jellemzőit)

A metrikák kapcsolatos további információkért lásd: [modell kiértékelése](#Model-evaluation).

Van azonban néhány módszereket, amelyek segítenek a orvoslása érdekében osztály egyensúlyhiány probléma. A két fő részesítse _mintavételi technikák_ és _költség-és nagybetűket tanulási_.

#### <a name="sampling-methods"></a>Mintavételi
Imbalanced tanulási magában foglalja a mintavételi módosítani a tanítási adathalmazt, hogy egy elosztott terhelésű készlet módszerek használatát. Mintavételi módszereket nem alkalmazható a tesztelési készlethez. Habár számos mintavételi módszert, vannak-e a legtöbb nagyon egyszerű néhányat a meglévők közül _véletlenszerű oversampling_ és _alatt mintavételi_.

_Véletlenszerű oversampling_ magában foglalja a kiválasztása véletlenszerűen kisebbségi osztályból származik, ezek a példák replikálásának és tanítási adathalmazt hozzáadását. Következésképpen kisebbségi osztály szereplő példák száma nő, és végül elosztása a különböző osztályokhoz példák száma. Oversampling hátránya, hogy egyes példák több példánya okozhat a osztályozó túl adott, ami túlzott méretezés válik. A modell előfordulhat, hogy megjelenítése nagy képzési pontosságának, de előfordulhat, hogy a teljesítményét, a nem látható tesztadatokat optimálisnál.

Ezzel ellentétben _véletlenszerű alatt mintavételi_ kiválasztása véletlenszerűen többsége osztályból, és ezek a példák eltávolítása a tanítási adathalmazt. Azonban példák eltávolítása a legtöbb osztály okozhat a teljesíti a legtöbb osztály kapcsolatos fontosabb fogalmakra osztályozó. _Hibrid mintavételi_ ahol kisebbségi osztály túlzott mintavételi és többsége osztály alatt-mintát ugyanazon belül-e egy másik életképes megközelítést.

Nincsenek számos kifinomult mintavételi technikákat. A választott módszerrel adatok tulajdonságok és ismétlődő kísérletek által az adatok tudósok eredmények függ.

#### <a name="cost-sensitive-learning"></a>Bizalmas tanulási költsége
A PdM a kisebbségi osztály alkotó hibák az érdekesebb normál példák mint vannak. Ezért a elsősorban főként a sikertelen végrehajtása esetén az algoritmus teljesítményét. Egy pozitív forrásosztállyal egy negatív helytelenül előrejelzésére is drágább, mint fordítva. Ez a helyzet gyakran hivatkozik, amely egyenlőtlen elvesztése vagy téves sorolásához elemek aszimmetrikus költségét különböző osztályú. Az ideális osztályozó továbbítsa magas előrejelzés pontosságát keresztül kisebbségi osztály a legtöbb a(z) pontosságára veszélyeztetése nélkül.

Ez az egyenleg eléréséhez több módja van. Egyenlőtlen veszteség probléma mérséklése érdekében magas költségekkel hozzárendelése kisebbségi osztály hibás osztályozás, és próbálja meg minimálisra csökkenteni a teljes költség szempontjából. Algoritmusok, például _SVMs (támogatási vektoros gépek)_ fogadja el ezt a módszert eredendően, lehetővé téve a betanítás során meg kell adni a pozitív és negatív példák költségét. Hasonlóképpen, például az módszerek kiemelése _súlyozott döntési fák_ általában megjelenítése a megfelelő teljesítmény imbalanced adatokkal.

## <a name="model-evaluation"></a>Modell kiértékelése
Hibás osztályozás a PdM forgatókönyvekben, ahol üzleti téves riasztásokat költségét magas jelentős probléma. Például az és a föld motor hibát egy helytelen előrejelzését alapján repülőgép döntést ütemezések megzavarhatják és tervek változatlan marad. A gép offline állapotba a szerelvény véve a bevétel elvesztéséhez vezethet. Ezért fontos a megfelelő metrikák teszt új adatokkal szemben a modell kiértékelése.

Tipikus teljesítménymutatók PdM modellek értékeléséhez használt alább:

- [Pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision) a legnépszerűbb metrika egy osztályozó teljesítmény leíró használt. De pontossága érzékeny adatok azokat a terjesztéseket, és a imbalanced adatkészletek forgatókönyvek hatástalan intézkedés. Más metrikákkal használja helyette. Eszközök, például [zavart mátrix](https://en.wikipedia.org/wiki/Confusion_matrix) segítségével számítási, valamint arról a modell pontosságát az okból.
- [Pontosság](https://en.wikipedia.org/wiki/Precision_and_recall) PdM a modellek kapcsolódnak a téves riasztásokat aránya. A modell pontosságát alacsonyabb általában nagyobb mértékű téves riasztásokat felel meg.
- [Visszahívása](https://en.wikipedia.org/wiki/Precision_and_recall) arány azt jelzi, hogy hány a sikertelen teszt készletében sikerült helyesen azonosítani a modell. A nagyobb visszaírási sebesség jelenti a modell sikeres, az igaz a hibák azonosításához.
- [F1 pontszám](https://en.wikipedia.org/wiki/F1_score) pontosság és a visszaírási közötti 1 (ajánlott) (legrosszabb) 0 értékkel harmonikus átlaga.

A bináris besorolás
- [Fogadó görbék (ROC) működő](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) népszerű metrika is van. A ROC görbék modell teljesítmény értelmezi egy üzemi pontját a ROC alapján.
- A PdM problémákat, de _decile táblák_ és _diagramok növekedési_ , még informatívabbá. Csak az a pozitív osztály (hibák) összpontosít, és adja meg az algoritmus teljesítményét mint ROC görbék összetettebb képe.
  - _Decile táblák_ tesztelési példák használatát egy hiba valószínűség csökkenő sorrendben. A rendezett minták majd szerint vannak csoportosítva deciles (10 % a legnagyobb valószínűséggel minták majd 20 %, 30 %, és így tovább). Az egyes decile a arány (true pozitív gyakoriság) /(random baseline) segít, minden egyes decile algoritmus teljesítményének. A véletlenszerű alapterv veszi értékek 0,1, 0,2, és így tovább.
  - [Diagramok növekedési](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) decile igaz pozitív sebessége és a véletlenszerű igaz pozitív aránya az összes deciles ábrázolásához. Az első deciles rendszerint a fókusz eredmények, mivel azok megjelenítése, hogy a legnagyobb növekedését. Első deciles is látható, reprezentatív "veszélyben", a PdM a használatakor.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modell operationalization prediktív karbantartás

Az adatok tudományos folyamat előnyt is tudjuk, csak ha a betanított modell legyen működési. Ez azt jelenti, hogy a modell kell telepíteni az új, korábban nem látható adatok alapján előrejelzéseket készítsen üzleti rendszerekbe.  Az új adatok pontosan meg kell felelnie a _modell aláírás_ a betanított modell két módon:
- minden szolgáltatás minden logikai példányát (azaz a tábla sorának) az új adatok jelen kell lennie.
- az új adatokat előre fel kell dolgozni, és a szolgáltatások mindegyikének fejthetők vissza, a pontosan ugyanúgy, mint a betanítási adatok.

A fenti eljárás az sokféleképpen academic és iparági szakirodalomban van megadva. De a következő utasításokat ugyanazt jelenti:
- _Új adatok pontozása_ a modell segítségével
- _Alkalmazzák a modellt_ új adatokhoz
- _Azok_ a modell
- _Telepítése_ a modell
- _Futtassa a modell_ új adatokkal szemben

Ahogy korábban is hangsúlyoztuk, a PdM modell operationalization eltér a társaknak. Anomáliadetektálás és tárhelyhiba-észlelés általában érintő forgatókönyvek megvalósításához _online pontozási_ (más néven _valós idejű pontozási_). Itt, a modell _pontszámok_ minden bejövő rekord, és visszaadja az előrejelzéshez. Az előrejelzés anomáliadetektálás, arra utal, hogy történt-e az anomáliadetektálási esetén (Példa: egy osztály SVM). A tárhelyhiba-észlelés lenne a típus vagy a hiba az osztály.

Ezzel szemben, magában foglalja a PdM _kötegelt pontozási_. Felel meg a modell aláírás, az új adatok funkciók a azonos módon a betanítási adatok fejthetők vissza. Az új adatokat általában nagy adatkészletek esetében a funkciók windows idő alatt összesített értéket és kötegelt a program pontozza a mennyiségeket. Kötegelt pontozási általában történik például elosztott rendszerek [Spark](http://spark.apache.org/) vagy [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Többféle alternatívák - is az optimálisnál gyengébb:
- Adatfolyam-továbbítási adatok motorok memóriában windows keresztül támogatja a csoportosítást. Így sikerült állították, hogy támogatják az online pontozási. De ezek a rendszerek megfelelő-e, vagy a ritka elemek keskeny Windows sűrű adatok szélesebb windows keresztül. Azok előfordulhat, hogy nem méretezhető a sűrű adatok esetén hálózatokon szélesebb idő windows PdM forgatókönyvek látható módon.
- Ha a kötegelt pontozás nem érhető el, a megoldást támogató kis kötegekben új adatok kezelésének egyszerre online pontozási.

## <a name="solution-templates-for-predictive-maintenance"></a>A prediktív karbantartási megoldás sablonok

Ez az útmutató utolsó része PdM megoldás sablonok, oktatóanyagok és az Azure-ban végrehajtott kísérletekhez listáját jeleníti meg. Ezek az alkalmazások PdM is telepíthető az Azure-előfizetés bizonyos esetekben percen belül. A koncepció igazolása bemutatók, védőfalak alternatívák, vagy tényleges éles megvalósítások gyorsító kísérletezhet, is használhatók. Ezek a sablonok találhatók a [Azure Eszközintelligencia-katalógus](http://gallery.azure.ai) vagy [Azure GitHub](https://github.com/Azure). A következő különböző mintákat adott idő alatt a megoldás sablonba lesz állítva.

| # | Beosztás | Leírás |
|--:|:------|-------------|
| 1 | [Prediktív karbantartási Azure Machine Learning minta](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Hiba a következő N mértékegységét keresztül-előrejelzés PdM minta. Ez a minta Azure ML munkaterület projektként írása, és a PdM kezdőknek ideális. [További dokumentáció](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) Ez a minta kapcsolódik.|
| 2 | [Az Azure prediktív karbantartási megoldás sablon](https://github.com/Azure/AI-PredictiveMaintenance) | Egy teljes körű keretrendszer több PdM forgatókönyv bemutatásához. Ez a sablon mutatja be két olyan eset: az egyik új használati eset valós idejű hiba feltétel besorolás. A második forgatókönyv, egyszerűen egy integrálása a megoldás [1] Ez a megoldás sablon. Azt mutatja be más új vagy meglévő forgatókönyvek hozzáadása telepített ugyanazon az infrastruktúrán újból.|
| 3 | [Learning a prediktív karbantartási mély](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | A bemutató-megoldás LSTM (hosszú rövid távú memória) hálózatok (ismétlődő Neurális hálózatokat osztály) használatával prediktív karbantartási, az Azure Notebook egy [erre a példára által írt blogbejegyzés](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Az R prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Az r parancsfájlok PdM fenyegetésmodellezési útmutatója|
| 5 | [Űrtechnikai Azure prediktív karbantartás](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Az Azure ML v1.0 repülőgép karbantartás alapján első PdM megoldás sablonok egyikét. Ez az útmutató a projekt származik. |
| 6 | [Az IoT-Edge Azure AI eszköztára](https://github.com/Azure/ai-toolkit-iot-edge) | AI az az IoT-edge használatával TensorFlow; részletes eszközkészlet csomagok tanulási modellek Azure IoT Edge-kompatibilis Docker-tárolókban, és tegye elérhetővé a REST API-k, modellek.
| 7 | [Az Azure IoT – prediktív karbantartás](https://github.com/Azure/azure-iot-predictive-maintenance) | Az Azure IoT Suite számítógépek - előkonfigurált megoldás. Repülőgép karbantartási PdM sablon IoT Suite. [Egy másik dokumentum](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) és [forgatókönyv](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) kapcsolódó ugyanabban a projektben. |
| 8 | [Az SQL Server R Services prediktív karbantartási sablon](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Az R-szolgáltatásoknak fennmaradó élettartama forgatókönyvek bemutató. |
| 9 | [A prediktív karbantartási Fenyegetésmodellezési útmutatója](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Repülőgép dataset karbantartásának fejthetők vissza az R használatával [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) és [adatkészletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) és [Azure notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) és [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)az AzureML 1.0-s verzió|

## <a name="training-resources-for-predictive-maintenance"></a>A prediktív karbantartási oktatóanyagok

A [Azure AI képzési terv prediktív karbantartási](https://github.com/Azure/AI-PredictiveMaintenance/blob/master/docs/azure-ai-learning-path-for-predictive-maintenance.md) biztosít az oktatóanyag a bemutatják a fogalmakat és matematikai algoritmusok és technikák PdM problémák használt mögött. 

Microsoft Azure ingyenes tartalom és a képzési általános AI fogalmak és eljárások kínál.

| Képzési erőforrást  | Rendelkezésre állás |
|:-------------------|--------------|
| [AI Developer az Azure-on](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Nyilvános |
| [Microsoft AI iskolai](http://aischool.microsoft.com/learning-paths) | Nyilvános |
| [Az Azure AI tanulási a Githubról](http://azure.github.io/learnanalytics/public) | Nyilvános |
| [LinkedIn tanulás](http://www.linkedin.com/learning) | Nyilvános |
| [Microsoft AI Youtube előadások](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Nyilvános |
| [Microsoft AI megjelenítése](http://channel9.msdn.com/Shows/AI-Show) | Nyilvános |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Microsoft-partnerek számára |
| [A Microsoft Partner Network](http://learningportal.microsoft.com) | Microsoft-partnerek számára |

Emellett a AI szabad MOOCS (nyitott nagy online tanfolyamokat) is online felajánlott academic intézmények például Stanford és MIT, és az egyéb oktatási vállalatok.