---
title: Custom Speech Model-Speech szolgáltatás betanítása és üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan végezheti el a Custom Speech modellek betanítását és üzembe helyezését. A beszédfelismerési modell betanítása javíthatja a Microsoft alapmodelljét vagy egyéni modelljét. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025599"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>A Custom Speech-modellek betanítása és üzembe helyezése

Ebből a cikkből megtudhatja, hogyan végezheti el a Custom Speech modellek betanítását és üzembe helyezését. A beszédfelismerési modell betanítása javíthatja a Microsoft alapmodelljének felismerési pontosságát. A modell emberi címkével ellátott átírásokkal és kapcsolódó szöveggel van betanítva. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak.

## <a name="use-training-to-resolve-accuracy-issues"></a>A pontossággal kapcsolatos problémák megoldása a képzés használatával

Ha az alapmodellel észlelt felismerési problémák merülnek fel, az emberi címkével ellátott átiratok és a kapcsolódó adatok az egyéni modellek betanításához segíthetnek a pontosság növelésében. A táblázat segítségével meghatározhatja, hogy melyik adatkészletet használja a probléma (ok) kezelésére:

| Használati eset | Adattípus |
| -------- | --------- |
| Az iparágra jellemző szókincs és nyelvtan – például az orvosi terminológia vagy az informatikai szakzsargon – jobb felismerési pontosságának javítása. | Kapcsolódó szöveg (mondat/hosszúságú kimondott szöveg) |
| Definiálja egy olyan szó vagy kifejezés fonetikus és megjelenített formáját, amely nem szabványos kiejtéssel rendelkezik, például Terméknév vagy betűszó. | Kapcsolódó szöveg (kiejtés) |
| Az elismerés pontosságának javítása a beszélő stílusok, hangsúlyozások vagy konkrét háttérzajok esetében. | Hang + emberi – címkézett átiratok |

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és kiértékelése

A modellek betanításának első lépése a betanítási adatok feltöltése. Az emberi címkével ellátott átiratok és a kapcsolódó szövegek (hosszúságú kimondott szöveg és kiejtések) előkészítéséhez használja az [előkészítés és a tesztelés](./how-to-custom-speech-test-and-train.md) részletes útmutatását. A betanítási adatai feltöltése után kövesse az alábbi utasításokat a modell képzésének megkezdéséhez:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Navigáljon a **beszéd – szöveg > Custom Speech > [projekt neve] > képzés** elemre.
3. Kattintson a **tanítási modell** elemre.
4. Ezután adja meg a betanítás **nevét** és **leírását**.
5. A **forgatókönyv és az alapterv modell** legördülő menüben válassza ki a tartományhoz legjobban illő forgatókönyvet. Ha nem biztos abban, hogy melyik forgatókönyvet szeretné kiválasztani, válassza az **általános** lehetőséget. Az alapmodell a betanítás kiindulási pontja. A legújabb modell általában a legjobb választás.
6. A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több hang + emberi címkével ellátott átírási adatkészletet, amelyeket képzésre szeretne használni.
7. A képzés befejezését követően dönthet úgy, hogy pontossági tesztelést végez az újonnan betanított modellen. Ez a lépés nem kötelező.
8. Válassza a **Létrehozás** lehetőséget az egyéni modell kiépítéséhez.

A betanítási táblázat egy új bejegyzést jelenít meg, amely megfelel az újonnan létrehozott modellnek. A tábla a következő állapotot is megjeleníti: feldolgozás, sikeres, sikertelen.

A Custom Speech modell pontosságának kiértékelésével és javításával kapcsolatban lásd: [útmutató](how-to-custom-speech-evaluate-data.md) . Ha a pontosság tesztelését választotta, fontos, hogy olyan akusztikai adatkészletet válasszon ki, amely eltér a modell teljesítményének reális értelmezéséhez.

## <a name="deploy-a-custom-model"></a>Egyéni modell üzembe helyezése

Miután feltöltötte és megvizsgálta az adatait, értékelte a pontosságot, és betanított egy egyéni modellt, üzembe helyezhet egy egyéni végpontot az alkalmazásaival, eszközeivel és termékeivel való használatra. 

Új egyéni végpont létrehozásához jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech) , és válassza a **telepítés** lehetőséget az oldal tetején található Custom Speech menüből. Ha először futtatja, láthatja, hogy nincsenek a táblázatban felsorolt végpontok. Miután létrehozott egy végpontot, ezen a lapon követheti nyomon az egyes telepített végpontokat.

Ezután válassza a **végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni modellt, amelyet hozzá szeretne rendelni ehhez a végponthoz. Ezen a lapon engedélyezheti a naplózást is. A naplózás lehetővé teszi a végponti forgalom figyelését. Ha le van tiltva, a rendszer nem tárolja a forgalmat.

![Modell üzembe helyezése](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Ne felejtse el elfogadni a használati feltételeket és a díjszabási adatokat.

Ezután válassza a **Létrehozás** lehetőséget. Ez a művelet visszaadja az **üzembe helyezési** lapot. A tábla mostantól egy olyan bejegyzést tartalmaz, amely megfelel az egyéni végpontnak. A végpont állapota megjeleníti a jelenlegi állapotát. Akár 30 percet is igénybe vehet, ha új végpontot hoz létre az egyéni modellek használatával. Ha a központi telepítés állapota **Befejezettre** változik, a végpont készen áll a használatra.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. Kattintson a hivatkozásra a végpontra jellemző információk megjelenítéséhez, például a végponti kulcs, a végpont URL-címe és a mintakód számára.

## <a name="view-logging-data"></a>Naplózási adatgyűjtés megtekintése

A naplózási adatok letölthetők a **Endpoint > részletei** területen.
> [!NOTE]
>A naplózási adatai 30 napig elérhetők a Microsoft tulajdonában lévő tárolóban, és ezt követően el lesznek távolítva. Abban az esetben, ha egy ügyfél tulajdonában lévő Storage-fiók kapcsolódik a kognitív szolgáltatások előfizetéséhez, a rendszer nem törli automatikusan a naplózási adataikat.

## <a name="next-steps"></a>További lépések

* Ismerje meg [, hogyan használhatja az egyéni modellt](how-to-specify-source-language.md).

## <a name="additional-resources"></a>További források

- [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
