---
title: 'Gyors útmutató: QnA Maker ügyféloldali kódtár a Node.js'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a Node.js QnA Maker ügyféloldali kódtár használatának első lépéseit.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: b64379e81f6c4d3da37526d75e08bc0fbed37103
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91253929"
---
A Node.js a QnA Maker ügyféloldali kódtár használatával:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Előrejelzési futtatókörnyezet végponti kulcsának beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis letöltése
* Válasz kérése
* Tudásbázis törlése

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy [QnA Maker erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) a Azure Portalban a szerzői kulcs és az erőforrás beszerzéséhez. Az üzembe helyezést követően válassza **az Ugrás erőforráshoz**lehetőséget.
    * Az alkalmazásnak a QnA Maker APIhoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcs-és erőforrás-nevére. A kulcs és az erőforrás nevét a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Futtassa a `npm init -y` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` .

```console
npm init -y
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a következő NPM-csomagokat:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

Az alkalmazás `package.json` fájlját a rendszer a függőségekkel frissíti.

Hozzon létre egy index.js nevű fájlt, és importálja a következő könyvtárakat:

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

Hozzon létre egy változót az erőforrás Azure-kulcsához és az erőforrás nevéhez. A szerzői műveletek és az előrejelzési URL-címek altartományként is használják az erőforrás nevét.

> [!IMPORTANT]
> Lépjen a Azure Portalra, és keresse meg az előfeltételekben létrehozott QnA Maker erőforrás kulcsát és végpontját. Ezek az erőforrás **kulcs és végpont** lapján, az **Erőforrás-kezelés**területen találhatók.
> A Tudásbázis létrehozásához a teljes kulcsra van szükség. Csak az erőforrás nevére van szükség a végpontból. A formátum `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) például biztonságos kulcstároló-tárolót biztosít.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

## <a name="object-models"></a>Objektummodell

QnA Maker két különböző objektummodell-modellt használ:
* A **[QnAMakerClient](#qnamakerclient-object-model)** a Tudásbázis létrehozásához, kezeléséhez, közzétételéhez és letöltéséhez szükséges objektum.
* A **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** a TUDÁSBÁZIS GenerateAnswer API-val való lekérdezésére szolgáló objektum, és új javasolt kérdések küldése a Train API használatával (az [aktív tanulás](../concepts/active-learning-suggestions.md)részeként).


### <a name="qnamakerclient-object-model"></a>QnAMakerClient objektummodell

A szerzői QnA Maker ügyfél egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) objektum, amely hitelesíti az Azure-t a kulcsát tartalmazó hitelesítő adataival.

Miután létrehozta az ügyfelet [, a Tudásbázis használatával hozza](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) létre, kezelje és tegye közzé a tudásbázist.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. A [kérelem állapotának](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest)meghatározásához hívja meg a [Client. Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--msrest-requestoptionsbase-) METÓDUSt a műveleti azonosítóval.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient objektummodell

Az előrejelzési QnA Maker ügyfél egy QnAMakerRuntimeClient objektum, amely a Microsoft. Rest. ServiceClientCredentials használatával hitelesíti az Azure-t, amely tartalmazza az előrejelzési futtatókörnyezet kulcsát, amelyet a szerzői ügyfél hívásakor, az [ügyféltől kapott vissza. EndpointKeys. getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest#getkeys-msrest-requestoptionsbase-) a Tudásbázis közzététele után.


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

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy ServiceClientCredentials objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) objektum létrehozásához.

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz**használja a [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) objektumot.
    * A metaadatok és a követő kérések használatához használja a szerkesztői környezetet, mivel ezeket az adatokat az egyes QnA pár szintjén adja hozzá a rendszer.
* **Fájlok**esetében használja a [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) objektumot. A FileDTO tartalmazza a fájlnevet, valamint a fájl eléréséhez szükséges nyilvános URL-címet.
* **URL-címek**esetében a karakterláncok listáját használhatja a nyilvánosan elérhető URL-címek ábrázolására.

A létrehozási lépés a tudásbázishoz tartozó tulajdonságokat is tartalmazza:
* `defaultAnswerUsedForExtraction` -Ha nem talál választ, a rendszer a visszaadott értéket adja vissza
* `enableHierarchicalExtraction` -a kinyert QnA-párok közötti gyors kapcsolatok automatikus létrehozása
* `language` – Amikor létrehoz egy erőforrás első tudásbázisát, állítsa be a nyelvet a Azure Search indexben való használatra.

Hívja meg a [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) metódust a Tudásbázis információi alapján. A Tudásbázis adatai alapvetően egy JSON-objektum.

Ha a Create metódus visszatér, adja át a visszaadott művelet AZONOSÍTÓját a [wait_for_operation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez. A wait_for_operation metódus a művelet befejeződése után tér vissza. Elemezheti a `resourceLocation` visszaadott művelet fejlécének értékét az új Tudásbázis-azonosító lekéréséhez.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod&highlight=39,46)]

A [`wait_for_operation`](#get-status-of-an-operation) Tudásbázis sikeres létrehozásához győződjön meg arról, hogy a fenti kódban hivatkozott függvény belefoglalása.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) használatával frissítheti, amely a DTO objektumok [hozzáadását](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [frissítését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)és [törlését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) tartalmazza a [frissítési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) metódushoz. A DTO is alapvetően JSON-objektumok. A [wait_for_operation](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod&highlight=74,81)]

Győződjön meg arról, hogy a [`wait_for_operation`](#get-status-of-an-operation) fenti kódban hivatkozott függvény belefoglalása a Tudásbázis sikeres frissítéséhez.

## <a name="download-a-knowledge-base"></a>Tudásbázis letöltése

A [letöltési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#download-string--models-environmenttype--msrest-requestoptionsbase-) módszer használatával letöltheti az adatbázist a [QnADocumentsDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto?view=azure-node-latest)listájára. Ez _nem_ felel meg a QnA Maker portál exportálásának a **Beállítások** lapról, mert ennek a módszernek az eredménye nem TSV-fájl.

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB&highlight=2)]


## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé. A HTTP-válasz kódjának ellenőrzésével ellenőrizze, hogy a közzététel sikeres volt-e.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB&highlight=3)]


## <a name="get-query-runtime-key"></a>Lekérdezés futásidejű kulcsának beolvasása

Miután közzétette a tudásbázist, szüksége lesz a lekérdezés futásidejű kulcsára a futtatókörnyezet lekérdezéséhez. Ez nem ugyanaz a kulcs, amely az eredeti ügyfél-objektum létrehozásához használatos.

A [EndpointKeysDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto?view=azure-node-latest) osztály beszerzéséhez használja a [EndpointKeys. getKeys](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys?view=azure-node-latest) metódust.

Használja az objektumban visszaadott kulcs-tulajdonságok egyikét a Tudásbázis lekérdezéséhez.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey&highlight=4)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>A futtatókörnyezet hitelesítése a válasz létrehozásához

Hozzon létre egy QnAMakerRuntimeClient, amely lekérdezi a tudásbázist az aktív tanulásból kapott válasz vagy képzés létrehozásához.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

A QnAMakerRuntimeClient segítségével választ kaphat az ismeretekből, vagy új, javasolt kérdéseket küldhet az [Active learning](../concepts/active-learning-suggestions.md)tudásbázisában.

## <a name="generate-an-answer-from-the-knowledge-base"></a>Válasz létrehozása a Tudásbázisból

Válasz létrehozása közzétett Tudásbázisból a RuntimeClient. Runtime. generateAnswer metódus használatával. Ez a metódus elfogadja a Tudásbázis AZONOSÍTÓját és a QueryDTO. Hozzáférés a QueryDTO további tulajdonságaihoz, például a legfelső és kontextushoz a csevegési robotban való használatra.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer&highlight=3)]

Ez egy egyszerű példa a Tudásbázis lekérdezésére. A speciális lekérdezési forgatókönyvek megismeréséhez tekintse át az [egyéb lekérdezési példákat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) a lekérdezésben (az újrapróbálkozási logikával).

Az újrapróbálkozási logikát az alábbi _delayTimer_ hívásával szimulálhatja. Cserélje le ezt a saját újrapróbálkozási logikára.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation&highlight=8)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node index.js` paranccsal az alkalmazás könyvtárából.

```console
node index.js
```

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)található.