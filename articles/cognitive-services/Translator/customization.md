---
title: A Microsoft Translator szöveg API fordítási testreszabási |} Microsoft Docs
description: A Microsoft Translator központ segítségével a saját gépi fordítás rendszert használ az előnyben részesített terminológia és a stílust.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349262"
---
# <a name="customize-your-text-translations"></a>A szöveg fordítások testreszabása

A Microsoft egyéni fordító előzetes szolgáltatás a Microsoft Translator szolgáltatás, amely lehetővé teszi a felhasználók Microsoft Translator speciális Neurális gépi fordítás testreszabása, amikor a Microsoft Translator szöveg API (csak a 3. verzió) használatával fordítása. 

A szolgáltatás is segítségével testre szabhatja a beszédfelismerés fordítási használata esetén [kognitív szolgáltatások beszéd előzetes](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Egyéni fordító
Az egyéni fordító Neurális fordítási rendszerek, amelyek a saját vállalati és iparági használt terminológiával megértéséhez hozhat létre. A testre szabott fordítási rendszer fog majd integrálja a meglévő alkalmazások, a munkafolyamatok és a webhelyek. 

### <a name="how-does-it-work"></a>Hogyan működik?
A korábban lefordított dokumentumok (termék, weblapjait, dokumentáció, stb.) hozhat létre, amely tükrözi a tartományspecifikus terminológia és stílusát, jobb, mint egy általános fordítási rendszer fordítási rendszer használata. Felhasználók feltöltheti TMX, XLIFF, TXT, DOCX és XLSX dokumentumok.  

A rendszer is fogad adatokat párhuzamos a szintjén, de a mondatok szintjén még nincsenek egyeztetve. Ha a felhasználók férhetnek hozzá ugyanahhoz a tartalomhoz verziói több nyelven, de külön dokumentumok egyéni fordító fog tudni automatikusan megfelelteti a dokumentumok között a mondatok.  A rendszer segítségével is monolingual adatok egyik vagy mindkét nyelven kiegészíti a párhuzamos betanítási adatok, a fordítások javítására. 

A testre szabott rendszer keresztül a Microsoft Translator szöveg API kategória paraméter használatával rendszeres hívása majd érhető el.

A megadott megfelelő típusát és képzési adatok mennyisége nem ritkán 5 és 10 közötti növekedését várja, vagy még több BLEU pontok fordítási minőségét egyéni fordító használatával.

További információt a testreszabásról a rendelkezésre álló adatok alapján a különböző szintekről megtalálhatók a [egyéni fordító felhasználói útmutató](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>A Microsoft Translator Hub

A Microsoft Translator Hub négyféleképpen használható statisztikai gépi fordítás lefordítani örökölt. [További információ](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Hub és egyéni fordító

|   | **Hub** | **Egyéni fordító**|
|:-----|:----:|:----:|
|Testreszabási szolgáltatás állapota   | Általános rendelkezésre állás  | Előzetes verzió |
| Szöveg API-verzió  | Csak 2   | Csak a v3 |
| SMT testreszabása | Igen   | Nem | 
| NMT testreszabása | Nem    | Igen |
| Új egyesített beszéd szolgáltatások testreszabása | Nem    | Igen | 
| [Nincs nyomkövetési](http://www.aka.ms/notrace) | Igen   | Igen | 

## <a name="collaborative-translations-framework"></a>Együttműködési fordítások keretrendszer

> [!NOTE]
> 2018. február 1-től AddTranslation() és AddTranslationArray() nem lesznek elérhetők a fordító szöveg API V2.0 való használatra. Ezek a módszerek sikertelen lesz, és semmi sem fog szerepelni. A fordító szöveg API 3.0-s verzió nem támogatja ezeket a módszereket.

>Hasonló funkciókat a fordító Hub API érhető el. Lásd: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger). 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A testre szabott nyelvi rendszerbe egyéni fordító beállítása](http://aka.ms/CustomTranslatorDocs)
