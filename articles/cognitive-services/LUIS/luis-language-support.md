---
title: Nyelvi támogatás – intelligens HANGFELISMERÉSI
titleSuffix: Azure Cognitive Services
description: A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: a26fa1f64f1ef8ecef8140b1feab89fdd57875fe
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569199"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelvéhez és régiójához támogatása

A LUIS különféle funkciókat a szolgáltatáson belül van. Nem minden funkciója el az azonos nyelven elérhetőek. Ellenőrizze, hogy a nyelvi kulturális környezet céloz meg az Önt érdeklő funkciók támogatottak. LUIS-alkalmazásokon kulturális környezet-specifikus, és nem módosítható, hogy be van állítva.

## <a name="multi-language-luis-apps"></a>A többnyelvű LUIS-alkalmazások

Ha például egy csevegőrobotot többnyelvű LUIS ügyfélalkalmazás van szüksége, több lehetősége van. Ha LUIS támogatja az összes nyelv, az egyes nyelvekhez a LUIS-alkalmazások fejlesztése. Minden egyes LUIS alkalmazás rendelkezik egy egyedi Alkalmazásazonosító, és a végpont napló. Ha meg kell adnia a nyelv értelmezése nem támogatja a LUIS, nyelvet használhatja [Microsoft Translator API](../Translator/translator-info-overview.md) lefordítani az utterance (kifejezés) egy támogatott nyelvre, küldje el az utterance (kifejezés), a LUIS-végponthoz, és megkapta az eredményül kapott értékeket.

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS tisztában van azzal a kimondott szöveg a következő nyelveken:

| Nyelv |Területi beállítás  |  Előre összeállított tartomány | Előre összeállított entitások | A kifejezés javaslatok | **[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Vélemények és<br>A kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol |`en-US` | ✔ | ✔  |✔|✔|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| holland |`nl-NL` |-|  -   |-|✔|
| francia (Franciaország) |`fr-FR` |-| ✔ |✔ |✔|
| francia (Kanada) |`fr-CA` |-|   -   |-|✔|
| német |`de-DE` |-| ✔ |✔ |✔|
| olasz |`it-IT` |-| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Csak a kulcsfontosságú kifejezések|
| koreai |`ko-KR` |-|   -   |-|Csak a kulcsfontosságú kifejezések|
| portugál (brazíliai) |`pt-BR` |-| ✔ |✔ |nem minden alárendelt kulturális környezetek|
| spanyol (spanyolországi) |`es-ES` |-| ✔ |✔|✔|
| spanyol (Mexikó)|`es-MX` |-|  -   |✔|✔|


Nyelvi támogatás esetében eltérő [előre összeállított entitások](luis-reference-prebuilt-entities.md) és [előre összeállított tartományok](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>* Kínai támogatja a jegyzeteket

 - Az a `zh-cn` kulturális környezet, LUIS vár helyett a hagyományos karakterkészlet egyszerűsített kínai karakterkészlet.
 - Leképezések, az entitások, a funkciók és a reguláris kifejezések nevei a kínai vagy latin karaktereket lehet.
 - Tekintse meg a [előre összeállított tartományok referencia ](luis-reference-prebuilt-domains.md) , amelyen előre összeállított tartományok használata támogatott, az információ a `zh-cn` kulturális környezet.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>* Japán támogatja a jegyzeteket

 - Mivel a LUIS nem biztosít szintaktikai analysis, és nem lesz a következő Keigo és az informális Japán közötti különbségek megértése, kell foglalják magukban képzési példaként használt alkalmazásait formalitást különböző szintjeit.
     - でございます nem ugyanaz, mint です.
     - です nem ugyanaz, mint だ.

### <a name="text-analytics-support-notes"></a>** A text analytics támogatja a jegyzeteket
Szövegelemzés tartalmaz keyPhrase előre létrehozott entitás- és vélemények elemzése. Csak portugál tápfolyadékok cseréjét esetében támogatott: `pt-PT` és `pt-BR`. Minden más kultúrák támogatottak az elsődleges kulturális környezet szintjén. További információ a Text Analytics [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Beszédfelismerési API támogatott nyelvek
Tekintse meg a beszédfelismerés [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) Speech Diktálás üzemmód nyelveken.

### <a name="bing-spell-check-supported-languages"></a>A Bing Spell Check, támogatott nyelvek
Tekintse meg a Bing Spell Check [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) támogatott nyelvek és állapot listáját.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy külső szavak egy alkalmazásban
Az a `en-us` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb angol szavak, beleértve a szleng felismerését. Az a `zh-cn` kulturális környezet, LUIS megtanulja különbséget tenni a legtöbb kínai karaktereket. Egy ritka kifejezése használatakor `en-us` karakter vagy `zh-cn`, és láthatja, hogy a LUIS úgy tűnik, nem tesz különbséget az adott szó vagy karakter lehet, adja hozzá az adott szó vagy a karakter egy [kifejezéslista funkció](luis-how-to-add-features.md). Például szavak kívül az alkalmazás –, hogy külső szavak--kulturális környezete kell adni egy kifejezéslista szolgáltatás. Ez a kifejezés a lista nem cserélhető, annak jelzésére, hogy a ritka szavak készletét képezi egy osztályt, amely a LUIS kell további ismeri fel, de azok nem szinonimák vagy cserélhető, egymással kell megjelölni.

### <a name="hybrid-languages"></a>Hibrid nyelvek
Hibrid nyelvek szó azoktól, például az angol és kínai két kulturális környezetek egyesítése. Ezeken a nyelveken nem támogatottak a LUIS, mivel az alkalmazás egyetlen kulturális környezetet alapul.

## <a name="tokenization"></a>A jogkivonatok
Machine learning alkalmazásához, LUIS bontja az utterance (kifejezés) [jogkivonatok](luis-glossary.md#token) kulturális környezet alapján.

|Nyelv|  minden szóközt vagy különleges karaktert | karakter szint|összetett szavakat|[tokenekre entitást adott vissza](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|kínai||✔||✔|
|holland|||✔|✔|
|Angol (en-us)|✔ ||||
|Francia (fr-FR)|✔||||
|Francia (fr, CA)|✔||||
|német|||✔|✔|
|olasz|✔||||
|japán||||✔|
|koreai||✔||✔|
|portugál (brazíliai)|✔||||
|Spanyol (es-ES)|✔||||
|Spanyol (es – MX)|✔||||
