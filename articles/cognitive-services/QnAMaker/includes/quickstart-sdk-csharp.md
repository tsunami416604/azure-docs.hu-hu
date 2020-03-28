---
title: 'Rövid útmutató: QnA Maker ügyféltár a .NET-hez'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a QnA Maker ügyfélkódtár .NET. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946340"
---
A .NET QnA Maker ügyféltár segítségével:

* Tudásbázis létrehozása
* Tudásbázis kezelése
* Tudásbázis közzététele
* Válasz létrehozása a tudásbázisból

[Referenciadokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [Könyvtár forráskódcsomagja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a QnA Maker számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen.

Miután beszerzett egy kulcsot és egy végpontot az erőforráshoz, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcshoz, melynek neve . `QNAMAKER_SUBSCRIPTION_KEY` Az erőforrás neve a végpont URL-címének részeként használatos.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core alkalmazást a kívánt szerkesztőben vagy IDE-ben.

Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal `qna-maker-quickstart`hozhat létre egy új konzolalkalmazást a .. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappára. Az alkalmazást a következőkkel hozhatja létre:

```dotnetcli
dotnet build
```

A build kimenetnem tartalmazhat figyelmeztetéseket vagy hibákat.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazáskönyvtáron belül telepítse a QnA Maker ügyfélkönyvtárát a .NET programhoz a következő paranccsal:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Ha a Visual Studio IDE-t használja, az ügyfélkönyvtár letölthető NuGet csomagként érhető el.


## <a name="object-model"></a>Objektummodell

A QnA Maker-ügyfél egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum, amely hitelesíti magát az Azure-ban a Microsoft.Rest.ServiceClientCredentials használatával, amely tartalmazza a kulcsot.

Az ügyfél létrehozása után a [Tudásbázis](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) tulajdonsággal hozza létre, kezelheti és teheti közzé tudásbázisát.

Kezelje tudásbázisát egy JSON-objektum küldésével. Azonnali műveletek esetén a metódus általában egy JSON-objektumot ad vissza, amely jelzi az állapotot. Hosszú ideig futó műveletek esetén a válasz a művelet azonosítója. Hívja fel az [ügyfelet. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metódust a [műveletazonosítóval a kérelem állapotának](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)meghatározásához.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a .NET QnA Maker ügyféltárával:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Művelet állapotának beszereznie](#get-status-of-an-operation)
* [Válasz létrehozása a tudásbázisból](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából nyissa meg a **Program.cs** fájlt a kívánt szerkesztőben vagy IDE-ben. Cserélje le `using` a meglévő `using` kódot a következő irányelvekre:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a tudásbázis szerkesztéséhez

A **fő** módszerben hozzon létre egy változót az erőforrás Azure-kulcsának egy környezeti változó nevű rendszerből. `QNAMAKER_SUBSCRIPTION_KEY` Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez. A metódusok később jönnek létre.

Ezután hozzon létre egy [ApiKeyServiceClients objektumot](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) a kulccsal, és használja azt a végponttal egy [QnAMakerClient objektum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) létrehozásához.

|Környezeti változó|Változó|Példa|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|A kulcs egy 32 karakterből álló karakterlánc, és elérhető az Azure Portalon, a QnA Maker erőforrás, a gyorsútmutató lapon. Ez nem ugyanaz, mint az előrejelzési végpont kulcs.|
|`QNAMAKER_HOST`|`Endpoint`| A szerzői végpont a formátumában `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`tartalmazza az **erőforrás nevét.** Ez nem ugyanaz az URL-cím, amelyet az előrejelzési végpont lekérdezéséhez használnak.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>A válasz generálásának futási idejének hitelesítése

A **fő** módszerben hozzon létre egy változót az erőforrás hitelesítéséhez, amelyet egy és `QNAMAKER_ENDPOINT_HOSTNAME` `QNAMAKER_ENDPOINT_KEY`a nevű környezeti változókból kérnek le. A tudásbázis közzétételekor ezek az értékek adják vissza. A közzététel után ezeket a beállításokat a QnA Maker portál **Beállítások** lapján találja.

Hozzon létre egy [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) lekérdezésa a tudásbázist, hogy választ hozzon létre, vagy a vonat az aktív tanulás.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A tudásbázis három forrásból tárolja a [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objektum kérdés- és válaszpárjait:

* Szerkesztői **tartalom**esetén használja a [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objektumot.
* **Fájlok**esetén használja a [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objektumot.
* **Url-címekhez**használja a karakterláncok listáját.

Hívja meg a [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metódust, majd adja át a visszaadott műveletazonosítót a [MonitorOperation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora a MonitorOoperation válaszából származó tudásbázis-azonosítót adja vissza.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

Győződjön meg [`MonitorOperation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres létrehozása érdekében.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázis tikóállapot-kezelő jának átadásával frissítheti a tudásbázis azonosítót és egy [UpdatekbOperationDTO-t,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) amely [hozzáadást,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet) [frissítést](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) [és](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO-objektumokat tartalmaz az [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metódusba. A [MonitorOperation](#get-status-of-an-operation) metódus segítségével állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Győződjön meg [`MonitorOperation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres frissítése érdekében.

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

Az adatbázis letöltése a [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metódus segítségével a [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet)listájaként tölthető le. Ez _nem_ egyenértékű a QnA Maker portál **Nak** a Beállítások oldalról történő exportálásával, mert ennek a módszernek az eredménye nem TSV-fájl.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metódussal. Ez veszi az aktuális mentett és betanított modell, a tudásbázis-azonosító által hivatkozott, és közzéteszi, hogy egy végponton.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a tudásbázisból

Válasz létrehozása közzétett tudásbázisból a [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)használatával. [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) metódus. Ez a módszer elfogadja a tudásbázis-azonosítót és a [QueryDTO-t.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet) A QueryDTO további tulajdonságaihoz férhet hozzá, például egy [felső](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) és [környezeti környezethez,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) amelyet a csevegőrobotban használhat.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [Tudásbázis deleteasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metódusával a tudásbázis-azonosító paraméterével.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beszereznie

Egyes módszerek, például a létrehozás és a frissítés, elegendő időt vehet igénybe, hogy ahelyett, hogy megvárnák a folyamat befejezését, egy [műveletet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) adnak vissza. Használja a [művelet azonosítóját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) a művelet lekérdezési (újrapróbálkozási logikával) állapotának meghatározásához az eredeti módszer.

A _hurok_ és a _Task.Delay_ a következő kódblokkban az újrapróbálkozási logika szimulálására szolgál. Ezeket a saját újrapróbálkozási logikával kell helyettesíteni.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `dotnet run` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

A cikkben szereplő összes kódrészlet [elérhető,](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) és egyetlen fájlként futtatható.

```dotnetcli
dotnet run
```

A [rövid útmutató forráskódja](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) a QnA Maker C# minták GitHub-tárházban érhető el.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)