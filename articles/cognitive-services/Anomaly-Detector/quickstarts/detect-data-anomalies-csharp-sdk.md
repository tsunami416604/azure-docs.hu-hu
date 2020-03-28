---
title: 'Rövid útmutató: Az idősorozat-adatok anomáliáinak észlelése a .NET anomáliadetektor ügyfélkönyvtárával'
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
ms.openlocfilehash: 268ceee5504e6e48b9eb8fdae18564482480e250
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239122"
---
# <a name="quickstart-anomaly-detector-client-library-for-net"></a>Rövid útmutató: Anomáliadetektor ügyfélkönyvtára .

Ismerkedés a .NET anomáliadetektor ügyfélkönyvtárával. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. Az Anomáliadetektor szolgáltatás lehetővé teszi, hogy az idősorozat-adatok rendellenességeit automatikusan megtalálja a legjobban illeszkedő modellek használatával, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A .NET anomáliadetektor ügyfélkönyvtárának használatával:

* Anomáliák észlelése az idősorozat-adatkészletben kötegelt kérelemként
* Az idősorozat legújabb adatpontjának anomáliaállapotának észlelése

[Könyvtár referenciadokumentációja](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [Könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [Keresse meg a kódot a GitHubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) aktuális verziója
* Anomáliadetektor kulcsa és végpontja

## <a name="setting-up"></a>Beállítása

### <a name="create-an-anomaly-detector-resource"></a>Anomáliadetektor-erőforrás létrehozása

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-net-core-application"></a>Új .NET Core alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `anomaly-detector-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" projektet hoz létre egyetlen C# forrásfájllal: *Program.cs*. 

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```dotnetcli
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Az alkalmazáskönyvtáron belül telepítse a .NET anomáliadetektor ügyfélkönyvtárát a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

A projekt könyvtárából *program.cs* nyissa meg a program.cs `directives`fájlt, és adja hozzá a következőket:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Az alkalmazás metódusában `main()` hozzon létre változókat az erőforrás Azure-helyéhez, és a kulcsot környezeti változóként. Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektummodell

Az Anomaly Detector ügyfél egy [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objektum, amely hitelesíti magát az Azure-ban [apiKeyServiceClients](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)használatával, amely tartalmazza a kulcsot. Az ügyfél két anomáliadetektálási módszert biztosít: A [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)használatával létrehozott teljes adatkészleten és a [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync)használatával a legújabb adatponton. 

Az idősorozat-adatok küldése [egy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) kérelemobjektum ban lévő pontok sorozataként [történik.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) Az `Request` objektum az adatok ( például[granularitás)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) leírására szolgáló tulajdonságokat és az anomáliadetektálás paramétereit tartalmazza. 

Az Anomáliadetektor válasza vagy egy [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) vagy [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objektum, a használt módszertől függően. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a .NET anomáliadetektor ügyfélkönyvtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adatkészlet anomáliáinak észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legújabb adatpont anomáliaállapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új módszer, példányosítja az ügyfél a végpont és a kulcs. Hozzon létre egy [ApiKeyServiceClientS objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) a kulccsal, és használja azt a végpontjával [egy AnomalyDetectorClient object](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) létrehozásához. 

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]
    
## <a name="load-time-series-data-from-a-file"></a>Idősorozat-adatok betöltése fájlból

Töltse le a rövid útmutató példaadatait a [GitHubról:](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)
1. A böngészőben kattintson a jobb gombbal **a Raw**elemre.
2. Kattintson **a Hivatkozás mentése másként gombra.**
3. Mentse a fájlt az alkalmazáskönyvtárba .csv fájlként.

Ez az idősorozat-adatok .csv fájlként vannak formázva, és az Anomáliadetektor API-ba kerülnek.

Hozzon létre egy új módszert az idősorozat-adatok olvasásához, és adja hozzá egy [kérelemobjektumhoz.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) Hívjon `File.ReadAllLines()` a fájl elérési útjával, hozzon létre egy listát a [pontobjektumokról,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) és levetkőztesse az új vonalkaraktereket. Bontsa ki az értékeket, és válassza el a dátumbélyegzőt a numerikus értéktől, és adja hozzá őket egy új `Point` objektumhoz. 

Készíts `Request` egy objektumot a pontok `Granularity.Daily` sorozatával, és az adatpontok [részletességére](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (vagy gyakoriságára).

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adatkészlet anomáliáinak észlelése 

Hozzon létre egy metódust az ügyfél [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódusának hívásához az `Request` objektummal, és várja meg a választ [entireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) objektumként. Ha az idősorozat tartalmaz bármilyen anomáliát, végighalad a válasz [isanomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) értékein, és nyomtassa ki azokat, amelyek . `true` Ezek az értékek megfelelnek a rendellenes adatpontok indexének, ha találhatók ilyenek.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliaállapotának észlelése

Hozzon létre egy metódust az ügyfél [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódusának hívásához az `Request` objektummal, és várja meg a választ [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) objektumként. Ellenőrizze a válasz [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) attribútumát annak megállapításához, hogy a legutóbbi elküldött adatpont anomália volt-e vagy sem. 

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `dotnet run` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
