---
title: Támogatja a honosítás LUIS alkalmazások használata az Azure-ban |} Microsoft Docs
description: Ismerje meg a nyelveket, amelyek LUIS támogatja.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: cahann
ms.openlocfilehash: 1eabc01ee07f8791680738a156471e3efe2c44ff
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349949"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>Kulturális környezet-specifikus LUIS alkalmazásokban ismertetése

Egy LUIS app kulturális környezet függ, és beállítása után már nem módosítható. 

## <a name="multi-language-luis-apps"></a>Több nyelven LUIS alkalmazások
Ha például egy chatbot beszélő LUIS ügyfélalkalmazás van szüksége, több lehetősége van. Ha LUIS támogatja az összes nyelven, ki kell dolgoznia egy LUIS alkalmazást az egyes nyelvekhez. Minden LUIS alkalmazás rendelkezik, egy egyedi Azonosítót, és a végpont naplót. Ha meg kell adni a nyelvi ismertetése LUIS nem támogatja, nyelvet használhat [Microsoft Translator API](../Translator/translator-info-overview.md) egy támogatott nyelvre utterance fordításához küldje el a LUIS végpontnak utterance, és fogadni a eredményül kapott eredmények.

## <a name="languages-supported"></a>Támogatott nyelvek
LUIS megértette utterances a következő nyelveken:


| Nyelv |Területi beállítás  |  Előre elkészített tartomány | Előre elkészített entitás | Kifejezés javaslatok | **[Szövegelemzések](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| Kanadai francia |`fr-CA` |-|   -   |-|✔|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| holland |`nl-NL` |-|  -   |-|✔|
| francia (Franciaország) |`fr-FR` |-| ✔ |✔ |✔|
| német |`de-DE` |-| ✔ |✔ |✔|
| olasz |`it-IT` |-| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Csak a kulcsfontosságú kifejezés|
| koreai |`ko-KR` |-|   -   |-|Csak a kulcsfontosságú kifejezés|
| portugál (brazíliai) |`pt-BR` |-| ✔ |✔ |nem minden alárendelt kulturális környezetek|
| spanyol (spanyolországi) |`es-ES` |-| ✔ |✔|✔|
| spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|


Nyelvi támogatás változó [előre elkészített entitások](luis-reference-prebuilt-entities.md) és [előre elkészített tartományok](luis-reference-prebuilt-domains.md). 

### <a name="chinese-support-notes"></a>* Kínai () támogatja a jegyzeteket

 - Az a `zh-cn` kulturális környezet, LUIS vár az egyszerűsített kínai karakterkészletet helyett a hagyományos karakterkészlet.
 - A leképezések, entitásokat, szolgáltatások és reguláris kifejezések nevei kínai vagy latin karaktereket lehet.
 - Tekintse meg a [előre elkészített tartományok hivatkozás ](luis-reference-prebuilt-domains.md) információt, amelyen előre elkészített tartományok támogatottak a `zh-cn` kulturális környezet.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japán támogatja a jegyzeteket

 - LUIS nem szintaktikai elemzés, ezért nem lesz a következő Keigo és kötetlen Japán közötti különbségek megértése, mert az alkalmazások példák mint formalitást különböző szintjei tartalmaznia kell. 
     - でございます megegyezik nem です. 
     - です megegyezik nem だ. 

### <a name="text-analytics-support-notes"></a>** Szövegelemzések támogatja a jegyzeteket
Csak portugál támogatott tápfolyadékok cseréjét: `pt-PT` és `pt-BR`. Minden más országban használható elsődleges kulturális környezet szinten támogatottak. További tudnivalók Szövegelemzések [támogatott nyelv](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). 

### <a name="speech-api-supported-languages"></a>Diktálásfelismerési API támogatott nyelvek
Tekintse meg a beszédfelismerés [támogatott nyelv](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) beszéd Diktálás mód nyelvekhez.

### <a name="bing-spell-check-supported-languages"></a>Bing helyesírás-ellenőrzés támogatott nyelvek
Bing helyesírás-ellenőrzés lásd [támogatott nyelv](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) támogatott nyelvek és állapot listáját.

## <a name="rare-or-foreign-words-in-an-application"></a>Egy alkalmazás ritka vagy külső szavakat
Az a `en-us` kulturális környezet, LUIS megtanulja a legtöbb angol szavak, beleértve a zsargon megkülönböztetésére. Az a `zh-cn` kulturális környezet, LUIS megtanulja segítségével különböztetheti meg egymástól a legtöbb kínai karaktereket. Ha ritka szó `en-us` vagy karakter `zh-cn`, és meggyőződött arról, hogy LUIS úgy tűnik, nem különbözteti meg a szó vagy karakter, adhat hozzá, hogy a word vagy a karakter egy [kifejezéslista szolgáltatás](luis-how-to-add-features.md). Például a kulturális környezet, az alkalmazás – Ez azt jelenti, hogy külső szavak – kívül szavak kell adni egy kifejezéslista szolgáltatás. A kifejezéslista nem cserélhető, annak jelzésére, hogy a ritka szavak készlete képezi egy osztály, amely LUIS kell bemutatják, hogyan ismeri fel, de nincsenek szinonimák vagy cserélhető egymás mellett kell megjelölni.

### <a name="hybrid-languages"></a>Hibrid nyelvek
Hibrid nyelvek két kulturális környezetek, például az angol és kínai szavak össze. Ezeken a nyelveken nem támogatottak a LUIS, mert egy alkalmazás egy egyetlen kulturális környezet alapul.

## <a name="tokenization"></a>Jogkivonat-létrehozási
Gépi tanulás végrehajtásához LUIS bontja egy utterance [jogkivonatok](luis-glossary.md#token) kulturális környezet alapján. 

|Nyelv|  minden szóközt vagy speciális karakter | karakter szint|összetett szavakat|[Visszaadott tokenekre entitás](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|kínai||✔||✔|
|holland|||✔|✔|
|Angol (en-us)|✔ ||||
|Francia (fr-FR)|✔||||
|Francia (fr-CA)|✔||||
|német|||✔|✔|
|olasz|✔||||
|japán||||✔|
|koreai||✔||✔|
|portugál (brazíliai)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es MX)|✔||||

 