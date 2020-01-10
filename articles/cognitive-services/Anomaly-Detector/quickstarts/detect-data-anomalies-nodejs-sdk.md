---
title: 'Gyors útmutató: adatanomáliák észlelése az anomáliák Kiderítő ügyféloldali kódtár használatával a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességek észlelése kötegként vagy adatfolyamként az ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 17ca1a7671718b5b96df0add7775fb82d4d97303
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770441"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Gyors útmutató: a Node. js-hez készült ügyféloldali kódtár

Ismerkedjen meg a Node. js-hez készült anomália-Kiderítő ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A Node. js-hez készült rendellenesség-Kiderítő ügyféloldali kódtárat a következőre használhatja:

* Az idősorozat-adatkészlet összes rendellenességének észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [a githubon található kód keresése](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org/) jelenlegi verziója
* Anomália-detektor kulcsa és végpontja

## <a name="setting-up"></a>Beállítás

### <a name="create-an-anomaly-detector-azure-resource"></a>Anomália-detektor Azure-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

A `npm init` parancs futtatásával hozzon létre egy csomópont-alkalmazást egy `package.json`-fájllal. 

```console
npm init
```

Hozzon létre egy `index.js` nevű fájlt, és importálja a következő könyvtárakat:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásakor. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. Hozzon létre egy másik változót egy későbbi lépésben letölteni kívánt adatfájlhoz, és az adatpontok üres listáját. Ezután hozzon létre egy `ApiKeyCredentials` objektumot, amely tartalmazza a kulcsot.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és `azure-cognitiveservices-anomalydetector` NPM-csomagokat. Ebben a rövid útmutatóban a CSV-elemzési függvénytárat is használják:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél két módszert biztosít a anomáliák észlelésére: egy teljes adatkészleten a [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)és a legújabb adatpontokon a [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)használatával. 

Az idősorozat-információk küldése a [kérelem](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) objektumában lévő [pontok](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) sorozata. A `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatokat (például a[részletességet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ), valamint az anomáliák észlelésének paramétereit. 

Az anomália-detektor válasza egy [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) vagy [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Kódpéldák 

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node. js-hez készült rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozza létre a [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektumot a végponttal és a hitelesítő adatokkal.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése a [githubról](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW**elemre.
2. Kattintson **a hivatkozás mentése másként**elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Olvassa el az adatfájlt a CSV-parse Library `readFileSync()` metódusával, és elemezze a fájlt a `parse()`. Az egyes sorok esetében leküldheti az időbélyeget tartalmazó [pont](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) objektumot és a numerikus értéket.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése 

Hívja meg az API-t, hogy a teljes idősorozaton keresztül észlelje a rendellenességeket az ügyfél [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) metódusával. Tárolja a visszaadott [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objektumot. Ismételje meg a válasz `isAnomaly` listáját, és nyomtassa ki a `true` értékek indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) objektumot. A válasz `isAnomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési fájlban lévő `node` paranccsal.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
