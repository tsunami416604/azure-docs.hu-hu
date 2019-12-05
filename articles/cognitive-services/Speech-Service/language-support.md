---
title: Nyelvi támogatás – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás számos nyelvet támogat a beszédfelismeréshez és a szöveg-beszéd átalakításhoz, valamint a beszéd fordításához. Ez a cikk a szolgáltatás által nyújtott nyelvi támogatás átfogó listáját tartalmazza.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8db70ccbabfc165dbe3f7b9a61dbe5023a87f708
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815381"
---
# <a name="language-and-region-support-for-the-speech-service"></a>Nyelvi és területi támogatás a Speech Service-hez

A nyelvi támogatás a beszédfelismerési szolgáltatás működésének függvényében változik. Az alábbi táblázatok összefoglalják a [beszédfelismerési](#speech-to-text)és a [szöveges](#text-to-speech) [, valamint a beszédfelismerési](#speech-translation) szolgáltatás nyelvének támogatását.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a hang-és emberi címkével ellátott átiratok vagy a kapcsolódó szövegek feltöltésével: mondatok.  A kiejtés testreszabása jelenleg csak `en-US` és `de-DE`számára érhető el. További információ a testreszabásról [itt](how-to-custom-speech.md).

 Területi beállítás | Nyelv | Támogatott | Testre szabható
------|------------|-----------|-------------
`ar-EG` | Arab (Egyiptom), modern standard | Igen | Igen
`ar-SA` | Arab (Szaúd-Arábiai) | Igen | Igen
`ar-AE` | Arab (UAE) | Igen | Igen
`ar-KW` | Arab (Kuvait) | Igen | Igen
`ar-QA` | Arab (Katar) | Igen | Igen
`ca-ES` | katalán | Igen | Nem
`da-DK` | dán (Dánia) | Igen | Nem
`de-DE` | Német (Németország) | Igen | Igen
`en-AU` | Angol (Ausztrália) | Igen | Igen
`en-CA` | Angol (Kanada) | Igen | Igen
`en-GB` | angol (Egyesült Királyság) | Igen | Igen
`en-IN` | Angol (India) | Igen | Igen
`en-NZ` | Angol (Új-Zéland) | Igen | Igen
`en-US` | Angol (Egyesült Államok) | Igen | Igen
`es-ES` | Spanyol (Spanyolország) | Igen | Igen
`es-MX` | Spanyol (Mexikó) | Igen | Igen
`fi-FI` | finn (Finnország) | Igen | Nem
`fr-CA` | Francia (Kanada) | Igen | Igen
`fr-FR` | Francia (Franciaország) | Igen | Igen
`gu-IN` | Gudzsaráti (indiai) | Igen | Igen
`hi-IN` | Hindi (India) | Igen | Igen
`it-IT` | Olasz (Olaszország) | Igen | Igen
`ja-JP` | Japán (Japán) | Igen | Igen
`ko-KR` | Koreai (Korea) | Igen | Igen
`mr-IN` | Marathi (India) | Igen | Igen
`nb-NO` | Norvég (nyelven) (Norvégia) | Igen | Nem
`nl-NL` | holland (Hollandia) | Igen | Igen
`pl-PL` | lengyel (Lengyelország) | Igen | Nem
`pt-BR` | Portugál (brazíliai) | Igen | Igen
`pt-PT` | portugál (Portugália) | Igen | Igen
`ru-RU` | Orosz (Oroszország) | Igen | Igen
`sv-SE` | svéd (Svédország) | Igen | Nem
`ta-IN` | Tamil (India) | Igen | Igen
`te-IN` | Telugu (India) | Igen | Igen
`zh-CN` | Kínai (mandarin, egyszerűsített) | Igen | Igen
`zh-HK` | Kínai (kantoni, hagyományos) | Igen | Igen
`zh-TW` | Kínai (tajvani mandarin) | Igen | Igen
`th-TH` | Thai (Thaiföld) | Igen | Nem
`tr-TR` | Törökország | Igen | Igen

## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API-k egyaránt támogatják ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít.

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

A neurális hangok és a regionális elérhetőség teljes listájáért lásd: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid hang neve
--------|----------|--------|---------|------------
`de-DE` | Német (Németország) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
`en-US` | Angol (Egyesült Államok) | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, GuyNeural)" | "en-US-GuyNeural"
`en-US` | Angol (Egyesült Államok) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaNeural)" | "en-US-JessaNeural"
`it-IT` | Olasz (Olaszország) | Nő |"Microsoft Server Speech Text to Speech hang (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
`zh-CN` | Kínai (anyaország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid név
-------|----------|---------|----------|----------
<sup>&dagger;</sup>`ar-EG` | Arab (Egyiptom) | Nő | "Microsoft Server Speech Text to Speech hang (AR-EG, a és a (z))" | "AR-EG-a (z)"
`ar-SA` | Arab (Szaúd-Arábiai) | Férfi | "Microsoft Server Speech Text to Speech hang (AR-SA, Naayf)" | "AR-SA-Naayf"
`bg-BG` | bolgár | Férfi | "Microsoft Server Speech Text to Speech Voice (BG-BG, Ivan)" | "BG-BG-Ivan"
`ca-ES` | Katalán (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech Voice (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS"
`cs-CZ` | Cseh | Férfi | "Microsoft Server Speech Text to Speech hang (CS-CZ, Jakub)" | "CS-CZ-Jakub"
`da-DK` | dán | Nő | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
`de-AT` | Német (Ausztria) | Férfi | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "de-AT-Michael"
`de-CH` | Német (Svájc) | Férfi | "Microsoft Server Speech Text to Speech hang (de-CH, Karsten)" | "de-CH-Karsten"
`de-DE` | Német (Németország) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
`el-GR` | görög | Férfi | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos"
`en-AU` | Angol (Ausztrália) | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, Catherine)" | "en-AU-Catherine"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, HayleyRUS)" | "en-AU-HayleyRUS"
`en-CA` | Angol (Kanada) | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, Linda)" | "en-CA-Linda"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
`en-GB` | Angol (Egyesült Királyság) | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-GB, George, Apollo)" | "en-GB-George-Apollo"
`en-IE` | Angol (Írország) | Férfi | "Microsoft Server Speech Text to Speech hang (en-IE, Sean)" | "en-IE-Sean"
`en-IN` | Angol (India) | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-IN, Ravi, Apollo)" | "en-IN-fosztogatás – Apollo"
`en-US` | Angol (Egyesült Államok) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
`es-ES` | Spanyol (Spanyolország) |Nő | "Microsoft Server Speech Text to Speech hang (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
`es-MX` | Spanyol (Mexikó) | Nő | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
`fi-FI` | finn | Nő | "Microsoft Server Speech Text to Speech hang (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
`fr-CA` | Francia (Kanada) |Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, Caroline)" | "fr-CA-Caroline"
| | | Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
`fr-CH` | Francia (Svájc)| Férfi | "Microsoft Server Speech Text to Speech hang (FR-CH, Guillaume)" | "fr-CH-Guillaume"
`fr-FR` | Francia (Franciaország)| Nő | "Microsoft Server Speech Text to Speech hang (FR-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech Voice (FR-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (FR-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
`he-IL` | Héber (Izrael) | Férfi| "Microsoft Server Speech Text to Speech hang (IT-IL, Asaf)" | "ő-IL-Asaf"
`hi-IN` | Hindi (India) | Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs, Apollo)" | "Hi-IN-Sánta-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs)" | "Hi-IN-kovacs"
| | | Férfi | "A Microsoft Server Speech Text to speech hangja (Hi-in, vérképzés)" | "Hi-IN-alapképzés"
`hr-HR` | horvát | Férfi | "Microsoft Server Speech Text to Speech hang (HR-HR, Matej)" | "HR-HR-Mate"
`hu-HU` | magyar | Férfi | "Microsoft Server Speech Text to Speech hang (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
`id-ID` | indonéz| Férfi | "Microsoft Server Speech Text to Speech Voice (ID-ID, Andika)" | "id-ID-Andika"
`it-IT` | olasz | Férfi | "Microsoft Server Speech Text to Speech hang (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (IT-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
`ja-JP` | japán | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
`ko-KR` | koreai | Nő | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
`ms-MY` | maláj | Férfi | "Microsoft Server Speech Text to Speech hang (MS-MY, AIMA)" | "MS-MY-Susanna"
`nb-NO` | norvég | Nő | "Microsoft Server Speech Text to Speech hang (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS"
`nl-NL` | Holland | Nő | "Microsoft Server Speech Text to Speech Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS"
`pl-PL` | lengyel | Nő | "Microsoft Server Speech Text to Speech Voice (pl-PL. PaulinaRUS)" | "pl-PL-PaulinaRUS"
`pt-BR` | Portugál (brazíliai) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS"
| | | Férfi |"Microsoft Server Speech Text to Speech Voice (PT-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo"
`pt-PT` | portugál (Portugália) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-PT, HeliaRUS)" | "PT-PT-HeliaRUS"
`ro-RO` | román | Férfi | "Microsoft Server Speech Text to Speech hang (RO-RO, Andrej)" | "RO-RO-Andrej"
`ru-RU` |orosz| Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
`sk-SK` | szlovák | Férfi | "Microsoft Server Speech Text to Speech Voice (SK-SK, Filip)" | "SK-SK-Filip"
`sl-SI` | szlovén | Férfi | "Microsoft Server Speech Text to Speech Voice (SL-SI, Ladó)" | "SL-SI-Ladó"
`sv-SE` | svéd | Nő | "Microsoft Server Speech Text to Speech Voice (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
`ta-IN` | Tamil (India) | Férfi | "Microsoft Server Speech Text to Speech hang (TA-IN, Valluvar)" | "TA-IN-Valluvar"
`te-IN` | Telugu (India) | Nő | "Microsoft Server Speech Text to Speech hang (te-IN, chiton)" | "saját
`th-TH` | thai | Férfi | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara"
`tr-TR` | török | Nő | "Microsoft Server Speech Text to Speech hang (TR-TR, SedaRUS)" | "TR-TR-SedaRUS"
`vi-VN` | vietnami | Férfi | "Microsoft Server Speech Text to Speech Voice (VI – VN, an)" | "VI-VN-an"
`zh-CN` | Kínai (anyaország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
`zh-HK` | Kínai (Hongkong) | Nő | "Microsoft Server Speech Text to Speech hang (ZH-HK, Tracy, Apollo)" | "ZH-HK-Tracy-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech Voice (ZH-HK, TracyRUS)" | "ZH-HK-TracyRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ZH-HK, Danny, Apollo)" | "ZH-HK-Danny-Apollo"
`zh-TW` | Kínai (Tajvan) | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Férfi | "Microsoft Server Speech Text to Speech hang (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

&dagger; *AR-eg a modern szabványos Arab (MSA) szabványokat támogatja.*

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="customization"></a>Testreszabás

A hang testreszabása `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`és `zh-CN`számára érhető el. Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adatként angol nyelven beszél angolul, válassza a `en-GB`lehetőséget.

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés minden területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, kivéve a `en-US` és `zh-CN`, ahol bármilyen méretű betanítási adatmennyiséggel elkezdheti.

## <a name="speech-translation"></a>Beszédfordítás

A **Speech Translation** API különböző nyelveket támogat a beszédfelismerés és a beszéd – szöveg fordításához. A forrás nyelvének mindig a beszéd-szöveg nyelvi táblából kell származnia. Az elérhető célnyelv attól függnek, hogy a fordítás célja beszéd vagy szöveg. A bejövő beszédet több mint [60 nyelvre](https://www.microsoft.com/translator/business/languages/)lefordíthatja. A [beszédfelismeréshez](language-support.md#text-languages)a nyelvek egy részhalmaza érhető el.

### <a name="text-languages"></a>Szöveg nyelve

| Szöveg nyelve    | Nyelvkód |
|:----------- |:-------------:|
| búr      | `af`          |
| arab       | `ar`          |
| Bangla      | `bn`          |
| Bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| Kantoni (hagyományos)      | `yue`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| kínai (hagyományos)      | `zh-Hant`          |
| horvát      | `hr`          |
| Cseh      | `cs`          |
| dán      | `da`          |
| Holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fidzsi      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| Francia      | `fr`          |
| Német      | `de`          |
| görög      | `el`          |
| Haiti kreol      | `ht`          |
| héber      | `he`          |
| hindi      | `hi`          |
| Hmong DAW      | `mww`          |
| magyar      | `hu`          |
| indonéz      | `id`          |
| olasz      | `it`          |
| japán      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Malgas      | `mg`          |
| maláj      | `ms`          |
| Máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| Portugál      | `pt`          |
| Queretaro otomi      | `otq`          |
| román      | `ro`          |
| orosz      | `ru`          |
| Szamoai      | `sm`          |
| Szerb (cirill betűs)      | `sr-Cyrl`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| Spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| telugu      | `te`          |
| thai      | `th`          |
| Tongai      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| Urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás próbaverziós előfizetésének beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
