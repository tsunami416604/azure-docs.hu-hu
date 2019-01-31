---
title: Az Azure AI-útmutató a prediktív karbantartási megoldás – a csoportos adatelemzési folyamat
description: A data science-megoldás több vertikális iparágak prediktív karbantartási megoldások átfogó leírást.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/11/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 860d24bf9de02d1b2ca46f05f1e09843a826aaf9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466829"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>A prediktív karbantartási megoldásokat az Azure AI útmutatója

## <a name="summary"></a>Összegzés

Prediktív karbantartás (**PdM**) prediktív elemzéseket, amelyek segítségével a vállalatok számos iparágban eszköz magas kihasználtság eléréséhez és a működési költségeket, akár egy népszerű alkalmazás. Ez az útmutató egyesíti az üzleti és analitikai irányelvek és gyakorlati tanácsok sikeresen fejlesztéséhez és üzembe helyezése a PdM-megoldások használatával a [a Microsoft Azure AI-platform](https://azure.microsoft.com/overview/ai-platform) technológia.

Márpedig Ez az útmutató bemutatja, iparág-specifikus üzleti forgatókönyvek és a feltételeknek megfelelő ezekben a forgatókönyvekben a PdM folyamatán. A követelményekkel és modellezési technikák PdM megoldásokra is biztosítja. A fő tartalom az útmutató az adatelemzési folyamat – beleértve az adat-előkészítés, a funkciófejlesztési, a modell létrehozásához és a modell operacionalizálás van. Ezek a legfontosabb fogalmak kiegészíteni, ez az útmutató segítségével felgyorsíthatja az alkalmazásfejlesztési PdM megoldássablonok készletét sorolja fel. Az útmutató hasznos képzési segédanyagokat a gyakorlott további információ a mesterséges Intelligencia, a data science mögött is mutat. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science útmutató áttekintése és azon célközönség
Ez az útmutató első felében tipikus üzleti problémák, ezek a problémák megoldása érdekében PdM végrehajtási előnyeit ismerteti, és néhány gyakori felhasználási esetét sorolja fel. Ez a tartalom az üzleti döntéshozók (BDMs) előnyeit. A második felében semmit a data science mögött PdM ismerteti, és ebben az útmutatóban leírt elveket felhasználásával PdM megoldások listáját tartalmazza. Képzési tervek és képzési anyagokat hivatkozásokat is tartalmaz. Szakmai döntéshozók (TDMs), ez a tartalom hasznosnak fogja.

| Kezdje... | Ha Ön... |
|:---------------|:---------------|
| [A prediktív karbantartáshoz üzleti eset](#Business-case-for-predictive-maintenance) |csökkenti az állásidőt és az üzemeltetési költségeket, és javíthatja a berendezések kihasználtsági szeretne üzleti döntéshozó (BDM) |
| [Adatelemzés a prediktív karbantartáshoz](#Data-Science-for-predictive-maintenance) |technikai döntéshozó (TDM) kiértékelése PdM technológiák megértéséhez egyedi adatfeldolgozás és a prediktív karbantartási AI-követelményei |
| [A prediktív karbantartási megoldás sablonok](#Solution-templates-for-predictive-maintenance)|egy szoftvertervező vagy szeretne gyorsan Felállítható egy bemutatót vagy egy proof-of-concept AI-fejlesztés |
| [A prediktív karbantartáshoz tanfolyamok forrásai](#Training-resources-for-predictive-maintenance) | a fenti egy részének vagy egészének, és ismerje meg, az adatelemzés, eszközöket és technikákat alapvető fogalmakat szeretné.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó Előfeltételek
A BDM tartalom bármely korábbi data science el, hogy az olvasó nem várja. A TDM tartalom statisztikák és adatok adatelemzési alapszintű ismerete hasznos lehet. Azure Data és a mesterséges Intelligencia szolgáltatásokat, Python, R, XML, és JSON Tudásbázis ajánlott. Mesterséges Intelligencia technikák vannak megvalósítva, a Python- és R-csomagokat. Megoldássablonok vannak megvalósítva, Azure-szolgáltatások, fejlesztői eszközök és SDK-k használatával.

## <a name="business-case-for-predictive-maintenance"></a>A prediktív karbantartáshoz üzleti eset

Vállalatok kritikus berendezésekre a maximális hatékonyság és kihasználtságának nyomon követésével valósíthat meg a visszaadandó tőkebefektetések futniuk van szükség. Ezek az eszközök repülőgép-hajtóművek, turbina, elevator vagy ipari hőmérsékletű – több millió – mindennap berendezések fénymásolókról, kávé gépek vagy Vízhűtők le követelnek egyéb fontos terjedhet.
- Alapértelmezés szerint a legtöbb vállalkozás támaszkodjon _javítási karbantartási_, ahol részek cseréje és amikor meghiúsulnak. Javítási karbantartásnak részek teljesen használt (tehát nem pazarolni arra összetevő élettartama), de a költségeket az üzleti az állásidő, munkaerő és nem tervezett karbantartási követelményeket (ki óra vagy feladatátadáshoz helyek).
- Jelenleg a következő szint vállalkozások gyakorlat _megelőző jellegű karbantartási_, amennyiben azok határozza meg a hasznos gyűjteményszintű egy részére, és karbantartása, vagy cserélje le a hiba előtt. Megelőző jellegű karbantartási elkerülheti a nem ütemezett és a katasztrofális hibák. De az ütemezett üzemszünet magas költségeket, az összetevő előtt feltételeit, és a munka továbbra is teljes élettartamuk korrigáljuk kihasználását.
- A cél az _prediktív karbantartási_ , hogy optimalizálja a javítási és megelőző jellegű karbantartási engedélyezésével közötti egyensúlyt _szerinti_ összetevők cserélni. Ez a módszer csak, amelyek közel hibát váltja fel összetevőket. (Ellentétben a megelőző karbantartás) összetevő lifespans kiterjesztésével, és nem tervezett karbantartás és a munka költségek csökkentése (keresztül helyesbítő karbantartás), vállalatok is költséghatékony és versenyelőny.

## <a name="business-problems-in-pdm"></a>A PdM üzleti problémák
Vállalkozások arc váratlan hibák miatt magas működési kockázatokat és a problémák kiváltó okának betekintést a komplex rendszerek korlátozott. A fő üzleti kérdéseket a következők:

- Rendellenességek észlelése berendezések vagy a rendszer teljesítményével vagy funkcióival.
- Előrejelezheti, hogy egy eszköz a közeljövőben meghiúsulhat.
- Becsülje meg egy eszköz a fennmaradó hasznos élettartamát.
- Azonosítsa a hibát egy eszköz fő okait.
- Azonosítsa a karbantartási műveleteket kell hajtható végre, akkor, ha az eszköz.

Tipikus cél utasításokat a PdM a következők:

- A működés szempontjából kritikus fontosságú berendezések működési kockázat csökkentéséhez.
- Növelje eszközállomány aránya alapján hibákat, mielőtt bekövetkeznének.
- Karbantartási költség ellenőrzés just-in-time-karbantartási műveletek engedélyezésével.
- Alacsonyabb vásárlói kopásállóság, a márka lemezképet, és elveszett értékesítési javítása.
- Alacsonyabb a készlet költségei átrendezési előrejelzésével a leltári állományt csökkentésével.
- Fedezze fel a minták csatlakozik a különböző karbantartási problémákat.
- Adja meg a KPI-k (fő teljesítménymutatók) például az egészségügyi pontszámok Eszközállapotok.
- Becsült hátralévő időtartama van, az eszközök.
- Javasoljuk a megfelelő időben karbantartási tevékenységek miatt.
- Csak az idő készlet engedélyezéséhez becslése a rendelési dátum részek kell cserélni.

Cél utasítások a kiindulási pontok:

- _az adatszakértők_ elemzéséhez, és adott prediktív kapcsolatos problémák megoldásához.
- _a felhőbeli tervezők és fejlesztők_ történő helyreállíthatósága egy teljes körű megoldást.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Jogosult a prediktív karbantartási kapcsolatos problémák
Fontos kiemelni, hogy nem minden használati esetek vagy üzleti problémák hatékonyan megoldhatók PdM. Van három fontos feltételeknek megfelelő feltételek, amelyeknek a probléma kiválasztása során figyelembe kell venni:

- A probléma azt kell prediktív jellegű; vagyis kell egy cél- vagy egy előre serkenti az eredményt. A probléma a hibák megelőzése, észlelése esetén végrehajtandó világos és egyértelmű utat is rendelkezhet.
- A probléma rendelkeznie kell egy rekordot a készülék, amely tartalmazza a működési előzmények _jó vagy hibás eredményeket_. Milyen műveleteket hibás eredményeket mérséklésére is biztosítani kell ezeket a rekordokat részeként. Javítsa ki a hibajelentéseket, a teljesítménycsökkenés, karbantartási naplóit, és cserélje le naplókat is fontosak. Emellett javításokat, és helyettesítő rekordok vállalt is hasznosak.
- A rögzített előzmények megjelennek _megfelelő_ az adatok _elegendő_ elegendő és megfelelő minőségű, a használati eset támogatására. Adatok relevancia és megfelelőségéről és kapcsolatos további információkért lásd: [adatkövetelmények prediktív karbantartást](#Data-requirements-for-predictive-maintenance).
- Végül az üzleti tartomány szakértők a probléma egyértelművé kell rendelkeznie. A belső folyamatai és eljárásai tudna segítséget az elemző ismertetése, és az adatok értelmezéséhez tisztában kell lennie. Akkor is tudnia kell, hogy a szükséges módosításokat a meglévő üzleti folyamatokba segítségével kapcsolatos problémák esetén a megfelelő adatok gyűjtése, ha szükséges.

## <a name="sample-pdm-use-cases"></a>Minta PdM alkalmazási helyzetek
Ez a szakasz a PdM alkalmazási helyzetek, például a légi közlekedésben, segédprogramok és szállítási számos iparág gyűjteménye összpontosít. Minden szakasz üzleti problémák megoldására kezdődik, és felvázolja az előnyöket, PdM, a vonatkozó adatokat a környező az üzleti problémát, és végül a PdM-megoldások előnyeit.

| Üzleti probléma | A PdM előnyei |
|:-----------------|-------------------|
|**Típus**      |                   |
|_Késleltetés és a lemondások eszközmegfelelőségre_ vehesse a műszaki problémák miatt. Hibák időben nem javítható megszakítandó repülőjáratok okozhatnak, és akadályozza az ütemezés és a műveletek. |PdM megoldások tudja jelezni a valószínűsége annak, légi késleltetett, vagy több vehesse a műszaki hibák miatt megszakadt.|
|_Repülőgéphajtómű-részek hiba_: Repülőgép motor rész cseréjére a repülőgépipar belül a legtöbb gyakori karbantartási feladatok közé tartoznak. Karbantartás megoldás szükséges összetevő készlet rendelkezésre állásának, szállítási és tervezési gondos kezelése|Tudja gyűjteni az eszközintelligencia összetevő megbízhatósági vezet jelentős csökkentését, beruházási költségeket.|
|**Pénzügyi** |                         |
|_ATM hiba_ gyakran okoz problémát a banki iparágon belül van. A probléma itt, hogy a jelentés a valószínűsége annak, hogy egy ATM készpénz kivonási tranzakció az készpénz adagolót egy tanulmány és részben vagy egészben hiba miatt beolvasása megszakítva. Tranzakciós hibák előrejelzésen alapuló, adatközponthoz karbantarthatók a proaktív módon megakadályozza, hogy a hiba lépett fel.| Hanem engedélyezi a gép midway keresztül egy tranzakció sikertelen, a kívánatos Ez esetben a program, szolgáltatás megtagadása a gép az előrejelzések alapján.|
|**Energiafogyasztás** |                          |
|_Megszünteti a turbina hibák_: A szél turbina a fő energiaforrás kímélő országban, és olyan magas tőkeköltségek terén. A szél turbina legfontosabb összetevője a készítő motor. a hiba jelenik meg a turbina hatástalanok. Emellett akkor is rendkívül költséges javításához.|Előrejelzésére MTTF (átlagos idő) például KPI-k segítségével az energiaszolgáltató vállalatok számára turbina hibák megelőzése, valamint a minimális üzemen kívüli idővel. Hiba valószínűségek figyelésére, amelyek várhatóan hamarosan sikertelen turbina szerelők értesíti, és karbantartási idő-alapú rendszerek ütemezése. Prediktív modelleket adjon meg másik tényező befolyásolja a hiba, amely segítségével a technikusok betekintést jobban megismerheti az esetleges problémák okait.|
|_Áramkör-megszakító hibák_: Terjesztés a lakhelyek és vállalkozások számára szükséges power sorok garantálja az energia kézbesítési mindig működőképes legyen. Áramkör-megszakítók segítségével korlátozható vagy power sérülésének elkerülése érdekében az időjárási viszonyok során terhelve vagy káros sorokat. Itt az üzleti problémát az áramkör-megszakító előforduló hibák előrejelzésében.| PdM megoldások javítási költségek csökkentése és a gyűjteményszintű az eszközökre, például az áramkör-megszakítók növelése érdekében. Segítik az energiagazdálkodási hálózati minőségének javítása a váratlan meghibásodások és szolgáltatáskiesések csökkentésével.|
|**Szállítási és logisztikai** |    |
|_Itt ajtó hibák_: Itt nagy méretű vállalatok számára működési elevator világszerte több millió teljes verem szolgáltatást biztosítanak. Itt biztonságát, megbízhatóságát és üzemidő olyan ügyfelek számára, akik fő területtel kell foglalkoznunk. Ezek a vállalatok keresztül érzékelők, javítási és megelőző jellegű karbantartással segítségével nyomon követheti ezeket és más jellemzők különböző. Az itt található a legjelentősebb ügyfél probléma itt ajtók hibásan működik. Az üzleti problémát ebben az esetben, hogy egy Tudásbázis prediktív alkalmazás, amely előrejelzi a lehetséges okai ajtó hibák.| Elevator tőkeberuházás potenciálisan egy 20-30 éves időtartama van a rendszer. Így lehet, hogy minden lehetséges értékesítés versenyhelyzetében; ezért a keresésről szolgáltatás és a támogatási. Prediktív karbantartás is ezek a vállalatok a termékeikben versenytársaik használhatóságát és szolgáltatásajánlatok.|
|_Hibák kerék_: Kerék hibák fiók összes felében derailments betanításához, és a globális sín ipar több milliárd költség. Kerék hibák is okozhatja a rails romlani, néha okoz a sín túl korán érvényteleníteni. Sín oldaltörések katasztrofális esemény, például derailments vezethet. Az ilyen példányok elkerülése érdekében vasutak kerekek teljesítményének figyeléséhez, és megelőző jellegű módon le kell. Az üzleti problémát itt kerék hibák előrejelzését.| Prediktív karbantartás kerekek segít a just-in-time csere |
|_Subway train ajtó hibák_: A subway műveletek az késleltetések fő oka train autó ajtó hibák. Itt az üzleti problémát, hogy train ajtó előforduló hibák előrejelzésében.|Korai ismertségét, ajtó hiba, vagy egy ajtó hiba hátralévő napok száma segít az üzleti optimalizálás karbantartási ütemezések ajtó betanításához.|

Ez a szakasz bemutatja, hogyan valósíthat meg a fent ismertetett PdM előnyeit a részletek beolvasása.

## <a name="data-science-for-predictive-maintenance"></a>Adatelemzés a prediktív karbantartáshoz

Ez a szakasz data science alapelvek és eljárás általános útmutatást nyújt a PdM a. Célja, hogy egy TDM, megoldásfejlesztőként segítségével, vagy egy fejlesztői megismerheti az előfeltételeket és a teljes körű AI-alkalmazások PdM kiépítése során. Ez a szakasz egy át a bemutatókat, valamint tudjon meg, és a felsorolt proof-of-concept sablonok [prediktív karbantartási megoldás sablonok](#Solution-templates-for-predictive-maintenance). Ezután használhatja ezeket alapelvek és ajánlott eljárások a PdM-megoldás megvalósítása az Azure-ban.

> [!NOTE]
> Ez az útmutató nem készült, akik az olvasó adatelemzés. Számos hasznos források áll rendelkezésre további információ a részben [a prediktív karbantartáshoz tanfolyamok forrásai](#Training-resources-for-predictive-maintenance). A [megoldássablonok](#Solution-templates-for-predictive-maintenance) az útmutatóban szereplő adott PdM problémák AI technikát mutatják be.

## <a name="data-requirements-for-predictive-maintenance"></a>Prediktív karbantartás adatok követelményei

Minden tanuló sikeres (a) a mi van folyamatban színesített minőségét, és (b) a learner képességét függ. Prediktív modelleket az előzményadatok minták bemutatása és a jövőbeni következmények előrejelzésére bizonyos valószínűséggel ezeket a megfigyelt mintáknak megfelelően. A modellek prediktív pontossága attól függ, a alkalmazhatóságát, a megfelelősége és a tanítási és tesztelési adatok minőségét. Az új adatok, amelyek "sorolódik" modell használatával ugyanazokat a szolgáltatásokat és séma kell rendelkeznie a tanítási és tesztelési adatként. A szolgáltatás jellemzői (típus, sűrűség, terjesztési és így tovább) az új adatok, amelyek a tanítási és tesztelési adatkészletek egyeznie kell. Az ebben a szakaszban célja az ilyen adatok követelményeinek.

### <a name="relevant-data"></a>A vonatkozó adatokat

Először is az adatok azt kell _a problémához kapcsolódó_. Vegye figyelembe a _hiba kerék_ tárgyalt kis-és nagybetűhasználattal újabb – a betanítási adatok tartalmaznia kell a kerék műveletekkel kapcsolatos funkciók. Ha a probléma előre jelezni a sikertelen volt a _elősegítik rendszer_, kiterül és kitölti a különböző összetevők elősegítik rendszer rendelkezik a betanítási adatok. Az első esetben egy adott összetevőre célozza meg, míg a második esetben célozza meg, a nagyobb alrendszer hibát. Az általános ajánlás az előrejelzési rendszerek nagyobb alrendszerek helyett az egyes összetevőkkel kapcsolatos tervezési, mivel ez utóbbi fog rendelkezik több elosztott adatok. A tartomány-szakértő (lásd: [befolyásoló problémákat a prediktív karbantartáshoz](#Qualifying-problems-for-predictive-maintenance)) segíthet a kiválasztja a leginkább releváns adatokat az elemzéshez alkészleteiben. A releváns adatforrások ismertetik részletesebben [adat-előkészítés a prediktív karbantartáshoz](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Elegendő adat
Előzményadatait meghibásodása kapcsolatban gyakran feltett kérdésekre: (1) "hány sikertelen események szükségesek a modell betanításához?" (2) "hány rekordok minősül"elegendő"?" Nincsenek nem végleges válaszokat, de csak szabályok költségcsökkenést eredményezzen. (1) a több sikertelen események száma jobb a modell. (2) és sikertelen események pontos száma attól függ, az adatok és a probléma alatt megoldott kontextusában. De a tükrözés oldalon, ha egy gép túl gyakran sikertelen majd az üzleti lecseréli, amely hiba példányok csökkenti. Itt újra, a tartományból szakértői útmutatást fontos. Előfordulhatnak azonban olyan méretezhetők a probléma, a metódusok _ritka események_. A szakasz tárgyalja azok [imbalanced adatok kezelésének alapelveit](#Handling-imbalanced-data).

### <a name="quality-data"></a>Minőségi adatok
Kritikus fontosságú az adatok minőségét – minden előjelző közé kell esnie _pontos_ a célként megadott változó értékének együtt. Adatminőség statisztikák és adatok felügyelete egy jól vizsgált területre, és ezért vette az a jelen útmutató.

> [!NOTE]
> Több erőforrást és vállalati termékek minőségét adatok is van. A hivatkozások minta lejjebb:
> - Dasu, T, a Johnson, a T., felderítő Data szintű adatbányászatra, és az adatok törlését, Wiley, 2003.
> - [Feltáró jellegű adatok elemzése, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kvantitatív adatoknak nagy méretű adatbázisok törlése](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der keresés, M, r-adatok törlését bemutatása](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Adat-előkészítés a prediktív karbantartáshoz

### <a name="data-sources"></a>Adatforrások

A prediktív karbantartáshoz kapcsolódó adatforrások közé tartozik, de nem korlátozódik:
- Sikertelen futtatások előzményei
- Karbantartási javítási előzmények
- Gép működési feltételek
- Készülék-metaadatok

#### <a name="failure-history"></a>Sikertelen futtatások előzményei
Hibaesemények ritkák PdM-alkalmazásokban. Azonban prediktív modellek létrehozását, az algoritmus kell normál műveleti minta egy összetevő, valamint a hiba minták megismeréséhez. Így a betanítási adatok tartalmaznia kell mindkét kategóriákból példák elegendő számú. Rekordok és a részek karbantartási helyettesítő előzményei szolgálnak sikertelen események megtalálásához. Az egyes tartományi ismeretek segítségével a betanítási adatok rendellenességeket is kódhibáiként lehet definiálni.

#### <a name="maintenancerepair-history"></a>Karbantartási javítási előzmények
Az eszköz karbantartási előzményeit azokról az összetevőkről cseréje esetén, például végzett javítási tevékenységek részleteit tartalmazza. Ezeket az eseményeket rögzíteni teljesítménycsökkenés mintákat. Ez rendkívül fontos információ a betanítási adatok hiányában félrevezető modell eredményeinek vezethet. Sikertelen futtatások előzményei a belül előzmények karbantartási is található a speciális hibakódok vagy rendelés dátumát részei. További adatforrások hiba minták befolyásoló meg kell vizsgálni és tartomány-szakértők által biztosított.

#### <a name="machine-operating-conditions"></a>Gép működési feltételek
Streamelési adatok alapján (vagy más) érzékelő a készülék műveletben egy fontos adatforráshoz. A PdM a fő feltételezése a, hogy a készülék állapota csökkenti a szokásos működés során idővel. Az adatok várható elévülési ezt a mintát, és felismerheti a rendellenességeket, amelyek teljesítménycsökkenést vezet rögzítése időfüggő funkciókat tartalmaznak. A historikus szempont az adatok az algoritmus a hiba és a hibát nem jelentő további idővel szükség. Ezek az adatpontok alapján, az algoritmus megtanulja előrejelzése idő hány több egységet egy gép folytathatja a munkát, mielőtt sikertelen lesz.

#### <a name="static-feature-data"></a>A szolgáltatás statikus adatok
Statikus szolgáltatása, a készülék metaadatait. Példák a készülék típusa, a modell, a gyártó dátumát, indítsa el a szolgáltatás dátuma, a rendszer és az egyéb műszaki specifikációk helyét.

Példák a vonatkozó adatokat a [minta PdM használati esetek](#Sample-PdM-use-cases) lenti táblázatban láthatóak:

| Használati eset | Példák a vonatkozó adatok |
|:---------|---------------------------|
|_Repülési késleltetés és a lemondások_ | A légi Repülőút szakaszainak formájában az útvonal-információkat, és naplók lapon. Repülőjáratok alapját képező adatainak útválasztási kitérnek például indító/érkezés dátuma, ideje, repülőterek, layovers stb. Oldal log tartalmazza a hiba- és karbantartási kódokat az alapoktól karbantartó személyzet által rögzített sorozatát.|
|_Repülőgép motor részek hiba_ | Maradjon a repülő karbantartására, amely információkat tartalmaz a feltétel a különböző részeiből szenzorait gyűjtött adatokat. Karbantartási rögzíti és segít azonosítani összetevő hiba előfordulásakor is szövegelemekben.|
|_ATM-hiba_ | Minden egyes tranzakció (cash/ellenőrzés helyezésével) érzékelőinek és készpénz kiadása. Információk a gap a mérési megjegyzések között, vastagsága vegye figyelembe, vegye figyelembe a beérkezés távolság, ellenőrizze a attribútumok stb. Hibakódok, a javítási információkat, utolsó időpont, amikor a pénzen adagolót biztosító karbantartási rekordok újratöltése esetén.|
|_A szél turbina hiba_ | Érzékelő megfigyelik a turbina például a hőmérséklet, viszonyítva megadott szélirány, generált power, generátor sebességet stb. A szél-farmok különböző régiókban található több szél turbina összegyűjtött adatokat. Általában minden turbina mérések továbbítását rögzített időközönként több érzékelőinek fog rendelkezni.|
|_Áramkör-megszakító hiba_ | Karbantartási naplók, amely korrekciós, megelőző jellegű, és rendszeres műveleteket tartalmazza. Operatív adatok, amelyek között műveletek esetében például megszakítók küldött automatikus és manuális parancsokat is tartalmaz. Eszközök metaadatait, például dátum előállítása, a helyet, a modell, stb. Áramkör-megszakító előírásai például feszültség szintek, a földrajzi hely, a környezeti feltételek.|
|_Itt ajtó hibák_| Itt metaadatok például itt, gyártott dátum, karbantartás gyakorisága, épület típusa és stb. Operatív információk, például ajtó ciklusokat, átlagos ajtó bezárása idő számát. Sikertelen futtatások előzményei okait.|
|_Kerék hibák_ | Érzékelőktől kapott adatok, hogy a mértékek kerék gyorsítás, fékezési példányok, vezetői távolság, megoldáscsomagja stb. Statikus adatait a kerekeket, például a gyártó, gyártott dátum. Hiba adatok következtetni rész rendelési adatbázisból, amelyek nyomon követik a rendelés dátuma és mennyisége.|
|_Subway train ajtó hibák_ | Ajtó nyitó és záró idő, egyéb működési adatok, például train ajtók állapotban van. Statikus adatok eszköz azonosítója, idő és feltétel értéke oszlop tartalmazhat.|

### <a name="data-types"></a>Adattípusok
Adja meg a fenti adatforrások, a két fő adattípusok megfigyelhető a PdM-tartományt a következők:

- _A historikus adatok_: Operatív telemetriát, a gép feltételek, a munkahelyi rendelés típusok, prioritás kódok, amely a rögzítés idején időbélyege lesz. Hiba, a karbantartás és javítás és a használati előzmények is minden eseményhez tartozó időbélyeggel rendelkezik.
- _Statikus adatok_: Gép szolgáltatása és operátor általában statikus mivel, bemutatják a műszaki specifikációk gépek vagy operátor attribútumok. Ezek a funkciók módosulhatnak, idővel, ha van továbbá időbélyegeket társítva.

Változók előjelző és a cél legyen üzenetfájlrekordok/átalakításának [numerikus kategorikus és más adattípusok](https://www.statsdirect.com/help/basics/measurement_scales.htm) attól függően, a használt algoritmus.

### <a name="data-preprocessing"></a>Adatok előfeldolgozása
Az előfeltételként _jellemzőkiemelés_, készítse elő az adatokat a különböző Streamek összeállításához, amelyből létre új funkciókat könnyebbé vált a sémát. Megjelenítheti az adatokat először a rekordok táblaként. A tábla minden sorára jelenti egy képzési példányt, és az oszlopok jelölik _előjelző_ funkciók (más néven független attribútumok vagy változók). Adatok rendezése úgy, hogy az utolsó oszlopot a _cél_ (függő változó). Képzési példányonként hozzárendelése egy _címke_ , ez az oszlop értékét.

Időegységek érzékelőktől kapott adatok időtartama osztása historikus adatok esetén. Az adott eszköz számára időegységet kell tartoznia minden egyes rekord _és kínáljon különböző információkat_. Időegységek hányszorosa másodperc, perc, óra, nap, az üzleti igényeknek megfelelően vannak meghatározva, hónapok, és így tovább. Az időegység _nem rendelkezik azonos adatok gyűjtésének gyakorisága_. Ha a gyakoriság értéke nagy, előfordulhat, hogy nem jelennek bármely lényeges különbség egy egységből a másikba. Tegyük fel például, hogy környezeti hőmérséklet gyűjtötte a program 10 másodpercenként. Csak ugyanazon az intervallum használ a betanítási adatok megnöveli a példák száma anélkül, hogy minden további információt. Ebben az esetben egy jobb stratégia lehet az adatok több mint 10 perc vagy egy órán alapuló a szakmai indoklással használja.

Statikus adatok esetében
- _Karbantartási rekordok_: Nyers karbantartási rendelkezik egy eszköz azonosítóját, valamint a timestamp információkat, karbantartási időben egy adott időpontban végzett tevékenységeket. Karbantartási tevékenységek átalakítása _kategorikus_ oszlop, amelyben minden kategória leíró egyedi képez le egy adott karbantartási művelet. Eszköz azonosítója, időpont és karbantartási művelet karbantartási rekordok a séma tartalmazhat.

- _Hiba a rekordok_: Adott hibakódok vagy adott üzleti feltételei által meghatározott hibaesemények hibák vagy a hiba oka lehet rögzíteni. Azokban az esetekben, ahol a készülék rendelkezik-e több hibakódok szakértői tartomány kell azonosíthatja azokat, amelyek a célváltozó tárolására. A fennmaradó hibakódok vagy feltételek használatával hozhat létre _előjelző_ ezek a hibák az jellemzőket. Hiba rekordok a séma tartalmazhat eszköz azonosítója, ideje, hiba vagy a hiba oka – Ha elérhető.

- _Gép és az operátorral metaadatok_: A gép és az operátorral adatok egyesítése egy sémát, hogy egy eszköz társítása a operátor szerinti szűrése, annak attribútumai mellett. A gép feltételek a séma tartalmazhat eszköz azonosítója, az eszközintelligencia-funkciók, operátor azonosítója és operátor funkciókat.

Más adatok előfeldolgozási lépéseket _kezelése a hiányzó értékeket_ és _normalizálási_ az attribútumértékek. Részletes leírásáért lásd az túlmutat a jelen útmutató – tekintse meg a következő szakaszban néhány hasznos hivatkozásokat is.

A fentiekben üzenetfájlrekordok helyen, a végső átalakítása előtt funkciófejlesztési, hogy az eszköz azonosítóját, valamint időbélyegző alapján a fenti táblázatokhoz csatlakozhat adatforrásokhoz. Az eredményül kapott tábla kellene null értékeket azokhoz a hiba oszlop gépen a normál működés esetén. Ezeket az értékeket null is lehet imputált egy mutató a normál működés szerint. Ebben az oszlopban a hiba használatával létrehozhat _prediktív modell címkék_. További információkért lásd a szakasz [modellezési technikák, prediktív karbantartás](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Jellemzőkiemelés
Funkciófejlesztési első lépése előtt az adatok modellezését. Az adatelemzési folyamat szerepét [az itt leírtak szerint](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkció_ attribútum prediktív modell – például a hőmérsékletet, nyomás, rezgés és így tovább. PdM funkciófejlesztési magában foglalja a gép állapota paltformfüggetlen eloszlatása időtartama alatt gyűjtött előzményadatok keresztül. Ebben az értelemben akkor különböző, például a távoli megfigyelés, a rendellenességek észlelése és a hibaészlelés a társaktól. 

### <a name="time-windows"></a>Idő windows
Távoli megfigyelés, az eseményeket a jelentés tartalmazza az elvégzendő _között eltelt_. Rendellenességek észlelése modellek (pontszám) bejövő adatfolyam jelző rendellenességeket pontok kezdődően az időben értékeli. Adott típusú kell pontok ideje a előforduló hibák osztályozza a hibaészlelés. Ezzel szemben a PdM magában foglalja a hibák előrejelzésére keresztül egy _jövőbeli időszakban_funkciók, amelyek a gépek működését keresztül alapján _múltbéli időszak_. A PdM idő egyes pontok adatait a szolgáltatás túl zajos prediktív kell. Így az adatok az egyes szolgáltatásokhoz kell lennie _smoothened_ idő windows keresztül az adatpontok összesítésével.

### <a name="lag-features"></a>Késés funkciók
Az üzleti követelmények meghatározása, hogy milyen a modell előre jelezni az elkövetkező rendelkezik. Viszont, ennek az időtartamnak segít "milyen vissza a modell rendelkezik keresse ki az" meghatározása, hogy ezek az előrejelzések. A "szeretne vissza" időszak neve a _lag_, és a szolgáltatások, a késés időszakban fejthetők vissza az úgynevezett _funkciók lag_. Ez a szakasz bemutatja az adatforrásokból az időbélyegeket és a szolgáltatás létrehozása a statikus adatforrások gyűjteménynévmintája lag jellemzőket. Késés funkciók jellemzően _numerikus_ jellegűek.

> [!IMPORTANT]
> Az ablak mérete Kísérletezési keresztül határozza meg, és szakértői tartomány segítségével. érdemes véglegesíthető. Azonos csoportosítani a kijelölés és a késés funkciók, az összesítések és a típus a windows-definícióját tartalmazza.

#### <a name="rolling-aggregates"></a>Működés közbeni összesítések
Az egyes rekordok egy eszköz a működés közbeni ablak méretének "W" választja idő egységek száma az összesítések kiszámításához. Késés funkciók majd számítja ki a W időszakok használatával _időpont előtt_ az adott rekord. Az 1. ábrán a kék vonal rögzítve az eszközre minden egyes időegység érzékelő értékek megjelenítése. W = 3 méretű ablak fölé funkció érték mozgóátlaga azok jelöl. A gördülő átlagát számított keresztül időbélyegeket t a tartományban az összes rekordot<sub>1</sub> (a narancssárga) t<sub>2</sub> (a zöld). A W értéke általában percben vagy az adatok természetétől függően órában. Azonban az egyes problémák, nagy W (például: 12 hónap) kiadási tud biztosítani az eszköz teljes előzményeit a rekord a időpontig.

![1. ábra A működés közbeni összesített funkciók](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) 1. ábra. A működés közbeni összesített funkciók

Működés közbeni összesítések adott időtartamra példák count, average, CUMESUM (halmozott összege) mértékek, minimális/maximális értékei. Ezenkívül eltérés, szórás és N szórás szorzataként túli kiugró értékek száma gyakran használják. Példák, amelyek alkalmazandók lehetnek az összesítések a [használati esetek](#Sample-PdM-use-cases) ebben az útmutatóban alább láthatók. 
- _Késleltetés eszközmegfelelőségre_: a múlt héten nap/hibakódok száma.
- _Repülőgéphajtómű-rész hiba_: a működés közbeni azt jelenti, hogy, szórás és összeg az elmúlt nap során hét stb. Ez a metrika az üzleti tartomány szakértői együtt kell meghatározni.
- _ATM-hibák_: a működés közbeni azt jelenti, hogy, medián, tartomány, szórás szorzataként, három szórás szorzataként, felső és alsó CUMESUM túli kiugró értékek száma.
- _Subway train ajtó hibák_: Elmúlt nap, heti, Kétheti stb során események száma.
- _Áramkör-megszakító hibák_: Hibaszámait felett múlt héten, év, három év stb.

A PdM egy másik hasznos módszer, hogy trend módosításokat, adatforgalmi csúcsokhoz és az adatok rendellenességek észlelése az algoritmusok használatával megváltozik.

#### <a name="tumbling-aggregates"></a>Átfedésmentes összesítések
Minden rekord egy eszköz, méretű ablak feliratú _W -<sub>k</sub>_  van definiálva, ahol _k_ windows méretű száma _W_. Összesítések majd jönnek létre keresztül _k_ _az átfedésmentes ablakok_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  előtt egy rekord időbélyeg időszakokra. _k_ lehet néhány rövid távú hatások rögzítése vagy sok teljesítménycsökkenés hosszú távú mintázatok rögzítéséhez. (lásd a 2. ábra).

![2. ábra Átfedésmentes összesített funkciók](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) 2. ábra. Átfedésmentes összesített funkciók

Például lag funkciók, az a szél turbina használati eset lehet, hogy lehet létrehozni a W = 1 és k = 3. A lag minden az elmúlt három havi felső és alsó kiugró értékek használatával azt tartalmazzák.

### <a name="static-features"></a>Statikus funkciók

A berendezés előállítása, a modell számának, a helyre, például a dátum a műszaki specifikációk adunk néhány ötletet statikus funkciókat. Akkor azok _kategorikus_ modellezési változókat. Néhány példa az áramkör-megszakító használati esetekhez: feszültség, aktuális, energiakapacitás, típusú átalakító, és az áramforrást. Kerék hibák kulcsszava kerekek (ötvözött vs acélból) típusú csak példaként szolgál.

Az adatok előkészítési erőfeszítéseket eddig tárgyalt folyamatban vannak rendezve, ahogy az alábbi adatokat kell vezethet. Képzés, tesztelési és érvényesítési adatok rendelkeznie kell a logikai séma (Ez a példa bemutatja idő nap egységben).

| Eszközazonosító | Time | <Feature Columns> | Címke |
| ---- | ---- | --- | --- |
| A123 |1. napi | . . . | . |
| A123 |2. napon | . . . | . |
| ...  |...   | . . . | . |
| B234 |1. napi | . . . | . |
| B234 |2. napon | . . . | . |
| ...  |...   | . . . | . |

Az utolsó lépés funkciófejlesztési a **címkézés** a cél változó. Ez a folyamat szolgáltatás a modellezési technika függ. Viszont a modellezési módszer az üzleti probléma megoldására és a rendelkezésre álló adatok jellegétől függ. A következő szakaszban tárgyalt címkézést.

> [!IMPORTANT]
> Adat-előkészítési és funkciófejlesztési feladatok olyan fontos, mint a modellezési módszerek a PdM-megoldások sikeres érkeznek. A tartomány szakértő és az azoknak megfelelő szolgáltatásokat és a modell adatainak díjszabással jelentős mennyiségű időt kell befektetni. Egy kis számos könyvek funkciófejlesztési a mintát az alábbiak:
> - Pyle, adatbányászati (Morgan Kaufmann oktatóanyagokban az adatkezelő rendszerek) 1999 D. adatok előkészítése
> - Zheng, A., Casari, A. Funkciófejlesztési a Machine Learning szolgáltatáshoz: Alapelvek és technikák adatszakértők, O'Reilly, 2018.
> - Dong, G. Liu, h (Szerkesztő), a szolgáltatás műszaki gépi tanulási és adatelemzési (Chapman & Hall/CRC adatbányászat és a Knowledge Discovery sorozat) CRC nyomja le, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>A prediktív karbantartáshoz modellezési technikák

Ez a szakasz ismerteti a fő modellezési technikák PdM kapcsolatos problémák esetén a megadott címkével konstrukció módszereket együtt. Figyelje meg, hogy a különböző iparágak egyetlen modellezési módszer használható. A modellezés technika párosítva van a data science probléma, mint az adatok csak olyan mélységben kontextusában.

> [!IMPORTANT]
> A hiba eseteket és a címkézési stratégia tartozó címkék kiválasztását  
> meg kell határozni a tartomány szakértői egyeztetve.

### <a name="binary-classification"></a>Bináris osztályozás
Bináris osztályozás szolgál _előre jelezni a valószínűsége annak, hogy a berendezés jövőbeli időn belül nem sikerül_ – néven a _jövőbeli horizon időszak X_. X határozza meg az üzleti problémát és az adatokat, a tartomány szakértői egyeztetve. Példák:
- _minimális ügyfélátfutási idő_ szükséges cserélje le az összetevők, karbantartási erőforrások üzembe helyezése, karbantartásához, amely nagy eséllyel fordulnak elő az adott időszakban probléma elkerülése érdekében.
- _minimális száma, események_ , amely akkor fordulhat elő, mielőtt probléma merül fel.

Ezzel a technikával a betanítási példákat kétféle jelöli. Egy pozitív például _egy hibát jelez, amely_, a címkével ellátott = 1. Egy negatív példának, amely azt jelzi, hogy a normál működés, a címkével ellátott = 0. A célváltozó, és ezért a címke értéke, _kategorikus_. A modell azonosítania kell minden egyes új például valószínűleg sikertelen, vagy át a következő X mértékegységét módon működnek.

#### <a name="label-construction-for-binary-classification"></a>A bináris osztályozási címke építése
Itt a kérdés van: "Mi a valószínűsége annak, hogy az eszköz meghiúsul a következő X időegységben?" A kérdés, a Címke X rekordokat "készül az nem", az eszköz a meghibásodás előtti választ (címke = 1), és a többi rekord, hogy "normál" címke (címke = 0). (lásd a 3. ábra).

![3. ábra Bináris osztályozás címkézését](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) 3. ábra. Bináris osztályozás címkézését

Címkézés stratégia a használati esetek néhány példát alább láthatók.
- _Eszközmegfelelőségre az késleltetések_: X 1 nap, akkor választható, előre jelezni az késleltetések 24 órán belül. 1, majd minden repülőjáratok, amelyek előtt hibák 24 órán belül vannak ellátva.
- _ATM készpénz hibák eredményeként_: A célja meghatározni a hiba annak valószínűsége, hogy egy tranzakció a következő egy óra is lehet. Ebben az esetben az elmúlt órában a hiba történt az összes tranzakciók 1 van jelölve. 1 használatával előrejelzi a hibák valószínűségét keresztül a következő N pénznem megjegyzések mellőzni, törölni, ha nem az utolsó N megjegyzések belül az összes jegyzet van jelölve.
- _Áramkör-megszakító hibák_: A cél lehet megjósolni a következő áramkör-megszakító parancshiba. Ebben az esetben X van kiválasztva egy jövőbeli parancsot is.
- _Ajtó hibák betanításához_: X akkor választható, mint két nap.
- _Megszünteti a turbina hibák_: X akkor választható, két hónapos időszakra.

### <a name="regression-for-predictive-maintenance"></a>A prediktív karbantartáshoz regresszió
Regressziós modellek is használható _a fennmaradó hasznos élettartamát (RUL) egy eszköz számítási_. Rul-JÉNEK meghatározása az, hogy mennyi ideig egy eszköz működik, mielőtt a következő hiba történik. Minden egyes képzési például, egy rekord, amelyhez tartozik egy időegységet _nY_ az eszközökre, ahol _n_ van az a többszörös. A modell kell minden egyes új példa, mint az rul értékének kiszámításához egy _folyamatos szám_. Ez a szám azt jelzi, hogy az az időtartam, a hiba előtt van hátra.

#### <a name="label-construction-for-regression"></a>A regresszió címke építése
Itt a kérdés van: "Mi az a készülék fennmaradó hasznos élettartamának (RUL)?" Az egyes rekordok a meghibásodás előtti kiszámíthatja a címke a következő hiba előtt hátralévő idő egységek számát. Ezzel a módszerrel a címkékhez folyamatos változókat. (Lásd a 4. ábra)

![4. ábra Regresszió címkézését](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) 4. ábra. Regresszió címkézését

Regresszió, a címkézés történik hivatkozó egy meghibásodási pont. A számítást esetén nem lehet anélkül, hogy mennyi ideig az eszköz ötszáz évet a meghibásodása előtt. Tehát ezzel szemben a bináris osztályozási eszközök az adatok az esetleges hibák nélkül nem használható modellezési. Ez leginkább a problémával nevű másik statisztikai technikát [Túléléselemzés](https://en.wikipedia.org/wiki/Survival_analysis). Azonban lehetséges komplikációk ezt a technikát alkalmazásakor érintő időfüggő adatok rendszeres időközönként a PdM-használati esetek fordulhatnak elő. Túléléselemzés további információkért lásd: [ebben egy személyhívóra](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>A prediktív karbantartáshoz többcsoportos besorolás
Többcsoportos osztályozási technikák gondoskodnak a PdM-megoldásokban is használható két forgatókönyvet:
- Előrejelezheti _két jövőbeni következmények_: Az első eredmény _hibáira időtartományt_ az adott eszköz számára. Az eszköz van rendelve egy több lehetséges időtartamra. A második eredménye a hibák valószínűségét oka az, hogy egy jövőbeli időszakban _hatására a több legfelső szintű egyik_. Az előrejelzési lehetővé teszi, hogy a karbantartó személyzet tünetek és a terv karbantartási ütemezések megtekintéshez.
- Előrejelezheti _a legvalószínűbb gyökerének_ megadott hiba. Az eredmény a megfelelő készletét az adott karbantartási műveleteket a hiba elhárításához javasolja. Kiváltó okokért és javasolt javítások rangsorolt listáját segítségével a technikusok rangsorolhatja a javítási lépések hiba után.

#### <a name="label-construction-for-multi-class-classification"></a>Többcsoportos besorolási címke építése
Itt a kérdés van: "Mi a valószínűsége annak, hogy egy eszköz meghiúsul a következő _nZ_ időegységben ahol _n_ időszakok száma?" Ez a kérdés megválaszolásához nZ rekordok gyűjtő idő (3Z, 2Z, a – Z) használatával egy eszköz a meghibásodás előtti címkét. Minden egyéb címkét "normál" rögzíti (label = 0). Ez a módszer a célváltozó tartalmazza _kategorikus_ értékeket. (Lásd az 5. ábra).

![5. ábra Hiba ideje előrejelzési többosztályos osztályozási címkék](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) 5. ábra. Hiba ideje előrejelzési többcsoportos besorolási címkézés

Itt a kérdés van: "Mi a valószínűsége annak, hogy az eszköz meghiúsul a következő alapvető ok/probléma miatt időegységben X _P<sub>i</sub>_?" ahol _i_ kiválthatja a száma. A kérdés, a Címke X rekordokat egy eszköz, a meghibásodás előtt válaszolnia "alapvető ok miatt sikertelen about _P<sub>i</sub>_" (címke = _P<sub>i</sub>_). Minden más rögzíti, hogy "normál" címke (label = 0). Ez a módszer a címkék is kategorikus (lásd a 6. ábra).

![6. ábra Alapvető ok többosztályos osztályozási címkék előrejelzési](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) 6. ábra. Alapvető ok előrejelzési többcsoportos besorolási címkézés

A modell rendeli hozzá a hiba miatt minden valószínűség _P<sub>i</sub>_  és hibák nélkül valószínűségét. Ezek valószínűségek, hogy a problémákat, amelyek a legnagyobb valószínűséggel fordulnak elő a jövőben előrejelzését magnitude szerint is rendezve.

Itt a kérdés van: "Karbantartási műveleteket ajánlanak egy meghibásodás után?" Ezt a kérdést megválaszolni címkézés _nem szükséges egy jövőbeli horizon kiválasztandó_, mert a modell a későbbiekben nem becslése hiba. Ez csak becslése a legvalószínűbb alapvető OK _a hiba bekövetkezése után_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Oktatás, érvényesítése és a prediktív karbantartáshoz tesztelési módszer
A [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) biztosít egy teljes körű, a modell train-teszt ellenőrzése ciklus. Ez a szakasz PdM egyedi szempontokat ismerteti.

### <a name="cross-validation"></a>Keresztellenőrzés
A cél az [keresztellenőrzés](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) határozza meg a "teszt" a modell a betanítási fázisban az adatkészletet. Ez az adatkészlet neve a _érvényesítési set_. Ez a módszer segít korlát problémák, mint például _overfitting_ és a egy betekintést enged meg, hogyan általánossá tétele a modell, egy független adatkészlet. Azt jelenti egy ismeretlen adatkészlet, amely lehet a problémát. A PdM a képzés és tesztelési rutin jobban általánosításához a jövőbeli nem látható adatok az idő különböző szempontokat figyelembe kell.

Számos gépi tanulási algoritmusok, amelyek jelentős mértékben módosíthatják a modellek teljesítményének hiperparaméterek számos függenek. Ezek hiperparaméterek optimális értékét nem vannak számított automatikusan, a modell betanításakor. Az adatszakértő kell megadni. Többféleképpen is megtalálni a hiperparaméterek helyes értékeket.

A leggyakoribb eset az egyik megoldás _k lépésből kereszt-ellenőrzési_ , amely felosztja a példák véletlenszerűen be _k_ modellrész. Az egyes hiperparaméterek értékek, futtassa a tanulási algoritmus _k_ alkalommal. Minden egyes ismétléskor használjuk a példákban az aktuális modellrészek az érvényesítés, és a példák a többi képzési készletként. Az algoritmus betanítása példák keresztül, és a teljesítmény-mérőszámok számítási érvényesítési példák keresztül. Ez a ciklus végén az átlag kiszámításakor a _k_ teljesítmény-mérőszámon. Az egyes hiperparaméter értékek válassza ki az azokat, amelyeket a legjobb átlagos teljesítmény. Hiperparaméterek kiválaszthatják gyakran kísérleti jellegű.

A PdM-problémák adatok, amelyek az adatok több forrásból származó események időbeli adatsoroknál rögzíti. Ezeket a rekordokat a címkézés időpontja szerint rendezhető. Ezért ha az adatkészlet van felosztva _véletlenszerűen_ képzés és érvényesítési csoportba _néhány példája képzés lehet későbbi időpontban, mint érvényesítési példák némelyike_. Lesz az egyes érkező adatok alapján becsült hiperparaméter értékek jövőbeli teljesítményét _előtt_ modell volt konfigurálható. Ezek becsléseket túlságosan optimista, lehet, különösen akkor, ha az idősorozat-nem álló és haladásával idővel. Ennek eredményeképpen a kiválasztott hiperparaméter értékek optimálisnál lehet.

Az ajánlott módszer az, hogy a példák felosztása tanítási és beállítása az érvényesítés egy _időfüggő_ módját, ahol az összes érvényesítési példák későbbi időben, mint az összes képzési példákat. Az egyes hiperparaméter értékek betanítása az algoritmus a tanítási adathalmazt keresztül. A modellek teljesítményének mérésére érvényesítési ugyanezek keresztül. Válassza ki a hiperparaméter értékek azt mutatják be, a legjobb teljesítmény érdekében. Hiperparaméter értékek kiválasztani az train és érvényesítési split eredmény jobban jövőbeli modell teljesítményét, mint a kereszt-ellenőrzési által véletlenszerűen kiválasztott értékekkel.

A kész modell hozhat létre tanulási algoritmus betanítási teljes betanítási adatokon ajánlott hiperparaméter értékeivel.

### <a name="testing-for-model-performance"></a>Modellek teljesítményének tesztelése
A modell a létrehozása után az új adatokat a későbbi teljesítményét becsült szükség. Egy jó a teljesítmény-mérőszám hiperparaméter értékek kiszámítása keresztül az érvényesítési beállítása, vagy egy átlagos teljesítmény-mérőszám kereszt-ellenőrzési alapján kiszámított becslése. Ezek becsléseket gyakran túlságosan optimista történik. Az üzleti gyakran előfordulhat néhány további irányelvek a hogyan szeretnék teszteli a modellt.

Az ajánlott módszer a PdM példák felosztáskor képzés, ellenőrzés, és beállítja a Tesztadatok egy _időfüggő_ módon. Az összes teszt példák a képzés és érvényesítési példák időben későbbinek kell lennie. A felosztás után a modell generálásához, és a teljesítmény mérésére, a fentebb leírt módon.

Ha idősorozat-helyhez kötött és könnyen kiismerhető, véletlenszerű és időfüggő megközelítések létrehozása hasonló becsléseket jövőbeli teljesítmény. De ha idősorozat-nem helyhez kötött, és/vagy nehezen becsülhető, az időfüggő megközelítés realisztikusabb jövőbeli teljesítmény becslései hoz létre.

### <a name="time-dependent-split"></a>Időfüggő felosztása
Ez a szakasz ismerteti az időfüggő split megvalósításához ajánlott eljárások. Egy időfüggő kétirányú felosztása tanítási és tesztelési között az alábbiakban ismertetjük.

Tegyük fel, az időbélyegzővel események például a különféle érzékelőktől származó mérések adatfolyam. Adja meg a funkciók és címkék a tanítási és tesztelési példák több esemény tartalmazó időkereteknek keresztül. Például a bináris osztályozási funkciói elmúlt események alapján hozzon létre, és címkék alapján az "X" egységet jövőbeli időpontot a jövőbeni események létrehozása (a következő szakaszokban talál a [jellemzőkiemelés](#Feature-engineering) és [modellezése technikák](#Modeling-techniques-applied-to-PdM-use-cases)). A címkézési időkereten egy példa, így később, mint az időkeretet funkcióját származnak.

Időfüggő felosztása, válasszon egy _képzés a megszakítási idő T<sub>c</sub>_  , amikor egy modell betanításához hiperparaméterek lehetőségeire az előzményadatok T akár a<sub>c</sub>. Jövőbeli címkék, amely nem T kiszivárgását elkerülése érdekében<sub>c</sub> a betanítási adatok, válassza ki a legújabb képzési példák címke, x idő előtt T egységek<sub>c</sub>. 7. ábra a példában minden egyes négyzeten egy rekordot a az adatkészlet, ahol funkciók és címkék számítja ki a fent leírt módon. Az ábra bemutatja a rekordokat, amelyek kell képzés és egy tesztelési X = 2 és W = 3:

![7. ábra Bináris osztályozás felosztást időfüggő](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) 7. ábra. Bináris osztályozás felosztást időfüggő

A zöld négyzetes jelölik az időegységek képzéshez használható tartozó bejegyzéseket. Minden egyes képzési példa hozza létre a mérlegeli az elmúlt három funkció létrehozása céljából, és a két későbbi időszakokra vonatkozóan előtt T címkézés<sub>c</sub>. Ha bármely része a két későbbi elszámolási időszakokra van-e túl az T<sub>c</sub>, példa kizárása a tanítási adathalmazt, mert az nem látható-e túl az T feltételezi<sub>c</sub>.

A fekete négyzetes a végső címkézett adatkészlet, amely nem lehet a tanítási adathalmazt, a fenti korlátozás adott a rekordok képviseli. Ezek a bejegyzések nem is használható az adatokat, tesztelést, mivel azok T előtt<sub>c</sub>. Emellett a címkézési időkereteknek részlegesen függenek képzési időkeretet, ideális, amelyek nem. Betanítási és vizsgálati adatokat külön címkézési időkereteknek címke információkat kiszivárgásának megelőzése érdekében kell rendelkeznie.

Az eddig tárgyalt módszer lehetővé teszi a betanításhoz és teszteléshez időbélyege közel T példái között átfedés<sub>c</sub>. Példák a W mértékegységét t kizárása egy megoldást a nagyobb elkülönítése<sub>c</sub> a vizsgálat beállítása. Azonban az ilyen egy agresszív split bőséges adatok rendelkezésre állásától függ.

Használt RUL előrejelzésére regressziós modellek több súlyosan érinti a kiszivárgását probléma. A véletlenszerű split metódussal szélsőséges túlzott méretezés vezet. Regresszió kapcsolatos problémák esetén a felosztás kell lennie, hogy mielőtt T hibákkal rendelkező eszközök tartozó bejegyzéseket<sub>c</sub> lépjen be a gyakorlókészlethez. Rekordok után a megszakítási hibákkal rendelkező eszközök nyissa meg a teszt csoportba.

Vágását meghatározó adatok betanítására és tesztelésére egy másik ajánlott eljárás, amely elágazást idéz eszköz által használandó A felosztás kell, hogy az eszközök a gyakorlókészlethez egyike használatban vannak a modellek teljesítményének tesztelése. Ezzel a megközelítéssel egy modellnek van egy új eszközök realisztikusabb eredmények biztosítása jobb esélyét.

### <a name="handling-imbalanced-data"></a>Imbalanced adatok kezelése
Osztályozási problémák, a további példái egy osztályát, mint a többi, ha az adatkészlet van szólt kell _imbalanced_. Ideális esetben minden egyes osztály a betanítási adatok elegendő képviselői előnyben részesített különböző osztályokhoz megkülönböztetése engedélyezéséhez. Ha egy osztály kevesebb mint 10 %-ban az adatokat, az adatok imbalanced kell tekinteni. Az alulreprezentált osztály egy _kisebbségi osztály_.

Számos PdM problémára ilyen imbalanced adatkészletekhez, ahol egy osztály van súlyosan alulreprezentált képest a másik osztály vagy osztályok között. Bizonyos esetekben a kisebbségi osztály jelenthet az összes adatpont csak 0,001 %. Osztály egyenetlenségének nem egyedi a PdM. A face hasonló problémát, példák, a csalások észlelése és a hálózati behatolások más tartományok, ahol hibák és a rendellenességeket is ritkán fordul elő. Ezek a hibák kisebbségi osztály példák alkotják.

Az osztály egyenetlenségének az adatokat legtöbb szabványos tanulási algoritmusok teljesítményének integritása sérül, mivel minimalizálása érdekében az általános Hibaarány irányulnak. Egy adatkészlet a 99 %-os negatív és pozitív példák 1 % a modell szerint minden példány negatív címkézés 99 %-os pontossággal rendelkezik is megjeleníthetők. De a modellt rosszul fog besorolni összes pozitív példák; így akkor is, ha az adatok pontosságát magas, az algoritmus nem hasznos egy. Ebből következően hagyományos értékelési mérőszámok például _Hibaarány általános pontosságát_ imbalanced tanulási nem elégségesek. Amikor megmérkőzött a imbalanced adatkészleteket, más metrikákkal modell kiértékelése használják:
- Pontosság
- Visszahívás
- F1 pontszámok
- Költség igazított ROC (fogadó működési jellemzőit)

Ezek a metrikák kapcsolatos további információkért lásd: [minta értékelés](#Model-evaluation).

Vannak azonban bizonyos módszereket, amelyek segítenek a osztályba orvosolják egyenetlenségének probléma. A két fő részesítse _mintavételi technikák_ és _költség-és nagybetűket learning_.

#### <a name="sampling-methods"></a>Mintavételi
Imbalanced learning mintavételi módosíthatja a tanítási adathalmazt, elosztott terhelésű adatkészlet módszerek használatát foglalja magában. Mintavételi módszereket nem alkalmazható a teszt-készlethez. Habár számos mintavételi technikák, a legtöbb nagyon egyszerű élvezzenek _véletlenszerű oversampling_ és _alatt mintavételi_.

_Véletlenszerű oversampling_ magában foglalja egy véletlenszerűen vett minta kiválasztása kisebbségi osztályból, ezek a példák replikálásának és tanítási adathalmazt hozzáadását. Ebből következően kisebbségi osztály szereplő példák száma növekszik, és végül a különböző osztályokhoz példák számát egyensúlyba hozni. Oversampling hátránya, hogy egyes példák több példánya okozhat az osztályozó által igénybe vett túl adott, ami túlzott méretezés válik. A modell előfordulhat, hogy megjelenítése nagy képzési pontossága, de lehet, hogy a teszt nem látható adatok teljesítménye optimálisnál.

Ezzel szemben _véletlenszerű alatt mintavételi_ egy véletlenszerűen vett minta kiválasztja a legtöbb osztályt, és ezek a példák eltávolítása a tanítási adathalmazt. Azonban példák eltávolítása a legtöbb osztály okozhat az osztályozó által igénybe vett, hagyja ki a legtöbb osztály kapcsolatos fontos fogalmakat. _Hibrid mintavételi_ ahol kisebbségi osztály túlterhelt a mintavételezett pedig legtöbb osztály alatt-mintát egyszerre ideje-e egy másik életképes megközelítést.

Nincsenek számos kifinomult mintavételi technikákat. A választott módszer az adatok tulajdonságokat és iteratív kísérletek által az adatszakértő eredményeit függ.

#### <a name="cost-sensitive-learning"></a>Költség-és nagybetűket tanulás
A PdM hibák kisebbségi osztály alkotó érdekesek lehetnek több, mint a normál példákat. Így célja főként a hibák az algoritmus teljesítményéről. Egy pozitív osztályba tartozik egy negatív osztály helytelenül előrejelzésének is drágább, mint a fordítva. Ez a helyzet gyakran különböző osztályokhoz említett egyenlőtlen veszteség vagy rosszul Írisz elemek aszimmetrikus költsége. Az ideális osztályozó kell magas előrejelzési pontosság keresztül kézbesítsen a kisebbségi osztály a legtöbb osztály pontosságának kompromisszumok nélkül.

Többféle módon a szabályozásra. Eltérő veszteség a probléma megoldásához, magas költségeket rendel kisebbségi osztály hibás osztályozás, és próbálja meg minimálisra csökkenteni a teljes költség. Algoritmusok, például _SVMs (támogatási vektor gépek)_ természetüknél fogva, elfogadja ezt a módszert a betanítás során meg kell adni a pozitív és negatív példák költségeit azáltal, hogy. Ehhez hasonlóan kiemelési például módszerek _súlyozott döntési fák_ általában bemutatják a jó teljesítmény imbalanced adatokkal.

## <a name="model-evaluation"></a>Modell értékelése
Hibás osztályozás a PdM olyan forgatókönyvekben, ahol üzleti vakriasztások költsége magas jelentős probléma. Például döntést és a egy repülő motor hibát egy helytelen előrejelzésével alapján föld ütemezések zavarja és utazási tervek. Egy gép kapcsolat nélküli módban meglévő véve bevételkiesést vezethet. Ezért fontos modell értékelése a jobb teljesítmény-mérőszámait teszt új adatokkal szemben.

Tipikus teljesítmény-mérőszámok PdM modellek értékeli ki, hogy az alábbiak ismertetik:

- [Pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision) a legnépszerűbb metrika egy osztályozó teljesítmény leírásához használja. De pontossága adatok disztribúciók-és nagybetűket, és hatástalan intézkedés imbalanced adatkészletek a forgatókönyvekhez. Más metrikákkal használja. Eszközök, például a [keveredési mátrixot](https://en.wikipedia.org/wiki/Confusion_matrix) számítási és a modell pontosságát kapcsolatos okból szolgálnak.
- [Pontosság](https://en.wikipedia.org/wiki/Precision_and_recall) PdM-modellek a vakriasztások száma vonatkoznak. A modell pontosságát alacsonyabb általában egy téves riasztásokat nagyobb mértékű felel meg.
- [Idézze](https://en.wikipedia.org/wiki/Precision_and_recall) arány azt jelzi, hogy a hibák a teszt csoport hány sikerült helyesen azonosítani a modellhez. Magasabb visszaírási érvényes jelenti azt, hogy a modell létrejött a valódi hibák azonosítására.
- [F1 pontszám](https://en.wikipedia.org/wiki/F1_score) harmonikus átlaga pontosság és a visszahívás közötti (ajánlott) 1-re (legrosszabb) 0 értékre.

A bináris osztályozási
- [Fogadó görbék (ROC) működő](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) egyben egy népszerű metrikát. ROC-görbét, a modellek teljesítményének értelmezi a egy működési fixpontos a ROC alapján.
- De PdM kapcsolatos problémák esetén _decile táblák_ és _diagramok lift_ konkrétabb. A pozitív osztály (hibák) összpontosíthat, és adja meg, mint a ROC-görbét algoritmus teljesítményének összetettebb képe.
  - _Decile táblák_ hiba valószínűségek egy csökkenő sorrendben tesztelési példák használatával jön létre. A rendezett minták majd deciles vannak csoportosítva (10 %-a legnagyobb valószínűséggel a mintákat, majd 20 %-os, 30 %-át, és így tovább). Az egyes decile az arány (valódi pozitív díj) /(random baseline) segít megbecsülni az egyes decile algoritmus teljesítmény. A véletlenszerű alapkonfiguráció veszi értékek 0.1, 0.2-es és így tovább.
  - [Növekedési diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) jeleníti meg a decile valódi pozitív sebesség és az összes deciles véletlenszerű valódi pozitív aránya. Az első deciles rendszerint a fókuszt az eredmények, mivel megmutatják a legnagyobb előnyökre is szert. Első deciles is látható a "veszélyben", a képviselője a PdM használatakor.

## <a name="model-operationalization-for-predictive-maintenance"></a>A prediktív karbantartáshoz modell operacionalizálás

A data science gyakorlat az előny, hogy csak ha a betanított modell jön létre operatív. Azt jelenti a modell telepíteni kell az üzleti rendszerekbe adatelemzésre új, a korábban látott adatok alapján.  Az új adatok pontosan meg kell felelnie a a _modell aláírás_ a betanított modell két módon:
- minden logikai példányát (például: egy sort a táblázatban) az új adatok szerepelniük kell az összes funkciót.
- az új adatokat előre fel kell dolgozni, és minden funkció fejthetők vissza, pontosan ugyanúgy mint a betanítási adatok.

A fenti folyamat academic és iparági elsajátításához számos módon van megadva. De ugyanazt jelenti a következő utasításokat:
- _Új adatok pontozásához_ a modell használatával
- _A modell a alkalmazni_ új adatokhoz
- _Üzembe helyezés_ a modell
- _Üzembe helyezése_ a modell
- _Futtassa a modell_ új adatokkal szemben

Ahogyan korábban hangsúlyoztuk, a PdM modell operacionalizálás eltér a társaktól. Rendellenességek észlelése és a hibaészlelés általában érintő forgatókönyvek megvalósítása _online pontozási_ (más néven _valós idejű pontozási_). Itt, a modell _pontszámok_ minden bejövő rekordot, és előrejelzést. A rendellenességek észlelése, az előrejelzési azt jelzi, hogy történt-e egy rendellenességet (például: One-Class SVM). A hibák észlelése a típus vagy hiba az osztály lenne.

Ezzel szemben az magában foglalja a PdM _kötegelt pontozási_. Felelnek meg a modell aláírás, az új adatokat az a funkciók a betanítási adatok az azonos módon fejthetők vissza. Jellemző, az új adatok nagy adatkészleteknél funkciók összesített értéket jelenít meg időt a windows és pontozunk a Batch szolgáltatásban. Kötegelt pontozási általában történik az elosztott rendszerek [Spark](http://spark.apache.org/) vagy [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Van néhány alternatívák - mindkét optimálisnál:
- Streamelési adatmotort támogatja az aggregálást keresztül a windows a memóriában. Így sikerült állították, azok támogatási online pontozási. Azonban ezekben a rendszerekben kiválóan alkalmasak a, vagy a ritka elemek keskeny windows sűrű adatok szélesebb körű windows keresztül. Előfordulhat, hogy nem méretezni őket a sűrű adatok számára is szélesebb körű időtartományok keresztül PdM forgatókönyvek látható módon.
- Ha a kötegelt pontozás nem érhető el, a megoldás az, kisebb kötegekben új adatok kezelésének egyszerre online pontozási igazítani.

## <a name="solution-templates-for-predictive-maintenance"></a>A prediktív karbantartási megoldás sablonok

Ez az útmutató az utolsó szakaszában PdM megoldássablonokkal, oktatóanyagok és az Azure-ban megvalósított kísérletek listáját tartalmazza. Ezek az alkalmazások PdM is telepíthető az Azure-előfizetés bizonyos esetekben percen belül. Mint proof-of-concept bemutatókat láthat, próbakörnyezetbe lehetőségeket, vagy tényleges éles megvalósításokhoz gyorsítók kísérletezhet lefordítja használható. Ezek a sablonok találhatók a [Azure AI-katalógusban](http://gallery.azure.ai) vagy [Azure GitHub](https://github.com/Azure). Ezek a különböző minták idővel Ez a megoldássablon be lesz állítva.

| # | Beosztás | Leírás |
|--:|:------|-------------|
| 2 | [Az Azure prediktív karbantartási megoldás – sablon](https://github.com/Azure/AI-PredictiveMaintenance) | Egy nyílt forráskódú megoldás sablon, amely bemutatja a gépi tanulás modellezési és a egy teljes Azure-infrastruktúra képes a prediktív karbantartási forgatókönyveket IoT távoli figyelési környezetében. |
| 3 | [Mélytanulás a prediktív karbantartás](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Egy bemutató megoldással LSTM (hosszú rövid távú memória) hálózatok (ismétlődő Neurális hálózatokat egy osztály) használatával prediktív karbantartás, az Azure Notebooks- [blogbejegyzés erre a példára](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Prediktív karbantartás Fenyegetésmodellezési útmutatója az R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Az r-parancsfájlokkal PdM fenyegetésmodellezési útmutatója|
| 5 | [Az Azure prediktív karbantartás a légi közlekedésben](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Az első PdM alapján az Azure ML v1.0 repülőgép karbantartás megoldás sablonok egyikét. Ez az útmutató adja meg a projekt. |
| 6 | [Az Azure IoT Edge AI-eszközkészlet](https://github.com/Azure/ai-toolkit-iot-edge) | Az IoT edge használatával TensorFlow; a mesterséges Intelligencia eszközkészlet csomagok deep learning-modellek Azure IoT Edge-kompatibilis a Docker-tárolókban, és elérhetővé tehettük ezen modellek REST API.
| 7 | [Az Azure IoT prediktív karbantartási](https://github.com/Azure/azure-iot-predictive-maintenance) | Számítógépek – az Azure IoT Suite előre konfigurált megoldás. Repülőgép karbantartási PdM sablon az IoT Suite használatával. [Egy másik dokumentumba](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) és [forgatókönyv](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) kapcsolatos ugyanazon a projekten. |
| 8 | [SQL Server R Services segítségével prediktív karbantartási sablon](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Bemutatót a fennmaradó hasznos élettartamát forgatókönyv R-szolgáltatások alapján. |
| 9 | [Prediktív karbantartás Fenyegetésmodellezési útmutatója](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Repülőgép karbantartási adatkészlet szolgáltatás fejthetők vissza az R használatával [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) és [adatkészletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) és [Azure Notebooks](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) és [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)az AzureML 1.0-s verzió|

## <a name="training-resources-for-predictive-maintenance"></a>A prediktív karbantartáshoz tanfolyamok forrásai

A Microsoft Azure képzési tervek az alapfogalmakat ismertet, PdM technikák, tartalmat és a mesterséges Intelligencia fogalmak és eljárások általános képzést mellett mögött biztosít.

| Képzési erőforrást  | Rendelkezésre állás |
|:-------------------|--------------|
| [Képzési terv a PdM-fák és véletlenszerű erdő használata](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Nyilvános | 
| [Képzési terv a PdM Deep Learning használatával](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Nyilvános |
| [AI-fejlesztés az Azure-ban](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Nyilvános |
| [A Microsoft AI iskolai](https://aischool.microsoft.com/learning-paths) | Nyilvános |
| [Az Azure AI-tanulás a Githubról](https://github.com/Azure/connectthedots/blob/master/readme.md) | Nyilvános |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Nyilvános |
| [A Microsoft AI Youtube-Webináriumok](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Nyilvános |
| [A Microsoft AI Show](http://channel9.msdn.com/Shows/AI-Show) | Nyilvános |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerek |
| [A Microsoft Partner Network](https://learningportal.microsoft.com) | Partnerek |

Emellett a mesterséges Intelligencia ingyenes MOOC (tömeges nyílt online kurzusok) olyan oktatási intézmények például Stanford és MIT online kínált és más oktatási cégek.
