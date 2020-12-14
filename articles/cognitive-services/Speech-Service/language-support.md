---
title: Nyelvi támogatás – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás számos nyelvet támogat a beszédfelismeréshez és a szöveg-beszéd átalakításhoz, valamint a beszéd fordításához. Ez a cikk a szolgáltatás által nyújtott nyelvi támogatás átfogó listáját tartalmazza.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 12c9777449d3f360b8f08d99aca7b916856139d4
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400182"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Beszédfelismerési szolgáltatás nyelvi és hangalapú támogatása

A nyelvi támogatás a beszédfelismerési szolgáltatás működésének függvényében változik. Az alábbi táblázatok összefoglalják a [beszédfelismerési](#speech-to-text)és a [szöveges](#text-to-speech) [, valamint a beszédfelismerési](#speech-translation) szolgáltatás nyelvének támogatását.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). 

A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a **hang-és emberi címkével ellátott átiratok** vagy a **kapcsolódó szövegek feltöltésével: mondatok**. A testreszabással kapcsolatos további tudnivalókért tekintse meg [a Custom Speech első lépéseivel foglalkozó](./custom-speech-overview.md)témakört.

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Nyelv                          | Területi beállítás (BCP-47) | Testreszabások                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Arab (Bahrein), modern standard  |`ar-BH` | Nyelvi modell                                   |
|Arab (Egyiptom)                     |`ar-EG` | Nyelvi modell                                   |
|Arab (Irak)                      |`ar-IQ` | Nyelvi modell                                   |
|Arab (Izrael)                              |`ar-IL` | Nyelvi modell                                                   |
|Arab (Jordánia)                    |`ar-JO` | Nyelvi modell                                   |
|Arab (Kuvait)                    |`ar-KW` | Nyelvi modell                                   |
|Arab (Libanon)                   |`ar-LB` | Nyelvi modell                                   |
|Arab (Omán)                      |`ar-OM` | Nyelvi modell                                   |
|Arab (Katar)                     |`ar-QA` | Nyelvi modell                                   |
|Arab (Szaúd-Arábia)              |`ar-SA` | Nyelvi modell                                   |
|Arab (palesztin állam)            |`ar-PS` | Nyelvi modell                                   |
|Arab (Szíria)                     |`ar-SY` | Nyelvi modell                                   |
|Arab (Egyesült Arab Emírségek)      |`ar-AE` | Nyelvi modell                                   |
|Bolgár (Bulgária)               |`bg-BG` | Nyelvi modell                                   |
|Katalán (Spanyolország)                    |`ca-ES` | Nyelvi modell                                   |
|Kínai (kantoni, hagyományos)   |`zh-HK` | Nyelvi modell                                   |
|Kínai (mandarin, egyszerűsített)     |`zh-CN` | Akusztikai modell<br>Nyelvi modell                 |
|Kínai (tajvani mandarin)       |`zh-TW` | Nyelvi modell                                   |
|Horvát (Horvátország)                 |`hr-HR` | Nyelvi modell                                   |
|Cseh (Cseh Köztársaság)             |`cs-CZ` | Nyelvi modell                                   |
|Dán (Dánia)                   |`da-DK` | Nyelvi modell                                   |
|Holland (Hollandia)                |`nl-NL` | Nyelvi modell                                   |
|Angol (Ausztrália)                |`en-AU` | Akusztikai modell<br>Nyelvi modell                 |
|Angol (Kanada)                   |`en-CA` | Akusztikai modell<br>Nyelvi modell                 |
|Angol (Hongkong)                |`en-HK` | Nyelvi modell                                   |
|Angol (India)                    |`en-IN` | Akusztikai modell<br>Nyelvi modell                 |
|Angol (Írország)                  |`en-IE` | Nyelvi modell                                   |
|Angol (Új-Zéland)              |`en-NZ` | Akusztikai modell<br>Nyelvi modell                 |
|Angol (Nigéria)                          |`en-NG` | Nyelvi modell                                                     |
|Angol (Fülöp-szigetek)              |`en-PH` | Nyelvi modell                                   |
|Angol (Szingapúr)                |`en-SG` | Nyelvi modell                                   |
|Angol (Dél-Afrika)             |`en-ZA` | Nyelvi modell                                   |
|angol (Egyesült Királyság)           |`en-GB` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|angol (Egyesült Államok)            |`en-US` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|Észt (Észtország)                  |`et-EE` | Nyelvi modell                                   |
|Finn (Finnország)                  |`fi-FI` | Nyelvi modell                                   |
|Francia (Kanada)                    |`fr-CA` | Akusztikai modell<br>Nyelvi modell                 |
|Francia (Franciaország)                    |`fr-FR` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|Német (Németország)                   |`de-DE` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|Görög (Görögország)                     |`el-GR` | Nyelvi modell                                   |
|Gudzsaráti (indiai)                  |`gu-IN` | Nyelvi modell                                   |
|Hindi (India)                      |`hi-IN` | Akusztikai modell<br>Nyelvi modell                 |
|Magyar (Magyarország)                |`hu-HU` | Nyelvi modell                                   |
|Ír (Írország)                     |`ga-IE` | Nyelvi modell                                   |
|Olasz (Olaszország)                    |`it-IT` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|Japán (Japán)                   |`ja-JP` | Nyelvi modell                                   |
|Koreai (Dél-Korea)                     |`ko-KR` | Nyelvi modell                                   |
|Lett (Lettország)                   |`lv-LV` | Nyelvi modell                                   |
|Litván (Litvánia)             |`lt-LT` | Nyelvi modell                                   |
|Máltai (Málta)                     |`mt-MT` | Nyelvi modell                                   |
|Marathi (India)                    |`mr-IN` | Nyelvi modell                                   |
|norvég (bokmål, Norvégia)         |`nb-NO` | Nyelvi modell                                   |
|Lengyel (Lengyelország)                    |`pl-PL` | Nyelvi modell                                   |
|Portugál (Brazília)                |`pt-BR` | Akusztikai modell<br>Nyelvi modell<br>Kiejtése|
|Portugál (Portugália)              |`pt-PT` | Nyelvi modell                                   |
|Román (Románia)                 |`ro-RO` | Nyelvi modell                                   |
|Orosz (Oroszország)                   |`ru-RU` | Akusztikai modell<br>Nyelvi modell                 |
|Szlovák (Szlovákia)                  |`sk-SK` | Nyelvi modell                                   |
|Szlovén (Szlovénia)               |`sl-SI` | Nyelvi modell                                   |
|Spanyol (Argentína)                |`es-AR` | Nyelvi modell                                   |
|Spanyol (Bolívia)                  |`es-BO` | Nyelvi modell                                   |
|Spanyol (Chile)                    |`es-CL` | Nyelvi modell                                   |
|Spanyol (Kolumbia)                 |`es-CO` | Nyelvi modell                                   |
|Spanyol (Costa Rica)               |`es-CR` | Nyelvi modell                                   |
|Spanyol (Kuba)                     |`es-CU` | Nyelvi modell                                   |
|Spanyol (Dominikai Köztársaság)       |`es-DO` | Nyelvi modell                                   |
|Spanyol (Ecuador)                  |`es-EC` | Nyelvi modell                                   |
|Spanyol (Salvador)              |`es-SV` | Nyelvi modell                                   |
|Spanyol (Egyenlítői-Guinea)            |`es-GQ` | Nyelvi modell                                   |
|Spanyol (Guatemala)                |`es-GT` | Nyelvi modell                                   |
|Spanyol (Honduras)                 |`es-HN` | Nyelvi modell                                   |
|Spanyol (Mexikó)                   |`es-MX` | Akusztikai modell<br>Nyelvi modell                 |
|Spanyol (Nicaragua)                |`es-NI` | Nyelvi modell                                   |
|Spanyol (Panama)                   |`es-PA` | Nyelvi modell                                   |
|Spanyol (Paraguay)                 |`es-PY` | Nyelvi modell                                   |
|Spanyol (perui)                     |`es-PE` | Nyelvi modell                                   |
|Spanyol (Puerto Rico)              |`es-PR` | Nyelvi modell                                   |
|Spanyol (Spanyolország)                    |`es-ES` | Akusztikai modell<br>Nyelvi modell                 |
|Spanyol (Uruguay)                  |`es-UY` | Nyelvi modell                                   |
|Spanyol (USA)                      |`es-US` | Nyelvi modell                                   |
|Spanyol (Venezuela)                |`es-VE` | Nyelvi modell                                   |
|Svéd (Svédország)                   |`sv-SE` | Nyelvi modell                                   |
|Tamil (India)                      |`ta-IN` | Nyelvi modell                                   |
|Telugu (India)                     |`te-IN` | Nyelvi modell                                   |
|Thai (Thaiföld)                    |`th-TH` | Nyelvi modell                                   |
|Török (Törökország)                   |`tr-TR` | Nyelvi modell                                   |

## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API-k egyaránt támogatják ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít. Az egyes régiókban vagy végpontokban támogatott nyelvek és hangok teljes listáját a [Voices/List API](rest-text-to-speech.md#get-a-list-of-voices)-n keresztül is elérheti. 

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

| Nyelv | Területi beállítás | Nem | Hang neve | Stílus támogatása |
|---|---|---|---|---|
| Arab (Egyiptom) | `ar-EG` | Nő | `ar-EG-SalmaNeural` | Általános kérdések |
| Arab (Egyiptom) | `ar-EG` | Férfi | `ar-EG-ShakirNeural` <sup>Új</sup> | Általános kérdések |
| Arab (Szaúd-Arábia) | `ar-SA` | Nő | `ar-SA-ZariyahNeural` | Általános kérdések |
| Arab (Szaúd-Arábia) | `ar-SA` | Férfi | `ar-SA-HamedNeural` <sup>Új</sup> | Általános kérdések |
| Bolgár (duzzadt) | `bg-BG` | Nő | `bg-BG-KalinaNeural` | Általános kérdések |
| Bolgár (duzzadt) | `bg-BG` | Férfi | `bg-BG-BorislavNeural` <sup>Új</sup> | Általános kérdések |
| Katalán (Spanyolország) | `ca-ES` | Nő | `ca-ES-AlbaNeural` | Általános kérdések |
| Katalán (Spanyolország) | `ca-ES` | Nő | `ca-ES-JoanaNeural` <sup>Új</sup> | Általános kérdések |
| Katalán (Spanyolország) | `ca-ES` | Férfi | `ca-ES-EnricNeural` <sup>Új</sup> | Általános kérdések |
| Kínai (kantoni, hagyományos) | `zh-HK` | Nő | `zh-HK-HiuGaaiNeural` | Általános kérdések |
| Kínai (kantoni, hagyományos) | `zh-HK` | Nő | `zh-HK-HiuMaanNeural` <sup>Új</sup> | Általános kérdések |
| Kínai (kantoni, hagyományos) | `zh-HK` | Férfi | `zh-HK-WanLungNeural` <sup>Új</sup> | Általános kérdések |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaoxiaoNeural` | Általános, több hangstílus érhető el [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaoyouNeural` | A szövegegység elbeszéléséhez optimalizált, gyerek hang |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Férfi | `zh-CN-YunyangNeural` | A hírek olvasására optimalizált<br /> több hangstílus érhető el [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Férfi | `zh-CN-YunyeNeural` | A történet elbeszéléséhez optimalizált  |
| Kínai (tajvani mandarin) | `zh-TW` | Nő | `zh-TW-HsiaoChenNeural` <sup>Új</sup> | Általános kérdések |
| Kínai (tajvani mandarin) | `zh-TW` | Nő | `zh-TW-HsiaoYuNeural` | Általános kérdések |
| Kínai (tajvani mandarin) | `zh-TW` | Férfi | `zh-TW-YunJheNeural` <sup>Új</sup> | Általános kérdések |
| Horvát (Horvátország) | `hr-HR` | Nő | `hr-HR-GabrijelaNeural` | Általános kérdések |
| Horvát (Horvátország) | `hr-HR` | Férfi | `hr-HR-SreckoNeural` <sup>Új</sup> | Általános kérdések |
| Cseh (Cseh) | `cs-CZ` | Nő | `cs-CZ-VlastaNeural` | Általános kérdések |
| Cseh (Cseh) | `cs-CZ` | Férfi | `cs-CZ-AntoninNeural` <sup>Új</sup> | Általános kérdések |
| Dán (Dánia) | `da-DK` | Nő | `da-DK-ChristelNeural` | Általános kérdések |
| Dán (Dánia) | `da-DK` | Férfi | `da-DK-JeppeNeural` <sup>Új</sup> | Általános kérdések |
| Holland (Hollandia) | `nl-NL` | Nő | `nl-NL-ColetteNeural` | Általános kérdések |
| Holland (Hollandia) | `nl-NL` | Nő | `nl-NL-FennaNeural` <sup>Új</sup> | Általános kérdések |
| Holland (Hollandia) | `nl-NL` | Férfi | `nl-NL-MaartenNeural` <sup>Új</sup> | Általános kérdések |
| Angol (Ausztrália) | `en-AU` | Nő | `en-AU-NatashaNeural` | Általános kérdések |
| Angol (Ausztrália) | `en-AU` | Férfi | `en-AU-WilliamNeural` | Általános kérdések |
| Angol (Kanada) | `en-CA` | Nő | `en-CA-ClaraNeural` | Általános kérdések |
| Angol (Kanada) | `en-CA` | Férfi | `en-CA-LiamNeural` <sup>Új</sup> | Általános kérdések |
| Angol (India) | `en-IN` | Nő | `en-IN-NeerjaNeural` | Általános kérdések |
| Angol (India) | `en-IN` | Férfi | `en-IN-PrabhatNeural` <sup>Új</sup> | Általános kérdések |
| Angol (Írország) | `en-IE` | Nő | `en-IE-EmilyNeural` | Általános kérdések |
| Angol (Írország) | `en-IE` | Férfi | `en-IE-ConnorNeural` <sup>Új</sup> | Általános kérdések |
| angol (Egyesült Királyság) | `en-GB` | Nő | `en-GB-LibbyNeural` | Általános kérdések |
| angol (Egyesült Királyság) | `en-GB` | Nő | `en-GB-MiaNeural` | Általános kérdések |
| angol (Egyesült Királyság) | `en-GB` | Férfi | `en-GB-RyanNeural` | Általános kérdések |
| angol (Egyesült Államok) | `en-US` | Nő | `en-US-AriaNeural` | Általános, több hangstílus érhető el [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles)  |
| angol (Egyesült Államok) | `en-US` | Nő | `en-US-JennyNeural` | Általános kérdések |
| angol (Egyesült Államok) | `en-US` | Férfi | `en-US-GuyNeural` | Általános kérdések |
| Finn (Finnország) | `fi-FI` | Nő | `fi-FI-NooraNeural` | Általános kérdések |
| Finn (Finnország) | `fi-FI` | Nő | `fi-FI-SelmaNeural` <sup>Új</sup> | Általános kérdések |
| Finn (Finnország) | `fi-FI` | Férfi | `fi-FI-HarriNeural` <sup>Új</sup> | Általános kérdések |
| Francia (Kanada) | `fr-CA` | Nő | `fr-CA-SylvieNeural` | Általános kérdések |
| Francia (Kanada) | `fr-CA` | Férfi | `fr-CA-JeanNeural` | Általános kérdések |
| Francia (Franciaország) | `fr-FR` | Nő | `fr-FR-DeniseNeural` | Általános kérdések |
| Francia (Franciaország) | `fr-FR` | Férfi | `fr-FR-HenriNeural` | Általános kérdések |
| Francia (Svájc) | `fr-CH` | Nő | `fr-CH-ArianeNeural` | Általános kérdések |
| Francia (Svájc) | `fr-CH` | Férfi | `fr-CH-FabriceNeural` <sup>Új</sup> | Általános kérdések |
| Német (Ausztria) | `de-AT` | Nő | `de-AT-IngridNeural` | Általános kérdések |
| Német (Ausztria) | `de-AT` | Férfi | `de-AT-JonasNeural` <sup>Új</sup> | Általános kérdések |
| Német (Németország) | `de-DE` | Nő | `de-DE-KatjaNeural` | Általános kérdések |
| Német (Németország) | `de-DE` | Férfi | `de-DE-ConradNeural` | Általános kérdések |
| Német (Svájc) | `de-CH` | Nő | `de-CH-LeniNeural` | Általános kérdések |
| Német (Svájc) | `de-CH` | Férfi | `de-CH-JanNeural` <sup>Új</sup> | Általános kérdések |
| Görög (Görögország) | `el-GR` | Nő | `el-GR-AthinaNeural` | Általános kérdések |
| Görög (Görögország) | `el-GR` | Férfi | `el-GR-NestorasNeural` <sup>Új</sup> | Általános kérdések |
| Héber (Izrael) | `he-IL` | Nő | `he-IL-HilaNeural` | Általános kérdések |
| Héber (Izrael) | `he-IL` | Férfi | `he-IL-AvriNeural` <sup>Új</sup> | Általános kérdések |
| Hindi (India) | `hi-IN` | Nő | `hi-IN-SwaraNeural` | Általános kérdések |
| Hindi (India) | `hi-IN` | Férfi | `hi-IN-MadhurNeural` <sup>Új</sup> | Általános kérdések |
| Magyar (Magyarország) | `hu-HU` | Nő | `hu-HU-NoemiNeural` | Általános kérdések |
| Magyar (Magyarország) | `hu-HU` | Férfi | `hu-HU-TamasNeural` <sup>Új</sup> | Általános kérdések |
| Indonéz (Indonézia) | `id-ID` | Nő | `id-ID-GadisNeural` <sup>Új</sup> | Általános kérdések |
| Indonéz (Indonézia) | `id-ID` | Férfi | `id-ID-ArdiNeural` | Általános kérdések |
| Olasz (Olaszország) | `it-IT` | Nő | `it-IT-ElsaNeural` | Általános kérdések |
| Olasz (Olaszország) | `it-IT` | Nő | `it-IT-IsabellaNeural` | Általános kérdések |
| Olasz (Olaszország) | `it-IT` | Férfi | `it-IT-DiegoNeural` | Általános kérdések |
| Japán (Japán) | `ja-JP` | Nő | `ja-JP-NanamiNeural` | Általános kérdések |
| Japán (Japán) | `ja-JP` | Férfi | `ja-JP-KeitaNeural` | Általános kérdések |
| Koreai (Dél-Korea) | `ko-KR` | Nő | `ko-KR-SunHiNeural` | Általános kérdések |
| Koreai (Dél-Korea) | `ko-KR` | Férfi | `ko-KR-InJoonNeural` | Általános kérdések |
| Maláj (Malajzia) | `ms-MY` | Nő | `ms-MY-YasminNeural` | Általános kérdések |
| Maláj (Malajzia) | `ms-MY` | Férfi | `ms-MY-OsmanNeural` <sup>Új</sup> | Általános kérdések |
| norvég (bokmål, Norvégia) | `nb-NO` | Nő | `nb-NO-IselinNeural` | Általános kérdések |
| norvég (bokmål, Norvégia) | `nb-NO` | Nő | `nb-NO-PernilleNeural` <sup>Új</sup> | Általános kérdések |
| norvég (bokmål, Norvégia) | `nb-NO` | Férfi | `nb-NO-FinnNeural` <sup>Új</sup> | Általános kérdések |
| Lengyel (Lengyelország) | `pl-PL` | Nő | `pl-PL-AgnieszkaNeural` <sup>Új</sup> | Általános kérdések |
| Lengyel (Lengyelország) | `pl-PL` | Nő | `pl-PL-ZofiaNeural` | Általános kérdések |
| Lengyel (Lengyelország) | `pl-PL` | Férfi | `pl-PL-MarekNeural` <sup>Új</sup> | Általános kérdések |
| Portugál (Brazília) | `pt-BR` | Nő | `pt-BR-FranciscaNeural` | Általános, több hangstílus érhető el [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Portugál (Brazília) | `pt-BR` | Férfi | `pt-BR-AntonioNeural` | Általános kérdések |
| Portugál (Portugália) | `pt-PT` | Nő | `pt-PT-FernandaNeural` | Általános kérdések |
| Portugál (Portugália) | `pt-PT` | Nő | `pt-PT-RaquelNeural` <sup>Új</sup> | Általános kérdések |
| Portugál (Portugália) | `pt-PT` | Férfi | `pt-PT-DuarteNeural` <sup>Új</sup> | Általános kérdések |
| Román (Románia) | `ro-RO` | Nő | `ro-RO-AlinaNeural` | Általános kérdések |
| Román (Románia) | `ro-RO` | Férfi | `ro-RO-EmilNeural` <sup>Új</sup> | Általános kérdések |
| Orosz (Oroszország) | `ru-RU` | Nő | `ru-RU-DariyaNeural` | Általános kérdések |
| Orosz (Oroszország) | `ru-RU` | Nő | `ru-RU-SvetlanaNeural` <sup>Új</sup> | Általános kérdések |
| Orosz (Oroszország) | `ru-RU` | Férfi | `ru-RU-DmitryNeural` <sup>Új</sup> | Általános kérdések |
| Szlovák (Szlovákia) | `sk-SK` | Nő | `sk-SK-ViktoriaNeural` | Általános kérdések |
| Szlovák (Szlovákia) | `sk-SK` | Férfi | `sk-SK-LukasNeural` <sup>Új</sup> | Általános kérdések |
| Szlovén (Szlovénia) | `sl-SI` | Nő | `sl-SI-PetraNeural` | Általános kérdések |
| Szlovén (Szlovénia) | `sl-SI` | Férfi | `sl-SI-RokNeural` <sup>Új</sup> | Általános kérdések |
| Spanyol (Mexikó) | `es-MX` | Nő | `es-MX-DaliaNeural` | Általános kérdések |
| Spanyol (Mexikó) | `es-MX` | Férfi | `es-MX-JorgeNeural` | Általános kérdések |
| Spanyol (Spanyolország) | `es-ES` | Nő | `es-ES-ElviraNeural` | Általános kérdések |
| Spanyol (Spanyolország) | `es-ES` | Férfi | `es-ES-AlvaroNeural` | Általános kérdések |
| Svéd (Svédország) | `sv-SE` | Nő | `sv-SE-HilleviNeural` | Általános kérdések |
| Svéd (Svédország) | `sv-SE` | Nő | `sv-SE-SofieNeural` <sup>Új</sup> | Általános kérdések |
| Svéd (Svédország) | `sv-SE` | Férfi | `sv-SE-MattiasNeural` <sup>Új</sup> | Általános kérdések |
| Tamil (India) | `ta-IN` | Nő | `ta-IN-PallaviNeural` | Általános kérdések |
| Tamil (India) | `ta-IN` | Férfi | `ta-IN-ValluvarNeural` <sup>Új</sup> | Általános kérdések |
| Telugu (India) | `te-IN` | Nő | `te-IN-ShrutiNeural` | Általános kérdések |
| Telugu (India) | `te-IN` | Férfi | `te-IN-MohanNeural` <sup>Új</sup> | Általános kérdések |
| Thai (Thaiföld) | `th-TH` | Nő | `th-TH-AcharaNeural` | Általános kérdések |
| Thai (Thaiföld) | `th-TH` | Nő | `th-TH-PremwadeeNeural` | Általános kérdések |
| Thai (Thaiföld) | `th-TH` | Férfi | `th-TH-NiwatNeural` <sup>Új</sup> | Általános kérdések |
| Török (Törökország) | `tr-TR` | Nő | `tr-TR-EmelNeural` | Általános kérdések |
| Török (Törökország) | `tr-TR` | Férfi | `tr-TR-AhmetNeural` <sup>Új</sup> | Általános kérdések |
| Vietnámi (Vietnám) | `vi-VN` | Nő | `vi-VN-HoaiMyNeural` | Általános kérdések |
| Vietnámi (Vietnám) | `vi-VN` | Férfi | `vi-VN-NamMinhNeural` <sup>Új</sup> | Általános kérdések |

#### <a name="neural-voices-in-preview"></a>Neurális hangok az előzetes verzióban

Az alábbi neurális hangok nyilvános előzetes verzióban érhetők el. 

| Nyelv                         | Területi beállítás  | Nem | Hang neve                             | Stílus támogatása |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaohanNeural` | Általános, több stílus is elérhető [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaomoNeural` | Általános, több szerepkör – lejátszás és [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) elérhető stílusok |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaoruiNeural` | Vezető hang, több stílus érhető el [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-XiaoxuanNeural` | Általános, több szerepkör – lejátszás és [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) elérhető stílusok |
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Férfi   | `zh-CN-YunxiNeural` | Általános, több stílus is elérhető [a SSML használatával](speech-synthesis-markup.md#adjust-speaking-styles) |
| észt (Észtország) | `et-EE` | Nő | `et-EE-AnuNeural` | Általános kérdések |
| észt (Észtország) | `et-EE` | Férfi | `et-EE-KertNeural` <sup>Új</sup> | Általános kérdések |
| Ír (Írország) | `ga-IE` | Nő | `ga-IE-OrlaNeural` | Általános kérdések |
| Ír (Írország) | `ga-IE` | Férfi | `ga-IE-ColmNeural` <sup>Új</sup> | Általános kérdések |
| Lett (Lettország) | `lv-LV` | Nő | `lv-LV-EveritaNeural` | Általános kérdések |
| Lett (Lettország) | `lv-LV` | Férfi | `lv-LV-NilsNeural` <sup>Új</sup> | Általános kérdések |
| Litván (Litvánia) | `lt-LT` | Nő | `lt-LT-OnaNeural` | Általános kérdések |
| Litván (Litvánia) | `lt-LT` | Férfi | `lt-LT-LeonasNeural` <sup>Új</sup> | Általános kérdések |
| Máltai (Málta) | `mt-MT` | Nő | `mt-MT-GraceNeural` | Általános kérdések |
| Máltai (Málta) | `mt-MT` | Férfi | `mt-MT-JosephNeural` <sup>Új</sup> | Általános kérdések |

> [!IMPORTANT]
> A nyilvános előzetes verzióban elérhető hangok csak 3 szolgáltatási régióban érhetők el: USA keleti régiója, Nyugat-Európa és Délkelet-Ázsia.

További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

A következő témakörből megtudhatja, hogyan konfigurálhat és állíthat be neurális hangokat, például a beszéd stílusait: [beszéd szintézisének leíró nyelve](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> A hang a következőre `en-US-JessaNeural` változott: `en-US-AriaNeural` . Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

> [!TIP]
> Továbbra is használhatja a teljes szolgáltatásnév leképezését (például "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)") a beszédfelismerési kérelmekben.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

| Nyelv | Területi beállítás (BCP-47) | Nem | Hang neve |
|--|--|--|--|
| Arab (arab) | `ar-EG` | Nő | `ar-EG-Hoda`|
| Arab (Szaúd-Arábia) | `ar-SA` | Férfi | `ar-SA-Naayf`|
| Bolgár (Bulgária) | `bg-BG` | Férfi | `bg-BG-Ivan`|
| Katalán (Spanyolország) | `ca-ES` | Nő | `ca-ES-HerenaRUS`|
| Kínai (kantoni, hagyományos) | `zh-HK` | Férfi | `zh-HK-Danny`|
| Kínai (kantoni, hagyományos) | `zh-HK` | Nő | `zh-HK-TracyRUS`|
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-HuihuiRUS`|
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Férfi | `zh-CN-Kangkang`|
| Kínai (mandarin, egyszerűsített) | `zh-CN` | Nő | `zh-CN-Yaoyao`|
| Kínai (tajvani mandarin) |  `zh-TW` | Nő | `zh-TW-HanHanRUS`|
| Kínai (tajvani mandarin) |  `zh-TW` | Nő | `zh-TW-Yating`|
| Kínai (tajvani mandarin) |  `zh-TW` | Férfi | `zh-TW-Zhiwei`|
| Horvát (Horvátország) | `hr-HR` | Férfi | `hr-HR-Matej`|
| Cseh (Cseh Köztársaság) | `cs-CZ` | Férfi | `cs-CZ-Jakub`|
| Dán (Dánia) | `da-DK` | Nő | `da-DK-HelleRUS`|
| Holland (Hollandia) | `nl-NL` | Nő | `nl-NL-HannaRUS`|
| Angol (Ausztrália) | `en-AU` | Nő | `en-AU-Catherine`|
| Angol (Ausztrália) | `en-AU` | Nő | `en-AU-HayleyRUS`|
| Angol (Kanada) | `en-CA` | Nő | `en-CA-HeatherRUS`|
| Angol (Kanada) | `en-CA` | Nő | `en-CA-Linda`|
| Angol (India) | `en-IN` | Nő | `en-IN-Heera`|
| Angol (India) | `en-IN` | Nő | `en-IN-PriyaRUS`|
| Angol (India) | `en-IN` | Férfi | `en-IN-Ravi`|
| Angol (Írország) | `en-IE` | Férfi | `en-IE-Sean`|
| angol (Egyesült Királyság) | `en-GB` | Férfi | `en-GB-George`|
| angol (Egyesült Királyság) | `en-GB` | Nő | `en-GB-HazelRUS`|
| angol (Egyesült Királyság) | `en-GB` | Nő | `en-GB-Susan`|
| angol (Egyesült Államok) | `en-US` | Férfi | `en-US-BenjaminRUS`|
| angol (Egyesült Államok) | `en-US` | Férfi | `en-US-GuyRUS`|
| angol (Egyesült Államok) | `en-US` | Nő | `en-US-JessaRUS`|
| angol (Egyesült Államok) | `en-US` | Nő | `en-US-ZiraRUS`|
| Finn (Finnország) | `fi-FI` | Nő | `fi-FI-HeidiRUS`|
| Francia (Kanada) | `fr-CA` | Nő | `fr-CA-Caroline`|
| Francia (Kanada) | `fr-CA` | Nő | `fr-CA-HarmonieRUS`|
| Francia (Franciaország) | `fr-FR` | Nő | `fr-FR-HortenseRUS`|
| Francia (Franciaország) | `fr-FR` | Nő | `fr-FR-Julie`|
| Francia (Franciaország) | `fr-FR` | Férfi | `fr-FR-Paul`|
| Francia (Svájc) | `fr-CH` | Férfi | `fr-CH-Guillaume`|
| Német (Ausztria) | `de-AT` | Férfi | `de-AT-Michael`|
| Német (Németország) | `de-DE` | Nő | `de-DE-HeddaRUS`|
| Német (Németország) | `de-DE` | Férfi | `de-DE-Stefan`|
| Német (Svájc) | `de-CH` | Férfi | `de-CH-Karsten`|
| Görög (Görögország) | `el-GR` | Férfi | `el-GR-Stefanos`|
| Héber (Izrael) | `he-IL` | Férfi | `he-IL-Asaf`|
| Hindi (India) | `hi-IN` | Férfi | `hi-IN-Hemant`|
| Hindi (India) | `hi-IN` | Nő | `hi-IN-Kalpana`|
| Magyar (Magyarország) | `hu-HU` | Férfi | `hu-HU-Szabolcs`|
| Indonéz (Indonézia) | `id-ID` | Férfi | `id-ID-Andika`|
| Olasz (Olaszország) | `it-IT` | Férfi | `it-IT-Cosimo`|
| Olasz (Olaszország) | `it-IT` | Nő | `it-IT-LuciaRUS`|
| Japán (Japán) | `ja-JP` | Nő | `ja-JP-Ayumi`|
| Japán (Japán) | `ja-JP` | Nő | `ja-JP-HarukaRUS`|
| Japán (Japán) | `ja-JP` | Férfi | `ja-JP-Ichiro`|
| Koreai (Dél-Korea) | `ko-KR` | Nő | `ko-KR-HeamiRUS`|
| Maláj (Malajzia) | `ms-MY` | Férfi | `ms-MY-Rizwan`|
| norvég (bokmål, Norvégia) | `nb-NO` | Nő | `nb-NO-HuldaRUS`|
| Lengyel (Lengyelország) | `pl-PL` | Nő | `pl-PL-PaulinaRUS`|
| Portugál (Brazília) | `pt-BR` | Férfi | `pt-BR-Daniel`|
| Portugál (Brazília) | `pt-BR` | Nő | `pt-BR-HeloisaRUS`|
| Portugál (Portugália) | `pt-PT` | Nő | `pt-PT-HeliaRUS`|
| Román (Románia) | `ro-RO` | Férfi | `ro-RO-Andrei`|
| Orosz (Oroszország) | `ru-RU` | Nő | `ru-RU-EkaterinaRUS`|
| Orosz (Oroszország) | `ru-RU` | Nő | `ru-RU-Irina`|
| Orosz (Oroszország) | `ru-RU` | Férfi | `ru-RU-Pavel`|
| Szlovák (Szlovákia) | `sk-SK` | Férfi | `sk-SK-Filip`|
| Szlovén (Szlovénia) | `sl-SI` | Férfi | `sl-SI-Lado`|
| Spanyol (Mexikó) | `es-MX` | Nő | `es-MX-HildaRUS`|
| Spanyol (Mexikó) | `es-MX` | Férfi | `es-MX-Raul`|
| Spanyol (Spanyolország) | `es-ES` | Nő | `es-ES-HelenaRUS`|
| Spanyol (Spanyolország) | `es-ES` | Nő | `es-ES-Laura`|
| Spanyol (Spanyolország) | `es-ES` | Férfi | `es-ES-Pablo`|
| Svéd (Svédország) | `sv-SE` | Nő | `sv-SE-HedvigRUS`|
| Tamil (India) | `ta-IN` | Férfi | `ta-IN-Valluvar`|
| Telugu (India) | `te-IN` | Nő | `te-IN-Chitra`|
| Thai (Thaiföld) | `th-TH` | Férfi | `th-TH-Pattara`|
| Török (Törökország) | `tr-TR` | Nő | `tr-TR-SedaRUS`|
| Vietnámi (Vietnám) | `vi-VN` | Férfi | `vi-VN-An` |

> [!IMPORTANT]
> A hang a következőre `en-US-Jessa` változott: `en-US-Aria` . Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

> [!TIP]
> Továbbra is használhatja a teljes szolgáltatásnév leképezését (például "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)") a beszédfelismerési kérelmekben.

### <a name="customization"></a>Testreszabás

A,,,,,,, és a hangalapú Testreszabás elérhető `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adatként angol nyelven beszél angolul, válassza a lehetőséget `en-GB` .

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a Chinese-English a kétnyelvű nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés az összes területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, kivéve a `en-US` és az, `zh-CN` ahol bármilyen méretű betanítási adatmennyiséget használhat.

## <a name="speech-translation"></a>Beszédfordítás

A **Speech Translation** API különböző nyelveket támogat a beszédfelismerés és a beszéd – szöveg fordításához. A forrás nyelvének mindig a beszéd-szöveg nyelvi táblából kell származnia. Az elérhető célnyelv attól függnek, hogy a fordítás célja beszéd vagy szöveg. A bejövő beszédet több mint [60 nyelvre](https://www.microsoft.com/translator/business/languages/)lefordíthatja. A [beszédfelismeréshez](language-support.md#text-languages)a nyelvek egy részhalmaza érhető el.

### <a name="text-languages"></a>Szöveg nyelve

| Szöveg nyelve           | Nyelvkód |
|:------------------------|:-------------:|
| búr               | `af`          |
| Arab                  | `ar`          |
| Bangla                  | `bn`          |
| Bosnyák (latin betűs)         | `bs`          |
| Bolgár               | `bg`          |
| Kantoni (hagyományos) | `yue`         |
| Katalán                 | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`     |
| kínai (hagyományos)     | `zh-Hant`     |
| Horvát                | `hr`          |
| Cseh                   | `cs`          |
| Dán                  | `da`          |
| Holland                   | `nl`          |
| Angol                 | `en`          |
| Észt                | `et`          |
| Fidzsi                  | `fj`          |
| Filipino                | `fil`         |
| Finn                 | `fi`          |
| Francia                  | `fr`          |
| Német                  | `de`          |
| Görög                   | `el`          |
| gudzsaráti                | `gu`          |
| Haiti kreol          | `ht`          |
| héber                  | `he`          |
| Hindi                   | `hi`          |
| Hmong DAW               | `mww`         |
| Magyar               | `hu`          |
| Indonéz              | `id`          |
| Ír                   | `ga`          |
| Olasz                 | `it`          |
| Japán                | `ja`          |
| kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Koreai                  | `ko`          |
| Lett                 | `lv`          |
| Litván              | `lt`          |
| Malgas                | `mg`          |
| Maláj                   | `ms`          |
| malajálam               | `ml`          |
| Máltai                 | `mt`          |
| maori                   | `mi`          |
| marathi                 | `mr`          |
| Norvég               | `nb`          |
| perzsa                 | `fa`          |
| Lengyel                  | `pl`          |
| Portugál (Brazília)     | `pt-br`       |
| Portugál (Portugália)   | `pt-pt`       |
| pandzsábi                 | `pa`          |
| Queretaro otomi         | `otq`         |
| Román                | `ro`          |
| Orosz                 | `ru`          |
| Szamoai                  | `sm`          |
| Szerb (cirill betűs)      | `sr-Cyrl`     |
| Szerb (latin betűs)         | `sr-Latn`     |
| Szlovák                  | `sk`          |
| Szlovén               | `sl`          |
| Spanyol                 | `es`          |
| Svéd                 | `sv`          |
| Tahitian                | `ty`          |
| tamil                   | `ta`          |
| telugu                  | `te`          |
| Thai                    | `th`          |
| Tongai                  | `to`          |
| Török                 | `tr`          |
| Ukrán               | `uk`          |
| urdu                    | `ur`          |
| Vietnámi              | `vi`          |
| walesi                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="speaker-recognition"></a>Speaker Recognition

A különböző Speaker Recognition API-khoz támogatott nyelveket a következő táblázat tartalmazza. További információ a Speaker Recognitionról: [Áttekintés](speaker-recognition-overview.md) .

| Nyelv | Területi beállítás (BCP-47) | Szövegtől függő ellenőrzés | Szövegtől független ellenőrzés | Szöveg – független azonosítás |
|----|----|----|----|----|
|Angol (US)  |  en-US  |  igen  |  igen  |  igen |
|Kínai (mandarin, egyszerűsített) | zh-CN     |     n/a |     igen |     igen|
|Angol (Ausztrália)     | EN-AU     | n/a     | igen     | igen|
|Angol (Kanada)     | en-CA     | n/a |     igen |     igen|
|Angol (Egyesült Királyság)     | en-GB     | n/a     | igen     | igen|
|Francia (Kanada)     | fr – CA     | n/a     | igen |     igen|
|Francia (Franciaország)     | fr-FR     | n/a     | igen     | igen|
|Német (Németország)     | de-DE     | n/a     | igen     | igen|
|Olasz | it-IT     |     n/a     | igen |     igen|
|Japán     | ja-JP | n/a     | igen     | igen|
|Portugál (Brazília) | pt-BR |     n/a |     igen |     igen|
|Spanyol (Mexikó)     | es-MX     | n/a |     igen |     igen|
|Spanyol (Spanyolország)     | es-ES | n/a     | igen |     igen|

## <a name="next-steps"></a>Következő lépések

* [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
* [Lásd: beszéd felismerése a C-ben #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
