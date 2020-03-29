---
title: Áttelepítés a V3-ra – Fordítószöveg API
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Translator Text API V2-ről V3-ra való áttelepítésének lépéseit ismerteti.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837312"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Fordító szöveg API V2-V3 áttelepítés

> [!NOTE]
> 2018. április 30-án a V2 elavult. Kérjük, telepítse át alkalmazásait a V3-ba, hogy kihasználja a kizárólag a V3-ban elérhető új funkciókat.
> 
> 2019. május 17-én a Microsoft Translator Hub megszűnik. [Fontos áttelepítési információk és dátumok megtekintése.](https://www.microsoft.com/translator/business/hub/)  

A Microsoft Translator csapata kiadta a Translator Text API 3-as verzióját (V3). Ez a kiadás új szolgáltatásokat, elavult módszereket és új formátumot tartalmaz a Microsoft Translator Service szolgáltatásba történő adatküldéshez és fogadáshoz. Ez a dokumentum a V3-as alkalmazások módosításához nyújt tájékoztatást. 

A dokumentum vége hasznos hivatkozásokat tartalmaz, amelyek további információval rendelkeznek.

## <a name="summary-of-features"></a>A szolgáltatások összefoglalása

* Nincs nyomkövetés – A V3-ban nincs nyomkövetés az Azure Portalon lévő összes tarifacsomagra vonatkozik. Ez a funkció azt jelenti, hogy a Microsoft nem menti a V3 API-ba küldött szöveget.
* A JSON – XML helyébe a JSON lép. A szolgáltatásnak küldött és a szolgáltatástól kapott összes adat JSON formátumú.
* Több célnyelv egyetlen kérelemben – A Fordítás metódus több "a" nyelvre fogad el fordítást egyetlen kérelemben. Egyetlen kérelem lehet például "angolról és "a" németre, spanyolra és japánra, vagy bármely más nyelvcsoportra.
* Kétnyelvű szótár – Az API-hoz kétnyelvű szótármódszer került hozzáadásra. Ez a módszer magában foglalja a "lookup" és a "examples".
* Transzlitert - Az API-hoz egy transzkitátszámítási módszer került hozzáadásra. Ez a módszer a szavakat és a mondatokat egy parancsfájlban (pl. arab) egy másik írásba (pl. latin).
* Nyelvek - Az új "nyelvek" módszer json formátumban biztosítja a nyelvi információkat a "fordítás", a "szótár" és a "transzliterát" metódusokkal való használatra.
* Új fordítás - Új képességek lettek hozzáadva a "fordítás" módszerhez, hogy a V2 API-ban lévő egyes funkciókat külön módszerként támogassák. Erre példa a TranslateArray.
* Kiolvasási módszer – A Szövegfelolvasás funkció már nem támogatott a Microsoft Translator API-ban. A szövegfelolvasás funkció a [Microsoft Speech Service szolgáltatásban](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)érhető el.

A V2 és V3 metódusok alábbi listája azonosítja azokat a V3-metódusokat és API-kat, amelyek biztosítják a V2-hez megadott funkciókat.

| V2 API-módszer   | V3 API-kompatibilitás |
|:----------- |:-------------|
| `Translate`     | [Fordítás](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Fordítás](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Nyelvek](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Nyelvek](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft beszédszolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft beszédszolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Kinyomoz](reference/v3-0-detect.md)         |
| `DetectArray`     | [Kinyomoz](reference/v3-0-detect.md)         |
| `AddTranslation`     | A szolgáltatás már nem támogatott       |
| `AddTranslationArray`    | A szolgáltatás már nem támogatott          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | A szolgáltatás már nem támogatott         |
| `GetTranslationsArray`      | A szolgáltatás már nem támogatott         |

## <a name="move-to-json-format"></a>Ugrás JSON formátumra

A Microsoft Translator Text Translation V2 elfogadta és visszaadta az adatokat XML formátumban. A V3-ban az API-val küldött és fogadott összes adat JSON formátumban van. Az XML a továbbiakban nem lesz elfogadva és nem lesz visszaküldve a V3-ban.

Ez a módosítás a V2 szövegfordítási API-hoz írt alkalmazás több aspektusát érinti. Példaként: A Nyelvek API nyelvi információkat ad vissza a szövegfordításhoz, az átíráshoz és a két szótármetódushoz. Egy hívásban az összes metódushoz kérhet minden nyelvi információt, vagy egyenként is kérheti őket.

A nyelvek metódus nem igényel hitelesítést; az alábbi linkre kattintva láthatja a V3 összes nyelvi információját a JSON-ban:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,szótár,átírás](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Hitelesítési kulcs

A V2-hez használt hitelesítési kulcs a V3-hoz lesz elfogadva. Nem kell új előfizetést beszereznie. Az egész éves áttelepítési időszakban a V2 és a V3 keverésével az alkalmazásokban is eltud majd havazóba lépni, így könnyebben kiadhatja az új verziókat, miközben még mindig a V2-XML-ről a V3-JSON-ra vált.

## <a name="pricing-model"></a>Árképzési modell

A Microsoft Translator V3 ára ugyanúgy V2 volt az ára; karakterenként, szóközöket is beleértve. A V3 új funkciói módosítják, hogy milyen karaktereket számít a számlázás.

| V3 módszer   | Számlázáshoz megszámlált karakterek |
|:----------- |:-------------|
| `Languages`     | Nincs beküldött karakter, egyik sem számít, nincs díj.          |
| `Translate`     | A darabszám attól függ, hogy hány karaktert küld el a fordításra, és hány nyelvre fordítják le a karaktereket. 50 karakter beküldött, és 5 nyelv kért lesz 50x5.           |
| `Transliterate`     | Az átírásra beküldött karakterek számát a szám megszámolja.         |
| `Dictionary lookup & example`     | A szótári keresményre küldött karakterek és példák száma megszámlálva.         |
| `BreakSentence`     | Nincs díj.       |
| `Detect`     | Nincs díj.      |

## <a name="v3-end-points"></a>V3 végpontok

Globális

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API szövegfordítási módszerek

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitás és testreszabás

> [!NOTE]
> 
> 2019. május 17-én a Microsoft Translator Hub megszűnik. [Fontos áttelepítési információk és dátumok megtekintése.](https://www.microsoft.com/translator/business/hub/)   

A Microsoft Translator V3 alapértelmezés szerint neurális gépi fordítást használ. Így nem használható a Microsoft Translator Hubbal. A Translator Hub csak a régebbi statisztikai gépi fordítást támogatja. Testreszabás a neurális fordítás már elérhető az egyéni fordító. [További információ a neurális gépi fordítás testreszabásáról](custom-translator/overview.md)

Neurális fordítás a V3 szöveges API-val nem támogatja a szabványos kategóriák (SMT, beszéd, tech, generalnn) használatát.

| |Végpont|    GDPR processzormegfelelőség|  A Fordítóközpont használata| Egyéni fordító használata (előzetes verzió)|
|:-----|:-----|:-----|:-----|:-----|
|Fordító szöveg API 2-es verzió| api.microsofttranslator.com|    Nem  |Igen    |Nem|
|Fordító szöveg API 3-as verzió| api.cognitive.microsofttranslator.com|  Igen|    Nem| Igen|

**Fordító szöveg API 3-as verzió**
* Általánosan elérhető és teljes mértékben támogatott.
* A GDPR processzorként megfelel, és megfelel az iSO 20001 és 20018 szabványnak, valamint az SOC 3 tanúsítási követelményeinek. 
* Lehetővé teszi, hogy meghívja a neurális hálózati fordítási rendszerek egyéni fordító (Preview), az új Translator NMT testreszabási funkció. 
* Nem biztosít hozzáférést a Microsoft Translator Hub használatával létrehozott egyéni fordítási rendszerekhez.

A Translator Text API 3-as verzióját használja, ha a api.cognitive.microsofttranslator.com végpontot használja.

**Fordító szöveg API 2-es verzió**
* Nem felel meg az ISO 20001,2001,20018 és az SOC 3 tanúsítási követelményeinek. 
* Nem teszi lehetővé a fordító testreszabási funkciójával testre szabott neurális hálózati fordítási rendszerek meghívását.
* Hozzáférést biztosít a Microsoft Translator Hub használatával létrehozott egyéni fordítási rendszerekhez.
* A Translator Text API 2-es verzióját használja, ha a api.microsofttranslator.com végpontot használja.

A Translator API egyetlen verziója sem hoz létre rekordot a fordításokról. A fordításokat soha nem osztjuk meg senkivel. További információ a [Fordító nem-trace](https://www.aka.ms/NoTrace) weboldalon.

## <a name="links"></a>Hivatkozások

* [A Microsoft adatvédelmi szabályzata](https://privacy.microsoft.com/privacystatement)
* [A Microsoft Azure jogi adatai](https://azure.microsoft.com/support/legal)
* [Online szolgáltatások feltételei](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A 3.0-s kibékés dokumentáció megtekintése](reference/v3-0-reference.md)
