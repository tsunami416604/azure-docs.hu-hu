---
title: Mi a Custom Translator?
titleSuffix: Azure Cognitive Services
description: A Custom Translator hasonló funkciókat kínál a Microsoft Translator hub számára a statisztikai gépi fordításhoz (SMT), de kizárólag neurális gépi fordítási (NMT) rendszerekhez.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: a78e2cbc6f1e33f516912911e2038a0de3ff6b99
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996941"
---
# <a name="what-is-custom-translator"></a>Mi a Custom Translator?

Az [Egyéni Translator](https://portal.customtranslator.azure.ai) a Microsoft Translator Service egyik funkciója, amely lehetővé teszi a fordító vállalatok, az alkalmazások fejlesztői és a nyelvi szolgáltatók számára a testreszabott neurális gépi fordítási (NMT-) rendszerek készítését. A testreszabott fordítási rendszerek zökkenőmentesen integrálva vannak a meglévő alkalmazásokkal, munkafolyamatokkal és webhelyekkel. A [Custom Translator](https://portal.customtranslator.azure.ai/) hasonló funkciókat kínál a [Microsoft Translator hub](https://hub.microsofttranslator.com/) számára a statisztikai gépi fordításhoz (SMT), de kizárólag neurális gépi fordítási (NMT) rendszerekhez.

Az [Egyéni fordítóval](https://portal.customtranslator.azure.ai) létrehozott fordítási rendszerek ugyanazon a felhőalapú, [biztonságos](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), nagy teljesítményű, rugalmasan MÉRETEZHETŐ Microsoft Translator [text API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)-as verzión keresztül érhetők el, amely naponta több milliárd fordítást biztosít.

Az egyéni Translator több mint három tucat nyelvet támogat, és közvetlenül a NMT elérhető nyelvekhez nyújt térképeket. A teljes listát lásd: [Microsoft Translator languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Szolgáltatások

Az egyéni Translator különböző funkciókat biztosít az egyéni fordítási rendszerek létrehozásához, majd ezt követően azt is elérheti.

|Funkció  |Leírás  |
|---------|---------|
|[A neurális gépi fordítási technológia kihasználása](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Fejlessze a fordítást az egyéni fordító által biztosított neurális gépi fordítással (NMT).       |
|[Az üzleti terminológiát ismerő rendszerek létrehozása](what-are-parallel-documents.md)     |  A párhuzamos dokumentumok segítségével testre szabhatja és felépítheti a fordítási rendszereket, és megismerheti a saját üzleti és iparági terminológiáját.       |
|[Modellek készítése szótár használatával](what-is-dictionary.md)     |   Ha nem rendelkezik betanítási adatkészlettel, a modelleket csak a szótárakból álló adattal lehet betanítani.       |
|[Együttműködés másokkal](how-to-manage-settings.md#share-your-workspace)     |   Együttműködik a csapatával, és megoszthatja a munkáját a különböző személyekkel.     |
|[Hozzáférés az egyéni fordítási modellhez](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Az egyéni fordítási modellt a meglévő alkalmazások/programok bármikor elérhetővé teheti a Translator v3-on keresztül.       |

## <a name="get-better-translations"></a>Jobb fordítások

A Microsoft Translator 2016-ben kiadott egy [neurális gépi fordítást (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) . A NMT az iparági szabványnak megfelelő [statisztikai gépi fordítási (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technológia keretében jelentős előrelépéseket biztosított a fordítási minőségben. Mivel a NMT jobban rögzíti a teljes mondatok kontextusát, mielőtt lefordítja őket, jobb minőségű, több emberi hanghatású és több Fluent fordítást biztosít. A [Custom Translator](https://portal.customtranslator.azure.ai) a jobb fordítási minőséget eredményező egyéni modellekhez biztosít NMT.

A korábban lefordított dokumentumok használatával fordítási rendszer hozható létre. Ezek a dokumentumok a tartományra jellemző terminológiát és stílust foglalják magukban, mint a szabványos fordítási rendszerek. A felhasználók az igazítás, a PDF, a LCL, a HTML, a HTM, a XLF, a TMX, a XLIFF, a TXT, a DOCX és az XLSX dokumentumokat tölthetik fel.

Az egyéni fordító a dokumentum szintjén párhuzamosan is elfogadja az adatgyűjtés és az előkészítés hatékonyságát. Ha a felhasználók több nyelven, de külön dokumentumokban is hozzáférnek ugyanahhoz a tartalomhoz, akkor az egyéni fordító automatikusan egyezteti a mondatokat a dokumentumok között.

Ha a megfelelő típusú és betanítási adatmennyiséget adja meg, nem ritka, hogy a [Bleu pontszám](what-is-bleu-score.md) nyeresége 5 és 10 pont közötti, egyéni fordító használatával.

## <a name="be-productive-and-cost-effective"></a>Hatékony és költséghatékony

Az [Egyéni fordítók](https://portal.customtranslator.azure.ai), az egyéni rendszerek betanítása és üzembe helyezése nem igényel programozási ismereteket.

A biztonságos [Egyéni Translator](https://portal.customtranslator.azure.ai) portál használatával a felhasználók az intuitív felhasználói felületen keresztül tölthetik fel a betanítási adatok, a rendszerek és a rendszerek tesztelésére szolgáló rendszereket, és üzembe helyezhetik őket éles környezetben. A rendszer ezután néhány órán belül elérhetővé válik a skálán (a tényleges idő a betanítási adatok méretétől függ).

Az [Egyéni fordító](https://portal.customtranslator.azure.ai) programozott módon is elérhető egy dedikált API-n keresztül (jelenleg előzetes [verzióban](https://custom-api.cognitive.microsofttranslator.com/swagger/) érhető el). Az API lehetővé teszi, hogy a felhasználók a saját alkalmazáson vagy webszolgáltatáson keresztül rendszeresen kezeljék a képzések létrehozását vagy frissítését.

A tartalmak fordítására szolgáló egyéni modell használatának díja a felhasználó Translator díjszabási szintjétől függ. A díjszabási csomagra vonatkozó részletekért tekintse meg a Cognitive Services [Translator díjszabási weboldalát](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) .

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Biztonságosan lefordítható bármikor, bárhol az alkalmazásokon és szolgáltatásokon

Az egyéni rendszerek zökkenőmentesen érhetők el és integrálhatók bármilyen termék-vagy üzleti munkafolyamatba, illetve bármilyen eszközön a fordítón keresztül a szabványos REST-technológián keresztül.

## <a name="next-steps"></a>További lépések

- Olvassa át a [díjszabási részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- A [rövid útmutatóból](quickstart-build-deploy-custom-model.md) megtudhatja, hogyan hozhat létre fordítási modellt egyéni fordítóban.
