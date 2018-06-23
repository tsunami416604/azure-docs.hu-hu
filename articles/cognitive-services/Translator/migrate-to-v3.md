---
title: Telepítse át a Microsoft Translator szöveg API - V3 |} Microsoft Docs
description: Megtudhatja, hogyan telepíthetők át a fordító szöveg API V3 V2.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349274"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>A Microsoft Translator szöveg API V2 V3 áttelepítése

A Microsoft Translator team kiadott 3-as verziójú (V3) az a szöveg fordítási API. Ez a kiadás tartalmaz új funkciókat, elavult módszerek és egy új formátumú küld és fogad adatokat a Microsoft Translator szolgáltatás. A dokumentum módosításához használja a V3-alkalmazások információkat tartalmaz. V2 a 2018. április 30 elavulttá válik, és a 2019. április 30 megszűnik.

Ez a dokumentum végére, hogy további hasznos hivatkozásokat tartalmaz.

## <a name="summary-of-features"></a>Szolgáltatások összegzése

* Nincs nyomkövetési - V3 No-nyomkövetési összes árképzési szinteket az Azure portálon vonatkozik. Ez azt jelenti, hogy nincs szöveg V3 API elküldi a Microsoft által mentett.
* JSON - XML JSON váltotta fel. Minden a szolgáltatásnak küldött és fogadott adatok a szolgáltatásból JSON formátumban van.
* Egyetlen kérelem - lefordítani a metódus több cél nyelven több "címzett" nyelv egyetlen kérelemben fordításhoz fogad el. Egyetlen kérelem lehet például a "from" angol és a "to" német, spanyol és japán vagy bármely más nyelv-csoport.
* Kétnyelvű szótár - kétnyelvű szótár metódus hozzá lett adva az API-t. Ez a metódus "keresési" és "példa" tartalmaz.
* Transliterate - transliterate metódus hozzá lett adva az API-t. Ez a módszer szavak és egy parancsfájl (pl. mondatok konvertálja. Arab) egy másik parancsfájlba (pl. Latin betűs).
* Nyelvek - egy új "nyelv" metódus verziójában elérhető nyelvi adatokkal, JSON formátumban, a "fordítása", "szótár" és "transliterate" metódusok való használatra.
* A fordítás - új funkcióját, a V2 API külön módszerként volt támogatásához "fordítása" metódus új képességekkel bővült. Példa: TranslateArray.
* Beszéd metódus, mert a szöveg-beszéd átalakítás funkció már nem támogatott a Microsoft Translator API-ban. Szöveg-beszéd átalakítás érhető el a Microsoft Azure kognitív szolgáltatások Bing Diktálásfelismerési API.

Az alábbi listán szereplő V2 és V3 módszerek V3 módszerek és API-t fogja ellátni a V2 mellékelt azonosítja.

| V2 API-módszer   | V3 API kompatibilitási |
|:----------- |:-------------|
| Fordítása     | Fordítása          |
| TranslateArray      | Fordítása          |
| GetLanguageNames      | Nyelvek          |
| GetLanguagesForTranslate     | Nyelvek        |
| GetLanguagesForSpeak      | Kognitív szolgáltatások Diktálásfelismerési API         |
| Beszéd     | Kognitív szolgáltatások Diktálásfelismerési API          |
| Észlelés     | Észlelés         |
| DetectArray     | Észlelés         |
| AddTranslation     | A Microsoft Translator HUB API         |
| AddTranslationArray    | A Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | A funkció már nem támogatott.         |
| GetTranslationsArray      | A funkció már nem támogatott.         |

## <a name="move-to-json-format"></a>Ugrás a JSON formátumban

A Microsoft Translator szöveg fordítási V2 elfogadva és XML-formátumú adatokat adott vissza. A V3 összes küldött és fogadott adatok az API-val JSON formátumban van. XML már nem elfogadásának vagy v3 adott vissza. 

Ez a módosítás hatással lesz az alkalmazások, a V2 szöveg fordítási API-hoz írt számos tulajdonságát. Példa: A nyelvek API szöveg fordítását, transliteration és a két szótár módszerek nyelvi olyan információkat ad vissza. Összes nyelv adatok egy hívásban módszerek mindegyikéhez kérése, vagy külön-külön kérésre.

A nyelvek metódus nem igényel hitelesítést; a következő hivatkozásra kattintva megtekintheti a JSON-ban V3 összes nyelvi adatot:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, szótár, transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Hitelesítési kulcs

A hitelesítési kulcs V2 használja a V3 fogadható el. Nem kell egy új előfizetés. Fogja tudni V2 és V3 összekeveri az alkalmazásokban lévő yearlong áttelepítési időszakban, így megkönnyíti, hogy új verzióira, miközben továbbra is az V2-XML-áttelepítés V3-JSON.

## <a name="pricing-model"></a>Díjszabási modell

A Microsoft Translator V3 árképzéséről ugyanúgy V2 lett áron; karakterek a szóközöket is beleértve. Az új funkciókkal V3 módosítja a számlázáshoz karakterek számítanak.

| V3 módszer   | A számlázási számítanak karakter |
|:----------- |:-------------|
| Nyelvek     | Egyetlen karakter benyújtott, nincs számítanak, nem kell fizetni.          |
| Fordítása     | Száma alapján hány karaktert elküldi fordítása, és hány nyelvek a karakterek lefordítva. elküldött 50 karakter hosszú lehet, és 5 nyelvek kért 50 x 5.           |
| Transliterate     | A transliteration átadott karakterek bájtjai számítanak.         |
| Szótár keresési & – példa     | A szótár-keresés és példákat átadott karakterek száma bájtjai számítanak.         |
| BreakSentence     | Nem kell fizetni.       |
| Észlelés     | Nem kell fizetni.      |

## <a name="v3-end-points"></a>V3-végpontok

Globális

* API.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API szöveg fordítások módszerek

[Nyelvek](reference/v3-0-languages.md)

[Fordítása](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Észlelése](reference/v3-0-detect.md)

[Szótár/keresési](reference/v3-0-dictionary-lookup.md)

[Szótár / – példa](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Testreszabás

A Microsoft Translator V3 Neurális gépi fordítás alapértelmezés szerint használja. Így nem használható a Microsoft Translator hubbal örökölt statisztikai gépi fordítás támogató. Neurális fordításhoz testreszabási már elérhető a egyéni fordító használatával. [További információ a Neurális gépi fordítás testreszabása](customization.md)

Neurális a V3 szöveggel API nem támogatja a szabványos kategóriák (smt, beszéd átalakítás, szöveg, generalnn) használatát.


## <a name="links"></a>Hivatkozások

* [A Microsoft adatvédelmi szabályzata](https://privacy.microsoft.com/privacystatement)
* [A Microsoft Azure jogi információk](https://azure.microsoft.com/support/legal)
* [Online szolgáltatási feltételek](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nézet V3.0 dokumentáció](reference/v3-0-reference.md)
