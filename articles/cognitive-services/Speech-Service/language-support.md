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
ms.openlocfilehash: 9a1745aa0e78dafca70ea90d4a5ac92f340312b3
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706934"
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
| `ar-IL` | Arab (Izrael)                   | No                                                |
| `ar-IQ` | Arab (Irak)                     | Nyelvi modell                                    |
| `ar-JO` | Arab (Jordánia)                   | Nyelvi modell                                    |
| `ar-KW` | Arab (Kuvait)                   | Nyelvi modell                                    |
| `ar-LB` | Arab (Libanon)                  | Nyelvi modell                                    |
| `ar-OM` | Arab (Omán)                     | Nyelvi modell                                    |
| `ar-PS` | Arab (palesztin állam)       | No                                                |
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
| `ar-EG` | Arab (Egyiptom)                  | Nő | `ar-EG-SalmaNeural`      | Általános kérdések |
| `ar-SA` | Arab (Szaúd-Arábia)           | Nő | `ar-SA-ZariyahNeural`    | Általános kérdések |
| `ca-ES` | Katalán (Spanyolország)                 | Nő | `ca-ES-AlbaNeural`       | Általános kérdések |
| `da-DK` | Dán (Dánia)                | Nő | `da-DK-ChristelNeural`   | Általános kérdések |
| `de-DE` | Német (Németország)                | Nő | `de-DE-KatjaNeural`      | Általános kérdések |
| `en-AU` | Angol (Ausztrália)             | Nő | `en-AU-NatashaNeural`    | Általános kérdések |
| `en-CA` | Angol (Kanada)                | Nő | `en-CA-ClaraNeural`      | Általános kérdések |
| `en-GB` | Angol (Egyesült Királyság)                    | Nő | `en-GB-LibbyNeural`      | Általános kérdések |
|         |                                 | Nő | `en-GB-MiaNeural`        | Általános kérdések |
| `en-IN` | Angol (India)                 | Nő | `en-IN-NeerjaNeural`     | Általános kérdések |
| `en-US` | Angol (US)                    | Nő | `en-US-AriaNeural`       | Általános, több hangstílus érhető el |
|         |                                 | Férfi   | `en-US-GuyNeural`        | Általános kérdések |
| `es-ES` | Spanyol (Spanyolország)                 | Nő | `es-ES-ElviraNeural`     | Általános kérdések |
| `es-MX` | Spanyol (Mexikó)                | Nő | `es-MX-DaliaNeural`      | Általános kérdések |
| `fi-FI` | Finn (Finnország)               | Nő | `fi-FI-NooraNeural`      | Általános kérdések |
| `fr-CA` | Francia (Kanada)                 | Nő | `fr-CA-SylvieNeural`     | Általános kérdések |
| `fr-FR` | Francia (Franciaország)                 | Nő | `fr-FR-DeniseNeural`     | Általános kérdések |
| `hi-IN` | Hindi (India)                   | Nő | `hi-IN-SwaraNeural`      | Általános kérdések |
| `it-IT` | Olasz (Olaszország)                 | Nő | `it-IT-ElsaNeural`       | Általános kérdések |
| `ja-JP` | Japán                        | Nő | `ja-JP-NanamiNeural`     | Általános kérdések |
| `ko-KR` | Koreai                          | Nő | `ko-KR-SunHiNeural`      | Általános kérdések |
| `nb-NO` | Norvég                       | Nő | `nb-NO-IselinNeural`     | Általános kérdések |
| `nl-NL` | Holland (Hollandia)              | Nő | `nl-NL-ColetteNeural`    | Általános kérdések |
| `pl-PL` | Lengyel (Lengyelország)                 | Nő | `pl-PL-ZofiaNeural`      | Általános kérdések |
| `pt-BR` | Portugál (Brazília)             | Nő | `pt-BR-FranciscaNeural`  | Általános, több hangstílus érhető el |
| `tr-TR` | Török                         | Nő | `tr-TR-EmelNeural`       | Általános kérdések |
| `pt-PT` | Portugál (Portugália)           | Nő | `pt-PT-FernandaNeural`   | Általános kérdések |
| `ru-RU` | Orosz (Oroszország)                | Nő | `ru-RU-DariyaNeural`     | Általános kérdések |
| `sv-SE` | Svéd (Svédország)                | Nő | `sv-SE-HilleviNeural`    | Általános kérdések |
| `th-TH` | Thai (Thaiföld)                 | Nő | `th-TH-AcharaNeural`     | Általános kérdések |
| `zh-CN` | Kínai (mandarin, egyszerűsített)  | Nő | `zh-CN-XiaoxiaoNeural`   | Általános, több hangstílus érhető el |
|         |                                 | Nő | `zh-CN-XiaoyouNeural`    | A szövegegység elbeszéléséhez optimalizált, gyerek hang |
|         |                                 | Férfi   | `zh-CN-YunyangNeural`    | A hírek olvasására optimalizált, több hangstílus érhető el |
|         |                                 | Férfi   | `zh-CN-YunyeNeural`      | A történet elbeszéléséhez optimalizált |
| `zh-HK` | Kínai (kantoni, hagyományos)   | Nő | `zh-HK-HiuGaaiNeural`| Általános kérdések |
| `zh-TW` | Kínai (tajvani mandarin)   | Nő | `zh-TW-HsiaoYuNeural`    | Általános kérdések |

> [!IMPORTANT]
> A hang a következőre `en-US-JessaNeural` változott: `en-US-AriaNeural` . Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

Ha szeretné megtudni, hogyan konfigurálhatja és módosíthatja a neurális hangokat, tekintse meg a [Speech szintézis Markup Language](speech-synthesis-markup.md#adjust-speaking-styles)című témakört.

> [!TIP]
> Továbbra is használhatja a teljes szolgáltatásnév leképezését (például "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)") a beszédfelismerési kérelmekben.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

| Területi beállítás | Nyelv | Nem | Hang neve |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arab (Egyiptom) | Nő | "AR-EG-a (z)" |
| `ar-SA` | Arab (Szaúd-Arábia) | Férfi | "AR-SA-Naayf" |
| `bg-BG` | Bolgár | Férfi |  "BG-BG-Ivan" |
| `ca-ES` | Katalán | Nő |  "CA-ES-HerenaRUS" |
| `cs-CZ` | Cseh | Férfi | "CS-CZ-Jakub" |
| `da-DK` | Dán | Nő |  "da-DK-HelleRUS" |
| `de-AT` | Német (Ausztria) | Férfi | "de-AT-Michael" |
| `de-CH` | Német (Svájc) | Férfi |  "de-CH-Karsten" |
| `de-DE` | Német (Németország) | Nő |  "de-DE-Hedda" |
|  |  | Nő | "de-DE-HeddaRUS" |
|  |  | Férfi |  "de-DE-Stefan-Apollo" |
| `el-GR` | Görög | Férfi | "el-GR-Stefanos" |
| `en-AU` | Angol (Ausztrália) | Nő |  "en-AU-Catherine" |
|  |  | Nő |  "en-AU-HayleyRUS" |
| `en-CA` | Angol (Kanada) | Nő |  "en-CA-Linda" |
|  |  | Nő |  "en-CA-HeatherRUS" |
| `en-GB` | Angol (Egyesült Királyság) | Nő |  "en-GB-Susan-Apollo" |
|  |  | Nő |  "en-GB-HazelRUS" |
|  |  | Férfi |  "en-GB-George-Apollo" |
| `en-IE` | Angol (Írország) | Férfi | "en-IE-Sean" |
| `en-IN` | Angol (India) | Nő | "en-IN-Heera-Apollo" |
|  |  | Nő |  "en-IN-PriyaRUS" |
|  |  | Férfi |  "en-IN-fosztogatás – Apollo" |
| `en-US` | Angol (US) | Nő |  "en-US-ZiraRUS" |
|  |  | Nő | "en-US-AriaRUS" |
|  |  | Férfi | "en-US-BenjaminRUS" |
|  |  | Férfi |  "en-US-Guy24kRUS" |
| `es-ES` | Spanyol (Spanyolország) | Nő |  "es-ES-Laura-Apollo" |
|  |  | Nő | "es-ES-HelenaRUS" |
|  |  | Férfi | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanyol (Mexikó) | Nő |  "es-MX-HildaRUS" |
|  |  | Férfi | "es-MX-Raul-Apollo" |
| `fi-FI` | Finn | Nő | "fi-FI-HeidiRUS" |
| `fr-CA` | Francia (Kanada) | Nő | "fr-CA-Caroline" |
|  |  | Nő | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francia (Svájc) | Férfi | "fr-CH-Guillaume" |
| `fr-FR` | Francia (Franciaország) | Nő |  "fr-FR-Julie-Apollo" |
|  |  | Nő |"fr-FR-HortenseRUS" |
|  |  | Férfi |  "fr-FR-Paul-Apollo" |
| `he-IL` | Héber (Izrael) | Férfi |  "ő-IL-Asaf" |
| `hi-IN` | Hindi (India) | Nő | "Hi-IN-Sánta-Apollo" |
|  |  | Nő |  "Hi-IN-kovacs" |
|  |  | Férfi |  "Hi-IN-alapképzés" |
| `hr-HR` | Horvát | Férfi | "HR-HR-Mate" |
| `hu-HU` | Magyar | Férfi |  "hu-HU-Szabolcs" |
| `id-ID` | Indonéz | Férfi | "id-ID-Andika" |
| `it-IT` | Olasz | Férfi |  "IT-IT-Cosimo-Apollo" |
|  |  | Nő |  "IT-IT-LuciaRUS" |
| `ja-JP` | Japán | Nő |  "ja-JP-Ayumi-Apollo" |
|  |  | Férfi | "ja-JP-Ichiro-Apollo" |
|  |  | Nő |  "ja-JP-HarukaRUS" |
| `ko-KR` | Koreai | Nő | "ko-KR-HeamiRUS" |
| `ms-MY` | Maláj | Férfi |  "MS-MY-Susanna" |
| `nb-NO` | Norvég | Nő |  "NB-NO-HuldaRUS" |
| `nl-NL` | Holland | Nő |  "NL-NL-HannaRUS" |
| `pl-PL` | Lengyel | Nő |  "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugál (Brazília) | Nő | "PT-BR-HeloisaRUS" |
|  |  | Férfi |  "PT-BR-Daniel-Apollo" |
| `pt-PT` | Portugál (Portugália) | Nő | "PT-PT-HeliaRUS" |
| `ro-RO` | Román | Férfi | "RO-RO-Andrej" |
| `ru-RU` | Orosz | Nő |  "ru-RU-Irina-Apollo" |
|  |  | Férfi | "ru-RU-Pavel-Apollo" |
|  |  | Nő |  ru-RU-EkaterinaRUS |
| `sk-SK` | Szlovák | Férfi | "SK-SK-Filip" |
| `sl-SI` | Szlovén | Férfi |  "SL-SI-Ladó" |
| `sv-SE` | Svéd | Nő | "SV-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Férfi |  "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | Nő |  "saját |
| `th-TH` | Thai | Férfi |  "th-TH-Pattara" |
| `tr-TR` | Török (Törökország) | Nő | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnámi | Férfi |  "VI-VN-an" |
| `zh-CN` | Kínai (mandarin, egyszerűsített) | Nő |  "zh-CN-HuihuiRUS" |
|  |  | Nő | "zh-CN-Yaoyao-Apollo" |
|  |  | Férfi | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kínai (kantoni, hagyományos) | Nő |  "ZH-HK-Tracy-Apollo" |
|  |  | Nő | "ZH-HK-TracyRUS" |
|  |  | Férfi |  "ZH-HK-Danny-Apollo" |
| `zh-TW` | Kínai (tajvani mandarin) | Nő |  "zh-TW-Yating-Apollo" |
|  |  | Nő | "zh-TW-HanHanRUS" |
|  |  | Férfi |  "zh-TW-Zhiwei-Apollo" |

**1** *AR – például a modern szabvány Arab (MSA) használatát támogatja.*

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
