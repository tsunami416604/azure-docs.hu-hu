---
title: Hol és hogyan kell használni - Personalizer
description: A Personalizer minden olyan helyzetben alkalmazható, ahol az alkalmazás kiválaszthatja a megfelelő elemet, műveletet vagy terméket a megjelenítéshez - annak érdekében, hogy jobb élményt nyújtson, jobb üzleti eredményeket érjen el, vagy javítsa a termelékenységet.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 63e66315898242beb5da59927e8d506e6f2cff78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219323"
---
# <a name="where-and-how-to-use-personalizer"></a>Hol és hogyan kell használni a Personalizer-t?

Használja a Personalizer-t minden olyan helyzetben, amikor az alkalmazásnak ki kell választania a megfelelő műveletet (tartalmat) a megjelenítéshez - az élmény javítása, jobb üzleti eredmények elérése vagy a termelékenység javítása érdekében.

A Personalizer gépi tanulással választja ki, hogy melyik műveletet (tartalmat) jelenítse meg a felhasználónak. A kijelölés a szolgáltatásnak küldött adatok mennyiségétől, minőségétől és elosztásától függően drasztikusan változhat.

## <a name="example-use-cases-for-personalizer"></a>Példa használati esetek a Personalizer-hoz

* **Szándék pontosítás & félreérthető:** segít a felhasználóknak jobb élményt, ha a szándék nem egyértelmű azáltal, hogy egy lehetőséget, amely személyre szabott.
* **A menük alapértelmezett javaslatai** & lehetőségek: a bot első lépésként személyre szabott módon javasolja a legvalószínűbb elemet, ahelyett, hogy személytelen menüt vagy alternatívák listáját mutatná be.
* **Bot tulajdonságok & hang:** a botok, amelyek változhatnak hang, bőbeszédűség, és az írás stílusa, úgy változó ezeket a tulajdonságokat.
* **Értesítés & riasztási tartalom:** döntse el, hogy milyen szöveget használjon a riasztásokhoz, hogy a felhasználókat még jobban bevonja.
* **Értesítés & riasztás időzítése:** személyre szabott tanulás, hogy mikor kell értesítéseket küldeni a felhasználóknak, hogy vegyenek részt őket.


## <a name="expectations-required-to-use-personalizer"></a>A Personalizer használatához szükséges elvárások

A Personalizer-t olyan helyzetekben alkalmazhatja, amikor megfelel vagy megvalósíthatja az alábbi irányelveket.

|Irányelv|Magyarázat|
|--|--|
|Üzleti cél|Van egy üzleti vagy használhatósági cél az alkalmazás.|
|Tartalom|Van egy hely az alkalmazásban, ahol a környezetfüggő döntést, hogy mit jelenít meg a felhasználók nak javítja ezt a célt.|
|Tartalom mennyiség|Hívásonként kevesebb mint 50 műveletet kell rangsorolnia.|
|Összesített adatok|A legjobb választás lehet és kell tanulni a kollektív felhasználói viselkedés és a teljes jutalom pontszámot.|
|Etikus használat|A gépi tanulás személyre szabáshoz való használata a [választott felelősségteljes használati irányelveket](ethics-responsible-use.md) és választási lehetőségeket követi.
|A legjobb egyopciós megoldás|A kontextuális döntés úgy fejezhető ki, mint a legjobb megoldás (akció) rangsorolása korlátozott választási lehetőségek közül.|
|Pontozott eredmény|Milyen jól működött a rangsorolt választás az alkalmazáshoz, meghatározható a felhasználói viselkedés bizonyos aspektusainak mérésével, és _[a jutalompontszámban](concept-rewards.md)_ kifejezve.|
|Releváns időzítés|A jutalom pontszám nem hoz túl sok zavaró vagy külső tényezők. A kísérlet időtartama elég alacsony ahhoz, hogy a jutalom pontszám kiszámítható, amíg még releváns.|
|Megfelelő környezeti jellemzők|A rangsor környezetét legalább 5 [olyan funkció](concepts-features.md) listájaként fejezheti ki, amelyek ről úgy gondolja, hogy segítenek a helyes választásban, és amelyek nem tartalmaznak felhasználóspecifikus azonosítható információkat.|
|Megfelelő cselekvési funkciók|Van információ az egyes tartalom választás, _akció_, mint egy lista legalább 5 [funkciók,](concepts-features.md) hogy úgy gondolja, segít Personalizer, hogy a helyes választás.|
|Napi adatok|Van elég esemény, hogy felfüggeszti a tetején az optimális személyre szabás, ha a probléma sodródik az idő múlásával (mint például a preferenciák hírek vagy divat). A Personalizer alkalmazkodik a valós világ folyamatos változásához, de az eredmények nem lesznek optimálisak, ha nincs elég esemény és adat ahhoz, hogy új mintákat fedezzen fel és rendezzen. Olyan használati esetet kell választania, amely elég gyakran előfordul. Fontolja meg a napi legalább 500 alkalommal bekövetkező használati esetek keresését.|
|Korábbi adatok|Az alkalmazás képes megőrizni az adatokat elég hosszú ahhoz, hogy legalább 100 000 interakcióinak előzményeit összegyűjtse. Ez lehetővé teszi a Personalizer számára, hogy elegendő adatot gyűjtsön az offline értékelések és a házirendek optimalizálásához.|

**Ne használja a Personalizer-t,** ha a személyre szabott viselkedés nem olyan dolog, amely minden felhasználó számára felfedezhető. Például a Personalizer használatával 20 lehetséges menüelem listájából az első pizzarendelést javasolhatja, de az, hogy a felhasználók névjegyzékéből melyik kapcsolatot hívhatja meg, amikor segítségre van szüksége a gyermekgondozással kapcsolatban (például "Nagymama") nem olyan dolog, amely személyre szabható a felhasználói bázist.

## <a name="how-to-use-personalizer-in-a-web-application"></a>A Personalizer használata webes alkalmazásokban

Tanulási ciklus hozzáadása egy webalkalmazáshoz a következőket foglalja magában:

* Határozza meg, hogy mely élményt szeretné személyre szabni, milyen műveletekkel és funkciókkal rendelkezik, milyen környezetfunkciókat használ, és milyen jutalmat fog beállítani.
* Adjon hozzá hivatkozást a személyre szabási SDK-hoz az alkalmazásban.
* Hívja meg a Rank API-t, ha készen áll a személyre szabásra.
* Tárolja az eventId azonosítót. Később jutalmat küld a Jutalom API-val.
1. Hívás aktiválása az eseményhez, ha biztos benne, hogy a felhasználó látta a személyre szabott oldalt.
1. Várja meg a rangsorolt tartalom felhasználó általi kiválasztását.
1. A Jutalom API hívása annak meghatározásához, hogy a Rank API kimenete mennyire jól teljesített.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Hogyan használjuk a Personalizer-t egy csevegőrobottal?

Ebben a példában látni fogja, hogyan használhatja a Személyre szabás, hogy egy alapértelmezett javaslatot ahelyett, hogy elküldi a felhasználónak egy sor menük vagy választási lehetőségek minden alkalommal.

* Szerezd meg a minta [kódját.](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample)
* Állítsa be a robot megoldást. Győződjön meg arról, hogy közzéteszi a LUIS-alkalmazást.
* Kezelése Rank and Reward API hívások bot.
    * Adjon hozzá kódot a LUIS leképezésfeldolgozásának kezeléséhez. Ha a **Nincs** adja vissza a legfelső szándék, vagy a felső szándék pontszáma alatt az üzleti logikai küszöbértéket, küldje el a szándékok listáját a personalizer a szándékok rangsorolása.
    * A leképezési lista megjelenítése a felhasználó számára választható hivatkozásként, az első szándék pedig a Rank API-válasz legmagasabb rangú leképezése.
    * Rögzítse a felhasználó választását, és küldje el ezt a Jutalom API-hívásban.

### <a name="recommended-bot-patterns"></a>Ajánlott bot minták

* Legyen Personalizer Rank API-hívások minden alkalommal, amikor egy félreérthetőség re van szükség, szemben a gyorsítótárazás i ad minden felhasználó számára. A leszögezés eredménye idővel változhat egy személy számára, és a Rank API eltéréseinek feltárása felgyorsítja az általános tanulást.
* Olyan interakciót válasszon, amely sok felhasználónál gyakori, így elegendő adat áll a személyre szabáshoz. Például a bevezető kérdések jobban illeszkedhetnek, mint a beszélgetési grafikon mélyén lévő kisebb pontosítások, amelyekhez csak néhány felhasználó juthat el.
* A Rank API-hívások használatával engedélyezheti az "első javaslat helyes" beszélgetéseket, ahol a rendszer megkérdezi a felhasználót, hogy "Szeretne X-et?" vagy "X-re gondoltál?" és a felhasználó csak megerősíti; ahelyett, hogy lehetőséget ad a felhasználónak, ahol választaniuk kell a menüből. Például, Felhasználó: "Szeretnék rendelni egy kávét" Bot: "Szeretne egy dupla eszpresszót?". Ily módon a jutalom jel is erős, mivel közvetlenül az egyetlen javaslatot.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>A Personalizer használata ajánlási megoldással

Számos vállalat használ ajánlási motorokat, marketing- és kampányeszközöket, közönségszegmentálást és fürtözést, együttműködésen alapuló szűrést és más eszközöket a nagy katalógusból származó termékek ügyfeleknek történő ajánlására.

A [Microsoft Recommenders GitHub-tárház](https://github.com/Microsoft/Recommenders) példákat és gyakorlati tanácsokat nyújt a Jupyter-jegyzetfüzetekként biztosított ajánlási rendszerek létrehozásához. Munkapéldákat tartalmaz az adatok előkészítéséhez, a modellek készítéséhez, az ajánlási motorok értékeléséhez, hangolásához és üzembe szerűvé kialakításához számos közös megközelítéshez, beleértve az xDeepFM, SAR, ALS, RBM, DKN.

A personalizer akkor tud dolgozni egy ajánlási motorral, ha jelen van.

* Az ajánlási motorok nagy mennyiségű elemet (például 500 000-et) vesznek fel, és több száz vagy ezer beállításból ajánlanak egy részhalmazt (például a top 20-at).
* A Personalizer kis számú műveletet vesz igénybe, sok információt tartalmaz róluk, és valós időben rangsorolja őket egy adott gazdag környezetben, míg a legtöbb ajánlási motor csak néhány attribútumot használ a felhasználókról, a termékekről és azok interakcióiról.
* A Personalizer célja, hogy mindig önállóan tárja fel a felhasználói preferenciákat, ami jobb eredményeket eredményez ott, ahol a tartalom gyorsan változik, például hírek, élő események, élő közösségi tartalmak, napi frissítésekkel ellátott tartalmak vagy szezonális tartalmak.

Gyakori használata, hogy a kimenet egy javaslat motor (például a top 20 termék egy bizonyos ügyfél), és használja, hogy a bemeneti műveletek Personalizer.

## <a name="adding-content-safeguards-to-your-application"></a>Tartalomvédelmi intézkedések hozzáadása az alkalmazáshoz

Ha az alkalmazás lehetővé teszi a felhasználók számára megjelenített tartalom nagy eltéréseit, és a tartalom egy része nem biztonságos vagy nem megfelelő egyes felhasználók számára, akkor előre kell terveznie, hogy megbizonyosodjon arról, hogy a megfelelő biztosítékok vannak érvényben annak megakadályozására, hogy a felhasználók elfogadhatatlannak lássák Tartalom. A biztosítékok végrehajtásának legjobb mintája a következő:
    * Szerezze be a rangsorolandó műveletek listáját.
    * Szűrje ki azokat, amelyek nem életképesek a közönség számára.
    * Csak rangsorolja ezeket az életképes cselekedeteket.
    * A legmagasabb rangsorolt művelet megjelenítése a felhasználó számára.

Egyes architektúrákban a fenti sorrendet nehéz lehet megvalósítani. Ebben az esetben van egy alternatív megközelítés a biztosítékok nak a rangsorolás utáni végrehajtására, de rendelkezni kell egy olyan intézkedésről, amely kívül esik a biztosítékon, ne használja fel a Personalizer modell betanítására.

* Szerezd meg a rangsorolandó műveletek listáját, inaktivált tanulással.
* Rangsorolási műveletek.
* Ellenőrizze, hogy a legfelső művelet életképes-e.
    * Ha a legfelső művelet életképes, aktiválja a tanulást ehhez a ranghoz, majd mutassa meg a felhasználónak.
    * Ha a felső művelet nem életképes, ne aktiválja a tanulást ehhez a rangsorhoz, és saját logikája vagy alternatív megközelítései alapján döntse el, hogy mit jelenítsen meg a felhasználónak. Még akkor is, ha a második legjobb rangsorolt opciót használja, ne aktiválja a tanulást ehhez a rangsorhoz.


## <a name="next-steps"></a>További lépések

[Az etikai & felelős használat](ethics-responsible-use.md).