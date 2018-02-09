---
title: "Az Azure Event Grid használata a feltöltött képek átméretezésének automatizálására | Microsoft Docs"
description: "Az Azure Event Grid a blobok Azure Storage-ba való feltöltésekor aktiválódhat. A segítségével Azure Storage-ba feltöltött képfájlokat küldhet más szolgáltatásoknak, például az Azure Functions szolgáltatásnak átméretezés vagy egyéb javítás céljából."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: d8ffd9b3b9a315129ab0442908a9b3ad3bbecd1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Feltöltött képek átméretezésének automatizálása az Event Grid használatával

Az [Azure Event Grid](overview.md) egy felhőalapú eseménykezelési szolgáltatás. Az Event Grid lehetővé teszi, hogy előfizetéseket hozzon létre az Azure-szolgáltatások vagy külső erőforrások által létrehozott eseményekhez.  

Ez az oktatóanyag a Storage oktatóanyag-sorozat második része. Kibővíti az [előző Storage oktatóanyagot][previous-tutorial], és hozzáadja a kiszolgáló nélküli automatikus miniatűr-létrehozást az Azure Event Grid és az Azure Functions használatával. Az Event Grid lehetővé teszi az [Azure Functions](..\azure-functions\functions-overview.md) számára, hogy reagáljon az [Azure Blob Storage](..\storage\blobs\storage-blobs-introduction.md) eseményeire, és létrehozza a feltöltött képek miniatűrjeit. Az esemény-előfizetések a Blob Storage-esemény létrehozásakor jönnek létre. Amikor egy blob bekerül egy adott Blob Storage-tárolóba, a rendszer meghívja egy függvény végpontját. Az Event Gridből a függvénykötésnek továbbadott adatokat a rendszer arra használja, hogy hozzáférjen a blobhoz, és létrehozza a miniatűrt. 

Az Azure CLI és az Azure Portal segítségével hozzáadja az átméretezési funkciót egy meglévő képfeltöltő alkalmazáshoz.

![Közzétett webalkalmazás az Edge böngészőben](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Általános célú Azure Storage-fiók létrehozása
> * Kiszolgáló nélküli programkód üzembe helyezése az Azure Functions használatával
> * Blob Storage esemény-előfizetés létrehozása az Event Gridben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ El kell végeznie az előző Blob Storage oktatóanyagot: [Képadatok feltöltése a felhőbe az Azure Storage segítségével][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.14-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha nem a Cloud Shellt használja, először be kell jelentkeznie a(z) `az login` használatával.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Az Azure Functions szolgáltatásnak egy általános célú tárfiókra van szüksége. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy külön, általános célú tárfiókot az erőforráscsoportban.

A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. 

A következő parancsban a `<general_storage_account>` helyőrző helyett írja be az általános célú tárfiók saját globálisan egyedi tárfióknevét. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása  

Rendelkeznie kell egy függvényalkalmazással a függvény végrehajtásának biztosításához. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#az_functionapp_create) parancs használatával. 

A következő parancsban a `<function_app>` helyőrző helyett írja be a saját, egyedi függvényalkalmazás-nevét. Az `<function_app>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Ebben az esetben `<general_storage_account>` a létrehozott általános célú tárfiók neve.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Most konfigurálnia kell a függvényalkalmazást, hogy csatlakozzon a Blob Storage-hez. 

## <a name="configure-the-function-app"></a>A függvényalkalmazás konfigurálása

A függvénynek szüksége van arra, hogy a kapcsolati karakterlánc csatlakozzon a Blob Storage-fiókhoz. Ebben az esetben `<blob_storage_account>` az előző oktatóanyagban létrehozott Blob Storage-fiók neve. Kérje le a kapcsolati karakterláncot az [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) paranccsal. A miniatűrtároló nevét szintén a következőre kell állítani: `thumbs`. Adja hozzá ezeket az alkalmazásbeállításokat a függvényalkalmazáshoz az [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) paranccsal.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Most már üzembe helyezhet egy függvénykód-projektet a függvényalkalmazásban.

## <a name="deploy-the-function-code"></a>A függvénykód üzembe helyezése 

A képek átméretezését végző C# függvény ebben a [minta GitHub-adattárban](https://github.com/Azure-Samples/function-image-upload-resize) található. Helyezze üzembe ezt a Functions-kódprojektet az [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config) paranccsal. 

Az alábbi parancsban a `<function_app>` ugyanaz a függvényalkalmazás, amelyet az előző parancsfájlban létrehozott.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

A képátméretezés függvényt egy esemény-előfizetés aktiválja egy, a Blob által létrehozott eseményre. Az eseményindítónak átadott adatok között megtalálható a blob URL-címe, amelyet ezután az eseményindító átad a bemeneti kötésnek a feltöltött kép Blob Storage-ból való lekéréséhez. A függvény létrehoz egy miniatűrt, és a Blob Storage egyik külön tárolójába írja az eredményül kapott streamet. A függvénnyel kapcsolatos további tudnivalókért tekintse meg [a mintatárházban található információs fájlt](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).

A projekt az `EventGridTrigger` típusú eseményindítót használja. Az általános HTTP-eseményindítók helyett az Event Grid eseményindító használata ajánlott. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

A függvény projektkódját a rendszer közvetlenül a nyilvános mintaadattárból helyezi üzembe. További információk az Azure Functions üzembe helyezési lehetőségeiről: [Azure Functions – folyamatos üzembe helyezés](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Saját esemény-előfizetés létrehozása

Az esemény-előfizetés jelzi, hogy melyik szolgáltató eseményeit kívánja elküldeni egy adott végpontnak. Ebben az esetben a függvény közzéteszi a végpontot. Az alábbi lépések segítségével hozzon létre egy esemény-előfizetést a függvényéből az Azure Portalon: 

1. Az [Azure Portalon](https://portal.azure.com) kattintson a bal alsó sarokban található nyílra a szolgáltatások kibontásához, írja be a `functions` kifejezést a **Szűrő** mezőbe, majd válassza a **Függvényalkalmazások** lehetőséget. 

    ![Keresse meg a Függvényalkalmazásokat az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Bontsa ki a függvényalkalmazást, válassza ki az **imageresizerfunc** függvényt, majd válassza az **Event Grid-előfizetés hozzáadása** lehetőséget.

    ![Keresse meg a Függvényalkalmazásokat az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Használja a táblázatban megadott esemény-előfizetési beállításokat.

    ![Esemény-előfizetés létrehozása a függvényből az Azure Portalon](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | imageresizersub | Az új esemény-előfizetés azonosítóneve. | 
    | **Témakörtípus** |  Tárfiókok | Válassza ki a Storage-fiók eseményszolgáltatóját. | 
    | **Előfizetés** | Az Azure-előfizetése | Alapértelmezés szerint az aktuális Azure-előfizetés van kiválasztva.   |
    | **Erőforráscsoport** | myResourceGroup | Válassza a **Meglévő használata** elemet, majd válassza ki az oktatóanyagban használt erőforráscsoportot.  |
    | **Példány** |  `<blob_storage_account>` |  Válassza ki a létrehozott Blob Storage-fiókot. |
    | **Eseménytípusok** | Létrehozott blob | Törölje a jelölést az összes típus mellől a **Létrehozott blob** kivételével. A rendszer csak a `Microsoft.Storage.BlobCreated` eseménytípusokat adja át a függvénynek.| 
    | **Előfizető végpontja** | automatikusan létrehozott | Használja a rendszer által létrehozott végpont URL-címét. | 
    | **Előtagszűrő** | /blobServices/default/containers/images/blobs/ | Csak a **képek** tároló Storage-eseményeit szűri.| 

4. Kattintson a **Létrehozás** gombra az esemény-előfizetés hozzáadásához. Ez létrehoz egy esemény-előfizetést, amely aktiválja az **imageresizerfunc** függvényt, amikor a **képek** tárolóba bekerül egy blob. Az átméretezett képek a **miniatűrök** tárolóba kerülnek.

Most, hogy konfigurálta a háttérszolgáltatásokat, tesztelni fogja a képátméretezési funkciót a minta-webalkalmazásban. 

## <a name="test-the-sample-app"></a>A mintaalkalmazás tesztelése

A képátméretezés webalkalmazásban való teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

Kattintson a **Fényképek feltöltése** területre egy fájl kiválasztásához és feltöltéséhez. Fényképet is behúzhat a területre. 

Figyelje meg, hogy miután a feltöltött kép eltűnik, a feltöltött kép másolata megjelenik a **Létrehozott miniatűrök** nevű forgó képválasztón. A függvény átméretezte a képet, hozzáadta a „miniatűrök” tárolóhoz, a webes ügyfél pedig letöltötte. 

![Közzétett webalkalmazás az Edge böngészőben](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Általános célú Azure Storage-fiók létrehozása
> * Kiszolgáló nélküli programkód üzembe helyezése az Azure Functions használatával
> * Blob Storage esemény-előfizetés létrehozása az Event Gridben

Lépjen a Storage oktatóanyag-sorozat harmadik részére, amelyből megtudhatja, hogyan férhet hozzá biztonságosan a tárfiókhoz.

> [!div class="nextstepaction"]
> [Biztonságos hozzáférés egy alkalmazás adataihoz a felhőben](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Az Event Griddel kapcsolatos további információkért lásd: [Az Azure Event Grid bemutatása](overview.md). 
+ Ha meg szeretne próbálkozni egy másik oktatóanyaggal, amely az Azure Functions szolgáltatással foglalkozik: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
