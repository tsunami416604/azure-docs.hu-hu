---
title: Text to Speech gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Választ kaphat a Text to Speech szolgáltatással kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: f06fda777cbebd8034ac4fd9254a4bd172d011a4
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661503"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Text to Speech gyakori kérdések

Ha nem talál választ a kérdéseire ebben a GYIK-ban, tekintse meg az [egyéb támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: mi a különbség a standard hangmodell és az egyéni hangmodell között?**

**A**: a standard hangmodell (más néven _Hangbetűkészlet_) a Microsoft tulajdonában lévő adataival lett kiképezve, és már telepítve van a felhőben. Használhat egyéni hangmodellt is egy átlagos modell módosításához, és átviheti a hangszóró hangstílusát és kifejezését, vagy betaníthat egy teljes, új modellt a felhasználó által készített betanítási adatok alapján. Napjainkban egyre több ügyfél szeretne egy egyfajta, márkás hangot használni a robotok számára. Az egyéni hangépítési platform az adott lehetőség számára megfelelő választás.

**K: hol kezdjek, ha szabványos hangmodellt szeretnék használni?**

**A**: a több mint 80 standard hangmodell több mint 45 nyelven érhető el http-kéréseken keresztül. Először szerezze be az [előfizetési kulcsot](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Az előtelepített hangmodellek REST-hívásainak meghívásához tekintse meg a [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**K: ha testreszabott hangmodellt kívánok használni, az API ugyanaz, mint amit a standard hangokhoz használ?**

**A**: egyéni hangmodell létrehozása és üzembe helyezése esetén a modell egyedi végpontot kap. Ha a hangját szeretné használni az alkalmazásokban, meg kell adnia a végpontot a HTTP-kérésekben. A Text to Speech szolgáltatás REST API elérhető funkciói az egyéni végponthoz is elérhetők. Megtudhatja, hogyan [hozhat létre és használhat egyéni végpontot](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint).

**K: elő kell készíteni a betanítási adataikat egyéni hangmodellek létrehozásához saját?**

Válasz **: igen**, a betanítási adatot saját kezűleg kell előkészítenie egy egyéni hangmodellhez.

A testreszabott hangmodell létrehozásához beszédfelismerési adatgyűjtemény szükséges. Ez a gyűjtemény a hanganyagokból álló hangfájlok, valamint az egyes hangfájlok átírására szolgáló szövegfájlok készletét tartalmazza. A digitális hang eredménye nagy mértékben támaszkodik a betanítási adatai minőségére. A jó szöveg-beszéd hang létrehozásához fontos, hogy a felvételek egy csendes helyen legyenek, magas színvonalú, állandó mikrofonnal. A hangvezéreltség, a beszéd és a hangvételi arány, valamint a beszéd kifejező színterének konzisztenciája elengedhetetlen a nagyszerű digitális hang létrehozásához. Javasoljuk, hogy rögzítse a hangokat egy regisztráló Studióban.

Jelenleg nem biztosítunk online rögzítési támogatást, vagy nem rendelkezik Studióval kapcsolatos javaslatokkal. A formátumra vonatkozó követelményekért lásd: [felvételek és átiratok előkészítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice).

**K: milyen szkripteket kell használni az egyéni hangképzéshez használt beszédfelismerési adatok rögzítéséhez?**

**A**: nem korlátozzák a szkripteket a hangrögzítéshez. A beszédfelismerést saját parancsfájlok használatával rögzítheti. Ügyeljen arra, hogy a beszédfelismerési adataiban elegendő legyen a fonetikus lefedettség. Egy egyéni hang betanításához elindíthat egy kis mennyiségű beszédfelismerési adat, amely 50 különböző mondatokat tartalmazhat (körülbelül 3-5 percnyi beszéd). Minél több adattal rendelkezik, annál természetesebb lesz a hangja. Megkezdheti a teljes hangbetűtípus betanítását, ha több mint 2 000 mondatot (körülbelül 3-4 órányi beszédet) ad meg. Magas színvonalú, teljes hangvételhez elő kell készítenie több mint 6 000 mondat (körülbelül 8-10 órányi beszéd) felvételét.

További szolgáltatásokat biztosítunk a parancsfájlok rögzítésére való előkészítéséhez. Érdeklődjön az [Egyéni hangalapú ügyfélszolgálatnál](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) .

**K: mit tegyek, ha az alapértelmezett értéknél vagy a portálon elérhetőnél nagyobb párhuzamosságra van szükség?**

**A**: akár 20 egyidejű kérést is felhasználhat a modell vertikális felskálázásához. A nagyobb skálázással kapcsolatos kérdésekért forduljon az [Egyéni hangalapú ügyfélszolgálathoz](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) .

**K: le tudom tölteni a modellt, és helyileg futtatom?**

**A: a**modellek nem tölthetők le és nem hajthatók végre helyileg.

**K: a kérelmek szabályozva vannak?**

**A**: lásd a [Speech Services kvótáit és korlátozásait](speech-services-quotas-and-limits.md).

## <a name="next-steps"></a>További lépések

- [Hibaelhárítás](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)
