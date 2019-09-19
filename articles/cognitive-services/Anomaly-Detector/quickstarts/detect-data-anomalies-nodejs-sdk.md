---
title: 'Gyors útmutató: Adatanomáliák észlelése a következőhöz: az anomália-detektor ügyféloldali kódtára a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességeket kötegként vagy adatfolyamként lehet érzékelni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 320c690eb873f760af89b7514893f14ecc209323
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106959"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Gyors útmutató: A Node. js-hez készült anomália-Kiderítő ügyféloldali kódtára

Ismerkedjen meg a Node. js-hez készült anomália-Kiderítő ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A Node. js-hez készült rendellenesség-Kiderítő ügyféloldali kódtárat a következőre használhatja:

* Az idősorozat-adatkészlet összes rendellenességének észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése

[A dokumentációs](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [-csomagjához (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | tartozó[minták](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org/) jelenlegi verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-an-anomaly-detector-azure-resource"></a>Anomália-detektor Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást az anomália-detektorhoz a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával a helyi gépen. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com/).

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű `ANOMALY_DETECTOR_KEY`kulcshoz. Ezután hozzon létre egyet az Azure- `ANOMALY_DETECTOR_ENDPOINT`végponthoz.
 
### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy `package.json` Node-alkalmazás fájlhoz való létrehozásához. 

```console
npm init
```

Hozzon létre egy `index.js` nevű fájlt, és importálja a következő könyvtárakat:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásakor. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. Hozzon létre egy másik változót a példaként letölteni kívánt adatfájlhoz egy későbbi lépésben. Ezután hozzon létre egy ApiKeyCredentials objektumot, amely tartalmazza a kulcsot.

[!code-javascript[Initial variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és `azure-cognitiveservices-anomalydetector` a NPM csomagokat. Ebben a rövid útmutatóban a CSV-elemzési függvénytárat is használják:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél két módszert biztosít a anomáliák észlelésére: Egy teljes adatkészlet [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)és a legújabb adatpontok használatával a [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)segítségével. 

Az idősorozat-információk küldése a [kérelem](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) objektumában lévő [pontok](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) sorozata. Az `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatok (például a[részletesség](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) ) és az anomáliák észlelésének paramétereit. 

Az anomália-detektor válasza egy [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) vagy [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objektum a használt módszertől függően. 

## <a name="code-examples"></a>Példák a kódokra 

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node. js-hez készült rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `ANOMALY_DETECTOR_KEY`nevű anomália detektor kulcsához.

Hozza létre a [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) objektumot a végponttal és a hitelesítő adatokkal.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése [a githubról](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW**elemre.
2. Kattintson **a hivatkozás mentése másként**elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Olvassa el az adatfájlt a CSV-parse `readFileSync()` Library metódusával, és elemezze `parse()`a fájlt a következővel:. Az egyes sorok esetében leküldheti az időbélyeget tartalmazó [pont](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) objektumot és a numerikus értéket.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése 

Hívja meg az API-t, hogy a teljes idősorozaton keresztül észlelje a rendellenességeket az ügyfél [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) metódusával. Tárolja a visszaadott [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objektumot. Ismételje meg `isAnomaly` a válasz listáját, és nyomtassa ki bármelyik `true` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) objektumot. A válasz `isAnomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `node` a gyors üzembe helyezési fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Adatfolyam-rendellenességek észlelése Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Mi a [rendellenesség-Kiderítő API?](../overview.md)
* [Ajánlott eljárások](../concepts/anomaly-detection-best-practices.md) az anomália-detektor API használatakor.
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)található.
