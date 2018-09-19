---
title: V3 – Translator Text API áttelepítése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan V2 át a Translator Text API v3-as.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: eaf65bef28110d73378c213ae4781a409b86e1bd
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128179"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 V3 áttelepítése

A Microsoft Translator csapat kiadott 3-as verziójú (V3), a Translator Text API. Ebben a kiadásban új funkciók, a elavult módszerek és a egy új formátum küld és fogad adatokat a Microsoft Translator szolgáltatás tartalmaz. Ez a dokumentum módosítása alkalmazások általi használatát a v3-as információkat szolgáltat. V2 2018. április 30. elavulttá válik, és a 2019. április 30. ezt megszüntetjük.

Ez a dokumentum végén, hogy további hasznos hivatkozásokat tartalmaz.

## <a name="summary-of-features"></a>Szolgáltatások összefoglalása

* Nincs nyomkövetés - a v3-as nyomkövetés-mellőzési minden tarifacsomagja az Azure Portalon vonatkozik. Ez a szolgáltatás azt jelenti, hogy az V3 API-nak elküldött szöveg nélkül menti a rendszer a Microsoft által.
* JSON - XML JSON váltotta fel. A szolgáltatásnak küldött és az szolgáltatásból származó összes adat JSON formátumban van.
* Egyetlen kérelem - a fordítására metódust a támogatott nyelveket cél több "to" nyelv fordítási egyetlen kérésbe fogad el. Egyetlen kérelem lehet például a "from" angol és a "to" német, spanyol és japán nyelven vagy bármely más csoport nyelveken.
* Kétnyelvű szótár - kétnyelvű szótár metódus az API-ra bővült. Ez a módszer "keresési" és "mintái" tartalmazza.
* Átbetűzés - transliterate metódus az API-ra bővült. Ez a módszer átalakítja a szavak és a egy parancsfájl (pl. a mondatok Arab) egy másik parancsfájlba (például: Latin betűs).
* Nyelvek – egy új "nyelv" módszer nyelvi információk ennek a JSON formátumban, a 'fordítása', "szótár" és a "átbetűzés" való használatra.
* Új fordítás – az új képességekkel bővült a "fordítása" metódus támogatja, amelyek korábban a V2 API külön módszerként funkcióit. Ez például akkor TranslateArray.
* Beszéljen metódus – szöveg-beszéd átalakítás funkció már nem támogatott a Microsoft Translator API-t. Szöveg-beszéd átalakítás is elérhető az Azure Cognitive services a Bing Speech API.

Az alábbi listában szereplő V2 és V3 módszerek a v3-as módszerek és API-kat biztosít a funkciót, melyet a V2 azonosítja.

| V2 API-metódus   | V3 API-kompatibilitás |
|:----------- |:-------------|
| Fordítás     | Fordítás          |
| TranslateArray      | Fordítás          |
| GetLanguageNames      | Nyelvek          |
| GetLanguagesForTranslate     | Nyelvek        |
| GetLanguagesForSpeak      | A cognitive Services beszédfelismerő API         |
| Beszéd     | A cognitive Services beszédfelismerő API          |
| Észlelés     | Észlelés         |
| DetectArray     | Észlelés         |
| AddTranslation     | A Microsoft Translator HUB API         |
| AddTranslationArray    | A Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | A szolgáltatás már nem támogatott.         |
| GetTranslationsArray      | A szolgáltatás már nem támogatott.         |

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
| Nyelvek     | Egyetlen elküldött karakter, nincs számításba, díjmentesen.          |
| Fordítás     | Száma alapján hány karaktert elküldése fordítás, és hány nyelvek a karakterek fordítja. elküldött 50 karakter hosszú lehet, és 5 nyelvek kért 50 x 5.           |
| Átírás     | Számoljuk el átbetűzésű-karakterek száma.         |
| Szótár keresési és példa     | Számoljuk el szótár keresés és a példák-karakterek száma.         |
| BreakSentence     | Díjmentes.       |
| Észlelés     | Díjmentes.      |

## <a name="v3-end-points"></a>V3 végpontok

Globális

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API szöveg fordítások módszerek

[Nyelvek](reference/v3-0-languages.md)

[Fordítása](reference/v3-0-translate.md)

[Átírás](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Észlelése](reference/v3-0-detect.md)

[Szótár/keresése](reference/v3-0-dictionary-lookup.md)

[Szótár/példa](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Testreszabás

A Microsoft Translator V3 Neurális gépi fordítás alapértelmezés szerint használ. Ezért nem használható a Microsoft Translator hubbal. A Translator Hub csak az örökölt statisztikai gépi fordítási támogatja. Neurális fordítással testreszabásának már elérhető az egyéni translatorral. [További tudnivalók a Neurális gépi fordítás testreszabása](customization.md)

API v3-as szöveggel Neurális fordítással nem támogatja a standard szintű kategóriák (SMT, beszédfelismerés, szöveg, generalnn) használatát.


## <a name="links"></a>Hivatkozások

* [A Microsoft adatvédelmi szabályzata](https://privacy.microsoft.com/privacystatement)
* [A Microsoft Azure – jogi információk](https://azure.microsoft.com/support/legal)
* [Online szolgáltatások használati feltételeit](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [V3.0 dokumentáció megtekintése](reference/v3-0-reference.md)
