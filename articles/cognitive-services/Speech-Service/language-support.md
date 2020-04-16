---
title: Nyelvi támogatás - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás számos nyelvet támogat a beszéd-szöveg és a szöveg-beszéd átalakításhoz, valamint a beszédfordítást. Ez a cikk a szolgáltatásszolgáltatás szerinti nyelvi támogatás átfogó listáját tartalmazza.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 2a876349af2bd53a46737dda82961afe442bae05
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401088"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>A beszédfelismerési szolgáltatás nyelvi és hangalapú támogatása

A nyelvi támogatás a Beszédszolgáltatás funkciótól függően változik. Az alábbi táblázatok a [beszédfelismerési,](#speech-to-text)a [szövegfelolvasás](#text-to-speech)és a [beszédfordítási](#speech-translation) szolgáltatás kínálatának nyelvi támogatását foglalják össze.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt támogatja a következő nyelveket (területi beállításokat). A pontosság javítása érdekében a nyelvek egy részhalmaza testreszabást kínálunk az Audio + Human címkével ellátott átiratok vagy kapcsolódó szövegek: Mondatok feltöltésével. A kiejtés testreszabása jelenleg `en-US` `de-DE`csak a és a számára érhető el. A testreszabásról itt olvashat [bővebben.](how-to-custom-speech.md)

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Területi beállítás  | Nyelv                          | Támogatott | Testreszabások                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | Arab (Egyesült Arab Emírségek)                      | Igen       | Nem                                                |
| `ar-BH` | Arab (Bahrein), modern szabvány | Igen       | Nyelvi modell                                    |
| `ar-EG` | arab (egyiptom)                    | Igen       | Nyelvi modell                                    |
| `ar-KW` | Arab (kuvaiti)                   | Igen       | Nem                                                |
| `ar-QA` | arab (katari)                    | Igen       | Nem                                                |
| `ar-SA` | Arab (Szaúd-Arábia)             | Igen       | Nem                                                |
| `ar-SY` | arab (szíriai)                    | Igen       | Nyelvi modell                                    |
| `ca-ES` | Katalán                           | Igen       | Nyelvi modell                                    |
| `da-DK` | Dán (Dánia)                  | Igen       | Nyelvi modell                                    |
| `de-DE` | Német (Németország)                  | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
| `en-AU` | Angol (Ausztrália)               | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-CA` | Angol (Kanada)                  | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-GB` | angol (Egyesült Királyság)          | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
| `en-IN` | Angol (India)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-NZ` | Angol (Új-Zéland)             | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `en-US` | angol (Egyesült Államok)           | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
| `es-ES` | Spanyol (Spanyolország)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `es-MX` | Spanyol (Mexikó)                  | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `fi-FI` | Finn (Finnország)                 | Igen       | Nyelvi modell                                    |
| `fr-CA` | Francia (Kanada)                   | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `fr-FR` | Francia (Franciaország)                   | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
| `gu-IN` | Gudzsaráti (indiai)                 | Igen       | Nyelvi modell                                    |
| `hi-IN` | Hindi (India)                     | Igen       | Akusztikai modell<br>Nyelvi modell                  |
| `it-IT` | Olasz (Olaszország)                   | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
| `ja-JP` | Japán (Japán)                  | Igen       | Nyelvi modell                                    |
| `ko-KR` | Koreai (Dél-Korea)                    | Igen       | Nyelvi modell                                    |
| `mr-IN` | Marathi (India)                   | Igen       | Nyelvi modell                                    |
| `nb-NO` | Norvég (Bokmål) (Norvégia)       | Igen       | Nyelvi modell                                    |
| `nl-NL` | Holland (Hollandia)               | Igen       | Nyelvi modell                                    |
| `pl-PL` | Lengyel (Lengyelország)                   | Igen       | Nyelvi modell                                    |
| `pt-BR` | Portugál (Brazília)               | Igen       | Akusztikai modell<br>Nyelvi modell<br>Kiejtés |
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

Mind a Microsoft Speech SDK, mind a REST API-k támogatják ezeket a hangokat, amelyek mindegyike támogat egy adott nyelvet és dialektust, amelyet a területi beállítások azonosítanak.

> [!IMPORTANT]
> Az árak a normál, az egyéni és a neurális hangoktól függően változnak. További információkért látogasson el az [Árképzés](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalra.

### <a name="neural-voices"></a>Neurális hangok

Neurális text-to-speech egy új típusú beszéd szintézise powered by mély neurális hálózatok. Neurális hang használata esetén a szintetizált beszéd szinte megkülönböztethetetlen az emberi felvételektől.

A neurális hangok segítségével a chatbotokkal és a hangasszisztensekkel való interakciók természetesebbé és vonzóbbá tehetők, a digitális szövegeket, például az e-könyveket hangoskönyvekké alakíthatják, és javíthatják az autós navigációs rendszereket. Az emberszerű természetes prosody és világos artikuláció a szavak, neurális hangok jelentősen csökkenti a hallgatás fáradtság, amikor a felhasználók kölcsönhatásba AI rendszerek.

A területi elérhetőségről további információt a régiók című [témakörben talál.](regions.md#standard-and-neural-voices)

| Területi beállítás  | Nyelv            | Nem | Teljes körű szolgáltatásnév-hozzárendelés                                               | Rövid hangnév        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | Német (Németország)    | Nő | "Microsoft Server speech text to speech voice (de-DE, KatjaNeural)"     | "de-DE-KatjaNális"     |
| `en-US` | Angol (US)        | Nő | "Microsoft Server Speech Text To Speech Voice (en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | Angol (US)        | Férfi   | "Microsoft Server Speech Text To Speech Voice (en-US, GuyNeural)"       | "en-US-GuyNeural"       |
| `it-IT` | Olasz (Olaszország)     | Nő | "Microsoft Server Speech Text To Speech Voice (it-IT, ElsaNeural)"      | "it-IT-ElsaNeural"      |
| `pt-BR` | Portugál (Brazília) | Nő | "Microsoft Server Speech Text To Speech Voice (pt-BR, FranciscaNeural)" | "pt-BR-FranciscaNeural" |
| `zh-CN` | Kínai (mandarin, egyszerűsített)  | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)"  | "zh-CN-XiaoxiaoNeural"  |

> [!IMPORTANT]
> A `en-US-JessaNeural` hang megváltozott `en-US-AriaNeural`. Ha korábban "Jessa"-t használtál, válts át "Aria"-ra.

A neurális hangok konfigurálásáról és módosításáról a [Beszédszintézis jelölőnyelv című témakörben](speech-synthesis-markup.md#adjust-speaking-styles)olvashat.

> [!TIP]
> Használhatja a teljes szolgáltatás névleképezése vagy a rövid hangnevét a beszédszintézis-kérelmekben.

### <a name="standard-voices"></a>Szabványos hangok

Több mint 75 szabványos hang érhető el több mint 45 nyelven és területi beállításban, amelyek lehetővé teszik a szöveg szintetizált beszédté alakítását. A területi elérhetőségről további információt a régiók című [témakörben talál.](regions.md#standard-and-neural-voices)

| Területi beállítás | Nyelv | Nem | Teljes körű szolgáltatásnév-hozzárendelés | Rövid név |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | arab (egyiptom) | Nő | "Microsoft Server speech text to speech voice (ar-EG, Hoda)" | "ar-EG-Hoda" |
| `ar-SA` | Arab (Szaúd-Arábia) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (ar-SA, Naayf)" | "ar-SA-Naayf" |
| `bg-BG` | Bolgár | Férfi | "Microsoft Server Speech Text To Speech Voice (bg-BG, Ivan)" | "BG-BG-Ivan" |
| `ca-ES` | Katalán (Spanyolország) | Nő | "Microsoft Server beszédszöveg-beszéd hang (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS" |
| `cs-CZ` | Cseh | Férfi | "Microsoft Server Speech Text To Speech Voice (cs-CZ, Jakub)" | "cs-CZ-Jakub" |
| `da-DK` | Dán | Nő | "Microsoft Server speech text to speech voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" |
| `de-AT` | Német (Ausztria) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (de-AT, Michael)" | "de-AT-Michael" |
| `de-CH` | Német (Svájc) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (de-CH, Karsten)" | "de-CH-Karsten" |
| `de-DE` | Német (Németország) | Nő | "Microsoft Server beszédszöveg-beszéd hang (de-DE, Hedda)" | "de-DE-Hedda" |
|  |  | Nő | "Microsoft Server beszédszöveg-beszéd hang (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" |
| `el-GR` | Görög | Férfi | "Microsoft Server speech text to speech voice (el-GR, Stefanos)" | "el-GR-Stefanos" |
| `en-AU` | Angol (Ausztrália) | Nő | "Microsoft Server speech text to speech voice (en-AU, Catherine)" | "en-AU-Catherine" |
|  |  | Nő | "Microsoft Server speech text to speech voice (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" |
| `en-CA` | Angol (Kanada) | Nő | "Microsoft Server beszédszöveg-beszéd hang (en-CA, Linda)" | "en-CA-Linda" |
|  |  | Nő | "Microsoft Server speech text to Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" |
| `en-GB` | Angol (UK) | Nő | "Microsoft Server Speech Text To Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" |
|  |  | Nő | "Microsoft Server speech text to speech voice (en-GB, HazelRUS)" | "en-GB-HazelRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo" |
| `en-IE` | Angol (Írország) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (en-IE, Sean)" | "en-IE-Sean" |
| `en-IN` | Angol (India) | Nő | "Microsoft Server Speech Text To Speech Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" |
|  |  | Nő | "Microsoft Server speech text to speech voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-Ravi-Apollo" |
| `en-US` | Angol (US) | Nő | "Microsoft Server Speech Text To Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS" |
|  |  | Nő | "Microsoft Server Speech Text To Speech Voice (en-US, AriaRUS)" | "en-US-AriaRUS" |
|  |  | Férfi | "Microsoft Server speech text to Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" |
| `es-ES` | Spanyol (Spanyolország) | Nő | "Microsoft Server Speech Text To Speech Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" |
|  |  | Nő | "Microsoft Server speech text to Speech Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" |
| `es-MX` | Spanyol (Mexikó) | Nő | "Microsoft Server speech text to speech voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" |
| `fi-FI` | Finn | Nő | "Microsoft Server speech text to Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" |
| `fr-CA` | Francia (Kanada) | Nő | "Microsoft Server Speech Text To Speech Voice (fr-CA, Caroline)" | "fr-CA-Caroline" |
|  |  | Nő | "Microsoft Server speech text to speech voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" |
| `fr-CH` | Francia (Svájc) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (fr-CH, Guillaume)" | "fr-CH-Guillaume" |
| `fr-FR` | Francia (Franciaország) | Nő | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" |
|  |  | Nő | "Microsoft Server speech text to speech voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" |
| `he-IL` | Héber (Izrael) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (he-IL, Asaf)" | "he-IL-Asaf" |
| `hi-IN` | Hindi (India) | Nő | "Microsoft Server Speech Text To Speech Voice (hi-IN, Kalpana, Apollo)" | "Hi-IN-Kalpana-Apollo" |
|  |  | Nő | "Microsoft Server beszédszöveg-beszéd hang (hi-IN, Kalpana)" | "Hi-IN-Kalpana" |
|  |  | Férfi | "Microsoft Server beszédszöveg-beszéd hang (hi-IN, Hemant)" | "Hi-IN-Hemant" |
| `hr-HR` | Horvát | Férfi | "Microsoft Server speech text to speech voice (hr-HR, Matej)" | "hr-HR-Matej" |
| `hu-HU` | Magyar | Férfi | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" |
| `id-ID` | Indonéz | Férfi | "Microsoft Server beszédszöveg-beszéd hang (id-id, Andika)" | "id-ID-Andika" |
| `it-IT` | Olasz | Férfi | "Microsoft Server Speech Text To Speech Voice (it-IT, Cosimo, Apollo)" | "it-IT-Cosimo-Apollo" |
|  |  | Nő | "Microsoft Server speech text to speech voice (it-IT, LuciaRUS)" | "it-IT-LuciaRUS" |
| `ja-JP` | Japán | Nő | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text To Speech Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" |
| `ko-KR` | Koreai | Nő | "Microsoft Server speech text to speech voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" |
| `ms-MY` | Maláj | Férfi | "Microsoft Server Speech Text To Speech Voice (ms-MY, Rizwan)" | "ms-MY-Rizwan" |
| `nb-NO` | Norvég | Nő | "Microsoft Server beszédszöveg-beszéd hang (nb-NO, HuldaRUS)" | "nb-NO-HuldaRUS" |
| `nl-NL` | Holland | Nő | "Microsoft Server beszédszöveg-beszéd hang (nl-NL, HannaRUS)" | "nl-NL-HannaRUS" |
| `pl-PL` | Lengyel | Nő | "Microsoft Server Speech Text To Speech Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS" |
| `pt-BR` | Portugál (Brazília) | Nő | "Microsoft Server Speech Text To Speech Voice (pt-BR, HeloisaRUS)" | "pt-BR-HeloisaRUS" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (pt-BR, Daniel, Apollo)" | "pt-BR-Daniel-Apollo" |
| `pt-PT` | Portugál (Portugália) | Nő | "Microsoft Server beszédszöveg-beszéd hang (pt-PT, HeliaRUS)" | "pt-PT-HeliaRUS" |
| `ro-RO` | Román | Férfi | "Microsoft Server beszédszöveg-beszéd hang (ro-RO, Andrei)" | "ro-RO-Andrei" |
| `ru-RU` | Orosz | Nő | "Microsoft Server Speech Text To Speech Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text To Speech Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text To Speech Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS |
| `sk-SK` | Szlovák | Férfi | "Microsoft Server beszédszöveg-beszéd hang (sk-SK, Filip)" | "sk-SK-Filip" |
| `sl-SI` | Szlovén | Férfi | "Microsoft Server beszédszöveg-beszéd hang (sl-SI, Lado)" | "sl-SI-Lado" |
| `sv-SE` | Svéd | Nő | "Microsoft Server beszédszöveg-beszéd hang (sv-SE, HedvigRUS)" | "sv-SE-HedvigRUS" |
| `ta-IN` | Tamil (India) | Férfi | "Microsoft Server beszédszöveg-beszéd hang (ta-IN, Valluvar)" | "ta-IN-Valluvar" |
| `te-IN` | Telugu (India) | Nő | "Microsoft Server beszédszöveg-beszéd hang (te-IN, Chitra)" | "te-IN-Chitra" |
| `th-TH` | Thai | Férfi | "Microsoft Server Speech Text To Speech Voice (th-TH, Pattara)" | "th-TH-Pattara" |
| `tr-TR` | Török (Törökország) | Nő | "Microsoft Server beszédszöveg-beszéd hang (tr-TR, SedaRUS)" | "tr-TR-SedaRUS" |
| `vi-VN` | Vietnami | Férfi | "Microsoft Server beszédszöveg-beszéd hang (vi-VN, An)" | "vi-VN-An" |
| `zh-CN` | Kínai (mandarin, egyszerűsített) | Nő | "Microsoft Server speech text to speech voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" |
|  |  | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | Kínai (kantoni, hagyományos) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)" | "zh-HK-Tracy-Apollo" |
|  |  | Nő | "Microsoft Server Speech Text To Speech Voice (zh-HK, TracyRUS)" | "zh-HK-TracyRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)" | "zh-HK-Danny-Apollo" |
| `zh-TW` | Kínai (tajvani mandarin) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)" | "zh-tw-yating-apollo" |
|  |  | Nő | "Microsoft Server Speech Text To Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" |
|  |  | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" |

**1** *az ar-EG támogatja a modern standard arab (MSA) szabványt.*

> [!IMPORTANT]
> A `en-US-Jessa` hang megváltozott `en-US-Aria`. Ha korábban "Jessa"-t használtál, válts át "Aria"-ra.

> [!TIP]
> Használhatja a teljes szolgáltatás névleképezése vagy a rövid hangnevét a beszédszintézis-kérelmekben.

### <a name="customization"></a>Testreszabás

A hangtestreszabás `de-DE`a `en-GB` `en-IN`, `en-US` `es-MX`, `fr-FR` `it-IT`, `pt-BR`, `zh-CN`, , , , , , és . Válassza ki a megfelelő területi beállítás, amely megfelel a betanítási adatokat be kell képeznie egy egyéni hangmodell. Ha például a rögzített adatokangolul, brit akcentussal `en-GB`beszélik, válassza a lehetőséget.

> [!NOTE]
> Nem támogatjuk a kétnyelvű modell képzést a Custom Voice-ban, kivéve a kínai-angol kétnyelvű. Válassza ki a "kínai-angol kétnyelvű", ha azt szeretné, hogy a vonat egy kínai hang, amely beszél angolul is. A hangbetanítás minden területi beállítással kezdődik egy 2000+ utterances `zh-CN` adatkészlettel, kivéve a és ahol `en-US` bármilyen méretű betanítási adatokkal kezdheti.

## <a name="speech-translation"></a>Beszédfordítás

A **beszédfordítás** api különböző nyelveket támogat a beszédfelolvasáshoz és a beszéd-szöveg fordításhoz. A forrásnyelvnek mindig a beszédfelismerési nyelvi táblából kell származnia. A rendelkezésre álló célnyelvek attól függnek, hogy a fordítási cél beszéd vagy szöveg. A bejövő beszédet több mint [60 nyelvre](https://www.microsoft.com/translator/business/languages/)fordíthatja le. A nyelvek egy részhalmaza elérhető a [beszédszintézishez.](language-support.md#text-languages)

### <a name="text-languages"></a>Szöveg nyelvek

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
| Haiti kreol          | `ht`          |
| Héber                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
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
| Málgas                | `mg`          |
| Maláj                   | `ms`          |
| malajálam               | `ml`          |
| Máltai                 | `mt`          |
| Norvég               | `nb`          |
| perzsa                 | `fa`          |
| Lengyel                  | `pl`          |
| Portugál (Brazília)     | `pt-br`       |
| Portugál (Portugália)   | `pt-pt`       |
| pandzsábi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
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

* [A Beszédfelismerési szolgáltatás próba-előfizetésének beolvasása](https://azure.microsoft.com/try/cognitive-services/)
* [A beszéd felismerése C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
