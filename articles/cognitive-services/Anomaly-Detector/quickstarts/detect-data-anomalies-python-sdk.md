---
title: 'Gyors útmutató: Az Anomáliadetektálási detector használatával erőforrástár és a Python használatával adatok rendellenességek észlelése'
titleSuffix: Azure Cognitive Services
description: Az Anomáliadetektálási detector használatával API segítségével észlelheti a rendellenességeket az adatsorozat egy kötegelt vagy a streamelt adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 9176ab84dd3f493604bd655e0498f5ad476776d0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721524"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Gyors útmutató: Anomáliadetektálási detector használatával ügyféloldali kódtára a Pythonhoz

Ismerkedés az Anomáliadetektálási detector használatával ügyféloldali kódtára a .NET-hez. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapvető feladatok példakód. Az Anomáliadetektálási detector használatával szolgáltatás lehetővé teszi, hogy az idősorozat-adatok használatával automatikusan a legjobban záró modellek, iparági, a forgatókönyv és adatok mennyiségétől függetlenül rendellenességeket található.

## <a name="key-concepts"></a>Fő fogalmak

Az Anomáliadetektálási detector használatával ügyféloldali kódtár használhatja a Python használatával:

* A time series adatkészlet egész rendellenességek észlelése, a kötegelt kérelem
* A legújabb adatpont anomáliadetektálási állapotának észleléséhez az idősor

[Kódtár dokumentációja](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [kódtár forráskódját](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [minták](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* A [Pandas adatok elemzőkönyvtár](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Beállítása

### <a name="create-an-anomaly-detector-resource"></a>Hozzon létre egy Anomáliadetektálási detector használatával erőforrást

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Miután telepítette a Python, az ügyféloldali kódtár a telepíthető:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Hálózatiobjektum-modellje

Az Anomáliadetektálási detector használatával ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objektum, amely az Azure-kulcs használatával hitelesít. Az ügyfél az anomáliadetektálás kétféle módszert biztosít: Egy teljes adatkészleten használatával [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-), és a legfrissebb adatok használatával [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Idősorozat-adatok küldött sorozataként [pontok](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) a egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) objektum. A `Request` objektum tulajdonságait írja le az adatokat tartalmazza ([Granularitási](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) például), és a rendellenességek észlelése paramétereit. 

Az Anomáliadetektálási detector használatával választ egy [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) vagy [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektumok a használt módszertől függően. 

## <a name="getting-started"></a>Első lépések

Hozzon létre egy új Python-alkalmazás az előnyben részesített szerkesztővel, vagy IDE. Ezután adja hozzá a következő importálási nyilatkozatokat a fájlhoz. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változó](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) az Anomáliadetektálási detector használatával kulcs nevű `ANOMALY_DETECTOR_KEY`.

Hozzon létre változókat a kulcsot egy környezeti változót, egy time series adatfájlt, és az azure-előfizetését, hely elérési útja. Például: `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Hitelesítésikód-példák 

Ezek a kódrészletek bemutatják, hogyan tegye a következőket az Anomáliadetektálási detector használatával ügyféloldali kódtára a .NET-keretrendszerhez készült:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Egy time series adatkészlet betöltése egy fájlból](#load-time-series-data-from-a-file)
* [Észlelheti a rendellenességeket a teljes adatkészlet](#detect-anomalies-in-the-entire-data-set) 
* [A legújabb adatpont anomáliadetektálási állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az Azure-beli hely változó hozzáadása a végponthoz, és az ügyfél hitelesítésére a kulccsal.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Idősorozat-adatok betöltése egy fájlból

Töltse le a példaadatokat az ebben a rövid [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. A böngészőben, kattintson a jobb gombbal **Raw**
2. Kattintson a **mentés hivatkozás**
3. Mentse a fájlt az alkalmazás könyvtárába, egy .csv-fájlba.

Az idősorozat-adatok egy .csv-fájlba van formázva, és az Anomáliadetektálási detector használatával API-t küld.

Az adatfájlban a Pandas kódtár betöltése `read_csv()` metódust, és módosítsa az adatsorozat tárolásához egy üres lista változó. Iterálódnak a fájlt, és az adatok hozzáfűzése egy [pont](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objektum. Ez az objektum tartalmazza a timestamp és az adatok a .csv-fájl sorainak numerikus érték. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Hozzon létre egy [kérelem](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) az idősorozat-objektum és a [granularitási](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (vagy a periodicitás) az adatpont. Például: `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Észlelheti a rendellenességeket a teljes adatkészlet 

Az API-t a teljes idősorozat-adatok használatával az ügyfél keresztül rendellenességek észlelése [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metódust. A visszaadott Store [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektum. A válasz iterálódnak `is_anomaly` listában, és nyomtassa ki az index minden `true` értékeket. Ha találhatók esetleges rendellenes adatpontok index ezeket az értékeket felelnek meg.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliadetektálási állapotának észlelése

Az Anomáliadetektálási detector használatával API-t, hogy a legújabb adatok pont az ügyfél anomáliát [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) módját, valamint a tároló által visszaadott [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektum. A válasz `is_anomaly` érték logikai érték beolvasása, amely meghatározza, hogy pont anomáliadetektálási állapotát.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az IDE-ben, vagy a parancssorban a `python` parancs, és a fájl nevét.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni, és távolítsa el a Cognitive Services-előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésekor a az erőforráscsoporthoz társított erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

A következő cloud shell parancs eltávolítható az erőforráscsoport és az összes kapcsolódó erőforrás is futtathatja. Ez eltarthat néhány percig. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Az Azure Databricks streamelési anomáliadetektálás](../tutorials/anomaly-detection-streaming-databricks.md)

* Mi az a [Anomáliadetektálási detector használatával API-t?](../overview.md)
* [Ajánlott eljárások](../concepts/anomaly-detection-best-practices.md) az Anomáliadetektálási detector használatával API használatakor.
* Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
