---
title: Gyakran feltett kérdés a Speech to Text szolgáltatás az Azure-ban
description: Az alábbiakban a szöveg-hang transzformációs legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 8d70c4a359c713d6c5f46423193e9c9e7e1f3baf
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282857"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Szövegfelolvasás – gyakori kérdések

Ha nem találja a kérdésekre adott válaszok a gyakori kérdések, tekintse meg a támogatási lehetőségek [Itt](support.md).

## <a name="general"></a>Általános kérdések

**Kérdés**: Mi a különbség a standard és egyéni beszédfelismerési modellek között?

**Válasz**: A standard szintű beszédfelismerési modellek (más néven) rendelkezik a Microsoft tulajdonában lévő adatok betanítva hangtípust), és már üzembe helyezte a felhőben. Egyéni beszédfelismerési modelleket engedélyezi a felhasználó vagy egy átlagos modell alkalmazkodnak és a timbre és a megfelelően a beszélő hangalapú stílusa kifejezés módon, vagy a betanítási adatok, a felhasználó által készített alapján egy új, teljes modell betanításához. Egyre több ügyfél Ma szeretné robotjaikat számára egy egy-az-maga nemében egyedülálló, a vállalati arculattal hangalapú rendelkezik. Az egyéni hangkészítő platform, amely a megfelelő választás.

**Kérdés**: Hogyan kezdjek hozzá standard beszédfelismerési modellek használata esetén?

**Válasz**: több mint 80 standard beszédfelismerési modelleket több mint 45 nyelvet a HTTP-kéréseken keresztül érhetők el. Először le szeretné kérni egy [előfizetési kulcs](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Ahhoz, hogy az előre telepített beszédfelismerési modellek REST-hívások, tekintse meg a [részletesen Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Kérdés**: szeretne egy testre szabott Hangüzenetek modellt használja, ha megegyezik az API a standard szintű beszédhangot?

**Válasz**: Ha az egyéni beszédfelismerési modell létrehozott és telepített, kap a modellhez tartozó egyedi végpont. Meg kell adnia a végpont a HTTP-kérelmekre, használja a hangját mérnökeinkkel, az alkalmazásokban található. Egyéni végpontra is érhető el az szöveg-hang transzformációs szolgáltatás a REST API-n keresztül elérhető ugyanazokat a funkciókat. Lásd: hogyan [hozhat létre és használhat egyéni végpontra](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Kérdés**: van szükségem a betanítási adatok előkészítése a saját egyéni beszédfelismerési modellek létrehozására?

**Válasz**: készítse elő a betanítási adatok saját magának kell. Beszéd adatok gyűjteménye, egy testre szabott Hangüzenetek modell létrehozásához szükséges. Ez a gyűjtemény hangfájlokat, a beszéd felvételek, és a egy szövegfájlt, mindegyik hangfájlt vonatkozó áll. A digitális hangját eredményét az erősen támaszkodik a betanítási adatok minőségét. Egy jó beszédfelismerő előállításához, fontos, hogy a felvételt a magas színvonalú, állandó mikrofon csendes szoba történik. Egységes kötet, és beszéljen sebesség, a beszéd, a terv lényegét, és még akkor is, konzisztencia, a beszéd kifejező mannerisms elengedhetetlenek, amellyel a nagyszerű digitális hangot. Kifejezetten ajánljuk, hogy a rögzítés stúdió rögzítve beszédhangot rendelkeznie kell.
Jelenleg nem online felvétel támogatást és rögzítés studio javaslatokkal szolgálni. A formátum követelmény, lásd: [és előkészítése](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Kérdés**: milyen parancsfájlokat kell használnom, a beszéd adatoknak, egyéni hangalapú képzéshez? 

**Válasz**: Mi nem korlátozzák a hangrögzítés parancsfájlok. A saját parancsfájlokat jegyezze fel a beszédet használhat. Csak ellenőrizze elegendő fonetikus lefedettség a speech adatait. Kis mennyiségű Beszédadatok, amely lehet 50 különböző mondatokat megkezdése egy egyéni beszédfelismerési betanításához (beszéd körülbelül 3-5 perc). Minél több adatot ad meg, annál több természetes lesz a hangját. Egy teljes hangtípusú betanításához, ha adja meg a felvételt a 2000-nél több mondatok (körülbelül 3-4 óra beszéd) megkezdése. Egy kiváló minőségű teljes hangalapú lekéréséhez kell (körülbelül 8 – 10 óra beszéd) a több mint 6000 mondatok felvétel előkészítése.  
További szolgáltatások segítő szkripteket a rögzítés lehetőséget biztosítunk. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) lekérdezésekhez.

**Kérdés**: Mi történik, ha az alapértelmezett értéknél magasabb szintű egyidejűség érdekében van szükség, vagy mi érhető el a portálon?

**Válasz**: a modell 20 egyidejű kérelmek egységnyi növekményekben vertikálisan felskálázhatja. Kapcsolattartó [egyéni beszédfelismerési ügyfél-támogatási](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) nagyobb méretezés a lekérdezések számára.

**Kérdés**: I letöltheti futtassam és helyileg futtatja az alkalmazást?

**Válasz**: modellek nem tölthető le és helyben végrehajtott.

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kiadási megjegyzések](releasenotes.md)
