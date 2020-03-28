---
title: Mi a Custom Translator?
titleSuffix: Azure Cognitive Services
description: Az egyéni fordító hasonló képességeket kínál, mint amit a Microsoft Translator Hub a statisztikai gépi fordítás (SMT), de kizárólag a neurális gépi fordítás (NMT) rendszerek.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d84a0190e13d7bb7664e8792e0047338fe4bf5e2
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982691"
---
# <a name="what-is-custom-translator"></a>Mi a Custom Translator?

[Az Egyéni fordító](https://portal.customtranslator.azure.ai) a Microsoft Translator szolgáltatás egyik szolgáltatása, amely lehetővé teszi a fordítóvállalatok, az alkalmazásfejlesztők és a nyelvi szolgáltatók számára, hogy testre szabott neurális gépi fordítási (NMT) rendszereket építsenek. A testreszabott fordítási rendszerek zökkenőmentesen integrálhatók a meglévő alkalmazásokba, munkafolyamatokba és webhelyekbe. [Az egyéni fordító](https://portal.customtranslator.azure.ai/) hasonló képességeket kínál, mint amit a [Microsoft Translator Hub](https://hub.microsofttranslator.com/) a statisztikai gépi fordítás (SMT), de kizárólag a neurális gépi fordítás (NMT) rendszerek.

Az Egyéni [fordítóval](https://portal.customtranslator.azure.ai) készült fordítási rendszerek ugyanazon a felhőalapú, [biztonságos,](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality)nagy teljesítményű, jól skálázható Microsoft Translator [Text API V3-on](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)keresztül érhetők el, amely naponta több milliárd fordítást működtet.

Az Egyéni fordító több mint három tucat nyelvet támogat, és közvetlenül az NMT számára elérhető nyelvekre képezi le a térképeket. A teljes listát a Microsoft Fordítónyelvek című [témakörben található.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)

## <a name="features"></a>Szolgáltatások

Az Egyéni fordító különböző funkciókat biztosít az egyéni fordítási rendszer létrehozásához és későbbi eléréséhez.

|Szolgáltatás  |Leírás  |
|---------|---------|
|[Használja ki a neurális gépi fordítási technológiát](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Javítsa a fordítást az egyéni fordító által biztosított neurális gépi fordítás (NMT) kihasználásával.       |
|[Olyan rendszereket hozhat létre, amelyek ismerik üzleti terminológiáját](what-are-parallel-documents.md)     |  A fordítási rendszereket párhuzamos dokumentumok segítségével testreszabhatja és felépítheti, amelyek megértik a saját vállalkozásában és iparágában használt terminológiákat.       |
|[Modellek készítése szótár használatával](what-is-dictionary.md)     |   Ha nem rendelkezik betanítási adatkészlet, betaníthatja a modell csak szótáradatokat.       |
|[Együttműködés másokkal](how-to-manage-settings.md#share-your-workspace)     |   Együttműködjön a csapatával, ha megosztja munkáját különböző emberekkel.     |
|[Az egyéni fordítási modell elérése](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Az egyéni fordítási modell bármikor elérhető a meglévő alkalmazások / programok segítségével Microsoft Translator Text API V3.       |

## <a name="get-better-translations"></a>Jobb fordítások beszereznie

A Microsoft Translator 2016-ban kiadta a [Neural Machine Translation (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) kiadását. Az NMT jelentős előrelépést nyújtott a fordítás minőségében az iparági szabványnak megfelelő [statisztikai gépi fordítási (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technológiával szemben. Mivel az NMT jobban rögzíti a teljes mondatok kontextusát, mielőtt lefordítana, jobb minőséget, emberibb hangzást és gördülékenyebb fordításokat biztosít. [Az egyéni fordító](https://portal.customtranslator.azure.ai) nmt-t biztosít az egyéni modellekhez, ami jobb fordítási minőséget eredményez.

A korábban lefordított dokumentumok segítségével fordítási rendszert hozhat létre. Ezek a dokumentumok tartományspecifikus terminológiát és stílust tartalmaznak, jobb, mint egy szabványos fordítási rendszer. A felhasználók feltölthetik az ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX és XLSX dokumentumokat.

Az Egyéni fordító a dokumentum szintjén párhuzamos adatokat is elfogad az adatgyűjtés és -előkészítés hatékonyabbá szabása érdekében. Ha a felhasználók több nyelven, de külön dokumentumokban férhetnek hozzá ugyanannak a tartalomnak a verzióihoz, az Egyéni fordító automatikusan egyeztethet a mondatokkal a dokumentumok között.

Ha a megfelelő típusú és mennyiségű betanítási adatok at adják meg, nem ritka, hogy [bleu pontszám](what-is-bleu-score.md) nyereség 5 és 10 pont között egyéni fordító használatával.

## <a name="be-productive-and-cost-effective"></a>Legyen termelékeny és költséghatékony

[Az egyéni fordító,](https://portal.customtranslator.azure.ai)a képzés és az egyéni rendszer telepítése nem igényel programozási ismereteket.

A biztonságos [egyéni fordítóportál](https://portal.customtranslator.azure.ai) használatával a felhasználók feltölthetik a betanítási adatokat, betaníthatják a rendszereket, tesztrendszereket, és egy intuitív felhasználói felületen keresztül üzembe helyezhetik őket egy éles környezetben. A rendszer ezután néhány órán belül elérhető lesz nagy léptékben (a tényleges idő a betanítási adatok méretétől függ).

[Egyéni fordító](https://portal.customtranslator.azure.ai) is programozott módon érhető el egy [dedikált API-n](https://custom-api.cognitive.microsofttranslator.com/swagger/) keresztül (jelenleg előzetes verzióban). Az API lehetővé teszi a felhasználók számára, hogy a saját alkalmazásukon vagy webszolgáltatásukon keresztül rendszeresen kezeljék a betanításlétrehozását vagy frissítését.

Az egyéni modell tartalom fordítása a felhasználó Translator Text API-díjszabási szintjére épül. Tekintse meg a Cognitive Services [Translator Text API díjszabási weblap](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) a tarifacsomag részleteit.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Biztonságos fordítás bármikor, bárhol az összes alkalmazásodon és szolgáltatásodon

Az egyéni rendszerek zökkenőmentesen elérhetők és integrálhatók bármely termék- vagy üzleti munkafolyamatba, illetve bármilyen eszközre, a Microsoft Translator Text API-n keresztül, szabványos REST technológián keresztül.

## <a name="next-steps"></a>További lépések

- Olvassa át a [díjszabási részleteket](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- A [rövid útmutató](quickstart-build-deploy-custom-model.md) megtanulja, hogy hozzon létre egy fordítási modellegyéni fordító.
