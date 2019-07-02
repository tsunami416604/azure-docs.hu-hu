---
title: 'Gyors útmutató: Rendellenességek észlelése az Anomáliadetektálási detector használatával SDK-val .NET-keretrendszerhez készült idősorozat-adatok'
titleSuffix: Azure Cognitive Services
description: Indítsa el az idősoros adatokat az Anomáliadetektálási detector használatával szolgáltatással a rendellenességek észlelése.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: c2a8bf10d7c279243c9f53801264ebbe9bfe72c9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503475"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Gyors útmutató: Anomáliadetektálási detector használatával .NET-klienskódtár

Ismerkedés az Anomáliadetektálási detector használatával ügyféloldali kódtára a .NET-hez. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapvető feladatok példakód. Az Anomáliadetektálási detector használatával szolgáltatás lehetővé teszi, hogy az idősorozat-adatok használatával automatikusan a legjobban záró modellek, iparági, a forgatókönyv és adatok mennyiségétől függetlenül rendellenességeket található.

Az Anomáliadetektálási detector használatával klienskódtárának használata a .NET használatával:

* Kötegelt feladatként rendellenességek észlelése
* A legújabb adatpont anomáliadetektálási állapotának észlelése

[API-referenciadokumentáció](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [kódtár forráskódját](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [minták](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Jelenlegi verziója [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Beállítása

### <a name="create-an-anomaly-detector-resource"></a>Hozzon létre egy Anomáliadetektálási detector használatával erőforrást

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-c-app"></a>Hozzon létre egy új C# alkalmazás

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztővel, vagy IDE. 

A konzolablakban (például a cmd parancsot, a PowerShell vagy a Bash), használja a dotnet `new` paranccsal hozzon létre egy új konzolalkalmazást nevű `anomaly-detector-quickstart`. Ez a parancs létrehoz egy egyszerű "Hello World" C# a projekt egy forrásfájl: **Program.cs**. 

```console
dotnet new console -n anomaly-detector-quickstart
```

Lépjen abba a címtár újonnan létrehozott alkalmazást. Az alkalmazást hozhat létre:

```console
dotnet build
```

A felépítési művelet kimenetében tartalmazhat semmilyen hiba vagy figyelmeztetés. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában található telepítése az Anomáliadetektálási detector használatával ügyféloldali kódtára a .NET-hez a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

Használja a Visual Studio IDE, ha a az ügyféloldali kódtár NuGet-csomagként érhető el. 

## <a name="object-model"></a>Hálózatiobjektum-modellje

Az Anomáliadetektálási detector használatával ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objektum, amely hitelesíti az Azure-bA [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), amely tartalmazza a kulcsot. Az ügyfél az anomáliadetektálás kétféle módszert biztosít: Egy teljes adatkészleten használatával [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync), és a legfrissebb adatok használatával [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). 

Idősorozat-adatok küldött sorozataként [pontok](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) a egy [kérelem](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) objektum. A `Request` objektum tulajdonságait írja le az adatokat tartalmazza ([Granularitási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) például), és a rendellenességek észlelése paramétereit. 

Az Anomáliadetektálási detector használatával választ, mint egy [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) vagy [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) a használt módszertől függően. 

## <a name="code-examples"></a>Hitelesítésikód-példák

Ezek a kódrészletek bemutatják, hogyan tegye a következőket az Anomáliadetektálási detector használatával ügyféloldali kódtára a .NET-keretrendszerhez készült:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Egy time series adatkészlet betöltése egy fájlból](#load-time-series-data-from-a-file)
* [Észlelheti a rendellenességeket a teljes adatkészlet](#detect-anomalies-in-the-entire-data-set) 
* [A legújabb adatpont anomáliadetektálási állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="add-the-main-method"></a>A fő metódus hozzáadása

A projekt könyvtárból:

1. Nyissa meg a Program.cs fájlt az előnyben részesített szerkesztővel, vagy IDE
2. Adja hozzá a következő `using` irányelvek

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

> [!NOTE]
> Ez a rövid útmutató feltételezi, hogy [létrehozott egy környezeti változó](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) az Anomáliadetektálási detector használatával kulcs nevű `ANOMALY_DETECTOR_KEY`.

Az alkalmazás `main()` metódus, létrehozhat változókat az erőforrás Azure-beli hely, és a kulcs környezeti változóban. Alkalmazás elindítása után a környezeti változó hozta létre, ha az szerkesztő, az IDE vagy az azt futtató rendszerhéj kell kell zárni, majd újbóli eléréséhez a változót.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új módszer esetében hozza létre a végpont és a kulcs az ügyfél. Hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) a kulccsal rendelkező objektumot, és ezzel a végponttal hozzon létre egy [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) objektum. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
### <a name="load-time-series-data-from-a-file"></a>Idősorozat-adatok betöltése egy fájlból

Töltse le a példaadatokat az ebben a rövid [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. A böngészőben, kattintson a jobb gombbal **Raw**
2. Kattintson a **mentés hivatkozás**
3. Mentse a fájlt az alkalmazás könyvtárába, egy .csv-fájlba.

Az idősorozat-adatok egy .csv-fájlba van formázva, és az Anomáliadetektálási detector használatával API-t küld.

Hozzon létre egy új módszer, olvassa el az idősoros adatokat, és adja hozzá a egy [kérelem](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) objektum. Hívás `File.ReadAllLines()` az fájl elérési útját, és hozzon létre egy [pont](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) objektumok és a szalag minden új sor karakter. Bontsa ki az értékeket, és a Dátumbélyegző elkülönítése a numerikus érték, és adja hozzá őket egy új `Point` objektum. 

Győződjön meg arról, egy `Request` sorozata, pontokat, az objektum és `Granularity.Daily` számára a [Granularitási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (vagy a periodicitás) az adatpont.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Észlelheti a rendellenességeket a teljes adatkészlet 

Hozzon létre egy metódust, hívja az ügyfél [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódust a `Request` objektumra, és a válasz való await egy [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) objektum. Ha az idősor felismerheti a rendellenességeket, iterálódnak a válasz [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) értékeket és a nyomtatási bármely, amelyek `true`. Ha találhatók esetleges rendellenes adatpontok index ezeket az értékeket felelnek meg.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliadetektálási állapotának észlelése

Hozzon létre egy metódust, hívja az ügyfél [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódust a `Request` objektumra, és a válasz való await egy [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) objektum. A válasz ellenőrzése [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) meghatározni, ha a legutóbbi adatok pontnak elküldött vagy nem volt egy anomáliadetektálási attribútum. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtathatja az alkalmazást a dotnet `run` parancsot az alkalmazás könyvtárába.

```dotnet
dotnet run
```

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
