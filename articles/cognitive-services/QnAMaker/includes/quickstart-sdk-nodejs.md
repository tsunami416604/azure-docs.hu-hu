---
title: 'Rövid útmutató: a Node. js-hez készült ügyféloldali kódtár QnA Maker'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti meg a Node. js-hez készült QnA Maker ügyféloldali kódtárat.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021064"
---
A Node. js-hez készült QnA Maker ügyféloldali kódtár a következőhöz használható:

* Tudásbázis létrehozása
* Tudásbázis frissítése
* Tudásbázis közzététele
* Közzétett végponti kulcs beolvasása
* Várakozás a hosszan futó feladatra
* Tudásbázis törlése

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node. js-minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást QnA Maker a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával.

Miután beolvasta a kulcsot és a végpontot az erőforrásból, szerezze be az értékeket az új erőforráshoz tartozó Azure Portal, a rövid útmutató lapon.

[Hozzon létre](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)`QNAMAKER_AUTHORING_KEY` és `QNAMAKER_ENDPOINT`nevű környezeti változót. A [. env. Sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) fájlt átmásolhatja `.env`re, és használhatja a fájl környezeti változóit.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

A `npm init -y` parancs futtatásával hozzon létre egy csomópont-alkalmazást egy `package.json`-fájllal.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a szükséges és választható NPM-csomagokat:

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

Az alkalmazás `package.json` fájlja a függőségekkel frissül. A `dotenv` megadása nem kötelező, és lehetővé teszi a környezeti változók beállítását szövegfájlban. Ne vizsgálja meg a `.env` a forrás vezérlőelemben.


## <a name="object-model"></a>Objektummodell

A QnA Maker ügyfél egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) objektum, amely a kulcsot tartalmazó ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után használja a tudásbázist [a Tudásbázis](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) létrehozásához, kezeléséhez és közzétételéhez.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. Hívja meg az [ügyfelet. Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) metódus a műveleti azonosítóval a [kérelem állapotának](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)meghatározásához.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node. js-hez készült QnA Maker ügyféloldali kódtár használatával:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Közzétett végpont beolvasása](#get-published-endpoint)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Hozzon létre egy `index.js` nevű fájlt. Adja hozzá a QnA Maker könyvtárat és a függőségeket a fájlhoz.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

|Környezeti változó|Node. js-változó|Példa|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|A kulcs egy 32 karakterből álló karakterlánc, amely a Azure Portal QnA Maker erőforráson, a rövid útmutató lapon érhető el. Ez nem ugyanaz, mint az előrejelzési végpont kulcsa.|
|`QNAMAKER_ENDPOINT`|`endpoint`| A szerzői végpont `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátuma tartalmazza az **erőforrás nevét**. Ez nem ugyanaz az URL-cím, amely az előrejelzési végpont lekérdezésére szolgál.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ezután hozzon létre egy ApiKeyCredentials objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) objektum létrehozásához. Az ügyfél-objektum segítségével szerezzen be egy [Tudásbázis-ügyfél](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) objektumot.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz**használja a [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) objektumot.
* **Fájlok**esetében használja a [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) objektumot.
* **URL-címek**esetén használja a karakterláncok listáját.

Hívja meg a [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) metódust a Tudásbázis információi alapján. A Tudásbázis adatai alapvetően egy JSON-objektum.

Ha a Create metódus visszatér, adja át a visszaadott művelet AZONOSÍTÓját a [wait_for_operation](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez. A wait_for_operation metódus a művelet befejeződése után tér vissza. Elemezheti a visszaadott művelet `resourceLocation` fejlécének értékét az új Tudásbázis-azonosító lekéréséhez.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

A Tudásbázis sikeres létrehozásához ellenőrizze, hogy a fenti kódban hivatkozott [`wait_for_operation`](#get-status-of-an-operation) függvény szerepeljen-e.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) használatával frissítheti, amely a DTO objektumok [hozzáadását](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [frissítését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)és [törlését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) tartalmazza a [frissítési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) metódushoz. A DTO is alapvetően JSON-objektumok. A [wait_for_operation](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

A Tudásbázis sikeres frissítéséhez ellenőrizze, hogy a fenti kódban hivatkozott [`wait_for_operation`](#get-status-of-an-operation) függvény szerepeljen-e.

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé. Ellenőrizze, hogy sikeres volt-e a HTTP-válasz kódja a közzététel ellenőrzéséhez.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>Közzétett végpont beolvasása

A Tudásbázis közzétételét követően a közzétett tudásbázist a lekérdezés előrejelzési futtatókörnyezetének "generateAnswer API" használatával érheti el. Ennek elvégzéséhez szüksége lesz a futtatókörnyezet végponti kulcsára. Ez nem ugyanaz, mint a szerzői kulcs.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

A rendszer két végponti kulcsot ad vissza a hívásból. Csak egy szükséges a futásidejű végpont eléréséhez.

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) a lekérdezésben (az újrapróbálkozási logikával).

Az újrapróbálkozási logikát az alábbi _delayTimer_ hívásával szimulálhatja. Cserélje le ezt a saját újrapróbálkozási logikára.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `node index.js` paranccsal az alkalmazás könyvtárából.

A cikkben szereplő kódrészletek mindegyike [elérhető](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js) , és egyetlen fájlként is futtatható.

```console
node index.js
```

A program az állapotot a konzolra nyomtatja:

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

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)