---
title: Translation Customization - Translator Text API
titlesuffix: Azure Cognitive Services
description: A Microsoft Translator Hub használatával hozhat létre a saját gépi fordítási rendszer használatával az előnyben részesített terminológia és a stílust.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: e66387a904f0026eb862178b19fd1b005212d674
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124014"
---
# <a name="customize-your-text-translations"></a>A szöveg fordítások testreszabása

A Microsoft egyéni Translator funkciója, a Microsoft Translator szolgáltatás, amely lehetővé teszi a felhasználók testre speciális Neurális gépi fordítás a Microsoft Translator fordítása a szöveg a Translator Text API (csak a 3. verzió) használatával.

A funkció is használható együtt használva beszédalapú fordítási testreszabásához [Cognitive Services beszéd](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Egyéni a fordítót hozhat létre olyan Neurális fordítással rendszerek, amelyek a saját üzleti és az iparágban használt terminológia ismertetése. A testre szabott fordítási rendszer majd fog integrálhatja meglévő alkalmazások, munkafolyamatok és a websites.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (termék, weblapjait, dokumentáció, stb.) hozhat létre, amely tükrözi a tartomány-specifikus terminológia és stílusát, jobban, mint egy általános fordítási rendszerét egy fordítási rendszerét használja. Felhasználók TMX, illetve XLIFF, TXT, DOCX, és mentse XLSX dokumentumokat feltölthet.  

A rendszer az adatok párhuzamos a szintjén, de még nem igazodik a mondat szintjén is fogad. Ha a felhasználók férhetnek hozzá ugyanahhoz a tartalomhoz verziói több nyelven is, de külön dokumentumok egyéni a fordítót tudják automatikusan felel meg a mondatokban dokumentumok között.  A rendszer egyik vagy mindkét nyelven használni monolingual adatokat is, a párhuzamos betanítási adatok a fordítások javítására kiegészíteni.

A testre szabott rendszer majd rendszeres hívása a Microsoft Translator Text API, a kategória paraméter használatával érhető el.

A megfelelő típusú és a betanítási adatok mennyisége a megadott nem ritka, nyereség 5 és 10 közötti várható, vagy még több BLEU mutat a fordítás minősége egyéni Translator használatával.

További információt a rendelkezésre álló adatok alapján testreszabási különböző szintjeit megtalálható a [egyéni Translator felhasználói útmutató](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> A régebbi Microsoft Translator Hub a 2019. április 30-án megszűnik. [Fontos állapotTelepítési információit és a dátumok](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Eseményközpont és egyéni fordító

|   | **Hub** | **Egyéni a fordítót**|
|:-----|:----:|:----:|
|Testreszabás funkció állapota   | Általános rendelkezésre állás  | Általános rendelkezésre állás |
| Text API-verzió  | Csak v2   | Csak a v3 |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egyesített Speech services testreszabása | Nem    | Igen |
| [Nincs nyomkövetés](https://www.aka.ms/notrace) | Igen  | Igen |

## <a name="collaborative-translations-framework"></a>Együttműködésen alapuló fordítások keretrendszer

> [!NOTE]
> 2018. február 1. AddTranslation() és AddTranslationArray() nem lesznek elérhetők a Translator Text API V2.0 való használatra. Ezek a metódusok sikertelen lesz, és semmi sem lesz írva. A Translator Text API 3.0-s verzió nem támogatja ezeket a metódusokat.
> 
> Hasonló funkciókat a Translator Hub API-ban érhető el. Lásd: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy egyéni translatorral testreszabott nyelvi rendszer beállítása](https://aka.ms/CustomTranslatorDocs)
