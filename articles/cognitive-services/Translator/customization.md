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
ms.openlocfilehash: 9c4410cb2b9550bc111da96204ada15313867fb1
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238088"
---
# <a name="customize-your-text-translations"></a>A szöveges fordítások testreszabása

Az egyéni Translator a Translator Service funkciója, amely lehetővé teszi, hogy a felhasználók testre szabják a Microsoft Translator speciális neurális gépi fordítását a fordítók fordításakor (csak a 3. verzióban).

A funkció a beszédfelismerési fordítás testreszabására is használható [Cognitive Services Speech](../speech-service/index.yml)használata esetén.

## <a name="custom-translator"></a>Custom Translator

Az egyéni fordítóval olyan neurális fordítási rendszereket hozhat létre, amelyek megértik a saját üzleti és iparági terminológiáját. A testreszabott fordítási rendszer ezután integrálva lesz a meglévő alkalmazásokhoz, munkafolyamatokhoz és webhelyekhez.

### <a name="how-does-it-work"></a>Hogyan működik?

A korábban lefordított dokumentumok (szórólapok, weblapok, dokumentációk stb.) használatával olyan fordítási rendszer hozható létre, amely a tartományra jellemző terminológiát és stílust tükrözi, ami jobb, mint a szabványos fordítási rendszer. A felhasználók feltölthetik a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat.  

A rendszer emellett a dokumentum szintjén párhuzamosan megjelenő olyan adattípusokat is elfogadja, amelyek még nem igazodnak a mondatok szintjéhez. Ha a felhasználók több nyelven is hozzáférnek ugyanahhoz a tartalomhoz, de a különálló dokumentumok egyéni fordítója automatikusan meg tudja feleltetni a mondatokat a dokumentumok között.  A rendszerek a párhuzamos betanítási adatgyűjtést a fordítások tökéletesítése érdekében akár mindkét nyelven is használhatják.

A testreszabott rendszer ezután a Translator paraméter használatával, a fordító rendszeres hívásával érhető el.

A megfelelő típusú és mennyiségű betanítási adat miatt nem ritka, hogy 5 és 10 közötti nyereséget vár, vagy akár több BLEU-pontot is a fordítási minőséghez egyéni Translator használatával.

Az elérhető adatok alapján történő Testreszabás különböző szintjeiről további részleteket az [Egyéni Translator felhasználói útmutatójában](./custom-translator/overview.md)találhat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Testreszabott nyelvi rendszer beállítása egyéni Translator használatával](./custom-translator/overview.md)
