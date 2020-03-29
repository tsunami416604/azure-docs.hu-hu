---
title: Szövegfelolvasás – gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Válaszok a Szövegfelolvasó szolgáltatással kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110498"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Szövegfelolvasás – gyakori kérdések

Ha ebben a gyIK-ben nem talál választ kérdéseire, tekintse meg [az egyéb támogatási lehetőségeket.](support.md)

## <a name="general"></a>Általános kérdések

**K: Mi a különbség a szabványos hangmodell és az egyéni hangmodell között?**

**A**: A szabványos hangmodell (más néven _hangbetűtípus)_ a Microsoft tulajdonában lévő adatok használatával lett betanítva, és már telepítve van a felhőben. Egyéni hangmodell használatával adaptálhat egy átlagos modellt, és átviheti a hangszóró hangstílusának hangszínét és kifejezését, vagy betaníthat egy teljes, új modellt a felhasználó által készített betanítási adatok alapján. Ma egyre több ügyfél akar egy-az-egy-fajta, márkás hang a botok. Az egyéni hangépítő platform a megfelelő választás erre a lehetőségre.

**K: Hol kezdjem, ha szabványos hangmodellt szeretnék használni?**

**A**: Több mint 80 szabványos hangmodell több mint 45 nyelven érhető el HTTP-kérelmeken keresztül. Először is, kap egy [előfizetési kulcsot](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Ha REST-hívásokat szeretne kezdeményezni az előre telepített hangmodellekhez, olvassa el a [REST API-t.](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)

**K: Ha testre szabott hangmodellt szeretnék használni, az API ugyanaz, mint a szabványos hangokhoz használt API?**

**A**: Egyéni hangmodell létrehozásakor és üzembe helyezésekor egy egyedi végpontot kap a modellhez. Ahhoz, hogy a hang az alkalmazásokban beszéljen, meg kell adnia a végpontot a HTTP-kérelmekben. A REST API-ban a text to speech szolgáltatás hoz rendelkezésre álló funkciók az egyéni végponthoz érhető el. További információ az [egyéni végpont létrehozásáról és használatáról.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint)

**K: Elő kell készítenem a betanítási adatokat, hogy saját magam hozzam létre az egyéni hangmodelleket?**

**A**: Igen, elő kell készítenie a betanítási adatokat magát egy egyéni hangmodell.

A testreszabott hangmodell létrehozásához beszédadatok gyűjteménye szükséges. Ez a gyűjtemény egy sor hangfájlok beszédfelvételek és egy szöveges fájlt az átírás minden audio fájlt. A digitális hang eredménye nagymértékben függ a betanítási adatok minőségéből. A jó szövegfelolvasó hang érdekében fontos, hogy a felvételek egy csendes szobában, kiváló minőségű, álló mikrofonnal legyenek eltántikulása. A hangmennyiség, a beszédsebesség és a beszédhangmagasság, valamint a beszéd kifejező modorának következetessége elengedhetetlen a nagyszerű digitális hang kialakításához. Javasoljuk, hogy a hangokat egy stúdióban rögzítsék.

Jelenleg nem nyújtunk online felvétel támogatást, és nem rendelkezünk semmilyen stúdióajánlást. A formátumkövetelményről a [felvételek és átiratok elkészítéséről](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice)itt látható.

**K: Milyen parancsfájlok segítségével rögzítsem a beszédadatokat az egyéni hangbetanításhoz?**

**A**: Nem korlátozzuk a hangfelvétel parancsfájljait. A beszéd rögzítéséhez használhatja a saját parancsfájljait. Csak győződjön meg arról, hogy elegendő fonetikus lefedettség a beszéd adatokat. Egyéni hang betanításához elkezdheti egy kis mennyiségű beszédadattal, amely 50 különböző mondat (kb. 3–5 perc beszéd) lehet. Minél több adatot ad meg, annál természetesebb lesz a hangja. Elkezdheti betanítását a teljes hangbetűtípus, ha több mint 2000 mondat (kb. 3-4 óra beszéd) felvételeit adja meg. Ahhoz, hogy kiváló minőségű, teljes hangot kapjon, több mint 6000 mondat (kb. 8-10 óra beszéd) felvételeit kell készítenie.

További szolgáltatásokat nyújtunk a parancsfájlok felvételhez való előkészítéséhez. Kérdéseivel forduljon a [Custom Voice ügyfélszolgálatához.](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)

**K: Mi a teendő, ha az alapértelmezett értéknél nagyobb egyidejűségre van szükségem, vagy mi van felkínálva a portálon?**

**A**: A modell 20 egyidejű kérelem lépésekben skálázható. Lépjen kapcsolatba a [Custom Voice ügyfélszolgálatával](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) a magasabb skálázással kapcsolatos kérdéseiért.

**K: Letölthetem a modellt, és helyileg futtathatom?**

**A**: A modellek nem tölthetők le és nem hajthatók végre helyileg.

**K: A kéréseim szabályozottak?**

**A**: A REST API 5 másodpercenként 25-re korlátozza a kérelmeket. Részletek találhatók oldalainkon [a text to speech](text-to-speech.md).

## <a name="next-steps"></a>További lépések

- [hibaelhárítással](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)
