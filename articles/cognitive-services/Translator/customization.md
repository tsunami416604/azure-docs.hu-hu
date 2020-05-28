---
title: Fordítás testreszabása – Translator
titleSuffix: Azure Cognitive Services
description: A Microsoft Translator hub használatával saját gépi fordítási rendszereket hozhat létre a kívánt terminológiával és stílussal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6db43300632ec5b2c4f6c18848442901a40561b0
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996998"
---
# <a name="customize-your-text-translations"></a>A szöveges fordítások testreszabása

Az egyéni Translator a Translator Service funkciója, amely lehetővé teszi, hogy a felhasználók testre szabják a Microsoft Translator speciális neurális gépi fordítását a fordítók fordításakor (csak a 3. verzióban).

A funkció a beszédfelismerési fordítás testreszabására is használható [Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/)használata esetén.

## <a name="custom-translator"></a>Custom Translator

Az egyéni fordítóval olyan neurális fordítási rendszereket hozhat létre, amelyek megértik a saját üzleti és iparági terminológiáját. A testreszabott fordítási rendszer ezután integrálva lesz a meglévő alkalmazásokhoz, munkafolyamatokhoz és webhelyekhez.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (szórólapok, weblapok, dokumentációk stb.) használatával olyan fordítási rendszer hozható létre, amely a tartományra jellemző terminológiát és stílust tükrözi, ami jobb, mint a szabványos fordítási rendszer. A felhasználók feltölthetik a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat.  

A rendszer emellett a dokumentum szintjén párhuzamosan megjelenő olyan adattípusokat is elfogadja, amelyek még nem igazodnak a mondatok szintjéhez. Ha a felhasználók több nyelven is hozzáférnek ugyanahhoz a tartalomhoz, de a különálló dokumentumok egyéni fordítója automatikusan meg tudja feleltetni a mondatokat a dokumentumok között.  A rendszerek a párhuzamos betanítási adatgyűjtést a fordítások tökéletesítése érdekében akár mindkét nyelven is használhatják.

A testreszabott rendszer ezután a Translator paraméter használatával, a fordító rendszeres hívásával érhető el.

A megfelelő típusú és mennyiségű betanítási adat miatt nem ritka, hogy 5 és 10 közötti nyereséget vár, vagy akár több BLEU-pontot is a fordítási minőséghez egyéni Translator használatával.

Az elérhető adatok alapján történő Testreszabás különböző szintjeiről további részleteket az [Egyéni Translator felhasználói útmutatójában](https://aka.ms/CustomTranslatorDocs)találhat.


## <a name="microsoft-translator-hub"></a>Microsoft Translator hub

> [!NOTE]
> Az örökölt Microsoft Translator hub 2019. május 17-én megszűnik. [Tekintse meg a fontos áttelepítési információkat és dátumokat](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Egyéni fordító és hub

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Testreszabási funkció állapota    | Általános elérhetőség    | Általános elérhetőség |
| Szöveges API-verzió    | Csak v2    | Csak v3 |
| SMT testreszabása    | Igen    | Nem |
| NMT testreszabása    | Nem    | Igen |
| Új Unified Speech Services testreszabása    | Nem    | Igen |
| [Nincs nyomkövetés](https://www.aka.ms/notrace) | Igen    | Igen |

## <a name="collaborative-translations-framework"></a>Együttműködésen alapuló fordítási keretrendszer

> [!NOTE]
> A 2018. február 1-től a AddTranslation () és a AddTranslationArray () már nem használható a Translator v 2.0-s verziójával. Ezek a metódusok sikertelenek lesznek, és semmi sem íródik. A Translator v 3.0 nem támogatja ezeket a metódusokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Testreszabott nyelvi rendszer beállítása egyéni Translator használatával](https://aka.ms/CustomTranslatorDocs)
