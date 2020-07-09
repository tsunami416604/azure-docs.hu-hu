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
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961234"
---
# <a name="what-is-custom-translator"></a>Mi a Custom Translator?

Az [Egyéni Translator](https://portal.customtranslator.azure.ai) a Translator Service egyik funkciója, amely lehetővé teszi, hogy a vállalatok, az alkalmazások fejlesztői és a nyelvi szolgáltatók testreszabott neurális gépi fordítási (NMT-) rendszereket hozzanak létre. A testreszabott fordítási rendszerek zökkenőmentesen integrálva vannak a meglévő alkalmazásokkal, munkafolyamatokkal és webhelyekkel. 

Az [Egyéni fordítóval](https://portal.customtranslator.azure.ai) létrehozott fordítási rendszerek ugyanazon a felhőalapú, [biztonságos](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), nagy teljesítményű, rugalmasan skálázható [Azure Cognitive Services Translator v3 platformon](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)érhetők el, amely minden nap több milliárd fordítást biztosít.

Az egyéni Translator több mint három tucat nyelvet támogat, és közvetlenül a NMT elérhető nyelvekhez nyújt térképeket. A teljes listát lásd: [Translator languages](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Szolgáltatások

Az egyéni Translator különböző funkciókat biztosít az egyéni fordítási rendszerek létrehozásához, majd ezt követően azt is elérheti.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[A neurális gépi fordítási technológia kihasználása](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Fejlessze a fordítást az egyéni fordító által biztosított neurális gépi fordítással (NMT).       |
|[Az üzleti terminológiát ismerő rendszerek létrehozása](what-are-parallel-documents.md)     |  A párhuzamos dokumentumok segítségével testre szabhatja és felépítheti a fordítási rendszereket, és megismerheti a saját üzleti és iparági terminológiáját.       |
|[Modellek készítése szótár használatával](what-is-dictionary.md)     |   Ha nem rendelkezik betanítási adatkészlettel, a modelleket csak a szótárakból álló adattal lehet betanítani.       |
|[Együttműködés másokkal](how-to-manage-settings.md#share-your-workspace)     |   Együttműködik a csapatával, és megoszthatja a munkáját a különböző személyekkel.     |
|[Hozzáférés az egyéni fordítási modellhez](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Az egyéni fordítási modellt a meglévő alkalmazások/programok bármikor elérhetővé teheti a Translator v3-on keresztül.       |

## <a name="get-better-translations"></a>Jobb fordítások

A fordító kiadta a [neurális gépi fordítást (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) a 2016-es verzióban. A NMT az iparági szabványnak megfelelő [statisztikai gépi fordítási (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technológia keretében jelentős előrelépéseket biztosított a fordítási minőségben. Mivel a NMT jobban rögzíti a teljes mondatok kontextusát, mielőtt lefordítja őket, jobb minőségű, több emberi hanghatású és több Fluent fordítást biztosít. A [Custom Translator](https://portal.customtranslator.azure.ai) a jobb fordítási minőséget eredményező egyéni modellekhez biztosít NMT.

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
