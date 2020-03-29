---
title: Az Anomaly Detector API használatával kapcsolatos ajánlott eljárások
titleSuffix: Azure Cognitive Services
description: Ismerje meg az anomáliák észlelése az anomáliák észlelése az anomáliadetektor API-val kapcsolatos gyakorlati tanácsok.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721621"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Az Anomáliadetektor API használatának gyakorlati tanácsok

Az anomáliadetektor API egy állapotnélküli anomáliadetektálási szolgáltatás. Eredményeinek pontosságára és teljesítményére a következők befolyásolhatják:

* Az idősorozat-adatok előkészítése.
* A használt anomáliadetektor API-paraméterei.
* Az API-kérelemben lévő adatpontok száma. 

Ebből a cikkből megtudhatja, hogy miként lehet az API használatával kapcsolatban az adatokhoz legjobb eredményeket elérni. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Mikor kell használni a köteget (teljes) vagy a legújabb (utolsó) pontanomália detektáltsa

Az Anomáliadetektor API kötegelt detektálási végpontja lehetővé teszi az anomáliák észlelését a teljes alkalommal adatsoradatokon keresztül. Ebben az észlelési módban egyetlen statisztikai modell jön létre és alkalmaz az adatkészlet minden pontjára. Ha az idősorozat az alábbi jellemzőkkel rendelkezik, javasoljuk, hogy használja a kötegelt észlelést az adatok egyetlen API-hívásban való előnézetének megtekintéséhez.

* Szezonális idősorok, alkalmi anomáliákkal.
* Egy lapos trend idősorozat, alkalmi tüskékkel/dipekkel. 

Nem javasoljuk a kötegelt anomáliadetektálás valós idejű adatfigyeléshez, vagy olyan idősorozat-adatokon való használatot, amelyek nem rendelkeznek a fenti jellemzőkkel. 

* A kötegelt észlelés csak egy modellt hoz létre és alkalmaz, az egyes pontok észlelése a teljes sorozat kontextusában történik. Ha az idősorozat-adatok szezonalitás nélkül fel-le trendeket mutatnak, a modell kihagyhat bizonyos változási pontokat (az adatok beugrása és kiugrása). Hasonlóképpen előfordulhat, hogy az adatkészlet későbbi részeinél kevésbé jelentős módosítási pontok nem számítanak elég jelentősnek ahhoz, hogy beépüljenek a modellbe.

* A kötegek észlelése lassabb, mint a legutóbbi pont anomáliaállapotának észlelése a valós idejű adatfigyelés során, az elemzett pontok száma miatt.

A valós idejű adatfigyeléshez azt javasoljuk, hogy csak a legújabb adatpont anomáliaállapotának észlelése. A legújabb pontészlelés folyamatos alkalmazásával a streamelési adatok figyelése hatékonyabban és pontosabban elvégezhető.

Az alábbi példa bemutatja, hogy ezek az észlelési módok milyen hatással lehetnek a teljesítményre. Az első képen az anomália állapotának folyamatos észlelése a 28 korábban látott adatpont mentén. A piros pontok anomáliák.

![Anomáliaészlelést megjelenítő kép a legújabb ponttal](../media/last.png)

Az alábbiakban ugyanazt az adatkészletet használja kötegelt anomáliadetektálás. A művelethez épített modell figyelmen kívül hagyott számos anomáliát, téglalapokkal jelölve.

![Atétel-módszerrel anomáliadetektálást megjelenítő kép](../media/entire.png)

## <a name="data-preparation"></a>Adatok előkészítése

Az Anomáliadetektor API elfogadja a JSON-kérelemobjektumba formázott idősorozat-adatokat. Az idősorok tetszőleges számszerű adatok lehetnek, amelyeket az idő múlásával szekvenciális sorrendben rögzítenek. Az idősorozat-adatok ablakait elküldheti az Anomália-detektor API-végpontra az API teljesítményének javítása érdekében. A küldhető adatpontok minimális száma 12, a maximális érték pedig 8640 pont. [A részletesség](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) az a sebesség, amelyen az adatok mintavételezése történik. 

Az Anomáliadetektor API-ba küldött adatpontoknak érvényes egyezményes világidő (UTC) időbélyegzővel és numerikus értékkel kell rendelkezniük. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Ha az adatok mintavételezése nem szabványos időintervallumban történik, `customInterval` megadhatja az attribútum hozzáadásával a kérelemben. Ha például a sorozatból 5 percenként mintavételkészül, a következőt veheti fel a JSON-kérelemhez:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Hiányzó adatpontok

A hiányzó adatpontok gyakoriak az egyenletesen elosztott idősorozat-adatkészletekben, különösen a finom részletességűadatkészletekben (Egy kis mintavételi időköz). Például néhány percenként mintavételezett adatok). Ha az adatokban a várt számú pont kevesebb, mint 10%-át hiányzik, az nem gyakorolhat negatív hatást az észlelési eredményekre. Fontolja meg az adatok hiányosságainak kitöltését a jellemzői alapján, például egy korábbi időszak adatpontjainak helyettesítése, lineáris interpoláció vagy mozgóátlag.

### <a name="aggregate-distributed-data"></a>Elosztott adatok összesítése

Az Anomáliadetektor API egy egyenletesen elosztott idősorozaton működik a legjobban. Ha az adatok véletlenszerűen vannak elosztva, akkor egy időegységgel kell összesíteni, például percenkénti, óránkénti vagy napi, például.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Anomáliakimutatás szezonális mintákat tartalmazó adatokon

Ha tudja, hogy az idősorozat-adatok szezonális mintával rendelkeznek (amely rendszeres időközönként fordul elő), javíthatja a pontosságot és az API válaszidejét. 

`period` Ha megadja a JSON-kérelem összeállításakor, az anomáliadetektálási késés akár 50%-kal is csökkenhet. Az `period` egy egész szám, amely nagyjából megadja, hogy az idősorozat hány adatpontot vesz igénybe a minta megismétléséhez. Például egy naponta egy adatponttal rendelkező `period` idősorozatnak `7`minta , és egy óránként egy ponttal rendelkező idősorozat (azonos heti mintával) a . `7*24` `period` Ha nem biztos az adatok mintáiban, nem kell megadnia ezt a paramétert.

A legjobb eredmény `period`érdekében adjon meg 4 adatpontot, plusz egy továbbiet. Például a fent leírt heti mintával rendelkező óránkénti adatoknak 673`7 * 24 * 4 + 1`adatpontot kell megadniuk a kérelemtörzsben ( ).

### <a name="sampling-data-for-real-time-monitoring"></a>Mintavételi adatok a valós idejű monitorozáshoz

Ha a streamelési adatok mintavételezése rövid időközönként (például másodperc vagy perc), az ajánlott adatpontok küldése meghaladhatja az Anomáliadetektor API maximális anama(8640 adatpontok). Ha az adatok stabil szezonális mintát mutatnak, fontolja meg az idősorozat-adatok mintaelküldését nagyobb időintervallumban, például órákban. Az adatok ily módon való mintavételezése is észrevehetően javíthatja az API válaszidejét. 

## <a name="next-steps"></a>További lépések

* [Mi az az Anomaly Detector API?](../overview.md)
* [Rövid útmutató: Az anomáliadetektor REST API-jával észleli az idősorozat-adatok anomáliáinak anomáliáit](../quickstarts/detect-data-anomalies-csharp.md)
