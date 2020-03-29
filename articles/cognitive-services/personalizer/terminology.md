---
title: Terminológia - Personalizer
description: A personalizer a megerősítési tanulásból származó terminológiát használ. Ezeket a kifejezéseket az Azure Portalon és az API-kban használják.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624274"
---
# <a name="terminology"></a>Terminológia

A personalizer a megerősítési tanulásból származó terminológiát használ. Ezeket a kifejezéseket az Azure Portalon és az API-kban használják.

## <a name="conceptual-terminology"></a>Koncepcionális terminológia

* **Tanulási hurok:** Hozzon létre egy personalizer erőforrást, amelyet _tanulási huroknak_neveznek, az alkalmazás minden olyan részére, amely a személyre szabás előnyeit élvezheti. Ha egynél több személyre szabási tapasztalattal rendelkezik, hozzon létre egy-egy hurkot mindegyikhez.

* **Modell**: A Personalizer modell rögzíti a felhasználói viselkedésről megismert összes adatot, beszerzi a betanítási adatokat a Rang és jutalom hívásoknak küldött argumentumok kombinációjából, valamint a tanulási szabályzat által meghatározott képzési viselkedéssel.

## <a name="personalizer-configuration"></a>Személyre szabó konfigurációja

Personalizer van konfigurálva az [Azure Portalon.](https://portal.azure.com)

* **Jutalmak:** konfigurálja az alapértelmezett értékeket a jutalom várakozási idejéhez, az alapértelmezett jutalomhoz és a jutalomösszesítési házirendhez.

* **Feltárás:** Konfigurálja a feltáráshoz használt ranghívások százalékos arányát

* **Modellfrissítési gyakoriság:** A modell újrabetanításának gyakorisága.

* **Adatmegőrzés**: Hány napnyi adatot kell tárolni. Ez hatással lehet az offline értékelésekre, amelyek a tanulási ciklus javítására szolgálnak.

## <a name="use-rank-and-reward-apis"></a>Rang- és jutalomAPI-k használata

* **Rang**: Tekintettel a funkciókkal és a környezetfunkcióval kapcsolatos műveletekre, használja a felfedezést vagy a kihasználást a legfelső művelet (tartalomelem) visszaküldéséhez.

    * **Műveletek**: A műveletek azok a tartalomelemek, mint például a termékek vagy a promóciók, amelyek közül választhat. Personalizer kiválasztja a legjobb művelet (vissza jutalom művelet azonosítója), hogy megjelenjen a felhasználók számára a Rang API-t.

    * **Kontextus**: A pontosabb rangsorolás érdekében adjon meg információkat a környezetéről, például:
        * A felhasználó.
        * Az eszköz, amit bekapcsolnak.
        * Az aktuális idő.
        * Egyéb adatok a jelenlegi helyzetről.
        * A felhasználóvagy a környezet előzményadatai.

        Az adott alkalmazás különböző környezeti adatokkal rendelkezhet.

    * **[Jellemzők](concepts-features.md)**: Egy tartalomelemre vagy egy felhasználói környezetre vonatkozó információegység. Győződjön meg arról, hogy csak összesített funkciókat használ. Ne használjon meghatározott időpontokat, felhasználói azonosítókat vagy más nem összesített adatokat szolgáltatásként.

        * A _műveletfunkció_ a tartalom metaadatai.
        * A _környezeti szolgáltatás_ metaadatok at a környezet, amelyben a tartalom jelenik meg.

* **Feltárás:** A Personalizer szolgáltatás vizsgálja, ha ahelyett, hogy a legjobb műveletet advissza, más műveletet választ a felhasználó számára. A Personalizer szolgáltatás elkerüli a sodródást, a stagnálást, és a folyamatos felhasználói viselkedéshez való alkalmazkodást a felfedezéssel.

* **Kihasználás:** A Personalizer szolgáltatás az aktuális modell segítségével dönti el a legjobb műveletet a múltbeli adatok alapján.

* **Kísérlet időtartama**: Az az időtartam, ameddig a Personalizer szolgáltatás vár a jutalomra, attól a pillanattól kezdve, hogy a Rang hívás történt az adott eseményre.

* **Inaktív események:** Az inaktív esemény az, ahol a rang, de nem biztos benne, hogy a felhasználó valaha is látni fogja az eredményt, az ügyfél alkalmazás döntései miatt. Inaktív események lehetővé teszik, hogy hozzon létre és tárolhatja a személyre szabási eredményeket, majd úgy dönt, hogy dobja őket később anélkül, hogy befolyásolná a gépi tanulási modell.


* **Jutalom:** Annak mértéke, hogy a felhasználó hogyan reagált a Rang API visszaadott jutalomművelet-azonosítójára, 0 és 1 közötti pontszámként. A 0 az 1-hez értéket az üzleti logika határozza meg, attól függően, hogy a választás hogyan segített elérni a személyre szabás üzleti céljait. A tanulási ciklus nem tárolja ezt a jutalmat egyéni felhasználói előzményekként.

## <a name="offline-evaluations"></a>Offline értékelések

* **Kiértékelés:** Az offline értékelés határozza meg a hurok legjobb tanulási szabályzatát a hurok adatai alapján.

* **Tanulási szabályzat:** Hogyan Szabatoz be a modell minden esemény határozza meg bizonyos paramétereket, amelyek befolyásolják a gépi tanulási algoritmus működését. Az új tanulási ciklus egy alapértelmezett **tanulási szabályzattal**kezdődik, amely mérsékelt teljesítményt eredményez. [Kiértékelések futtatásakor,](concepts-offline-evaluation.md)Personalizer létrehoz új tanulási szabályzatok kifejezetten a használati esetek ben a hurok. A Personalizer jelentősen jobban teljesít az egyes ciklusokra optimalizált szabályzatokkal, amelyek et az értékelés során generálnak. A tanulási szabályzat neve _tanulási beállítások_ a modell és a **tanulási beállítások** at a Personalizer erőforrás az Azure Portalon.