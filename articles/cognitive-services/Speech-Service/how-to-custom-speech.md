---
title: Egyéni beszédfelismerés – beszédszolgáltatások használatának első lépései
titlesuffix: Azure Cognitive Services
description: Egyéni Beszédmodell egy online eszközök, amelyek lehetővé teszik, hogy értékelje ki és kezelje az alkalmazásokat, eszközöket és termékeket a Microsoft hang-szöveg transzformációs pontosságának javítása. Első lépésként mindössze teszt hangfájlok szemponttal. Indítsa el, egy egyéni hang-szöveg transzformációs megoldást vezet be az alábbi hivatkozásokat követve.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: f9b9fc0a2939f601cbddafb6ac400130e794da2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060891"
---
# <a name="what-is-custom-speech"></a>Mi az egyéni beszédfelismerési?

[Egyéni Beszédmodell](https://aka.ms/custom-speech) , amelyek lehetővé teszik, hogy értékelje ki és kezelje az alkalmazásokat, eszközöket és termékeket a Microsoft hang-szöveg transzformációs pontosságának javítása online eszközök készlete. Első lépésként mindössze teszt hangfájlok szemponttal. Indítsa el, egy egyéni hang-szöveg transzformációs megoldást vezet be az alábbi hivatkozásokat követve.

## <a name="whats-in-custom-speech"></a>Mi az egyéni beszédfelismerési?

Mielőtt bármit a Custom Speech megteheti, szüksége lesz egy Azure-fiók és a egy beszédszolgáltatások előfizetés. Fiókkal konfiguráltunk, miután adatok előkészítése, betanítása és tesztelni a, vizsgálja meg a felismerés minőségi, kiértékelése pontosságát, és végső soron telepítheti és a hang-szöveg transzformációs egyéni modellt használja.

Ez az ábra a Custom Speech-portál alkotóelemeit emeli ki. Az alábbi hivatkozások segítségével további információ az egyes lépések.

![A Custom Speech-portál alkotó különböző összetevői emeli ki.](./media/custom-speech/custom-speech-overview.png)

1. [Fizessen elő és hozzon létre egy projektet](#set-up-your-azure-account) – Azure-fiók létrehozása, és feliratkozhat a beszédszolgáltatások. Ez az egységesített előfizetés hozzáférést biztosít a hang-szöveg, szöveg-hang transzformációs, beszédalapú fordítási és a Custom Speech-portál. Ezután beszédszolgáltatások előfizetését használja, hozzon létre az első egyéni beszéd-projekt.

2. [Tesztadatok feltöltése](how-to-custom-speech-test-data.md) -feltöltési Tesztadatok (hangfájlokat) értékelheti ki a Microsoft hang-szöveg transzformációs szánt az alkalmazásokat, eszközöket és termékeket.

3. [Vizsgálja meg a felismerés minőségi](how-to-custom-speech-inspect-data.md) – a Custom Speech-portál használatával feltöltött hang lejátszása, és vizsgálja meg a teszt adatai speech recognition minőségét. A mennyiségi mérések, lásd: [adatok vizsgálata](how-to-custom-speech-inspect-data.md).

4. [Pontosság kiértékelése](how-to-custom-speech-evaluate-data.md) – a hang-szöveg transzformációs modell pontosságának kiértékelése. A Custom Speech-portál biztosít egy *Word Hibaarány*, amelyek segítségével megállapítható, ha szükség-e további. Ha elégedett a pontosságot, a Speech Service API-k közvetlenül is használhatja. Ha szeretne egy relatív átlagos 5-20 %, a pontosság növeléséhez használja a **képzési** lapján töltse fel a további betanítási adatok, például az átiratok emberi címkével, és a kapcsolódó szöveges a portálon.

5. [A modell betanítását](how-to-custom-speech-train-model.md) – írt átiratok (10 – 1000 órában) biztosításával a hang-szöveg transzformációs modell pontosságának javítása, és a kapcsolódó szöveges (< 200 MB) a hang és ellenőrizheti az adatokat. Ezen adatok segítenek a hang-szöveg transzformációs modell betanításához. Képzés, után tesztelje újra, ha elégedett az eredménnyel, a modell üzembe helyezheti.

6. [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md) – a hang-szöveg transzformációs modell egyéni végpont létrehozásához, és használhatja az alkalmazásokat, eszközöket és termékeket.

## <a name="set-up-your-azure-account"></a>Az Azure-fiók

Beszédszolgáltatások előfizetés szükség, egy egyéni modell létrehozásához a Custom Speech-portál használata előtt. Kövesse ezeket az utasításokat hozzon létre egy standard beszédszolgáltatások előfizetést: [Hozzon létre egy beszéd előfizetést](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Ügyeljen arra, hogy standard szintű (S0)-előfizetések létrehozása, (F0) ingyenes próbaverziós előfizetésekben nem támogatottak.

Miután létrehozott egy Azure-fiók és a egy beszédszolgáltatások előfizetés, szüksége lesz a Custom Speech-portál bejelentkezni, és csatlakozzon az előfizetéséhez.

1. Az beszédszolgáltatások előfizetési kulcs lekérése az Azure Portalról.
2. Jelentkezzen be a [Custom Speech-portál](https://aka.ms/custom-speech).
3. Válassza ki az előfizetést, és hozzon létre egy beszéd projektet kell.
4. Ha szeretné módosítani az előfizetés, használja a **fogaskerék** ikonra a felső navigációs menüben található.

## <a name="how-to-create-a-project"></a>A projekt létrehozása

Tartalom, például az adatokat, modelleket, tesztek és végpontok vannak szervezve **projektek** a Custom Speech-portál. Minden projekt csak egy tartományhoz, és az ország és nyelven. Például előfordulhat, hogy hozzon létre egy projektet, amelyek használják az Egyesült Államokban angol nyelvű telefonos ügyfélszolgálatok.

Az első-projekt létrehozásához válassza a **Speech-szöveg/egyéni – beszédfelismerés**, majd kattintson a **új projekt**. Kövesse az utasításokat a varázsló által biztosított a projekt létrehozásához. Miután létrehozott egy projektet, négy lapon kell megjelennie: **Adatok**, **tesztelés**, **képzési**, és **üzembe helyezési**. A megadott hivatkozásokon találhat [további lépések](#next-steps) megtudhatja, hogyan használhatja az egyes lapokon.

## <a name="next-steps"></a>További lépések

* [Készítse elő és az adatok tesztelése](how-to-custom-speech-test-data.md)
* [Az adatok vizsgálata](how-to-custom-speech-inspect-data.md)
* [Az adatok](how-to-custom-speech-evaluate-data.md)
* [A modell tanítása](how-to-custom-speech-train-model.md)
* [A modell üzembe helyezése](how-to-custom-speech-deploy-model.md)
