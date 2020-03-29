---
title: Arcfelismerési és attribútumfogalmak
titleSuffix: Azure Cognitive Services
description: Az arcfelismerés az emberi arcok képbe való benyomásának és az opcionálisan különböző arcalapú adatok visszaadásának művelete.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73743051"
---
# <a name="face-detection-and-attributes"></a>Arcfelismerés és attribútumok

Ez a cikk ismerteti az arcfelismerés és az arcattribútum-adatok fogalmait. Az arcfelismerés az emberi arcok képbe való benyomásának és az opcionálisan különböző arcalapú adatok visszaadásának művelete.

A [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) művelettel észlelheti a képen lévő arcokat. Legalább minden észlelt lap megfelel egy faceTéglalap mező a válaszban. Ez a bal, felső, szélesség és magasság képpontkoordinátái jelzik a található felületet. Ezekkel a koordinátákkal megkaphatja az arc helyét és méretét. Az API-válaszban az arcok méretsorrendben vannak felsorolva a legnagyobbtól a legkisebbig.

## <a name="face-id"></a>Arcazonosító

Az arcazonosító a kép minden egyes észlelt arcának egyedi azonosítókarakterlánca. Arcazonosítót kérhet az [Arc – API-hívás észlelése.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-landmarks"></a>Arcrészek

Face tereptárgyak egy sor könnyen megtalálható pontokat az arcon, mint például a diákok vagy az orr csúcsa. Alapértelmezés szerint 27 előre meghatározott arcpont van. Az alábbi ábra mind a 27 pontot mutatja:

![Arcdiagram mind a 27 terepábrával](../Images/landmarks.1.jpg)

A pontok koordinátáit a rendszer képpontokban adja vissza.

## <a name="attributes"></a>Attribútumok

Az attribútumok olyan funkciók, amelyeket a [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API érzékel. A következő attribútumok észlelhetők:

* **Életkor**. A becsült életkor egy adott arc éveiben.
* **Blur**. Az arc elmosódottsága a képen. Ez az attribútum nulla és egy közötti értéket, valamint alacsony, közepes vagy magas informális minősítést ad eredményül.
* **Érzelem**. Az érzelmek listája az adott arc érzékelési magabiztosságával. A megbízhatósági pontszámok normalizálódnak, és az összes érzelem pontszáma ily módon egy. A visszatért érzelmek a boldogság, szomorúság, semleges, harag, megvetés, undor, meglepetés és félelem.
* **Expozíció**. Az expozíció az arc a képen. Ez az attribútum nulla és egy közötti értéket, valamint az alulexponálás, a goodExposure vagy a overExposure nem hivatalos minősítését adja vissza.
* **Arcszőrzet**. Az arcszőrzet becsült jelenléte és az adott arc hossza.
* **Nem**. Az adott arc becsült neme. Lehetséges értékek a férfi, nő, és a nemek nélküli.
* **Szemüveget.** Hogy az adott arcnak van-e szemüvege. Lehetséges értékek: NoGlasses, ReadingGlasses, Napszemüveg és Úszó szemüveg.
* **Haj**. Az arc hajtípusa. Ez az attribútum megmutatja, hogy a haj látható-e, hogy a kopaszság észlelhető-e, és milyen hajszíneket észlel.
* **Fej póz**. Az arc tájolása a 3D térben. Ezt az attribútumot a hangmagasság, a tekercs és a szögek fokban írják le. Az értéktartományok -90 foktól 90 fokig, -180 foktól 180 fokig, és -90 foktól 90 fokig terjednek. A szögleképezéseket az alábbi ábrán olvassa el:

    ![A fej a pályán, roll, és yaw tengelyek feliratú](../Images/headpose.1.jpg)
* **Smink**. Hogy az arc smink. Ez az attribútum logikai értéket ad vissza az eyeMakeup és alipMakeup számára.
* **Zaj**. Az arcképen észlelt vizuális zaj. Ez az attribútum nulla és egy közötti értéket, valamint alacsony, közepes vagy magas informális minősítést ad eredményül.
* **Elklúzió .** Azt jelzik, hogy vannak-e olyan objektumok, amelyek eltorlaszolják az arc egyes részeit. Ez az attribútum logikai értéket ad vissza a szemoccszolt, a homlokkihasznált és a mouthOccluded értékhez.
* **Mosolyogj.** Az adott arc mosolykifejezése. Ez az érték a nulla között nincs mosoly, és egy tiszta mosoly.

> [!IMPORTANT]
> Az arcattribútumok előrejelzése statisztikai algoritmusok használatával történik. Lehet, hogy nem mindig pontosak. Legyen körültekintő, amikor attribútumadatok alapján hoz döntéseket.

## <a name="input-data"></a>Bemeneti adatok

Az alábbi tippek segítségével győződjön meg arról, hogy a bemeneti képek a legpontosabb észlelési eredményeket adják:

* A támogatott bemeneti képformátumok a következők: JPEG, PNG, GIF az első képkocka és A BMP.
* A képfájl mérete nem lehet nagyobb 4 MB-nál.
* A kimutatható arcméret tartomány 36 x 36 és 4096 x 4096 képpont között van. Az ezen a tartományon kívüli arcokat a rendszer nem észleli.
* Előfordulhat, hogy egyes arcok technikai kihívások miatt nem észlelhetők. Az extrém arcszögek (fejpóz) vagy az arc elzáródása (olyan tárgyak, mint a napszemüveg vagy az arc egy részét eltorlaszoló kezek) befolyásolhatják az észlelést. A frontális és a közel-frontális arcok adják a legjobb eredményt.

Ha arcokat észlel egy videofelvételen, a videokamera bizonyos beállításainak módosításával javíthatja a teljesítményt:

* **Simítás**: Sok videokamera alkalmaz simító hatást. Kapcsolja ki, ha tudja, mert életlenítést hoz létre a keretek között, és csökkenti a tisztaságot.
* **Záridő:** A gyorsabb záridő csökkenti a keretek közötti mozgás mértékét, és minden egyes képkockát tisztábbá tesz. Javasoljuk, hogy a záridő 1/60 másodperc vagy gyorsabb.
* **Zárszög:** Egyes kamerák zárszöget határoznak meg a záridő helyett. Ha lehetséges, használjon alacsonyabb zárszöget. Ez tisztább videoképkockákat eredményez.

    >[!NOTE]
    > Az alacsonyabb zárszögű fényképezőgépek minden képkockában kevesebb fényt kapnak, így a kép sötétebb lesz. Meg kell határoznia a megfelelő szintet a használatához.

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri az arcfelismerési fogalmakat, ismerje meg, hogyan írhat olyan parancsfájlt, amely észleli az arcokat egy adott képen.

* [Arcok felismerése a képeken](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)