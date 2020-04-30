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
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113833"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Beszédfelismerési szolgáltatás nyelvi és hangalapú támogatása

A nyelvi támogatás a beszédfelismerési szolgáltatás működésének függvényében változik. Az alábbi táblázatok összefoglalják a [beszédfelismerési](#speech-to-text)és a [szöveges](#text-to-speech) [, valamint a beszédfelismerési](#speech-translation) szolgáltatás nyelvének támogatását.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a hang-és emberi címkével ellátott átiratok vagy a kapcsolódó szövegek feltöltésével: mondatok. A kiejtés testreszabása jelenleg csak a és `en-US` `de-DE`a esetében érhető el. További információ a testreszabásról [itt](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Területi beállítás  | Nyelv                          | Támogatott | Testreszabások                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arab (UAE)                      | Igen       | Nem                                                |
| `ar-BH` | Arab (Bahrein), modern standard | Igen       | Nyelvi modell                                    |
| `ar-EG` | Arab (Egyiptom)                    | Igen       | Nyelvi modell                                    |
| `ar-KW` | Arab (Kuvait)                   | Igen       | Nem                                                |
| `ar-QA` | Arab (Katar)                    | Igen       | Nem                                                |
| `ar-SA` | Arab (Szaúd-Arábiai)             | Igen       | Nem                                                |
| `ar-SY` | Arab (Szíria)                    | Igen       | Nyelvi modell                                    |
| `ca-ES` | Katalán                           | Igen       | Nyelvi modell                                    |
| `da-DK` | Dán (Dánia)                  | Igen       | Nyelvi modell                                    |
| `de-DE` | Német (Németország)                  | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-AU` | Angol (Ausztrália)               | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-CA` | Angol (Kanada)                  | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-GB` | angol (Egyesült Királyság)          | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `en-IN` | Angol (India)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-NZ` | Angol (Új-Zéland)             | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-US` | angol (Egyesült Államok)           | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `es-ES` | Spanyol (Spanyolország)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `es-MX` | Spanyol (Mexikó)                  | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `fi-FI` | Finn (Finnország)                 | Igen       | Nyelvi modell                                    |
| `fr-CA` | Francia (Kanada)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `fr-FR` | Francia (Franciaország)                   | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `gu-IN` | Gudzsaráti (indiai)                 | Igen       | Nyelvi modell                                    |
| `hi-IN` | Hindi (India)                     | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `it-IT` | Olasz (Olaszország)                   | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `ja-JP` | Japán (Japán)                  | Igen       | Nyelvi modell                                    |
| `ko-KR` | Koreai (Dél-Korea)                    | Igen       | Nyelvi modell                                    |
| `mr-IN` | Marathi (India)                   | Igen       | Nyelvi modell                                    |
| `nb-NO` | Norvég (nyelven) (Norvégia)       | Igen       | Nyelvi modell                                    |
| `nl-NL` | Holland (Hollandia)               | Igen       | Nyelvi modell                                    |
| `pl-PL` | Lengyel (Lengyelország)                   | Igen       | Nyelvi modell                                    |
| `pt-BR` | Portugál (Brazília)               | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtése |
| `pt-PT` | Portugál (Portugália)             | Igen       | Nyelvi modell                                    |
| `ru-RU` | Orosz (Oroszország)                  | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `sv-SE` | Svéd (Svédország)                  | Igen       | Nyelvi modell                                    |
| `ta-IN` | Tamil (India)                     | Igen       | Nyelvi modell                                    |
| `te-IN` | Telugu (India)                    | Igen       | Nem                                                |
| `th-TH` | Thai (Thaiföld)                   | Igen       | Nem                                                |
| `tr-TR` | Török (Törökország)                  | Igen       | Nem                                                |
| `zh-CN` | Kínai (mandarin, egyszerűsített)    | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `zh-HK` | Kínai (kantoni, hagyományos)  | Igen       | Nyelvi modell                                    |
| `zh-TW` | Kínai (tajvani mandarin)      | Igen       | Nyelvi modell                                    |

## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API-k egyaránt támogatják ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít.

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

| Területi beállítás  | Nyelv            | Nem | Teljes szolgáltatásnév-hozzárendelés                                               | Rövid hang neve        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Német (Németország)    | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNeural"     |
| `en-US` | Angol (US)        | Nő | "Microsoft Server Speech Text to Speech hang (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Angol (US)        | Férfi   | "Microsoft Server Speech Text to Speech hang (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Olasz (Olaszország)     | Nő | "Microsoft Server Speech Text to Speech hang (IT-IT, ElsaNeural)"      | "IT-IT-ElsaNeural"      |
| `pt-BR` | Portugál (Brazília) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-BR, FranciscaNeural)" | "PT-BR-FranciscaNeural" |
| `zh-CN` | Kínai (mandarin, egyszerűsített)  | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> A `en-US-JessaNeural` hang a következőre `en-US-AriaNeural`változott:. Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

Ha szeretné megtudni, hogyan konfigurálhatja és módosíthatja a neurális hangokat, tekintse meg a [Speech szintézis Markup Language](speech-synthesis-markup.md#adjust-speaking-styles)című témakört.

> [!TIP]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

| Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid név |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | Arab (Egyiptom) | Nő | "Microsoft Server Speech Text to Speech hang (AR-EG, a és a (z))" | "AR-EG-a (z)" |
| `ar-SA` | Arab (Szaúd-Arábiai) | Férfi | "Microsoft Server Speech Text to Speech hang (AR-SA, Naayf)" | "AR-SA-Naayf" |
| `bg-BG` | Bolgár | Férfi | "Microsoft Server Speech Text to Speech Voice (BG-BG, Ivan)" | "BG-BG-Ivan" |
| `ca-ES` | Katalán (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech Voice (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS" |
| `cs-CZ` | Cseh | Férfi | "Microsoft Server Speech Text to Speech hang (CS-CZ, Jakub)" | "CS-CZ-Jakub" |
| `da-DK` | Dán | Nő | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Német (Ausztria) | Férfi | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Német (Svájc) | Férfi | "Microsoft Server Speech Text to Speech hang (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Német (Németország) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Görög | Férfi | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Angol (Ausztrália) | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Angol (Kanada) | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Angol (Egyesült Királyság) | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Angol (Írország) | Férfi | "Microsoft Server Speech Text to Speech hang (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Angol (India) | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (en-IN, Ravi, Apollo)" | "en-IN-fosztogatás – Apollo" |
| `en-US` | Angol (US) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spanyol (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech hang (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanyol (Mexikó) | Nő | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finn | Nő | "Microsoft Server Speech Text to Speech hang (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Francia (Kanada) | Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francia (Svájc) | Férfi | "Microsoft Server Speech Text to Speech hang (FR-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Francia (Franciaország) | Nő | "Microsoft Server Speech Text to Speech hang (FR-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (FR-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (FR-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Héber (Izrael) | Férfi | "Microsoft Server Speech Text to Speech hang (IT-IL, Asaf)" | "ő-IL-Asaf" |
| `hi-IN` | Hindi (India) | Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs, Apollo)" | "Hi-IN-Sánta-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs)" | "Hi-IN-kovacs" |
|  |  | Férfi | "A Microsoft Server Speech Text to speech hangja (Hi-in, vérképzés)" | "Hi-IN-alapképzés" |
| `hr-HR` | Horvát | Férfi | "Microsoft Server Speech Text to Speech hang (HR-HR, Matej)" | "HR-HR-Mate" |
| `hu-HU` | Magyar | Férfi | "Microsoft Server Speech Text to Speech hang (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonéz | Férfi | "Microsoft Server Speech Text to Speech Voice (ID-ID, Andika)" | "id-ID-Andika" |
| `it-IT` | Olasz | Férfi | "Microsoft Server Speech Text to Speech hang (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (IT-IT, LuciaRUS)" | "IT-IT-LuciaRUS" |
| `ja-JP` | Japán | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreai | Nő | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Maláj | Férfi | "Microsoft Server Speech Text to Speech hang (MS-MY, AIMA)" | "MS-MY-Susanna" |
| `nb-NO` | Norvég | Nő | "Microsoft Server Speech Text to Speech hang (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS" |
| `nl-NL` | Holland | Nő | "Microsoft Server Speech Text to Speech Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS" |
| `pl-PL` | Lengyel | Nő | "Microsoft Server Speech Text to Speech Voice (pl-PL. PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugál (Brazília) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (PT-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo" |
| `pt-PT` | Portugál (Portugália) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-PT, HeliaRUS)" | "PT-PT-HeliaRUS" |
| `ro-RO` | Román | Férfi | "Microsoft Server Speech Text to Speech hang (RO-RO, Andrej)" | "RO-RO-Andrej" |
| `ru-RU` | Orosz | Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Szlovák | Férfi | "Microsoft Server Speech Text to Speech Voice (SK-SK, Filip)" | "SK-SK-Filip" |
| `sl-SI` | Szlovén | Férfi | "Microsoft Server Speech Text to Speech Voice (SL-SI, Ladó)" | "SL-SI-Ladó" |
| `sv-SE` | Svéd | Nő | "Microsoft Server Speech Text to Speech Voice (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Férfi | "Microsoft Server Speech Text to Speech hang (TA-IN, Valluvar)" | "TA-IN-Valluvar" |
| `te-IN` | Telugu (India) | Nő | "Microsoft Server Speech Text to Speech hang (te-IN, chiton)" | "saját |
| `th-TH` | Thai | Férfi | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Török (Törökország) | Nő | "Microsoft Server Speech Text to Speech hang (TR-TR, SedaRUS)" | "TR-TR-SedaRUS" |
| `vi-VN` | Vietnami | Férfi | "Microsoft Server Speech Text to Speech Voice (VI – VN, an)" | "VI-VN-an" |
| `zh-CN` | Kínai (mandarin, egyszerűsített) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kínai (kantoni, hagyományos) | Nő | "Microsoft Server Speech Text to Speech hang (ZH-HK, Tracy, Apollo)" | "ZH-HK-Tracy-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (ZH-HK, TracyRUS)" | "ZH-HK-TracyRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (ZH-HK, Danny, Apollo)" | "ZH-HK-Danny-Apollo" |
| `zh-TW` | Kínai (tajvani mandarin) | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech hang (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *AR – például a modern szabvány Arab (MSA) használatát támogatja.*

> [!IMPORTANT]
> A `en-US-Jessa` hang a következőre `en-US-Aria`változott:. Ha korábban a "Jessa" kifejezést használta, váltson át az "aria" kifejezésre.

> [!TIP]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="customization"></a>Testreszabás

A `de-DE` `en-GB` `en-IN` `zh-CN`,, `fr-FR`,,,,, és a hangalapú Testreszabás elérhető. `en-US` `es-MX` `it-IT` `pt-BR` Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adatként angol nyelven beszél angolul, válassza a lehetőséget `en-GB`.

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés az összes területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, kivéve a `en-US` és `zh-CN` az, ahol bármilyen méretű betanítási adatmennyiséget használhat.

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
