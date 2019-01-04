---
title: Nyelvi támogatás – Beszédszolgáltatási API Használatába
titleSuffix: Azure Cognitive Services
description: Az Azure Speech Services számos nyelv támogatása a hang-szöveg és a szöveg-beszéd átalakítás, beszédalapú fordítási együtt. Ez a cikk a szolgáltatás által nyelvi támogatás átfogó listáját tartalmazza.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 27b952fb1a901ff716f25cc7d512f860f639fb02
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632618"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Nyelvéhez és régiójához támogatása Beszédszolgáltatási API Használatába

Beszédszolgáltatások különböző funkciók különböző nyelvekhez támogatottak. A következő táblázat összefoglalja a nyelvi támogatás.

## <a name="speech-to-text"></a>Speech-to-text

A Microsoft beszédfelismerési API-t a következő nyelveket támogatja. Az egyes nyelvekhez testreszabás szinteken érhetők el.

  Kód | Nyelv | [Akusztikai betanítás](how-to-customize-acoustic-models.md) | [Nyelvi órái](how-to-customize-language-model.md) | [Adaptációs kiejtése](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar – példa | Arab (Egyiptom), a modern standard | Nem | Igen | Nem
 CA-ES | Katalán | Nem | Nem | Nem
 da-DK | dán (Dánia) | Nem | Nem | Nem
 de-DE | német (Németország) | Igen | Igen | Nem
 en-Ausztrália | angol (Ausztrália) | Nem | Igen | Igen
 en-hitelesítésszolgáltató | angol (Kanada) | Nem | Igen | Igen
 en-GB | angol (Egyesült Királyság) | Nem | Igen | Igen
 en-IN | Angol (India) | Igen | Igen | Igen
 en-NZ | angol (Új-Zéland) | Nem | Igen | Igen  
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
 zh-HK | Kínai (Mandarin, hagyományos) | Nem | Igen | Nem
 zh-TW | Kínai (tajvani Mandarin) | Nem | Igen | Nem
 cs-EDIK | thai (Thaiföld) | Nem | Nem | Nem


## <a name="text-to-speech"></a>Szövegfelolvasás

A szöveg-hang transzformációs REST API támogatja ezeket beszédhangot, amelyek mindegyike támogatja egy adott nyelvhez és dialect, területi beállítás azonosítjuk.

### <a name="neural-voices-preview"></a>Neurális beszédhangot (előzetes verzió)

Neurális szöveg-hang transzformációs egy új típusú beszédszintézishez neurálishálózat működteti. Neurális hangalapú használatakor szintetizált szinte megkülönböztetni a az emberi felvételek.

Győződjön meg arról, látás- és a virtuális asszisztensek interakció természetesebb, és vonzó, digitális szövegek, például az e-könyvek átalakítása audiobooks, és javíthatja a autós navigációs rendszerek Neurális beszédhangot használható. Emberszerű természetes prosody és egyértelmű tagolódását szavak Neurális beszédhangot jelentősen csökkentheti figyel-e fáradás amikor AI rendszerek a felhasználók használják.

Neurális beszédhangot és a régiónkénti rendelkezésre állás teljes listáját lásd: [régiók](regions.md#neural-voices).

| Területi beállítás | Nyelv | Nem | A felhasználónév-leképezés|
|--------|----------|---------|--------------------|
| en-US | Angol (Egyesült Államok) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, GuyNeural)" |
| en-US | Angol (Egyesült Államok) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaNeural)" |

### <a name="standard-voices"></a>Standard beszédhangot

Több mint 75 standard beszédhangot több mint 45 nyelvet és a területi beállításokat, amelyek lehetővé teszik, hogy a szöveg átalakítása beszéddé szintetizált érhetők el. Régiónkénti rendelkezésre állás kapcsolatos további információkért lásd: [régiók](regions.md#standard-voices).

Területi beállítás | Nyelv | Nem | A felhasználónév-leképezés
-------|----------|---------|--------------------
ar – példa\* | arab (Egyiptom) | Nő | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (ar-működtek az Adatbázisok, Hoda)"
ar-SA | arab (Szaúd-Arábia) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ar-SA, Naayf)"
bg-BG | bolgár | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (bg-BG, Ivan)"
CA-ES | Katalán | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ca-ES, HerenaRUS)"
cs-CZ | cseh | Férfi | "A Microsoft Server hang-szöveg, beszédfelismerés, beszédfelismerési (cs-CZ, Jakub)"
da-DK | dán | Nő | "A Microsoft Server beszéd szöveg Speech Voice (da-DK, HelleRUS)"
Németország-AT | német (Ausztria) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-AT, Michael)"
Németország – CH | német (Svájc) | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Németország-CH, Karsten)"
de-DE | német (Németország) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, Hedda)"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, HeddaRUS)"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (de-DE, lengyel, Apollo)"
el-GR | görög | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (el-GR, Stefanos)"
en-Ausztrália | angol (Ausztrália) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, Catherine)"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-Ausztrália, HayleyRUS)"
en-hitelesítésszolgáltató | angol (Kanada) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, Linda)"
| | | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-CA, HeatherRUS)"
en-GB | Angol (Egyesült Királyság) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, Susan, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, HazelRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-GB, George, Apollo)"
en-IE | angol (Írország) |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IE, Sean)"
en-IN | Angol (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Heera, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, PriyaRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-IN, Ravi, Apollo)"
en-US | Angol (Egyesült Államok) |Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, ZiraRUS)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, JessaRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, BenjaminRUS)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Jessa24kRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (en-US, Guy24kRUS)"
es-ES | spanyol (spanyolországi) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Laura, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, HelenaRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es-ES, Pablo, Apollo)"
es-MX | spanyol (Mexikó) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, HildaRUS)"
| | | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (es – MX, Raul, Apollo)"
fi-FI | finn | Nő | "A Microsoft Server beszéd szöveg Speech Voice (fi-FI, HeidiRUS)"
FR-hitelesítésszolgáltató | francia (Kanada) |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, Caroline)"
| | | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CA, HarmonieRUS)"
FR-CH | francia (Svájc)|Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-CH, Guillaume)"
FR-FR | francia (Franciaország)|Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Ágnes, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, HortenseRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (fr-FR, Paul, Apollo)"
He-IL| héber (Izrael) | Férfi| "A Microsoft Server beszéd szöveg Speech Voice (he-IL, Asaf)"
Üdvözöljük – India | hindi (India) | Nő | "A Microsoft Server beszéd szöveg Speech Voice (hi-IN, Kalpana, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Kalpana)"
| | | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (Üdvözlöm-IN, Hemant)"
hr-HR | horvát | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (hr-HR, Matej)"
hu-HU | magyar | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (hu-HU, Szabolcs)"
ID-Azonosítóját | indonéz| Férfi | "A Microsoft Server beszéd szöveg Speech Voice (id-azonosító, Andika)"
it-IT | olasz |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, Cosimo, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (it-IT, LuciaRUS)"
ja-JP | japán |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ayumi, Apollo)"
| | |Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, Ichiro, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ja-JP, HarukaRUS)"
ko-KR | koreai |Nő | "A Microsoft Server beszéd szöveg Speech Voice (ko-KR, HeamiRUS)"
MS-saját | maláj | Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ms-saját, Rizwan)"
nb-NO | norvég | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nb-NO, HuldaRUS)"
NL-NL | holland | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (nl-NL, HannaRUS)"
pl-PL | lengyel | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pl-PL, PaulinaRUS)"
pt-BR | portugál (brazíliai) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, HeloisaRUS)"
| | |Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-BR, Daniel, Apollo)"
PT-PT | portugál (általános) | Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (pt-PT, HeliaRUS)"
ro-RO | román | Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ro-RO, Andrei)"
ru-RU |orosz| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Irina, Apollo)"
| | |Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, Pavel, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (ru-RU, EkaterinaRUS)"
SK-SK | szlovák|Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sk-SK, Filip)"
sl SI | szlovén|Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (sl SI, Lado)"
SV-SE | svéd|Nő | "A Microsoft Server beszéd szöveg Speech Voice (sv-SE, HedvigRUS)"
TA-India | tamil (India) |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (ta-IN, Valluvar)"
Te-India | telugu (India) |Nő | "A Microsoft Server beszéd szöveg Speech Voice (te-IN, Chitra)"
cs-EDIK | thai|Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (cs-EDIK, Pattara)"
tr-TR |török| Nő | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (tr-TR, SedaRUS)"
vi-VN | vietnami|Férfi | "A Microsoft Server beszéd szöveg-beszéd átalakítás Voice (vi-VN, egy)"
zh-CN | Kínai (Spanyolország)|Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, HuihuiRUS)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Yaoyao, Apollo)"
| | |Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | kínai (Hongkong)|Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Tracy, Apollo)"
| | |Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK TracyRUS)"
| || Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-HK Danny, Apollo)"
zh-TW | kínai (tajvani)|Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Yating, Apollo)"
| || Nő | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, HanHanRUS)"
| || Férfi | "A Microsoft Server beszéd szöveg Speech Voice (zh-TW, Zhiwei, Apollo)"

\* *ar Például támogatja a Modern Standard arab (MSA).*

### <a name="customization"></a>Testreszabás

Hangalapú testreszabási az Amerikai angol (en-US) kontinens kínai (zh-CN), francia (fr-FR), német (de-DE) és olasz (it-IT) érhető el.

> [!NOTE]
> Francia, német, olasz és hang képzési elindítja egy adatkészlet 2000 + kimondott szöveg. Angol nyelvű kínai kétnyelvű modellek is támogatottak egy kezdeti be, mint 2000 kimondott szöveg.

## <a name="speech-translation"></a>Tolmácsolás

A **beszédalapú fordítási** API különböző nyelveket támogat a speech beszéd és hang-szöveg transzformációs fordítás. A Forrásnyelv mindig a hang-szöveg transzformációs nyelvi táblából kell származnia. A cél elérhető nyelvek attól függ,-e a fordítási cél a speech vagy szöveget. Bejövő speech be, előfordulhat, hogy fordítása több mint [60 nyelvet](https://www.microsoft.com/translator/business/languages/). Ezek egy részét érhetők el a [beszédszintézishez](language-support.md#text-languages).

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

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
