---
title: Mi az egyéni a fordítót?
titleSuffix: Azure Cognitive Services
description: Egyéni a fordítót hasonló funkciókat kínál, a Microsoft Translator Hub célja a statisztikai gépi fordítási (SMT), de a kizárólag a Neurális gépi fordítás (NMT) rendszerekhez.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
ms.openlocfilehash: 76e6bc006ff6049b631409a3515628fbd169f713
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976978"
---
# <a name="what-is-custom-translator-preview"></a>Mi a Custom Translator (előzetes verzió)?

[Egyéni a fordítót](https://portal.customtranslator.azure.ai) funkció a Microsoft Translator szolgáltatás, amely lehetővé teszi a Translator vállalkozások, fejlesztők, és a nyelvi szolgáltatók hozhat létre egyéni Neurális gépi fordítás (NMT) rendszereket. A testre szabott fordítási rendszerek zökkenőmentesen integrálhatja meglévő alkalmazások, munkafolyamatok és a websites. [Egyéni a fordítót](https://portal.customtranslator.azure.ai/) hasonló funkciókat kínál, amit [Microsoft Translator Hub](https://hub.microsofttranslator.com/) does a statisztikai gépi fordítási (SMT), de a kizárólag a Neurális gépi fordítás (NMT) rendszerekhez.

A használatával létrehozott fordítási rendszerek [egyéni a fordítót](https://portal.customtranslator.azure.ai) ugyanaz a felhő alapú keresztül elérhető [biztonságos](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), nagy teljesítményű, nagy mértékben skálázható Microsoft Translator [Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), amely működteti fordítások milliárd minden nap. 

Egyéni Translator több mint három tucat nyelveket támogatja, és a NMT közvetlenül elérhető nyelvek rendeli. Teljes listáját lásd: [Microsoft Translator nyelvek]( https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

## <a name="features"></a>Szolgáltatások

Egyéni a fordítót hozhat létre egyéni fordítási rendszer, és később elérhető-e a különböző funkciókat biztosít.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Neurális gépi fordítás technológia hajtják végre](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  A fordítás javítása egyéni a fordítót által biztosított Neurális gépi fordítás (NMT) kihasználásával.       |
|[Hozhat létre rendszereket, hogy ismeri az üzleti terminológiája](what-are-parallel-documents.md)     |  Testre szabhatja, és a párhuzamos dokumentumok, megismerheti a saját üzleti és az iparágban használt terminológiát használ fordítási rendszerek készítése.       |
|[Egy szótár segítségével készítsen modelleket](what-is-dictionary.md)     |   Ha nem rendelkezik a tanítási adathalmazt, betaníthatja a modell csak szótár adatokat.       |
|[Együttműködés másokkal a](how-to-manage-settings.md#share-your-workspace)     |   Együttműködhet a csapatával a munkahelyi más személyekkel való megosztásával.     |
|[Az egyéni fordítási modellben eléréséhez](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Az egyéni fordítási modellben bármikor a meglévő alkalmazások / programokat a Microsoft Translator Text API V3-n keresztül.       |

## <a name="get-better-translations"></a>Jobb fordítások beolvasása

Megjelent a Microsoft Translator [Neurális gépi fordítás (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) a 2016-ban. NMT a fordítás minősége a fő fejlődésére biztosított az iparági szabványnak megfelelő [statisztikai gépi fordítási (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technológia. Mivel teljes mondatokból kontextusában kell-e NMT jobban előtt fordítja le őket, jobb minőségű, több emberi hangzó, és több fluent fordítások biztosít. [Egyéni a fordítót](https://portal.customtranslator.azure.ai) NMT biztosít az egyéni modellek az eredményül kapott jobb fordítási minőség.

Korábban lefordított dokumentumok használatával hozhat létre egy fordítási rendszerét. Ezeket a dokumentumokat például a tartomány-specifikus terminológia és stílus, jobban, mint egy általános fordítási rendszerét. Felhasználók IGAZÍTÁS, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX és mentse XLSX dokumentumokat feltölthet.

Egyéni a fordítót az adatgyűjtés és -előkészítés hatékonyabbá tétele a dokumentum szintjén párhuzamos adatokat is fogad. Ha a felhasználók hozzáférése ugyanahhoz a tartalomhoz verziói több nyelven is, de külön dokumentumok, egyéni a fordítót tudják automatikusan felel meg a mondatokban dokumentumok között.

Ha a megfelelő típusú és a betanítási adatok mennyisége van megadva, akkor egyáltalán nem ritka, hogy [BLEU pontszám](what-is-bleu-score.md) nyereség egyéni a fordítót az 5-10 pontok között.

## <a name="be-productive-and-cost-effective"></a>Hatékony és költséghatékony

A [egyéni a fordítót](https://portal.customtranslator.azure.ai), képzés és a egy egyéni rendszer központi telepítéséhez nem szükséges minden olyan programozási ismeretek. 

A biztonságos [egyéni a fordítót](https://portal.customtranslator.azure.ai) portál, felhasználók is töltse fel a betanítási adatok, rendszerek betanításához, tesztrendszerek és üzembe helyezheti őket az éles környezetben egy intuitív felhasználói felületen keresztül. A rendszer ezután lesz használható nagy mennyiségű (a tényleges idő függ, a betanítási adatok mérete) néhány órán belül.

[Egyéni a fordítót](https://portal.customtranslator.azure.ai) is programozott módon keresztül érhetők el egy [API dedikált](https://custom-api.cognitive.microsofttranslator.com/swagger/) (jelenleg előzetes verzióban érhető el). Az API lehetővé teszi, hogy a felhasználók létrehozása vagy frissítése rendszeresen saját alkalmazás vagy a webszolgáltatás képzési kezeléséhez.

Egyéni modell segítségével a tartalmak lefordítása költsége a felhasználó a Translator Text API-tarifacsomag alapul. Tekintse meg a Cognitive Services [a Translator Text API díjszabása weblap](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) csomag díjszabásáról.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Bármikor, bárhonnan biztonságosan lefordítja az alkalmazások és szolgáltatások

Egyéni rendszerek zökkenőmentesen érheti el és integrált bármely termék vagy üzleti munkafolyamatba, és bármilyen eszközön, a szabványos REST-technológia révén a Microsoft Translator Text API-n keresztül.

## <a name="next-steps"></a>További lépések

- Olvassa át a [díjszabási részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- A [rövid](quickstart-build-deploy-custom-model.md) megtanulhatja, hogyan hozhat létre egy egyéni a fordítót a fordítási modellben.
