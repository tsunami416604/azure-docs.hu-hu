---
title: Mi az, hogy egy szótárban? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Egy szótárban az igazított dokumentum, amely felsorolja a kifejezések vagy mondatokat (és a kapcsolódó fordítások), amelyeket szeretne lefordítani, ugyanúgy, mint a Microsoft Translator mindig. A szótárak szószedeteket vagy kifejezés bázisok néha is nevezik.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 2b7e709a06797323cc99bfd636ee26e9bfb8026d
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731854"
---
# <a name="what-is-a-dictionary"></a>Mi az, hogy egy szótárban?

Egy szótárban igazított párjának dokumentumok, amely felsorolja a kifejezések vagy mondatok és a kapcsolódó fordítások. Használja egy szótárban a tanítási, ha azt szeretné, a Microsoft Translator mindig lefordítani a forrás kifejezés vagy mondat, példányai a fordítás a megadott a szótár segítségével. A szótárak szószedeteket vagy kifejezés bázisok is nevezik. A szótár, egy találgatásos "másolása és cserélje le a" minden használati lista is felfoghatók.

Szótárak csak működik, amely egy teljes körűen támogatott a Microsoft Neurális gépi fordítás (NMT) rendszer mögöttük nyelvi címpárral projektekhez. [A nyelvek teljes listáját megtekintheti](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>A kifejezés szótár
Amikor egy kifejezést szótár szerepel a modell tanítása, szót vagy kifejezést, megjelenik a megadott módon le. A többi a mondat a szokásos módon van lefordítva. Egy kifejezés szótár segítségével adja meg a kifejezések, amelyek azáltal, hogy a forrás és cél fájlban a szótár azonos jelez a nem konvertált kifejezés nem fordítható.

## <a name="sentence-dictionary"></a>Mondat szótár
A mondat szótár egy pontos cél forrás mondatok fordítása megadását teszi lehetővé. Egy mondatban szótár egyezéssel fordulhat elő a teljes elküldött mondat meg kell egyeznie a forrás dictionary-bejegyzés.  Ha csak a mondat egy része megegyezik-e a bejegyzés nem egyeznek.  Találat esetén elvégez, ha a cél-bejegyzés a mondat szótár lesz visszaadva.

## <a name="dictionary-only-trainings"></a>Csak szótár betanítások
Csak a szótár-adatokat használó modell betaníthatja. Ehhez válassza ki a csak a szótár dokumentumok (vagy több szótár dokumentum), amelyeket meg szeretne közé tartozik, és koppintson a modell létrehozása. Mivel ez egy csak szótár képzés, nincs nincs képzési mondatokat szükséges minimális számát. A modell általában befejezi a betanítási sokkal gyorsabb, mint egy normál képzési.  Az eredményül kapott modellek fogja használni a Microsoft baseline modellek fordítási hozzáadta a szótárak igény szerinti hozzáadásával.  Nem kap egy Tesztjelentés.

>[!Note]
>Egyéni a fordítót nem Mondatkezdő igazítása szótár fájlokat, ezért fontos, hogy nincsenek-e forrás- és kifejezések azonos számú / a szótár sentences dokumentumokat, és hogy azok pontosan elhelyezve.

## <a name="recommendations"></a>Javaslatok

- Szótárak nem lesznek a betanított modell a betanítási adatok helyett.  A szótárak lényegében keresés és csere szavakat vagy mondatokat.  Így a rendszer az oktatóanyag teljes mondatokban tanuljon alapvetően jobb megoldás, mint egy szótár segítségével.
- A kifejezés szótár takarékosan. Egy kifejezés mondaton belül váltja fel, ha az adott mondaton belül környezet elveszett vagy fordíthatók le mindkét irányba a mondat részeinek korlátozott. Az eredmény, hogy közben a kifejezést vagy word, az adott mondaton belül lefordítja a kifejezés szótár megfelelően, a fordítási minőség a mondat gyakran romlani fog.
- A kifejezés szótár jól főnevek például termékneveket ("Microsoft SQL Server"), a megfelelő neveket ("város Hamburg") vagy a szolgáltatások a termék ("kimutatás") működik. Nem működik egyaránt jól műveletek vagy melléknevek, mert ezek vannak általában magas ragozott a forrás vagy cél nyelven. A kifejezés dictionary bejegyzései a főnevek csak elkerülése érdekében.
- Egy szótárban használatakor a kis-és nagybetűk, és a fordítások írásjelek jelenik meg a kis-és nagybetűk, és a célként megadott fájlban megadott absztrakt. Írásjelek és a kis-és nagybetűk figyelmen kívül hagyja azonosíthatja a bemeneti mondatot, és a szótár fájlban a forrás mondatok megegyezzen tett kísérlet során. Például tegyük fel, hogy betanított egy angol, spanyol rendszerre, amely egy szótárban használt, a megadott "város Hamburg" a forrásfájl és a cél fájlban "Ciudad Németország hamburg". Amely tartalmazza a "város Hamburg" kifejezést a mondatok fordítása kérésre majd "város Hamburg" megfelel a szótár-fájlt a következő bejegyzést: "Hamburg város", és szeretné leképezése "Ciudad Németország hamburg" saját végső fordítási problémái vannak.
- Egy szó egy szótár fájl egynél többször jelenik meg, ha a rendszer mindig az utolsó bejegyzés megadott fogja használni. A szótár nem tartalmazhat több fordítások ugyanazon szó.

## <a name="next-steps"></a>További lépések

- További információ [irányelvek a dokumentum formátumok](document-formats-naming-convention.md).
