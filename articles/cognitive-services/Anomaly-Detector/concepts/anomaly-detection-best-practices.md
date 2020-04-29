---
title: Az Anomaly Detector API használatával kapcsolatos ajánlott eljárások
titleSuffix: Azure Cognitive Services
description: Ismerje meg az ajánlott eljárásokat az anomáliák észlelési API-val kapcsolatos rendellenességek észlelése során.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67721621"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Ajánlott eljárások az anomália-detektor API használatához

Az anomália-detektor API állapot nélküli anomália-észlelési szolgáltatás. Az eredmények pontossága és teljesítménye az alábbiakra is hatással lehet:

* Az idősorozat-adatsorok előkészítése.
* A használatban lévő anomália-detektor API-paraméterei.
* Az API-kérelemben szereplő adatpontok száma. 

Ebből a cikkből megtudhatja, hogyan használhatók az API-k a legjobb eredmények az adatkezeléshez. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Mikor kell használni a Batch (teljes) vagy a legutóbbi (utolsó) pont anomália észlelését

A rendellenesség-érzékelő API batch észlelési végpontja lehetővé teszi a rendellenességek észlelését a teljes idősorozat-adatain keresztül. Ebben az észlelési módban létrejön egy statisztikai modell, amely az adathalmaz minden pontjára vonatkozik. Ha az idősorozat az alábbi jellemzőkkel rendelkezik, javasoljuk, hogy a Batch-észlelés használatával tekintse meg az adatait egy API-hívásban.

* Egy szezonális idősorozat, alkalmanként anomáliák.
* Egy, az alkalmi tüskékkel/dipstal rendelkező, lapos trend idősorozata. 

A rendszer nem javasolja a Batch-anomáliák észlelését a valós idejű adatfigyeléshez, vagy a fenti jellemzőkkel nem rendelkező idősorozat-adatok használatát. 

* A Batch-észlelés csak egyetlen modellt hoz létre és alkalmaz, az egyes pontok észlelése a teljes sorozat kontextusában történik. Ha az idősoros adatsorozat-adatváltozás szezonálisan leáll, előfordulhat, hogy a modell nem hagyhatja ki a változások (dips és tüskék) bizonyos pontjait. Hasonlóképpen, előfordulhat, hogy néhány olyan változási pont, amely kevésbé jelentős, mint az adathalmaz később, nem számít elég jelentősnek a modellbe való beépítés során.

* A kötegelt észlelés lassabb, mint a valós idejű adatfigyelés során a legutóbbi pont anomália állapotának észlelése, az elemzett pontok száma miatt.

A valós idejű adatfigyelés érdekében javasoljuk, hogy csak a legújabb adatpontok rendellenességi állapotát észlelje. A legfrissebb pontok észlelésének folyamatos alkalmazásával a folyamatos átviteli adatfigyelés hatékonyabban és pontosan is elvégezhető.

Az alábbi példa azt mutatja be, hogy az észlelési módok milyen hatással lehetnek a teljesítményre. Az első képen látható, hogy az anomália állapotának folyamatos észlelése milyen eredménnyel jár a 28 korábban látott adatpontok mentén. A vörös pontok rendellenességeket mutatnak.

![A legújabb ponttal a anomália észlelését bemutató kép](../media/last.png)

Az alábbi érték ugyanaz az adathalmaz, amely a Batch anomália észlelését használja. A művelethez készült modell számos rendellenességet figyelmen kívül hagyott, téglalapok által megjelölve.

![A Batch metódust használó anomáliák észlelését bemutató kép](../media/entire.png)

## <a name="data-preparation"></a>Adatok előkészítése

Az anomália detektor API egy JSON-kérelem objektumba formázott idősorozat-adathalmazokat fogad el. Az idősorozatok tetszőleges számú, egymást követő sorrendben rögzített számadatok lehetnek. Az API-k teljesítményének növelése érdekében elküldheti az idősoros adat Windows-adatait a rendellenesség-érzékelő API-végpontjának. Az elküldhető adatpontok minimális száma 12, a maximális érték 8640 pont. A [részletesség](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) határozza meg, hogy az adatai milyen arányban kerülnek mintavételre. 

Az anomália-detektor API-nak elküldett adatpontoknak érvényes, egyezményes világidő (UTC) időbélyegzővel és numerikus értékkel kell rendelkezniük. 

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

Ha az adatait nem szabványos időintervallumban kell mintát venni, megadhatja a `customInterval` kérelemben szereplő attribútum hozzáadásával. Ha például az adatsorozat 5 percenként van mintavétel alatt, a következőt adhatja hozzá a JSON-kérelemhez:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Hiányzó adatpontok

A hiányzó adatpontok közösek a egyenletesen elosztott idősorozat-adatkészletekben, különösen a részletes részletességgel (kis mintavételi intervallummal). Például az adatmintavétel néhány percenként történik.) Az adatpontok várt számának kevesebb, mint 10%-a nem befolyásolhatja negatívan az észlelés eredményét. Vegye figyelembe, hogy a hézagok az adatokon alapulnak, például az adatpontok korábbi időszakból, lineáris interpolációból vagy mozgó átlagból való helyettesítésével.

### <a name="aggregate-distributed-data"></a>Összesített elosztott adatforgalom

Az anomália-detektor API a legjobban egy egyenletesen elosztott idősorozaton működik. Ha az adatokat véletlenszerűen terjesztik, összesíteni kell egy adott időegységgel, például percenként, óránként vagy naponta, például.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Az adatanomáliák észlelése szezonális mintázattal

Ha tudja, hogy az idősorozat-adatok szezonális mintázattal rendelkeznek (amely rendszeres időközönként történik), akkor javíthatja a pontosságot és az API-válaszidőt. 

A JSON- `period` kérés összeállításakor megadható, hogy a rendszer akár 50%-kal is csökkentheti a rendellenességek észlelésének késleltetését. A `period` egy egész szám, amely azt határozza meg, hogy az idősorozat hány adatpontja veszi át a mintát. A napi egy adatponttal rendelkező idősorozatok például `period` a következők `7`lehetnek:, és egy adott időponttal rendelkező idősorozat (ugyanazzal a heti mintával). `period` `7*24` Ha nem biztos benne, hogy az adatai mintáik, nem kell megadnia ezt a paramétert.

A legjobb eredmények érdekében 4 `period`értékű adatpontot adjon meg, és egy másikat. Például a fentiekben leírtak szerint óránkénti adatbevitel esetén a kérelem törzsében (`7 * 24 * 4 + 1`) 673 adatpontot kell megadni.

### <a name="sampling-data-for-real-time-monitoring"></a>Mintavételezési adatgyűjtés a valós idejű figyeléshez

Ha a folyamatos átviteli adatokat rövid időn belül (például másodpercben vagy percben) mintavétel útján adja meg, az ajánlott számú adatpont elküldésével meghaladhatja a rendellenesség-érzékelő API megengedett maximális számát (8640 adatpont). Ha az adatok stabil szezonális mintát mutatnak, érdemes lehet az idősorozat-adatok egy mintáját egy nagyobb időintervallumban, például órákban elküldeni. Az ilyen módon mintavételezéssel az API-válaszidő is javítható. 

## <a name="next-steps"></a>További lépések

* [Mi az az Anomaly Detector API?](../overview.md)
* [Gyors útmutató: az idősoros adataiban észlelt rendellenességek észlelése az anomália-detektor használatával REST API](../quickstarts/detect-data-anomalies-csharp.md)
