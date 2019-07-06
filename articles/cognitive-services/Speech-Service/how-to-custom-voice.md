---
title: Mi az egyéni beszédfelismerési? – Beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: Egyedi Hangüzenetek online eszközöket, amelyek lehetővé teszik, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját. Első lépésként mindössze szemponttal hangfájlok és a kapcsolódó beszédátírás. Indítsa el, egy egyéni hang-szöveg transzformációs megoldást vezet be az alábbi hivatkozásokat követve.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ed5428da28ebea8b1c7f925696f88e3f3f898942
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606702"
---
# <a name="get-started-with-custom-voice"></a>Bevezetés a Custom Voice szolgáltatásba

Egyedi Hangüzenetek online eszközöket, amelyek lehetővé teszik, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját. Első lépésként mindössze szemponttal hangfájlok és a kapcsolódó beszédátírás. Kövesse az alábbi hivatkozásokat, egy egyéni szöveg-hang transzformációs élmény létrehozásának megkezdéséhez.

## <a name="whats-in-custom-voice"></a>Mi az egyéni beszédfelismerési?

Mielőtt elkezdené az egyéni beszédfelismerési, az Azure-fiók és a egy beszédszolgáltatások előfizetés lesz szüksége. Miután létrehozott egy fiókot, meg is adatok előkészítése, betanítását és tesztelni a, hangalapú minőségi kiértékelése és végső soron az egyéni beszédfelismerési modell rendszerbe állítása.

Az alábbi ábrán kiemeli azon lépéseket, az egyéni beszédfelismerési portal segítségével egyéni beszédfelismerési modell létrehozásához. További információ a hivatkozások segítségével.

![Egyéni beszédfelismerési architektúra ábrája](media/custom-voice/custom-voice-diagram.png)

1.  [Fizessen elő és hozzon létre egy projektet](#set-up-your-azure-account) – Azure-fiók létrehozása, és hozzon létre egy beszédszolgáltatások előfizetést. Ez az egységesített előfizetés hozzáférést biztosít a hang-szöveg, szöveg-hang transzformációs, beszédalapú fordítási és az egyéni beszédfelismerési portálon. Ezután beszédszolgáltatások előfizetését használja, hozzon létre az első egyéni beszédfelismerési projektet.

2.  [Adatok feltöltése](how-to-custom-voice-create-voice.md#upload-your-datasets) -adatok (hang- és szöveget) feltöltése az egyéni beszédfelismerési portal vagy az egyéni beszédfelismerési API használatával. A portálról vizsgálja meg, és írásmódja pontszámokat és jel zaj arányok kiértékeléséhez. További információkért lásd: [adatok előkészítése az egyéni beszédfelismerési](how-to-custom-voice-prepare-data.md).

3.  [A modell betanítását](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – használja az adatok egy egyéni szövegfelolvasás modell létrehozásához. A modell különböző nyelveken betaníthatja. Képzés, miután teszteli a modellt, és ha már elégedett az eredménnyel, a modell is telepítheti.

4.  [A modell üzembe helyezése](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – hozzon létre egy egyéni végpontot a szövegfelolvasás modell, és a termékek, eszközök és alkalmazások beszédszintézishez vele.

## <a name="set-up-your-azure-account"></a>Az Azure-fiók

Beszédszolgáltatások előfizetés szükség, egy egyéni modell létrehozásához a Custom Speech-portál használata előtt. Kövesse ezeket az utasításokat beszédszolgáltatások előfizetés létrehozása az Azure-ban. Ha nem rendelkezik Azure-fiókkal, akkor regisztráljon egy új.  

Miután létrehozott egy Azure-fiók és a egy beszédszolgáltatások előfizetés, kell jelentkezzen be az egyéni beszédfelismerési portálra, és csatlakozzon az előfizetéséhez.

1. Az beszédszolgáltatások előfizetési kulcs lekérése az Azure Portalról.
2. Jelentkezzen be a [egyéni beszédfelismerési portál](https://aka.ms/custom-voice).
3. Válassza ki az előfizetését, és a egy beszéd-projekt létrehozása.
4. Ha másik Speech-előfizetésre váltani szeretne, használja a fogaskerék ikonra a felső navigációs menüben található.

> [!NOTE]
> Az egyéni beszédfelismerési szolgáltatás nem támogatja a 30 napos ingyenes próbaverzió kulcsot. Rendelkeznie kell egy F0 vagy a szolgáltatás használatához Azure-ban létrehozott S0 kulcsot.

## <a name="how-to-create-a-project"></a>A projekt létrehozása

Tartalom, például az adatokat, modelleket, tesztek és végpontok vannak szervezve **projektek** az egyéni beszédfelismerési portálon. Minden projekt az adott ország/nyelv és a létrehozni kívánt hang tartalmazzák. Például előfordulhat, hogy hozzon létre egy projektet a ügyfélszolgálatával Csevegés robotok az Egyesült államokbeli (en-US) angol használó a női hangot.

Az első-projekt létrehozásához válassza a **Text-to-Speech/egyéni beszédfelismerési** lapfülre, majd kattintson a **új projekt**. Kövesse az utasításokat a varázsló által biztosított a projekt létrehozásához. Miután létrehozott egy projektet, négy lapján jelenik meg: **Adatok**, **képzési**, **tesztelés**, és **üzembe helyezési**. A megadott hivatkozásokon találhat [további lépések](#next-steps) megtudhatja, hogyan használhatja az egyes lapokon.

## <a name="next-steps"></a>További lépések

- [Egyéni beszédfelismerési adatok előkészítése](how-to-custom-voice.md)
- [Hozzon létre egy egyéni beszédfelismerési](how-to-custom-voice-create-voice.md)
- [Útmutató: Rögzítése a voice-minták](record-custom-voice-samples.md)
