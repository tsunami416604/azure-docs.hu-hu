---
title: Az Azure-on egyéni beszéd szolgáltatás áttekintése |} Microsoft Docs
description: Az egyéni beszéd szolgáltatás nem egy felhőalapú szolgáltatás, amely lehetővé teszi, hogy a felhasználók számára vonatkozó beszéd szöveg beszéd átalakítás modellek testreszabása.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347886"
---
# <a name="what-is-custom-speech-service"></a>Mi az egyéni beszéd szolgáltatás?

Egyéni beszéd Service egy felhőalapú szolgáltatás, amely megadja a felhasználóknak vonatkozó beszéd szöveg beszéd átalakítás modellek testreszabása.
Az egyéni beszéd szolgáltatás használatára, tekintse meg a [egyéni beszéd portálon](https://cris.ai).

Az egyéni beszéd szolgáltatás lehetővé teszi testre szabott nyelvi modellek és az alkalmazás és a felhasználók igazított akusztikus modellek létrehozásához. Az egyéni Beszéd szolgáltatáshoz az adott beszéd és/vagy szöveges adat feltöltésével használható egyéni modellek is létrehozhat a Microsoft meglévő-az-a-legkorszerűbb beszéd modellek.

Például mobiltelefon, táblagép vagy PC-alkalmazás hozzáadása hang interakció, létrehozhat egyéni nyelvi modell, amely a Microsoft akusztikus modell kifejezetten az alkalmazás számára tervezett beszéd-szöveg-végpont létrehozása kombinálva. Ha az alkalmazás az adott környezetben vagy egy adott felhasználó sokaság használja tervezték, is létrehozása és központi telepítése egy egyéni akusztikus modell ezzel a szolgáltatással.


## <a name="how-do-speech-recognition-systems-work"></a>Hogyan működik a beszédfelismerés felismerés rendszerek?
Beszéd felismerés rendszerek álló, amelyek együttműködése több összetevőt. A legfontosabb összetevők kettő a akusztikus modell és a nyelvi modell.

A akusztikus modell besorolás, amely az hang rövid darabjai több Fonémák vagy a hang egység, egy adott nyelv valamelyikére. Például a word "beszéd" négy Fonémák "s p iy ch" tevődik össze. Ezek a besorolások másodpercenként százas nagyságrendben zajlanak le.

A nyelvi modell szószekvenciák valószínűségi eloszlását jelenti. A nyelvi modell segíti a rendszert abban, hogy válasszon a hasonló hangzású szószekvenciák közül. Ehhez maguknak a szószekvenciáknak a valószínűségét használja fel. A „recognize speech” és a „wreck a nice beach” például ugyanúgy hangzik, de az első változat sokkal valószínűbb, ezért magasabb pontszámot kap a nyelvi modellben.

A akusztikus és a nyelvi modell betanítási adatok tanult statisztikai modellek. Ennek eredményeképpen általuk legjobb szembesülnek alkalmazások való használata esetén a beszédfelismerés hasonló a betanítás során megfigyelt adatok esetén. A Microsoft beszéd-szöveg összetevőben akusztikus és nyelvi modellek rendelkezik betanítva a beszéd- és hatalmas gyűjteménye, és -az-a-legkorszerűbb teljesítményt biztosít a leggyakrabban használt használati forgatókönyvek esetén, például az intelligens a Cortana való interakció telefon, a táblagép vagy a számítógép, a webes keresés hanggal vagy diktálás "Friend" szöveges üzeneteket.

## <a name="why-use-the-custom-speech-service"></a>Az egyéni beszéd szolgáltatás miért érdemes használni?
Míg a Microsoft beszéd-szöveg motor világszínvonalú, céloz a fent leírt forgatókönyvek felé. Azonban ha hang lekérdezések magában foglalja az adott szóhasználatának elemek, például a termék nevét, vagy egy zsargon, amely ritkán fordul elő a tipikus beszéd átalakítás, az alkalmazás valószínű, hogy javítja a teljesítményt beszerezheti a nyelvi modell testreszabásával.

Ha például az alkalmazás hangalapú keresést végez az MSDN-ben, valószínű, hogy az olyan kifejezések, mint az „objektumorientált”, a „névtér” vagy a „dot net” gyakrabban fordulnak majd elő, mint más alkalmazások esetében. A nyelvi modell testreszabásával lehetővé válik, hogy a rendszer megtanulja ezeket.

## <a name="next-steps"></a>További lépések

Az egyéni beszéd szolgáltatás használatával kapcsolatos további információkért lásd: a [egyéni beszéd portálon] (https://cris.ai).

* [Első lépések](cognitive-services-custom-speech-get-started.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
