---
title: Fordítás testreszabása – Translator Text API
titlesuffix: Azure Cognitive Services
description: A Microsoft Translator Hub használatával hozhat létre a saját gépi fordítási rendszer használatával az előnyben részesített terminológia és a stílust.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: d01a5a8a9a60bef315327721b9f55345bc3d1361
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645051"
---
# <a name="customize-your-text-translations"></a>A szöveg fordítások testreszabása

A Microsoft egyéni Translator előzetes funkciója, a Microsoft Translator szolgáltatás, amely lehetővé teszi a felhasználók testre speciális Neurális gépi fordítás a Microsoft Translator fordítása a szöveg a Translator Text API (csak a 3. verzió) használatával.

A funkció is használható együtt használva beszédalapú fordítási testreszabásához [Cognitive Services beszéd előzetes](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Egyéni a fordítót

Egyéni a fordítót hozhat létre olyan Neurális fordítással rendszerek, amelyek a saját üzleti és az iparágban használt terminológia ismertetése. A testre szabott fordítási rendszer majd fog integrálhatja meglévő alkalmazások, munkafolyamatok és a websites.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (termék, weblapjait, dokumentáció, stb.) hozhat létre, amely tükrözi a tartomány-specifikus terminológia és stílusát, jobban, mint egy általános fordítási rendszerét egy fordítási rendszerét használja. Felhasználók TMX, illetve XLIFF, TXT, DOCX, és mentse XLSX dokumentumokat feltölthet.  

A rendszer az adatok párhuzamos a szintjén, de még nem igazodik a mondat szintjén is fogad. Ha a felhasználók férhetnek hozzá ugyanahhoz a tartalomhoz verziói több nyelven is, de külön dokumentumok egyéni a fordítót tudják automatikusan felel meg a mondatokban dokumentumok között.  A rendszer egyik vagy mindkét nyelven használni monolingual adatokat is, a párhuzamos betanítási adatok a fordítások javítására kiegészíteni.

A testre szabott rendszer majd rendszeres hívása a Microsoft Translator Text API, a kategória paraméter használatával érhető el.

A megfelelő típusú és a betanítási adatok mennyisége a megadott nem ritka, nyereség 5 és 10 közötti várható, vagy még több BLEU mutat a fordítás minősége egyéni Translator használatával.

További információt a rendelkezésre álló adatok alapján testreszabási különböző szintjeit megtalálható a [egyéni Translator felhasználói útmutató](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>A Microsoft Translator-Hub

A Microsoft Translator örökölt Hub statisztikai gépi fordítási lefordítani használható. [További információ](https://www.microsoft.com/en-us/translator/hub.aspx)

## <a name="custom-translator-versus-hub"></a>Eseményközpont és egyéni fordító

|   | **Hub** | **Egyéni a fordítót**|
|:-----|:----:|:----:|
|Testreszabás funkció állapota   | Általános rendelkezésre állás  | Előzetes verzió |
| Text API-verzió  | Csak v2   | Csak a v3 |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egyesített Speech services testreszabása | Nem    | Igen |
| [Nincs nyomkövetés](http://www.aka.ms/notrace) | Igen   | Igen |

## <a name="collaborative-translations-framework"></a>Együttműködésen alapuló fordítások keretrendszer

> [!NOTE]
> 2018. február 1. AddTranslation() és AddTranslationArray() nem lesznek elérhetők a Translator Text API V2.0 való használatra. Ezek a metódusok sikertelen lesz, és semmi sem lesz írva. A Translator Text API 3.0-s verzió nem támogatja ezeket a metódusokat.

>Hasonló funkciókat a Translator Hub API-ban érhető el. Lásd: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy egyéni translatorral testreszabott nyelvi rendszer beállítása](http://aka.ms/CustomTranslatorDocs)
