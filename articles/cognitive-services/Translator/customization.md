---
title: Fordítás testreszabása - Fordító szöveg API
titleSuffix: Azure Cognitive Services
description: A Microsoft Translator Hub segítségével saját gépi fordítási rendszert hozhat létre a kívánt terminológiával és stílussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71257627"
---
# <a name="customize-your-text-translations"></a>A szövegfordítások testreszabása

A Microsoft Custom Translator a Microsoft Translator szolgáltatás jellemzője, amely lehetővé teszi a felhasználók számára, hogy testre szabják a Microsoft Translator fejlett neurális gépi fordítását, amikor szöveget fordítanak a Translator Text API-val (csak a 3-as verzióval).

A szolgáltatás a [beszédfordítás](https://docs.microsoft.com/azure/cognitive-services/speech-service/)testreszabására is használható, ha a Cognitive Services beszédfelismeréssel használja.

## <a name="custom-translator"></a>Custom Translator

Az egyéni fordítóval olyan neurális fordítási rendszereket hozhat létre, amelyek megértik a saját vállalkozásában és iparágában használt terminológiát. A testreszabott fordítási rendszer ezután integrálódik a meglévő alkalmazásokba, munkafolyamatokba és webhelyekbe.

### <a name="how-does-it-work"></a>Hogyan működik?

Használja a korábban lefordított dokumentumokat (szórólapok, weboldalak, dokumentáció, stb.) egy olyan fordítási rendszer létrehozásához, amely tükrözi a tartományspecifikus terminológiát és stílust, jobb, mint egy szabványos fordítási rendszer. A felhasználók TMX, XLIFF, TXT, DOCX és XLSX dokumentumokat tölthetnek fel.  

A rendszer olyan adatokat is elfogad, amelyek párhuzamosak a dokumentum szintjén, de még nincsenek igazítva a mondat szintjén. Ha a felhasználók több nyelven is hozzáférhetnek ugyanannak a tartalomnak a verzióihoz, de külön dokumentumokban az Egyéni fordító automatikusan egyeztethet a mondatokkal a dokumentumok között.  A rendszer egynyelvű adatokat is használhat egyik vagy mindkét nyelven a párhuzamos betanítási adatok kiegészítésére a fordítások javítása érdekében.

A testreszabott rendszer ezután a Microsoft Translator Text API-hoz a kategóriaparaméter használatával rendszeresen elérhető.

Tekintettel a megfelelő típusú és mennyiségű betanítási adatok nem ritka, hogy számíthat nyereség között 5 és 10, vagy még több BLEU pontot a fordítás minősége segítségével Custom Translator.

A rendelkezésre álló adatok on alapuló testreszabás különböző szintjeiről további részletek találhatók az [Egyéni fordító felhasználói útmutatójában.](https://aka.ms/CustomTranslatorDocs)


## <a name="microsoft-translator-hub"></a>Microsoft Fordítóközpont

> [!NOTE]
> 2019. május 17-én megszűnik a régebbi Microsoft Translator Hub. [Fontos áttelepítési információk és dátumok megtekintése.](https://www.microsoft.com/translator/business/hub/)  

## <a name="custom-translator-versus-hub"></a>Egyéni fordító kontra központ

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Testreszabási szolgáltatás állapota   | Általános elérhetőség  | Általános elérhetőség |
| Szöveges API-verzió  | Csak V2   | Csak V3 |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egységes beszédfelismerési szolgáltatások testreszabása | Nem    | Igen |
| [Nincs nyomkövetés](https://www.aka.ms/notrace) | Igen  | Igen |

## <a name="collaborative-translations-framework"></a>Együttműködésen alapuló fordítási keretrendszer

> [!NOTE]
> 2018. február 1-től az AddTranslation() és az AddTranslationArray() már nem használható a Translator Text API V2.0-val. Ezek a módszerek nem fogsannak, és semmi sem lesz megírva. A Translator Text API V3.0 nem támogatja ezeket a módszereket.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni fordító val testreszabott nyelvi rendszer beállítása](https://aka.ms/CustomTranslatorDocs)
