---
title: Signal Gate beállítása eseményvezérelt videofelvételhez – Azure
description: Ez a cikk útmutatást nyújt ahhoz, hogyan konfigurálható a Signal Gate egy Media Graph-ban.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380173"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Signal Gate beállítása eseményvezérelt videofelvételhez

A Media Graph-ban a [Signal Gate processzor csomópont](media-graph-concept.md#signal-gate-processor) lehetővé teszi az adathordozó továbbítását az egyik csomópontról a másikra, ha a kaput egy esemény indítja el. Indításkor a kapu megnyílik, és lehetővé teszi a média áramlását a megadott időtartamra. A kapu kiváltásához szükséges események hiányában a kapu leáll, és a média abbahagyja a folyamatot. A Signal Gate-feldolgozó az esemény-alapú videofelvételek esetében alkalmazható.
Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Signal Gate-processzort.

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás
-   [Médiagrafikon](media-graph-concept.md)
-   [Eseményalapú videófelvétel](event-based-video-recording-concept.md)


## <a name="problem"></a>Probléma
Előfordulhat, hogy a felhasználó egy esemény elindítása előtt vagy után egy adott időt szeretne rögzíteni. A felhasználó ismeri a rendszeren belüli elfogadható késést, így a felhasználó meg kívánja határozni a Signal Gate processzor késését. A felhasználónak meg kell adnia a legrövidebb és leghosszabb, hogy a rögzítés időtartama nem számít, hogy hány új esemény érkezik.
 
### <a name="use-case-scenario"></a>Használatieset-forgatókönyvek
Tegyük fel, hogy minden alkalommal rögzíteni szeretné a videót, amikor megnyílik az épület ajtaja. Azt szeretné, hogy a rögzítés részét képező ajtó előtt **X** másodperc legyen megnyitva. A rögzítést legalább **Y** másodpercre szeretné állítani, ha az ajtó nincs újra megnyitva. Azt szeretné, hogy a rögzítés legfeljebb **z** másodpercig legyen, ha az ajtó többször is meg van nyitva. Biztos lehet benne, hogy az ajtó érzékelője másodperces késéssel rendelkezik, és szeretné csökkenteni az események figyelmen kívül **hagyásával való** esélyét ("késői érkezések"), ezért legalább **K** másodpercet szeretne engedélyezni az események megérkezéséhez.


## <a name="solution"></a>Megoldás

*A *_Signal Gate processzor paramétereinek módosítása_* _

A Signal Gate processzor 4 paraméterrel van konfigurálva:
- _ *aktiválás-értékelési ablak**
- **aktiválási jel eltolása**
- **minimális aktiválási ablak**
- **aktiválás maximális száma ablak**. 

A Signal Gate-processzor aktiválása után a rendszer nyitva marad az aktiválás minimális ideje alatt. Az aktiválási esemény a legkorábbi esemény időbélyegzője, valamint az aktiválási jel eltolásával kezdődik. Ha a Signal Gate-processzor újra aktiválódik, amíg meg van nyitva, az időzítő alaphelyzetbe áll, és a kapu nyitva marad legalább az aktiválás minimális ideje alatt. A Signal Gate-processzor soha nem marad meg a maximális aktiválási időpontnál. Egy esemény **(1. esemény)** , amely a média időbélyegei alapján egy másik esemény **(2. esemény)** előtt következik be, a rendszer figyelmen kívül hagyja, ha a rendszer elmarad, és az **1** . esemény a **2. eseményt** követően megérkezik a Signal Gate-feldolgozóba. Ha az **1** . esemény nem érkezik meg a **2. esemény** és az **aktiválás kiértékelése ablak** között, a rendszer figyelmen kívül hagyja az 1. **eseményt** , és nem továbbítja a jelet a Signal Gate-processzoron. A korrelációs azonosítók minden eseményhez be vannak állítva. Ezek az azonosítók a kezdeti eseményből vannak beállítva, és az egyes következő eseményekhez szekvenciálisak.

> [!IMPORTANT]
> A média ideje az adathordozó időbélyegzője alapján történik, amikor egy esemény bekövetkezett az adathordozón. Előfordulhat, hogy a Signal Gate-be érkező események sora nem tükrözi a médiában beérkező események sorát.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Paraméterek: (az alapján, hogy az események Mikor érkeznek meg fizikai időben a Signal Gate-be)

* **minimumActivationTime (a rögzítés legrövidebb időtartama)** = a másodpercenként megnyíló másodpercek száma az új események fogadása után, kivéve, ha a **maximumActivationTime** megszakítja a jelet
* a **maximumActivationTime (egy rögzítés leghosszabb lehetséges időtartama)** = a kezdeti eseménytől az a másodpercben megadott idő, ameddig a Signal Gate-processzor nyitva marad, miután új események fogadására kerül sor, függetlenül attól, hogy milyen események érkeznek
* **activationSignalOffset** = a Signal Gate-feldolgozó aktiválása és a videofelvétel megkezdése között eltelt másodpercek száma, jellemzően ez az érték negatív, a rögzítés elindításához a kiváltó esemény előtt
* **activationEvaluationWindow** = a kezdeti kiváltó eseménytől kezdődő másodpercek száma, amelyben a kezdeti esemény előtt bekövetkezett esemény a média ideje alatt a Signal Gate-feldolgozónak meg kell érkeznie, mielőtt figyelmen kívül lenne hagyva, és "késve érkezik"

> [!NOTE]
> A késői beérkezés minden olyan esemény, amely az aktiválási kiértékelési időszak lejártát követően érkezik, de ez az esemény a kezdeti esemény előtt érkezett.

### <a name="limits-of-parameters"></a>Paraméterek korlátai

* **activationEvaluationWindow: 0 másodperc – 10 másodperc**

* **activationSignalOffset: – 1 perc – 1 perc**

* **minimumActivationTime: 1 másodperc – 1 óra**

* **maximumActivationTime: 1 másodperc – 1 óra**


A használati eset alapján a paraméterek a következőképpen lesznek beállítva:

* **activationEvaluationWindow = K mp**
* **activationSignalOffset =-X mp**
* **minimumActivationWindow = Y mp**
* **maximumActivationWindow = Z mp**


Itt látható egy példa arra, hogy a Signal Gate processzor csomópontjának szakasza hogyan szeretne egy Media Graph-topológiában a következő paraméterek értékeinél:
* **activationEvaluationWindow = 1 másodperc**
* **activationSignalOffset =-5 másodperc**
* **minimumActivationTime = 20 másodperc**
* **maximumActivationTime = 40 másodperc**

> [!IMPORTANT]
> Az egyes paraméterek értékének az [ISO 8601 időtartam formátumát](https://en.wikipedia.org/wiki/ISO_8601#Durations
) kell megvárnia. 
**Pl.: PT1S = 1 másodperc**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Tekintsük át, hogy a Signal Gate-processzor konfigurációja milyen módon fog működni a különböző rögzítési helyzetekben.


**1 esemény 1 forrásból ( *normál aktiválás* )**

Az egy eseményt fogadó Signal Gate-feldolgozó egy olyan rögzítést eredményez, amely "aktiválási jel eltolása" (5) másodpercet indít el, mielőtt az esemény megérkezett a kapun. A rögzítés fennmaradó része a "minimális aktiválási idő" (20) másodperc, mivel nem érkezik más esemény a minimális aktiválási idő befejeződése előtt a kapu újraindításához.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Normál aktiválás":::

* Rögzítés időtartama =-eltolás + minimumActivationTime = [E1 + eltolás, E1 + minimumActivationTime]


**2 esemény 1 forrásból ( *újraindított aktiválás* )**

A két eseményt fogadó Signal Gate-feldolgozó egy olyan rögzítést eredményez, amely "aktiválási jel eltolása" (öt) másodpercet indít el, mielőtt az első esemény megérkezett a kapun. A második esemény az első esemény után öt másodperccel érkezik, amely az első eseménytől számított "minimális aktiválási idő" (20) másodperc, így a kapu újraindul, hogy nyitva maradjon. A rögzítés fennmaradó része a "minimális aktiválási idő" (20) másodperc, mivel nem érkezik más esemény a 2. esemény minimális aktiválási ideje után, hogy újra lehessen indítani a kaput.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Aktiválás újraaktiválva":::

* Rögzítés időtartama =-eltolás + (a 2. esemény érkezése – 1. esemény érkezése) + minimumActivationTime


**N esemény 1 forrásból ( *maximális aktiválás* )**

Az N eseményt fogadó Signal Gate-processzorok egy olyan rögzítést eredményeznek, amely "aktiválási jel eltolása" (5) másodpercet indít el, mielőtt az első esemény megérkezett a kapun. Mivel minden esemény az előző esemény "minimális aktiválási idő" (20) másodpercének befejezése előtt érkezik, a kaput folyamatosan újra kell indítani, és az első esemény után a "maximális aktiválási idő" (40) másodpercig nyitva maradnak, amikor a kapu lezárul, és már nem fogad el új eseményeket.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Maximális aktiválás":::
 
* Rögzítés időtartama =-eltolás + maximumActivationTime

> [!IMPORTANT]
> A diagramok azt feltételezik, hogy minden esemény ugyanazon a példányon érkezik fizikai és adathordozói időben. (Késői érkezés nélkül)

## <a name="next-steps"></a>Következő lépések

### <a name="try-it-out"></a>Kipróbálás

[Eseményvezérelt videók rögzítése – oktatóanyag](event-based-video-recording-tutorial.md)

Az Event-based video Recording oktatóanyag használatával szerkessze a [topology.jst](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), módosítsa a signalgateProcessor csomópont paramétereit, majd kövesse az oktatóanyag hátralévő részét. Tekintse át a videofelvételeket a paraméterek hatásának elemzéséhez.



