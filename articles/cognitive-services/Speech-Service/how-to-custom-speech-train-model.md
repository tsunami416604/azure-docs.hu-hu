---
title: Custom Speech Model-Speech szolgáltatás betanítása és üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan taníthat és helyezhet üzembe Custom Speech modelleket. A beszédfelismerési modell betanítása javíthatja a Microsoft Baseline Model vagy a for egyéni modell felismerési pontosságát.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 130cd643856b38471eac6d6869cdc1ed8b0bcd2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499152"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>A Custom Speech-modellek betanítása és üzembe helyezése

Ebből a cikkből megtudhatja, hogyan végezheti el a Custom Speech modellek betanítását és üzembe helyezését. A beszédfelismerési modell betanítása javíthatja a Microsoft Baseline modell felismerési pontosságát. Az emberi címkével ellátott átírásokat és a kapcsolódó szövegeket a modellek betanításához használhatja. Ezek az adatkészletek a korábban feltöltött hangadatokkal együtt a beszéd-szöveg modell pontosítására és betanítására szolgálnak.

## <a name="use-training-to-resolve-accuracy-problems"></a>A pontossággal kapcsolatos problémák megoldása a képzés használatával

Ha az alapmodellel észlelt felismerési problémákat tapasztal, használhat emberi címkével ellátott átiratokat és kapcsolódó adatokkal egy egyéni modell betanítását, és javíthatja a pontosságot. A táblázat segítségével meghatározhatja, hogy melyik adatkészletet használja a problémák megoldásához:

| Használati eset | Adattípus |
| -------- | --------- |
| Az iparág specifikus szókincsének és nyelvtanának, például az orvosi terminológia vagy az informatikai szakzsargonnak az ismertségének javítása | Kapcsolódó szöveg (mondat/hosszúságú kimondott szöveg) |
| A nem szabványos kiejtéssel (például Terméknév vagy betűszó) rendelkező szó vagy kifejezés fonetikus és megjelenített formátumának megadása | Kapcsolódó szöveg (kiejtés) |
| Az elismerés pontosságának javítása a beszélő stílusok, hangsúlyozások vagy konkrét háttérzajok esetén | Hang + emberi – címkézett átiratok |

## <a name="train-and-evaluate-a-model"></a>Modell betanítása és kiértékelése

A modellek betanításának első lépése a betanítási adatok feltöltése. Az emberi címkével ellátott átírások és a kapcsolódó szövegek (hosszúságú kimondott szöveg és kiejtések) előkészítéséhez lásd: [az adat előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md) részletes utasításokkal. A betanítási adatok feltöltése után kövesse az alábbi utasításokat a modell képzésének megkezdéséhez:

1. Jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech).
2. Ugrás a **beszéd – szöveg**  >  **Custom Speech**  >  **[projekt neve]**  >  **képzésre**.
3. Válassza ki a **betanítási modellt**.
4. Adja meg a betanítás **nevét** és **leírását**.
5. A **forgatókönyv és** az alapkonfiguráció-modell listából válassza ki azt a forgatókönyvet, amely a legjobban megfelel a tartománynak. Ha nem biztos abban, hogy melyik forgatókönyvet szeretné kiválasztani, válassza az **általános** lehetőséget. Az alapmodell a betanítás kiindulási pontja. A legújabb modell általában a legjobb választás.
6. A **betanítási adatok kiválasztása** lapon válasszon ki egy vagy több olyan hang + emberi címkével ellátott átírási adatkészletet, amelyet képzésre szeretne használni.
7. A betanítás befejezése után az újonnan betanított modell pontossági tesztelését végezheti el. Ez a lépés nem kötelező.
8. Válassza a **Létrehozás** lehetőséget az egyéni modell kiépítéséhez.

A **betanítási** táblázat egy új bejegyzést jelenít meg, amely megfelel az új modellnek. A tábla a következő állapotot is megjeleníti: **feldolgozás**, **sikeres** vagy **sikertelen**.

A Custom Speech modell pontosságának kiértékelésével és javításával kapcsolatban lásd: [útmutató](how-to-custom-speech-evaluate-data.md) . Ha a pontosság tesztelését választja, fontos, hogy olyan akusztikai adatkészletet válasszon, amely eltér a modell teljesítményének reális értelmezéséhez.

## <a name="deploy-a-custom-model"></a>Egyéni modell üzembe helyezése

Az adatok feltöltése és ellenőrzése, a pontosság kiértékelése és az egyéni modell betanítása után üzembe helyezhet egy egyéni végpontot, amelyet az alkalmazásaihoz, eszközeihez és termékeihez használhat. 

Egyéni végpont létrehozásához jelentkezzen be a [Custom Speech portálra](https://speech.microsoft.com/customspeech). A lap tetején található **Custom Speech** menüben válassza a **telepítés** lehetőséget. Ha először futtatja, láthatja, hogy nincsenek a táblázatban felsorolt végpontok. A végpont létrehozása után ezen a lapon követheti nyomon az egyes telepített végpontokat.

Ezután válassza a **végpont hozzáadása** lehetőséget, és adja meg az egyéni végpont **nevét** és **leírását** . Ezután válassza ki azt az egyéni modellt, amelyet hozzá szeretne rendelni a végponthoz.  Ezen a lapon engedélyezheti a naplózást is. A naplózás lehetővé teszi a végponti forgalom figyelését. Ha a naplózás le van tiltva, a rendszer nem tárolja a forgalmat.

![Képernyőkép, amely az új végpont oldalt jeleníti meg.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Ne felejtse el elfogadni a használati feltételeket és a díjszabási adatokat.

Ezután válassza a **Létrehozás** lehetőséget. Ez a művelet visszaadja az **üzembe helyezési** lapot. A tábla mostantól egy olyan bejegyzést tartalmaz, amely megfelel az egyéni végpontnak. A végpont állapota megjeleníti a jelenlegi állapotát. Akár 30 percet is igénybe vehet, ha új végpontot hoz létre az egyéni modellek használatával. Ha a központi telepítés állapota **Befejezettre** változik, a végpont készen áll a használatra.

A végpont üzembe helyezése után a végpont neve hivatkozásként jelenik meg. A hivatkozásra kattintva megtekintheti a végpontra vonatkozó adatokat, például a végpont kulcsát, a végpont URL-címét és a mintakód.

## <a name="view-logging-data"></a>Naplózási adatgyűjtés megtekintése

A naplózási adatok letölthetők a **végpont**  >  **részletei** területen.
> [!NOTE]
>A naplózási adatai 30 napig érhetők el a Microsoft tulajdonában lévő tárolóban. Ezt követően el lesz távolítva. Ha a felhasználó tulajdonában lévő Storage-fiók a Cognitive Services-előfizetéshez van csatolva, a naplózási adatai nem törlődnek automatikusan.

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan használhatja az egyéni modellt](how-to-specify-source-language.md)

## <a name="additional-resources"></a>További források

- [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
