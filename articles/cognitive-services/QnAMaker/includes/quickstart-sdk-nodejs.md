---
title: 'Rövid útmutató: QnA Maker ügyfélkódtár a Node.js fájlhoz'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a QnA Maker ügyfélkódtár node.js.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021064"
---
A Node.js qna maker ügyfélkönyvtárának használatával:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Közzétett végpontkulcs beszereznie
* Várakozás a hosszan futó feladatra
* Tudásbázis törlése

[Referenciadokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [könyvtár forráskódcsomag](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org)jelenlegi verziója .

## <a name="setting-up"></a>Beállítása

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a QnA Maker számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen.

Miután bekapta a kulcsot és a végpontot az erőforrásból, az értékeket az Azure Portalról az új erőforráshoz a rövid útmutató lapon szerezheti be.

[Hozzon létre környezeti változókat,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)elnevezett `QNAMAKER_AUTHORING_KEY` és `QNAMAKER_ENDPOINT`. Az [.env.mintafájlt](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) `.env` másolhatja, és használhatja a fájlban lévő környezeti változókat.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Futtassa a `npm init -y` parancsot egy `package.json` csomópontalkalmazás fájllal való létrehozásához.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Az ügyféltár telepítése

Telepítse a szükséges és opcionális NPM csomagokat:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel. A `dotenv` nem kötelező, és lehetővé teszi a környezeti változók bevitelét egy szövegfájlban. Ne ellenőrizze `.env` a forrásvezérlőt.


## <a name="object-model"></a>Objektummodell

A QnA Maker-ügyfél egy [QnAMakerClient objektum,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) amely hitelesíti magát az Azure-ban serviceClientCredentials használatával, amely tartalmazza a kulcsot.

Az ügyfél létrehozása után használja a [Tudásbázis](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) tulajdonságot a tudásbázis létrehozása, kezelése és közzététele segítségével.

Kezelje tudásbázisát egy JSON-objektum küldésével. Azonnali műveletek esetén a metódus általában egy JSON-objektumot ad vissza, amely jelzi az állapotot. Hosszú ideig futó műveletek esetén a válasz a művelet azonosítója. Hívja fel az [ügyfelet. Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) metódus a műveletazonosítóval [a kérelem állapotának](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)meghatározásához.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a QnA Maker ügyfélkódtárban a Node.js fájlhoz:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Közzétett végpont beszereznie](#get-published-endpoint)
* [Művelet állapotának beszereznie](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Hozzon létre egy `index.js` nevű fájlt. Adja hozzá a QnA Maker könyvtárat és a függőségeket a fájlhoz.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

|Környezeti változó|Node.js változó|Példa|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|A kulcs egy 32 karakterből álló karakterlánc, és elérhető az Azure Portalon, a QnA Maker erőforrás, a gyorsútmutató lapon. Ez nem ugyanaz, mint az előrejelzési végpont kulcs.|
|`QNAMAKER_ENDPOINT`|`endpoint`| A szerzői végpont a formátumában `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`tartalmazza az **erőforrás nevét.** Ez nem ugyanaz az URL-cím, amelyet az előrejelzési végpont lekérdezéséhez használnak.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ezután hozzon létre egy ApiKeyCredentials objektumot a kulccsal, és használja azt a végponttal egy [QnAMakerClient objektum](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) létrehozásához. Az ügyfélobjektum segítségével szerezzen be egy [tudásbázis-ügyfélobjektumot.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A tudásbázis három forrásból tárolja a [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) objektum kérdés- és válaszpárjait:

* Szerkesztői **tartalom**esetén használja a [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) objektumot.
* **Fájlok**esetén használja a [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) objektumot.
* **Url-címekhez**használja a karakterláncok listáját.

Hívja meg a [létrehozási](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) metódust a tudásbázis adataival. A tudásbázis-információ alapvetően JSON-objektum.

Amikor a létrehozási metódus visszatér, adja át a visszaadott műveletazonosítót a [wait_for_operation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez. A wait_for_operation metódus a művelet befejezésekor ad vissza. Elemezheti a `resourceLocation` visszaadott művelet fejlécértékét az új tudásbázis-azonosító leolvasásához.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

Győződjön meg [`wait_for_operation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres létrehozása érdekében.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázis tikóállapot-objektumát frissítheti a tudásbázis-azonosító és az [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) segítségével, amely [hozzáadja,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add) [frissíti](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)és [törli](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) a [Frissítési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) metódus DTO-objektumait. A DTO-k alapvetően JSON-objektumok is. A [wait_for_operation](#get-status-of-an-operation) módszerrel állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

Győződjön meg [`wait_for_operation`](#get-status-of-an-operation) arról, hogy a függvény szerepel a fenti kódban, a tudásbázis sikeres frissítése érdekében.

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) módszerrel. Ez veszi az aktuális mentett és betanított modell, a tudásbázis-azonosító által hivatkozott, és közzéteszi, hogy egy végponton. Ellenőrizze a HTTP-válaszkódot a közzététel ellenőrzéséhez.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Közzétett végpont beszereznie

A tudásbázis közzététele után a közzétett tudásbázis elérése a lekérdezés előrejelzési runtime generateAnswer API-n keresztül. Ehhez a futásidejű végpontkulcsra van szükség. Ez más, mint a szerzői kulcs.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

A hívásból két végpontkulcs jön vissza. Csak egy szükséges a futásidejű végpont eléréséhez.

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a tudásbázis azonosítójának paraméterét tartalmazó [törlési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) módszerrel.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beszereznie

Egyes módszerek, például a létrehozás és a frissítés, elegendő időt vehet igénybe, hogy ahelyett, hogy megvárnák a folyamat befejezését, egy [műveletet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) adnak vissza. Használja a [művelet azonosítóját](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) a művelet lekérdezési (újrapróbálkozási logikával) állapotának meghatározásához az eredeti módszer.

A _delayTimer_ hívás a következő kódblokkban az újrapróbálkozási logika szimulálására szolgál. Cserélje le ezt a saját újrapróbálkozási logikájára.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa `node index.js` az alkalmazást az alkalmazáskönyvtárból származó paranccsal.

A cikkben szereplő összes kódrészlet [elérhető,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) és egyetlen fájlként futtatható.

```console
node index.js
```

A program a konzol állapotát nyomtatja ki:

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)