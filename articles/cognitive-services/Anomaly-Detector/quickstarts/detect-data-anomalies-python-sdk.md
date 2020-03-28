---
title: 'Rövid útmutató: Adatanomáliák észlelése az Anomália-detektor ügyfélkódtár használatával pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Anomália-detektor API-t az adatsorok rendellenességei észlelésére kötegként vagy streamelési adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 451d5b0eb4fea8ba9764268d963bb7b021414f4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239075"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Rövid útmutató: Anomáliadetektor ügyfélkódtár a Pythonhoz

Első lépések az Anomáliadetektor python-ügyfélkódtárjával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. Az Anomáliadetektor szolgáltatás lehetővé teszi, hogy az idősorozat-adatok rendellenességeit automatikusan megtalálja a legjobban illeszkedő modellek használatával, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A Python anomáliadetektor ügyfélkönyvtárának használatával:

* Anomáliák észlelése az idősorozat-adatkészletben kötegelt kérelemként
* Az idősorozat legújabb adatpontjának anomáliaállapotának észlelése

[Könyvtár referenciadokumentációja](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [(PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | A[mintakód megkeresése a GitHubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Anomáliadetektor kulcsa és végpontja
* [Python 3.x](https://www.python.org/)
* A [Pandas adatelemző könyvtár](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Beállítása

### <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Új python-alkalmazás létrehozása

 Hozzon létre egy új Python-fájlt, és importálja a következő tárakat.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Hozzon létre változókat a kulcshoz környezeti változóként, az idősorozat-adatfájl elérési útja és az előfizetés azure-helye. Például: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

A Python telepítése után telepítheti az ügyfélkönyvtárat a következőkkel:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektummodell

Az Anomáliadetektor-ügyfél egy [AnomalyDetectorClient objektum,](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél két anomáliadetektálási módszert biztosít: [a entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)használatával létrehozott teljes adatkészleten és a [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)segítségével a legújabb adatponton. 

Az idősorozat-adatok küldése [egy](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) kérelemobjektum ban lévő pontok sorozataként [történik.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) Az `Request` objektum az adatok ( például[granularitás)](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) leírására szolgáló tulajdonságokat és az anomáliadetektálás paramétereit tartalmazza. 

Az Anomália-érzékelő válasza egy [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) vagy [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Kódpéldák 

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Pythonanomaly Detector ügyfélkódtárjával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adatkészlet anomáliáinak észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legújabb adatpont anomáliaállapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá az azure helyváltozót a végponthoz, és hitelesítse az ügyfelet a kulccsal.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Idősorozat-adatok betöltése fájlból

Töltse le a rövid útmutató példaadatait a [GitHubról:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. A böngészőben kattintson a jobb gombbal **a Raw**elemre.
2. Kattintson **a Hivatkozás mentése másként gombra.**
3. Mentse a fájlt az alkalmazáskönyvtárba .csv fájlként.

Ez az idősorozat-adatok .csv fájlként vannak formázva, és az Anomáliadetektor API-ba kerülnek.

Töltse be az adatfájlt a `read_csv()` Pandas könyvtár módszerével, és készítsen egy üres listaváltozót az adatsorok tárolásához. Végighaladanak a fájlon, és [pontobjektumként](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) fűzik hozzá az adatokat. Ez az objektum a .csv adatfájl soraiból származó időbélyeget és numerikus értéket fogja tartalmazni. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Hozzon létre egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objektumot az idősorozatokkal és az adatpontok [részletességével](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (vagy gyakoriságával). Például: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adatkészlet anomáliáinak észlelése 

Hívja meg az API-t az anomáliák észleléséhez az ügyfél [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metódusával a teljes idősorozat-adatokon keresztül. A visszaadott [EntireDetectResponse objektum tárolása.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) Végighaladhat a válasz `is_anomaly` listáján, és nyomtassa ki az `true` értékek indexét. Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

Hívja meg az Anomália-detektor API-t annak megállapításához, hogy a legújabb adatpont az ügyfél [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metódusát használó anomália-e, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektumot. A válasz `is_anomaly` értéke egy logikai érték, amely megadja a pont anomália állapotát.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python` alkalmazást a paranccsal és a fájlnévvel.
 
[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
