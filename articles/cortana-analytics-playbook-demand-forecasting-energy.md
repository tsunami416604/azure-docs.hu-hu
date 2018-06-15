---
title: A Cortana Intelligence megoldás sablon alkalmazástervezési az igény szerinti előrejelzési energia |} Microsoft Docs
description: A Microsoft a Cortana Intelligence, amelyek segítségével igény szerint egy energia segédprogram vállalat előrejelzési megoldás sablon.
services: cortana-analytics
documentationcenter: ''
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23846429"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>A Cortana Intelligence megoldás sablon alkalmazástervezési energia előrejelzési igény szerint
## <a name="executive-summary"></a>Vezetői összefoglaló
Az elmúlt néhány évben az eszközök internetes hálózatát (IoT), a másik energia források és a big Data típusú adatok egyesített rendelkező túlnyomó lehetőségek segédprogram és energia tartományban létrehozására. Egy időben a segédprogram és a teljes energia szektort láthatta egybesimítását a fogyasztók jobb módjának energia használatát igénylő felhasználási. Emiatt az segédprogram és intelligens rács vállalatok is nagy szükség van a innovációját, majd megújítani magukat. Ezenkívül számos energiagazdálkodási és segédprogram rácsok egyre elavult és nagyon költséges, kezelése és kezeléséhez. Az elmúlt év során a csapat a energia tartományon belüli kapcsolattartás során számos dolgozott. A kapcsolattartás során közben történt, amelyben a segédprogramok vagy a független szoftverszállítók (független szoftvergyártók) rendelkezik van szüksége az előrejelzés a jövőbeli energia igény szerint sok esetben. Az ilyen előrejelzések fontos szerepet játszanak az jelenlegi és jövőbeli üzleti, és a különböző használati esetek alapját váltak. Ezek közé tartozik a rövid és hosszú távú power terhelés előrejelzés, kereskedelmi, terheléselosztás, rács optimalizálás stb. Big Data típusú adatok és a speciális elemzés AA módszerek, például a Machine Learning (ML) a kulcs enablers pontos és megbízható előrejelzések készítésére.  

Ez a forgatókönyv a azt helyreállíthatósága az üzleti és analitikai irányelvek sikeres fejlesztői szükséges, és energia igény szerinti telepítését előrejelzési megoldás. Javasolt iránymutatás segítséget, segédprogramok, az adatelemzők és az adatok mérnökök teljesen operationalized, felhőalapú, igény szerinti előrejelzés megoldások kialakítása során. A vállalatok számára, a big Data típusú adatok és a speciális elemzés út csak indító ilyen megoldást jelenthet a hosszú távú intelligens rács stratégiai kezdeti leképezésben.

> [!TIP]
> A diagram, amely ezt a sablont az architektúra áttekintése letöltéséhez lásd: [Cortana Intelligence Megoldássablonban architektúra az igény szerinti előrejelzési energia](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a business, az adatok és a Cortana Intelligence használatával és az adott Azure Machine Learning (AML) megvalósítása és havi előrejelzési megoldások üzembe helyezésére műszaki szempontjait. A dokumentum három fő részből áll:  

1. Üzleti ismertetés  
2. Adatok ismertetése  
3. Technikai kivitelezés

A **üzleti ismertetése** rész ismerteti az üzleti aspektus megértéséhez, valamint befektetési döntés előtt fontolja meg egy kell. Ismerteti, hogyan ahhoz, hogy az üzleti probléma az elvégzendő annak érdekében, hogy a prediktív elemzés és a gépi tanulás valóban hatékony és alkalmazható. A dokumentum további gépi tanulási és felhasználásukról energia-előrejelzés problémák megoldására használatának alapjait ismerteti. Azt ismerteti, az Előfeltételek és a használati esetek minősítési feltétel. Néhány példa használja esetek és üzleti eset forgatókönyvek is megadja.

A adata gépi tanulási megoldást a főbb összetevő. A **adatok ismertetése** Ez a dokumentum része ismertet néhány fontos szempont, az adatok. Azt ismerteti, hogy milyen típusú adatokhoz, energia előrejelzés, minőségi követelményeit, és milyen adatforrásokat általában létezik. Azt is ismertetik a nyers adatok használatáról az adatokkal kapcsolatos funkciókkal, amely a modellezési rész ténylegesen meghajtó előkészítése.

A harmadik részét a dokumentum ismerteti a **technikai kivitelezés** szempontja, hogy a megoldás. A szolgáltatás műszaki osztály és modellezési alapját képezi az tudományos folyamat, és részletesen bemutatható ezért tárgyalt. A webes szolgáltatások, amelyek egy fontos vehicle prediktív elemzési megoldások felhőben telepítését fogalma magában foglalja. Azt is szerkezeti egy tipikus architektúra egy végpontok közötti operationalized megoldás.

A dokumentum tartalmazza továbbá referenciaanyag ahhoz, hogy további ismertetése a tartomány és a technológia használható.

Fontos megjegyezni, hogy azt nem kíván foglalni a dokumentum az mélyebb tudományos folyamat, a matematikai és műszaki szempontjait. A részletek megtalálhatók a [Azure ML dokumentáció](http://azure.microsoft.com/services/machine-learning/) és [blogok](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Célközönség
A jelen dokumentum célközönsége üzleti és a technikai tanácsadási csoporthoz ki szeretné, hogy megismerje, és ismeri a Machine Learning-alapú megoldások, és hogyan ezeket használják-e kifejezetten a havi előrejelzési tartományon belül.

Adatszakértőkön is kihasználhassa a dokumentum ahhoz, hogy a magas szintű folyamata, amelyek a központi telepítés egy megoldás előrejelzés energia meghajtók jobb megértése olvasva. Ebben a környezetben is használat jó meghatározásához és több kiindulási pont részletes, és speciális anyagot.

### <a name="industry-trends"></a>Trendek
Az elmúlt néhány évben IoT, a másik energia források és a big Data típusú adatok egyesített rendelkező a segédprogram és energia területen hatalmas lehetőségek létrehozására. Egy időben a segédprogram és a teljes energia szektorok láthatta egybesimítását a fogyasztók jobb módjának energia használatát igénylő felhasználási.

Számos segédprogram és intelligens energiavállalatok pioneering a [intelligens rács](https://en.wikipedia.org/wiki/Smart_grid) használja több telepítésével esetek, amelyek a rács által létrehozott adatok használata. Az elektromos áram belső tulajdonságai köré szerveződik sok használati esetek: nem lehet halmozott sem készlet fenntartott tárolja. Igen milyen hozzák kell használni. Hatékonyabban kívánt segédprogramok kell energiafogyasztását egyszerűen előrejelzési mert, amely ad nekik a nagyobb lehetőséget **ellátásának és igényének**, megelőzve az energia veszteség **üvegházhatású gáz csökkentése kibocsátási**, és a költségek szabályozásához.

Ha van szó, a költségek, akkor egy másik fontos szempont, amely ár. Nagy szükség van az új képességek között segédprogramok kereskedelmi léptetik **előrejelzési jövőbeli igények és az elektromos áram jövőbeli ár**. Ez segít a vállalatoknak az éles kötetek meghatározása.

A "intelligens" szó használatakor ténylegesen hivatkozunk, amely majd előrejelzésekhez, és ismerje meg a rács. Azt is, amelyek várhatóan fogyasztás határozza változásainak, valamint **várható ideiglenes túlterhelés olyan helyzetekben, és az automatikus igazítása**. Távolról szabályozásához fogyasztás (ezek intelligens mérőszámok segítségével), amelyet honosított túlterhelési olyan helyzetekben lehet kezelni. **Először előrejelzésére és majd működő**, a rács teszi magát intelligensebb adott idő alatt.

Ez a dokumentum egy adott családba tartozó fedik le a jövőbeli, előrejelzés használati esethez tárgyaljuk a többi rövid távú és hosszú távú energia igény szerint. Jelenleg néhány hónapig évi működik, és szereztek néhány Tudásbázis és szakértelem, amelyek lehetővé teszik a számunkra, iparági osztályú eredmények eredményezett. Más használati eseteket tárgyalja, valamint a dokumentum a közeljövőben.

## <a name="business-understanding"></a>Az üzleti igények felmérése
### <a name="business-goals"></a>Üzleti céljaihoz
A **energia bemutató** célja annak bemutatása egy tipikus prediktív elemzési és a gépi tanulási megoldás, amely egy nagyon rövid időkereten belül is telepíthető. Pontosabban az aktuális elsősorban energia igény szerinti előrejelzési megoldások engedélyezése, hogy az üzleti értékét gyorsan kell is tudjuk és esetén alkalmazhatók. Ez a forgatókönyv is nyújt segítséget az ügyfél a következő célok parancsokról:

* Gépi tanulás értékének rövid ideig alapú megoldás
* Bővíthető egy próba-és nagybetűhasználattal más használati eseteket, vagy az üzleti igények alapján szélesebb körű hatókör
* Gyorsan hozzáférni a Cortana Intelligence Suite termékismeretek

Az ezen célok szem előtt a forgatókönyv célja, hogy az üzleti és műszaki ismereteket, amely segít ezen célok eléréséhez.

### <a name="power-load-and-demand-forecasting"></a>Energiagazdálkodási terhelés és igény szerint előrejelzés
A energia szektort belül lehetnek az sokféleképpen mely igény szerinti előrejelzés segíthet kritikus üzleti problémák megoldásához. Valójában igény szerinti előrejelzés lehet tekinteni a sok alapvető használati esetek az iparág alapját. Általában az adatraktárakban az energia igény szerinti előrejelzések kétféle: rövid távú és hosszú távú. Minden egyes előfordulhat, hogy más célt szolgálnak, és egy másik módszert használják. A fő különbség a kettő között a előrejelzési horizon, tehát az időtartományt a jövőben, amelynek azt kellene előrejelzési.

#### <a name="short-term-load-forecasting"></a>Rövid távú terhelés előrejelzése
Energia igény szerinti keretén belül a rövid távú betölteni az előrejelzés (STLF) összesített be, hogy a rács (vagy a rács egész) különböző részein a közeljövőben van előrejelzett típusúként van definiálva. Ebben a környezetben a rövid távú idő horizon 1 óra 24 óra közötti tartományba kell van definiálva. Bizonyos esetekben egy horizon 48 órán keresztül is lehetőség. STLF ezért gyakran előfordul, a rács egyik működési használata esetében. Íme néhány példa a használati esetek vezérelt STLF:

* Terheléselosztás készletek és igények
* Energiagazdálkodási kereskedelmi támogatása
* Piaci elvégzése (a beállítás kiemelt ár)
* Rács működési optimalizálása
* [Igény szerinti válasz](https://en.wikipedia.org/wiki/Demand_response)
* Igény szerinti előrejelzés maximális
* Igény szerinti ügyféloldali kezelése
* Terheléselosztás és megelőzési túlterhelés
* Hosszú távon terhelés előrejelzése
* Hiba és anomáliadetektálási észlelése
* Csúcsidőszak megszorítás/simítás 

STLF modell főként a near múltbeli (utolsó nap vagy hét) adatokkal és -felhasználási előrejelzett hőmérséklet, egy fontos előrejelzőjének. A következő órában előrejelzésének pontos hőmérséklet beszerezni, majd be 24 óra egyre nagyobb kihívást most nap. Ezek a modellek kevesebb érzékenyek határozza minták vagy hosszú távú fogyasztási trendjeinek a.

SLTF megoldásokat is valószínűleg nagy mennyiségű előrejelzés hívások (Szolgáltatáskérés), mert azok vannak meghívott óránként, és egyes esetekben még nagyobb gyakorisággal. Egyúttal nagyon gyakori, ahol minden egyes állomás vagy átalakító önálló modellként szerepel, és ezért a mennyiségi előrejelzés kérelmek még nagyobb beültetésre megjelenítéséhez.

#### <a name="long-term-load-forecasting"></a>Hosszú távon terhelés előrejelzése
A hosszú távú terhelés előrejelzés (LTLF) célja egy több hónapra (és egyes esetekben az évek) 1 hét közötti idő horizon az előrejelzések power igény szerint. Ezt a tartományt az időhatár többnyire alkalmazható a tervezési és befektetési használati esetekben.

Hosszú távú forgatókönyvek esetén fontos kiváló minőségű adatai, amely lefedi a span több éves (minimális 3 év). Ezek a modellek fog általában minták szezonalitás értékének kinyerése a korábbi adatok és lehetővé teszik a külső predicators például időjárása és a környezet minták szerint.

Fontos, hogy elmagyarázza, hogy minél hosszabb a előrejelzési horizon, annál kevésbé pontos lehet az előrejelzés. Ezért fontos bizonyos abban, hogy intervallumok együtt, amely lehetővé teszi az emberek azokat a tervezési folyamat lehetséges variation számításba a tényleges Előrejelzés létrehozásához.

Mivel a fogyasztás forgatókönyvhöz, amelyben LTLF többnyire azt tervezi, azt várható sokkal alacsonyabb előrejelzés kötetek (képest STLF). Azt a képi megjelenítés eszközök, például Excel vagy a Power bi beilleszthető előrejelzéseket általában jelenik meg, és a felhasználó manuálisan elindítható.

### <a name="short-term-vs-long-term-prediction"></a>Rövid távú vs. Hosszú távú előrejelzés
A következő táblázat a legfontosabb attribútumok tekintetben STLF és LTLF hasonlítja össze:

| Attribútum | Rövid távú terhelés előrejelzése | Hosszú távon terhelés előrejelzés |
| --- | --- | --- |
| Horizon előrejelzés |Az 1 óra, 48 óra |1 – 6 hónapos |
| Adatok granularitási |Óradíj |Óránként vagy naponta |
| A tipikus használati esetek |<ul><li>Igény szerinti/ellátási terheléselosztás</li><li>Válassza ki az óra előrejelzés</li><li>Igény szerinti válasz</li></ul> |<ul><li>Hosszú távon tervezési</li><li>Rács eszközök tervezése</li><li>Erőforrás tervezése</li></ul> |
| Tipikus előre |<ul><li>Nap vagy hét</li><li>Nap órája</li><li>Óránkénti hőmérséklet</li></ul> |<ul><li>Hónap év</li><li>Hónap napja</li><li>Hosszú távú hőmérséklet és a környezet</li></ul> |
| Előzményadatokat tartomány |Két-három év értékelésével |5 – 10 évre visszamenőleg értékelésével |
| Tipikus pontossága |MAPE * 5 % vagy alacsonyabb |MAPE * 25 %-os vagy alacsonyabb |
| Előrejelzési gyakorisága |Óránként vagy 24 óránként |Miután a havi, negyedéves és éves előállított |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – témakörök átlagos százalékos hiba

Mivel ebben a táblázatban is látható, fontos meglehetősen megkülönböztetni a rövid és a hosszú távú előrejelzés forgatókönyvek, mivel határoz meg különböző üzleti igényeknek és különböző központi telepítési és felhasználási minták rendelkezhetnek.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Példa használati eset 1: eSmart rendszerek – túlterhelési optimalizálása
A fontos szerepet egy [intelligens rács](https://en.wikipedia.org/wiki/Smart_grid) dinamikusan és folyamatosan optimalizálása és a változó fogyasztási igazíthatja. Energiafogyasztás befolyásolhatja, főleg hőmérséklet ingadozását által okozott rövid távú módosításokkal (*pl.*, nagyobb teljesítmény vezeték nélkül regisztrálja az állapot vagy a fűtésrendszerek használatos). Egy időben a hosszú távú trendek energiafogyasztását is befolyásol. Ezek közé tartozhatnak szezonalitás értékének hatások, nemzeti ünnepek, hosszú távú használat növekedés és fogyasztói index, olaj ár és GDP akár gazdasági tényezők.

A használati eset a [eSmart](http://www.esmartsystems.com/) egy felhőalapú megoldás, amely lehetővé teszi, hogy a rács az összes megadott állomás egy túlterhelési helyzet propensity előrejelzésére telepíteni kívánta. Különösen eSmart szeretett volna, amelyek valószínűleg úgy azonnali intézkedésre lehet hozni elkerüléséhez vagy megoldásához ilyen esetben a következő órán belül túlterhelés alállomások azonosítása.

Pontos és gyors a előrejelzésének végrehajtása szükséges három prediktív modelleket végrehajtását:

* Hosszú távú modell, amely lehetővé teszi, hogy a következő néhány hét és hónap során minden egyes állomás a energiafogyasztás előrejelzés
* Rövid távú modell, amely lehetővé teszi a következő órán belül egy adott állomás túlterhelési helyzet előrejelzését
* Hőmérséklet-modell, amely több forgatókönyv keresztül jövőbeli hőmérséklet előrejelzés

A cél a hosszú távú modell által a innovációkká alakuljon során a következő heti vagy havi (a megadott a power transmission kapacitásának) túlterhelni a alállomások rangsorolja. Ez lehetővé teszi, hogy a rövid távú előrejelzés bemenetként szolgál alállomások rövid listáját létrehozását. Mivel hőmérséklet egy fontos előrejelzőjének a hosszú távú modell, szükség van a folyamatosan több forgatókönyv hőmérséklet előrejelzések előállításához, és azokat a hosszú távú modell bevitt hírcsatorna. A rövid távú előrejelzés majd meghívott megjósolható melyik állomás várhatóan a következő órán keresztül túlterhelést.

A rövid és hosszú távú modellek minden állomás egy külön-külön vannak telepítve. Ezért a gyakorlati ezek a modellek végrehajtásához kiterjedt vezénylési. Ahhoz, hogy magasabb előrejelzés pontosságát rövid távon, a részletesebb modellek esetében minden nap van kijelölve. Ezek a modellek hajtja végre a rendszer minden órában, és fejezze be a végrehajtása néhány percig, hogy elég ideje, hogy válaszol, és megelőző műveletek végrehajtása, ha szükséges belül. Ez a témakörgyűjtemény a modellek frissül folyamatosan időszakos megőrzési a legfrissebb adatok használatával.

További információ a használati eset található [Itt](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Használja a nagybetűk minősítési feltételek – Előfeltételek
A fő Cortana Intelligence erőssége üzembe helyezését és méretezését a machine learning-központú megoldások hatékony képessége. Több ezer támogatásához párhuzamosan végrehajtott előrejelzések eltéréseinek tervezték. Az automatikusan át tudja méretezni a változó fogyasztás minta teljesítéséhez. A megoldás ezért elsősorban pontosságát és számítási teljesítményt. Például a vállalat olyan pontos energia igény szerint a következő órában, illetve a nap óránként előrejelzési előállító iránt érdeklődik. Másrészről, azt is kevesebb, az igény szerinti miért van előre jelezni kell lennie, mert az a kérdés megválaszolásához (a modellben kezeli, amely).

Ezért fontos vegye figyelembe, hogy nem minden esetben használja, és üzleti problémák hatékonyan megoldhatók gépi tanulás használatával.

A Cortana Intelligence és a gépi tanulás lehet nagyon hatékony egy adott üzleti probléma megoldásához az alábbi feltételek teljesülése esetén:

* Az aktuális üzleti probléma **prediktív** jellegűek. Egy prediktív használata eset például, amelyeket szeretne a következő órán belül egy adott állomás power terhelése előrejelzése vállalat. Másrészről, elemzése és illesztőprogramok korábbi igényű prioritás lenne **leíró** jellegű, ezért kevesebb alkalmazható.
* Nyilvánvaló **elérési útja művelet** kell fordítani az előrejelzés elérhetővé válik. Például a következő órán belül az egy állomás túlterhelés előrejelzésére is elindíthatja egy adott állomás társított terhelés csökkentését, és így potenciálisan megakadályozza az olyan túlterhelést proaktív műveletet.
* A használati eset jelöli egy **tipikus típusú probléma** úgy, hogy ha orvosolhatók azt is váltaniuk más hasonló elhárításához használati esetekben.
* Az ügyfél állíthatja be **mennyiségi és minőségi célok** a sikeres megoldás megvalósításának bemutatása. Például az energia igény szerinti előrejelzés helyes mennyiségi cél lenne a szükséges pontosságot küszöbértéket (*pl.*, 5 % hiba akár engedélyezett), vagy ha állomás előrejelzésére túlterhelés majd a pontosság (true figyelmeztetéséket aránya) és a visszaírási (true figyelmeztetéséket mértékének) egy adott küszöbérték feletti kell lennie. Ezen célok az ügyfél üzleti célokat kell származnia.
* Nyilvánvaló **integrációs forgatókönyv** a vállalat üzleti a munkafolyamathoz. Például az állomás terhelés előrejelzés integrálható a rács vezérlőközpontba lehetővé teszi a túlterhelési megelőzési tevékenységek.
* Az ügyfél rendelkezik a felhasználásra kész **megfelelő minőségű adatok** használati eset támogatásához (bővebb információt a következő szakaszban a **az adatminőségi**, ez a forgatókönyv az).
* Az ügyfél támogatja a központú adatok architektúra vagy **felhőalapú gépi tanulás**, beleértve az Azure ML és egyéb Cortana Intelligence Suite összetevői.
* Az ügyfél hajlandó létrehozására **egy végpontok közötti adatáramlás** , hogy rendelkezik a adatok kézbesítését kiterjeszti a felhőbe helyezni, és hajlandó **azok** a megoldás.
* Az ügyfél készen áll a **erőforrást fordítsanak** ki lesz kell aktívan részt vevő kezdeti kísérleti végrehajtása során, hogy a Tudásbázis és a megoldás tulajdonjogát át lehet vinni az ügyfél sikeres befejezését követően.
* A felhasználói erőforrás kell lennie egy **gyakorlott adatok professional**, lehetőség szerint olyan adatok tudósok.

A fenti feltételeknek megfelelő használati eset minősítése nagy mértékben javíthatják a használati esetek sikerességi arányát és létrehozni egy jó beachhead jövőbeli használati esetek végrehajtásához.

### <a name="cloud-based-solutions"></a>Felhő alapú megoldások
Az Azure Cortana Intelligence Suite integrált környezetet a felhőben található. Egy speciális elemzésekre megoldás egy felhőalapú környezetben a központi telepítés rendelkezik, a vállalatok számára, és ezzel egy időben jelentős előnyt azt jelentheti, nagy változást a vállalatok számára, hogy továbbra is használja a helyszíni IT-megoldásokhoz. A energia szektort belül nincs fokozatos áttelepítés a felhőbeli műveletek egyértelmű tendenciát. Erre az irányra kerül jár együtt az intelligens rács fejlesztésének című szakaszban leírtaknak megfelelően a fenti a **trendek**. Mivel ez a forgatókönyv a energia tartomány felhőalapú megoldás kialakításával foglalkozik, fontos az előnyöket és az egyéb szempontok a felhőalapú megoldás.

Lehet, hogy a legnagyobb egy felhőalapú megoldás előnye a költségeket. Megoldás él felhő telepített összetevők nincs társaságuk költségek vagy (az árukat eladott) ELÁBÉ összetevők költsége helyett társítva. Ez azt jelenti, hogy a hardverek, szoftverek és informatikai karbantartási beruházásának nincs szükség, és ezért nem jelentős üzleti kockázat csökkentése.

Egy másik előnye, a felhőalapú megoldásokhoz használatalapú költség szerkezete. Kiszolgálók felhőalapú számítási és tárolási telepített, és most-szükség szerint alapon méretezhető. Egy felhőalapú megoldás költség hatékonyságát előnye jelképez.

Végül, ez a felhőalapú ajánlat része informatikai karbantartás vagy későbbi infrastruktúra fejlesztési terhelésnél nincs szükség van. Olyan mértékben, hogy Cortana Intelligence Suite osztály szolgáltatások tartalmaz a legjobb, és az ütemterv fejlődnek tartja. Új szolgáltatások, összetevők és képességek folyamatosan bevezetni, és fejleszteni.

Egy olyan vállalat csak megkezdi annak átmenetében kiterjeszti a felhőbe magas javasoljuk a felhő áttelepítési ütemterv implementálásával fokozatos megközelítés érvénybe. Biztosak vagyunk abban, hogy segédprogramok és vállalatok energia a tartományban, a használati esetek, ez a forgatókönyv ismertetett prediktív elemzési megoldások felhőben ügyfélteszteléssel kiváló lehetőséget jelképezi.

#### <a name="business-case-justification-considerations"></a>Üzleti eset indoklás kapcsolatos szempontok
Sok esetben az ügyfél érdekében, hogy egy adott használati eset, amelyben egy felhőalapú megoldás és a gépi tanulás olyan fontos összetevők az üzleti indoklásának érdekelt lehet. Egy helyszíni megoldás esetében egy felhőalapú megoldás eltérően a minimális társaságuk költség összetevője, és költség tényezők társított valós használatot. Amikor a központi telepítése egy megoldást a Cortana Intelligence Suite előrejelzés energia, több szolgáltatás integrálható az egyetlen közös költség struktúrára. Például adatbázisok (*pl.*, az SQL Azure) a nyers adatok tárolására is használható, és ezután az Azure ML a tényleges előrejelzések szolgál az előrejelzési szolgáltatásokat. Ebben a példában a költség struktúra tartalmazhatnak, tárolás és a tranzakciós összetevők.

Másrészt rendelkeznie kell egy üzleti értékét egy energia igény szerinti (rövid és hosszú távú) előrejelzés működő beható ismerete. Fontos, vegye figyelembe az egyes előrejelzési műveletek üzleti értékét. Például pontosan előrejelzés power betöltése a következő 24 órában megakadályozhatja, hogy túltermelés vagy segít megakadályozni, hogy a rács túlterhelések és ez mennyiségi kell naponta pénzügyi megtakarítások tekintetében.

A pénzügyi előnye, hogy igény kiszámításához alapvető képlet előrejelzési megoldás: ![alapvető képletet a pénzügyi előnye, hogy igény kiszámításához előrejelzési megoldás](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Cortana Intelligence Suite használatalapú árképzési modellt biztosít, mivel nincs szükség van a képlet rögzített költség összetevő nélül. A képlet kerülhet sor, naponta, havi vagy éves alapon.

Aktuális Cortana Intelligence Suite és Azure ML díjszabások található [Itt](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>A megoldás fejlesztési folyamata
A fejlesztési ciklus egy energia igényű előrejelzés általában szükség 4 fázisra, biztosítjuk, amelyek minden felhőalapú technológiákat és szolgáltatásokat a Cortana Intelligence Suite belül használja.

Ezt az alábbi ábra szemlélteti:

![Intelligens rács ciklus](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

A következő bekezdés a 4. lépés folyamat ismerteti:

1. **Adatgyűjtés** – minden speciális elemzési alapú megoldás adatok támaszkodik (lásd: **adatok ismertetése**). Kifejezetten amikor a prediktív elemzés és előrejelzését, azt használja a folyamatban lévő, dinamikus az adatok áramlását. Esetén energia igény szerinti előrejelzés, ezeket az adatokat közvetlenül az intelligens mérőszámok forrása lehet, vagy egy helyszíni adatbázisban már összesíteni. Azt is más külső adatforrások például időjárása és a hőmérséklet támaszkodnak. A folyamatban lévő adatok és kell lennie vezénylését, ütemezett, tárolja. [Az Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) a fő workhorse való elvégzéséhez szükséges parancsokról ezt a feladatot.
2. **Modellezési** – pontos és megbízható energia előrejelzéseket, egy kell fejlesztése (szerelvény) és egy nagy modellt, hogy elérhetővé válnak a korábbi adatok használatát, és kinyeri az adatokat a beállítás után sokatmondóbbak és prediktív minták karbantartása. A Machine Learning (ML) területen van lett gyorsan növekszik speciális algoritmusok rendszeresen fejlesztés alatt áll. Az Azure ML Studio, amely segít a teljes munkafolyamat belül a legfejlettebb ML algoritmusok használata kiváló felhasználói élményt nyújt. A munkafolyamat mutatja be egy egyszerűen elsajátítható folyamatábrája, és az adatok előkészítése, a szolgáltatás kivonása, a modellezési és a modell kiértékelése tartalmazza. A felhasználó ebben a környezetben található különböző modellek több száz bekérésére is. Ebben a fázisban végén egy adatok tudósok lesz működő modell, amely teljes kiértékelt, készen áll a központi telepítéshez.
   
   Az alábbi ábrán egy tipikus munkafolyamat ábrája:
   
   ![Modellezési munkafolyamat](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Központi telepítés** – működő modell, a következő lépés a központi telepítés. A modell itt alakul egy webszolgáltatás, amelyet egy RESTful API egyidejűleg meghívható különböző felhasználási ügyfelek az interneten keresztül mutatja be. Az Azure ML egy modellt közvetlenül az Azure ML Studio gomb egyetlen kattintással üzembe egyszerű módszert kínál. A teljes telepítési folyamat alatt a technikai részletek történik. Ez a megoldás felel meg a szükséges felhasználásához automatikusan átméretezheti.
4. **Felhasználás** – ebben a fázisban ténylegesen biztosítjuk előrejelzéseket készítsen az előrejelzési modell használatával. A felhasználás is alapú, felhasználó-alkalmazás (*pl.*, irányítópult), vagy közvetlenül például működési rendszerből igény szerinti/rendszer és a rács optimalizálási megoldást. Több használati esetek is vezeti egyetlen modellből.

## <a name="data-understanding"></a>Adatok ismertetése
Az üzleti szempontok kiterjedő után (lásd: **üzleti ismertetése**) egy energia igényű előrejelzés megoldás, azt most már készen áll és beszéljék meg, az adatok része. A prediktív elemzési megoldások megbízható adatok támaszkodik. Az előrejelzés igény szerinti energia, azt támaszkodhat különböző szintű részletességű múltbeli adatokkal. A felhasznált anyagokat, hogy az előzményadatok lesz. Azt is, amelyben az adatok tudósok előre (más néven szolgáltatások), végül hozza létre a szükséges előrejelzések modellt helyezhetők azonosítja gondos elemzést változni fog.

Ez a szakasz a többi azt ismerteti, a különböző lépéseit és szempontjait az adatokat, és hogyan annak érdekében, hogy használható formába való megértéséhez.

### <a name="the-model-development-cycle"></a>A modell fejlesztési ciklus
Jó előrejelzési modellek bizonyos gondos előkészületeket és tervezési előállító. A modellezési folyamat több lépést bontásához, és egyszerre csak egy lépésben összpontosító jelentősen javíthatja a teljes folyamat eredményeit.

A következő diagram azt ábrázolja, hogyan a modellezési folyamatban volt oszlanak több lépést:

![Modell fejlesztési ciklus](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Amint is látható a ciklus hat lépésekből áll:

* Probléma létrehozását
* Adatfeldolgozást és az adatok feltárása
* Adatok előkészítése és a szolgáltatás műszaki osztály
* Modellezés
* Modell kiértékelése
* Fejlesztés

A többi Ez a szakasz azt ismerteti, az egyes lépéseket és mérlegelje lépésről lépésre.

### <a name="problem-formulation"></a>Probléma létrehozását
Azt is érdemes lehet a probléma létrehozását, a legfontosabb lépés egy kell vennie a prediktív elemzési megoldás megvalósítása előtt. Itt azt volna az üzleti probléma átalakító és felbontani azt az adott elemeket, amelyeket adatok használatával, és technikák modellezési megoldhatók. Tanácsos állítson össze a problémát, kérdésekre kell válaszolnia. Az alábbiakban néhány lehetséges kérdésre, előfordulhat, hogy alkalmazhatók energia igény szerinti előrejelzés hatókörén belül:

* Mit tartalmaznak az egyes állomás várható terhelése a következő óránként vagy naponta?
* A nap milyen időpontban a rács tapasztal csúcs igény szerint?
* Hogyan valószínűleg van a rács a terhelés fenntartása érdekében várható?
* Energia mennyiségét kell az energiagazdálkodási állomás létrehozása minden órában, a nap során?

Ezeket a kérdéseket kialakítása lehetővé teszi a megfelelő adatok és az üzleti probléma az elvégzendő teljesen igazított megoldások megvalósításának összpontosítanak. Majd továbbá azt állíthatja be néhány alapvető metrikákat, amelyek lehetővé teszik a számunkra, hogy a modell teljesítményét értékeli. Például hogyan pontos az előrejelzés kell, és mi az, hogy a tartomány, amely a vállalat által elfogadható hiba?

### <a name="data-sources"></a>Adatforrások
A modern intelligens rács különböző részeit és összetevők a rács adatait gyűjti. Ezek az adatok különböző szempontjairól a művelet és a kiemelt rács kihasználtsági jelöli. Az előrejelzési energia igény szerinti hatókörén belül azt a tényleges igény szerinti fogyasztását tükrözik adatforrások döntéseken vannak korlátozása. Energiafogyasztás egyik fontos forrása intelligens mérőszámok. A világ minden táján segédprogramok intelligens mérőszámok gyorsan telepíti a fogyasztók számára. Intelligens mérőszámok jegyezze fel a tényleges energiafogyasztás, és ezeket az adatokat a vállalat biztonsági folyamatosan továbbítják. Adatok gyűjtése és küldött vissza rögzített időközönként, a beállításnak 1 óra és 5 percenként közötti. Speciális intelligens mérőszámok programozása távolról elosztása a fogyasztás háztartását belül, és szabályozhatja. Intelligens mérési adatok viszonylag megbízható, és magában foglalja az időbélyegző. Így az előrejelzés igény fontos összetevőként. A mérési adatok összesíthetők (összegzett) a rács topológia belül különböző szinteken: átalakító, állomás, régió, *stb*. Majd kiválaszthatja azt a szükséges összesítési szint, az előrejelzési modell létrehozásához. Például ha a vállalat szeretne az egyes a rács alállomások jövőbeli terhelés előrejelzése majd összes mérőszámok-adatokat is kell összesítve van minden egyes állomás és az előrejelzési modell bemenetként használja. Egy belső adatforrás lesz az intelligens mérőszámok.

Megbízható energia igény szerinti előrejelzés más külső adatforrások is támaszkodik. Egy fontos tényező, amely befolyásolja az energiafogyasztás, de a időjárási, pontosabban a hőmérséklet. Előzményadatokat erős korrelációs között külső hőmérséklet és energiafogyasztását jeleníti meg. Működés közbeni nyári napban fogyasztókat abban, hogy azok légkondicionálók és fűtésrendszerek téli bekapcsolása közben. A rács helyen korábbi hőmérséklet egy megbízható forrás, ezért kulcs. Ezenkívül azt is támaszkodniuk hőmérséklet pontos előrejelzés egy előrejelzőjének energiafogyasztás.

Egyéb külső forrásból is hozzájárulhat a energia igény szerinti előrejelzési modellek fejlesztése során. Ezek közé tartozhatnak a hosszú távú ideértve az éghajlatból módosítások gazdaságos indexek (*pl.*, GDP), és másokkal. A jelen dokumentum nem közzétesszük ezek más adatforrásokhoz.

### <a name="data-structure"></a>Adatok szerkezete
A szükséges adatforrások azonosítása, után szeretnénk győződjön meg arról, hogy a nyers adatokat gyűjtött a megfelelő szolgáltatásokat tartalmazza. Megbízható igény szerinti előrejelzési modell összeállításához, igazolnia kell győződjön meg arról, hogy az összegyűjtött adatokat tartalmaz, amelyek segítségével előre jelezni jövőbeli iránti igény miatt adatelemek. Az alábbiakban néhány alapvető követelményeivel kapcsolatos a nyers adatok adatok szerkezete (séma).

A nyers adatok sorok és oszlopok áll. Minden egyes mérési adatok egyetlen sor jelzi. Minden egyes soraiban levő adatok (más néven szolgáltatásokat vagy mezők) több oszlopot tartalmaz.

1. **Időbélyeg** – a Timestamp típusú mező képviseli a tényleges időpontot, amikor a mérési lett felvéve. Azt kell tartania a közös dátum/idő formátumok egyikét. Dátum és idő is részei tartalmaznia kell. A legtöbb esetben nincs szükség vége: a második szintű részletesség rögzítendő alkalommal. Fontos adja meg, amelyben az adatok rögzítése időzónáját.
2. **Azonosító mérni** -ebben a mezőben a mérő vagy a készülék azonosítja. Egy kategorikus változó, és számjegyek és karakterek kombinációja lehet.
3. **Felhasználás értéke** – Ez az adott dátum/idő a fogyasztás. A felhasználási mérhető kWh-ban (kilowatt-hour), vagy bármely más elsődleges egység. Fontos megjegyezni, hogy a következő mértékegység kell maradnia konzisztens összes mérték az adatok között. Bizonyos esetekben fogyasztás lehet biztosítani a több mint 3 power fázisban. Ebben az esetben azt kell gyűjteni a független fogyasztás fázisok.
4. **Hőmérséklet** – a hőmérséklet általában gyűjtött független forrásból. Azonban az kompatibilis legyen az fogyasztási adatokhoz. Az időbélyegző fent leírt módon, amely lehetővé teszi, hogy a tényleges adatokkal szinkronizálni kell szerepelnie kell benne. A hőmérséklet érték fok Celsius vagy Fahrenheit adható meg, de kell az összes mérték között konzisztens marad.
5. **Hely –** a location mező általában a hely, ahol a hőmérséklet adatokat összegyűjtötte-e társítva. Irányítószám számként vagy szélességi/hosszúság (lat/hosszú) formátumban ábrázolhatók.

Az alábbi táblázatok jó használati és hőmérséklet adatformátum példát látható:

| **Dátum** | **Idő** | **A mérési azonosítója** | **1. fázis** | **2. fázis** | **3. fázis** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Dátum** | **Idő** | **Hely** | **Hőmérséklet** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Mivel a fent látható, akkor ebben a példában a 3 power fázisaihoz tartozó megjeleníthető 3 eltérő értékeket tartalmaz. Vegye figyelembe azt is, hogy a dátumot és időpontot tartalmazó mezői elkülönül egymástól, azonban is kombinált egyetlen oszlopba. Ebben az esetben a hely oszlopban jelennek meg az 5 jegyű irányítószám formátum és mértékben Celsius formátumban hőmérséklet.

### <a name="data-format"></a>Adatok formátuma
Cortana Intelligence Suite képes támogatni a fürt megosztott kötetei szolgáltatás, TSV, JSON, leggyakrabban használt adatokat-formátumok *stb*. Fontos, hogy adatformátum számára a teljes életciklusát a projekt konzisztens marad.

### <a name="data-ingestion"></a>Adatfeldolgozás
Mivel energia igény szerinti előrejelzés folyamatosan és gyakran várhatóan, gondoskodunk róla, hogy a nyers adatok áramlik teli és megbízható adatfeldolgozást eljárással. Az adatfeldolgozást folyamat biztosítania kell, hogy a nyers adatok érhető el az előrejelzési folyamathoz szükséges időt. Ez azt jelenti, hogy az adatok adatfeldolgozást gyakoriság előrejelzési gyakoriságát nagyobbnak kell lennie.

Példa: Ha az igény szerinti előrejelzés megoldást hoz létre egy új előrejelzés naponta 8:00 órakor, akkor győződjön meg arról, hogy mind az adatok az elmúlt 24 órában gyűjtött rendelkezik már teljesen okozhatnak: adott pontra, és akkor is igaz, hogy rendelkezik tartalmaz-e az elmúlt órában a kell  adatok.

Ehhez az szükséges, a Cortana Intelligence Suite egy megbízható adatok adatfeldolgozást folyamat támogatásához különböző lehetőséget kínál. Ez további tárgyalja a **telepítési** szakasz ebben a dokumentumban.

### <a name="data-quality"></a>Az adatminőségi
A nyers adatok forrása megbízható és pontos igény szerinti előrejelzés végrehajtásához szükséges néhány alapvető adatok minőségi feltételeknek kell megfelelnie. Habár speciális statisztikai módszerek kiegyensúlyozása érdekében néhány lehetséges minőségi probléma, továbbra is győződjön meg arról, hogy néhány alapadatokhoz minőségi küszöbérték amikor új adatok bevitele vannak meghaladó szükséges. Az alábbiakban néhány nyers adatok minőségi vonatkozó szempontok:

* **Hiányzó érték** – ez: olyan helyzet, amikor adott mérési gyűjtése nem történt meg. Az alapvető követelmény az, hogy a hiányzó érték gyakorisága nem lehet nagyobb, mint 10 % bármely adott időtartamon. Esetben egyetlen érték hiányzik, fel kell tüntetni egy előre definiált érték használatával (például: "9999"), és nem "0", amely érvényes érték lehet.
* **Mérési pontosság** – a tényleges érték fogyasztási vagy hőmérséklet pontosan kell rögzíteni. Pontos mérések pontos előrejelzéseket eredményez. A mérési hiba általában a true érték 1 % alacsonyabbnak kell lennie.
* **Mérési idő** – szükséges a tényleges mérési igaz idején képest több mint 10 másodperc, hogy az adatok tényleges időbélyegzője gyűjtött fog nem tér.
* **Szinkronizálás** – Ha több adatforrást használják (*pl.*, felhasználása és hőmérséklet) azt győződjön meg arról, hogy vannak-e nem időszinkronizálást problémák közöttük. Ez azt jelenti, hogy az összegyűjtött időbélyeg bármely két független adatforrásokból közötti időeltérés nem haladhatja meg a több mint 10 másodperc.
* **Késés** - kapcsolatban a fentiekben ismertetett, a **adatfeldolgozást**, azt a megbízható adatok folyamata és feldolgozási folyamat függenek. Szabályozhatja, hogy azt győződjön meg arról, hogy azt szabályozzák a adatkésleltetést. Ez a tényleges mérési készült és az idő, ahol a Cortana Intelligence Suite a tárba betöltődött, és használatra kész közötti időeltérés van megadva. Rövid távú terhelést a teljes késést az előrejelzés nem lehet nagyobb, mint 30 perc. Hosszú távú terhelést a teljes késést az előrejelzés nem lehet nagyobb, mint 1 nap.

### <a name="data-preparation-and-feature-engineering"></a>Adatok előkészítése és a szolgáltatás műszaki osztály
Miután a nyers adatok keresztül a szervezetbe (lásd: **adatfeldolgozást**), és biztonságosan tárolt, készen áll a feldolgozásra. Az adatok előkészítési fázis alapvetően a nyers adatok véve és átalakítás (átalakítása, átalakításakor) a modellezési fázisban űrlap be azt. Előfordulhat, hogy egyszerű olyan műveleteket tartalmaznak, például a nyers adatok oszlopa, a tényleges mért érték, szabványosított értékek, többek között az összetettebb műveletek [alkalommal elmaradt](https://en.wikipedia.org/wiki/Lag_operator), stb. Az újonnan létrehozott adatoszlopok nevezzük az adatokkal kapcsolatos funkciókkal, és ezek folyamat nevezzük szolgáltatás mérnöki csapathoz. Ez a folyamat végén, amely a nyers adatok kinyert, és nem használható modellezési új adatkészlet kellene azt. Ezenkívül az adatok előkészítési fázis irányuló a hiányzó értékeket kell (lásd: **az adatminőségi**) és ellensúlyozza a őket. Néhány esetben azt kell az adatokat annak érdekében, hogy ugyanolyan skála jelennek meg az összes érték optimalizálására.

Ebben a szakaszban látható néhány energia szerepelnek a közös adatok funkciója az igény szerinti előrejelzési modellek.

**Idő áll a szolgáltatások:** ezeket a szolgáltatásokat a dátumot/időbélyegző adatok származtatja. Ezek kibontott és konvertálni kategorikus szolgáltatások, mint:

* Idő nap – Ez az a nap, ami értékek 0 és 23 óra
* Napja a héten – Ez a hét napját jelöli, és 1 közötti értéket fogad (vasárnap) 7 (szombat)
* Nap hónap – Ez az aktuális dátumot jelöli, és 1 és 31 közötti értéket vehet
* Hónap év – Ez a hónap jelöli, és veszi értéke 1 (január) és 12 (December)
* Hétvégi – Ez a tulajdonság bináris érték, amely a 0 érték létrehozását vagy 1-es a hétvégi
* Szünnap - bináris érték funkciója, amely szükséges időt a 0 érték szabadnap rendszeres napi vagy 1
* Fourier feltételek – Fourier feltételei, amelyek alapján a Timestamp típusú, és a szezonalitás értékének (ciklus) rögzítéséhez használt súlyok az adatokat. Mivel jelenleg több évszak előfordulhat, hogy rendelkezik az adatok több Fourier feltételek előfordulhat, hogy kell. Igény szerinti értékek Előfordulhat például, éves, heti és napi évszak/ciklusok 3 Fourier feltételek eredménye.

**Független mérési funkciókat:** független szolgáltatásai közé tartozik a szeretnénk előre a modellben használandó összes adatelemeket. Itt azt zárja ki a függő szolgáltatás, amely előre jelezni kell azt.

* Lag szolgáltatás – ezek a idő vette a tényleges igény szerinti értékeit. Lag 1 szolgáltatások például tartsa a az igény szerinti érték az előző órában (óránkénti adatok feltételezve) az aktuális időbélyeg viszonyítva. Hasonlóképpen, a lag 3, azt, adja hozzá a lag 2 *stb*. A lag használt szolgáltatásai minőségén tényleges kombinációja határozza meg a modellezési fázis során a modell eredmények értékelése.
* Hosszú távon trendekkel – Ez a funkció igény szerinti év közötti lineáris növekedése jelöli.

**A függő szolgáltatás:** a függő szolgáltatás le az adatoszlop, amely szeretnénk előre jelezni tekinthetők. A [felügyelt gépi tanulás](https://en.wikipedia.org/wiki/Supervised_learning), igazolnia kell a először még betanítani a modellt a szolgáltatások használata (más néven a címkék). Ez lehetővé teszi, hogy a függő szolgáltatás társított adatokat a minták további modell. Az előrejelzés energia igény általában szeretnénk előre jelezni a tényleges igény szerint, és ezért azt használhatja azt a függő szolgáltatás.

**A hiányzó értékeket kezelése:** az adatok előkészítési fázis során azt kell kezelni a hiányzó értékeket a legjobb stratégiájának meghatározása. Ez főleg használatával hajtható végre a különféle statisztikai [adatok imputálási módszerek](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). Energiát igény szerinti előrejelzéséhez esetén azt általában imputálására hiányzó értékeket az előző elérhető adatpontok mozgóátlag használatával.

**Adatok normalizálási:** adatok normalizálási átalakítása, hogy az összes numerikus adatok, például igény szerinti előrejelzési hasonló méretezési használt egy másik típusú. Ez általában javítja a modell pontosságát, és a pontosság. Azt kellene általában ehhez a tényleges érték elosztjuk az adatok a tartományon.
Ez fogja csökkentheti az eredeti érték kisebb tartományba, általában a -1 és 1 között.

## <a name="modeling"></a>Modellezés
A modellezési fázisban kell, ha a modell átalakításhoz adatok történik. A hiba a folyamat core speciális algoritmusok, amely az előzményadatok (betanítási adatok) vizsgálata, bontsa ki a mintákat és a modell létrehozása. A modell később használható előre jelezni az új adatokat, amelyek még egyszer sem használták a modell létrehozásához.

Ha van egy működő megbízható modellt, majd használhatjuk, amely tartalmazza a szükséges funkciókat (X) van felépítve új adatok pontozása céljából. A pontozási folyamat használja a megőrzött modell (a képzés fázis objektumot) és a cél változó, amely Ŷ megjelölt előrejelzése biztosítják.

### <a name="demand-forecasting-modeling-techniques"></a>Az előrejelzés modellezési módszereket igény szerint
Esetén az előrejelzés biztosítjuk igény szerint van rendezve idő előzményadatoknak használja. Általában hivatkozunk, amely tartalmazza az idődimenzió adatok [time series](https://en.wikipedia.org/wiki/Time_series). Az idő adatsorozat modellezési célja, hogy megtalálja az időbeli kapcsolódó trendeket, szezonalitás értékének, automatikus-korrelációs (idővel korrelációs), és állítson össze azokat a modellbe.

Az elmúlt években speciális algoritmusok fejlesztettek idő series előrejelzési alkalmazásához, és az előrejelzési pontosság növeléséhez. Röviden arról lesz szó néhány őket itt.

> [!NOTE]
> Ebben a szakaszban használt, betanítási és az előrejelzés – áttekintés egy gép hanem modellezési módszereket, amelyek gyakran használják az igény szerinti előrejelzési rövid áttekintése nem használhatók. További információk és kapcsolatos idő series előrejelzési oktatási anyagok, erősen ajánlott a online könyv [előrejelzés: alapelvek és eljárás](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (mozgóátlag)**](https://www.otexts.org/fpp/6/2)
Mozgóátlag egyike az első analitikai módszereket is, az idő series előrejelzési használatban van, és továbbra is egyik leggyakrabban használt módszerek mai. Egyben a alapját fejlettebb, technikák előrejelzés. A mozgóátlag azt vannak az előrejelzés a következő adatpont által átlagosan azt jelzi, ahol K a mozgóátlag sorrendjének K legutóbbi pontokon keresztül.

A mozgóátlag átlagos módszer az előrejelzés simítás hatása van, és ezért előfordulhat, hogy nem kezeli az adatok is nagy illékonyság.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (beállításáról)**](https://www.otexts.org/fpp/7/5)
Az exponenciális simítás (ETS) családba tartozó legutóbbi adatpontok súlyozott átlagát használja a következő adatpont előrejelzése különböző módszerekkel. A képet, hogy nagyobb súlyt hozzárendelése újabb értékek és fokozatosan csökkentse a régebbi mért értékek súlya. Számos különböző módszer a termékcsalád is van, ezek közé tartoznak a szezonalitás értékének az adatok kezelésére, mint [Vilmos-telek jellemzők határozza metódus](https://www.otexts.org/fpp/7/5).

Ezek a módszerek közül is figyelembe az adatok a szezonalitás értékének a.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**Elemhez tartozó ARIMA (automatikus regressziós integrált átlagos áthelyezése)**](https://www.otexts.org/fpp/8)
Automatikus regressziós integrált áthelyezése átlagos (ARIMA) egy másik család módszerek a time series előrejelzési általánosan használt. Ez gyakorlatilag egyesíti az auto-regressziós módszerek mozgóátlaga. Automatikus-regressziós módszerek regressziós modell előző idő sorozatértékek megtételével számítási a következő dátum pont használja. ARIMA módszerek kiszámításához az adatpontok közötti különbséget, és azokat az eredeti mért érték helyett használata különböző módszereket is érvényesek. Végül, az ARIMA is használ a fent említett áthelyezése átlagos technikákat. Minden, az alábbi módszerek különböző módokon kombinációja, mi a családba tartozó ARIMA módszerek hoz létre.

ETS mind az ARIMA széles körben használják ma energia igény szerinti előrejelzés és sok más előrejelzési problémákat. Sok esetben ezek a kvóták segítségével nagyon pontos eredményeket biztosításához.

**Általános többváltozós regresszió** regressziós modell machine learning és a tartományon belül a legfontosabb modellezési megközelítés lehet. A time series környezetében előre jelezni a jövőbeni értékeket használjuk regressziós (*pl.*, az igény szerinti). Regressziós azt a előre lineáris kombinációja igénybe vehet, és ismerje meg, a képzési során ezek előre súlyozását (más néven együttható). A cél, hogy egy regressziós egyenes, amely az előre jelzett érték lesz előrejelzési eredményez. Regressziós módszerek is alkalmas, ha a célváltozót numerikus, és ezért is megfelelő idő series előrejelzési. Nincs regressziós metódusok, mint például a nagyon egyszerű regressziós modell számos [lineáris regressziós](https://en.wikipedia.org/wiki/Linear_regression) és azokat, például a döntési fák, további speciális [véletlenszerű erdők](https://en.wikipedia.org/wiki/Random_forest), [Neural Hálózatok](https://en.wikipedia.org/wiki/Artificial_neural_network), és a súlyozott döntési fák.

Hozhat létre, energia igény szerinti regressziós hibát előrejelzés biztosítanak számunkra a magas fokú rugalmasságot biztosít az adatok egyesíthetők funkciók kiválasztása külső tényezőkkel, például hőmérséklet és a tényleges igény szerinti idő adatsorozat adatokból. További információ a kiválasztott szolgáltatások ismerteti a szolgáltatás mérnöki (lásd: **adatok előkészítése és a szolgáltatás mérnöki**) Ez a forgatókönyv részét.

A megvalósítás és a telepítési energia igény szerinti előrejelzések próbaüzem tapasztalatunk találtunk, amelyeknek, hogy a speciális regressziós modell által biztosított Azure ML általában a legjobb eredményt, és biztosítjuk is.

## <a name="model-evaluation"></a>Modell kiértékelése
Modell kiértékelése rendelkezik belül kulcsfontosságú szerepet a **modell fejlesztési ciklus**. Ebben a lépésben azt megismerhetők a modell és a teljesítmény valós adatok ellenőrzése. A modellezési lépés során a rendelkezésre álló adatok egy részét a modell betanítása a használjuk. A termékértékelési fázisban vesszük további része az adatokat a modell teszteléséhez használjuk. Gyakorlatilag azt jelenti, hogy azt vannak etetési a modellt új adatokat, amely rendelkezik szerkezetátalakítás lett, és a képzési dataset azonos szolgáltatásokat tartalmazza. Azonban az érvényesítési folyamat során használjuk a modell előrejelzése a célváltozó, nem pedig az elérhető célkiszolgálók változó adja meg. A Microsoft gyakran tekintse meg ezt a folyamatot, mint a modell pontozása. Azt kellene majd igaz cél értékek használatához, és hasonlítsa össze azokat előre jelzett frissítheti. A cél mérése és minimalizálása érdekében a előrejelzési hiba, tehát az előrejelzés és a true érték közötti különbséget. Kulcs mennyiségi meghatározására a hiba mérési azért, mert szeretnénk Finomhangolja a modell, és ellenőrizze, hogy ténylegesen csökken a hiba. A modell pontosabb beállításra teheti a tanulást szabályozó Modellparaméterek módosításával, vagy hozzáadásával vagy eltávolításával az adatokkal kapcsolatos funkciókkal (néven [paraméterek ismétlés](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Gyakorlatilag, amely azt jelenti, hogy, hogy előfordulhat, hogy kell a következőnek között a funkció mérnöki csapathoz, modellezési, a modell kiértékelése fázisok többször amíg lehet a hiba szükséges csökkentésére.

Fontos, hogy az előrejelzés hiba soha nem lesznek NULL hangsúlyt soha nincs modell, amely tökéletesen képes előre jelezni minden eredménye. Van azonban bizonyos nagyságát, amely a vállalat által elfogadható. Az érvényesítési folyamat során győződjön meg arról, hogy a modell előrejelzéses hiba van a szintjén szeretnénk vagy nagyobb, mint az üzleti tűréshatáron. Ezért fontos, hogy a megengedhető hiba szintjének beállítása során a ciklus elején a **probléma létrehozását** fázisban.

### <a name="typical-evaluation-techniques"></a>Tipikus értékelési eljárások
A mely előrejelzési hiba mérhető és mennyiségi különböző módja van. Ebben a szakaszban a kiértékelési technikákkal specifikus előrejelzési energia igény és a time Series döntéseken tárgyaljuk.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE rövidítése abszolút százalékos hiba jelenti. A MAPE közötti különbség számítástechnikai azt minden egyes előrejelzett pont és a tényleges érték, az adott pont. A Microsoft majd számlálása pontonkénti hiba által a tényleges érték különbség arányának kiszámításához. Az utolsó lépésben azt átlagos ezeket az értékeket. A használt MAPE matematikai képlete a következő:

![MAPE képlet](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*ahol A<sub>t</sub> a tényleges érték, F<sub>t</sub> az előre jelzett érték, és n az előrejelzési horizon.*

## <a name="deployment"></a>Környezet
Amennyiben azt le a modellezési fázis rögzített, és a modell teljesítmény érvényesítve dolgozunk készen áll a központi telepítési fázis be. Ebben a környezetben központi telepítés azt jelenti, hogy az ügyfél a modell felhasználásához futtatja a tényleges előrejelzéseket, nagy méretekben engedélyezése. A központi telepítés fogalma kulcsot az Azure ML óta fő célunk folyamatosan meghívni az előrejelzéseket, szemben a csupán a insight beszerzése az adatokat. A központi telepítési fázis része Ha engedélyezzük a modell nagy léptékű fel.

Energiát igény szerinti előrejelzés keretén belül a célja, hogy folyamatos és rendszeres időközönként előrejelzések meghívása során győződjön meg arról, hogy friss adatokat a modell és, hogy az előre jelzett adatokat a fogyasztó ügyfél zajlik.

### <a name="web-services-deployment"></a>A webes szolgáltatások telepítése
Azure ml fő telepíthető építőelem a webszolgáltatás. Ez az a leghatékonyabban szeretné engedélyezni a felhőalapú prediktív modellek felhasználását. A webszolgáltatás magában foglalja a modell és foglalja össze az olyan [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). Az API-t bármely ügyfél kódot az alábbi ábrán szemléltetett részeként használható.

![Azt a szolgáltatás telepítését és használat](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Amint is látható, a webszolgáltatás a Cortana Intelligence Suite felhőben üzemel, és is elindítható a kitett REST API végpontra. Különböző típusú ügyfeleket különböző tartományok közötti hívhat meg egyszerre a szolgáltatás a webes API-n keresztül. A webszolgáltatás támogatja az egyidejű hívások ezer is méretezhető.

### <a name="a-typical-solution-architecture"></a>Egy tipikus megoldás architektúrája
Az előrejelzés megoldás energia igény szerinti telepítésekor dolgozunk egy teljes körű megoldás, amely meghaladja a előrejelzés webszolgáltatás, és megkönnyíti a teljes adatfolyama üzembe helyezése iránt érdeklődik. Új előrejelzés meghívása azt időpontjában azt kellene győződjön meg arról, hogy a modell táplált a legfrissebb adatokat szolgáltatásokkal. Ez azt jelenti, hogy az újonnan összegyűjtött nyers adatok folyamatosan okozhatnak, feldolgozni, és akkor kell beállítani a modell készült "szükséges" funkció. Egy időben szeretnénk az ügyfelek fel End elérhetővé az előre jelzett adatokat. Egy példa adatok folyamata ciklus (vagy adatok pipeline) az alábbi ábrán látható:

![Energiát igény szerinti előrejelzési végpont adatfolyama](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

A lépéseket, amelyek a energia igény szerinti előrejelzési ciklus részeként kerül sor az alábbiak:

1. Központilag telepített adatok mérőszámok több millió valós idejű energiafogyasztási adatokkal folyamatosan generál.
2. Folyamatban van a ezeket az adatokat gyűjt, és egy felhőalapú-tárházat töltve (*pl.*, Azure Blob).
3. Mielőtt feldolgozhatóvá válna, a nyers adatok összesített értéket egy állomás vagy regionális szintű a vállalat által definiált konfigurációjának kialakításához.
4. A szolgáltatás feldolgozás (lásd: **adatok előkészítése és a szolgáltatás feldolgozási**) majd történik és eredményez a szükséges adatok modell betanítása vagy pontozási – a szolgáltatás adatokat adatbázisban tárolja (*pl.*, Az SQL Azure).
5. Meghívták a újra képzési szolgáltatás újra betanítása az előrejelzési modell –, hogy a modell frissített verzióját is, hogy a pontozási webszolgáltatás által használható megőrződjenek.
6. A pontozási webszolgáltatás meghívták, amely megfelel a szükséges előrejelzési gyakoriságra ütemezés szerint.
7. Az előre jelzett adatokat az end fogyasztás ügyfél által elérhető adatbázisban tárolódik.
8. A felhasználási ügyfél lekéri az előrejelzések, újra üzembe a rács vonatkozik, és megfelel-e a szükséges használati esetnek t használ fel.

Fontos megjegyezni, hogy a teljes ciklus teljesen automatizált, és ütemezés szerint futtatott. Az adatok ciklus teljes vezénylési eszközök segítségével végezhető [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Teljes körű üzembe helyezési architektúrája
Ahhoz, hogy egy energia igény szerinti előrejelzési megoldás a Cortana Intelligence gyakorlatilag telepítéséhez kell győződjön meg arról, hogy a szükséges összetevők létrehozott és megfelelően konfigurálva.

Az alábbi ábrán egy tipikus Cortana Intelligence alapú architektúra, amely megvalósítja és koordinálja a fent ismertetett folyamatot adatciklus látható:

![Teljes körű üzembe helyezési architektúrája](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

További információ az összetevők és a teljes architektúra tekintse meg a energia megoldás sablont.

