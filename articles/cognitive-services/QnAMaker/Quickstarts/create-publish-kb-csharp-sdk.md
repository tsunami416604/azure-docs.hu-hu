---
title: 'Gyors útmutató: A .NET-hez készült ügyféloldali kódtár QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a .NET-hez készült QnA Maker ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 31bd85ca9b106758dbb7bfd399b7a493ea7fea9f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803092"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Gyors útmutató: A .NET-hez készült ügyféloldali kódtár QnA Maker

Ismerkedjen meg a .NET-hez készült QnA Maker ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók. 

A .NET-hez készült QnA Maker ügyféloldali kódtára a következőre használható:

* Tudásbázis létrehozása 
* Tudásbázis kezelése
* Tudásbázis közzététele
* Válasz létrehozása a Tudásbázisból

[A dokumentációs](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [-csomagjához (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# tartozó minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást QnA Maker a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. 

Az erőforrás kulcsának és végpontjának lekérése után [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) `QNAMAKER_SUBSCRIPTION_KEY` nevű kulcshoz. Az erőforrás nevét a rendszer a végpont URL-címének részeként használja.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) a DotNet `new` paranccsal hozzon létre egy új, a nevű `qna-maker-quickstart`Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazás könyvtárában telepítse az QnA Maker .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.


## <a name="object-model"></a>Objektummodell

A QnA Maker ügyfél egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum, amely az Azure-ban a Microsoft. Rest. ServiceClientCredentials használatával hitelesíti a kulcsot.

Az ügyfél létrehozása után használja a tudásbázist [a Tudásbázis](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) létrehozásához, kezeléséhez és közzétételéhez. 

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. Hívja meg az [ügyfelet. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metódus a műveleti azonosítóval a [kérelem állapotának](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)meghatározásához. 

 
## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket az QnA Maker .NET-hez készült ügyféloldali kódtár használatával:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)
* [Válasz létrehozása a Tudásbázisból](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a **program.cs** fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Cserélje le a `using` meglévő kódot a következő `using` irányelvekre:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

A **Main** metódusban hozzon létre egy változót az erőforrás Azure-kulcsához egy nevű `QNAMAKER_SUBSCRIPTION_KEY`környezeti változóból. Ha az alkalmazás elindítása után hozta létre a környezeti változót, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusok később lesznek létrehozva.

Ezután hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum létrehozásához.

Módosítsa az `<your-custom-domain>` **végpont** változót az egyéni tartomány nevére. Ez a hely a Azure Portal QnA Maker erőforrásának **Áttekintés** lapján található.

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

A **Main** metódusban hozzon létre egy változót az erőforrás-hitelesítéshez egy `QNAMAKER_ENDPOINT_HOSTNAME` és `QNAMAKER_ENDPOINT_KEY` nevű környezeti változó alapján. A Tudásbázis közzétételekor a rendszer ezeket az értékeket adja vissza. A közzététel után ezeket a beállításokat a QnA Maker portál **Beállítások** lapján tekintheti meg. 

Hozzon létre egy [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) , amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz**használja a [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objektumot.
* **Fájlok**esetében használja a [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objektumot. 
* **URL-címek**esetén használja a karakterláncok listáját.

Hívja meg a [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metódust, majd adja át a visszaadott művelet azonosítóját a [MonitorOperation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez. 

A következő kód utolsó sora visszaadja a Tudásbázis AZONOSÍTÓját a MonitorOoperation válaszból. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

A Tudásbázis sikeres létrehozásához [`MonitorOperation`](#get-status-of-an-operation) győződjön meg arról, hogy a fenti kódban hivatkozott függvény belefoglalása. 

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) használatával frissítheti, amely a DTO objektumok [hozzáadását](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [frissítését](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)és [törlését](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) tartalmazza a [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metódushoz. A [MonitorOperation](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

Győződjön meg arról, hogy [`MonitorOperation`](#get-status-of-an-operation) a fenti kódban hivatkozott függvény belefoglalása a Tudásbázis sikeres frissítéséhez. 

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metódussal töltheti le az adatbázist a [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Egy közzétett Tudásbázis válaszának létrehozása a [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)használatával. [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) metódus. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Hozzáférés a QueryDTO további tulajdonságaihoz, például a [legfelső](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) és [kontextushoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) a csevegési robotban való használatra. 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metódussal a TUDÁSBÁZIS-azonosító paraméterének használatával. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) a lekérdezésben (az újrapróbálkozási logikával). 

A _hurok_ és a feladat. a következő kódrészlet _késleltetése_ az újrapróbálkozási logika szimulálására szolgál. Ezeket a saját újrapróbálkozási logikával kell helyettesíteni. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a DotNet `run` paranccsal az alkalmazás könyvtárából.

A cikkben szereplő kódrészletek mindegyike [elérhető](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) , és egyetlen fájlként is futtatható.

```console
dotnet run
```

Ennek a rövid útmutatónak a [forráskódja](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs) elérhető a QnA Maker C# Samples GitHub-tárházban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Oktatóanyag: KB létrehozása és válasza](../tutorials/create-publish-query-in-portal.md)

* [Mi a QnA Maker API?](../Overview/overview.md)
* [Tudásbázis szerkesztése](../how-to/edit-knowledge-base.md)
* [Használati elemzések beolvasása](../how-to/get-analytics-knowledge-base.md)