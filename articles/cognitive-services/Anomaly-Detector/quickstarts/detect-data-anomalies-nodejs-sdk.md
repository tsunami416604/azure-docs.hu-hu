---
title: 'Rövid útmutató: Adatanomáliák észlelése az Anomália-detektor ügyfélkódtár használatával pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az Anomália-detektor API-t az adatsorok rendellenességei észlelésére kötegként vagy streamelési adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: c13657b8f2dae3868c1b9820236585aa930c6be4
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239086"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Rövid útmutató: Anomáliadetektor ügyfélkönyvtára a Node.js fájlhoz

Első lépések a Node.js anomáliadetektor ügyfélkönyvtárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. Az Anomáliadetektor szolgáltatás lehetővé teszi, hogy az idősorozat-adatok rendellenességeit automatikusan megtalálja a legjobban illeszkedő modellek használatával, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A Node.js anomáliadetektor ügyfélkönyvtárának használatával:

* Anomáliák észlelése az idősorozat-adatkészletben kötegelt kérelemként
* Az idősorozat legújabb adatpontjának anomáliaállapotának észlelése

[Referenciadokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [A kód megkeresése a GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org/) jelenlegi verziója
* Anomáliadetektor kulcsa és végpontja

## <a name="setting-up"></a>Beállítása

### <a name="create-an-anomaly-detector-azure-resource"></a>Anomáliadetektor Azure-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy `package.json` csomópontalkalmazás fájllal való létrehozásához. 

```console
npm init
```

Hozzon létre `index.js` egy elnevezett fájlt, és importálja a következő tárakat:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Hozzon létre változókat az erőforrás Azure-végpontés kulcs. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat. Hozzon létre egy másik változót a példa adatfájlhoz, amelyet egy későbbi lépésben tölt le, és egy üres listát az adatpontokhoz. Ezután `ApiKeyCredentials` hozzon létre egy objektumot, amely tartalmazza a kulcsot.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Telepítse `ms-rest-azure` a `azure-cognitiveservices-anomalydetector` és az NPM csomagokat. A csv-parse könyvtár is használható ebben a rövid útmutatóban:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

## <a name="object-model"></a>Objektummodell

Az Anomáliadetektor-ügyfél egy [anomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél két anomáliadetektálási módszert biztosít: a [teljesDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)használatával, a [lastdetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)használatával pedig a legújabb adatponton. 

Az idősorozat-adatok küldése egy [kérelemobjektum](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) [ban lévő pontok](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) sorozataként történik. Az `Request` objektum az adatok ( például[granularitás)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) leírására szolgáló tulajdonságokat és az anomáliadetektálás paramétereit tartalmazza. 

Az Anomália-érzékelő válasza egy [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) vagy [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Kódpéldák 

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Node.js anomáliadetektor ügyfélkönyvtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adatkészlet anomáliáinak észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legújabb adatpont anomáliaállapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

[AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektum példányossá a végpontés a hitelesítő adatok.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Idősorozat-adatok betöltése fájlból

Töltse le a rövid útmutató példaadatait a [GitHubról:](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)
1. A böngészőben kattintson a jobb gombbal **a Raw**elemre.
2. Kattintson **a Hivatkozás mentése másként gombra.**
3. Mentse a fájlt az alkalmazáskönyvtárba .csv fájlként.

Ez az idősorozat-adatok .csv fájlként vannak formázva, és az Anomáliadetektor API-ba kerülnek.

Olvassa el az adatfájlt a csv-elemzési könyvtár módszerével, `readFileSync()` és `parse()`elemezze a fájlt a segítségével. Minden sorhoz nyomja le az időbélyeget és a numerikus értéket tartalmazó [Pont](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) objektumot.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adatkészlet anomáliáinak észlelése 

Hívja meg az API-t a teljes idősorozaton keresztüli anomáliák észleléséhez az ügyfél [teljesDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) metódusával kötegként. A visszaadott [EntireDetectResponse objektum tárolása.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) Végighaladhat a válasz `isAnomaly` listáján, és nyomtassa ki az `true` értékek indexét. Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

Hívja meg az Anomália-detektor API-t annak megállapításához, hogy a legutóbbi adatpont az ügyfél [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) metódusát használó anomália-e, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) objektumot. A válasz `isAnomaly` értéke egy logikai érték, amely megadja a pont anomália állapotát.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `node` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
