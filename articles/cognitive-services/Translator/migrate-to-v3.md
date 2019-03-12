---
title: V3 – Translator Text API áttelepítése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan V2 át a Translator Text API v3-as.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 89a97b583407b448f296b1a54f5aabbf825b8b65
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537745"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 V3 áttelepítése

> [!NOTE]
> V2 elavulttá 2018. április 30., és a 2019. április 30. ezt megszüntetjük.

> A Microsoft Translator Hub jén kivonjuk a forgalomból 2019. április 30. [Fontos állapotTelepítési információit és a dátumok](https://www.microsoft.com/translator/business/hub/).  

A Microsoft Translator csapat kiadott 3-as verziójú (V3), a Translator Text API. Ebben a kiadásban új funkciók, a elavult módszerek és a egy új formátum küld és fogad adatokat a Microsoft Translator szolgáltatás tartalmaz. Ez a dokumentum módosítása alkalmazások általi használatát a v3-as információkat szolgáltat. 

Ez a dokumentum végén, hogy további hasznos hivatkozásokat tartalmaz.

## <a name="summary-of-features"></a>Szolgáltatások összefoglalása

* Nincs nyomkövetés - a v3-as nyomkövetés-mellőzési minden tarifacsomagja az Azure Portalon vonatkozik. Ez a szolgáltatás azt jelenti, hogy az V3 API-nak elküldött szöveg nélkül menti a rendszer a Microsoft által.
* JSON - XML JSON váltotta fel. A szolgáltatásnak küldött és az szolgáltatásból származó összes adat JSON formátumban van.
* Egyetlen kérelem - a fordítására metódust a támogatott nyelveket cél több "to" nyelv fordítási egyetlen kérésbe fogad el. Egyetlen kérelem lehet például a "from" angol és a "to" német, spanyol és japán nyelven vagy bármely más csoport nyelveken.
* Kétnyelvű szótár - kétnyelvű szótár metódus az API-ra bővült. Ez a módszer "keresési" és "mintái" tartalmazza.
* Átbetűzés - transliterate metódus az API-ra bővült. Ez a módszer átalakítja a szavak és a egy parancsfájl (pl. a mondatok Arab) egy másik parancsfájlba (például: Latin betűs).
* Nyelvek – egy új "nyelv" módszer nyelvi információk ennek a JSON formátumban, a 'fordítása', "szótár" és a "átbetűzés" való használatra.
* Új fordítás – az új képességekkel bővült a "fordítása" metódus támogatja, amelyek korábban a V2 API külön módszerként funkcióit. Ez például akkor TranslateArray.
* Beszéljen metódus – szöveg-beszéd átalakítás funkció már nem támogatott a Microsoft Translator API-t. Szöveg-beszéd átalakítás funkció érhető el a [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Az alábbi listában szereplő V2 és V3 módszerek a v3-as módszerek és API-kat biztosít a funkciót, melyet a V2 azonosítja.

| V2 API-metódus   | V3 API-kompatibilitás |
|:----------- |:-------------|
| `Translate`     | [Fordítása](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Fordítása](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Nyelvek](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Nyelvek](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Észlelése](reference/v3-0-detect.md)         |
| `DetectArray`     | [Észlelése](reference/v3-0-detect.md)         |
| `AddTranslation`     | A szolgáltatás már nem támogatott.       |
| `AddTranslationArray`    | A szolgáltatás már nem támogatott.          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | A szolgáltatás már nem támogatott.         |
| `GetTranslationsArray`      | A szolgáltatás már nem támogatott.         |

## <a name="move-to-json-format"></a>Helyezze át, JSON-formátumban

A Microsoft Translator Text fordítási V2 az elfogadott és XML-formátumú adatokat adott vissza. V3 API használatával küldött és fogadott összes adatot JSON formátumban vannak. XML már elfogad vagy V3 adja vissza.

Ez a módosítás érinti a V2 szöveg fordítási API-hoz írt alkalmazások számos aspektusát. Példa: A nyelvek API szövegfordítás, átbetűzésű és a két szótár módszerek nyelvi adatait adja vissza. Az összes módszer egyetlen hívásával kapcsolatos összes nyelv kérheti, vagy külön-külön tanúsítványkérelmeket.

A nyelvek metódus nem igényel hitelesítést; a következő hivatkozásra kattintva a v3-as JSON összes nyelvi információk jelennek meg:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, szótár, átbetűzésű](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Hitelesítési kulcs

A hitelesítési kulcs a v2-ben használja a v3-as fogadható el. Nem kell egy új előfizetést. Akkor fog tudni V2 és V3 kombinálhatók az alkalmazásokban az yearlong áttelepítés idején megkönnyíti, hogy új verzióira, miközben továbbra is végzi az áttelepítést a V2-XML V3-JSON.

## <a name="pricing-model"></a>Díjszabási modell

A Microsoft Translator V3 V2 díjszabása volt; ugyanúgy díjszabása karakterenként beleértve a szóközöket. Az új szolgáltatások a v3-as hajtson végre néhány módosítást karakterek vannak számlázásban is megjelenik.

| V3 metódus   | Karakterek számlázásban is megjelenik |
|:----------- |:-------------|
| `Languages`     | Egyetlen elküldött karakter, nincs számításba, díjmentesen.          |
| `Translate`     | Száma alapján hány karaktert elküldése fordítás, és hány nyelvek a karakterek fordítja. elküldött 50 karakter hosszú lehet, és 5 nyelvek kért 50 x 5.           |
| `Transliterate`     | Számoljuk el átbetűzésű-karakterek száma.         |
| `Dictionary lookup & example`     | Számoljuk el szótár keresés és a példák-karakterek száma.         |
| `BreakSentence`     | Díjmentes.       |
| `Detect`     | Díjmentes.      |

## <a name="v3-end-points"></a>V3 végpontok

Globális

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API szöveg fordítások módszerek

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitási és testreszabás

> [!NOTE] 

> A Microsoft Translator Hub jén kivonjuk a forgalomból 2019. április 30. [Fontos állapotTelepítési információit és a dátumok](https://www.microsoft.com/translator/business/hub/).   

A Microsoft Translator V3 Neurális gépi fordítás alapértelmezés szerint használ. Ezért nem használható a Microsoft Translator hubbal. A Translator Hub csak az örökölt statisztikai gépi fordítási támogatja. Neurális fordítással testreszabásának már elérhető az egyéni translatorral. [További tudnivalók a Neurális gépi fordítás testreszabása](custom-translator/overview.md)

API v3-as szöveggel Neurális fordítással nem támogatja a standard szintű kategóriák (SMT, beszédfelismerés, technológiai, generalnn) használatát.

| |Végpont|    GDPR-megfelelőség processzor|  Use Translator Hub| Egyéni Translator (előzetes verzió) használata|
|:-----|:-----|:-----|:-----|:-----|
|Translator Text API-Version 2| api.microsofttranslator.com|    Nem  |Igen    |Nem|
|Translator Text API-verzió 3| api.cognitive.microsofttranslator.com|  Igen|    Nem| Igen|

**Translator Text API-verzió 3**
* Az általánosan elérhető és teljes mértékben támogatott.
* GDPR-kompatibilis, a processzor, amely eleget tesz ISO 20001 és 20018, valamint SOC 3 minősítési követelményeinek. 
* Lehetővé teszi a Neurális hálózat fordítási rendszerek egyéni Translator (előzetes verzió), az új Translator NMT testreszabási funkcióról a testreszabott meghívásához. 
* Nem biztosít a Microsoft Translator Hub használatával létrehozott egyéni fordítási rendszerekhez való hozzáférést.

A Translator Text API 3-as verziójához használja a api.cognitive.microsofttranslator.com végpont használata.

**Translator Text API-Version 2**
* Elavult. A 2019. április 30. ezt megszüntetjük. 
* Nem felel meg az összes ISO 20001,20018 és a SOC 3 hardvertanúsítvány követelményeit. 
* Nem teszi lehetővé a Neurális hálózat fordítási rendszerek a Translator testreszabási funkcióval testreszabott meghívásához.
* A Microsoft Translator Hub használatával létrehozott egyéni fordítási rendszerekhez való hozzáférést biztosít.
* A Translator Text API 2-es verzióját használja a api.microsofttranslator.com végpont használata.

Nincs a fordító API-verzió létrehoz egy rekordot a fordítások. A fordítások soha nem bárkivel vannak megosztva. További információ a [Translator nyomkövetés-mellőzési](https://www.aka.ms/NoTrace) weblapon.

## <a name="links"></a>Hivatkozások

* [A Microsoft adatvédelmi szabályzata](https://privacy.microsoft.com/privacystatement)
* [A Microsoft Azure – jogi információk](https://azure.microsoft.com/support/legal)
* [Online Services Terms](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [V3.0 dokumentáció megtekintése](reference/v3-0-reference.md)
