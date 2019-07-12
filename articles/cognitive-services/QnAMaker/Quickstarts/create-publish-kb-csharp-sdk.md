---
title: 'Gyors útmutató: A QnA Maker ügyféloldali kódtára a .NET-hez'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a QnA Maker ügyféloldali kódtára a .NET-hez. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapvető feladatok példakód.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: f38cbfa0efd3b9be9ca091942dca7ffcd91b6019
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828119"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>Gyors útmutató: A QnA Maker ügyféloldali kódtára a .NET-hez

Ismerkedés a QnA Maker ügyféloldali kódtára a .NET-hez. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapvető feladatok példakód.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók. 

A .NET-keretrendszerhez készült a QnA Maker klienskódtárának használata:

* Tudásbázis létrehozása 
* Tudásbázis kezelése
* Tudásbázis közzététele

[Referenciadokumentációt](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [kódtár forráskódját](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [ C# minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Jelenlegi verziója [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Beállítása

### <a name="create-a-qna-maker-azure-resource"></a>A QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Services előfizet az Azure-erőforrások képviseli. A QnA Maker erőforrás létrehozása a [az Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy [Azure CLI-vel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. 

Miután egy kulcs lekérése a resource [egy környezeti változó létrehozásához](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs nevű `QNAMAKER_SUBSCRIPTION_KEY`.

### <a name="create-a-new-c-application"></a>Hozzon létre egy új C# alkalmazás

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztővel, vagy IDE. 

A konzolablakban (például a cmd parancsot, a PowerShell vagy a Bash), használja a dotnet `new` paranccsal hozzon létre egy új konzolalkalmazást nevű `qna-maker-quickstart`. Ez a parancs létrehoz egy egyszerű "Hello World" C# a projekt egy forrásfájl: `Program.cs`. 

```console
dotnet new console -n qna-maker-quickstart
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


### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazás könyvtárában belül telepít a QnA Maker ügyféloldali kódtára a .NET-hez a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Használja a Visual Studio IDE, ha egy letölthető NuGet-csomagként az ügyféloldali kódtár érhető el.


## <a name="object-model"></a>Hálózatiobjektum-modellje

A QnA Maker-ügyfél egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum, amely hitelesíti az Azure-ban Microsoft.Rest.ServiceClientCredentials, amely tartalmazza a kulcsot.

Az ügyfél létrehozása után használja a [Tudásbázis](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) tulajdonság létrehozása, kezelése és közzéteheti a tudásbázist. 

A Tudásbázis kezelése egy JSON-objektum küldésével. Azonnali műveletek esetében egy metódus általában egy JSON-objektum állapotát jelző adja vissza. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. Hívja a [ügyfél. Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metódust a művelet azonosítója meghatározni a [a kérés állapotát](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet). 

 
## <a name="code-examples"></a>Hitelesítésikód-példák

Ezek a kódrészletek bemutatják, hogyan tegye a következőket a QnA Maker ügyféloldali kódtára a .NET-keretrendszerhez készült:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Töltse le a Tudásbázis](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Egy művelet állapotának beolvasása](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Adja a függőségeket

A projekt könyvtárában nyissa meg a **Program.cs** fájlt az előnyben részesített szerkesztővel, vagy IDE. Cserélje le a meglévő `using` kódot a következőre `using` irányelveket:

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az a **fő** metódus, hozzon létre egy változót az erőforrást az Azure key nevű környezeti változó lekért `QNAMAKER_SUBSCRIPTION_KEY`. A környezeti változó az alkalmazás elindítása után hozta létre, ha az szerkesztő, az IDE vagy az azt futtató rendszerhéj kell kell zárni, majd újbóli eléréséhez a változót. A módszerek később jön létre.

Ezután hozzon létre egy [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) a kulccsal rendelkező objektumot, és ezzel a végponttal hozzon létre egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum.

Ha a kulcs nem található a `westus` régióban, mint a mintakód bemutatja, módosítsa a helyet a **végpont** változó. Ezen a helyen található a **áttekintése** oldala az Azure Portalon a QnA Maker erőforrás.

[!code-csharp[Authorization to resource key](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

Tudásbázis tárolja a kérdés és válasz párt a [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objektum három forrásokból:

* A **Szerkesztői tartalom**, használja a [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objektum.
* A **fájlok**, használja a [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objektum. 
* A **URL-címek**, használja a karakterláncok listáját.

Hívja a [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metódust akkor továbbítja a visszaadott Műveletazonosító a [MonitorOperation](#get-status-of-an-operation) metódus a állapotának lekérdezéséhez. 

Az utolsó sorában a következő kódot a Tudásbázis-azonosító MonitorOoperation kapott választ adja vissza. 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

Tudásbázis frissítheti a Tudásbázis-azonosító megadásával és a egy [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) tartalmazó [hozzáadása](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [frissítése](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet), és [törlése](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet)Objektumok dto-t a [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metódust. Használja a [MonitorOperation](#get-status-of-an-operation) módszert meghatározni, ha a frissítés sikeres volt.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

## <a name="download-a-knowledge-base"></a>Töltse le a Tudásbázis

Használja a [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metódus az adatbázis-ként való letöltéséhez listáját [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet). Ez a _nem_ egyenértékű a QnA Maker portal exportálás a **beállítások** lapon, mert ez a metódus eredményét nem egy TSV-fájlt.

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Közzététel a Tudásbázis használatával a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metódust. Az aktuális mentett és a betanított modell, a Tudásbázis-azonosító által hivatkozott vesz igénybe, és a egy végpontot, amely közzéteszi. 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a Tudásbázis használatával a [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metódus egy paraméterrel, a Tudásbázis azonosítóját. 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>Egy művelet állapotának beolvasása

Egyes metódusok például, létrehozhat és frissíthet, időt is igénybe vehet elegendő, amelyek helyett a folyamat befejeződik, Várakozás egy [művelet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) adja vissza. Használja a [Műveletazonosító](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) a műveletből (az újrapróbálkozási logika) az eredeti metódus állapotának lekérdezéséhez. 

A _hurok_ és _Task.Delay_ a következő kódblokk a használt újrapróbálkozási logikát szimulálásához. Ezek le kell cserélni a saját újrapróbálkozási logikát. 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtathatja az alkalmazást a dotnet `run` parancsot az alkalmazás könyvtárába.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni, és távolítsa el a Cognitive Services-előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésekor a hozzá társított összes többi erőforrást is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Oktatóanyag: Hozzon létre, és a egy KB-os választ](../tutorials/create-publish-query-in-portal.md)

* [Mi az a QnA Maker API-t?](../Overview/overview.md)
* [Tudásbázis szerkesztése](../how-to/edit-knowledge-base.md)
* [A használatelemzés beolvasása](../how-to/get-analytics-knowledge-base.md)
* Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs).