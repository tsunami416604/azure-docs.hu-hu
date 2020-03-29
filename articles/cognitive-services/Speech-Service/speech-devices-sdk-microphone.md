---
title: Beszédeszközök SDK mikrofontömbjavaslatok
titleSuffix: Azure Cognitive Services
description: Beszédeszközök SDK mikrofontömb javaslatok. Ezek a tömbgeometriák a Microsoft Audio Stack hez való használatra ajánlottak.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168140"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Beszédeszközök SDK mikrofontömbjének ajánlásai

Ebben a cikkben megtudhatja, hogyan tervezheti meg a beszédeszközök SDK mikrofontömbjét.

A Beszédeszközök SDK a következő irányelvek nek megfelelően tervezett mikrofontömbökkel működik a legjobban, beleértve a mikrofon geometriáját és az alkatrészek kiválasztását. Iránymutatást adnak az integrációval és az elektromos megfontolásokkal kapcsolatos szempontokról is.

## <a name="microphone-geometry"></a>Mikrofon geometriája

A következő tömbgeometriák használata ajánlott a Microsoft Audio Stack használatához. A hangforrások helye és a környezeti zaj elutasítása javul, mivel több mikrofon függ az egyes alkalmazásoktól, a felhasználói forgatókönyvektől és az eszköz formatényezőtől.

|     | Körkörös tömb |     | Lineáris tömb |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mikrofon | 7 | 4 | 4 | 2 |
| Geometria | 6 Külső, 1 Középre, Sugár = 42,5 mm, egyenletesen elosztott | 3 Külső, 1 Középre, Sugár = 42,5 mm, egyenletesen elosztott | Hossz = 120 mm, térköz = 40 mm | Térköz = 40 mm |

A mikrofoncsatornákat a fenti tömbök számozásának megfelelően kell megrendelni, 0-ról növelve. A Visszhang-megszakítás végrehajtásához a Microsoft Audio Stack további referenciaadatfolyamot igényel a hanglejátszáshoz.

## <a name="component-selection"></a>Összetevő kiválasztása

A mikrofon komponenseit úgy kell kiválasztani, hogy pontosan reprodukálják a zajtól és torzítástól mentes jelet.

A mikrofonok kiválasztásakor ajánlott tulajdonságok a következők:

| Paraméter | Ajánlott |
| --------- | ----------- |
| Snr | \>= 65 dB (1 kHz-es jel 94 dBSPL, A-súlyozott zaj) |
| Amplitúdó-egyeztetés | ± 1 dB @ 1 kHz |
| Fázis egyeztetése | ± 2° @ 1 kHz |
| Akusztikus túlterhelési pont (AOP) | \>= 120 dBSPL (THD = 10%) |
| Bitsebesség | Minimum 24 bites |
| Mintavételezési frekvencia | Legalább 16 kHz\* |
| Frekvenciaválasz | ± 3 dB, 200-8000 Hz úszó maszk\* |
| Megbízhatóság | Tárolási hőmérséklet tartomány -40°C és 70°C között<br />Üzemi hőmérséklet -20°C és 55°C között |

\*_A kiváló minőségű kommunikációs (VoIP) alkalmazásokhoz magasabb mintavételi arányokra vagy "szélesebb" frekvenciatartományokra lehet szükség_

A jó alkatrészválasztást jó elektroakusztikus integrációval kell párosítani a felhasznált alkatrészek teljesítményének romlásának elkerülése érdekében. Az egyedi használati esetek további követelményeket is szükségessé tehetnek (például: üzemi hőmérséklet-tartományok).

## <a name="microphone-array-integration"></a>Mikrofontömb integrációja

A mikrofontömb teljesítménye az eszközbe integrálva eltér az alkatrész specifikációjától. Fontos annak biztosítása, hogy a mikrofonok az integráció után jól illeszkedjenek. Ezért a rögzített nyereség vagy EQ után mért eszközteljesítménynek meg kell felelnie az alábbi ajánlásoknak:

| Paraméter          | Ajánlott                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (1 kHz-es jel 94 dBSPL, A-súlyozott zaj) |
| Kimeneti érzékenység | -26 dBFS/Pa @ 1 kHz (ajánlott)                  |
| Amplitúdó-egyeztetés | ± 2 dB, 200–8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5.             |
| Frekvenciaválasz | ± 6 dB, 200-8000 Hz úszó maszk\*\*              |

\*\*_A THD méréséhez alacsony torzítású hangszóró szükséges (pl. Neumann KH120)_

\*\*_A kiváló minőségű kommunikációs (VoIP) alkalmazásokhoz "szélesebb" frekvenciatartományokra lehet szükség_

## <a name="speaker-integration-recommendations"></a>A felszólalók integrációs javaslatai

Mivel a hangszórókat tartalmazó beszédfelismerő eszközök esetében visszhangszűrésre van szükség, további javaslatok at tartalmaznak a hangszóró kiválasztásához és integrációjához.

| Paraméter | Ajánlott |
| --------- | ----------- |
| Linearitási szempontok | Nincs nem lineáris feldolgozás a hangszóró hivatkozása után, ellenkező esetben hardveralapú visszacsatolási referencia-adatfolyamra van szükség |
| Hangszóró visszacsatolás | Biztosított keresztül WASAPI, privát API-k, egyéni ALSA plug-in (Linux), vagy keresztül nyújtott firmware csatorna |
| THD% | 3. Octave Zenekarok minimum 5. |
| Echo csatlakozó mikrofonokhoz | \>-10 dB TCLw ITU-T G.122 B.4.<br />TCLw = TCLwmeasured \+ (mért szint - cél kimeneti érzékenység)<br />TCLw = TCLwmért \+ (mért szint - (-26)) |

## <a name="integration-design-architecture"></a>Integrációs tervezési architektúra

A mikrofonok eszközbe való integrálásakor az alábbi architektúra-irányelvekszükségesek:

| Paraméter | Ajánlás |
| --------- | -------------- |
| Mikrofonport hasonlósága | Minden mikrofonport azonos hosszúságú a tömbben |
| Mikrofonport méretei | Port mérete Ø0,8-1,0 mm. Port hossza / \< Port átmérője 2 |
| Mikrofon tömítés         | Tömítő tömítések egységesen végre stack-up. Javasoljuk, \> 70% tömörítési arány hab tömítések |
| Mikrofon megbízhatósága     | A por és a be- és beszivárgás megelőzésére hálót kell használni (az alsó portolt mikrofonok PCB-je és a tömítőtömítés/felső fedél között) |
| Mikrofon szigetelése       | Gumitömítések és rezgésleválasztás a szerkezeten keresztül, különösen a vibrációs útvonalak elszigetelésére a beépített hangszóróknak köszönhetően |
| Mintavételi óra      | A készülék hangjának alacsony sodródású vibrátormentesnek kell lennie |
| Rögzítési képesség   | Az eszköznek képesnek kell lennie az egyes csatornanyers adatfolyamok egyidejű rögzítésére |
| USB                 | Minden USB audiobemeneti eszköznek az [USB Audio Devices Rev3 spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometriája | Az illesztőprogramok megfelelően kell [implementáljanak a mikrofontömb geometriai leíróit](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Felfedező     | Az eszközök nem rendelkezhetnek az eszközre/eszközről érkező, nem észlelhető vagy irányíthatatlan hardverrel, firmware-rel vagy külső szoftveralapú nem lineáris hangfeldolgozási algoritmussal. |
| Rögzítési formátum      | A rögzítési formátumoknak legalább 16 kHz-es mintavételi sebességet és ajánlott 24 bites mélységet kell használniuk |

## <a name="electrical-architecture-considerations"></a>Elektromos architektúra szempontok

Adott esetben a tömbök csatlakoztathatók usb-állomáshoz (például a Microsoft Audio Stacket futtató soc-hoz), valamint a beszédfelismerési szolgáltatásokhoz vagy más alkalmazásokhoz való kapcsolódási pontokhoz.

A hardverösszetevőknek, például a PDM–TDM átalakításnak biztosítaniuk kell, hogy a mikrofonok dinamikus tartománya és SNR-je megmaradjon az újramintavevőkben.

A nagy sebességű USB Audio Class 2.0-t minden audio MCU-n belül támogatni kell, hogy a szükséges sávszélességet akár hét csatornához is biztosítsa magasabb mintavételi sebességgel és bitmélységben.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a beszédfelismerési eszközök SDK-járól](speech-devices-sdk.md)
