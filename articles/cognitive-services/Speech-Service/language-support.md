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
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 26cab7ba3ed864382ae5511755fee09c3826702c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580180"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Nyelvi és területi támogatás a Speech Serviceshez

A különböző Speech Services-függvények különböző nyelveket támogatnak. A következő táblázatok összefoglalják a nyelvi támogatást.

## <a name="speech-to-text"></a>Diktálás

A Microsoft Speech SDK és a REST API egyaránt a következő nyelveket támogatja (területi beállítások). A pontosság javítása érdekében a testreszabást a nyelvek egy részhalmaza számára ajánljuk a hang-és emberi címkével ellátott átiratok vagy a kapcsolódó szövegek feltöltésével: mondatok.  A kiejtés testreszabása jelenleg csak en-US és de-DE esetén érhető el. További információ a testreszabásról [itt](how-to-custom-speech.md).

  Területi beállítás | Nyelv | Támogatott | Testreszabható | Tárolótámogatás
 ------|------------|-----------|--------------|--------------
 AR – EG | Arab (Egyiptom), modern standard | ✔️ | ✔️ | ✔️
 AR-SA | Arab (Szaúd-Arábiai) | ✔️ | ✔️ | ❌
 AR-AE | Arab (UAE) | ✔️ | ✔️ | ❌
 AR-KW | Arab (Kuvait) | ✔️ | ✔️ | ❌
 AR-QA | Arab (Katar) | ✔️ | ✔️ | ❌
 CA-ES | katalán | ✔️ | ❌ | ✔️
 da-DK | Dán (Dánia) | ✔️ | ❌ | ✔️
 de-DE | Német (németországi) | ✔️ | ✔️ | ✔️
 EN-AU | Angol (ausztráliai) | ✔️ | ✔️ | ✔️
 en-CA | Angol (Kanada) | ✔️ | ✔️ | ✔️
 en-GB | Angol (Egyesült Királyság) | ✔️ | ✔️ | ✔️
 EN-IN | Angol (India) | ✔️ | ✔️ | ✔️
 EN-NZ | Angol (új-zélandi) | ✔️ | ✔️ | ✔️
 en-US | Angol (Egyesült Államok) | ✔️ | ✔️ | ✔️
 es-ES | Spanyol (Spanyolország) | ✔️ | ✔️ | ✔️
 es-MX | Spanyol (Mexikó) | ✔️ | ✔️ | ✔️
 Fi-FI | Finn (Finnország) | ✔️ | ❌ | ✔️
 fr – CA | Francia (Kanada) | ✔️ | ✔️ | ✔️
 fr – FR | Francia (franciaországi) | ✔️ | ✔️ | ✔️
 Gu – a | Gudzsaráti (indiai) | ✔️ | ✔️ | ❌
 Hi-IN | Hindi (India) | ✔️ | ✔️ | ✔️
 informatikai | Olasz (Olaszország) | ✔️ | ✔️ | ✔️
 ja-JP | Japán (Japán) | ✔️ | ✔️ | ✔️
 ko-KR | Koreai (Korea) | ✔️ | ✔️ | ✔️
 Mr-IN | Marathi (India) | ✔️ | ✔️ | ❌
 NB-nem | Norvég (nyelven) (Norvégia) | ✔️ | ❌ | ✔️
 nl-NL | Holland (Hollandia) | ✔️ | ✔️ | ✔️
 pl-PL | Lengyel (Lengyelország) | ✔️ | ❌ | ✔️
 PT-BR | Portugál (brazíliai) | ✔️ | ✔️ | ✔️
 PT-PT | Portugál (portugáliai) | ✔️ | ✔️ | ✔️
 ru-RU | Orosz (Oroszország) | ✔️ | ✔️ | ✔️
 SV-SE | Svéd (svédországi) | ✔️ | ❌ | ✔️
 ta-be | Tamil (India) | ✔️ | ✔️ | ❌
 te-a | Telugu (India) | ✔️ | ✔️ | ❌
 zh-CN | Kínai (mandarin, egyszerűsített) | ✔️ | ✔️ | ✔️
 ZH-HK | Kínai (kantoni, hagyományos) | ✔️ | ✔️ | ✔️
 zh – TW | Kínai (tajvani mandarin) | ✔️ | ✔️ | ✔️
 th-TH | Thai (Thaiföld) | ✔️ | ❌ | ✔️
 TR-TR | Törökország | ✔️ | ✔️ | ❌


## <a name="text-to-speech"></a>Szövegfelolvasás

A Microsoft Speech SDK és a REST API egyaránt támogatja ezeket a hangokat, amelyek mindegyike egy adott nyelvet és dialektust támogat, amelyet a területi beállítás azonosít.

> [!IMPORTANT]
> A díjszabás a standard, az egyéni és a neurális hangok esetében is változhat. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) oldalt.

### <a name="neural-voices"></a>Neurális hangok

A neurális szövegek és a beszédek egy új, mély neurális hálózatok által vezérelt beszédfelismerési típusú hangszintézis. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket.

A neurális hangokat felhasználhatja az csevegőrobotok-és hangsegédekkel való interakcióra, így természetesebb és vonzó, digitális szövegeket, például e-könyveket készíthet hangoskönyvekbe, és hatékonyabbá teheti az autós navigációs rendszereket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

A neurális hangok és a regionális elérhetőség teljes listájáért lásd: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid hang neve
--------|----------|--------|---------|------------
de-DE | Német (németországi) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | Angol (Egyesült Államok) | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Angol (Egyesült Államok) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaNeural)" | "en-US-JessaNeural"
informatikai | Olasz (Olaszország) | Nő |"Microsoft Server Speech Text to Speech hang (IT-IT, ElsaNeural)" | "IT-IT-ElsaNeural"
zh-CN | Kínai (anyaország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="standard-voices"></a>Standard hangok

Több mint 75 standard hang érhető el több mint 45 nyelven és területi beállításban, amely lehetővé teszi szöveg konvertálását a szintetizált beszédbe. További információ a regionális elérhetőségről: [régiók](regions.md#standard-and-neural-voices).

Területi beállítás | Nyelv | Nem | Teljes szolgáltatásnév-hozzárendelés | Rövid név | Tárolótámogatás
-------|----------|---------|----------|----------|------
AR-EG\* | Arab (Egyiptom) | Nő | "Microsoft Server Speech Text to Speech hang (AR-EG, a és a (z))" | "AR-EG-a (z)" | ✔️
AR-SA | Arab (Szaúd-Arábiai) | Férfi | "Microsoft Server Speech Text to Speech hang (AR-SA, Naayf)" | "AR-SA-Naayf" | ✔️
BG – BG | bolgár | Férfi | "Microsoft Server Speech Text to Speech Voice (BG-BG, Ivan)" | "BG-BG-Ivan" | ✔️
CA-ES | Katalán (Spanyolország) | Nő | "Microsoft Server Speech Text to Speech Voice (CA-ES, HerenaRUS)" | "CA-ES-HerenaRUS" | ✔️
CS-CZ | cseh | Férfi | "Microsoft Server Speech Text to Speech hang (CS-CZ, Jakub)" | "CS-CZ-Jakub" | ✔️
da-DK | dán | Nő | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)" | "da-DK-HelleRUS" | ✔️
de-AT | Német (Austria) | Férfi | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)" | "de-AT-Michael" | ✔️
de-CH | Német (Svájc) | Férfi | "Microsoft Server Speech Text to Speech hang (de-CH, Karsten)" | "de-CH-Karsten" | ✔️
de-DE | Német (németországi) | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)" | "de-DE-Hedda" | ❌
| | | Nő | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)" | "de-DE-HeddaRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo" | ✔️
el-GR | görög | Férfi | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)" | "el-GR-Stefanos" | ✔️
EN-AU | Angol (ausztráliai) | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, Catherine)" | "en-AU-Catherine" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-AU, HayleyRUS)" | "en-AU-HayleyRUS" | ✔️
en-CA | Angol (Kanada) | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, Linda)" | "en-CA-Linda" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-CA, HeatherRUS)" | "en-CA-HeatherRUS" | ✔️
en-GB | Angol (Egyesült Királyság) | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, Susan, Apollo)" | "en-GB-Susan-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-GB, HazelRUS)" | "en-GB-HazelRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-GB, George, Apollo)" | "en-GB-George-Apollo" | ✔️
EN-IE | Angol (Írország) | Férfi | "Microsoft Server Speech Text to Speech hang (en-IE, Sean)" | "en-IE-Sean" | ✔️
EN-IN | Angol (India) | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, Heera, Apollo)" | "en-IN-Heera-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-IN, PriyaRUS)" | "en-IN-PriyaRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-IN, Ravi, Apollo)" | "en-IN-fosztogatás – Apollo" | ✔️
en-US | Angol (Egyesült Államok) | Nő | "Microsoft Server Speech Text to Speech hang (en-US, ZiraRUS)" | "en-US-ZiraRUS" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-US, JessaRUS)" | "en-US-JessaRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, BenjaminRUS)" | "en-US-BenjaminRUS" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (en-US, Guy24kRUS)" | "en-US-Guy24kRUS" | ✔️
es-ES | Spanyol (Spanyolország) |Nő | "Microsoft Server Speech Text to Speech hang (es-ES, Laura, Apollo)" | "es-ES-Laura-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (es-ES, HelenaRUS)" | "es-ES-HelenaRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (es-ES, Pablo, Apollo)" | "es-ES-Pablo-Apollo" | ✔️
es-MX | Spanyol (Mexikó) | Nő | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)" | "es-MX-HildaRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (es-MX, Raul, Apollo)" | "es-MX-Raul-Apollo" | ✔️
Fi-FI | finn | Nő | "Microsoft Server Speech Text to Speech hang (fi-FI, HeidiRUS)" | "fi-FI-HeidiRUS" | ✔️
fr – CA | Francia (Kanada) |Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, Caroline)" | "fr-CA-Caroline" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech Voice (FR-CA, HarmonieRUS)" | "fr-CA-HarmonieRUS" | ✔️
fr – CH | Francia (Svájc)| Férfi | "Microsoft Server Speech Text to Speech hang (FR-CH, Guillaume)" | "fr-CH-Guillaume" | ✔️
fr – FR | Francia (franciaországi)| Nő | "Microsoft Server Speech Text to Speech hang (FR-FR, Julie, Apollo)" | "fr-FR-Julie-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech Voice (FR-FR, HortenseRUS)" | "fr-FR-HortenseRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (FR-FR, Paul, Apollo)" | "fr-FR-Paul-Apollo" | ✔️
ő – IL| Héber (Izrael) | Férfi| "Microsoft Server Speech Text to Speech hang (IT-IL, Asaf)" | "ő-IL-Asaf" | ✔️
Hi-IN | Hindi (India) | Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs, Apollo)" | "Hi-IN-Sánta-Apollo" | ✔️
| | |Nő | "Microsoft Server Speech Text to Speech hang (Hi-IN, kovacs)" | "Hi-IN-kovacs" | ✔️
| | | Férfi | "A Microsoft Server Speech Text to speech hangja (Hi-in, vérképzés)" | "Hi-IN-alapképzés" | ✔️
HR – HR | horvát | Férfi | "Microsoft Server Speech Text to Speech hang (HR-HR, Matej)" | "HR-HR-Mate" | ✔️
hu – HU | magyar | Férfi | "Microsoft Server Speech Text to Speech hang (hu-HU, Szabolcs)" | "hu-HU-Szabolcs" | ✔️
azonosító-azonosító | indonéz| Férfi | "Microsoft Server Speech Text to Speech Voice (ID-ID, Andika)" | "id-ID-Andika" | ✔️
informatikai | olasz | Férfi | "Microsoft Server Speech Text to Speech hang (IT-IT, Cosimo, Apollo)" | "IT-IT-Cosimo-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (IT-IT, LuciaRUS)" | "IT-IT-LuciaRUS" | ✔️
ja-JP | japán | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, Ayumi, Apollo)" | "ja-JP-Ayumi-Apollo" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ja-JP, Ichiro, Apollo)" | "ja-JP-Ichiro-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (ja-JP, HarukaRUS)" | "ja-JP-HarukaRUS" | ✔️
ko-KR | koreai | Nő | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)" | "ko-KR-HeamiRUS" | ✔️
MS-MY | maláj | Férfi | "Microsoft Server Speech Text to Speech hang (MS-MY, AIMA)" | "MS-MY-Susanna" | ✔️
NB-nem | norvég | Nő | "Microsoft Server Speech Text to Speech hang (NB-NO, HuldaRUS)" | "NB-NO-HuldaRUS" | ✔️
nl-NL | holland | Nő | "Microsoft Server Speech Text to Speech Voice (NL-NL, HannaRUS)" | "NL-NL-HannaRUS" | ✔️
pl-PL | lengyel | Nő | "Microsoft Server Speech Text to Speech Voice (pl-PL. PaulinaRUS)" | "pl-PL-PaulinaRUS" | ✔️
PT-BR | Portugál (brazíliai) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-BR, HeloisaRUS)" | "PT-BR-HeloisaRUS" | ✔️
| | | Férfi |"Microsoft Server Speech Text to Speech Voice (PT-BR, Daniel, Apollo)" | "PT-BR-Daniel-Apollo" | ✔️
PT-PT | Portugál (portugáliai) | Nő | "Microsoft Server Speech Text to Speech Voice (PT-PT, HeliaRUS)" | "PT-PT-HeliaRUS" | ✔️
RO-RO | román | Férfi | "Microsoft Server Speech Text to Speech hang (RO-RO, Andrej)" | "RO-RO-Andrej" | ✔️
ru-RU |orosz| Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, Irina, Apollo)" | "ru-RU-Irina-Apollo" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ru-RU, Pavel, Apollo)" | "ru-RU-Pavel-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS | ✔️
sk-SK | szlovák | Férfi | "Microsoft Server Speech Text to Speech Voice (SK-SK, Filip)" | "SK-SK-Filip" | ✔️
SL-SI | szlovén | Férfi | "Microsoft Server Speech Text to Speech Voice (SL-SI, Ladó)" | "SL-SI-Ladó" | ✔️
SV-SE | svéd | Nő | "Microsoft Server Speech Text to Speech Voice (SV-SE, HedvigRUS)" | "SV-SE-HedvigRUS" | ✔️
ta-be | Tamil (India) | Férfi | "Microsoft Server Speech Text to Speech hang (TA-IN, Valluvar)" | "TA-IN-Valluvar" | ✔️
te-a | Telugu (India) | Nő | "Microsoft Server Speech Text to Speech hang (te-IN, chiton)" | "saját | ✔️
th-TH | thai | Férfi | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)" | "th-TH-Pattara" | ✔️
TR-TR | török | Nő | "Microsoft Server Speech Text to Speech hang (TR-TR, SedaRUS)" | "TR-TR-SedaRUS" | ✔️
VI – VN | vietnami | Férfi | "Microsoft Server Speech Text to Speech Voice (VI – VN, an)" | "VI-VN-an" | ✔️
zh-CN | Kínai (anyaország) | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)" | "zh-CN-HuihuiRUS" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)" | "zh-CN-Yaoyao-Apollo" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)" | "zh-CN-Kangkang-Apollo" | ✔️
ZH-HK | Kínai (Hongkong) | Nő | "Microsoft Server Speech Text to Speech hang (ZH-HK, Tracy, Apollo)" | "ZH-HK-Tracy-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech Voice (ZH-HK, TracyRUS)" | "ZH-HK-TracyRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (ZH-HK, Danny, Apollo)" | "ZH-HK-Danny-Apollo" | ✔️
zh – TW | Kínai (Tajvan) | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, Yating, Apollo)" | "zh-TW-Yating-Apollo" | ✔️
| | | Nő | "Microsoft Server Speech Text to Speech hang (zh-TW, HanHanRUS)" | "zh-TW-HanHanRUS" | ✔️
| | | Férfi | "Microsoft Server Speech Text to Speech hang (zh-TW, Zhiwei, Apollo)" | "zh-TW-Zhiwei-Apollo" | ✔️

\* *AR-eg a modern szabványos Arab (MSA) szabványokat támogatja.*

> [!NOTE]
> A beszédfelismerési kérelmekben a teljes szolgáltatásnév-hozzárendelést vagy a rövid hangnevet használhatja.

### <a name="customization"></a>Testreszabás

A hang testreszabása a de-DE, az en-GB, az EN-IN, az en-US, az es-MX, a fr-FR, az IT-IT, a PT-BR és a zh-CN szolgáltatásban érhető el. Válassza ki a megfelelő területi beállítást, amely megfelel az egyéni hangmodell betanításához szükséges betanítási adattípusoknak. Ha például a rögzítési adat angol nyelven van kiválasztva egy brit kiejtéssel, válassza az en-GB lehetőséget.  

> [!NOTE]
> Az egyéni hangon nem támogatjuk a BI-nyelvi Modelles képzést, kivéve a kínai – angol nyelveken. Válassza a "kínai angol nyelvű kétnyelvű" lehetőséget, ha olyan kínai hangvételt szeretne betanítani, amely angolul is tud beszélni. A hangképzés minden területi beállításban egy 2000 + hosszúságú kimondott szöveg adatkészlettel kezdődik, az en-US és a zh-CN kivételével, ahol bármilyen méretű betanítási adatmennyiséggel elkezdheti.

## <a name="speech-translation"></a>Beszédfordítás

A **Speech Translation** API különböző nyelveket támogat a beszédfelismerés és a beszéd – szöveg fordításához. A forrás nyelvének mindig a beszéd-szöveg nyelvi táblából kell származnia. Az elérhető célnyelv attól függnek, hogy a fordítás célja beszéd vagy szöveg. A bejövő beszédet több mint [60 nyelvre](https://www.microsoft.com/translator/business/languages/)lefordíthatja. Ezen nyelvek egy részhalmaza elérhető a [beszédfelismerési szintézishez](language-support.md#text-languages).

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
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fidzsi      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
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
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| Queretaro otomi      | `otq`          |
| román      | `ro`          |
| orosz      | `ru`          |
| Szamoai      | `sm`          |
| szerb (cirill betűs)      | `sr-Cyrl`          |
| Szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| thai      | `th`          |
| Tongai      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech Services-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: beszéd felismerése a c-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
