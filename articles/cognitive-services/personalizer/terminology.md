---
title: Terminológia – személyre szabás
description: A személyre szabás szakkifejezéseket használ a megerősítő tanulásban. Ezeket a feltételeket a Azure Portal és az API-k használják.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77624274"
---
# <a name="terminology"></a>Terminológia

A személyre szabás szakkifejezéseket használ a megerősítő tanulásban. Ezeket a feltételeket a Azure Portal és az API-k használják.

## <a name="conceptual-terminology"></a>Fogalmi terminológia

* **Tanulási hurok**: létrehozhat egy _tanulási ciklust_használó, személyre szabott erőforrást az alkalmazás minden olyan részéhez, amely kihasználhatja a személyre szabást. Ha több felhasználói felülettel rendelkezik, hozzon létre egy hurkot mindegyikhez.

* **Modell**: a személyre szabott modell rögzíti a felhasználói viselkedéssel kapcsolatos összes információt, betanítási adatok beszerzését a rangsorba és a jutalmazási hívásokba küldött argumentumok kombinációjával, valamint a tanulási szabályzat által meghatározott tanítási viselkedéssel.

## <a name="personalizer-configuration"></a>Személyre szabott konfiguráció

A személyre szabott beállítás a [Azure Portal](https://portal.azure.com).

* **Jutalmak**: állítsa be az alapértelmezett értékeket a jutalmazási várakozási idő, az alapértelmezett jutalom és a jutalmazási összesítési házirend számára.

* **Feltárás**: a feltáráshoz használandó rangsorolt hívások százalékos arányának konfigurálása

* **Modell frissítési gyakorisága**: milyen gyakran történik a modell újratanítása.

* **Adatmegőrzés**: ennyi nap szükséges az adatok tárolására. Ez hatással lehet az offline értékelésekre, amelyek segítségével javíthatja a tanulási ciklust.

## <a name="use-rank-and-reward-apis"></a>A Rank és a jutalmazási API-k használata

* **Rank**: a funkciókkal és a környezeti funkciókkal kapcsolatos műveletekben a legfelső szintű művelet (tartalmi elem) visszaküldéséhez használja a felderítés vagy a kihasználat lehetőséget.

    * **Műveletek**: a műveletek közül választhat a tartalmi elemek, például termékek vagy promóciók. A személyre szabási művelet a rangsor API használatával jeleníti meg a felhasználók számára a legfontosabb műveletet (a jutalom műveleti AZONOSÍTÓját).

    * **Kontextus**: pontosabb rangsor biztosításához adja meg a környezetével kapcsolatos információkat, például:
        * A felhasználó.
        * Az eszköz, amelyen be vannak kapcsolva.
        * Az aktuális idő.
        * Az aktuális helyzettel kapcsolatos egyéb információk.
        * A felhasználóval vagy a környezettel kapcsolatos korábbi információk.

        Előfordulhat, hogy az adott alkalmazás eltérő környezeti információval rendelkezik.

    * **[Szolgáltatások](concepts-features.md)**: egy tartalmi elemre vagy egy felhasználói környezetre vonatkozó információk egysége. Ügyeljen arra, hogy csak az összesített szolgáltatásokat használja. Ne használjon adott időpontokat, felhasználói azonosítókat vagy más, nem aggregált adatokat szolgáltatásként.

        * A _műveleti funkció_ a tartalommal kapcsolatos metaadatokat tartalmaz.
        * A _környezeti funkciók_ a tartalom megjelenítését szolgáló kontextusra vonatkozó metaadatok.

* **Feltárás**: a személyre szabott szolgáltatás azt vizsgálja, hogy mikor, a legjobb művelet helyett egy másik műveletet választ a felhasználó számára. A személyre szabott szolgáltatás elkerüli a sodródás, a stagnálás és a folyamatos felhasználói viselkedésre való alkalmazkodást.

* Kihasználat **: a**személyre szabott szolgáltatás a jelenlegi modellt használja a legalkalmasabb művelet eldöntésére a múltbeli információk alapján.

* **Kísérlet időtartama**: azt az időtartamot, ameddig a személyre szabott szolgáltatás megvárja a jutalmat, ettől kezdve az adott eseményhez tartozó rangsor meghívásának pillanatától számítva.

* **Inaktív események**: inaktív esemény az a hely, ahol rangot hívott, de nem biztos benne, hogy a felhasználó az ügyfélalkalmazás döntései miatt soha nem fogja látni az eredményt. Az inaktív események lehetővé teszik a személyre szabási eredmények létrehozását és tárolását, majd később eldönteni, hogy a gépi tanulási modell hatására a későbbiekben elveti őket.


* **Jutalom**: azt méri, hogy a felhasználó hogyan válaszolt a Rank API által visszaadott jutalom műveleti azonosítóra 0 és 1 közötti pontszámként. A 0 – 1 értéket az üzleti logikája állítja be, attól függően, hogy a választás miként segítette a személyre szabás üzleti céljainak megvalósítását. A tanulási ciklus nem tárolja ezt a jutalmat egyéni felhasználói előzményekként.

## <a name="offline-evaluations"></a>Offline értékelések

* **Értékelés**: a hurok adatai alapján az offline kiértékelés meghatározza a hurok legjobb tanulási szabályzatát.

* **Tanulási szabályzat**: a személyre szabott modelleket minden eseményre kiterjedően olyan paraméterek határozzák meg, amelyek befolyásolják a gépi tanulási algoritmus működését. Az új tanulási hurok egy alapértelmezett **képzési szabályzattal**kezdődik, amely mérsékelt teljesítményt eredményezhet. Az [értékelések](concepts-offline-evaluation.md)futtatásakor a személyre szabott új tanulási szabályzatokat hozhat létre, amelyek kifejezetten a hurok használati eseteire vannak optimalizálva. A személyre szabás a kiértékelés során generált minden egyes hurokhoz optimalizált házirendekkel jelentősen jobban teljesít. A tanulási szabályzat a Azure Portal személyre szabott erőforrásának **modell-és tanulási beállításaiban** található _tanulási beállítások_ nevű.