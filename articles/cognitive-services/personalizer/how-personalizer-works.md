---
title: A megszemélyesítő működése – személyre szabás
titleSuffix: Azure Cognitive Services
description: A személyre szabott gépi tanulás segítségével megismerheti, hogy milyen műveleteket kell használni a kontextusban. Minden tanulási hurokhoz olyan modell tartozik, amely kizárólag az Ön által a Range és a jutalmazási hívások útján eljuttatott adatgyűjtésre van kitanítva. Minden tanulási hurok teljesen független egymástól.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.openlocfilehash: 7c163dacae24749dbe309bca33bac016a3be7aa5
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002895"
---
# <a name="how-personalizer-works"></a>A Personalizer működése

A személyre szabott gépi tanulás segítségével megismerheti, hogy milyen műveleteket kell használni a kontextusban. Minden tanulási hurokhoz olyan modell tartozik, amely kizárólag az Ön által a **Range** és a jutalmazási hívások útján eljuttatott adatgyűjtésre van kitanítva. Minden tanulási hurok teljesen független egymástól. Hozzon létre egy tanulási ciklust a személyre szabni kívánt alkalmazás egyes részeihez vagy működéséhez.

Minden hurok esetében **hívja meg a Rank API-t** az aktuális környezet alapján, a következővel:

* A lehetséges műveletek listája: azok a tartalmi elemek, amelyekről a legfelső szintű műveletet ki kell választani.
* A [környezeti funkciók](concepts-features.md)listája: a kontextusban releváns adatokat, például a felhasználót, a tartalmat és a környezetet.

A **Rank** API úgy dönt, hogy az alábbiakat használja:

* _Kiaknázás_: Az aktuális modell, amely a legalkalmasabb műveletet határozza meg a múltbeli információk alapján.
* _Ismerkedés_: Válasszon másik műveletet a felső művelet helyett.

A **jutalmazási** API:

* Összegyűjti az adatokat a modell betanításához az egyes rangsorolási hívások funkcióinak és jutalmazási pontjainak rögzítésével.
* Ezeket az adattípusokat használja a modell frissítéséhez a _tanulási szabályzatban_megadott beállítások alapján.

## <a name="architecture"></a>Architektúra

Az alábbi képen a rang és a jutalmazási hívások meghívásának építészeti folyamata látható:

![helyettesítő szöveg](./media/how-personalizer-works/personalization-how-it-works.png "személyre szabás működése")

1. A megszemélyesítő egy belső AI-modellt használ a művelet rangsorának meghatározásához.
1. A szolgáltatás eldönti, hogy kihasználja-e az aktuális modellt, vagy új választási lehetőségeket keres a modellhez.  
1. A rangsor eredményét a rendszer elküldi a EventHub.
1. Ha a személy megkapja a jutalmat, a jutalmat a EventHub kapja meg. 
1. A rangsor és a jutalom összefügg.
1. Az AI-modell a korrelációs eredmények alapján frissül.
1. A következtetési motor az új modellel frissül. 

## <a name="research-behind-personalizer"></a>Személyre szabott kutatás

A személyre szabott tudomány és kutatás területén a megerősítő [tanulás](concepts-reinforcement-learning.md) , többek között a dokumentumok, a kutatási tevékenységek, valamint a kutatás folyamatban van a Microsoft Research szolgáltatásban.

## <a name="terminology"></a>Terminológia

* **Tanulási hurok**: Létrehozhat egy tanulási hurkot az alkalmazás minden olyan részéhez, amely kihasználhatja a személyre szabást. Ha több felhasználói felülettel rendelkezik, hozzon létre egy hurkot mindegyikhez. 

* **Műveletek**: A műveletek olyan tartalmi elemek, például termékek vagy promóciók, amelyek közül választhat. A személyre szabási művelettel kiválaszthatja, hogy a legjobb művelet jelenjenmeg a felhasználók számára, a RANGSOROLÁSi API-n keresztül. Minden művelethez a Rank kéréssel elküldött funkciók tartozhatnak.

* **Környezet**: A pontosabb rangsor megadásához adja meg a környezettel kapcsolatos információkat, például:
    * A felhasználó.
    * Az eszköz, amelyen be vannak kapcsolva. 
    * Az aktuális idő.
    * Az aktuális helyzettel kapcsolatos egyéb információk.
    * A felhasználóval vagy a környezettel kapcsolatos korábbi információk.

    Előfordulhat, hogy az adott alkalmazás eltérő környezeti információval rendelkezik. 

* **[Funkciók](concepts-features.md)** : Egy tartalmi elemmel vagy felhasználói környezettel kapcsolatos információk egysége.

* **Jutalom**: Egy mérőszám arra vonatkozóan, hogy a felhasználó hogyan válaszolt a Range API által visszaadott műveletre 0 és 1 közötti pontszámként. A 0 – 1 értéket az üzleti logikája állítja be, attól függően, hogy a választás miként segítette a személyre szabás üzleti céljainak megvalósítását. 

* **Feltárás**: A személyre szabott szolgáltatás vizsgálja, hogy mikor, a legjobb művelet helyett egy másik műveletet választ a felhasználó számára. A személyre szabott szolgáltatás elkerüli a sodródás, a stagnálás és a folyamatos felhasználói viselkedésre való alkalmazkodást. 

* **Kísérlet időtartama**: Az az időtartam, ameddig a személyre szabott szolgáltatás megvárja a jutalmat, és ettől kezdve a rangsorban történt hívás az adott eseménynél.

* **Inaktív események**: Az inaktív esemény az a hely, ahol rangot hívott, de nem biztos benne, hogy a felhasználó az ügyfélalkalmazás döntései miatt soha nem fogja látni az eredményt. Az inaktív események lehetővé teszik a személyre szabási eredmények létrehozását és tárolását, majd később eldönteni, hogy a gépi tanulási modell hatására a későbbiekben elveti őket.

* **Modell**: A személyre szabott modell rögzíti a felhasználói viselkedéssel kapcsolatos összes információt, betanítási adatok beszerzését a rangsorba és a jutalmazási hívásokba küldött argumentumok kombinációjával, valamint a tanulási szabályzat által meghatározott tanítási viselkedéssel. 

* **Képzési szabályzat**: Egy modell minden eseményen való személyre szabása a gépi tanulási algoritmusok működését befolyásoló meta-paraméterek alapján történik. Az új személyre szabott hurkok alapértelmezett tanulási szabályzattal kezdődnek, ami mérsékelt teljesítményt eredményezhet. Az [értékelések](concepts-offline-evaluation.md)futtatásakor a személyre szabott új képzési szabályzatokat hozhat létre, amelyek kifejezetten a hurok használati eseteire optimalizáltak. A személyre szabás a kiértékelés során generált minden egyes hurokhoz optimalizált házirendekkel jelentősen jobban teljesít.

## <a name="example-use-cases-for-personalizer"></a>Példa a személyre szabott használati esetek használatára

* A szándék tisztázásának &e: segítheti a felhasználókat abban, hogy jobb felhasználói élményt nyújtsanak, ha az egyes felhasználók számára személyre szabott lehetőséget biztosítanak.
* A menükre vonatkozó alapértelmezett javaslatok & lehetőségek: a robot azt sugallja, hogy a legvalószínűbb elem egy személyre szabott módon, első lépésként, nem pedig egy személytelen menüt vagy alternatívák listáját jeleníti meg.
* Bot-tulajdonságok & hang: olyan robotok esetében, amelyek a tónustól, részletességtől és írástól eltérőek lehetnek, érdemes lehet ezeket a tulajdonságokat személyre szabott módon megváltoztatni.
* Értesítési & riasztás tartalma: döntse el, hogy a riasztások milyen szöveggel legyenek felhasználva a felhasználók számára.
* Értesítési & riasztás időzítése: személyre szabott tanulás arról, hogy mikor küldjön értesítéseket a felhasználóknak, hogy további műveleteket folytassanak.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Személyre szabott webes alkalmazás használata

Hurok hozzáadása egy webalkalmazáshoz:

* Határozza meg, hogy melyik felhasználói élményre legyen személyre szabva, milyen műveletekre és funkciókra van szükség, milyen környezeti funkciókat kell használni, és milyen jutalmat kell beállítania.
* Adjon hozzá egy hivatkozást a személyre szabási SDK-hoz az alkalmazásban.
* Ha készen áll a személyre szabásra, hívja meg a Rank API-t.
* Tárolja a Napszállta. Később jutalmat küld a jutalmazási API-nak.
1. Ha biztos benne, hogy a felhasználó megtekintette a személyre szabott lapot, hívja meg az aktiválást az eseményre.
1. Várja meg a rangsorolt tartalom felhasználó általi kijelölését. 
1. A jutalmazási API meghívásával megtudhatja, hogy a rangsor API kimenete milyen jól működött.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Személyre szabott csevegési robot használata

Ebből a példából megtudhatja, hogyan használhatja a személyre szabást úgy, hogy egy alapértelmezett javaslatot tegyen elérhetővé, ahelyett, hogy minden alkalommal le kellene küldenie a felhasználót egy sor menükből vagy választási lehetőségből.

* A minta [kódjának](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) beolvasása.
* Állítsa be a robot-megoldást. Győződjön meg arról, hogy a LUIS-alkalmazást közzéteszi. 
* Rangsor és jutalmazási API-hívások kezelése a robothoz.
    * Kód hozzáadása a LUIS szándék-feldolgozás kezeléséhez. Ha a **none** értéket a rendszer a legfelső szintű szándék vagy a legnépszerűbb cél pontszáma alatt éri el, az üzleti logika küszöbértéke alatt küldje el a szándékok listáját a személyre a szándékok rangsorolásához.
    * Megadhatja a leképezések listáját a felhasználók számára választható hivatkozásként, amely az első szándék, hogy a rangsor API-válaszból származó legfelső rangú szándék.
    * Rögzítse a felhasználó kijelölését, és küldje el ezt a jutalmazási API-hívásban. 

### <a name="recommended-bot-patterns"></a>Ajánlott bot-minták

* Személyre szabhatja az API-hívásokat minden alkalommal, amikor szükség van rá, az egyes felhasználók gyorsítótárazási eredményeivel szemben. A eszköznév szándéka idővel változhat egy személy számára, és lehetővé teszi, hogy a rangsor API felgyorsítsa az általános tanulást.
* Válassza ki a sok felhasználóval közös interakciót, hogy elegendő adattal rendelkezzen a személyre szabáshoz. Előfordulhat például, hogy a bevezető kérdések jobban illeszkednek a beszélgetési gráfban található kisebb pontosításokhoz, amelyek csak néhány felhasználó számára lehetségesek.
* A Rank API-hívások használatával engedélyezheti az "első javaslat megfelelő" beszélgetéseket, ahol a felhasználó a következőt kérdezi le: "szeretne X?" vagy "érted X?" és a felhasználó csak megerősítheti; nem kell megadnia a beállításokat arra a felhasználóra vonatkozóan, ahol a menüt ki kell választani. Például: "szeretnék megrendelni egy kávét?" bot: "szeretne egy dupla Espresso?". Így a jutalmazási jel is erős, mivel közvetlenül az egyik javaslatra vonatkozik.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>A személyre szabott megoldás használata javaslattal

Az ajánlási motor segítségével több elemre szűrheti a nagyméretű katalógusokat, amelyeket aztán 30 lehetséges műveletként lehet elküldeni a Rank API-nak.

A személyre szabott ajánlási motorokat használhatja:

* A javaslati [megoldás](https://github.com/Microsoft/Recommenders/)beállítása. 
* Egy oldal megjelenítésekor hívja meg a javaslat modelljét a javaslatok rövid listájának beszerzéséhez.
* Hívja meg a személyre szabást, hogy rangsorolja az ajánlási megoldás kimenetét.
* Küldjön visszajelzést a felhasználói műveletről a jutalmazási API-hívással.


## <a name="pitfalls-to-avoid"></a>Kikerülő buktatók

* Ne használja a személyre szabott viselkedést, ha a személyre szabott viselkedés nem minden felhasználó számára észlelhető, hanem egy adott felhasználóra vonatkozó megjegyzés, vagy az alternatívák felhasználó-specifikus listájából származik. Ha például a személyre szabott lehetőséget használja arra, hogy egy 20 lehetséges menüelemből álló lista első pizza-sorrendjét javasolja, akkor a felhasználók kapcsolattartási listájáról hívható hívás, ha a gyermekgondozási segítségre van szükség (például "nagymama"), nem pedig az egész személyre szabható a felhasználói bázis.


## <a name="adding-content-safeguards-to-your-application"></a>Tartalmi óvintézkedések hozzáadása az alkalmazáshoz

Ha az alkalmazás lehetővé teszi a nagy eltérések használatát a felhasználók számára megjelenített tartalomban, és előfordulhat, hogy bizonyos tartalmak nem biztonságosak vagy nem megfelelőek egyes felhasználók számára, előre meg kell terveznie, hogy a megfelelő védelmet biztosítson a felhasználók számára, hogy ne láthassák elfogadhatatlannak tartalom. A védelem megvalósításának legjobb mintája a következő:
    * A rangsorolni kívánt műveletek listájának beszerzése.
    * Szűrje ki azokat, amelyek nem életképesek a közönség számára.
    * Csak a következő életképes műveletek rangsorolása.
    * Jelenítse meg a legjobban rangsorolt műveletet a felhasználó számára.

Egyes architektúrákban a fenti sorozatot nehéz lehet megvalósítani. Ebben az esetben létezik egy alternatív módszer a védelmi feladatok rangsorolás utáni megvalósítására, de ki kell alakítani egy olyan műveletet, amely a védelemen kívül esik, és nem a személyre szabott modell betanítására szolgál.

* Szerezze be a rangsorban szereplő műveletek listáját, a tanulás inaktiválva.
* Rangsoroló műveletek.
* Ellenőrizze, hogy a legfelső szintű művelet életképes-e.
    * Ha a legfelső szintű művelet életképes, aktiválja a tanulást ebben a rangsorban, majd jelenítse meg a felhasználót.
    * Ha a legfelső szintű művelet nem életképes, ne aktiválja a betanítást ehhez a rangsoroláshoz, és döntse el saját logikáját vagy alternatív megközelítéseit a felhasználónak való megjelenítéshez. Még ha a második legjobb rangsorolási lehetőséget is használja, ne aktiválja a tanulást ehhez a rangsoroláshoz.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>A személyre szabás megfelelő hatékonyságának ellenőrzése

Az [Offline értékelések](how-to-offline-evaluation.md) végrehajtásával rendszeres időközönként nyomon követheti a személyre szabott személy hatékonyságát

## <a name="next-steps"></a>További lépések

Megtudhatja [, hol](where-can-you-use-personalizer.md)használhatja a személyre szabást.
[Offline értékelések](how-to-offline-evaluation.md) végrehajtása
