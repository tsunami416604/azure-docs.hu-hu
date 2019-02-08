---
title: Az Azure-ban a szöveg-beszéd átalakítás szolgáltatás – gyakran ismételt kérdések
titleSuffix: Azure Cognitive Services
description: Az szöveg-beszéd átalakítás szolgáltatással kapcsolatban a legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: d93de4ff3f6dd00ac049ea9d83aebb7aad07137e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883287"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Szövegfelolvasás – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, tekintse meg az [más támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános

**K: Mi a különbség, standard beszédfelismerési modellek és a egy egyéni beszédfelismerési modell között?**

**A**: A standard szintű hangalapú modell (más néven egy *hangtípusú*) rendelkezik betanítva Microsoft tulajdonú adatok használatával, és már üzembe helyezte a felhőben. Segítségével egyéni beszédfelismerési modellek vagy egy átlagos modell alkalmazkodnak és a timbre és a beszélő hangalapú stílus kifejezése vagy alapján a betanítási adatok, a felhasználó által készített teljes, az új modell betanításához. Napjainkban egyre több ügyfél használni kívánt egy egy-az-maga nemében egyedülálló, a vállalati arculattal hangalapú robotjaikat. Az egyéni beszédfelismerési-sestavuje se platforma a megfelelő választás az adott beállítási mód.

**K: Hogyan kezdjek hozzá standard beszédfelismerési modellek használata esetén?**

**A**: Több mint 80 standard beszédfelismerési modelleket a több mint 45 nyelvet a HTTP-kéréseken keresztül érhetők el. Először kérje le egy [előfizetési kulcs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Ahhoz, hogy a predeployed beszédfelismerési modellek REST-hívások, tekintse meg a [REST API-val](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

**K: Ha szeretne egy testre szabott Hangüzenetek modellt használja, akkor az API-t ugyanaz, mint a standard szintű beszédhangot használt?**

**A**: Egyéni beszédfelismerési modellek létrehozása és üzembe helyezett, kap egy egyedi végpont a modellt. A beszédfelismerési mérnökeinkkel, az alkalmazások használatához adjon meg a végpont a HTTP-kérelmekre. Ugyanazokat a funkciókat az a szöveg-beszéd átalakítás szolgáltatás REST API-ban elérhető egyéni végpontra érhető el. Ismerje meg, hogyan [hozhat létre és használhat egyéni végpontra](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**K: Kell előkészíteni a betanítási adatok, a saját egyéni beszédfelismerési modelleket hozhat létre?**

**A**: Igen, el kell készítenie a betanítási adatok saját egyéni beszédfelismerési modellek esetében.

Beszéd adatok gyűjteménye, egy testre szabott Hangüzenetek modell létrehozásához szükséges. Ez a gyűjtemény hangfájlok speech felvételek, és a egy szövegfájlt, mindegyik hangfájlt vonatkozó áll. A digitális hangját eredményét az erősen támaszkodik a betanítási adatok minőségét. Egy jó szövegfelolvasás előállításához, fontos, hogy a felvételt a magas színvonalú, állandó mikrofon csendes szoba történik. Egy egységes kötet sebességét, és beszéljen és térközt, és még akkor is, konzisztencia, a beszéd kifejező mannerisms beszéd is, amellyel a nagyszerű digitális hangot alapvető. Erősen ajánlott az adott beszédhangot rögzítése egy felvételt studióban.

Nem jelenleg a Microsoft online felvétel támogatást, vagy felvétel studio javaslatokkal szolgálni. Lásd: a formátum követelmény [és előkészítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts).

**K: Milyen parancsfájlok használjam jegyezze fel az egyéni beszédfelismerési képzéshez Beszédadatok?**

**A**: A hangrögzítés parancsfájlok nem korlátozzuk. A saját parancsfájlokat jegyezze fel a beszédet használhat. Csak győződjön meg arról, hogy rendelkezik-e elegendő fonetikus lefedettség a speech adatait. Egy egyéni beszédfelismerési betanításához elkezdheti kis mennyiségű Beszédadatok, amely 50 különböző mondatokat (körülbelül 3 – 5 percet beszéd) lehet. Minél több adatot ad meg, annál több természetes lesz a hangját. Egy teljes hangtípusú betanításához, ha adja meg a felvételt a több mint 2000 mondatok (körülbelül 3-4 óra beszéd) megkezdése. Egy kiváló minőségű, teljes hang lekéréséhez kell (körülbelül 8 – 10 óra beszéd) a több mint 6000 mondatok felvétel előkészítése.

További szolgáltatások segítő szkripteket a rögzítés lehetőséget biztosítunk. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) lekérdezésekhez.

**K: Mi történik, ha az alapértelmezett érték, vagy a portálon érhető el, mint a magasabb szintű egyidejűség érdekében van szükség?**

**A**: A modell 20 egyidejű kérelmek egységnyi növekményekben vertikálisan felskálázhatja. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) magasabb méretezésével kapcsolatos lekérdezések számára.

**K: A modell letöltheti és helyileg futtatja az alkalmazást?**

**A**: Modellek nem tölthető le és helyben végrehajtott.

**K: Saját kérések szabályozott?**

**A**: A REST API-25, 5 másodpercenként kérelmek korlátozza. Részletek tekintheti meg az oldalak [szöveg-beszéd átalakítás](text-to-speech.md). 

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
