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
ms.custom: seodec18
ms.openlocfilehash: e0d6694d44fdac9958acdf70be067348fadde57f
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461240"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Beszédfelismerési szolgáltatás nyelvi és hangalapú támogatása

A nyelvi támogatás a beszédfelismerési szolgáltatás működésének függvényében változik. Az alábbi táblázatok összefoglalják a [beszédfelismerési](#speech-to-text)és a [szöveges](#text-to-speech) [, valamint a beszédfelismerési](#speech-translation) szolgáltatás nyelvének támogatását.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). 

A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a **hang-és emberi címkével ellátott átiratok** vagy a **kapcsolódó szövegek feltöltésével: mondatok**. A testreszabással kapcsolatos további tudnivalókért tekintse meg [a Custom Speech első lépéseivel foglalkozó](how-to-custom-speech.md)témakört.

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Területi beállítás  | Nyelv                          | Testreszabások                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | Arab (Egyesült Arab Emírségek)     | Nyelvi modell                                    |
| `ar-BH` | Arab (Bahrein), modern standard | Nyelvi modell                                    |
| `ar-EG` | Arab (Egyiptom)                    | Nyelvi modell                                    |
| `ar-IQ` | Arab (Irak)                     | Nyelvi modell                                    |
| `ar-JO` | Arab (Jordánia)                   | Nyelvi modell                                    |
| `ar-KW` | Arab (Kuvait)                   | Nyelvi modell                                    |
| `ar-LB` | Arab (Libanon)                  | Nyelvi modell                                    |
| `ar-OM` | Arab (Omán)                     | Nyelvi modell                                    |
| `ar-QA` | Arab (Katar)                    | Nyelvi modell                                    |
| `ar-SA` | Arab (Szaúd-Arábia)             | Nyelvi modell                                    |
| `ar-SY` | Arab (Szíria)                    | Nyelvi modell                                    |
| `bg-BG` | Bolgár (Bulgária)              | Nyelvi modell                                    |
| `ca-ES` | Katalán (Spanyolország)                   | Nyelvi modell                                    |
| `cs-CZ` | Cseh (Cseh Köztársaság)            | Nyelvi modell                                    | 
| `da-DK` | Dán (Dánia)                  | Nyelvi modell                                    |
| `de-DE` | Német (Németország)                  | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `el-GR` | Görög (Görögország)                    | Nyelvi modell                                    |
| `en-AU` | Angol (Ausztrália)               | Akusztikai modell<br>Nyelvi modell                  |
| `en-CA` | Angol (Kanada)                  | Akusztikai modell<br>Nyelvi modell                  |
| `en-GB` | angol (Egyesült Királyság)          | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-HK` | Angol (Hongkong)               | Nyelvi modell                                    | 
| `en-IE` | Angol (Írország)                 | Nyelvi modell                                    | 
| `en-IN` | Angol (India)                   | Akusztikai modell<br>Nyelvi modell                  |
| `en-NZ` | Angol (Új-Zéland)             | Akusztikai modell<br>Nyelvi modell                  |
| `en-PH` | Angol (Fülöp-szigetek)             | Nyelvi modell                                    | 
| `en-SG` | Angol (Szingapúr)               | Nyelvi modell                                    | 
| `en-US` | angol (Egyesült Államok)           | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-ZA` | Angol (Dél-Afrika)            | Nyelvi modell                                    | 
| `es-AR` | Spanyol (Argentína)               | Nyelvi modell                                    | 
| `es-BO` | Spanyol (Bolívia)                 | Nyelvi modell                                    | 
| `es-CL` | Spanyol (Chile)                   | Nyelvi modell                                    | 
| `es-CO` | Spanyol (Kolumbia)                | Nyelvi modell                                    | 
| `es-CR` | Spanyol (Costa Rica)              | Nyelvi modell                                    | 
| `es-CU` | Spanyol (Kuba)                    | Nyelvi modell                                    | 
| `es-DO` | Spanyol (Dominikai Köztársaság)      | Nyelvi modell                                    | 
| `es-EC` | Spanyol (Ecuador)                 | Nyelvi modell                                    | 
| `es-ES` | Spanyol (Spanyolország)                   | Akusztikai modell<br>Nyelvi modell                  |
| `es-GT` | Spanyol (Guatemala)               | Nyelvi modell                                    | 
| `es-HN` | Spanyol (Honduras)                | Nyelvi modell                                    | 
| `es-MX` | Spanyol (Mexikó)                  | Akusztikai modell<br>Nyelvi modell                  |
| `es-NI` | Spanyol (Nicaragua)               | Nyelvi modell                                    | 
| `es-PA` | Spanyol (Panama)                  | Nyelvi modell                                    | 
| `es-PE` | Spanyol (perui)                    | Nyelvi modell                                    | 
| `es-PR` | Spanyol (Puerto Rico)             | Nyelvi modell                                    | 
| `es-PY` | Spanyol (Paraguay)                | Nyelvi modell                                    | 
| `es-SV` | Spanyol (Salvador)             | Nyelvi modell                                    | 
| `es-US` | Spanyol (USA)                     | Nyelvi modell                                    | 
| `es-UY` | Spanyol (Uruguay)                 | Nyelvi modell                                    | 
| `es-VE` | Spanyol (Venezuela)               | Nyelvi modell                                    |
| `et-EE` | Észt (Észtország)                 | Nyelvi modell                                    | 
| `fi-FI` | Finn (Finnország)                 | Nyelvi modell                                    |
| `fr-CA` | Francia (Kanada)                   | Akusztikai modell<br>Nyelvi modell                  |
| `fr-FR` | Francia (Franciaország)                   | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `ga-IE` | Ír (Írország)                    | Nyelvi modell                                    |
| `gu-IN` | Gudzsaráti (indiai)                 | Nyelvi modell                                    |
| `hi-IN` | Hindi (India)                     | Akusztikai modell<br>Nyelvi modell                  |
| `hr-HR` | Horvát (Horvátország)                | Nyelvi modell                                    |
| `hu-HU` | Magyar (Magyarország)               | Nyelvi modell                                    | 
| `it-IT` | Olasz (Olaszország)                   | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `ja-JP` | Japán (Japán)                  | Nyelvi modell                                    |
| `ko-KR` | Koreai (Dél-Korea)                    | Nyelvi modell                                    |
| `lt-LT` | Litván (Litvánia)            | Nyelvi modell                                    |
| `lv-LV` | Lett (Lettország)                  | Nyelvi modell                                    |
| `mr-IN` | Marathi (India)                   | Nyelvi modell                                    |
| `mt-MT` | Máltai (Málta)                    | Nyelvi modell                                    |
| `nb-NO` | Norvég (nyelven) (Norvégia)       | Nyelvi modell                                    |
| `nl-NL` | Holland (Hollandia)               | Nyelvi modell                                    |
| `pl-PL` | Lengyel (Lengyelország)                   | Nyelvi modell                                    |
| `pt-BR` | Portugál (Brazília)               | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `pt-PT` | Portugál (Portugália)             | Nyelvi modell                                    |
| `ro-RO` | Román (Románia)                | Nyelvi modell                                    |
| `ru-RU` | Orosz (Oroszország)                  | Akusztikai modell<br>Nyelvi modell                  |
| `sk-SK` | Szlovák (Szlovákia)                 | Nyelvi modell                                    |
| `sl-SI` | Szlovén (Szlovénia)              | Nyelvi modell                                    |
| `sv-SE` | Svéd (Svédország)                  | Nyelvi modell                                    |
| `ta-IN` | Tamil (India)                     | Nyelvi modell                                    |
| `te-IN` | Telugu (India)                    | Nyelvi modell                                    |
| `th-TH` | Thai (Thaiföld)                   | Nyelvi modell                                    |
| `tr-TR` | Török (Törökország)                  | Nyelvi modell                                    |
| `zh-CN` | Kínai (mandarin, egyszerűsített)    | Akusztikai modell<br>Nyelvi modell                  |
| `zh-HK` | Kínai (kantoni, hagyományos)  | Nyelvi modell                                    |
| `zh-TW` | Kínai (tajvani mandarin)      | Nyelvi modell                                    |

## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API-k egyaránt támogatják ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít. Az egyes régiókban vagy végpontokban támogatott nyelvek és hangok teljes listáját a [Voices/List API](rest-text-to-speech.md#get-a-list-of-voices)-n keresztül is elérheti. 

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

|Területi beállítás  | Nyelv            | Nem | Hang neve | Stílus támogatása |
|--|--|--|--|--|
| `ar-EG` | Arab (Egyiptom) | Nő | `ar-EG-SalmaNeural` | Általános kérdések |
| `ar-SA` | Arab (Szaúd-Arábiai) | Nő | `ar-SA-ZariyahNeural` | Általános kérdések |
| `bg-BG` <sup>Új</sup> | Bolgár (duzzadt) | Nő | `bg-BG-KalinaNeural` | Általános kérdések |
| `ca-ES` | Katalán (Spanyolország) | Nő | `ca-ES-AlbaNeural` | Általános kérdések |
| `cs-CZ` <sup>Új</sup> | Cseh (Cseh)  | Nő | `cs-CZ-VlastaNeural` | Általános kérdések |
| `da-DK` | Dán (Dánia) | Nő | `da-DK-ChristelNeural` | Általános kérdések |
| `de-AT` <sup>Új</sup> | Német (Ausztria) | Nő | `de-AT-IngridNeural` | Általános kérdések |
| `de-CH` <sup>Új</sup> | Német (Svájc) | Nő | `de-CH-LeniNeural` | Általános kérdések |
| `de-DE` | Német (németországi) | Nő | `de-DE-KatjaNeural` | Általános kérdések |
| `de-DE` <sup>Új</sup> | Német (Németország) | Férfi | `de-DE-ConradNeural` | Általános kérdések |
| `el-GR` <sup>Új</sup> | Görög (Görögország) | Nő | `el-GR-AthinaNeural` | Általános kérdések |
| `en-AU` | Angol (ausztráliai) | Nő | `en-AU-NatashaNeural` | Általános kérdések |
| `en-AU` <sup>Új</sup> | Ausztráliai (ausztráliai) | Férfi | `en-AU-WilliamNeural` | Általános kérdések |
| `en-CA` | Angol (Kanada) | Nő | `en-CA-ClaraNeural` | Általános kérdések |
| `en-GB` | Angol (Egyesült Királyság) | Nő | `en-GB-LibbyNeural` | Általános kérdések |
| `en-GB` | Angol (Egyesült Királyság) | Nő | `en-GB-MiaNeural` | Általános kérdések |
| `en-GB` <sup>Új</sup> | angol (Egyesült Királyság) | Férfi | `En-GB-RyanNeural` | Általános kérdések |
| `en-IE` <sup>Új</sup> | Ír angol (Írország) | Nő | `en-IE-EmilyNeural` | Általános kérdések |
| `en-IN` | Angol (India) | Nő | `en-IN-NeerjaNeural` | Általános kérdések |
| `en-US` | Angol (Egyesült Államok) | Nő | `en-US-AriaNeural` | Általános, több hangstílus érhető el |
| `en-US` | Angol (Egyesült Államok) | Férfi | `en-US-GuyNeural` | Általános kérdések |
| `en-US` <sup>Új</sup> | Angol (Egyesült Államok) | Nő | `en-US-JennyNeural` | Általános, több hangstílus érhető el |
| `es-ES` | Spanyol (Spanyolország) | Nő | `es-ES-ElviraNeural` | Általános kérdések |
| `es-ES` <sup>Új</sup> | Spanyol (Spanyolország) | Férfi | `es-ES-AlvaroNeural` | Általános kérdések |
| `es-MX` | Spanyol (Mexikó) | Nő | `es-MX-DaliaNeural` | Általános kérdések |
| `es-MX` <sup>Új</sup> | Spanyol (Mexikó) | Férfi | `es-MX-JorgeNeural` | Általános kérdések |
| `fi-FI` | Finn (Finnország) | Nő | `fi-FI-NooraNeural` | Általános kérdések |
| `fr-CA` | Francia (Kanada) | Nő | `fr-CA-SylvieNeural` | Általános kérdések |
| `fr-CA` <sup>Új</sup> | Francia (Kanada) | Férfi | `fr-CA-JeanNeural` | Általános kérdések |
| `fr-CH` <sup>Új</sup> | Francia (Svájc) | Nő | `fr-CH-ArianeNeural` | Általános kérdések |
| `fr-FR` | Francia (franciaországi) | Nő | `fr-FR-DeniseNeural` | Általános kérdések |
| `fr-FR` <sup>Új</sup> | Francia (Franciaország) | Férfi | `fr-FR-HenriNeural` | Általános kérdések |
| `he-IL` <sup>Új</sup> | Héber (Isareal) | Nő | `he-IL-HilaNeural` | Általános kérdések |
| `hi-IN` | Hindi (India) | Nő | `hi-IN-SwaraNeural` | Általános kérdések |
| `hr-HR` <sup>Új</sup> | Horvát (Horvátország) | Nő | `hr-HR-GabrijelaNeural` | Általános kérdések |
| `hu-HU` <sup>Új</sup> | Magyar (Magyarország) | Nő | `hu-HU-NoemiNeural` | Általános kérdések |
| `id-ID` <sup>Új</sup> | Bahasa indonéz (Indonézia) | Férfi | `id-ID-ArdiNeural` | Általános kérdések |
| `it-IT` | Olasz (Olaszország) | Nő | `it-IT-ElsaNeural` | Általános kérdések |
| `it-IT` <sup>Új</sup> | Olasz (Olaszország) | Nő | `it-IT-IsabellaNeural` | Általános kérdések |
| `it-IT` <sup>Új</sup> | Olasz (Olaszország) | Férfi | `it-IT-DiegoNeural` | Általános kérdések |
| `ja-JP` | Japán (Japán) | Nő | `ja-JP-NanamiNeural` | Általános kérdések |
| `ja-JP` <sup>Új</sup> | Japán (Japán) | Férfi | `ja-JP-KeitaNeural` | Általános kérdések |
| `ko-KR` | Koreai (Korea) | Nő | `ko-KR-SunHiNeural` | Általános kérdések |
| `ko-KR` <sup>Új</sup> | Koreai (Dél-Korea) | Férfi | `ko-KR-InJoonNeural` | Általános kérdések |
| `ms-MY` <sup>Új</sup> | Maláj (Malajzia) | Nő | `ms-MY-YasminNeural` | Általános kérdések |
| `nb-NO` | Norvég, (Norvégia) | Nő | `nb-NO-IselinNeural` | Általános kérdések |
| `nl-NL` | Holland (Hollandia) | Nő | `nl-NL-ColetteNeural` | Általános kérdések |
| `pl-PL` | Lengyel (Lengyelország) | Nő | `pl-PL-ZofiaNeural` | Általános kérdések |
| `pt-BR` | Portugál (brazíliai) | Nő | `pt-BR-FranciscaNeural` | Általános, több hangstílus érhető el |
| `pt-BR` <sup>Új</sup> | Brazíliai Portugál (brazíliai) | Férfi | `pt-BR-AntonioNeural` | Általános kérdések |
| `pt-PT` | Portugál (portugáliai) | Nő | `pt-PT-FernandaNeural` | Általános kérdések |
| `ro-RO` <sup>Új</sup> | Román (Románia) | Nő | `ro-RO-AlinaNeural` | Általános kérdések |
| `ru-RU` | Orosz (Oroszország) | Nő | `ru-RU-DariyaNeural` | Általános kérdések |
| `sk-SK` <sup>Új</sup> | Szlovák (Szlovákia) | Nő | `sk-SK-ViktoriaNeural` | Általános kérdések |
| `sl-SI` <sup>Új</sup> | Szlovén (Szlovénia) | Nő | `sl-SI-PetraNeural` | Általános kérdések |
| `sv-SE` | Svéd (svédországi) | Nő | `sv-SE-HilleviNeural` | Általános kérdések |
| `ta-IN` <sup>Új</sup> | Tamil (India) | Nő | `ta-IN-PallaviNeural` | Általános kérdések |
| `te-IN` <sup>Új</sup> | Telugu (India) | Nő | `te-IN-ShrutiNeural` | Általános kérdések |
| `th-TH` | Thai (Thaiföld) | Nő | `th-TH-AcharaNeural` | Általános kérdések |
| `th-TH` <sup>Új</sup> | Thai (Thaiföld) | Nő | `th-TH-PremwadeeNeural` | Általános kérdések |
| `tr-TR` | Török (Törökország) | Nő | `tr-TR-EmelNeural` | Általános kérdések |
| `vi-VN` <sup>Új</sup> | Vietnámi (Vietnám) | Nő | `vi-VN-HoaiMyNeural` | Általános kérdések |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Nő | `zh-CN-XiaoxiaoNeural` | Általános, több hangstílus érhető el |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Nő | `zh-CN-XiaoyouNeural` | A szövegegység elbeszéléséhez optimalizált, gyerek hang |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Férfi | `zh-CN-YunyangNeural` | A hírek olvasására optimalizált, több hangstílus érhető el |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Férfi | `zh-CN-YunyeNeural` | A történet elbeszéléséhez optimalizált |
| `zh-HK` | Kantoni (hagyományos kínai, Hongkong) | Nő | `zh-HK-HiuGaaiNeural` | Általános kérdések |
| `zh-TW` | Mandarin (hagyományos kínai, Tajvan) | Nő | `zh-TW-HsiaoYuNeural` | Általános kérdések |


> [!IMPORTANT]
> A hang a következőre `en-US-JessaNeural` változott: `en-US-AriaNeural` . Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

Ha szeretné megtudni, hogyan konfigurálhatja és módosíthatja a neurális hangokat, tekintse meg a [Speech szintézis Markup Language](speech-synthesis-markup.md#adjust-speaking-styles)című témakört.

> [!TIP]
> Továbbra is használhatja a teljes szolgáltatásnév leképezését (például "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)") a beszédfelismerési kérelmekben.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

| Területi beállítás | Nyelv | Nem | Hang neve |
|--|--|--|--|
| `ar-EG` | Arab (arab) | Nő | `ar-EG-Hoda` |
| `ar-SA` | Arab (Szaúd-Arábia) | Férfi | `ar-SA-Naayf` |
| `bg-BG` | Bolgár (Bulgária) | Férfi | `bg-BG-Ivan` |
| `ca-ES` | Katalán (Spanyolország) | Nő | `ca-ES-HerenaRUS` |
| `cs-CZ` | Cseh (Cseh Köztársaság) | Férfi | `cs-CZ-Jakub` |
| `da-DK` | Dán (Dánia) | Nő | `da-DK-HelleRUS` |
| `de-AT` | Német (Ausztria) | Férfi | `de-AT-Michael` |
| `de-CH` | Német (Svájc) | Férfi | `de-CH-Karsten` |
| `de-DE` | Német (Németország) | Nő | `de-DE-HeddaRUS` |
| `de-DE` | Német (Németország) | Férfi | `de-DE-Stefan` |
| `el-GR` | Görög (Görögország) | Férfi | `el-GR-Stefanos` |
| `en-AU` | Angol (Ausztrália) | Nő | `en-AU-Catherine` |
| `en-AU` | Angol (Ausztrália) | Nő | `en-AU-HayleyRUS` |
| `en-CA` | Angol (Kanada) | Nő | `en-CA-HeatherRUS` |
| `en-CA` | Angol (Kanada) | Nő | `en-CA-Linda` |
| `en-GB` | angol (Egyesült Királyság) | Férfi | `en-GB-George` |
| `en-GB` | angol (Egyesült Királyság) | Nő | `en-GB-HazelRUS` |
| `en-GB` | angol (Egyesült Királyság) | Nő | `en-GB-Susan` |
| `en-IE` | Angol (Írország) | Férfi | `en-IE-Sean` |
| `en-IN` | Angol (India) | Nő | `en-IN-Heera` |
| `en-IN` | Angol (India) | Nő | `en-IN-PriyaRUS` |
| `en-IN` | Angol (India) | Férfi | `en-IN-Ravi` |
| `en-US` | angol (Egyesült Államok) | Férfi | `en-US-BenjaminRUS` |
| `en-US` | angol (Egyesült Államok) | Férfi | `en-US-GuyRUS` |
| `en-US` | angol (Egyesült Államok) | Nő | `en-US-JessaRUS` |
| `en-US` | angol (Egyesült Államok) | Nő | `en-US-ZiraRUS` |
| `es-ES` | Spanyol (Spanyolország) | Nő | `es-ES-HelenaRUS` |
| `es-ES` | Spanyol (Spanyolország) | Nő | `es-ES-Laura` |
| `es-ES` | Spanyol (Spanyolország) | Férfi | `es-ES-Pablo` |
| `es-MX` | Spanyol (Mexikó) | Nő | `es-MX-HildaRUS` |
| `es-MX` | Spanyol (Mexikó) | Férfi | `es-MX-Raul` |
| `fi-FI` | Finn (Finnország) | Nő | `fi-FI-HeidiRUS` |
| `fr-CA` | Francia (Kanada) | Nő | `fr-CA-Caroline` |
| `fr-CA` | Francia (Kanada) | Nő | `fr-CA-HarmonieRUS` |
| `fr-CH` | Francia (Svájc) | Férfi | `fr-CH-Guillaume` |
| `fr-FR` | Francia (Franciaország) | Nő | `fr-FR-HortenseRUS` |
| `fr-FR` | Francia (Franciaország) | Nő | `fr-FR-Julie` |
| `fr-FR` | Francia (Franciaország) | Férfi | `fr-FR-Paul` |
| `he-IL` | Héber (Izrael) | Férfi | `he-IL-Asaf` |
| `hi-IN` | Hindi (India) | Férfi | `hi-IN-Hemant` |
| `hi-IN` | Hindi (India) | Nő | `hi-IN-Kalpana` |
| `hr-HR` | Horvát (Horvátország) | Férfi | `hr-HR-Matej` |
| `hu-HU` | Magyar (Magyarország) | Férfi | `hu-HU-Szabolcs` |
| `id-ID` | Indonéz (Indonézia) | Férfi | `id-ID-Andika` |
| `it-IT` | Olasz (Olaszország) | Férfi | `it-IT-Cosimo` |
| `it-IT` | Olasz (Olaszország) | Nő | `it-IT-LuciaRUS` |
| `ja-JP` | Japán (Japán) | Nő | `ja-JP-Ayumi` |
| `ja-JP` | Japán (Japán) | Nő | `ja-JP-HarukaRUS` |
| `ja-JP` | Japán (Japán) | Férfi | `ja-JP-Ichiro` |
| `ko-KR` | Koreai (Dél-Korea) | Nő | `ko-KR-HeamiRUS` |
| `ms-MY` | Maláj (Malajzia) | Férfi | `ms-MY-Rizwan` |
| `nb-NO` | Norvég, (Norvégia) | Nő | `nb-NO-HuldaRUS` |
| `nl-NL` | Holland (Hollandia) | Nő | `nl-NL-HannaRUS` |
| `pl-PL` | Lengyel (Lengyelország) | Nő | `pl-PL-PaulinaRUS` |
| `pt-BR` | Portugál (Brazília) | Férfi | `pt-BR-Daniel` |
| `pt-BR` | Portugál (Brazília) | Nő | `pt-BR-HeloisaRUS` |
| `pt-PT` | Portugál (Portugália) | Nő | `pt-PT-HeliaRUS` |
| `ro-RO` | Román (Románia) | Férfi | `ro-RO-Andrei` |
| `ru-RU` | Orosz (Oroszország) | Nő | `ru-RU-EkaterinaRUS` |
| `ru-RU` | Orosz (Oroszország) | Nő | `ru-RU-Irina` |
| `ru-RU` | Orosz (Oroszország) | Férfi | `ru-RU-Pavel` |
| `sk-SK` | Szlovák (Szlovákia) | Férfi | `sk-SK-Filip` |
| `sl-SI` | Szlovén (Szlovénia) | Férfi | `sl-SI-Lado` |
| `sv-SE` | Svéd (Svédország) | Nő | `sv-SE-HedvigRUS` |
| `ta-IN` | Tamil (India) | Férfi | `ta-IN-Valluvar` |
| `te-IN` | Telugu (India) | Nő | `te-IN-Chitra` |
| `th-TH` | Thai (Thaiföld) | Férfi | `th-TH-Pattara` |
| `tr-TR` | Török (Törökország) | Nő | `tr-TR-SedaRUS` |
| `vi-VN` | Vietnámi (Vietnám) | Férfi | `vi-VN-An` |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Nő | `zh-CN-HuihuiRUS` |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Férfi | `zh-CN-Kangkang` |
| `zh-CN` | Mandarin (egyszerűsített kínai, Kína) | Nő | `zh-CN-Yaoyao` |
| `zh-HK` | Kantoni (hagyományos kínai, Hongkong) | Férfi | `zh-HK-Danny` |
| `zh-HK` | Kantoni (hagyományos kínai, Hongkong) | Nő | `zh-HK-TracyRUS` |
| `zh-TW` | Mandarin (hagyományos kínai, Tajvan) | Nő | `zh-TW-HanHanRUS` |
| `zh-TW` | Mandarin (hagyományos kínai, Tajvan) | Nő | `zh-TW-Yating` |
| `zh-TW` | Mandarin (hagyományos kínai, Tajvan) | Férfi | `zh-TW-Zhiwei` |

> [!IMPORTANT]
> A hang a következőre `en-US-Jessa` változott: `en-US-Aria` . Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

> [!TIP]
> Továbbra is használhatja a teljes szolgáltatásnév leképezését (például "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)") a beszédfelismerési kérelmekben.

### <a name="customization"></a>Testreszabás

A,,,,,,, és a hangalapú Testreszabás elérhető `de-DE` `en-GB` `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adatként angol nyelven beszél angolul, válassza a lehetőséget `en-GB` .

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés az összes területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, kivéve a `en-US` és az, `zh-CN` ahol bármilyen méretű betanítási adatmennyiséget használhat.

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
| Héber                  | `he`          |
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

| Területi beállítás | Nyelv | Szövegtől függő ellenőrzés | Szövegtől független ellenőrzés | Szöveg – független azonosítás |
|----|----|----|----|----|
| en-US | Angol (US) | igen | igen | igen |
|zh-CN    |Kínai (mandarin, egyszerűsített)|    n.a.|    igen|    igen|
|de-DE    |Német (Németország)    |n.a.    |igen    |igen|
|en-GB    |Angol (Egyesült Királyság)    |n.a.    |igen    |igen|
|fr-FR    |Francia (Franciaország)    |n.a.    |igen    |igen|
|EN-AU    |Angol (Ausztrália)    |n.a.    |igen    |igen|
|en-CA    |Angol (Kanada)    |n.a.|    igen|    igen|
|fr – CA    |Francia (Kanada)    |n.a.    |igen|    igen|
|it-IT    |Olasz|    n.a.    |igen|    igen|
|es-ES|    Spanyol (Spanyolország)    |n.a.    |igen|    igen|
|es-MX    |Spanyol (Mexikó)    |n.a.|    igen|    igen|
|ja-JP|    Japán    |n.a.    |igen    |igen|
|pt-BR|    Portugál (Brazília)|    n.a.|    igen|    igen|

## <a name="next-steps"></a>Következő lépések

* [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
* [Lásd: beszéd felismerése a C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
