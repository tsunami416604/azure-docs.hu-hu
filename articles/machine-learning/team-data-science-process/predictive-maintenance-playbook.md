---
title: Az Azure AI-útmutató a prediktív karbantartási megoldás – a csoportos adatelemzési folyamat
description: A data science-megoldás több vertikális iparágak prediktív karbantartási megoldások átfogó leírást.
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
ms.openlocfilehash: 5cf3f02284777a54a9d26cad8a7f3b5b4fa6b335
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087709"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>A prediktív karbantartási megoldásokat az Azure AI útmutatója

## <a name="summary"></a>Összegzés

A prediktív karbantartás (**PDM**) a prediktív elemzések népszerű alkalmazása, amely segítheti a különböző iparágakban lévő vállalkozásoknak a működési költségek magas szintű kihasználtságának és megtakarításának megvalósítását. Ez az útmutató a PdM-megoldások [Microsoft Azure AI platform](https://azure.microsoft.com/overview/ai-platform) technológiával való sikeres fejlesztéséhez és üzembe helyezéséhez szükséges üzleti és elemzési irányelvek és ajánlott eljárások együttes használatát ismerteti.

Márpedig Ez az útmutató bemutatja, iparág-specifikus üzleti forgatókönyvek és a feltételeknek megfelelő ezekben a forgatókönyvekben a PdM folyamatán. A követelményekkel és modellezési technikák PdM megoldásokra is biztosítja. A fő tartalom az útmutató az adatelemzési folyamat – beleértve az adat-előkészítés, a funkciófejlesztési, a modell létrehozásához és a modell operacionalizálás van. Ezek a legfontosabb fogalmak kiegészíteni, ez az útmutató segítségével felgyorsíthatja az alkalmazásfejlesztési PdM megoldássablonok készletét sorolja fel. Az útmutató hasznos képzési segédanyagokat a gyakorlott további információ a mesterséges Intelligencia, a data science mögött is mutat. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science útmutató áttekintése és azon célközönség
Ez az útmutató első felében tipikus üzleti problémák, ezek a problémák megoldása érdekében PdM végrehajtási előnyeit ismerteti, és néhány gyakori felhasználási esetét sorolja fel. Ez a tartalom az üzleti döntéshozók (BDMs) előnyeit. A második felében semmit a data science mögött PdM ismerteti, és ebben az útmutatóban leírt elveket felhasználásával PdM megoldások listáját tartalmazza. Képzési tervek és képzési anyagokat hivatkozásokat is tartalmaz. Szakmai döntéshozók (TDMs), ez a tartalom hasznosnak fogja.

| Kezdje... | Ha Ön... |
|:---------------|:---------------|
| [Üzleti eset a prediktív karbantartáshoz](#business-case-for-predictive-maintenance) |csökkenti az állásidőt és az üzemeltetési költségeket, és javíthatja a berendezések kihasználtsági szeretne üzleti döntéshozó (BDM) |
| [Adatelemzés a prediktív karbantartáshoz](#data-science-for-predictive-maintenance) |technikai döntéshozó (TDM) kiértékelése PdM technológiák megértéséhez egyedi adatfeldolgozás és a prediktív karbantartási AI-követelményei |
| [Megoldási sablonok a prediktív karbantartáshoz](#solution-templates-for-predictive-maintenance)|egy szoftvertervező vagy szeretne gyorsan Felállítható egy bemutatót vagy egy proof-of-concept AI-fejlesztés |
| [Erőforrások betanítása a prediktív karbantartáshoz](#training-resources-for-predictive-maintenance) | a fenti egy részének vagy egészének, és ismerje meg, az adatelemzés, eszközöket és technikákat alapvető fogalmakat szeretné.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó Előfeltételek
A BDM tartalom bármely korábbi data science el, hogy az olvasó nem várja. A TDM tartalom statisztikák és adatok adatelemzési alapszintű ismerete hasznos lehet. Azure Data és a mesterséges Intelligencia szolgáltatásokat, Python, R, XML, és JSON Tudásbázis ajánlott. Mesterséges Intelligencia technikák vannak megvalósítva, a Python- és R-csomagokat. Megoldássablonok vannak megvalósítva, Azure-szolgáltatások, fejlesztői eszközök és SDK-k használatával.

## <a name="business-case-for-predictive-maintenance"></a>A prediktív karbantartáshoz üzleti eset

Vállalatok kritikus berendezésekre a maximális hatékonyság és kihasználtságának nyomon követésével valósíthat meg a visszaadandó tőkebefektetések futniuk van szükség. Ezek az eszközök repülőgép-hajtóművek, turbina, elevator vagy ipari hőmérsékletű – több millió – mindennap berendezések fénymásolókról, kávé gépek vagy Vízhűtők le követelnek egyéb fontos terjedhet.
- Alapértelmezés szerint a legtöbb vállalat a _javító karbantartásra_támaszkodik, ahol a részek a és a meghibásodása esetén is lecserélhetők. Javítási karbantartásnak részek teljesen használt (tehát nem pazarolni arra összetevő élettartama), de a költségeket az üzleti az állásidő, munkaerő és nem tervezett karbantartási követelményeket (ki óra vagy feladatátadáshoz helyek).
- A következő szinten a vállalatok _megelőző karbantartást_végeznek, ahol egy rész hasznos élettartamát határozzák meg, és a hiba előtt megtarthatják vagy lecserélhetik azokat. Megelőző jellegű karbantartási elkerülheti a nem ütemezett és a katasztrofális hibák. Azonban az ütemezett leállás magas költségei, az összetevő kihasználtsága a hasznos élettartama alatt is fennáll, és továbbra is fennáll a munka.
- A _prediktív karbantartás_ célja, hogy optimalizálja az egyensúlyt a javítási és a megelőző karbantartás között az összetevők _időben_ történő cseréje révén. Ez a módszer csak, amelyek közel hibát váltja fel összetevőket. (Ellentétben a megelőző karbantartás) összetevő lifespans kiterjesztésével, és nem tervezett karbantartás és a munka költségek csökkentése (keresztül helyesbítő karbantartás), vállalatok is költséghatékony és versenyelőny.

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

- _adatszakértők_ az adott prediktív problémák elemzéséhez és megoldásához.
- a _Felhőbeli tervezők és fejlesztők_ közösen helyezik üzembe a teljes körű megoldást.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Jogosult a prediktív karbantartási kapcsolatos problémák
Fontos kiemelni, hogy nem minden használati esetek vagy üzleti problémák hatékonyan megoldhatók PdM. Van három fontos feltételeknek megfelelő feltételek, amelyeknek a probléma kiválasztása során figyelembe kell venni:

- A probléma azt kell prediktív jellegű; vagyis kell egy cél- vagy egy előre serkenti az eredményt. A probléma a hibák megelőzése, észlelése esetén végrehajtandó világos és egyértelmű utat is rendelkezhet.
- A problémának szerepelnie kell a _jó és a rossz eredményeket_tartalmazó berendezés működési előzményeivel. Milyen műveleteket hibás eredményeket mérséklésére is biztosítani kell ezeket a rekordokat részeként. Javítsa ki a hibajelentéseket, a teljesítménycsökkenés, karbantartási naplóit, és cserélje le naplókat is fontosak. Emellett javításokat, és helyettesítő rekordok vállalt is hasznosak.
- A rögzített előzményeket a használati eset támogatásához _elégséges_ minőségű _megfelelő_ adatokat kell figyelembe venni. Az adatok relevanciájának és megfelelőségének részletes ismertetését lásd: [az adatkövetelmények a prediktív karbantartáshoz](#data-requirements-for-predictive-maintenance).
- Végül az üzleti tartomány szakértők a probléma egyértelművé kell rendelkeznie. A belső folyamatai és eljárásai tudna segítséget az elemző ismertetése, és az adatok értelmezéséhez tisztában kell lennie. Akkor is tudnia kell, hogy a szükséges módosításokat a meglévő üzleti folyamatokba segítségével kapcsolatos problémák esetén a megfelelő adatok gyűjtése, ha szükséges.

## <a name="sample-pdm-use-cases"></a>Minta PdM alkalmazási helyzetek
Ez a szakasz a PdM alkalmazási helyzetek, például a légi közlekedésben, segédprogramok és szállítási számos iparág gyűjteménye összpontosít. Minden szakasz üzleti problémák megoldására kezdődik, és felvázolja az előnyöket, PdM, a vonatkozó adatokat a környező az üzleti problémát, és végül a PdM-megoldások előnyeit.

| Üzleti probléma | A PdM előnyei |
|:-----------------|-------------------|
|**Légiközlekedési**      |                   |
|A _repülési késés és a lemondás_ mechanikai problémák miatt. Hibák időben nem javítható megszakítandó repülőjáratok okozhatnak, és akadályozza az ütemezés és a műveletek. |PdM megoldások tudja jelezni a valószínűsége annak, légi késleltetett, vagy több vehesse a műszaki hibák miatt megszakadt.|
|_Repülőgép-motor részeinek meghibásodása_: a repülőgép-hajtóművek részei a légitársaságok legáltalánosabb karbantartási feladatai közé tartoznak. Karbantartás megoldás szükséges összetevő készlet rendelkezésre állásának, szállítási és tervezési gondos kezelése|Tudja gyűjteni az eszközintelligencia összetevő megbízhatósági vezet jelentős csökkentését, beruházási költségeket.|
|**Pénzügyi** |                         |
|Az _ATM-hiba_ a bankszektor gyakori problémája. A probléma itt, hogy a jelentés a valószínűsége annak, hogy egy ATM készpénz kivonási tranzakció az készpénz adagolót egy tanulmány és részben vagy egészben hiba miatt beolvasása megszakítva. Tranzakciós hibák előrejelzésen alapuló, adatközponthoz karbantarthatók a proaktív módon megakadályozza, hogy a hiba lépett fel.| Hanem engedélyezi a gép midway keresztül egy tranzakció sikertelen, a kívánatos Ez esetben a program, szolgáltatás megtagadása a gép az előrejelzések alapján.|
|**Energiafogyasztás** |                          |
|_Szélturbinák meghibásodásai_: a szélerőművek a legfontosabb energiaforrások a környezetvédelmi szempontból felelős országokban/régiókban, és nagy beruházási költségekkel járnak. A szélerőművek egyik fő összetevője a Generator motor, amelynek meghibásodása miatt a turbina hatástalan. Emellett akkor is rendkívül költséges javításához.|Előrejelzésére MTTF (átlagos idő) például KPI-k segítségével az energiaszolgáltató vállalatok számára turbina hibák megelőzése, valamint a minimális üzemen kívüli idővel. Hiba valószínűségek figyelésére, amelyek várhatóan hamarosan sikertelen turbina szerelők értesíti, és karbantartási idő-alapú rendszerek ütemezése. Prediktív modelleket adjon meg másik tényező befolyásolja a hiba, amely segítségével a technikusok betekintést jobban megismerheti az esetleges problémák okait.|
|_Áramkör-megszakítói hibák_: a villamos energia és a vállalkozások közötti elosztáshoz a Power Lines szolgáltatásnak mindig működőképesnek kell lennie az energiaellátás biztosításához. Áramkör-megszakítók segítségével korlátozható vagy power sérülésének elkerülése érdekében az időjárási viszonyok során terhelve vagy káros sorokat. Itt az üzleti problémát az áramkör-megszakító előforduló hibák előrejelzésében.| PdM megoldások javítási költségek csökkentése és a gyűjteményszintű az eszközökre, például az áramkör-megszakítók növelése érdekében. Segítik az energiagazdálkodási hálózati minőségének javítása a váratlan meghibásodások és szolgáltatáskiesések csökkentésével.|
|**Szállítás és logisztika** |    |
|_Lift Door-hibák_: a nagyméretű lifttel rendelkező vállalatok teljes stack szolgáltatást biztosítanak a világ számos millió működési felvonója számára. Itt biztonságát, megbízhatóságát és üzemidő olyan ügyfelek számára, akik fő területtel kell foglalkoznunk. Ezek a vállalatok keresztül érzékelők, javítási és megelőző jellegű karbantartással segítségével nyomon követheti ezeket és más jellemzők különböző. Az itt található a legjelentősebb ügyfél probléma itt ajtók hibásan működik. Az üzleti problémát ebben az esetben, hogy egy Tudásbázis prediktív alkalmazás, amely előrejelzi a lehetséges okai ajtó hibák.| Elevator tőkeberuházás potenciálisan egy 20-30 éves időtartama van a rendszer. Így lehet, hogy minden lehetséges értékesítés versenyhelyzetében; ezért a keresésről szolgáltatás és a támogatási. Prediktív karbantartás is ezek a vállalatok a termékeikben versenytársaik használhatóságát és szolgáltatásajánlatok.|
|_Kerekek meghibásodása_: az összes vonat kisiklásának felét és a globális vasúti iparágra vonatkozó díjakért felelős kormány meghibásodása. Kerék hibák is okozhatja a rails romlani, néha okoz a sín túl korán érvényteleníteni. Sín oldaltörések katasztrofális esemény, például derailments vezethet. Az ilyen példányok elkerülése érdekében vasutak kerekek teljesítményének figyeléséhez, és megelőző jellegű módon le kell. Az üzleti problémát itt kerék hibák előrejelzését.| Prediktív karbantartás kerekek segít a just-in-time csere |
|_Földalatti betanítási ajtó hibái_: a földalatti műveletekben a késések jelentős oka az autók betanítása. Itt az üzleti problémát, hogy train ajtó előforduló hibák előrejelzésében.|Korai ismertségét, ajtó hiba, vagy egy ajtó hiba hátralévő napok száma segít az üzleti optimalizálás karbantartási ütemezések ajtó betanításához.|

Ez a szakasz bemutatja, hogyan valósíthat meg a fent ismertetett PdM előnyeit a részletek beolvasása.

## <a name="data-science-for-predictive-maintenance"></a>Adatelemzés a prediktív karbantartáshoz

Ez a szakasz data science alapelvek és eljárás általános útmutatást nyújt a PdM a. Célja, hogy egy TDM, megoldásfejlesztőként segítségével, vagy egy fejlesztői megismerheti az előfeltételeket és a teljes körű AI-alkalmazások PdM kiépítése során. Ez a szakasz a [prediktív karbantartáshoz használható megoldási sablonokban](#solution-templates-for-predictive-maintenance)található demók és próba-koncepciós sablonok áttekintésével együtt olvasható. Ezután használhatja ezeket alapelvek és ajánlott eljárások a PdM-megoldás megvalósítása az Azure-ban.

> [!NOTE]
> Ez az útmutató nem készült, akik az olvasó adatelemzés. Számos hasznos forrást talál a [prediktív karbantartáshoz szükséges erőforrások betanítása](#training-resources-for-predictive-maintenance)című szakaszban. Az útmutatóban felsorolt [megoldási sablonok](#solution-templates-for-predictive-maintenance) néhány ilyen AI-technikát mutatnak be bizonyos PDM problémák esetén.

## <a name="data-requirements-for-predictive-maintenance"></a>Prediktív karbantartás adatok követelményei

Minden tanuló sikeres (a) a mi van folyamatban színesített minőségét, és (b) a learner képességét függ. Prediktív modelleket az előzményadatok minták bemutatása és a jövőbeni következmények előrejelzésére bizonyos valószínűséggel ezeket a megfigyelt mintáknak megfelelően. A modellek prediktív pontossága attól függ, a alkalmazhatóságát, a megfelelősége és a tanítási és tesztelési adatok minőségét. Az új adatok, amelyek "sorolódik" modell használatával ugyanazokat a szolgáltatásokat és séma kell rendelkeznie a tanítási és tesztelési adatként. A szolgáltatás jellemzői (típus, sűrűség, terjesztési és így tovább) az új adatok, amelyek a tanítási és tesztelési adatkészletek egyeznie kell. Az ebben a szakaszban célja az ilyen adatok követelményeinek.

### <a name="relevant-data"></a>A vonatkozó adatokat

Először is fontos, hogy az adatokat meg kell adni _a problémához_. Vegye figyelembe, hogy a fentiekben tárgyalt _kerék-meghibásodási_ eset – a betanítási adatoknak a kerék műveletekkel kapcsolatos funkciókat kell tartalmazniuk. Ha a probléma a _vontatási rendszer_meghibásodásának előrejelzése volt, a betanítási adatmennyiségnek a vontatási rendszer minden különböző összetevőjét ki kell terjednie. Az első esetben egy adott összetevőre célozza meg, míg a második esetben célozza meg, a nagyobb alrendszer hibát. Az általános ajánlás az előrejelzési rendszerek nagyobb alrendszerek helyett az egyes összetevőkkel kapcsolatos tervezési, mivel ez utóbbi fog rendelkezik több elosztott adatok. A tartományi szakértő (lásd: a [prediktív karbantartással kapcsolatos problémák](#qualifying-problems-for-predictive-maintenance)) segítséget nyújt az elemzéshez tartozó legfontosabb alkészletek kiválasztásában. A megfelelő adatforrásokat részletesebben ismertetjük a [prediktív karbantartás adatelőkészítési feladataival](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Elegendő adat
Előzményadatait meghibásodása kapcsolatban gyakran feltett kérdésekre: (1) "hány sikertelen események szükségesek a modell betanításához?" (2) "hány rekord tekintendő" elegendőnek? " Nincsenek végleges válaszok, de csak a hüvelykujj szabályok. (1) a több sikertelen események száma jobb a modell. (2) és sikertelen események pontos száma attól függ, az adatok és a probléma alatt megoldott kontextusában. De a tükrözés oldalon, ha egy gép túl gyakran sikertelen majd az üzleti lecseréli, amely hiba példányok csökkenti. Itt újra, a tartományból szakértői útmutatást fontos. Vannak azonban olyan metódusok, amelyek megbirkóznak a _ritka események_problémájára. Ezeket a rendszer a [kiegyensúlyozatlan adatkezelési](#handling-imbalanced-data)szakaszban tárgyalja.

### <a name="quality-data"></a>Minőségi adatok
Az adatminőség kritikus fontosságú – minden előrejelző attribútum értékének _pontosnak_ kell lennie a cél változó értékével együtt. Adatminőség statisztikák és adatok felügyelete egy jól vizsgált területre, és ezért vette az a jelen útmutató.

> [!NOTE]
> Több erőforrást és vállalati termékek minőségét adatok is van. A hivatkozások minta lejjebb:
> - Dasu, T, a Johnson, a T., felderítő Data szintű adatbányászatra, és az adatok törlését, Wiley, 2003.
> - [Tájékozódási célú adatelemzés, wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, mennyiségi Adattisztítás nagyméretű adatbázisokhoz](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der Loo, M, bevezetés az adattisztításba az R használatával](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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
Egy adott eszköz karbantartási előzményei a lecserélt összetevőkre, a végrehajtott javítási tevékenységekre stb. vonatkozó adatokat tartalmaznak. Ezek az események a romlási mintákat rögzítik. Ez rendkívül fontos információ a betanítási adatok hiányában félrevezető modell eredményeinek vezethet. Sikertelen futtatások előzményei a belül előzmények karbantartási is található a speciális hibakódok vagy rendelés dátumát részei. További adatforrások hiba minták befolyásoló meg kell vizsgálni és tartomány-szakértők által biztosított.

#### <a name="machine-operating-conditions"></a>Gép működési feltételek
Streamelési adatok alapján (vagy más) érzékelő a készülék műveletben egy fontos adatforráshoz. A PdM egyik legfontosabb feltételezése, hogy a gép állapotának romlása az idő múlásával történik a rutin művelet során. Az adatok várható elévülési ezt a mintát, és felismerheti a rendellenességeket, amelyek teljesítménycsökkenést vezet rögzítése időfüggő funkciókat tartalmaznak. A historikus szempont az adatok az algoritmus a hiba és a hibát nem jelentő további idővel szükség. Ezek az adatpontok alapján, az algoritmus megtanulja előrejelzése idő hány több egységet egy gép folytathatja a munkát, mielőtt sikertelen lesz.

#### <a name="static-feature-data"></a>A szolgáltatás statikus adatok
Statikus szolgáltatása, a készülék metaadatait. Példák a készülék típusa, a modell, a gyártó dátumát, indítsa el a szolgáltatás dátuma, a rendszer és az egyéb műszaki specifikációk helyét.

A [minta PDM használati eseteire](#sample-pdm-use-cases) vonatkozó releváns adatokat az alábbi táblázat ismerteti:

| Használati eset | Példák a vonatkozó adatok |
|:---------|---------------------------|
|_Repülési késés és lemondás_ | A légi Repülőút szakaszainak formájában az útvonal-információkat, és naplók lapon. A repülési láb adatai olyan útválasztási részleteket foglalnak magukban, mint a távozás/érkezés dátuma, az idő, a repülőtér, a layovers stb. Az oldal naplójában számos hiba és karbantartási kód található, amelyeket a rendszer a helyszíni karbantartó személyzete rögzített.|
|_Repülőgép-motor részeinek meghibásodása_ | Maradjon a repülő karbantartására, amely információkat tartalmaz a feltétel a különböző részeiből szenzorait gyűjtött adatokat. Karbantartási rögzíti és segít azonosítani összetevő hiba előfordulásakor is szövegelemekben.|
|_ATM-hiba_ | Minden egyes tranzakció (cash/ellenőrzés helyezésével) érzékelőinek és készpénz kiadása. A jegyzetek közötti Térközi méréssel kapcsolatos információk, a vastagság, a Megjegyzés érkezési távolsága, az attribútumok megtekintése stb. Hibakódokat biztosító karbantartási rekordok, javítási információk, a készpénz-adagoló utolsó újratöltésének időpontja.|
|_Szélturbina meghibásodása_ | Az érzékelők monitorozási feltételeit, például a hőmérsékletet, a szél irányát, az áramellátást, a generátor sebességét stb. Az adatok több, különböző régiókban található szélerőműből származnak. Általában minden turbina mérések továbbítását rögzített időközönként több érzékelőinek fog rendelkezni.|
|_Áramkör-megszakító hibái_ | Karbantartási naplók, amely korrekciós, megelőző jellegű, és rendszeres műveleteket tartalmazza. Operatív adatok, amelyek között műveletek esetében például megszakítók küldött automatikus és manuális parancsokat is tartalmaz. Az eszköz metaadatai, például a gyártás dátuma, a hely, a modell stb. Áramkör-megszakító specifikációi, például feszültségi szintek, térinformatikai, környezeti feltételek.|
|_Elevátori ajtó meghibásodása_| Itt metaadatok például itt, gyártott dátum, karbantartás gyakorisága, épület típusa és stb. Operatív információk, például ajtó ciklusokat, átlagos ajtó bezárása idő számát. Sikertelen futtatások előzményei okait.|
|_Kerekek meghibásodása_ | A görgős gyorsítást, a fékezési példányokat, a távolságot, a sebességet stb. mérő adat. Statikus információ a kerekek, például a gyártó, a gyári dátum alapján. Hiba adatok következtetni rész rendelési adatbázisból, amelyek nyomon követik a rendelés dátuma és mennyisége.|
|_Földalatti vonatok meghibásodása_ | Ajtó nyitó és záró idő, egyéb működési adatok, például train ajtók állapotban van. Statikus adatok eszköz azonosítója, idő és feltétel értéke oszlop tartalmazhat.|

### <a name="data-types"></a>Adattípusok
Adja meg a fenti adatforrások, a két fő adattípusok megfigyelhető a PdM-tartományt a következők:

- _Időbeli adatmennyiség_: működési telemetria, gépi feltételek, munkasorrend-típusok, prioritási kódok, amelyeknek időbélyegei lesznek a rögzítés időpontjában. Hiba, a karbantartás és javítás és a használati előzmények is minden eseményhez tartozó időbélyeggel rendelkezik.
- _Statikus adatok_: a gépi funkciók és az operátori funkciók általában statikusak, mivel leírják a gépek vagy a kezelő attribútumainak műszaki specifikációit. Ezek a funkciók módosulhatnak, idővel, ha van továbbá időbélyegeket társítva.

A prediktív és a TARGET változókat előre fel kell dolgozni/át kell alakítani [számszerű, kategorikus és más adattípusokra](https://www.statsdirect.com/help/basics/measurement_scales.htm) a használt algoritmustól függően.

### <a name="data-preprocessing"></a>Adatok előfeldolgozása
A funkciók _mérnöki_előfeltétele, hogy előkészítse a különböző streamekről származó adatok előkészítését egy olyan séma összeállításához, amelyből egyszerűen létrehozhatók szolgáltatások. Megjelenítheti az adatokat először a rekordok táblaként. A táblázat minden sora egy betanítási példányt jelöl, és az oszlopok a _prediktív_ funkciókat (más néven független attribútumokat vagy változókat) jelölik. Rendezze az adathalmazt úgy, hogy az utolsó oszlop (ok) a _cél_ (függő változó) legyen. Minden egyes betanítási példányhoz rendeljen egy _címkét_ az oszlop értékeként.

Időegységek érzékelőktől kapott adatok időtartama osztása historikus adatok esetén. Minden rekordnak egy adott eszközhöz tartozó időegységhez kell tartoznia, _és külön adatokat kell_biztosítania. Időegységek hányszorosa másodperc, perc, óra, nap, az üzleti igényeknek megfelelően vannak meghatározva, hónapok, és így tovább. Az időegységnek _nem kell megegyeznie az adatgyűjtés gyakoriságával_. Ha a gyakoriság értéke nagy, előfordulhat, hogy nem jelennek bármely lényeges különbség egy egységből a másikba. Tegyük fel például, hogy környezeti hőmérséklet gyűjtötte a program 10 másodpercenként. Csak ugyanazon az intervallum használ a betanítási adatok megnöveli a példák száma anélkül, hogy minden további információt. Ebben az esetben egy jobb stratégia lehet az adatok több mint 10 perc vagy egy órán alapuló a szakmai indoklással használja.

Statikus adatok esetében
- _Karbantartási rekordok_: a nyers karbantartási adatok egy adott időpontban végrehajtott karbantartási tevékenységekre vonatkozó adatokat tartalmazó azonosítóval és időbélyeggel rendelkeznek. A karbantartási tevékenységeket _kategorikus_ oszlopokra alakíthatja át, ahol minden kategória leírója egyedi módon képezi le egy adott karbantartási műveletet. Eszköz azonosítója, időpont és karbantartási művelet karbantartási rekordok a séma tartalmazhat.

- _Sikertelen rekordok_: a hibák vagy a meghibásodási okok az adott üzleti feltételek által meghatározott hibakódként vagy meghibásodási eseményként rögzíthetik. Azokban az esetekben, ahol a készülék rendelkezik-e több hibakódok szakértői tartomány kell azonosíthatja azokat, amelyek a célváltozó tárolására. A fennmaradó hibakódokkal vagy feltétellel a hibákkal kapcsolatos _előrejelző_ funkciókat hozhat létre. Hiba rekordok a séma tartalmazhat eszköz azonosítója, ideje, hiba vagy a hiba oka – Ha elérhető.

- _Számítógép-és operátori metaadatok_: egyesítse a gépet és az operátori adatokat egy sémába, hogy az eszközt társítsa a kezelőhöz, valamint a megfelelő attribútumokat. A gép feltételek a séma tartalmazhat eszköz azonosítója, az eszközintelligencia-funkciók, operátor azonosítója és operátor funkciókat.

Az egyéb adatok előfeldolgozási lépései közé tartozik a _hiányzó értékek_ és az attribútumérték _normalizálása_ . Részletes leírásáért lásd az túlmutat a jelen útmutató – tekintse meg a következő szakaszban néhány hasznos hivatkozásokat is.

A fentiekben üzenetfájlrekordok helyen, a végső átalakítása előtt funkciófejlesztési, hogy az eszköz azonosítóját, valamint időbélyegző alapján a fenti táblázatokhoz csatlakozhat adatforrásokhoz. Az eredményül kapott tábla kellene null értékeket azokhoz a hiba oszlop gépen a normál működés esetén. Ezeket az értékeket null is lehet imputált egy mutató a normál működés szerint. A hiba oszlop segítségével címkéket hozhat létre _a prediktív modellhez_. További információ: a [prediktív karbantartás modellezési módszereinek](#modeling-techniques-for-predictive-maintenance)szakasza.

## <a name="feature-engineering"></a>Jellemzőkiemelés
Funkciófejlesztési első lépése előtt az adatok modellezését. Az adatelemzési folyamat szerepkörét [itt ismertetjük](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). A _funkció_ a modell prediktív attribútuma – például a hőmérséklet, a nyomás, a vibráció stb. A PdM esetében a funkciók Mérnöki tevékenysége a gép állapotának a jelentős időtartamon keresztül gyűjtött korábbi adatokkal való kivonását foglalja magában. Ebben az értelemben akkor különböző, például a távoli megfigyelés, a rendellenességek észlelése és a hibaészlelés a társaktól. 

### <a name="time-windows"></a>Idő windows
A távoli figyelés magában foglalja az adott _időpontban_megjelenő események jelentését. Rendellenességek észlelése modellek (pontszám) bejövő adatfolyam jelző rendellenességeket pontok kezdődően az időben értékeli. Adott típusú kell pontok ideje a előforduló hibák osztályozza a hibaészlelés. Ezzel szemben a PdM egy _jövőbeli_időszakon belül a hibák előrejelzését is magában foglalja a _korábbi_időszakokban a gépi viselkedést képviselő funkciók alapján. A PdM idő egyes pontok adatait a szolgáltatás túl zajos prediktív kell. Így az egyes szolgáltatásokhoz tartozó adatpontokat az adatpontok időbeli összevonásával _kell kialakítani._

### <a name="lag-features"></a>Késés funkciók
Az üzleti követelmények meghatározása, hogy milyen a modell előre jelezni az elkövetkező rendelkezik. Viszont, ennek az időtartamnak segít "milyen vissza a modell rendelkezik keresse ki az" meghatározása, hogy ezek az előrejelzések. Ez a "visszakeresés" időszak a _késésnek_, és az ezen a késési időszakban megjelenő funkcióknak _lag-funkcióknak_nevezik. Ez a szakasz bemutatja az adatforrásokból az időbélyegeket és a szolgáltatás létrehozása a statikus adatforrások gyűjteménynévmintája lag jellemzőket. A lag-funkciók jellemzően _számszerű_ jellegűek.

> [!IMPORTANT]
> Az ablak mérete Kísérletezési keresztül határozza meg, és szakértői tartomány segítségével. érdemes véglegesíthető. Azonos csoportosítani a kijelölés és a késés funkciók, az összesítések és a típus a windows-definícióját tartalmazza.

#### <a name="rolling-aggregates"></a>Működés közbeni összesítések
Az egyes rekordok egy eszköz a működés közbeni ablak méretének "W" választja idő egységek száma az összesítések kiszámításához. A lag funkcióit ezután a rendszer a rekord _dátumát megelőző W-pontokkal_ számítja ki. Az 1. ábrán a kék vonal rögzítve az eszközre minden egyes időegység érzékelő értékek megjelenítése. W = 3 méretű ablak fölé funkció érték mozgóátlaga azok jelöl. A gördülő átlagot az összes olyan rekordra számítjuk, amelyben a t<sub>1</sub> (narancssárga) és a t<sub>2</sub> közötti időbélyeg szerepel (zöld színnel). A W értéke általában percben vagy az adatok természetétől függően órában. Azonban az egyes problémák, nagy W (például: 12 hónap) kiadási tud biztosítani az eszköz teljes előzményeit a rekord a időpontig.

![1\. ábra. A működés közbeni összesített funkciók](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

1\. ábra. A működés közbeni összesített funkciók

Működés közbeni összesítések adott időtartamra példák count, average, CUMESUM (halmozott összege) mértékek, minimális/maximális értékei. Ezenkívül eltérés, szórás és N szórás szorzataként túli kiugró értékek száma gyakran használják. Alább láthatók az útmutató [használati eseteire](#sample-pdm-use-cases) alkalmazható összesítések. 
- _Repülési késleltetés_: a hibakódok száma az elmúlt nap/hét során.
- _Repülőgép-hajtóművek meghibásodása_: a gördülő, a szórás és az összeg az elmúlt nap, hét stb. esetében Ezt a metrikát az üzleti tartomány szakértővel együtt kell meghatározni.
- _ATM-hibák_: a működés közbeni középértékek, a medián, a tartomány, a szórások, a kiugró értékek száma a három szóráson felül, a felső és az alsó CUMESUM.
- _Földalatti betanítási hibák_: az elmúlt nap, hét, két hét stb. eseményeinek száma.
- _Áramkör-megszakítói hibák_: az elmúlt hét, év, három év stb. során fellépő hibák száma.

A PdM egy másik hasznos módszer, hogy trend módosításokat, adatforgalmi csúcsokhoz és az adatok rendellenességek észlelése az algoritmusok használatával megváltozik.

#### <a name="tumbling-aggregates"></a>Átfedésmentes összesítések
Egy adott objektum minden címkézett rekordjánál meg van határozva egy " _w-<sub>k</sub>_  " méretű ablak, ahol a _k_ a " _w_" méretű ablakok száma. Az összesítéseket a rendszer a rekordok időbélyegének megkezdése előtti időszakokban hozza létre több mint _k_ -re felváltó _Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_  . a _k_ rövid távú hatások rögzítésére, illetve a hosszú távú romlási minták rögzítésére szolgáló nagy szám lehet. (lásd a 2. ábra).

![2\. ábra Átfedésmentes összesített funkciók](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

2\. ábra Átfedésmentes összesített funkciók

Például lag funkciók, az a szél turbina használati eset lehet, hogy lehet létrehozni a W = 1 és k = 3. A lag minden az elmúlt három havi felső és alsó kiugró értékek használatával azt tartalmazzák.

### <a name="static-features"></a>Statikus funkciók

A berendezés előállítása, a modell számának, a helyre, például a dátum a műszaki specifikációk adunk néhány ötletet statikus funkciókat. Ezeket _kategorikus_ változókként kezelik modellezéshez. Néhány példa az áramkör-megszakító használati esetekhez: feszültség, aktuális, energiakapacitás, típusú átalakító, és az áramforrást. Kerék hibák kulcsszava kerekek (ötvözött vs acélból) típusú csak példaként szolgál.

Az adatok előkészítési erőfeszítéseket eddig tárgyalt folyamatban vannak rendezve, ahogy az alábbi adatokat kell vezethet. Képzés, tesztelési és érvényesítési adatok rendelkeznie kell a logikai séma (Ez a példa bemutatja idő nap egységben).

| Eszközazonosító | Time | \<funkció oszlopai > | Címke |
| ---- | ---- | --- | --- |
| A123 |1\. napi | . . . | . |
| A123 |2\. napon | . . . | . |
| ...  |...   | . . . | . |
| B234 |1\. napi | . . . | . |
| B234 |2\. napon | . . . | . |
| ...  |...   | . . . | . |

A Feature Engineering utolsó lépése a cél változó **címkézése** . Ez a folyamat szolgáltatás a modellezési technika függ. Viszont a modellezési módszer az üzleti probléma megoldására és a rendelkezésre álló adatok jellegétől függ. A következő szakaszban tárgyalt címkézést.

> [!IMPORTANT]
> Adat-előkészítési és funkciófejlesztési feladatok olyan fontos, mint a modellezési módszerek a PdM-megoldások sikeres érkeznek. A tartomány szakértő és az azoknak megfelelő szolgáltatásokat és a modell adatainak díjszabással jelentős mennyiségű időt kell befektetni. Egy kis számos könyvek funkciófejlesztési a mintát az alábbiak:
> - Pyle, adatbányászati (Morgan Kaufmann oktatóanyagokban az adatkezelő rendszerek) 1999 D. adatok előkészítése
> - Zheng, A., Casari, a Machine Learning A. Funkciófejlesztési: elvek és technikák adatszakértők, O'Reilly, 2018.
> - Dong, G. Liu, H. (szerkesztők), a Machine Learning és az adatelemzési funkciók (Chapman & Hall/CRC adatbányászat és a Knowledge Discovery sorozat), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>A prediktív karbantartáshoz modellezési technikák

Ez a szakasz ismerteti a fő modellezési technikák PdM kapcsolatos problémák esetén a megadott címkével konstrukció módszereket együtt. Figyelje meg, hogy a különböző iparágak egyetlen modellezési módszer használható. A modellezés technika párosítva van a data science probléma, mint az adatok csak olyan mélységben kontextusában.

> [!IMPORTANT]
> A hiba eseteket és a címkézési stratégia tartozó címkék kiválasztását  
> meg kell határozni a tartomány szakértői egyeztetve.

### <a name="binary-classification"></a>Bináris osztályozás
A bináris besorolással _megjósolható, hogy egy adott berendezés egy jövőbeli időszakon belül meghibásodik-e, azaz_ az _X jövőbeli horizontját_. Az X-t az üzleti probléma és az ott tárolt adatszolgáltatás határozza meg, a tartományi szakértővel való konzultáció során. Példák:
- az összetevők lecseréléséhez, karbantartási erőforrások üzembe helyezéséhez _minimálisan szükséges idő_ , karbantartási műveletek elvégzése az adott időszakban valószínűleg előforduló probléma elkerüléséhez.
- _az események minimális_ száma, amely a probléma megkezdése előtt fordulhat elő.

Ezzel a technikával a betanítási példákat kétféle jelöli. Egy pozitív példa, _amely egy hibát jelez_a Label = 1 címkével. Egy negatív példának, amely azt jelzi, hogy a normál működés, a címkével ellátott = 0. A cél változó, így a címke értékei _kategorikusak_. A modell azonosítania kell minden egyes új például valószínűleg sikertelen, vagy át a következő X mértékegységét módon működnek.

#### <a name="label-construction-for-binary-classification"></a>A bináris osztályozási címke építése
Itt a kérdést: "Mi a valószínűsége annak, hogy az eszköz meghiúsul a következő X időegységben?" A kérdés, a Címke X rekordokat "készül az nem", az eszköz a meghibásodás előtti választ (címke = 1), és a többi rekord, hogy "normál" címke (címke = 0). (lásd a 3. ábra).

![3\. ábra. Bináris osztályozás címkézését](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

3\. ábra. Bináris osztályozás címkézését

Címkézés stratégia a használati esetek néhány példát alább láthatók.
- _Repülési késések_: X kiválasztható egy nap, a késések előrejelzésére a következő 24 órában. 1, majd minden repülőjáratok, amelyek előtt hibák 24 órán belül vannak ellátva.
- _ATM készpénz_-kiosztási hibák: a következő egy órában egy tranzakció meghibásodási valószínűségét lehet meghatározni. Ebben az esetben az elmúlt órában a hiba történt az összes tranzakciók 1 van jelölve. 1 használatával előrejelzi a hibák valószínűségét keresztül a következő N pénznem megjegyzések mellőzni, törölni, ha nem az utolsó N megjegyzések belül az összes jegyzet van jelölve.
- _Áramkör-megszakítói hibák_: a cél lehet előre jelezni a következő áramkör-megszakító parancs hibáját. Ebben az esetben X van kiválasztva egy jövőbeli parancsot is.
- _Betanítási ajtó meghibásodása_: az X két napra kiválasztható.
- _Szélturbinák-hibák_: az X két hónapra kiválasztható.

### <a name="regression-for-predictive-maintenance"></a>A prediktív karbantartáshoz regresszió
A regressziós modellek _egy adott eszköz hátralévő hasznos élettartamának (RUL) kiszámítására_szolgálnak. Rul-JÉNEK meghatározása az, hogy mennyi ideig egy eszköz működik, mielőtt a következő hiba történik. Az egyes betanítási példa egy olyan rekord, amely egy, az adott objektumhoz tartozó, a _nY_ időegységéhez tartozik, ahol _n_ a többszörös. A modellnek minden új példa RUL kell kiszámítani _folytonos számként_. Ez a szám azt jelzi, hogy az az időtartam, a hiba előtt van hátra.

#### <a name="label-construction-for-regression"></a>A regresszió címke építése
Itt a kérdést: "Mi az a készülék fennmaradó hasznos élettartamának (RUL)?" Az egyes rekordok a meghibásodás előtti kiszámíthatja a címke a következő hiba előtt hátralévő idő egységek számát. Ezzel a módszerrel a címkékhez folyamatos változókat. (Lásd a 4. ábra)

![4\. ábra. Regresszió címkézését](./media/predictive-maintenance-playbook/labelling-for-regression.png)

4\. ábra. Regresszió címkézését

Regresszió, a címkézés történik hivatkozó egy meghibásodási pont. A számítást esetén nem lehet anélkül, hogy mennyi ideig az eszköz ötszáz évet a meghibásodása előtt. Tehát ezzel szemben a bináris osztályozási eszközök az adatok az esetleges hibák nélkül nem használható modellezési. Ezt a problémát a [túlélés elemzése](https://en.wikipedia.org/wiki/Survival_analysis)nevű másik statisztikai módszer tárgyalja. Azonban lehetséges komplikációk ezt a technikát alkalmazásakor érintő időfüggő adatok rendszeres időközönként a PdM-használati esetek fordulhatnak elő. A Survival Analysis szolgáltatással kapcsolatos további információkért tekintse meg [ezt az egy pagert](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>A prediktív karbantartáshoz többcsoportos besorolás
Többcsoportos osztályozási technikák gondoskodnak a PdM-megoldásokban is használható két forgatókönyvet:
- _Kétféle jövőbeli_végeredmény: az első eredmény az adott eszköz _meghibásodásának egy tartománya_ . Az eszköz van rendelve egy több lehetséges időtartamra. A második eredmény az a valószínűsége, hogy egy jövőbeli időszakban hiba történt a _többszörös kiváltó okok egyike_miatt. Az előrejelzési lehetővé teszi, hogy a karbantartó személyzet tünetek és a terv karbantartási ütemezések megtekintéshez.
- Egy adott hiba _legvalószínűbb kiváltó okának_ előrejelzése. Az eredmény a megfelelő készletét az adott karbantartási műveleteket a hiba elhárításához javasolja. Kiváltó okokért és javasolt javítások rangsorolt listáját segítségével a technikusok rangsorolhatja a javítási lépések hiba után.

#### <a name="label-construction-for-multi-class-classification"></a>Többcsoportos besorolási címke építése
A kérdés a következő: "mi a valószínűsége annak, hogy egy eszköz nem fog sikerülni a következő _nZ_ -egységekben, ahol _n_ az időszakok száma?" Ez a kérdés megválaszolásához nZ rekordok gyűjtő idő (3Z, 2Z, a – Z) használatával egy eszköz a meghibásodás előtti címkét. Minden egyéb címkét "normál" rögzíti (label = 0). Ebben a metódusban a TARGET változó a _kategorikus_ értékeket tartalmazza. (Lásd az 5. ábra).

![5\. ábra. A többosztályos besorolásnál fellépő hibák időpontjának előrejelzése](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

5\. ábra. Hiba ideje előrejelzési többcsoportos besorolási címkézés

A kérdés itt látható: "mi a valószínűsége annak, hogy az eszköz a következő X egységben sikertelen lesz a kiváltó ok/probléma miatt. _P<sub>i</sub>_ ?" hol _vagyok_ a lehetséges kiváltó okok száma. A kérdés megválaszolásához X rekordok felirata az eszköz meghibásodása előtt "a hiba oka, hogy a root ok miatt meghiúsul a _p<sub>i</sub>_ " (Label = _P<sub>i</sub>_ ). Minden más rögzíti, hogy "normál" címke (label = 0). Ez a módszer a címkék is kategorikus (lásd a 6. ábra).

![6\. ábra. Kiváltó okok előrejelzési címkéi többosztályos besoroláshoz](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

6\. ábra. Alapvető ok előrejelzési többcsoportos besorolási címkézés

A modell egy hiba valószínűségét rendeli hozzá a _P<sub>i</sub>_  . és a hiba valószínűsége miatt. Ezek valószínűségek, hogy a problémákat, amelyek a legnagyobb valószínűséggel fordulnak elő a jövőben előrejelzését magnitude szerint is rendezve.

Itt a kérdést: "milyen karbantartási műveletek ajánlanak egy meghibásodás után?" A kérdés megválaszolásához a címkézés nem _igényel jövőbeli horizontot_, mivel a modell a jövőben nem jósol előre hibát. Csak a legvalószínűbb kiváltó okot Jósolja meg, _Ha a hiba már megtörtént_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Oktatás, érvényesítése és a prediktív karbantartáshoz tesztelési módszer
A [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) teljes körű lefedettséget biztosít a modell Train-test-validate ciklusával. Ez a szakasz PdM egyedi szempontokat ismerteti.

### <a name="cross-validation"></a>Keresztellenőrzés
A [több érvényesítés](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) célja, hogy Definiáljon egy olyan adatkészletet, amely a modellnek a betanítási fázisban történő "tesztelés" értékre van állítva. Ezt az adatkészletet az _ellenőrzési csoportnak_nevezzük. Ez a módszer segít korlátozni a problémákat, például a túlterhelést, és betekintést nyújt _arról, hogyan_ általánosítja a modell egy független adatkészletet. Azt jelenti egy ismeretlen adatkészlet, amely lehet a problémát. A PdM a képzés és tesztelési rutin jobban általánosításához a jövőbeli nem látható adatok az idő különböző szempontokat figyelembe kell.

Számos gépi tanulási algoritmusok, amelyek jelentős mértékben módosíthatják a modellek teljesítményének hiperparaméterek számos függenek. Ezek hiperparaméterek optimális értékét nem vannak számított automatikusan, a modell betanításakor. Az adatszakértő kell megadni. Többféleképpen is megtalálni a hiperparaméterek helyes értékeket.

A leggyakoribb érték a _k-szor_ , amely a példákat véletlenszerűen felosztja _k_ -re. Az egyes hiperparaméterek beállítása-értékek esetében futtassa a " _k_ " tanulási algoritmust. Minden egyes ismétléskor használjuk a példákban az aktuális modellrészek az érvényesítés, és a példák a többi képzési készletként. Az algoritmus betanítása példák keresztül, és a teljesítmény-mérőszámok számítási érvényesítési példák keresztül. A hurok végén számítjuk ki a _k_ teljesítmény-mérőszámok átlagát. Az egyes hiperparaméter értékek válassza ki az azokat, amelyeket a legjobb átlagos teljesítmény. Hiperparaméterek kiválaszthatják gyakran kísérleti jellegű.

A PdM-problémák adatok, amelyek az adatok több forrásból származó események időbeli adatsoroknál rögzíti. Ezeket a rekordokat a címkézés időpontja szerint rendezhető. Ezért ha az adatkészlet _véletlenszerűen_ van felosztva a betanítási és az ellenőrzési csoportba, _néhány betanítási példa később is előfordulhat, mint néhány érvényesítési példa_. A hiperparaméter-értékek jövőbeli teljesítményét a rendszer a modell betanítása _előtt_ beérkező adatok alapján becsüli. Ezek becsléseket túlságosan optimista, lehet, különösen akkor, ha az idősorozat-nem álló és haladásával idővel. Ennek eredményeképpen a kiválasztott hiperparaméter értékek optimálisnál lehet.

Az ajánlott módszer a példák betanításra és érvényesítésre való felosztása _időfüggő_ módon, ahol az összes ellenőrzési példa később, mint az összes betanítási példa. Az egyes hiperparaméter értékek betanítása az algoritmus a tanítási adathalmazt keresztül. A modell teljesítményének mérése ugyanazzal az ellenőrzési csoporttal. Válassza ki a hiperparaméter értékek azt mutatják be, a legjobb teljesítmény érdekében. Hiperparaméter értékek kiválasztani az train és érvényesítési split eredmény jobban jövőbeli modell teljesítményét, mint a kereszt-ellenőrzési által véletlenszerűen kiválasztott értékekkel.

A kész modell hozhat létre tanulási algoritmus betanítási teljes betanítási adatokon ajánlott hiperparaméter értékeivel.

### <a name="testing-for-model-performance"></a>Modellek teljesítményének tesztelése
A modell a létrehozása után az új adatokat a későbbi teljesítményét becsült szükség. Egy jó a teljesítmény-mérőszám hiperparaméter értékek kiszámítása keresztül az érvényesítési beállítása, vagy egy átlagos teljesítmény-mérőszám kereszt-ellenőrzési alapján kiszámított becslése. Ezek becsléseket gyakran túlságosan optimista történik. Az üzleti gyakran előfordulhat néhány további irányelvek a hogyan szeretnék teszteli a modellt.

A PdM ajánlott módja, ha a példákat a tanítás, az érvényesítés és a tesztelési adatkészletek _időfüggő_ módon kell felosztani. Az összes teszt példák a képzés és érvényesítési példák időben későbbinek kell lennie. A felosztás után a modell generálásához, és a teljesítmény mérésére, a fentebb leírt módon.

Ha idősorozat-helyhez kötött és könnyen kiismerhető, véletlenszerű és időfüggő megközelítések létrehozása hasonló becsléseket jövőbeli teljesítmény. De ha idősorozat-nem helyhez kötött, és/vagy nehezen becsülhető, az időfüggő megközelítés realisztikusabb jövőbeli teljesítmény becslései hoz létre.

### <a name="time-dependent-split"></a>Időfüggő felosztása
Ez a szakasz ismerteti az időfüggő split megvalósításához ajánlott eljárások. Egy időfüggő kétirányú felosztása tanítási és tesztelési között az alábbiakban ismertetjük.

Tegyük fel, az időbélyegzővel események például a különféle érzékelőktől származó mérések adatfolyam. Adja meg a funkciók és címkék a tanítási és tesztelési példák több esemény tartalmazó időkereteknek keresztül. Például a bináris besoroláshoz, a múltbeli események alapján hozzon létre szolgáltatásokat, és hozzon létre címkéket a jövőben az "X" időegységeken belüli jövőbeli események alapján (lásd a [funkciók mérnöki](#feature-engineering) és modellezési technikák című szakaszt). A címkézési időkereten egy példa, így később, mint az időkeretet funkcióját származnak.

Az időfüggő felosztáshoz válasszon ki egy, a modell betanításához szükséges _kiképzési<sub>c</sub> időt_ , amelynek során a hiperparaméterek beállítása-t a korábbi és a t<sub>c</sub>értékekkel hangolják össze. Ha meg szeretné akadályozni, hogy a jövőbeli címkék ne legyenek a betanítási adatmennyiségen felül, válassza ki a legkésőbbi betanítási példákat X egységként<sub>a t</sub> <sub>c</sub>előtt. 7\. ábra a példában minden egyes négyzeten egy rekordot a az adatkészlet, ahol funkciók és címkék számítja ki a fent leírt módon. Az ábra bemutatja a rekordokat, amelyek kell képzés és egy tesztelési X = 2 és W = 3:

![7\. ábra. Bináris osztályozás felosztást időfüggő](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

7\. ábra. Bináris osztályozás felosztást időfüggő

A zöld négyzetes jelölik az időegységek képzéshez használható tartozó bejegyzéseket. Az egyes betanítási példákat a szolgáltatás generációjának utolsó három időszakára, valamint a T<sub>c</sub>előtti címkézésre két jövőbeli időszakra vonatkozóan kell létrehozni. Ha a két jövőbeli időszak bármelyik része meghaladja a T<sub>c</sub>-t, akkor kizárhatja ezt a példát a betanítási adatkészletből, mert nem feltételezhető, hogy a t<sub>c</sub>-t túlmutat.

A fekete négyzetes a végső címkézett adatkészlet, amely nem lehet a tanítási adathalmazt, a fenti korlátozás adott a rekordok képviseli. Ezeket a rekordokat a rendszer nem használja a tesztelési adatokban, mivel azok a T<sub>c</sub>előtt vannak. Emellett a címkézési időkereteknek részlegesen függenek képzési időkeretet, ideális, amelyek nem. Betanítási és vizsgálati adatokat külön címkézési időkereteknek címke információkat kiszivárgásának megelőzése érdekében kell rendelkeznie.

Az eddig tárgyalt eljárás lehetővé teszi az átfedést a betanítási és tesztelési példák között, amelyek időbélyege a T<sub>c</sub>közelében van. A nagyobb elkülönítés elérésére irányuló megoldás a tesztelési készletben lévő T<sub>c</sub> -n belül található W-Time egységekben található példák kizárása. Azonban az ilyen egy agresszív split bőséges adatok rendelkezésre állásától függ.

Használt RUL előrejelzésére regressziós modellek több súlyosan érinti a kiszivárgását probléma. A véletlenszerű split metódussal szélsőséges túlzott méretezés vezet. A regressziós problémák esetén a felosztásnak olyannak kell lennie, hogy az eszközökhöz tartozó rekordok a betanítási készletbe kerüljenek, mielőtt a T<sub>c</sub> bejelentkeznek. Rekordok után a megszakítási hibákkal rendelkező eszközök nyissa meg a teszt csoportba.

Vágását meghatározó adatok betanítására és tesztelésére egy másik ajánlott eljárás, amely elágazást idéz eszköz által használandó A felosztás kell, hogy az eszközök a gyakorlókészlethez egyike használatban vannak a modellek teljesítményének tesztelése. Ezzel a megközelítéssel egy modellnek van egy új eszközök realisztikusabb eredmények biztosítása jobb esélyét.

### <a name="handling-imbalanced-data"></a>Imbalanced adatok kezelése
Besorolási problémák esetén, ha több példa van egy osztályra, mint a többitől, az adathalmazt a rendszer _kiegyensúlyozza_. Ideális esetben minden egyes osztály a betanítási adatok elegendő képviselői előnyben részesített különböző osztályokhoz megkülönböztetése engedélyezéséhez. Ha egy osztály kevesebb mint 10 %-ban az adatokat, az adatok imbalanced kell tekinteni. A kijelölt osztály neve _kisebbségi osztály_.

Számos PdM problémára ilyen imbalanced adatkészletekhez, ahol egy osztály van súlyosan alulreprezentált képest a másik osztály vagy osztályok között. Bizonyos esetekben a kisebbségi osztály jelenthet az összes adatpont csak 0,001 %. Osztály egyenetlenségének nem egyedi a PdM. A face hasonló problémát, példák, a csalások észlelése és a hálózati behatolások más tartományok, ahol hibák és a rendellenességeket is ritkán fordul elő. Ezek a hibák kisebbségi osztály példák alkotják.

Az osztály egyenetlenségének az adatokat legtöbb szabványos tanulási algoritmusok teljesítményének integritása sérül, mivel minimalizálása érdekében az általános Hibaarány irányulnak. Egy adatkészlet a 99 %-os negatív és pozitív példák 1 % a modell szerint minden példány negatív címkézés 99 %-os pontossággal rendelkezik is megjeleníthetők. De a modellt rosszul fog besorolni összes pozitív példák; így akkor is, ha az adatok pontosságát magas, az algoritmus nem hasznos egy. Ennek következtében a hagyományos értékelési mérőszámok, például _a hibák összesített pontossága_ nem elegendő a kiegyensúlyozatlan tanuláshoz. Amikor megmérkőzött a imbalanced adatkészleteket, más metrikákkal modell kiértékelése használják:
- Pontosság
- Visszahívás
- F1 pontszámok
- Költség igazított ROC (fogadó működési jellemzőit)

További információ ezekről a metrikákkal kapcsolatban: [modell kiértékelése](#model-evaluation).

Vannak azonban bizonyos módszereket, amelyek segítenek a osztályba orvosolják egyenetlenségének probléma. A két fő közül a _mintavételi technikák_ és a _költséges tanulás_.

#### <a name="sampling-methods"></a>Mintavételi
Imbalanced learning mintavételi módosíthatja a tanítási adathalmazt, elosztott terhelésű adatkészlet módszerek használatát foglalja magában. Mintavételi módszereket nem alkalmazható a teszt-készlethez. Bár több mintavételi módszer is létezik, a legtöbb közvetlen továbbítás _véletlenszerű túlmintavételezést_ és _mintavételezést_is végez.

_Véletlenszerű túlmintavételezés_ esetén a rendszer kiválasztja a kisebbségi osztályból származó véletlenszerű mintát, replikálja ezeket a példákat, és hozzáadja őket a betanítási adatkészlethez. Ebből következően kisebbségi osztály szereplő példák száma növekszik, és végül a különböző osztályokhoz példák számát egyensúlyba hozni. Oversampling hátránya, hogy egyes példák több példánya okozhat az osztályozó által igénybe vett túl adott, ami túlzott méretezés válik. A modell előfordulhat, hogy megjelenítése nagy képzési pontossága, de lehet, hogy a teszt nem látható adatok teljesítménye optimálisnál.

Ezzel szemben a _mintavétel során véletlenszerűen_ választ egy véletlenszerű mintát a többségi osztályból, és eltávolítja ezeket a példákat a betanítási adatkészletből. Azonban példák eltávolítása a legtöbb osztály okozhat az osztályozó által igénybe vett, hagyja ki a legtöbb osztály kapcsolatos fontos fogalmakat. A _hibrid mintavétel_ , amelyben a kisebbségi osztály a mintavételen kívüli és a többségi osztály alatt van, egy másik életképes megközelítés.

Nincsenek számos kifinomult mintavételi technikákat. A választott módszer az adatok tulajdonságokat és iteratív kísérletek által az adatszakértő eredményeit függ.

#### <a name="cost-sensitive-learning"></a>Költség-és nagybetűket tanulás
A PdM hibák kisebbségi osztály alkotó érdekesek lehetnek több, mint a normál példákat. Így célja főként a hibák az algoritmus teljesítményéről. Egy pozitív osztályba tartozik egy negatív osztály helytelenül előrejelzésének is drágább, mint a fordítva. Ez a helyzet gyakran különböző osztályokhoz említett egyenlőtlen veszteség vagy rosszul Írisz elemek aszimmetrikus költsége. Az ideális osztályozó kell magas előrejelzési pontosság keresztül kézbesítsen a kisebbségi osztály a legtöbb osztály pontosságának kompromisszumok nélkül.

Többféle módon a szabályozásra. Eltérő veszteség a probléma megoldásához, magas költségeket rendel kisebbségi osztály hibás osztályozás, és próbálja meg minimálisra csökkenteni a teljes költség. Az olyan algoritmusok, mint a _SVMs (support Vector machines)_ , ez a metódus önmagában is bevezethető, mivel lehetővé teszi a pozitív és negatív példák bevezetését a képzés során. Hasonlóképpen, a többek között a _megnövelt döntési fákat_ is kihasználó módszerek általában a jó teljesítményt mutatják be a kiegyensúlyozatlan adatokkal.

## <a name="model-evaluation"></a>Modell értékelése
Hibás osztályozás a PdM olyan forgatókönyvekben, ahol üzleti vakriasztások költsége magas jelentős probléma. Például döntést és a egy repülő motor hibát egy helytelen előrejelzésével alapján föld ütemezések zavarja és utazási tervek. Egy gép kapcsolat nélküli módban meglévő véve bevételkiesést vezethet. Ezért fontos modell értékelése a jobb teljesítmény-mérőszámait teszt új adatokkal szemben.

Tipikus teljesítmény-mérőszámok PdM modellek értékeli ki, hogy az alábbiak ismertetik:

- A [pontosság](https://en.wikipedia.org/wiki/Accuracy_and_precision) a legelterjedtebb metrika, amely az osztályozó teljesítményének leírására szolgál. De pontossága adatok disztribúciók-és nagybetűket, és hatástalan intézkedés imbalanced adatkészletek a forgatókönyvekhez. Más metrikákkal használja. Az olyan eszközök, mint a [zavartsági mátrix](https://en.wikipedia.org/wiki/Confusion_matrix) , a modell pontosságának kiszámítására és indoklására szolgálnak.
- A PdM modellek [pontossága](https://en.wikipedia.org/wiki/Precision_and_recall) a téves riasztások arányára vonatkozik. A modell pontosságát alacsonyabb általában egy téves riasztásokat nagyobb mértékű felel meg.
- A [visszahívási](https://en.wikipedia.org/wiki/Precision_and_recall) arány azt jelzi, hogy a modell által jelzett hibák közül hányat helyesen azonosítottak. Magasabb visszaírási érvényes jelenti azt, hogy a modell létrejött a valódi hibák azonosítására.
- Az [F1 pontszám](https://en.wikipedia.org/wiki/F1_score) a pontosság és a visszahívás harmonikus átlaga, amelynek értéke 0 (legrosszabb) és 1 (a legjobb) között van.

A bináris osztályozási
- A [fogadó működési görbék (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) szintén népszerű metrika. ROC-görbét, a modellek teljesítményének értelmezi a egy működési fixpontos a ROC alapján.
- A PdM problémák miatt azonban a _decile táblák_ és a _lift-diagramok_ is részletesebbek. A pozitív osztály (hibák) összpontosíthat, és adja meg, mint a ROC-görbét algoritmus teljesítményének összetettebb képe.
  - A _Decile táblák_ a hibák valószínűségének csökkenő sorrendjében, tesztelési példák használatával jönnek létre. A rendezett minták majd deciles vannak csoportosítva (10 %-a legnagyobb valószínűséggel a mintákat, majd 20 %-os, 30 %-át, és így tovább). Az egyes decile az arány (valódi pozitív díj) /(random baseline) segít megbecsülni az egyes decile algoritmus teljesítmény. A véletlenszerű alapkonfiguráció veszi értékek 0.1, 0.2-es és így tovább.
  - A [lift-diagramok](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) a decile valódi pozitív arányát és az összes deciles véletlenszerű valódi pozitív arányát ábrázolják. Az első deciles rendszerint a fókuszt az eredmények, mivel megmutatják a legnagyobb előnyökre is szert. Első deciles is látható a "veszélyben", a képviselője a PdM használatakor.

## <a name="model-operationalization-for-predictive-maintenance"></a>A prediktív karbantartáshoz modell operacionalizálás

A data science gyakorlat az előny, hogy csak ha a betanított modell jön létre operatív. Azt jelenti a modell telepíteni kell az üzleti rendszerekbe adatelemzésre új, a korábban látott adatok alapján.  Az új adatmennyiségnek pontosan meg kell felelnie a betanított modell _aláírásának_ két módon:
- minden logikai példányát (például: egy sort a táblázatban) az új adatok szerepelniük kell az összes funkciót.
- az új adatokat előre fel kell dolgozni, és minden funkció fejthetők vissza, pontosan ugyanúgy mint a betanítási adatok.

A fenti folyamat academic és iparági elsajátításához számos módon van megadva. De ugyanazt jelenti a következő utasításokat:
- _Új adatértékek kiértékelése_ a modell használatával
- _A modell alkalmazása_ új adatértékekre
- A modell _működővé tenni_
- A modell _üzembe helyezése_
- _A modell futtatása_ új adatértékekkel

Ahogyan korábban hangsúlyoztuk, a PdM modell operacionalizálás eltér a társaktól. A rendellenességek észlelését és a hibák észlelését érintő forgatókönyvek jellemzően az _online pontozást_ (más néven _valós idejű pontozást_) implementálják. Itt _a modell minden_ bejövő rekordot leolvas, és egy előrejelzést ad vissza. A rendellenességek észlelése, a nevezzük azt jelzi, hogy történt-e egy rendellenességet (Példa: egy szintű SVM). A hibák észlelése a típus vagy hiba az osztály lenne.

Ezzel szemben a PdM a _Batch pontozást_is magában foglalja. Felelnek meg a modell aláírás, az új adatokat az a funkciók a betanítási adatok az azonos módon fejthetők vissza. Jellemző, az új adatok nagy adatkészleteknél funkciók összesített értéket jelenít meg időt a windows és pontozunk a Batch szolgáltatásban. A kötegelt pontozás általában olyan elosztott rendszerekben történik, mint a [Spark](https://spark.apache.org/) vagy a [Azure batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Van néhány alternatívák - mindkét optimálisnál:
- Streamelési adatmotort támogatja az aggregálást keresztül a windows a memóriában. Így sikerült állították, azok támogatási online pontozási. Azonban ezekben a rendszerekben kiválóan alkalmasak a, vagy a ritka elemek keskeny windows sűrű adatok szélesebb körű windows keresztül. Előfordulhat, hogy nem méretezni őket a sűrű adatok számára is szélesebb körű időtartományok keresztül PdM forgatókönyvek látható módon.
- Ha a kötegelt pontozás nem érhető el, a megoldás az, kisebb kötegekben új adatok kezelésének egyszerre online pontozási igazítani.

## <a name="solution-templates-for-predictive-maintenance"></a>A prediktív karbantartási megoldás sablonok

Ez az útmutató az utolsó szakaszában PdM megoldássablonokkal, oktatóanyagok és az Azure-ban megvalósított kísérletek listáját tartalmazza. Ezek az alkalmazások PdM is telepíthető az Azure-előfizetés bizonyos esetekben percen belül. Mint proof-of-concept bemutatókat láthat, próbakörnyezetbe lehetőségeket, vagy tényleges éles megvalósításokhoz gyorsítók kísérletezhet lefordítja használható. Ezek a sablonok a [Azure AI Gallery](https://gallery.azure.ai) vagy az [Azure githubon](https://github.com/Azure)találhatók. Ezek a különböző minták idővel Ez a megoldássablon be lesz állítva.

| # | Cím | Leírás |
|--:|:------|-------------|
| 2 | [Azure prediktív karbantartási megoldás sablonja](https://github.com/Azure/AI-PredictiveMaintenance) | Egy nyílt forráskódú megoldási sablon, amely bemutatja az Azure ML-modellezést és egy teljes körű Azure-infrastruktúrát, amely képes a prediktív karbantartási forgatókönyvek támogatására a IoT távoli monitorozása kontextusában. |
| 3 | [Mélyreható tanulás a prediktív karbantartáshoz](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure notebook egy bemutató megoldással, amely a LSTM (hosszú rövid távú memória) hálózatokat (az ismétlődő neurális hálózatok osztályát) használja a prediktív karbantartáshoz, egy [blogbejegyzésben a mintában](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Prediktív karbantartási modellezési útmutató az R-ben](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Az r-parancsfájlokkal PdM fenyegetésmodellezési útmutatója|
| 5 | [Azure prediktív karbantartás a repülőgépipar számára](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Az első PdM alapján az Azure ML v1.0 repülőgép karbantartás megoldás sablonok egyikét. Ez az útmutató adja meg a projekt. |
| 6 | [Azure AI-eszközkészlet a IoT Edgehoz](https://github.com/Azure/ai-toolkit-iot-edge) | AI a IoT Edge a TensorFlow használatával; Az eszközkészlet részletes tanulási modelleket biztosít Azure IoT Edge-kompatibilis Docker-tárolókban, és a modelleket REST API-ként teszi elérhetővé.
| 7 | [Azure IoT prediktív karbantartás](https://github.com/Azure/azure-iot-predictive-maintenance) | Számítógépek – az Azure IoT Suite előre konfigurált megoldás. Repülőgép karbantartási PdM sablon az IoT Suite használatával. [Egy másik](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) , ugyanahhoz a projekthez kapcsolódó dokumentum és [útmutató](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) . |
| 8 | [Prediktív karbantartási sablon SQL Server R Services használatával](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Bemutatót a fennmaradó hasznos élettartamát forgatókönyv R-szolgáltatások alapján. |
| 9 | [Prediktív karbantartási modellezési útmutató](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | A repülőgép-karbantartási adatkészlet az R [használatával kísérletezik](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) és [adatkészletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) , valamint [Azure notebookok](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) és [kísérletek](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) a AzureML 1.0-s verziójában|

## <a name="training-resources-for-predictive-maintenance"></a>A prediktív karbantartáshoz tanfolyamok forrásai

A Microsoft Azure képzési tervek az alapfogalmakat ismertet, PdM technikák, tartalmat és a mesterséges Intelligencia fogalmak és eljárások általános képzést mellett mögött biztosít.

| Képzési erőforrást  | Rendelkezésre állás |
|:-------------------|--------------|
| [Képzési terv a PdM a fák és a véletlenszerű erdő használatával](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Nyilvános | 
| [Képzési terv a PdM a Deep learning használatával](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Nyilvános |
| [AI-fejlesztő az Azure-ban](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Nyilvános |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Nyilvános |
| [Azure AI learning a GitHubról](https://github.com/Azure/connectthedots/blob/master/readme.md) | Nyilvános |
| [LinkedIn learning](https://www.linkedin.com/learning) | Nyilvános |
| [Microsoft AI YouTube-előadások](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Nyilvános |
| [Microsoft AI-show](https://channel9.msdn.com/Shows/AI-Show) | Nyilvános |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partnerek |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partnerek |

Emellett a mesterséges Intelligencia ingyenes MOOC (tömeges nyílt online kurzusok) olyan oktatási intézmények például Stanford és MIT online kínált és más oktatási cégek.
