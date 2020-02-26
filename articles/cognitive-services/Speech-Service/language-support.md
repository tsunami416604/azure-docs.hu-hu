---
title: Nyelvi támogatás – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás számos nyelvet támogat a beszédfelismeréshez és a szöveg-beszéd átalakításhoz, valamint a beszéd fordításához. Ez a cikk a szolgáltatás által nyújtott nyelvi támogatás átfogó listáját tartalmazza.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 86224508b9f24906abce157b92a77c0a2f24d882
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605731"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Nyelvi és területi támogatás a Speech Service-hez

A nyelvi támogatás a beszédfelismerési szolgáltatás működésének függvényében változik. Az alábbi táblázatok összefoglalják a [beszédfelismerési](#speech-to-text)és a [szöveges](#text-to-speech) [, valamint a beszédfelismerési](#speech-translation) szolgáltatás nyelvének támogatását.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a hang-és emberi címkével ellátott átiratok vagy a kapcsolódó szövegek feltöltésével: mondatok. A kiejtés testreszabása jelenleg csak `en-US` és `de-DE`számára érhető el. További információ a testreszabásról [itt](how-to-custom-speech.md).

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

 Területi beállítás | Nyelv | Támogatott | Testreszabások
------|------------|-----------|-------------
`ar-AE` | Arab (UAE) | Igen | Nem
`ar-BH` | Arab (Bahrein) | Igen | Nyelvi modell
`ar-EG` | Arab (Egyiptom), a modern standard | Igen | Nyelvi modell
`ar-KW` | Arab (Kuvait) | Igen | Nem
`ar-QA` | Arab (Katar) | Igen | Nem
`ar-SA` | arab (Szaúd-Arábia) | Igen | Nem
`ca-ES` | katalán | Igen | Nyelvi modell
`da-DK` | dán (Dánia) | Igen | Nyelvi modell
`de-DE` | német (Németország) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`en-AU` | angol (Ausztrália) | Igen | Akusztikai modell<br>Nyelvi modell
`en-CA` | angol (Kanada) | Igen | Akusztikai modell<br>Nyelvi modell
`en-GB` | angol (Egyesült Királyság) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`en-IN` | Angol (India) | Igen | Akusztikai modell<br>Nyelvi modell
`en-NZ` | angol (Új-Zéland) | Igen | Akusztikai modell<br>Nyelvi modell
`en-US` | angol (Egyesült Államok) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`es-ES` | spanyol (spanyolországi) | Igen | Akusztikai modell<br>Nyelvi modell
`es-MX` | spanyol (Mexikó) | Igen | Akusztikai modell<br>Nyelvi modell
`fi-FI` | finn (Finnország) | Igen | Nyelvi modell
`fr-CA` | francia (Kanada) | Igen | Akusztikai modell<br>Nyelvi modell
`fr-FR` | francia (Franciaország) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`gu-IN` | Gudzsaráti (indiai) | Igen | Nyelvi modell
`hi-IN` | hindi (India) | Igen | Akusztikai modell<br>Nyelvi modell
`it-IT` | olasz (Olaszország) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`ja-JP` | japán (Japán) | Igen | Nyelvi modell
`ko-KR` | koreai (Korea) | Igen | Nyelvi modell
`mr-IN` | Marathi (India) | Igen | Nyelvi modell
`nb-NO` | Norvég (bokmal) (Norvégia) | Igen | Nyelvi modell
`nl-NL` | holland (Hollandia) | Igen | Nyelvi modell
`pl-PL` | lengyel (Lengyelország) | Igen | Nyelvi modell
`pt-BR` | portugál (brazíliai) | Igen | Akusztikai modell<br>Nyelvi modell<br>Kiejtése
`pt-PT` | portugál (általános) | Igen | Nyelvi modell
`ru-RU` | orosz (Oroszország) | Igen | Akusztikai modell<br>Nyelvi modell
`sv-SE` | svéd (Svédország) | Igen | Nyelvi modell
`ta-IN` | tamil (India) | Igen | Nyelvi modell
`te-IN` | telugu (India) | Igen | Nem
`th-TH` | thai (Thaiföld) | Igen | Nem
`tr-TR` | Török (Törökország) | Igen | Nem
`zh-CN` | Kínai (Mandarin, egyszerűsített) | Igen | Akusztikai modell<br>Nyelvi modell
`zh-HK` | Kínai (kantoni, hagyományos) | Igen | Nyelvi modell
`zh-TW` | Kínai (tajvani Mandarin) | Igen | Nyelvi modell

## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API-k egyaránt támogatják ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít.

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid hang neve
--------|----------|--------|---------|------------
`de-DE` | német (Németország) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | Angol (US) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaNeural)" | "en-US-JessaNeural"
`en-US` | Angol (US) | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, GuyNeural)" | "en-US-GuyNeural"
`it-IT` | olasz (Olaszország) | Nő |"Microsoft Server Speech Text to Speech hang (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
`pt-BR` | portugál (brazíliai) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-BR, FranciscaNeural)" | "PT-BR-FranciscaNeural"
`zh-CN` | Kínai (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

Ha szeretné megtudni, hogyan konfigurálhatja és módosíthatja a neurális hangokat, tekintse meg a [Speech szintézis Markup Language](speech-synthesis-markup.md#adjust-speaking-styles)című témakört.

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid név
-------|----------|---------|----------|----------
<sup>1</sup>`ar-EG` | arab (Egyiptom) | Nő | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (ar-működtek az Adatbázisok, Hoda)" | "AR-EG-a (z)"
`ar-SA` | arab (Szaúd-Arábia) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
`bg-BG` | bolgár | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (bg-BG, Ivan)" | "BG-BG-Ivan"
`ca-ES` | Katalán | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
`cs-CZ` | cseh | Férfi | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | dán | Nő | "A Microsoft Server beszéd szöveg Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | német (Ausztria) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-AT, Michael)" | "de-AT-Michael"
`de-CH` | német (Svájc) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | német (Németország) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, lengyel, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | görög | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos"
`en-AU` | angol (Ausztrália) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, Catherine)" | "en-AU-Catherine"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | angol (Kanada) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | Angol (UK) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | angol (Írország) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IE, Sean)" | "en-IE-Sean"
`en-IN` | Angol (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-fosztogatás – Apollo"
`en-US` | Angol (US) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | spanyol (spanyolországi) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | spanyol (Mexikó) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | finn | Nő | "A Microsoft Server beszéd szöveg Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | francia (Kanada) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | francia (Svájc)| Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | francia (Franciaország)| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Ágnes, Apollo)" | "fr-FR-Julie-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | héber (Izrael) | Férfi| "A Microsoft Server beszéd szöveg Speech Voice (he-IL, Asaf)" | "ő-IL-Asaf"
`hi-IN` | hindi (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (hi-IN, Kalpana, Apollo)" | "Hi-IN-Sánta-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Kalpana)" | "Hi-IN-kovacs"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Hemant)" | "Hi-IN-alapképzés"
`hr-HR` | horvát | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (hr-HR, Matej)" | "HR-HR-Mate"
`hu-HU` | magyar | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | Indonéz| Férfi | "A Microsoft Server beszéd szöveg Speech Voice (id-azonosító, Andika)" | "id-ID-Andika"
`it-IT` | olasz | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | japán | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | koreai | Nő | "A Microsoft Server beszéd szöveg Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | Maláj | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ms-saját, Rizwan)" | "MS-MY-Susanna"
`nb-NO` | norvég | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nb-NO, HuldaRUS)" | "NB-NO-HuldaRUS"
`nl-NL` | Holland | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
`pl-PL` | lengyel | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | portugál (brazíliai) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS"
| | | Férfi |"A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo"
`pt-PT` | portugál (általános) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-PT, HeliaRUS)" | "PT-PT-HeliaRUS"
`ro-RO` | Román | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ro-RO, Andrei)" | "RO-RO-Andrej"
`ru-RU` |Orosz| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | szlovák | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sk-SK, Filip)" | "SK-SK-Filip"
`sl-SI` | szlovén | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sl SI, Lado)" | "sl-SI-Lado"
`sv-SE` | svéd | Nő | "A Microsoft Server beszéd szöveg Speech Voice (sv-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
`ta-IN` | tamil (India) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ta-IN, Valluvar)" | "TA-IN-Valluvar"
`te-IN` | telugu (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (te-IN, Chitra)" | "saját
`th-TH` | thai | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (cs-EDIK, Pattara)" | "th-TH-Pattara"
`tr-TR` | Török (Törökország) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (tr-TR, SedaRUS)" | "TR-TR-SedaRUS"
`vi-VN` | Vietnami | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (vi-VN, egy)" | "VI-VN-an"
`zh-CN` | Kínai (Spanyolország) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | kínai (Hongkong) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK TracyRUS)" | "ZH-HK-TracyRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
`zh-TW` | kínai (tajvani) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

**1** *AR – például a modern szabvány Arab (MSA) használatát támogatja.*

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="customization"></a>Testreszabás

A hang testreszabása `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`és `zh-CN`számára érhető el. Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adatként angol nyelven beszél angolul, válassza a `en-GB`lehetőséget.

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés minden területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, kivéve a `en-US` és `zh-CN`, ahol bármilyen méretű betanítási adatmennyiséggel elkezdheti.

## <a name="speech-translation"></a>Beszédfordítás

A **Speech Translation** API különböző nyelveket támogat a beszédfelismerés és a beszéd – szöveg fordításához. A forrás nyelvének mindig a beszéd-szöveg nyelvi táblából kell származnia. A cél elérhető nyelvek attól függ,-e a fordítási cél a speech vagy szöveget. A bejövő beszédet több mint [60 nyelvre](https://www.microsoft.com/translator/business/languages/)lefordíthatja. A [beszédfelismeréshez](language-support.md#text-languages)a nyelvek egy részhalmaza érhető el.

### <a name="text-languages"></a>Szöveg nyelvének

| Szöveg nyelvét    | Nyelvkód |
|:----------- |:-------------:|
| afrikaans      | `af`          |
| Arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| Kantoni (hagyományos)      | `yue`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| Kínai (hagyományos)      | `zh-Hant`          |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| Holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fijian      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| Haitian Creole      | `ht`          |
| Héber      | `he`          |
| hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| magyar      | `hu`          |
| Indonéz      | `id`          |
| ír      | `ga`          |
| olasz      | `it`          |
| japán      | `ja`          |
| kannada      | `kn`          |
| szuahéli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Madagaszkári      | `mg`          |
| Maláj      | `ms`          |
| malajálam      | `ml`          |
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál (brazíliai)      | `pt-br`          |
| portugál (általános)      | `pt-pt`          |
| pandzsábi      | `pa`          |
| Queretaro Otomi      | `otq`          |
| Román      | `ro`          |
| Orosz      | `ru`          |
| Samoa      | `sm`          |
| szerb (cirill betűs)      | `sr-Cyrl`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| telugu      | `te`          |
| thai      | `th`          |
| Tongan      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| Urdu      | `ur`          |
| Vietnami      | `vi`          |
| walesi      | `cy`          |
| A Maya alkalmazáshoz Yucatec      | `yua`          |


## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás próbaverziós előfizetésének beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
