---
title: 'Rövid útmutató: a .NET-hez készült ügyféloldali kódtár QnA Maker'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a QnA Maker .NET-hez készült ügyféloldali kódtárat. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: eecbf162f3bd043bf4aabe9796ee095667e0cc39
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246079"
---
A .NET-hez készült QnA Maker ügyféloldali kódtára a következőre használható:

 * Tudásbázis létrehozása
 * Tudásbázis frissítése
 * Tudásbázis közzététele
 * Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
 * Várakozás a hosszan futó feladatra
 * Tudásbázis letöltése
 * Válasz kérése
 * Tudásbázis törlése

[Dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/)  |  [C#-minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portal a szerzői kulcs és az erőforrás nevének beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz**lehetőséget.
    * Az alkalmazásnak a QnA Maker APIhoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcs-és erőforrás-nevére. A kulcs és az erőforrás nevét a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studióval hozzon létre egy .NET Core-alkalmazást, és telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** a megoldásra kattint, és kiválasztja a **NuGet-csomagok kezelése**lehetőséget. A megnyíló csomagkezelő válassza a **Tallózás**lehetőséget, jelölje be az **előzetes verzió**használata jelölőnégyzetet, és keressen rá `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Válassza `2.0.0-preview.1` a verzió, majd a **telepítés**lehetőséget.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `qna-maker-quickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*.

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

Az alkalmazás könyvtárában telepítse az QnA Maker .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

Az alkalmazás `Main` metódusában adja hozzá a következő szakaszban látható változókat és kódokat, hogy az ebben a rövid útmutatóban szereplő gyakori feladatokat használja.

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés**területen találhatók.
> A Tudásbázis létrehozásához a teljes kulcsra van szükség. Csak az erőforrás nevére van szükség a végpontból. A formátum `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) például biztonságos kulcstároló-tárolót biztosít.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>Objektummodell

QnA Maker két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../concepts/active-learning-suggestions.md)részeként).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) objektum, amely a kulcsot tartalmazó Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után a [Tudásbázis tulajdonság használatával](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) hozhatja létre, kezelheti és teheti közzé a tudásbázist.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. Hívja meg az [ügyfelet. Operations. GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) metódus a műveleti azonosítóval a [kérelem állapotának](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)meghatározásához.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

Az előrejelzési QnA Maker ügyfél egy [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) objektum, amely a Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t, amely tartalmazza az előrejelzési futtatókörnyezet kulcsát, amelyet a szerzői ügyfél hívja vissza, a `client.EndpointKeys.GetKeys` Tudásbázis közzétételekor.

A [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) metódus használatával kaphat választ a lekérdezési futtatókörnyezettől.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket az QnA Maker .NET-hez készült ügyféloldali kódtár használatával:

* [A szerzői ügyfél hitelesítése](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis letöltése](#download-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Lekérdezés futásidejű kulcsának beolvasása](#get-query-runtime-key)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)
* [A lekérdezési futtatókörnyezet ügyfelének hitelesítése](#authenticate-the-runtime-for-generating-an-answer)
* [Válasz létrehozása a Tudásbázisból](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Az ügyfél hitelesítése a Tudásbázis létrehozásához

Hozzon létre egy ügyféltanúsítványt a kulccsal, és használja azt az erőforrással, hogy létrehozza a végpontot egy [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) létrehozásához a végponttal és a kulccsal. Hozzon létre egy [ServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet) objektumot.

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz**használja a [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok**esetében használja a [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek**esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

A létrehozási lépés a tudásbázishoz tartozó tulajdonságokat is tartalmazza:
* `defaultAnswerUsedForExtraction` -Ha nem talál választ, a rendszer a visszaadott értéket adja vissza
* `enableHierarchicalExtraction` -a kinyert QnA-párok közötti gyors kapcsolatok automatikus létrehozása
* `language` – Amikor létrehoz egy erőforrás első tudásbázisát, állítsa be a nyelvet a Azure Search indexben való használatra.

Hívja meg a [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) metódust, majd adja át a visszaadott művelet azonosítóját a [MonitorOperation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

A következő kód utolsó sora visszaadja a Tudásbázis AZONOSÍTÓját a MonitorOperation válaszból.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

A [`MonitorOperation`](#get-status-of-an-operation) Tudásbázis sikeres létrehozásához győződjön meg arról, hogy a fenti kódban hivatkozott függvény belefoglalása.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) használatával frissítheti, amely a DTO objektumok [hozzáadását](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [frissítését](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)és [törlését](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) tartalmazza a [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) metódushoz. A [MonitorOperation](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

Győződjön meg arról, hogy a [`MonitorOperation`](#get-status-of-an-operation) fenti kódban hivatkozott függvény belefoglalása a Tudásbázis sikeres frissítéséhez.

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) metódussal töltheti le az adatbázist a [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem fájl.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és közzéteszi a végpontján. Ez egy szükséges lépés a Tudásbázis lekérdezéséhez.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>Lekérdezés futásidejű kulcsának beolvasása

Miután közzétette a tudásbázist, szüksége lesz a lekérdezés futásidejű kulcsára a futtatókörnyezet lekérdezéséhez. Ez nem ugyanaz a kulcs, amely az eredeti ügyfél-objektum létrehozásához használatos.

A [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) osztály beszerzéséhez használja a [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) metódust.

Használja az objektumban visszaadott kulcs-tulajdonságok egyikét a Tudásbázis lekérdezéséhez.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

A Tudásbázis lekérdezéséhez szükség van egy futtatókörnyezeti kulcsra.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

Hozzon létre egy [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) , amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Használja a QnAMakerRuntimeClient a következőhöz:
* Válasz kérése a Tudásbázisból
* új, javasolt kérdések küldése az [Active learning](../concepts/active-learning-suggestions.md)tudásbázisba.

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Egy közzétett tudásbázisbeli válasz létrehozása a [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)használatával. [GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) metódus. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet). Hozzáférés a QueryDTO további tulajdonságaihoz, például a [legfelső](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) és [kontextushoz](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) a csevegési robotban való használatra.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

Ez egy egyszerű példa a Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) a lekérdezésben (az újrapróbálkozási logikával).

A _hurok_ és a feladat. a következő kódrészlet _késleltetése_ az újrapróbálkozási logika szimulálására szolgál. Ezeket a saját újrapróbálkozási logikával kell helyettesíteni.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)található.
