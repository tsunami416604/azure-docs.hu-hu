---
title: Gyakran ismételt kérdések a beszédfelismerés szöveg szolgáltatás az Azure-on |} Microsoft Docs
description: Az alábbiakban a beszédfelismerés szöveg legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082824"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Szöveg-beszéd átalakítás, gyakran ismételt kérdések

Ha ez a GYIK a kérdésekre adott válaszok nem található, próbálja meg az egyéni beszéd Service Közösség kérése a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) és [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Általános kérdések

**Kérdés**: Mi a különbség a normál és egyéni hang modellek között?

**Válasz**: A szabványos hang modellek (más néven hang betűtípusok) rendelkezik a Microsoft tulajdonában lévő adatok betanítva, és a már telepített a felhőben. Egyedi Hangüzenetek modellek engedélyezése a felhasználó vagy egy átlagos modellt támogató, és vigye át a timbre és a kifejezés módon a beszélőre hang stílus megfelelően, vagy a betanítási adatok, a felhasználó által készített alapján egy új, teljes modell betanításához. Napjainkban egyre több ügyfél szeretné a botok az egy-az-a-típus, a vállalati arculattal ellátott hangot. A platform felépítése egyedi hangüzenetek, amely a megfelelő választás.

**Kérdés**: Hogyan kezdjek hozzá, ha egy szabványos hang modell használni kívánt?

**Válasz**: több mint 80 szabványos hang modellek több mint 45 nyelvű HTTP-kérelmek keresztül érhetők el. Először be kell szereznie egy [előfizetés kulcs](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Ahhoz, hogy az előre telepített hang modellekhez REST-hívások, tekintse át a [részletesen Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Kérdés**: Ha a használni kívánt egy testre szabott Hangüzenetek modell, megegyezik az API-t a szabványos hangok?

**Válasz**: Ha az egyedi Hangüzenetek modell létrehozott és telepített, elérhetővé válik egy egyedi végpont a modell. Meg kell adnia a végpont a a HTTP-kérelmekre, a hang használatára képes legyen alkalmazni az alkalmazásokban. A szöveg-beszéd átalakítás szolgáltatás REST API-n keresztül elérhető ugyanezeket a funkciókat a egyéni végponthoz is érhető el. Lásd: hogyan [létrehozásáról és használatáról a egyéni végpont](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Kérdés**: van a betanítási adatok előkészítése egyedi Hangüzenetek modellek létrehozása a saját?

**Válasz**: készítse elő a betanítási adatok saját magának kell. Beszéd adatok gyűjteménye, a testre szabott Hangüzenetek modellek létrehozásához szükséges. Ez a gyűjtemény a beszédfelismerés felvételek hangfájlok, és a vonatkozó mindegyik hangfájlt szövegfájlba áll. A digitális hang eredményét fokozottan támaszkodik a betanítási adatok minőségét. Egy jó beszédfelismerő eredményezett, fontos, hogy a felvétel kiváló minőségű állandó mikrofon csendes helyiségben történik. Egységes kötet beszéd sebessége, és beszéljen térközt, és a beszéd kifejező mannerisms még akkor is, konzisztencia nélkülözhetetlenek a kiváló digitális hangot felépítése. Erősen ajánlott, hogy kell-e a hangok tárolja, amely egy rögzítése studio.
Jelenleg nem online rögzítése támogatásához és nem áll a rögzítés studio ajánlott. A formátum a követelményt, lásd: [felvételek és ki előkészítése](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Kérdés**:-parancsfájlok mit érdemes használni a beszéd adatoknak a egyedi Hangüzenetek betanításhoz? 

**Válasz**: jelenleg korlátozza a parancsfájlok Hangrögzítés. A saját parancsfájlok segítségével jegyezze fel a beszédfelismerés. Csak gondoskodjon elegendő fonetikus érvényességének a beszédfelismerés adatait. Egyedi Hangüzenetek betanításához elindíthatja beszéd adatokat, amelyek oka az lehet, 50 különböző mondat kis mennyiségű (körülbelül 3-5 perc beszéd). A több adatot ad meg, annál természetes lesz a hang. Egy teljes hang betűtípus betanítása, ha ad meg, hogy több mint 2000 mondat (körülbelül 3-4 órát beszéd) felvételek megkezdése. Ahhoz, hogy a jó minőségű teljes hang, szüksége lesz való felvételek több mint 6000 mondat (körülbelül 8-10 órát beszéd) előkészítése.  
Készítse elő a parancsfájlok a rögzítés a további szolgáltatások nyújtunk. Ügyfél [hang egyéni ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) lekérdezésekhez.

**Kérdés**: Mi történik, ha az alapértelmezett értéknél nagyobb feldolgozási kell, vagy hogy mit nyújt a portálon?

**Válasz**: legfeljebb 20 egyidejű kérelmek lépésekben a modell. Ügyfél [hang egyéni ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) nagyobb méretezés a lekérdezésekhez.

**Kérdés**: is szeretnék a modell letöltése és Futtatás helyben?

**Válasz**: modellek nem lehet letölti és helyben végrehajtott.

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kiadási megjegyzések](releasenotes.md)
