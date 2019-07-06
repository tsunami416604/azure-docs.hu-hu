---
title: Beszéd Devices SDK-val mikrofon tömb ajánlatok – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Beszéd Devices SDK-val mikrofon tömb javaslatok. A következő tömb geometriája használata akkor javasolt, a Microsoft hang Stack való használatra. Megbízható forrásból helyét, és a zajszint elutasítása javult a függőségeket tartalmazó mikrofonok nagyobb számú konkrét alkalmazások, a felhasználói esetek és az eszköz méretformátumhoz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 121e94228ca85684b20f2ee43c0f7fa3af82fc73
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606338"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Beszéd eszközök SDK mikrofon tömb javaslatok

Ebből a cikkből megismerheti, hogyan tervezhet a mikrofon tömb a Speech eszköz SDK-hoz készült.

A beszédfelismerés Devices SDK-val amelyik a legjobban illik a mikrofon-tömb, amely a mikrofon geometry és összetevő-választást is beleértve, a következő irányelvek alapján úgy lett kialakítva. Integráció és elektromos szempontok útmutatást is kapnak.

## <a name="microphone-geometry"></a>Mikrofon geometriai

A következő tömb geometriája használata akkor javasolt, a Microsoft hang Stack való használatra. Megbízható forrásból helyét, és a zajszint elutasítása javult a függőségeket tartalmazó mikrofonok nagyobb számú konkrét alkalmazások, a felhasználói esetek és az eszköz méretformátumhoz.

|          | Kör alakú tömb    |       |  Lineáris tömb              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Mikrofonok előtt  | 7                 | 4                 | 4              | 2              |
| geometriai | 6 külső, 1 központ, a Radius 42.5 mm egyenletesen =| 3 külső, 1 központ, a Radius 42.5 mm egyenletesen = | Hossz = 120 mm térköz = 40 mm | Térköz = 40 mm |

Mikrofon csatornák megfelelően minden tömb, 0-tól növelése újabb kitaláltak számozására összefűzve.  A Microsoft hang verem egy további referencia stream hanglejátszás echo megszakítás végrehajtásához szükséges.

## <a name="component-selection"></a>Összetevő kiválasztása

Mikrofon összetevők pontosan reprodukálnia ingyenes zaj és torzulást jel van kiválasztva.

A javasolt tulajdonságok mikrofonok kiválasztásakor a következők:

| Paraméter                         | Ajánlott                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz signal 94 dBSPL, A-weighted noise)   |
| Amplitúdó egyeztetése                | ± 1 dB @ 1 kHz                     |
| Fázis egyeztetése                    | \+ 2° 1 kHz @                       |
| Akusztikai túlterhelési pont (OFJ)     | \> 120 dBSPL (THD = 10%)          |
| Átviteli sebesség                          | Minimális 24 bites                    |
| Mintavételezési frekvencia                     | Minimum 16 kHz\*                   |
| Directivity                       | Körsugárzó                   |
| Gyakoriság válasz                | ± 3 dB, 200-8000-es Hz lebegőpontos maszk\*|
| Megbízhatóság                       | Tárolási hőmérséklet-70 C tartsuk ott-40 C<br />Működési hőmérséklet 20 ° C 55 c  |

*\*Nagyobb mintavételi arányt vagy "szélesebb" gyakoriság tartományokat is szükség lehet magas színvonalú kommunikáció (VoIP) alkalmazások*

Jó összetevő kijelölés jó elektroakusztikai integrációs annak érdekében, hogy a teljesítmény, a használt összetevők funkcionalitásának megsértése kell megfeleltetni. Egyedi alkalmazási helyzetek is szükségessé teheti a további követelmények (például: hőmérséklet-tartományokban működő).

## <a name="microphone-array-integration"></a>Mikrofon tömb integráció

A teljesítmény a tömbök integrálva az eszköz és a rögzített nyereséget vagy EQ után meg kell felelnie az alábbi javaslatokat:

|  Paraméter        |    Ajánlott |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz signal 94 dBSPL, A-weighted noise) |
|  Kimeneti érzékenység  | -26 dBFS/Pa @ 1 kHz (ajánlott) |
|  Amplitúdó egyeztetése  | ± 2 dB, 200-8000 Hz |
|  Fázis egyeztetése      | \+ 5°, 200-8000-es Hz |
| THD%                 | Az időtartomány legfeljebb 1 %, 200-8000-es Hz, 94 dBSPL, 5. rendelés |
|  Gyakoriság válasz  | ± 6 dB, 200-8000-es Hz lebegőpontos maszk\* |

*\*"Szélesebb" gyakoriság tartományok magas színvonalú kommunikáció (VoIP) alkalmazások esetén szükséges lehet*

## <a name="speaker-integration-recommendations"></a>Hangszóró integrációs javaslatok

Echo megszakítás speech recognition eszközök előadói tartalmazó szükség szerint további javaslatok előírt speaker kiválasztási és -integráció.

| Paraméter                         | Ajánlott                       |
|-----------------------------------|-----------------------------------|
| Lineáris kapcsolatos szempontok          | Nincs nem lineáris feldolgozása után speaker referencia, ellenkező esetben egy visszacsatolási hardveralapú referencia adatfolyam megadása kötelező  |
| Hangszóró visszacsatolási                  | Nyújtja WASAPI, saját API-k, egyéni ALSA beépülő modult (Linux), vagy a belső vezérlőprogram csatornán keresztül biztosítja      |
| THD%                              | 3\. frekvenciáin legalább 5. ahhoz, @ 0,8 m ≤ 6.3 %, 315-500 Hz az időtartomány legfeljebb 5 % 630 – 5000 Hz 70 dBA lejátszás                 |
| A mikrofon echo kapcsoló      | \> a mic szintre normalized ITU-T G.122 melléklet B.4 módszerrel TCLw-10 dB<br />TCLw = TCLwmeasured \+ (kimeneti érzékenységi szint – mért cél)<br />TCLw = TCLwmeasured \+ (mért szint – (-26)) |

## <a name="integration-design-architecture"></a>Integráció tervezés architektúrájának

Az architektúra a következőkre szükség, ha a mikrofon integrálása egy eszköz:

| Paraméter                         | Ajánlás                    |
|-----------------------------------|-----------------------------------|
| Hasonlósági MIC-Port               | Az összes mikrofon portjait ugyanolyan hosszúságú tömb    |
| MIC-Port dimenziók               | Port mérete Ø0.8 – 1.0 mm. Port hossza átmérőjű portkereséssel \< 2              |
| MIC zárolás                       | Zárolási tömítések stack felfelé egységesen implementálva. Javasoljuk, \> habgumiból készült tömítések 70 %-os tömörítési aránya     |
| MIC-megbízhatóság                   | Háló kell használni, hogy a port és a bejövő forgalom (között az alsó PCB ültették át a mikrofonok és tömítéssel/felső cover sealing)  |
| MIC-elkülönítés                     | Gumiból tömítések és rezgés elválasztás keresztül struktúra, különösen a bármely rezgés útvonalak miatt integrált előadói elkülönítése      |
| Mintavételi óra                    | Eszköz hang jitter és hitelköltségek ingyenes alacsony eltéréseket kell lennie.    |
| Rekord képesség                 | Az eszköz jegyezze fel a különálló csatornákat nyers Streamek egyidejűleg képesnek kell lennie. |
| USB                               | Az összes USB hangbeviteli eszközök leírók a következők szerint kell beállítania a [USB hangbemeneti eszközök Rev3 specifikációja](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometriai               | Illesztőprogramok musí implementovat [mikrofon tömb geometriai leírók](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) megfelelően  |
| Felderíthetőség                   | Eszközök nem rendelkezhetnek bármely undiscoverable vagy ellenőrizhetetlen hardver, a belső vezérlőprogram vagy a 3. fél szoftveres nem lineáris hang algoritmusokat és-tárolókról az eszköz|
| Rögzítési formátum                    | Rögzítési formátum kell használnia egy minimális mintavételi ráta 16 kHz és ajánlott 24-bitová hloubka obrázku      |

## <a name="electrical-architecture-considerations"></a>Elektromos architektúra szempontjai

Ahol lehetséges, egy USB-gazdagép (például az SoC, a Microsoft hang vermet futtató) tömbök csatlakozott és kapcsolódási beszédszolgáltatások vagy más alkalmazásokhoz.

Hardverösszetevők, például a PDM-TDM átalakítás megmaradjanak, hogy a dinamikus tartományának és a mikrofon-SNR újbóli vételezők belül.

Nagy sebességű USB hang osztály 2.0 annak érdekében, hogy a szükséges sávszélesség legfeljebb hét csatornák minta a nagyobb sebesség és az átviteli depths belül bármely hang MCU kell támogatni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a Speech Devices SDK-val](speech-devices-sdk.md)
