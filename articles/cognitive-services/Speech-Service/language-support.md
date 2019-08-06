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
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: ac3cb1d637eae1b4ee0a7db59efe631c7eb1ac6f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815258"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Nyelvi és területi támogatás a Speech Serviceshez

A különböző Speech Services-függvények különböző nyelveket támogatnak. A következő táblázat összefoglalja a nyelvi támogatás.

## <a name="speech-to-text"></a>Speech-to-text

A Microsoft Speech Recognition SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). Az egyes nyelvekhez testreszabás szinteken érhetők el.

  Kód | Nyelv | [Akusztikai betanítás](how-to-customize-acoustic-models.md) | [Nyelvi órái](how-to-customize-language-model.md) | [Adaptációs kiejtése](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar – példa | Arab (Egyiptom), a modern standard | Nem | Igen | Nem
 CA-ES | katalán | Nem | Nem | Nem
 da-DK | dán (Dánia) | Nem | Nem | Nem
 de-DE | német (Németország) | Igen | Igen | Igen
 en-Ausztrália | angol (Ausztrália) | Nem | Igen | Nem
 en-hitelesítésszolgáltató | angol (Kanada) | Nem | Igen | Nem
 en-GB | angol (Egyesült Királyság) | Nem | Igen | Nem
 en-IN | Angol (India) | Igen | Igen | Nem
 en-NZ | angol (Új-Zéland) | Nem | Igen | Nem 
 en-US | angol (Egyesült Államok) | Igen | Igen | Igen
 es-ES | spanyol (spanyolországi) | Igen | Igen | Nem
 es-MX | spanyol (Mexikó) | Nem | Igen | Nem
 fi-FI | finn (Finnország) | Nem | Nem | Nem
 FR-hitelesítésszolgáltató | francia (Kanada) | Nem | Igen | Nem
 FR-FR | francia (Franciaország) | Igen | Igen | Nem
 Üdvözöljük – India | hindi (India) | Nem | Igen | Nem
 it-IT | olasz (Olaszország) | Igen | Igen | Nem
 ja-JP | japán (Japán) | Nem | Igen | Nem
 ko-KR | koreai (Korea) | Nem | Igen | Nem
 nb-NO | Norvég (bokmal) (Norvégia) | Nem | Nem | Nem
 NL-NL | holland (Hollandia) | Nem | Igen | Nem
 pl-PL | lengyel (Lengyelország) | Nem | Nem | Nem
 pt-BR | portugál (brazíliai) | Igen | Igen | Nem
 PT-PT | portugál (általános) | Nem | Igen | Nem
 ru-RU | orosz (Oroszország) | Igen | Igen | Nem
 SV-SE | svéd (Svédország) | Nem | Nem | Nem
 zh-CN | Kínai (Mandarin, egyszerűsített) | Igen | Igen | Nem
 zh-HK | Kínai (kantoni, hagyományos) | Nem | Igen | Nem
 zh-TW | Kínai (tajvani Mandarin) | Nem | Igen | Nem
 cs-EDIK | thai (Thaiföld) | Nem | Nem | Nem


## <a name="text-to-speech"></a>Szövegfelolvasás

A szöveg-beszéd REST API támogatja ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, területi beállítással azonosítva.

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és virtuális asszisztensekkel folytatott interakcióra, így természetesebb és vonzó lehet a digitális szövegek, például az e-könyvek konvertálása az hangoskönyvekre, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

A neurális hangok és a regionális elérhetőség teljes listájáért lásd: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid hang neve
--------|----------|--------|---------|------------
de-DE | német (Németország) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | Angol (Egyesült Államok) | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Angol (Egyesült Államok) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | olasz (Olaszország) | Nő |"Microsoft Server Speech Text to Speech hang (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
zh-CN | Kínai (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid hang neve
-------|----------|---------|----------|----------
ar – példa\* | arab (Egyiptom) | Nő | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (ar-működtek az Adatbázisok, Hoda)" | "AR-EG-a (z)"
ar-SA | arab (Szaúd-Arábia) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ar-SA, Naayf)" | "ar-SA-Naayf"
bg-BG | bolgár | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (bg-BG, Ivan)" | "BG-BG-Ivan"
CA-ES | Katalán | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ca-ES, HerenaRUS)" | "ca-ES-HerenaRUS"
cs-CZ | cseh | Férfi | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Jakub)" | "CS-CZ-Jakub"
da-DK | dán | Nő | "A Microsoft Server beszéd szöveg Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS"
Németország-AT | német (Ausztria) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-AT, Michael)" | "de-AT-Michael"
Németország – CH | német (Svájc) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-CH, Karsten)" | "de-CH-Karsten"
de-DE | német (Németország) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, Hedda)" | "de-DE-Hedda"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, lengyel, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | görög | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos"
en-Ausztrália | angol (Ausztrália) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, Catherine)" | "en-AU-Catherine"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, HayleyRUS)" | "en-AU-HayleyRUS"
en-hitelesítésszolgáltató | angol (Kanada) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, Linda)" | "en-CA-Linda"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, HeatherRUS)" | "en-CA-HeatherRUS"
en-GB | Angol (Egyesült Királyság) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, HazelRUS)" | "en-GB-HazelRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, George, Apollo)" | "en-GB-George-Apollo"
en-IE | angol (Írország) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IE, Sean)" | "en-IE-Sean"
en-IN | Angol (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, PriyaRUS)" | "en-IN-PriyaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Ravi, Apollo)" | "en-IN-fosztogatás – Apollo"
en-US | Angol (Egyesült Államok) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, ZiraRUS)" | "en-US-ZiraRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaRUS)" | "en-US-JessaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, BenjaminRUS)" | "en-US-BenjaminRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | spanyol (spanyolországi) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, HelenaRUS)" | "es-ES-HelenaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo"
es-MX | spanyol (Mexikó) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, HildaRUS)" | "es-MX-HildaRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, Raul, Apollo)" | "es-MX-Raul-Apollo"
fi-FI | finn | Nő | "A Microsoft Server beszéd szöveg Speech Voice (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS"
FR-hitelesítésszolgáltató | francia (Kanada) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, Caroline)" | "fr-CA-Caroline"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS"
FR-CH | francia (Svájc)| Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CH, Guillaume)" | "fr-CH-Guillaume"
FR-FR | francia (Franciaország)| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Ágnes, Apollo)" | "fr-FR-Julie-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, HortenseRUS)" | "fr-FR-HortenseRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo"
He-IL| héber (Izrael) | Férfi| "A Microsoft Server beszéd szöveg Speech Voice (he-IL, Asaf)" | "ő-IL-Asaf"
Üdvözöljük – India | hindi (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (hi-IN, Kalpana, Apollo)" | "Hi-IN-Sánta-Apollo"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Kalpana)" | "Hi-IN-kovacs"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Hemant)" | "Hi-IN-alapképzés"
hr-HR | horvát | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (hr-HR, Matej)" | "HR-HR-Mate"
hu-HU | magyar | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (hu-HU, Szabolcs)" | "hu-HU-Szabolcs"
ID-Azonosítóját | indonéz| Férfi | "A Microsoft Server beszéd szöveg Speech Voice (id-azonosító, Andika)" | "id-ID-Andika"
it-IT | olasz | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, LuciaRUS)" | "IT-IT-LuciaRUS"
ja-JP | japán | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS"
ko-KR | koreai | Nő | "A Microsoft Server beszéd szöveg Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS"
MS-saját | maláj | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ms-saját, Rizwan)" | "MS-MY-Susanna"
nb-NO | norvég | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nb-NO, HuldaRUS)" | "NB-NO-HuldaRUS"
NL-NL | holland | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nl-NL, HannaRUS)" | "nl-NL-HannaRUS"
pl-PL | lengyel | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pl-PL, PaulinaRUS)" | "pl-PL-PaulinaRUS"
pt-BR | portugál (brazíliai) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS"
| | | Férfi |"A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo"
PT-PT | portugál (általános) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-PT, HeliaRUS)" | "PT-PT-HeliaRUS"
ro-RO | román | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ro-RO, Andrei)" | "RO-RO-Andrej"
ru-RU |orosz| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
SK-SK | szlovák | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sk-SK, Filip)" | "SK-SK-Filip"
sl SI | szlovén | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sl SI, Lado)" | "sl-SI-Lado"
SV-SE | svéd | Nő | "A Microsoft Server beszéd szöveg Speech Voice (sv-SE, HedvigRUS)" | "SV-SE-HedvigRUS"
TA-India | tamil (India) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ta-IN, Valluvar)" | "TA-IN-Valluvar"
Te-India | telugu (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (te-IN, Chitra)" | "saját
cs-EDIK | thai | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (cs-EDIK, Pattara)" | "th-TH-Pattara"
tr-TR | török | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (tr-TR, SedaRUS)" | "TR-TR-SedaRUS"
vi-VN | vietnami | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (vi-VN, egy)" | "VI-VN-an"
zh-CN | Kínai (Spanyolország) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo"
zh-HK | kínai (Hongkong) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Tracy, Apollo)" | "zh-HK-Tracy-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK TracyRUS)" | "ZH-HK-TracyRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Danny, Apollo)" | "zh-HK-Danny-Apollo"
zh-TW | kínai (tajvani) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo"

\* *ar Például támogatja a Modern Standard arab (MSA).*

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="customization"></a>Testreszabás

A hang testreszabása a de-DE, az en-GB, az EN-IN, az en-US, az es-MX, a fr-FR, az IT-IT, a PT-BR és a zh-CN szolgáltatásban érhető el. Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adat angol nyelven van kiválasztva egy brit kiejtéssel, válassza az en-GB lehetőséget.  

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés minden területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, az en-US és a zh-CN kivételével, ahol bármilyen méretű betanítási adatmennyiséggel elkezdheti.

## <a name="speech-translation"></a>Tolmácsolás

A **beszédalapú fordítási** API különböző nyelveket támogat a speech beszéd és hang-szöveg transzformációs fordítás. A forrás nyelvének mindig a beszéd-szöveg nyelvi táblából kell származnia. A cél elérhető nyelvek attól függ,-e a fordítási cél a speech vagy szöveget. Bejövő speech be, előfordulhat, hogy fordítása több mint [60 nyelvet](https://www.microsoft.com/translator/business/languages/). Ezek egy részét érhetők el a [beszédszintézishez](language-support.md#text-languages).

### <a name="text-languages"></a>Szöveg nyelvének

| Szöveg nyelvét    | Nyelvkód |
|:----------- |:-------------:|
| afrikaans      | `af`          |
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| Kantoni (hagyományos)      | `yue`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| kínai (hagyományos)      | `zh-Hant`          |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fijian      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| Haitian Creole      | `ht`          |
| héber      | `he`          |
| hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| magyar      | `hu`          |
| indonéz      | `id`          |
| olasz      | `it`          |
| japán      | `ja`          |
| szuahéli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Madagaszkári      | `mg`          |
| maláj      | `ms`          |
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| Queretaro Otomi      | `otq`          |
| román      | `ro`          |
| orosz      | `ru`          |
| Samoa      | `sm`          |
| szerb (cirill betűs)      | `sr-Cyrl`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| thai      | `th`          |
| Tongan      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |
| A Maya alkalmazáshoz Yucatec      | `yua`          |


## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech Services-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
