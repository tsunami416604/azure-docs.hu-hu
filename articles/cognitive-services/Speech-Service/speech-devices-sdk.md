---
title: A beszéd-eszközökről SDK
description: Ismerkedjen meg a beszédfelismerés Devices SDK-val.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: ba91d5fd556cdc189f6303ac216c8fdd9495c74b
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165420"
---
# <a name="about-the-speech-devices-sdk-preview"></a>A beszéd-eszközökről SDK-t (előzetes verzió)

A [Microsoft Speech service](overview.md) együttműködik számos különböző hang forrásai és eszközei. Most a következő szintre való megfelelést kiváltó hardver- és beszédfelismerés alkalmazásait is igénybe vehet. A beszédfelismerés Devices SDK-val az régiójával célirányosan fejlesztett, mikrofon tömb szoftverfejlesztői készletekkel pretuned könyvtárból. 

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

