---
title: Egyéni beszédszolgáltatás áttekintése az Azure-ban |} A Microsoft Docs
description: A Custom Speech Service egy felhőalapú szolgáltatás, amely lehetővé teszi a felhasználók szabhatja testre a hang-szöveg transzformációs beszédátírási beszédmodellek.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948366"
---
# <a name="what-is-custom-speech-service"></a>Mi a Custom Speech Service?

A Custom Speech Service egy felhőalapú szolgáltatás, amely megadja a felhasználóknak a hang-szöveg transzformációs beszédátírási beszédmodellek testre szabhatók.
A Custom Speech Service használatára, tekintse meg a [Custom Speech Service-portál](https://cris.ai).

A Custom Speech Service lehetővé teszi, hogy testreszabott nyelvi modell és az alkalmazás és a felhasználók személyre szabott akusztikai modell létrehozásához. A tartományspecifikus beszédtartalom és/vagy szöveges adatok feltöltése a Custom Speech Service, egyéni modelleket, használható együtt a Microsoft meglévő állapota-a-a legújabb beszédfelismerési modelleket hozhat létre.

Például ha hangalapú interakció mobiltelefonra, táblagépen vagy PC-alkalmazást, létrehozhat egyéni nyelvi modell, amely a Microsoft akusztikai modell létrehozásához egy hang-szöveg transzformációs végpontot az alkalmazás kifejezetten mobilalkalmazásokhoz tervezett kombinálva. Ha az alkalmazás egy adott környezetben, vagy egy adott felhasználói körhöz használatra készült, is létrehozhat és ezt a szolgáltatást egyéni akusztikai modell üzembe helyezése.


## <a name="how-do-speech-recognition-systems-work"></a>Hogyan működik a speech recognition rendszerek?
Speech recognition rendszerek, amelyek együttműködve több összetevőből állnak. Az akusztikai modell és a nyelvi modell közül kettő, a legfontosabb összetevők.

Az akusztikai modell, amely egy több fonémából vagy hang egység, az adott nyelv fonémáiként feliratok besorolás. A "speech" szó például négy fonémából "s, p, í, CS" áll. Ezek a besorolások másodpercenként százas nagyságrendben zajlanak le.

A nyelvi modell szószekvenciák valószínűségi eloszlását jelenti. A nyelvi modell segíti a rendszert abban, hogy válasszon a hasonló hangzású szószekvenciák közül. Ehhez maguknak a szószekvenciáknak a valószínűségét használja fel. A „recognize speech” és a „wreck a nice beach” például ugyanúgy hangzik, de az első változat sokkal valószínűbb, ezért magasabb pontszámot kap a nyelvi modellben.

Az akusztikai és nyelvi modell olyan származó betanítási adatok statisztikai modell. Ennek eredményeképpen ezek teljesítenek a legjobban tapasztalnak, amikor az alkalmazások használják a speech hasonló megfigyelt betanítás során az adatok esetén. Az akusztikai és nyelvi modelleket a Microsoft hang-szöveg transzformációs motor rendelkezik betanítva beszéd- és a szöveg egy hatalmas mennyiségű gyűjteményében, és a leggyakoribb használati forgatókönyvek, például az intelligens a Cortana használata a állapota-a-a legújabb teljesítményt telefonon, táblagépen vagy számítógép, a webes keresés hanggal vagy durva kifejezéseket mondjon ki egy barátjának szöveges üzeneteket.

## <a name="why-use-the-custom-speech-service"></a>Miért érdemes használni a Custom Speech Service?
Bár a Microsoft hang-szöveg transzformációs motor világszínvonalú, akkor a fent leírt forgatókönyveket célozza. Azonban ha azt az adott szöveg szóhasználati elemek, például termékneveket vagy beszédben ritkán fordul elő a tipikus, amely tartalmazza az alkalmazás lekérdezések valószínű, hogy jobb teljesítmény érdekében szerezhet a nyelvi modell testreszabásával.

Ha például az alkalmazás hangalapú keresést végez az MSDN-ben, valószínű, hogy az olyan kifejezések, mint az „objektumorientált”, a „névtér” vagy a „dot net” gyakrabban fordulnak majd elő, mint más alkalmazások esetében. A nyelvi modell testreszabásával lehetővé válik, hogy a rendszer megtanulja ezeket.

## <a name="next-steps"></a>További lépések

A Custom Speech Service használatával kapcsolatos további információkért tekintse meg a [Custom Speech Service Portal] (https://cris.ai).

* [Első lépések](cognitive-services-custom-speech-get-started.md)
* [GYIK](cognitive-services-custom-speech-faq.md)
* [Szószedet](cognitive-services-custom-speech-glossary.md)
