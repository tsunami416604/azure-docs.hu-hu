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
ms.openlocfilehash: 36b653fb3c3a24f3d19408aa2c87b3b764d04ec1
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730322"
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

| Területi beállítás  | Nyelv                          | Támogatott | Testreszabások                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arab (UAE)                      | Igen       | Nem                                                |
| `ar-BH` | Arab (Bahrein), modern standard | Yes       | Nyelvi modell                                    |
| `ar-EG` | Arab (Egyiptom)                    | Yes       | Nyelvi modell                                    |
| `ar-IL` | Arab (Izrael)                   | Igen       | Nem                                                |
| `ar-JO` | Arab (Jordánia)                   | Igen       | Nem                                                |
| `ar-KW` | Arab (Kuvait)                   | Igen       | Nem                                                |
| `ar-LB` | Arab (Libanon)                  | Igen       | Nem                                                |
| `ar-PS` | Arab (Palesztina)                | Igen       | Nem                                                |
| `ar-QA` | Arab (Katar)                    | Igen       | Nem                                                |
| `ar-SA` | Arab (Szaúd-Arábia)             | Igen       | Nem                                                |
| `ar-SY` | Arab (Szíria)                    | Yes       | Nyelvi modell                                    |
| `ca-ES` | Katalán                           | Yes       | Nyelvi modell                                    |
| `da-DK` | Dán (Dánia)                  | Yes       | Nyelvi modell                                    |
| `de-DE` | Német (Németország)                  | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-AU` | Angol (Ausztrália)               | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `en-CA` | Angol (Kanada)                  | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `en-GB` | angol (Egyesült Királyság)          | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-IN` | Angol (India)                   | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `en-NZ` | Angol (Új-Zéland)             | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `en-US` | angol (Egyesült Államok)           | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `es-ES` | Spanyol (Spanyolország)                   | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `es-MX` | Spanyol (Mexikó)                  | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `fi-FI` | Finn (Finnország)                 | Yes       | Nyelvi modell                                    |
| `fr-CA` | Francia (Kanada)                   | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `fr-FR` | Francia (Franciaország)                   | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `gu-IN` | Gudzsaráti (indiai)                 | Yes       | Nyelvi modell                                    |
| `hi-IN` | Hindi (India)                     | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `it-IT` | Olasz (Olaszország)                   | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `ja-JP` | Japán (Japán)                  | Yes       | Nyelvi modell                                    |
| `ko-KR` | Koreai (Dél-Korea)                    | Yes       | Nyelvi modell                                    |
| `mr-IN` | Marathi (India)                   | Yes       | Nyelvi modell                                    |
| `nb-NO` | Norvég (nyelven) (Norvégia)       | Yes       | Nyelvi modell                                    |
| `nl-NL` | Holland (Hollandia)               | Yes       | Nyelvi modell                                    |
| `pl-PL` | Lengyel (Lengyelország)                   | Yes       | Nyelvi modell                                    |
| `pt-BR` | Portugál (Brazília)               | Yes       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `pt-PT` | Portugál (Portugália)             | Yes       | Nyelvi modell                                    |
| `ru-RU` | Orosz (Oroszország)                  | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `sv-SE` | Svéd (Svédország)                  | Yes       | Nyelvi modell                                    |
| `ta-IN` | Tamil (India)                     | Yes       | Nyelvi modell                                    |
| `te-IN` | Telugu (India)                    | Yes       | Nyelvi modell                                    |
| `th-TH` | Thai (Thaiföld)                   | Igen       | Nem                                                |
| `tr-TR` | Török (Törökország)                  | Yes       | Nyelvi modell                                    |
| `zh-CN` | Kínai (mandarin, egyszerűsített)    | Yes       | Akusztikai modell<br>Nyelvi modell                  |
| `zh-HK` | Kínai (kantoni, hagyományos)  | Yes       | Nyelvi modell                                    |
| `zh-TW` | Kínai (tajvani mandarin)      | Yes       | Nyelvi modell                                    |

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
| `de-DE` | Német (Németország)                | Nő | "de-DE-KatjaNeural"      | Általános kérdések |
| `en-AU` | Angol (Ausztrália)             | Nő | "en-AU-NatashaNeural"    | Általános kérdések |
| `en-CA` | Angol (Kanada)                | Nő | "en-CA-ClaraNeural"      | Általános kérdések |
| `en-GB` | Angol (Egyesült Királyság)                    | Nő | "en-GB-LibbyNeural"      | Általános kérdések |
|         |                                 | Nő | "en-GB-MiaNeural"        | Általános kérdések |
| `en-US` | Angol (US)                    | Nő | "en-US-AriaNeural"       | Általános, több hangstílus érhető el |
|         |                                 | Férfi   | "en-US-GuyNeural"        | Általános kérdések |
| `es-ES` | Spanyol (Spanyolország)                 | Nő | "es-ES-ElviraNeural"     | Általános kérdések |
| `es-MX` | Spanyol (Mexikó)                | Nő | "es-MX-DaliaNeural"      | Általános kérdések |
| `fr-CA` | Francia (Kanada)                 | Nő | "fr-CA-SylvieNeural"     | Általános kérdések |
| `fr-FR` | Francia (Franciaország)                 | Nő | "fr-FR-DeniseNeural"     | Általános kérdések |
| `it-IT` | Olasz (Olaszország)                 | Nő | "IT-IT-ElsaNeural"       | Általános kérdések |
| `ja-JP` | Japán                        | Nő | "ja-JP-NanamiNeural"     | Általános kérdések |
| `ko-KR` | Koreai                          | Nő | "ko-KR-SunHiNeural"      | Általános kérdések |
| `nb-NO` | Norvég                       | Nő | "NB-NO-IselinNeural"     | Általános kérdések |
| `pt-BR` | Portugál (Brazília)             | Nő | "PT-BR-FranciscaNeural"  | Általános kérdések |
| `tr-TR` | Török                         | Nő | "TR-TR-EmelNeural"       | Általános kérdések |
| `zh-CN` | Kínai (mandarin, egyszerűsített)  | Nő | "zh-CN-XiaoxiaoNeural"   | Általános, több hangstílus érhető el |
|         |                                 | Nő | "zh-CN-XiaoyouNeural"    | A szövegegység elbeszéléséhez optimalizált, gyerek hang |
|         |                                 | Férfi   | "zh-CN-YunyangNeural"    | A hírek olvasására optimalizált, több hangstílus érhető el |
|         |                                 | Férfi   | "zh-CN-YunyeNeural"      | A történet elbeszéléséhez optimalizált |

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
| `vi-VN` | Vietnami | Férfi |  "VI-VN-an" |
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
| Klingon                 | `tlh`         |
| Klingon (plqaD)         | `tlh-Qaak`    |
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
| Vietnami              | `vi`          |
| walesi                   | `cy`          |
| Yucatec Maya            | `yua`         |

## <a name="next-steps"></a>További lépések

* [Beszédfelismerési szolgáltatás próbaverziós előfizetésének beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszéd felismerése a C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
