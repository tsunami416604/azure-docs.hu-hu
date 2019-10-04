---
title: Arcfelismerés és attribútumok fogalmak
titleSuffix: Azure Cognitive Services
description: Ismerje meg az arcfelismerés és arctulajdonságok kapcsolatos alapvető fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65891145"
---
# <a name="face-detection-and-attributes"></a>Arcfelismerés és attribútumok

Ez a cikk ismerteti az arcfelismerés és a face attribútumadatok visszaadásához. Arcfelismerés a emberi arcok megkeresése a képet, és igény szerint a különböző típusú face kapcsolatos adatokat visszaadó művelet.

Használja a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) arcfelismerés képen egy művelet. Minimális minden észlelt face faceRectangle kitölteni a válaszban felel meg. A bal oldalon, felül, szélességét és magasságát képpontban koordináták készletét a található face megjelölni. Használja ezeket a koordinátákat, beszerezheti az arcfelismerés és annak méretét. Az API-válasz az arcok mérete sorrend a legnagyobbtól a legkisebbig láthatók.

## <a name="face-id"></a>Arc azonosítója

A face ID: minden észlelt lap a képen egyedi azonosító karakterlánc. Kérheti a face ID, az a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-hívás.

## <a name="face-landmarks"></a>Arcrészek

Arcrészek olyan, könnyen megtalálható pontok a egy oldallal, például a tanulók vagy az én csúcsa. Alapértelmezés szerint 27 előre meghatározott arcpont van. A következő ábrán látható összes 27 pont:

![A címkével ellátott összes 27 arcrész face diagram](../Images/landmarks.1.jpg)

A pontok koordinátáit adja vissza képpontban megadva.

## <a name="attributes"></a>Attribútumok

Attribútumok olyan szolgáltatások készlete, amelyek igény szerint által észlelt a [arc - észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-t. A következő attribútumok észlelése:

* **Kor**. Egy adott arc éves becsült kora.
* **Elmossa**. Az az ábrán a face blurriness. Ez az attribútum értéke egy nulla és a egy és a egy informális minősítése alacsony, közepes vagy magas.
* **Érzelemfelismerési**. Az észlelési magabiztosan az adott személyt az érzelmek listája. Megbízhatósági pontszámok van normalizálva, és az összes érzelmek között pontszámok hozzá akár egy. A visszaadott érzelmek boldogság, szomorúság, semleges, düh, megvetés, undor, meglepő és lehetőségének.
* **Kitettség**. Az arcok a képen vannak kitéve. Ez az attribútum nulla és a egy és a egy informális minősítése alulexponál, goodExposure vagy túlexponálás beállításával közötti értéket ad vissza.
* **Arcfelismerési haj**. A becsült arcfelismerési haj jelenléte és a hossza az adott személyt.
* **A nemek**. Becsült tartalmazzák az adott személyt. Lehetséges értékek: férfi női és genderless.
* **Szemüveg**. Van-e az adott személyt szemüveg. Lehetséges értékek: NoGlasses, ReadingGlasses, napszemüvegeket és Úszóklub védõszemüveg.
* **Haj**. A face haj típusa. Ez az attribútum a haj e látható, hogy baldness észlelt, és milyen haj színek észlelt jeleníti meg.
* **A fő jelentenek**. A face tájolás a 3D terület. Ez az attribútum a terv lényegét, visszaállítási és kitérése szögek fokban írja le. A értéktartományú rendre olyan-90 fokkal 90 fokos, - 180 fokos 180 fokkal, illetve -90 fokos, és 90 fok. Az alábbi ábrában szög leképezések:

    ![Egy fő, a térköz-visszaállítás, és yaw tengelyek címkével](../Images/headpose.1.jpg)
* **Makeup**. Van-e a face makeup. Ez az attribútum a eyeMakeup és lipMakeup egy logikai értéket ad vissza.
* **Zaj**. A vizuális zaj észlelt az arcfelismerés képen. Ez az attribútum értéke egy nulla és a egy és a egy informális minősítése alacsony, közepes vagy magas.
* **Hangelnyelés**. Vannak-e objektumok a face blokkoló részeit. Ez az attribútum a eyeOccluded, foreheadOccluded és mouthOccluded egy logikai értéket ad vissza.
* **Mosolyogjon**. Az adott személyt mosoly kifejezés. Ez az érték nem mosoly nulla és a egy egyértelmű mosoly között van.

> [!IMPORTANT]
> Olyan tulajdonságok vannak előre jelzett statisztikai algoritmusokkal. Ezek mindig nem pontos. Legyen körültekintő, amikor attribútum adatokon alapuló döntések.

## <a name="input-data"></a>Bemeneti adatok

Az alábbi tippek segítségével győződjön meg arról, hogy a bemeneti képekhez adnak a legpontosabb észlelésének eredménye:

* A bemeneti formátum támogatott JPEG, PNG, GIF és BMP első keret.
* A kép fájlmérete kell nem lehet nagyobb, mint 4 MB.
* Az észlelhető oldallal mérete tartománya 36 x 36, 4096 x 4096 képpont. Arcok a tartományon kívül nem észlelhető.
* Néhány arcok előfordulhat, hogy észlelhető kihívások miatt. Szélsőséges face szög (fő testtartás) vagy a face hangelnyelés (például napszemüvegeket vagy teljes letiltása a face része, objektumok) hatással lehet az észlelés. Elülső és a közel-elülső arcok adja a legjobb eredmények elérése érdekében.

Csatorna videoklipet arcokat feltárásának előfordulhat, egyes a kamerát a beállítások hangolását a teljesítmény javítása érdekében:

* **Simítás**: Számos videokamera élsimítási effektus alkalmazása. Célszerű kikapcsolni ez Ha lehetséges, mert egy életlenítés keretek között hoz létre, és csökkenti az egyértelműség érdekében.
* **Zár sebesség**: Zár gyorsabb csökkenti a mozgásban lévő adatoknak egyaránt keretek között, és lehetővé teszi az egyes világosabb keretet. Azt javasoljuk, hogy 1/60 másodperc vagy annál gyorsabb zár megbízhatóbbak.
* **Szög zár**: Egyes kamerák zár szög zár sebessége helyett adja meg. Egy kisebb zár szög lemezrendszert használjon. Egyértelműbb videókban eredményez.

    >[!NOTE]
    > Így a lemezkép sötétebb kamerával rendelkező egy kisebb zár szög kevesebb világos fog kapni az egyes keretek. Határozza meg a megfelelő szintű használni kell.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri, a face észlelési fogalmak, megtudhatja, hogyan írható olyan szkript, amely a megadott lemezkép arcokat észleli.

* [A kép arcok észlelése](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)