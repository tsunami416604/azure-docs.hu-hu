---
title: Cortana Intelligence megoldás sablon forgatókönyve a kereslet-előrejelzés, energia
description: A Microsoft Cortana Intelligence szolgáltatással, amely segít a segédprogram energiavállalat iránti kereslet-előrejelzési megoldás sablon.
services: machine-learning
author: ilanr9
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/24/2016
ms.author: garye
ms.openlocfilehash: 195776cda0005b3a79aa82220660fcc328f6ee98
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426254"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence megoldás sablon forgatókönyve a kereslet-előrejelzés, energia
## <a name="executive-summary"></a>Vezetői összefoglaló
Az elmúlt néhány évben eszközök internetes hálózata (IoT), alternatív energiaforrások és big Data típusú adatok óriási lehetőségeket létrehozásához a segédprogram és energia tartományban lehet egyesített. Egy időben a segédprogram és a teljes energia ágazat látott az erőforrás-igényes hatékonyabb módon lehet szabályozni az energia használati fogyasztóval rendelkező egybesimítás használatalapú. Ezért az segédprogram és intelligens vállalatok is nagy szükség van a innovációját, és újítsa meg magukat. Ezenkívül számos teljesítmény- és segédprogram rácsok egyre elavult, és nagyon költséges, karbantartása és kezelése. Az elmúlt év során a csapat dolgozott az energia tartományon belüli marketingmódszerek számos. Ezek marketingmódszerek során történt, amelyben a segédprogramok vagy ISV (független szoftvergyártók) keresi az előrejelzés a jövőbeli energiaszükségletét sok esetben. Ezek az előrejelzések fontos szerepet játszanak az jelenlegi és jövőbeli üzleti és a különböző használati esetek alapját váltak. Ezek közé tartozik a rövid és hosszú távú power terhelés előrejelzést, kereskedelmi, terheléselosztást, rács optimalizálás stb. Big data és fejlett analitikai AA módszerek például a Machine Learning (gépi tanulás) vonatkoznak a legfontosabb előfeltétele pontos és megbízható előrejelzések előállító.  

A forgatókönyv együtt elhelyezni az üzleti és a sikeres fejlesztéshez szükséges analitikai irányelvek és központi telepítését energiaszükségletét előrejelzési megoldás. Javasolt iránymutatás segítséget segédprogramok, az adatelemzők és adatmérnökök teljesen szolgáltatáscsomagot, felhőalapú, alkalmazható igény-előrejelzési megoldások kialakítása során. Olyan vállalatok, akik most használja először a big data és fejlett analitikai utazás ilyen megoldás megfelelhet a hosszú távú intelligens stratégiájuk kezdeti mag.

> [!TIP]
> Egy diagram, amely biztosítja az architektúra áttekintése a sablon letöltése: [kereslet-előrejelzés, energia-architektúrát Cortana Intelligence-Megoldássablon](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Áttekintés
Ez a dokumentum ismerteti a business, az adatok és műszaki szempontjait a Cortana Intelligence használatával és az adott Azure Machine Learning (AML) a megvalósítás és a energia-előrejelzés megoldások üzembe helyezéséhez. A dokumentum három fő részből áll:  

1. Üzleti ismertetés  
2. Adatok megismerése  
3. Technikai megvalósítás

A **az üzleti igények felmérése** rész ismerteti az üzleti aspektus egy megjelölése szükséges és megfontolnia előtt befektetési döntés meghozatalakor. Ismerteti, hogyan ahhoz, hogy az üzleti probléma megoldására összpontosíthasson annak érdekében, hogy prediktív elemzési és gépi tanulási valóban hatékony és a alkalmazni. A dokumentum további machine learning és felhasználásukról energia-előrejelzés problémákra történő alapjait ismerteti. Az előfeltételekről és a egy alkalmazási helyzet a feltételnek megfelelő ismerteti. Mintául szolgáló használatieset-forgatókönyveit és vállalkozását forgatókönyveket is rendelkezésre állnak.

Adatok el a fő összetevővel kapcsolatban bármilyen machine learning-megoldását. A **Data Understanding** részét ez a dokumentum bemutatja néhány fontos szempont az adatok. Ismerteti, milyen típusú adatokhoz, energia-előrejelzés, adatok minőségi követelményeknek és milyen adatforrásokat általában létezik. Is ismertetjük, hogyan a nyers adatok a modellezési rész ténylegesen hangból funkciók előkészítéséhez használható.

A harmadik rész a dokumentum ismerteti a **műszaki megvalósítási** egy megoldás része. Funkciófejlesztési és modellezés középpontjában álló az adatelemzési folyamat, és néhány részletet a ezért tárgyalja. Ez fedezi a webszolgáltatásokhoz, amelyek egy fontos eszközzel helyezhető üzembe prediktív elemzési megoldások felhőben vannak fogalmát. Azt is szerkezeti egy tipikus egy teljes körű szolgáltatáscsomagot megoldások architektúrájának.

Emellett a dokumentum tartalmaz, amelyek segítségével tovább a tartomány és a technológiai ismereteket szerezhet – referenciaanyag.

Fontos megjegyezni, hogy nem tervezünk, hogy biztosítsák a jelen dokumentum a mélyebb adatelemzési folyamat, a matematikai és technikai aspektusait. Ezek a részletek megtalálhatók [Azure Machine Learning dokumentációs](https://azure.microsoft.com/services/machine-learning/) és [blogok](https://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Célközönség
Ez a dokumentum célközönsége az üzleti és műszaki személyzet kívánó ismeretek is, és ismeri a Machine Learning-alapú megoldásokat, és hogyan ezeket használják-e az energia-előrejelzés tartományon belül kifejezetten.

Az adatelemzők is kihasználhatják ezt a dokumentumot, szerezhet, hogy a magas szintű folyamata, amely a központi telepítését előrejelzési megoldás az energiaszektor olvasása. Ebben a környezetben is használat jó az alapértékeket, és kiindulási pontként több részletes, és speciális anyagot.

### <a name="industry-trends"></a>Iparági trendek
Az elmúlt néhány évben IoT, alternatív energiaforrások és big Data típusú adatok óriási lehetőségeket létrehozásához a segédprogram és energia területen lehet egyesített. Egy időben a segédprogram és a teljes energia szektorok látott az erőforrás-igényes hatékonyabb módon lehet szabályozni az energia használati fogyasztóval rendelkező egybesimítás használatalapú.

Számos segédprogram és intelligens energiaszolgáltató vállalatok számára úttörő a [intelligens](https://en.wikipedia.org/wiki/Smart_grid) használható számos üzembe helyezésével esetek, győződjön meg arról, hogy a rács által létrehozott adatok használata. Az elektromos áram éles belső tulajdonságai köré szerveződik számos alkalmazási: nem lehet halmozott sem készlet fenntartott tárolja. Tehát mi jön létre kell használni. Segédprogramok, amelyek segítségével hatékonyabban előre kell látniuk egyszerűen az energiafogyasztás mert, amely ad nekik a nagyobb lehetőséget **elosztása és kínálat**, megelőzve az energia veszteség, **üvegházhatást gáz csökkentése kibocsátási**, és a költségek.

Beszéd, a költségek, ha van egy másik fontos szempont, amelyre az ár. Kereskedelmi power segédprogramok között új funkciókat vehetnek igénybe a nagy szükség van a léptetik **előre jelzett keresletet és a jövőbeli ára az elektromos áram**. Ez segít meghatározni a termelési kötetek vállalatok.

A "intelligens" szót használja, ha ténylegesen nevezzük, amely ismerje meg, és ezután az előrejelzéseket rács. Használatalapú szezonális változásai, kiszámítható legyen, valamint **ideiglenes túlterhelés helyzetekben bizonyulhat, és automatikusan módosítsa a hozzá tartozó**. Távolról szabályozó-használat (az intelligens mérőktől ezek segítségével), a honosított túlterhelési lehet kezelni. **Először előrejelzésére és majd működő**, a rácshoz teszi magát intelligensebb idővel.

Ez a dokumentum egy adott használati esetek, mind a jövő, előrejelzés-család fogunk dolgozni a többi rövid távú és hosszú távú energiaszükségletét. Azt néhány hónapon keresztül a következő területeken működik, és néhány ismeretek és a szakértelem, amelyekkel ipari szintű eredményt révén. Más használati eseteket tárgyalja, valamint a dokumentum a közeljövőben.

## <a name="business-understanding"></a>Az üzleti igények felmérése
### <a name="business-goals"></a>Üzleti céljait
A **energia bemutató** célja, hogy egy tipikus prediktív elemzési és a machine learning-megoldását, a nagyon rövid időkereten belül telepíthető. Az aktuális fókusz többek között az energia igény-előrejelzési megoldások engedélyezése, hogy az üzleti értékét gyorsan kell, hogy és ki. Ez a forgatókönyv található információk segíthetnek az ügyfél a következő célok megvalósítására:

* Rövid idő értékre a gépi tanulás alapú megoldás
* Bérlőnként egy próbaprogram használati eset más használati eseteket, vagy az üzleti igények alapján szélesebb hatókört
* Gyorsan nyerhetnek a Cortana Intelligence Suite termékismeretek

A következő célok szem előtt a forgatókönyv célja, hogy az üzleti és technikai ismereteit, amely segítséget nyújt az ezen célok eléréséhez.

### <a name="power-load-and-demand-forecasting"></a>Teljesítmény terhelés és a kereslet-előrejelzés
Az energia ágazat belül lehetnek sokféleképpen melyik igény előrejelzése segíthet kritikus fontosságú üzleti problémák megoldásában. Valójában a kereslet-előrejelzés lehessen venni az alapja az iparágban számos alapvető használati eseteit. Általánosságban javasolt energia igény szerint előrejelzések két típusú: rövid távú és hosszú távon. Egyenként előfordulhat, hogy a különböző célt szolgál, és felhasználja az eltérő megközelítést. A kettő közötti fő különbség az előrejelzési horizon, ami azt jelenti, az időtartományt, amelyhez azt kellene előrejelzési a jövőre.

#### <a name="short-term-load-forecasting"></a>Rövid távú terhelés előrejelzése
Rövid távú betöltése előrejelzés (STLF) energiaszükségletét kontextusában összesített be, amely az előrejelzett a közeljövőben a rács (vagy a rács egész) különböző részein definiálható. Ebben a környezetben a rövid távú kell idő horizon 1 óra 24 órás tartományán belül van meghatározva. Bizonyos esetekben egy horizon 48 órán keresztül is lehetőség. STLF ezért nagyon gyakori a egy működési használati eset a rács. Íme néhány példa a használati esetek driven STLF:

* Terheléselosztás és kínálat
* Kereskedelmi Power-támogatás
* Piaci így (a beállítás kiemelt díj)
* Rács működési optimalizálása
* [Igény szerinti válasz](https://en.wikipedia.org/wiki/Demand_response)
* Kiugró kereslet-előrejelzés
* Igény szerinti ügyféloldali kezelése
* Terheléselosztás és a megelőzési túlterhelni
* Hosszú távú terhelés előrejelzése
* Tartalék és rendellenességészlelés
* Csúcsidőszak megszorítás/simítás 

STLF modell főként közel korábban (utolsó nap vagy hét) használati adatokat és -felhasználási előrejelzett hőmérséklet, egy fontos előjelző. Következő órában előrejelzési pontos hőmérséklet beszerzése és mentése 24 órára egyre kisebb kihívást most nap. Ezek a modellek kevésbé érzékenyek a szezonális mintázatokat vagy hosszú távú használati trendeket.

SLTF megoldásokat is várhatóan készítése a nagy mennyiségű előrejelzési hívások (szolgáltatáskérések), mivel azok vannak meghívott óránként és bizonyos esetekben még nagyobb gyakorisággal. Emellett akkor is előfordul, ahol minden egyes állomás vagy átalakító jelenik meg különálló modellt, és ezért előrejelzési kérések mennyisége még nagyobb beültetésre megtekintéséhez.

#### <a name="long-term-load-forecasting"></a>Hosszú távú terhelés előrejelzése
A hosszú távú terhelés előrejelzés (LTLF) célja áramellátási igény előrejelzése és a egy idő horizon 1 héttel kezdve a több hónapon (és egyes esetekben az évek). Ez horizon tartománya leginkább megfelelő tervezési és a befektetési használati esetek.

A hosszú távú esetben fontos kiváló minőségű az adatokat, amely több év (legalább 3 év) vég lefedi. Ezek a modellek fog általában az előzményadatok szezonalitás minták kinyerése és lehetővé teszik a külső predicators például, időjárás és éghajlati mintákat.

Fontos, hogy elmagyarázza, hogy minél hosszabb a előrejelzési közeljövőre van, a kevésbé pontos lehet az előrejelzés. Ezért fontos néhány konfidencia együtt, amelyek lehetővé teszik, hogy az emberek figyelembe vennie a lehetséges változat, a tervezési folyamatba tényleges előrejelzés előállításához.

Mivel a felhasználási forgatókönyve LTLF többnyire azt tervezi, várhatóan is sokkal alacsonyabb előrejelzési kötetek (mint a korábban megszokott STLF). Általában jelennének meg ezek az előrejelzések, adatvizualizációs eszközök, például az Excel- vagy Power bi embedded, és amelyet a felhasználó manuálisan lehet meghívni.

### <a name="short-term-vs-long-term-prediction"></a>Rövid távú vs. Hosszú távú előrejelzése
Az alábbi táblázat a legfontosabb attribútumok tekintetben STLF és LTLF hasonlítja össze:

| Attribútum | Rövid távú terhelés előrejelzése | Hosszú távú terhelés előrejelzés |
| --- | --- | --- |
| Horizon előrejelzése |Az 1 óra, 48 óra |6 hónapos 1-ről |
| Adatok részletessége |Óradíj |Óránként vagy naponta |
| A tipikus használati esetek |<ul><li>Igény szerint vagy ellátási terheléselosztás</li><li>Válasszon óra előrejelzés</li><li>Igény szerinti válasz</li></ul> |<ul><li>Hosszú távú tervezési</li><li>Rács eszközök tervezése</li><li>Erőforrás-tervezés</li></ul> |
| Tipikus előrejelzőket |<ul><li>Nap vagy hét</li><li>nap órája</li><li>Óránkénti hőmérséklet</li></ul> |<ul><li>Év hónapja</li><li>Hónap napja</li><li>Hosszú távú hőmérséklet és éghajlati</li></ul> |
| Előzményadatok tartomány |Két vagy három évnyi adat |Az adatok 5 – 10 évre visszamenőleg |
| Tipikus pontossága |MAPE * 5 %-os vagy kisebb |MAPE * 25 %-os vagy kisebb |
| Előrejelzési gyakorisága |Előállított óránként vagy 24 óránként |Miután a havi, negyedéves és éves előállítása |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – jelenti azt, hogy átlagos százalékos hiba

Ez a táblázat is látható, fontos meglehetősen rövid és hosszú távú forgatókönyvek előrejelzés, mivel ezek képviselik a különféle üzleti igényekhez, és előfordulhat, hogy különböző üzembe helyezési és felhasználási minták között.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Példa használati 1. eset: eSmart rendszerek – a túlterhelési optimalizálása
A fontos szerepet egy [intelligens](https://en.wikipedia.org/wiki/Smart_grid) dinamikus, folyamatosan optimalizálása, majd állítsa be a változó használati mintákat. Energiafogyasztás befolyásolja rövid távú módosításával, főleg hőmérséklet ingadozások által okozott (*például*, további power vezeték nélkül regisztrálja az állapot vagy a fűtésrendszerek szolgál). Egy időben hosszú távú trendek energiafogyasztás is befolyásol. Ezek közé tartozhatnak szezonalitás hatások, nemzeti ünnepek, hosszú távú használat növekedési és még gazdasági tényezők, például fogyasztói index, az olaj ár és a GDP.

Az ezt a használati esetet [eSmart](https://www.esmartsystems.com/) szeretne telepíteni egy felhőalapú megoldás, amely lehetővé teszi, hogy a magasabb kategóriájú egy túlterhelési helyzet a bármely megadott állomás a rács előrejelzésére. Különösen eSmart szerette volna azonosítani, többek között alállomásokkal, amelyek valószínűleg túlterhelni a következő órán belül, így az azonnali intézkedést elkerüléséhez vagy megoldásához helyzet lehet hozni.

Pontos és előrejelzési gyors végrehajtása szükséges három prediktív modelleket végrehajtása:

* Hosszú távú modell, amely lehetővé teszi, hogy a következő néhány hetek vagy hónapok során minden egyes állomás a energiafogyasztás előrejelzés
* Rövid távú modell, amely lehetővé teszi a következő órában megadott állomás túlterhelési helyzet előrejelzését
* Hőmérséklet-modell által biztosított jövőbeli hőmérséklet számos módja létezik keresztüli előrejelzés

A hosszú távú modell célja, hogy az alállomások rangsor során a következő week vagy month (a megadott azok power átviteli kapacitás) túlterhelni a magasabb kategóriájú szerint. Ez lehetővé teszi egy rövid lista, amely a rövid távú előrejelzéshez bemenetként szolgál az alállomások fogadják a létrehozását. Mivel hőmérséklet-fontos előjelző a hosszú távú modell, szükség van folyamatosan több forgatókönyv hőmérséklet előrejelzések létrehozására, és továbbíthatja azokat a hosszú távú modell be bemenetként. A rövid távú előrejelzés majd meghívásainak előre jelezni, melyik állomás valószínű, hogy a következő órában túlterhelni.

A rövid és hosszú távú modellek minden állomás egy külön-külön vannak telepítve. Ezért ezek a modellek gyakorlati végrehajtásának kiterjedt vezénylési van szükség. Nagyobb előrejelzési pontosság próbál a jeggyel rövid távon, a részletesebb modell a nap minden órára van kijelölve. Ezek a modellek a rendszer végrehajtja a minden órában, és a válaszadáshoz és a megelőző jellegű műveleteket, ha szükséges, adjon elég időt az néhány percen belül végrehajtása befejeződött. Ez a témakörgyűjtemény modellek naprakész időszakos megőrzési a legfrissebb adatok használatával.

További információ a használati eset található [Itt](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Használja a kis minősítési feltételeknek – Előfeltételek
Cortana Intelligence fő erőssége, telepíthetők és méretezhetők a machine learning-központú megoldások hatékony képessége. Több ezer támogatása és az előrejelzések párhuzamosan végrehajtott szolgál. Egy változó felhasználási minta kielégítése érdekében automatikusan képes méretezni. A megoldás ezért célja a pontosság és a számítási teljesítményt. Például a vállalat előrejelzési következő órában, és a nap minden órára pontos energiaszükségletét előállító iránt. Másrészről, kevesebb iránt, miért érdemes az igény szerinti összegyűjtése várható lennie, mert az a kérdés megválaszolásával tudjuk (modellelemek a modellben gondoskodik, amelyek).

Ezért fontos vegye figyelembe, hogy nem minden használati esetek és üzleti problémák hatékonyan megoldhatók machine learning segítségével.

Cortana Intelligence és a gépi tanulás lehet hatékonyan az egy adott üzleti probléma megoldására, a következő feltételek teljesülése esetén:

* Az aktuális üzleti probléma, **prediktív** jellegűek. Egy prediktív használata eset például, vállalat, szeretnénk előre jelezni egy adott állomás power terhelését a következő órában. Másrészről, elemzése és illesztőprogramok előzményadatok alapján a rangsorolás lenne **leíró** jellegű, ezért kevesebb alkalmazható.
* Nyilvánvaló **művelet az elérési út** végrehajtását követően az előrejelzési érhető el. Például előrejelzésére egy állomás a következő órában túlterhelés is indíthat, hogy az állomás kapcsolódó terhelés csökkentése és a megelőzve potenciálisan túlterhelés esetén használható proaktív művelet.
* A használati eset jelöli egy **tipikus típusú probléma** úgy, hogy ha megoldott azt is váltaniuk elkészítésétől más hasonló használati esetek.
* Az ügyfél állíthatja **mennyiségi és minőségi célok** bemutatása egy megoldás sikeres végrehajtását. Például az energia kereslet-előrejelzés helyes mennyiségi cél lenne a szükséges pontosság küszöbértéket (*például*, legfeljebb 5 %-os hiba engedélyezett), vagy ha állomás előrejelzésére túlterhelni majd a pontosság (valódi pozitívok mértéke) és a visszaírási (valódi pozitívok mértéke) kell lennie egy adott küszöbérték feletti. Ezen célok kell származik, az ügyfél üzleti céljainak megvalósítását.
* Nyilvánvaló **integrációjának** a vállalat üzleti munkafolyamattal. Például az állomás terhelés előrejelzés integrálhatók a rács vezérlőközpontba lehetővé teszi a túlterhelési megelőzési tevékenységek.
* Az ügyfél rendelkezik használatra kész **adatokat a megfelelő minőségű** használati eset támogatására (bővebb információt a következő szakaszban a **adatminőség**, az a forgatókönyv).
* Az ügyfél támogatja a standardokat központú adatok felhőarchitektúra vagy **felhőalapú és gépi tanulási**, többek között az Azure ML és az egyéb Cortana Intelligence Suite összetevői.
* Az ügyfél hajlandó létrehozására **egy teljes körű adatfolyam** a létesítmények kézbesítését az adatok folyamatosan, a felhőbe, és hajlandó **üzembe helyezése** a megoldás.
* Az ügyfél készen áll a **dedikált erőforrások** ki fogja lehet aktívan foglalkozom kezdeti kísérleti végrehajtása során, hogy a Tudásbázis és a megoldás tulajdonjogát át lehet vinni az ügyfél sikeres telepítést.
* Az ügyfél erőforrás kell lennie egy **képzett adatok professional**, lehetőleg értenie az adatokhoz.

A feltételnek, a fenti feltételek alapján használati esetek nagy mértékben javíthatják a a sikerességi arányokat, használati esetek és létrehozni egy jó beachhead jövőbeli használati esetek végrehajtására.

### <a name="cloud-based-solutions"></a>Felhőalapú megoldások
Az Azure-ban a Cortana Intelligence Suite olyan integrált környezetben, amely a felhőben található. Egy fejlett analitikai megoldást egy felhőalapú környezetben a központi telepítés tartalmazza a vállalkozások számára, és a egy időben technológiáival jelentheti big Data jellegű módosítása a vállalatok számára, hogy továbbra is használja a helyi IT-megoldásokhoz. Az energia ágazat belül nincs egyértelmű a tendencia fokozatos migrálás a felhőbe műveletek. Erre az irányra kerül szakembereinek közreműködésével együtt a fejlesztés az intelligens rács mint már említettük, a **trendek**. Ez a forgatókönyv egy felhőalapú megoldás az energiaszektor tartományban összpontosít, fontos annak magyarázata, az előnyei és a egy felhőalapú megoldás üzembe helyezésének egyéb szempontok.

Például egy felhőalapú megoldás legnagyobb előnye a költségét. Megoldás használ felhőalapú által telepített összetevők, nem előzetes költségek, vagy (a termékek eladott) COGS összetevők költsége helyett társítva. Ez azt jelenti, hogy nem kell hardveres, szoftveres és informatikai karbantartási támogatásán, és ezért nincs jelentős üzleti kockázat csökkentése.

Egy másik előnye, felhőalapú megoldások használatalapú költség struktúráját. Kiszolgálók felhőalapú számítási vagy tárolási telepíthető és horizontálisan csak – igény szerint történik. Egy felhőalapú megoldás költség hatékonyságát előnyeit Ez jelöli.

Végül ott, nem szükséges a portfóliónk fejlesztésén informatikai karbantartás, illetve a jövőbeli infrastruktúrájának fejlesztését, mivel ez a felhőalapú ajánlat része. Olyan mértékben, hogy a Cortana Intelligence csomag tartalmazza a legjobb osztály szolgáltatások, és annak közúti térképes tartja a folyamatosan fejlődő. Új funkciók, összetevőkhöz és képességekhez folyamatosan bevezetett és hogyan fejlesztheti tovább.

A vállalat, amely a felhőbe az átmenet indítása magas és útválasztókra sincs fokozatos megközelítés érvénybe léptetésével egy felhőbeli migrálás közúti térképes. Úgy véli, hogy segédprogramok és vállalatok energia a tartományban, a használati esetek, a forgatókönyv tárgyalt kiváló lehetőség a felhőbeli prediktív elemzési megoldások kiszolgálóinkat jelölik.

#### <a name="business-case-justification-considerations"></a>Kis üzleti indoklás kapcsolatos szempontok
Sok esetben az ügyfél lehet egy adott alkalmazási helyzet, amelyben egy felhőalapú megoldás és a Machine Learning összetevői fontos üzleti indoklásának így iránt. Egy helyszíni megoldás esetében egy felhőalapú megoldás eltérően az előzetes költségek összetevő minimális és a legtöbb a költségelemek társítva a tényleges használat. Esetén, a-előrejelzési megoldás a Cortana Intelligence Suite energiát, több szolgáltatás integrálható legyen az egyetlen közös költség struktúrára. Például adatbázisok (*például*, SQL Azure) a nyers adatok tárolására használható, és ezután az Azure Machine Learning a tényleges előrejelzések tárolására használt előrejelzési szolgáltatásokat. Ebben a példában a költségek struktúra storage és a tranzakciós összetevők lehetnek.

Másrészről rendelkeznie kell egy jól ismerik az energia kereslet-előrejelzési (rövid vagy hosszú távú) működő üzleti értékét a. Valójában a fontos az üzleti értéket minden egyes előrejelzési művelet. Például pontosan előrejelzés power betöltése a következő 24 órában megakadályozhatja, hogy túltermelés vagy segít megakadályozni, hogy a rács túlterheléssel, és ez számszerűen kell naponta pénzügyi megtakarítások tekintetében.

A pénzügyi előnyeit, igény szerint kiszámításának egyszerű képlet-előrejelzési megoldás az lenne: ![egyszerű képlet a pénzügyi előnyeit, igény szerint kiszámításának-előrejelzési megoldás](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Cortana Intelligence Suite használatalapú díjszabási modellt biztosít, mivel nincs kellene ezt a képletet egy állandó költség összetevő nincs szükség. Ez a képlet napi, havi vagy éves alapján számítható ki.

Aktuális Cortana Intelligence Suite és az Azure Machine Learning díjszabási csomagokat található [Itt](https://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Megoldás fejlesztési folyamata
A fejlesztési ciklus egy energia kereslet-előrejelzési megoldás általában 4 fázisra biztosítjuk, amelyek mindegyikét magában foglalja a felhőalapú technológiák és szolgáltatások ismerik a Cortana Intelligence Suite használata.

Ez az alábbi ábrán látható:

![Intelligens ciklus](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

A következő bekezdést a 4. lépés folyamatát ismerteti:

1. **Adatgyűjtés** – semmilyen speciális alapú analytics megoldás adatokon alapul. (lásd: **Data Understanding**). Pontosabban a esetén, a prediktív elemzés és előrejelzését, telefonszámokkal kapcsolatos adatok folyamatban lévő, dinamikus folyamat. Energia kereslet-előrejelzés, esetén ezeket az adatokat közvetlenül az intelligens mérőktől származó beolvasva is, vagy már összesíteni kell egy helyi adatbázist. Azt is támaszkodik más külső forrásokból, például az időjárás és hőmérséklettel kapcsolatos adatok. Az adatok folyamatban lévő folyamat kell vezényelt, ütemezett és tárolva. [Az Azure Data Factory](https://azure.microsoft.com/services/data-factory/) (ADF) a fő workhorse szól ez a feladat teljesítéséért.
2. **Modellezés** – pontos és megbízható energia-előrejelzés, (train) fejlesztéséhez és karbantartásához, hogy révén az előzményadatok használja, és kinyeri az adatokat a beállítás után sokatmondóbbak és prediktív mintákat egy nagyszerű modellt kell egyet. A terület a Machine Learning (gépi tanulás) rendelkezik lett gyorsan növekszik az speciális algoritmusokkal rendszeresen fejlesztés alatt áll. Az Azure gépi tanulás Stúdióban, amely segít a legtöbb fejlett gépi Tanulási algoritmusokat, egy teljes munkahelyi flow-ban használják nagyszerű felhasználói élményt nyújt. A munkafolyamat egy intuitív folyamatábrája mutatja be, és az adat-előkészítés, a szolgáltatás kivonása, modellezés és modell kiértékelése tartalmazza. Több száz különböző modelleket, amelyek szerepelnek ebben a környezetben, a felhasználó lehet kötni. Ebben a fázisban a végén működő modell, amely teljes mértékben kiértékelt és készen áll a központi telepítési kell értenie az adatokhoz.
   
   Az alábbi ábrán egy tipikus munkafolyamat ábrája:
   
   ![Modellezési munkafolyamat](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Üzembe helyezés** – egy működő modellel, a következő lépés a központi telepítés. A modell itt egy webszolgáltatás, amelyet tesz elérhetővé, amelyek különféle felhasználási ügyfelek az interneten keresztül egyidejűleg hívható RESTful API lesz konvertálva. Az Azure Machine Learning egy modellt közvetlenül az Azure ML Studio-egyetlen gombnyomással üzembe helyezésének egyszerű módszert kínál. A teljes üzembe helyezési folyamat technikai részletek történik. Ez a megoldás megfelel a szükséges használatalapú automatikusan méretezheti.
4. **Felhasználás** – ebben a fázisban, hogy ténylegesen győződjön meg arról, használja az előrejelzési modell előrejelzéses előállításához. A használatalapú is meghatározni a felhasználó-alkalmazás (*például*, irányítópult), vagy közvetlenül a egy működési rendszer például igény szerinti/rendszer és a egy rács optimization megoldása. Több használati esetek is meghatározni egy egyetlen modellből.

## <a name="data-understanding"></a>Adatok megismerése
Az üzleti szempontok kiterjedő után (lásd: **az üzleti igények felmérése**), az energia kereslet-előrejelzési megoldás, azt most már készen áll az adatok rész tárgyalják. Prediktív elemzési megoldások megbízható adatokon alapul. Energia kereslet-előrejelzés, hogy támaszkodhat a különböző részletességi korábbi használati adatokat. Hogy a nyersanyagok lesz korábbi adatok alapján. Azt, amelyben az adatszakértő előrejelzőket (más néven szolgáltatások), amely is elhelyezhető egy modellt, amely végül hoz létre a szükséges előrejelzések azonosítja analizálását halad át.

Ez a szakasz a többi bemutatunk néhányat a különféle lépéseit és szempontjait az adatok és a egy formába – az életre annak megértésében.

### <a name="the-model-development-cycle"></a>A modell fejlesztési ciklus
Jó előrejelzési modellek bizonyos gondos előkészületeket és tervezési előállító. Használhatatlanná tévő a modellezési folyamat több lépésekre bonthatók le, és egyszerre csak egy lépéssel összpontosító sikerült jelentősen növelhető a teljes folyamat eredményéről.

A következő diagram azt ábrázolja, hogyan a modellezési folyamat sikerült kell bontani több lépést:

![Modell fejlesztési ciklus](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Ahogy láthatja a ciklus hat lépésekből áll:

* A probléma kialakításához
* Adatbetöltés és az adatok feltárása
* Adat-előkészítési és funkciófejlesztési feladatok
* Modellezés
* Modell értékelése
* Fejlesztés

Ez a szakasz további részében található bemutatunk néhányat az egyes lépések és a szükséges elemeket minden lépésnél.

### <a name="problem-formulation"></a>A probléma kialakításához
Azt is érdemes lehet a probléma kialakításához, a kritikus fontosságú lépés egy kell tennie minden olyan prediktív elemzési megoldás bevezetése előtt. Itt mi lenne az üzleti problémát átalakítása és bontható fel azt az adott elemeket, amelyek segítségével adatokat, illetve modellezési technikák megoldhatók. Tanácsos határozhatja meg a probléma, azt szeretnénk, hogy megválaszoljuk a kérdésekre. Az alábbiakban a megfelelő energia kereslet-előrejelzés hatókörén belül lehet lehetséges kérdésekre kaphat választ:

* Mi várható terhelésének az egyes állomás a következő óra vagy nap?
* A rács a nap melyik időpontjában tapasztalható megugrása?
* Mennyire valószínű van saját rács a várható terhelés fenntartása érdekében?
* Mekkora teljesítményre kell az energiagazdálkodási állomás létrehozása a nap minden órában?

Ezeket a kérdéseket kialakítása lehetővé teszi számunkra, hogy a megfelelő adatokat és a egy megoldás, amely teljes mértékben teljesíti az üzleti problémára végrehajtási összpontosíthat. Továbbá hogy ezután állíthatja be néhány kulcsfontosságú metrikákhoz kaphat, amelyek lehetővé teszik számunkra, hogy a modell teljesítményét értékeli. Például hogyan pontos az előrejelzés legyen, és mi az a hiba, amely továbbra is lenne a vállalkozás számára elfogadható tartomány?

### <a name="data-sources"></a>Adatforrások
A modern intelligens rács gyűjti az adatokat különböző részeit és a rács összetevői. Ezeket az adatokat az üzemeltetés minőségének különböző szempontjait, a művelet és a kihasználtság a kiemelt rács jelöli. Előrejelzési energiaszükségletét hatókörén belül azt is korlátozza a beszélgetések adatforrások, amelyek tükrözik a tényleges használat. Energiafogyasztás egyik fontos forrása olyan intelligens mérőktől. A világ minden pontján segédprogramok gyorsan végzi el intelligens mérőktől márkák számára. Intelligens mérőktől jegyezze fel a tényleges energiafogyasztását, és folyamatosan továbbítja az adatokat a vállalat. Adatok gyűjtése és küldött vissza rögzített időközönként, és a 5 percenként 1 óra. Speciális intelligens mérőktől programozása távolról ellenőrzése és a tényleges használat háztartását belüli elosztása. Intelligens mérőszám az adatok viszonylag megbízható, és egy időbélyegzőt tartalmaz. Így a kereslet-előrejelzési fontos összetevő. Fogyasztásmérő adatok összesíthetők (összegzett) különböző szinteken belül a rács topológia: átalakító, állomás, régió, *stb*. Azt a szükséges összesítés szint hozhat létre egy előrejelzési modellt, majd kiválaszthatja. Például ha szeretné, hogy a vállalat jövőbeli terhelését a rács alállomások mindegyike az előre jelzett majd minden mérőszámok adatokat is kell minden egyes állomás a adatforgalmát összesítjük bemenetként az előrejelzési modell. Nevezzük intelligens mérőktől belső adatforrásként.

Egy megbízható energia értékesítési előrejelzés is támaszkodik más külső adatforrásokhoz. Egy fontos tényező, amely befolyásolja az energiafogyasztás az időjárás, vagy még pontosabban a hőmérséklet. Előzményadatok erős korrelációját külső hőmérséklet és energiafogyasztását jeleníti meg. Gyakori elérésű nyár napban fogyasztók győződjön meg azok légkondicionálók és a téli olimpiai teljesítmény, a fűtésrendszerek során. Egy megbízható forrásból a rács helyen korábbi hőmérsékletek ezért kulcs. Ezenkívül azt is támaszkodik pontos előrejelzését, hőmérséklet, egy előjelző energiafogyasztás.

Egyéb külső adatforrásokhoz is segít az energia kereslet-előrejelzési modellek létrehozása. Ezek közé tartozhatnak a hosszú távú éghajlatváltozás módosításokat, gazdaságos indexek (*például*, a GDP), és másokkal. Ebben a dokumentumban a Microsoft nem tartalmazza ezeket más adatforrásokkal.

### <a name="data-structure"></a>Adatszerkezet
A szükséges adatforrások azonosítása, után szeretnénk győződjön meg arról, hogy a nyers adatokat összegyűjtötte-e a megfelelő adatok szolgáltatásokat tartalmazza. Egy megbízható kereslet-előrejelzési modell, hogy kell lennie annak érdekében, hogy az összegyűjtött adatokat tartalmazza, amely segíthet a kereslet-előrejelzéseket adatelem. Íme néhány a nyers adatok adatok szerkezetének (séma) vonatkozó minimális követelményeinek.

A nyers adatok sorok és oszlopok áll. Minden mérték jelenik meg az adatok egyetlen sor. Minden egyes sorára adatok (más néven funkciók vagy mezők) több oszlopot tartalmaz.

1. **Időbélyeg** – az időbélyegmezőt a tényleges idő, mikor lett rögzítve a mérték jelöli. Azt meg kell felelnie az Általános dátum/idő-formátumok egyikét. Dátum- és részek tartalmaznia kell. A legtöbb esetben van, nem szükséges időpontját, a második szintű granularitási eddig rögzítve. Fontos adja meg az adott időzóna, amelyben az adat keletkezik.
2. **Mérőszám azonosítója** – Ez a mező mérőszám vagy a készülék azonosítja. Egy kategorikus változó, és számjegyek és karakterek kombinációját is lehet.
3. **Fogyasztási érték** – Ez az adott dátum/idő a tényleges használat. A használatalapú mérhető kWh-ban (kilowatt-hour), vagy bármely más javasolt egységek. Fontos megjegyezni, hogy a mérési egysége kell maradnia konzisztens az összes mérték, az adatok között. Bizonyos esetekben fogyasztás lehet biztosítani a több mint 3 power fázisait. Ebben az esetben azt kell gyűjteni a független fogyasztás szakaszok.
4. **Hőmérséklet** – a hőmérséklet általában független forrásból összegyűjtött. Azonban azt kell kompatibilis a használati adatokat. A timestamp fent leírtak szerint, amely lehetővé teszi, hogy a tényleges használati adatokat szinkronizálni kell tartalmaznia. A hőmérséklet érték Celsius-fokban vagy Fahrenheit adható meg, de kell minden mérések között konzisztens marad.
5. **Hely –** a hely mező kitöltése általában a hely, ahol a hőmérsékleti adatokat összegyűjtötte-e társítva. Zip-kód számként vagy szélességi és hosszúsági koordinátákkal (szél/hosszú) formátumban is képviseli.

Az alábbi táblázat egy jó felhasználás és hőmérséklet-adatformátum példákat mutat be:

| **Dátum** | **idő** | **Fogyasztásmérő azonosítója** | **1. fázis** | **2. fázis** | **3. fázis** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Dátum** | **idő** | **Hely** | **hőmérséklet** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Ahogy fent látható, az ebben a példában 3 power fázisok társított fogyasztás 3 különböző értékeket tartalmaz. Vegye figyelembe azt is, hogy a dátum és idő mezők el vannak különítve, azonban akkor is kombinálhatók egyetlen oszlopba. Ebben az esetben a hely oszlopban jelennek meg egy 5 számjegyű zip-kód formátuma és a hőmérséklet mértékben Celsius formátumban.

### <a name="data-format"></a>Adatformátum
Cortana Intelligence Suite is támogatja a CSV, TSV, JSON-t, mint a leggyakoribb adatformátumok a célnyelven *stb*. Fontos, hogy az adatok formátumát a projekt teljes életciklusát a konzisztens marad.

### <a name="data-ingestion"></a>Adatfeldolgozás
Energia igény-előrejelzési folyamatosan és gyakori összegyűjtése várható, mivel azt biztosítania kell, hogy egy szilárd és megbízható Adatbetöltési folyamat útján áramlik a nyers adatokat. A betöltési folyamat biztosítania kell, hogy a nyers adatok érhető el az előrejelzési folyamat a szükséges időben. Ez azt jelenti, hogy az adatok betöltési gyakorisága előrejelzési gyakoriságát nagyobbnak kell lennie.

Példa: Ha a kereslet-előrejelzési megoldás hoz létre egy új előrejelzés naponta 8:00 órakor, akkor győződjön meg arról, hogy mind az adatok az elmúlt 24 órában gyűjtött rendelkezik lett teljesen betöltött eddig a pont, és még rendelkezik tartalmaz-e az elmúlt órában a kell  adatok.

Annak érdekében, hogy ennek érdekében a Cortana Intelligence Suite egy megbízható Adatbetöltési folyamat támogatásához különféle lehetőségeket biztosít a. Ez további tárgyalja a **üzembe helyezési** szakasz ebben a dokumentumban.

### <a name="data-quality"></a>Adatminőség
A legmegbízhatóbb és legpontosabb kereslet-előrejelzés végrehajtásához szükséges nyers adatok forrás néhány alapvető adatokat minőségi feltételeknek kell megfelelnie. Bár a meghiúsult lépések kompenzációjához minőségi lehetséges probléma speciális statisztikai módszerek segítségével, még mindig győződjön meg arról, hogy néhány alapadatok minőségi küszöbértéket, amikor új adatok feldolgozása vannak metsző kell. Az alábbiakban a nyers adatok minőséggel kapcsolatos néhány megfontolandó szempont:

* **Hiányzó értéket** – Ez vonatkozik a helyzetet, amikor adott mérték nem gyűjtötte a program. Az alapvető követelmény az, hogy hiányzó értéket gyakorisága nem lehet nagyobb, mint 10 % bármely adott időtartamon. Az eset, hogy egyetlen érték hiányzik, fel kell tüntetni egy előre meghatározott érték használatával (például: "9999"), és nem "0", amely egy érvényes érték lehet.
* **Mérték pontosságának** – a tényleges érték, vagy a hőmérséklet pontosan kell rögzíteni. Pontatlan mérések pontos előrejelzéseket állítja elő. A mérési hiba általában a true érték 1 % alacsonyabbnak kell lennie.
* **A mérési idő** – szükséges a tényleges mérési igaz atáridő több mint 10 másodpercet, hogy az adatok tényleges időbélyegzője gyűjtött fog nem tér.
* **Szinkronizálási** – Ha több adatforrást használ (*például*, felhasználás és hőmérséklet) kell biztosítható, hogy nincsenek-e nem időszinkronizálás problémák közöttük. Ez azt jelenti, hogy az összegyűjtött időbélyeg két független adatforrásokból származó közötti nem haladhatja meg a több mint 10 másodperc.
* **Késés** – mint már említettük, a **adatbetöltés**, azt egy megbízható folyamat és a betöltési folyamat függenek. Szabályozhatja, hogy azt biztosítania kell, hogy az adatok késés befolyásolni. Ez az idő, amelyet a tényleges mérték lett, és az időpont, amely betöltődik a Cortana Intelligence Suite storage-ba, és készen áll a használatra közötti van megadva. A rövid távú terheléselosztást a teljes késést előrejelzés nem lehet nagyobb, mint 30 perc. A hosszú távú terheléselosztást a teljes késést előrejelzés nem lehet nagyobb, mint 1 nap.

### <a name="data-preparation-and-feature-engineering"></a>Adat-előkészítési és Funkciófejlesztési feladatok
Miután a nyers adatok keresztül a szervezetbe (lásd: **adatbetöltés**), és biztonságosan tárolva, készen áll a dolgozhatók. Az adat-előkészítési fázisban alapvetően véve a nyers adatokat, és átalakítása (átalakítása, átalakításakor), a modellezési fázishoz űrlapot. Amely tartalmazhat például a nyers adatok oszlopban, a tényleges mért értékeit, a szabványos értékek, az összetettebb műveleteket, mint például az egyszerű műveleteket [alkalommal elmaradt](https://en.wikipedia.org/wiki/Lag_operator), és másokkal. Az újonnan létrehozott oszlopokat nevezzük funkciókat, és létrehozni bloberőforrásokhoz, ezeket nevezik funkciófejlesztési. Ez a folyamat végén kell azt egy új adatkészlet, amely a nyers adatok kinyert és modellezés is használható. Emellett az adat-előkészítési fázisban kell használatával hitelesítik a hiányzó értékeket (lásd: **adatminőség**) és a számukra. Néhány esetben azt is kellene normalizálása győződjön meg arról, hogy minden érték megjelenjen-e az azonos méretezési csoportban lévő adatokat.

Ebben a szakaszban látható a közös adatok funkcióit, amelyek szerepelnek a felhasznált energia igény-előrejelzési modelleket.

**Szolgáltatások driven idő:** ezeket a funkciókat a dátum/timestamp adatok származnak. Ezeket ki kell olvasni, konvertálva a kategorikus funkciók, például:

* Idő nap – Ez az az a nap, amely értékek 0 és 23 óra
* Nap hét – Ez a hét napját jelöli, és 1 közötti értékeket vesz igénybe (vasárnap) és 7 (szombat)
* Naponta, hónap – Ez az aktuális dátumot jelöli, és értékek 1 és 31 is igénybe vehet.
* Hónap év – Ez a hónapot jelöli, és 1 közötti értékeket vesz igénybe (január) és 12 (December)
* Hétvégi – Ez a bináris érték funkció, amely az értékek 0 weekdays vagy 1-es a hétvége
* Szünnap – Ez a bináris érték funkció, amely szükséges időt a 0 értéket egy szünnap rendszeres napi vagy 1-es
* Fourier feltételek – Fourier feltételek, amelyek alapján történő küldés időbélyegzője legyen, és a szezonalitás értékének (cycles) rögzítéséhez használt súlyok az adatokat. Mivel az adatokban található több szezon talán van szükségünk előfordulhat, hogy több Fourier használati. Igény szerinti értékeket Előfordulhat például, éves, heti és napi szezon/ciklusok ami 3 Fourier feltételek eredményezi.

**Független mérési funkciókat:** független szolgáltatásai közé tartozik a szeretnénk használja, mint a modellben előrejelzőket minden adatelemet. Itt Elzárkózunk a függő szolgáltatást, amely azt kellene előre jelezni.

* Késés szolgáltatás – ezek az ideje, hogy áttért a tényleges igény szerinti értékét. Lag 1 funkciók például tartsa a igény szerinti értékét az előző órában (óránkénti adatok feltételezve) az aktuális timestamp viszonyítva. Ehhez hasonlóan azt hozzáadása lag 2, 3, lag *stb*. Használt lag szolgáltatásai tényleges kombinációja határozza meg a modellezési fázis során a modell eredmények értékelése.
* Hosszú távú felkapott – Ez a funkció igény szerinti év közötti lineáris növekedésének jelöli.

**A függő szolgáltatás:** a függő lehetőség a oszlop, amelyeket szeretnénk előre jelezni a modell. A [felügyelt machine learning](https://en.wikipedia.org/wiki/Supervised_learning), a modellt, a szolgáltatások használatával (amely szintén nevezzük címkék) először be kell. Ez lehetővé teszi, hogy ismerje meg a mintákat a függő szolgáltatás társított adatokat a modell. Az előrejelzés energiaszükségletét általában szeretnénk előre jelezni a tényleges igény szerint, és így tudjuk használni, a függő szolgáltatás.

**Kezelési érték hiányzik:** az adat-előkészítési fázisban azt kellene kezelni a hiányzó értékek legjobb stratégiáját határozza meg. Ez főleg használatával hajtható végre a különféle statisztikai [imputálási metody dat](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). Esetén energia kereslet-előrejelzés, hogy általában imputálására hiányzó értékeket az előző elérhető adatpontok mozgóátlag használatával.

**Adatok normalizálási:** adatok normalizálási az átalakítást, amely hasonló méretezési csoport összes numerikus adatok, mint a kereslet-előrejelzési beemelése használható egy másik típusa. Ez általában javítja a pontosság és a pontosság. Hogy általában ehhez a tényleges érték elosztjuk az adatok tartományán.
Ez lesz az eredeti érték vertikális leskálázás kisebb tartományba, általában 1 és 1 között.

## <a name="modeling"></a>Modellezés
A modellezés fázisban, ahol az adatokat a modellbe való átalakítása történik. Az alapvető van ezt a folyamatot a speciális algoritmusok, amely az előzményadatok (betanítási adatok) vizsgálata, bontsa ki a mintákat és modell létrehozása. Ehhez a modellhez később használható előre jelezni az új adatokat, amelyek nem használták a modell létrehozásához.

Ha már rendelkezünk egy működő megbízható modell majd használhatók, amely tartalmazza a szükséges funkciókat (X) van felépítve új adatok pontozása céljából. A pontozó folyamat fogja elérhetővé tenni, használja a megőrzött modell (a betanítási fázis objektumot), és előrejelezheti a célváltozó, amely Ŷ helyén.

### <a name="demand-forecasting-modeling-techniques"></a>Kereslet-előrejelzési modellezési technikák
Kereslet-előrejelzési biztosítjuk, esetén használja az előzményadatok, amely idő alapján vannak rendezve. Általában nevezzük, amely tartalmazza az idődimenzió adatok [time series](https://en.wikipedia.org/wiki/Time_series). A time series modellezési célja, hogy a keresés kapcsolatos trendeket, szezonalitás, automatikus összefüggések (korrelációs időbeli alakulása), time, és állítson össze azokat a modellbe.

Az elmúlt években a speciális algoritmusok fejlesztettek befogadásához idősorozat-előrejelzés és az előrejelzési pontosság növeléséhez. Röviden bemutatjuk, néhány őket itt.

> [!NOTE]
> Ez a szakasz nem célja, hogy egy machine learning és az előrejelzés – áttekintés, hanem inkább egy rövid kérdőív modellezési technikák, általában használt kereslet-előrejelzés használható. További információk és az oktatási anyagok kapcsolatos idősorozat-előrejelzés, erősen ajánlott az online könyv [előrejelzés: elvek és eljárás](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (mozgóátlag)**](https://www.otexts.org/fpp/6/2)
Mozgóátlag egyike az első elemzési módszereket, az idősorozat-előrejelzés használt, és továbbra is a legtöbb egyik leggyakrabban használt mai technikákat. Emellett akkor is alapját speciális technikák előrejelzéseket. A mozgó átlag azt is előrejelzési a következő adatpont által átlagosan kevesebb mint a K legutóbbi pontokat, ahol K azt jelzi, hogy a mozgó átlag sorrendje felett.

A mozgó átlag technika simítás az előrejelzés hatásának rendelkezik, és emiatt előfordulhat, hogy nem kezeli a is nagy, az adatok változékonyságán.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (exponenciális simítás)**](https://www.otexts.org/fpp/7/5)
Exponenciális simítás (ETS) olyan különböző módszereket használó legutóbbi adatpontok súlyozott átlag annak érdekében, hogy a következő adatpont előrejelzése összessége. A cél, hogy nagyobb súly hozzárendelése a legújabb értékeket, és fokozatosan csökkentse a régebbi mért értékek súlya. Számos különböző módszerek és a termékcsalád, azokat közé tartoznak például az adatok szezonalitás kezelése [Holt-Winters szezonális metódus](https://www.otexts.org/fpp/7/5).

Ezek a módszerek közül is figyelembe vennie a szezonalitás értékének az adatok.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automatikus regressziós integrált mozgóátlag)**](https://www.otexts.org/fpp/8)
Automatikus regressziós integrált mozgó átlag (ARIMA) idősor-előrejelzési gyakran használt módszerek egy másik család. Gyakorlatilag kombinálja automatikus regression módszerek mozgóátlaga. Automatikus regression módszerek tovább a sorozat előző időértékek regressziós modelleket annak érdekében, hogy a következő dátum pont számítási használja. ARIMA módszereket is érvényesek lesznek a különbséglemezek módszerek, például az adatpontok közötti különbség kiszámítása, és azok eredeti mért érték helyett használja. Végül ARIMA is használ a fent tárgyalt mozgó átlagos technikákat. Különböző módokon ezen módszerek mindegyike együttes használata, mit hoz létre a családba tartozó ARIMA módszerek.

ETS és ARIMA széles körben használják még ma energia kereslet-előrejelzés és sok más előrejelzési problémákat. A legtöbb esetben ezek együttesen együtt, így gyorsabban nagyon pontos.

**Általános többváltozós regresszió** regressziós modellek gépi tanulás és a statisztikáit, a tartományban a legfontosabb modellezési megközelítés lehet. A time series összefüggésében használjuk regressziós előre jelezni a jövőbeli értékek (*például*, a kereslet). Regresszió hozunk a előrejelzőket lineáris kombinációját is, és ismerje meg, a képzés során ezeket előrejelzőket súlyok (más néven együttható). A cél, hogy egy regressziós egyenes, amely az előre jelzett érték lesz előrejelzési előállításához. Regressziós módszert alkalmasak, ha a célváltozót numerikus, és így is megfelel idősorozat-előrejelzés. Számos különféle regressziós módszert, többek között például a rendkívül egyszerű regressziós modellek van [lineáris regressziós](https://en.wikipedia.org/wiki/Linear_regression) és azokról, például a döntési fákat, a speciális [véletlenszerű erdők](https://en.wikipedia.org/wiki/Random_forest), [Neural Hálózatok](https://en.wikipedia.org/wiki/Artificial_neural_network), és a súlyozott döntési fák.

Hozhat létre, amely regressziós problémaként előrejelzés energiaszükségletét biztosít számunkra nagy rugalmasságot biztosít a jelölje ki a data-funkciókat kombinálhatók, amely a tényleges idősorozat-adatok és a külső tényezők, például a hőmérséklet. További információ a kijelölt szolgáltatások tárgyalja a szolgáltatás Engineering (lásd: **adat-előkészítési és a szolgáltatás mérnöki**) szakaszában a forgatókönyv.

A megvalósítás és a telepítési energia igény szerint előrejelzések próbaüzem tapasztalatainkon találtunk, hogy a speciális regressziós modellek az Azure gépi tanulás által biztosított általában a legjobb eredményeket, és elérhetővé azokat használni.

## <a name="model-evaluation"></a>Modell értékelése
Modell értékelése belül szerepük van az **modell fejlesztési ciklus**. Ebben a lépésben mi megvizsgáljuk a modell és a teljesítmény valós adatok ellenőrzése. A modellezés lépés során a rendelkezésre álló adatok egy részét a modell betanításához használjuk. Az értékelési fázis során az adatokat a modell része is. Gyakorlatilag azt jelenti, hogy mi van számból a modell új adatokat, amely rendelkezik átstrukturálása lett, és ugyanazokat a szolgáltatásokat, mint a betanítási adatkészletet tartalmaz. Azonban az érvényesítési folyamat során használjuk a modell előre jelezni a célváltozó adja meg a rendelkezésre álló terület változó helyett. Hogy milyen gyakran tekintse meg ezt a folyamatot, mint a modell pontozása. Azt kellene majd igaz target értéket használja, és hasonlítsa össze azokat előre jelzett olyanokra cserélni. A cél, hogy mérni, és minimalizálja az előrejelzési hiba, ami azt jelenti, az előrejelzéseket és a true érték közötti különbséget. Kulcs mennyiségi meghatározására a hiba mérték azért, mert szeretnénk a modell finomhangolása, és ellenőrizze, hogy ténylegesen csökken a hibát. A modell finomhangoló teheti meg, amelyek vezérlik a tanulási folyamat Modellparaméterek módosításával, vagy hozzáadásával vagy eltávolításával funkciók (néven [paraméterek ismétlés](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Gyakorlatilag ez azt jelenti, hogy előfordulhat, hogy szükségünk között a műszaki elemzésekhez, modellezés, ismételt futtatásával és a modell a többször értékelési fázis amíg nem tudjuk a hibát csökkentheti a szükséges szintre.

Fontos, hogy az előrejelzési hiba soha nem lesznek nulla hangsúlyt soha nincs modell, amely tökéletesen tudja jelezni minden serkenti az eredményt. Van azonban egy bizonyos mértékű hiba, amely a vállalkozás számára elfogadható. Az érvényesítési folyamat során azt szeretnénk, annak érdekében, hogy a modell előrejelzési hiba a szintjén vagy jobb, mint az üzleti tűréshatáron. Ezért fontos, hogy a megengedhető hiba szintjének beállítása a ciklus során elején a **probléma kialakításához** fázisban.

### <a name="typical-evaluation-techniques"></a>Átlagos értékelés technikák
Számos különféle módon, mely előrejelzésbe hiba mérhető és számszerűen. Ebben a szakaszban fogunk dolgozni a hozzászólás a time Series és a kifejezetten az előrejelzési igényeknek megfelelő értékelési technikákat.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE abszolút hibaszázalék jelenti azt, hogy a rövidítése. A MAPE közötti különbség számítástechnika azt minden egyes előrejelzett pont és az adott pont a tényleges érték. Hogy majd számlálása a hiba pontonként szerint kiszámításának az időarány, amíg a különbség a tényleges érték. Az utolsó lépés azt átlagos ezeket az értékeket. A használt MAPE matematikai képlete a következő:

![MAPE képlet](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*ahol A<sub>t</sub> a tényleges érték F<sub>t</sub> az előre jelzett érték van, és n az előrejelzési horizon.*

## <a name="deployment"></a>Környezet
Azt le a modellezési fázis rögzített, és a modellek teljesítményének ellenőrzése után készen állunk az üzembe helyezési fázisnak bele. Ebben a környezetben üzembe helyezési azt jelenti, az ügyfél számára a modell feldolgozása a tényleges előrejelzéseket futó, nagy méretekben engedélyezése. A központi telepítési fogalmát kulcsot az Azure ML azért, mert a fő célunk, hogy folyamatosan meghívása helyett csak az adatok beszerzése a elemzés előrejelzéseket. Az üzembe helyezési fázisnak része funkciótól, nagy mennyiségű fogják a modellt.

Energia kereslet-előrejelzési keretein belül a célja, hogy folyamatos és rendszeres előrejelzések meghívása közben biztosítva, hogy a friss adatok érhető a modellhez és, hogy az adatokat küld vissza a fogyasztó ügyfél.

### <a name="web-services-deployment"></a>A webes szolgáltatások telepítése
Az Azure Machine Learning fő üzembe helyezhető építőelem egy webes szolgáltatás. Ez az a leghatékonyabb megoldást választja ahhoz, hogy a felhőben egy prediktív modellt az felhasználását. A Web service magában foglalja a modell, és a értünk egy [RESTful](https://www.restapitutorial.com/) API (Application Programming Interface). Az API-t az alábbi ábrán szemléltetett módon ügyfél kód részeként is használható.

![We szolgáltatásba, telepítés és használat](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Ahogy látható, a web service a Cortana Intelligence Suite felhőben üzemel, és az elérhetővé tett REST API-végponton keresztül ér el. Különböző típusú ügyfeleket különböző tartományok közötti hívhat meg egyszerre a webes API-n keresztül a szolgáltatás. A web service támogatja a több ezer párhuzamos hívások is méretezheti.

### <a name="a-typical-solution-architecture"></a>Egy tipikus megoldás architektúrája
Az energia kereslet-előrejelzési megoldás üzembe helyezésekor, tudjuk egy teljes körű megoldás, amely megkezdte az előrejelzési webszolgáltatást, és lehetővé teszi a teljes adatfolyamatot üzembe helyezése iránt. Azt új előrejelzés meghívása időpontjában azt kell győződjön meg arról, hogy a modell adatkéréseket az adatok szerepeljenek-szolgáltatások. Ez azt jelenti, hogy az újonnan összegyűjtött nyers adatok folyamatosan betöltött, feldolgozása, és a szükséges szolgáltatást, állítsa be a modell készítették alakítja át. Egyszerre így elérhetővé téve az adatokat használó ügyfelek End szeretnénk. Egy példa adatok folyamat ciklus (vagy adatfolyamat) az alábbi ábra mutatja be:

![Energia igény-előrejelzési adatok végpontok közötti folyamat](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Az energia igény-előrejelzési ciklus részeként végzett, a lépések a következők:

1. Több millió üzembe helyezett mérőszámok, valós idejű folyamatosan létrehozó energiafogyasztási adatokkal.
2. Folyamatban van, ezeket az adatokat gyűjt, és feltölti a felhőalapú adattárban (*például*, az Azure Blob).
3. Mielőtt feldolgozhatóvá, a nyers adatokat a rendszer összesíti állomás vagy regionális szintű határozzák meg az üzleti.
4. A funkció feldolgozási (lásd: **adat-előkészítési és a funkció feldolgozási**) majd kerül sor, és állít elő a szükséges adatmodell képzési vagy pontozási – a szolgáltatás adatokat tárolja egy adatbázisban (*például*, SQL Azure).
5. Az újbóli képzésekre szolgáltatást hív újbóli betanítása az előrejelzési modell –, hogy a modell frissített verzióját, hogy a pontozási webszolgáltatás által használható megőrzi.
6. A pontozási webszolgáltatás hív meg egy ütemezést, amely megfelel a szükséges előrejelzési gyakoriságát.
7. Az adatokat, amely a teljes felhasználás ügyfél által hozzáférhető adatbázisban tárolódik.
8. A használatalapú ügyfél kérdezi le az előrejelzéseket, vissza a rácsba vonatkozik, és használ fel, a szükséges használati eset megfelelően.

Fontos megjegyezni, hogy a teljes ciklus teljesen automatizált, és a egy ütemezés szerint futtatja. Ezen adatok ciklus teljes vezénylési eszközök segítségével teheti meg [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Teljes körű üzembe helyezési architektúrája
Az energia igény szerint előrejelzési megoldás a Cortana Intelligence gyakorlatilag üzembe kell, hogy a szükséges összetevők létrehozott és megfelelően konfigurálva.

Az alábbi ábra egy tipikus Cortana Intelligence-alapú architektúrát, amely megvalósítja és hangolja össze az adatokat a folyamat ciklus, amely a fent leírtak szerint:

![Teljes körű üzembe helyezési architektúrája](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Egyes összetevők és a teljes architektúrára kapcsolatos további részletekért tekintse meg az energia Megoldássablon.

