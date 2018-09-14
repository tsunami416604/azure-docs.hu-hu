---
title: Az Azure-ban a szöveg-beszéd átalakítás szolgáltatás – gyakran ismételt kérdések
description: Az szöveg-beszéd átalakítás szolgáltatással kapcsolatban a legnépszerűbb kérdésekre adott válaszok.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 2068bcb796b660847d0ea9b1126e64a9003ca871
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576850"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Szövegfelolvasás – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, tekintse meg az [más támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: Mi a különbség egy standard hangalapú modell és egyéni beszédfelismerési modellek között?**

**A**: A standard szintű hangalapú modell (más néven egy *hangtípusú*) rendelkezik betanítva Microsoft tulajdonú adatok használatával, és már üzembe helyezte a felhőben. Segítségével egyéni beszédfelismerési modellek vagy egy átlagos modell alkalmazkodnak és a timbre és a beszélő hangalapú stílus kifejezése vagy alapján a betanítási adatok, a felhasználó által készített teljes, az új modell betanításához. Napjainkban egyre több ügyfél használni kívánt egy egy-az-maga nemében egyedülálló, a vállalati arculattal hangalapú robotjaikat. Az egyéni beszédfelismerési-sestavuje se platforma a megfelelő választás az adott beállítási mód.

**K: hol kezdhetem standard beszédfelismerési modellek használata esetén?**

**A**: több mint 80 standard beszédfelismerési modelleket több mint 45 nyelvet a HTTP-kéréseken keresztül érhetők el. Először kérje le egy [előfizetési kulcs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Ahhoz, hogy a predeployed beszédfelismerési modellek REST-hívások, tekintse meg a [REST API-val](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**K: Ha szeretne egy testre szabott Hangüzenetek modellt használja, az API ugyanaz, mint a standard szintű beszédhangot használt?**

**A**: egyéni beszédfelismerési modellek létrehozása és üzembe helyezett, kap egy egyedi végpont a modellt. A beszédfelismerési mérnökeinkkel, az alkalmazások használatához adjon meg a végpont a HTTP-kérelmekre. Ugyanazokat a funkciókat az a szöveg-beszéd átalakítás szolgáltatás REST API-ban elérhető egyéni végpontra érhető el. Ismerje meg, hogyan [hozhat létre és használhat egyéni végpontra](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**K: van szükségem a betanítási adatok, a saját egyéni beszédfelismerési modelleket hozhat létre előkészítése?**

**A**: Igen, elő kell készítenie a betanítási adatok saját egyéni beszédfelismerési modellek esetében.

Beszéd adatok gyűjteménye, egy testre szabott Hangüzenetek modell létrehozásához szükséges. Ez a gyűjtemény hangfájlok speech felvételek, és a egy szövegfájlt, mindegyik hangfájlt vonatkozó áll. A digitális hangját eredményét az erősen támaszkodik a betanítási adatok minőségét. Egy jó szövegfelolvasás előállításához, fontos, hogy a felvételt a magas színvonalú, állandó mikrofon csendes szoba történik. Egy egységes kötet sebességét, és beszéljen és térközt, és még akkor is, konzisztencia, a beszéd kifejező mannerisms beszéd is, amellyel a nagyszerű digitális hangot alapvető. Erősen ajánlott az adott beszédhangot rögzítése egy felvételt studióban.

Nem jelenleg a Microsoft online felvétel támogatást, vagy felvétel studio javaslatokkal szolgálni. Lásd: a formátum követelmény [és előkészítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**K: mely parancsfájlok használjam jegyezze fel az egyéni beszédfelismerési képzéshez Beszédadatok?**

**A**: hangrögzítés a parancsfájlok nem korlátozzuk. A saját parancsfájlokat jegyezze fel a beszédet használhat. Csak győződjön meg arról, hogy rendelkezik-e elegendő fonetikus lefedettség a speech adatait. Egy egyéni beszédfelismerési betanításához elkezdheti kis mennyiségű Beszédadatok, amely 50 különböző mondatokat (körülbelül 3 – 5 percet beszéd) lehet. Minél több adatot ad meg, annál több természetes lesz a hangját. Egy teljes hangtípusú betanításához, ha adja meg a felvételt a több mint 2000 mondatok (körülbelül 3-4 óra beszéd) megkezdése. Egy kiváló minőségű, teljes hang lekéréséhez kell (körülbelül 8 – 10 óra beszéd) a több mint 6000 mondatok felvétel előkészítése.

További szolgáltatások segítő szkripteket a rögzítés lehetőséget biztosítunk. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) lekérdezésekhez.

**K: Mi történik, ha az alapértelmezett érték, vagy a portálon érhető el, mint a magasabb szintű egyidejűség érdekében van szükség?**

**A**: a modell 20 egyidejű kérelmek egységnyi növekményekben vertikálisan felskálázhatja. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) magasabb méretezésével kapcsolatos lekérdezések számára.

**K: helyezhetem saját modell letöltése és helyileg futtatja az alkalmazást?**

**A**: modellek nem tölthető le és helyben végrehajtott.

**K: a kérelmek szabályozva van?**

**A**: A REST API-25, 5 másodpercenként kérelmek korlátozza. Részletek tekintheti meg az oldalak [szöveg-beszéd átalakítás](text-to-speech.md). 

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kiadási megjegyzések](releasenotes.md)
