---
title: IntelliSense az Azure Stream Analytics eszközei a Visual Studio-kódhoz
description: Ez a cikk az IntelliSense-funkciók használatát ismerteti az Azure Stream Analytics-eszközök vizuális stúdiókódhoz.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394396"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense az Azure Stream Analytics eszközei a Visual Studio-kódhoz

Az IntelliSense a [Stream Analytics lekérdezési nyelvéhez](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) érhető el az [Azure Stream Analytics-eszközök vs code eszközében.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) Az IntelliSense egy kódkiegészítési segédeszköz, amely számos funkciót tartalmaz: Listatagok, Paraméteradatok, Gyors információ és Teljes Word. Az IntelliSense funkcióit néha más nevek is nevezik, például "kódkiegészítés", "tartalomsegéd" és "kódcélzás".

![IntelliSense bemutató](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Az IntelliSense szolgáltatásai

A VS Code Stream Analytics eszközeinek IntelliSense-funkcióit nyelvi szolgáltatás működteti. A nyelvi szolgáltatás elemzi a forráskódot, és nyelvi szemantika alapján intelligens kódkiegészítéseket biztosít. Ha egy nyelvi szolgáltatás ismeri a lehetséges kiegészítéseket, az IntelliSense javaslatok gépelés közben jelennek meg. Ha folytatja a gépelést, a program a tagok listáját , például a változókat és a metódusokat szűri, hogy csak a beírt karaktereket tartalmazó tagokat tartalmazza. Amikor megnyomja `Tab` `Enter` a vagy a billentyűket, az IntelliSense beszúrja a kijelölt tagot.

Az IntelliSense bármely szerkesztőablakban elindítható egy eseményindító karakter, `.`például a dot karakter beírásával.

![intellisense autocompletion](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> A javaslatok widget támogatja CamelCase szűrés. A javaslatok korlátozásához írja be a nagybetűs betűket a metódus nevében. Például a "cra" gyorsan megjelenik a "createApplication" .For example, "cra" will quickly bring up "createApplication".

### <a name="types-of-completions"></a>A kiegészítések típusai

A VS Code IntelliSense Stream Analytics eszközei különböző típusú kiegészítéseket kínálnak, beleértve a nyelvi kiszolgálójavaslatokat, a kódrészleteket és az egyszerű szóalapú szöveges kiegészítéseket.

|       |         |       |
| ----- | ------- | ----- |
| Kulcsszavak | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Adatkészlet neve| `input`, `output`, `intermediate result set`|
| Adatkészlet oszlopának neve|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Név kiegészítése

A kulcsszó automatikus kiegészítése mellett a VS Code Stream Analytics-eszközei beolvassa a feladatbemeneti és kimeneti nevek listáját, valamint az adatforrások oszlopainak nevét, amikor konfigurálva vannak. A bővítmény megjegyzi ezeket az információkat, hogy olyan névkiegészítési lehetőségeket biztosítson, amelyek hasznosak a néhány billentyűleütéssel rendelkező utasítások beviteléhez:

Kódolás közben nem kell elhagynia a szerkesztőt, hogy keresést hajtson végre a feladatbemeneti neveken, a kimeneti neveken és az oszlopneveken. Megtarthatja a környezetet, megtalálhatja a szükséges információkat, elemeket szúrhat be közvetlenül a kódjába, és az IntelliSense befejezheti a gépelést.

Ne feledje, hogy a névkiegészítés használatához be kell állítania a helyi vagy az élő bemenetet, és mentenie kell a konfigurációs fájlt.

![név kiegészítése](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Paraméter információ

Az IntelliSense **paraméterinformáció** beállítás megnyit egy paraméterlistát, amely a függvények által igényelt paraméterek számáról, nevéről és típusáról nyújt információt. A félkövérrel szedett paraméter a következő paramétert jelzi, amely a függvény beírásakor szükséges.

A beágyazott függvények paraméterlistája is megjelenik. Ha egy függvényt paraméterként ír be egy másik függvényhez, a paraméterlista megjeleníti a belső függvény paramétereit. Ezután, amikor a belső függvényparaméter-lista befejeződött, a paraméterlista visszaáll a külső függvényparaméterek megjelenítésére.

![paraméter információ](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Gyors információ

A nyelvi szolgáltatás által biztosított módon a kódban minden egyes azonosítóhoz gyors **információ** látható. Az azonosítók közé tartoznak például a bemenet, a kimenet, a köztes eredményhalmaz vagy a függvény. Amikor az egérmutatót egy azonosító fölé viszi, annak deklarációja egy előugró ablakban jelenik meg. A bemenetek tulajdonságai és adatsémák, ha konfigurálva vannak, és köztes adatkészlet jelennek meg.

![gyors információ](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Az IntelliSense hibáinak elhárítása

Ezt a problémát az okozza, hogy hiányzik az adatokat kibocsátó bemeneti konfiguráció. Ellenőrizheti, hogy a [helyi bemenet](visual-studio-code-local-run.md#define-a-local-input) vagy az [élő bemenet](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) megfelelően van-e konfigurálva.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzon létre egy Azure Stream Analytics-feladatot a Visual Studio-kódban](quick-create-vs-code.md)
* [A Test Stream Analytics helyi lekérdezéseket lekérdezése imént a Visual Studio-kód használatával](visual-studio-code-local-run.md)
* [A Test Stream Analytics helyi legfelhasználhatósági lekérdezéseket használ a Visual Studio-kód használatával](visual-studio-code-local-run-live-input.md)
