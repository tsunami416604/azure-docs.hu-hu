---
title: SDK - beszédszolgáltatások beszédfelismerési eszközökkel kapcsolatos
titleSuffix: Azure Cognitive Services
description: Első lépések a beszédfelismerő Devices SDK-val. A beszédszolgáltatások együttműködve számos hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val egy előre beállított kódtár, amely az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 44e993375b17960ad5d9ced11d37ed3701a58f73
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101738"
---
# <a name="about-the-speech-devices-sdk-preview"></a>A beszéd-eszközökről SDK-t (előzetes verzió)

A [beszédszolgáltatás](overview.md) együttműködik számos különböző hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel pretuned könyvtárból.

A beszédfelismerés eszközök SDK segítségével:
* Gyorsan tesztelheti az új hangalapú forgatókönyvek.
* A felhő alapú beszédszolgáltatás könnyen integrálható az eszköz.
* Hozzon létre egy kivételes felhasználói élmény az ügyfelek számára.

A beszédfelismerési eszköz SDK-t használ a [beszéd SDK](speech-sdk.md). A beszédfelismerés SDK-t használ a hang, a speciális hang feldolgozási algoritmus az eszköz mikrofonját tömbből, která je zpracována küldése a [beszédszolgáltatás](overview.md). Többcsatornás hang pontosabb távolságban mező biztosításához használt [beszédfelismerés](speech-to-text.md) zaj tiltási, echo törlését, beamforming és dereverberation keresztül.

A beszédfelismerés eszközök SDK-t hozhat létre környezeti rendelkező eszközök a saját is használhatja [testre szabott ébresztési word](speech-devices-sdk-create-kws.md)– így a köteg kezdeményező felhasználói interakció a márka egyedi.

A beszédfelismerés Devices SDK-val segíti az voice-kompatibilis forgatókönyvek, például a meghajtó érvényes sorbarendezésre rendszerek, az áruházon belüli vagy in-home asszisztensek és intelligens előadói különböző. Vissza a azokat egy alapértelmezett beszél, hogy a felhasználók a szöveges, reagáljon vagy [egyéni beszédfelismerési](how-to-customize-voice-font.md), adja meg a keresési eredmények között, [fordítása](speech-translation.md) az más nyelvekre, és egyéb. Kíváncsian várjuk, mit hoz létre!

## <a name="development-kit-providers"></a>Development kit szolgáltatók

Jelenleg ezek teljes, végpontok közötti rendszerek referenciatervek érhetők el:

|||
|-|-|
|[![ROOBO embléma](media/speech-devices-sdk/roobo-logo.png)](http://ddk.roobo.com/)|ROOBO kész mesterséges intelligencia (AI) rendszer megoldásokat kínál electric háztartási, autók, robotok, a játékok és más iparágak. Referenciatervek ROOBO a jelentősen csökkentheti a fejlesztési-piacra keresztül a Microsoft Speech szolgáltatással. [Látogasson el a ROOBO](http://ddk.roobo.com/).|

## <a name="next-steps"></a>További lépések

Első lépésként kérje le egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/ai/) és iratkozzon fel a Speech Devices SDK-val.

> [!div class="nextstepaction"]
> [Iratkozzon fel a Speech Devices SDK-val](get-speech-devices-sdk.md)
