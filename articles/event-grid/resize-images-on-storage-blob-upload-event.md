---
title: "Használata Azure esemény rács automatikus átméretezését feltöltött képek |} Microsoft Docs"
description: "Az Azure Event rács aktiválhatók az Azure Storage blob feltöltések. Egyéb fejlesztések és egyéb szolgáltatások, például az Azure Functions átméretezéséhez Azure Storage feltöltött lemezkép fájlok küldése használhatja."
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
ms.openlocfilehash: 22eafca56eb5677c63a833d298799b725c50f768
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Esemény rács segítségével feltöltött képek átméretezése automatizálásához

[Az Azure Event rács](overview.md) egy a szolgáltatás a felhőben van. Esemény rács előfizetések az Azure-szolgáltatások vagy a külső erőforrások által kiváltott események létrehozását teszi lehetővé.  

Ez az oktatóanyag része két tárolási oktatóanyag egy sorozat része. Kiterjed a [előző tárolási oktatóanyag] [ previous-tutorial] hozzáadásához a kiszolgáló nélküli automatikus miniatűr létrehozása Azure esemény rács és az Azure Functions használatával. Esemény rács lehetővé teszi, hogy [Azure Functions](..\azure-functions\functions-overview.md) válaszolni [Azure Blob Storage tárolóban](..\storage\blobs\storage-blobs-introduction.md) események és a feltöltött képek indexképének létrehozására használnak. Egy esemény-előfizetés létrehozása szemben a Blob storage esemény létrehozása. Egy blob ad hozzá egy adott Blob storage tárolót, a függvény végpont neve. A függvénykötés esemény rácsban átadott adatok eléréséhez a blob, és a miniatűr képére készítése szolgál. 

Az Azure parancssori felület és az Azure portál segítségével az átméretezési funkció hozzáadása egy meglévő lemezkép feltöltése alkalmazást.

![Az Edge böngészőben a közzétett webes alkalmazás](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Általános Azure Storage-fiók létrehozása
> * Az Azure Functions használatával kiszolgáló nélküli kód telepítése
> * Előfizetés létrehozása a Blob storage esemény esemény rácsban

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Végrehajtotta az előző Blob storage-oktatóanyag: [kép adatok a felhőben az Azure Storage feltöltése][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez a témakör van szükség, hogy futnak-e az Azure parancssori felület 2.0.14 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha nem a Cloud Shellt használja, először be kell jelentkeznie a(z) `az login` használatával.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Az Azure Functions egy általános storage-fiók szükséges. Külön általános storage-fiók létrehozása az erőforráscsoportban használatával a [az storage-fiók létrehozása](/cli/azure/storage/account#create) parancsot.

A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. 

Az alábbi parancs helyettesítse a saját globálisan egyedi nevet a általános tárfiókot, ahol megjelenik a `<general_storage_account>` helyőrző. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása  

Rendelkeznie kell egy függvény alkalmazást, a függvény végrehajtása üzemeltetéséhez. A függvényalkalmazás szolgáltat környezetet a függvénykód kiszolgáló nélküli végrehajtásához. Hozzon létre egy függvényalkalmazást az [az functionapp create](/cli/azure/functionapp#create) parancs használatával. 

Az alábbi parancs helyettesítse a saját egyedi alkalmazás függvénynév válaszoknál láthatja a `<function_app>` helyőrző. Az `<function_app>` nevet a rendszer a függvényalkalmazás alapértelmezett DNS-tartományának részeként használja, ezért annak egyedinek kell lennie az Azure összes alkalmazásában. Ebben az esetben `<general_storage_account>` létrehozott általános storage-fiók neve.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

A függvény alkalmazás csatlakozzon a blob storage most konfigurálnia kell. 

## <a name="configure-the-function-app"></a>A függvény alkalmazás konfigurálása

A függvény a kapcsolódási karakterlánc csatlakozni a blob storage-fiók szükséges. Ebben az esetben `<blob_storage_account>` az előző oktatóanyag létrehozott Blob storage-fiók neve. A kapcsolati karakterlánc beolvasása a [az storage-fiók megjelenítése-kapcsolat-karakterlánc](/cli/azure/storage/account#show-connection-string) parancsot. A miniatűr képére Tárolónév is meg kell `thumbs`. Ezek a beállítások alkalmazás hozzáadása a függvény alkalmazás a [az functionapp appsettings konfiguráció](/cli/azure/functionapp/config/appsettings#set) parancsot.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Egy függvény kódú projektben a függvény alkalmazás most már telepítheti.

## <a name="deploy-the-function-code"></a>A függvény kód központi telepítése 

A C# funkció, amely végrehajtja a rendszerképek átméretezéséhez érhető el ezen [minta GitHub-tárházban](https://github.com/Azure-Samples/function-image-upload-resize). A projekt rendszerbe állítására funkciók kód a függvény alkalmazás segítségével a [az functionapp a központi telepítési forrás konfigurációs](/cli/azure/functionapp/deployment/source#config) parancsot. 

Az alábbi parancs `<function_app>` ugyanazt a függvény az alkalmazást az előző parancsfájl létrehozott van.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

A kép átméretezése függvény egy Eseményelőfizetés létrehozott Blob esemény váltja ki. Az eseményindító átadott szerepel a blob, a feltöltött lemezkép beszerezni a Blob storage bemeneti kötése pedig átadott URL-CÍMÉT. A függvény hoz létre a miniatűr képére, és az eredményül kapott adatfolyam ír egy külön tárolóba, a Blob Storage tárolóban. Ez a funkció kapcsolatos további tudnivalókért tekintse meg a [információs fájl a minta-tárházban](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
A projekt funkciókódot közvetlenül a nyilvános minta tárházból van telepítve. További információt az Azure Functions üzembe helyezési lehetőségeit, tekintse meg a [folyamatos üzembe helyezés az Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Az esemény-előfizetés létrehozása

Egy esemény-előfizetést azt jelzi, mely szolgáltató által létrehozott eseményeket egy adott végpont küldeni. Ebben az esetben a végpont tesz elérhetővé a függvény. Egy esemény-előfizetés létrehozása az Azure-portálon a függvényből tegye a következőket: 

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a nyílra bal alsó bontsa ki a szolgáltatásokat, írja be a következőt `functions` a a **szűrő** mezőben, majd válassza a **függvény alkalmazások**. 

    ![Keresse meg a függvény alkalmazásokat, hogy az Azure-portálon](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Bontsa ki a függvény alkalmazást, válassza ki a **imageresizerfunc** működik, és válassza ki **hozzáadása esemény rács előfizetés**.

    ![Keresse meg a függvény alkalmazásokat, hogy az Azure-portálon](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Az előfizetés beállításokat használják a táblázatban megadott.

    ![A függvény az Azure portálon esemény előfizetés létrehozása](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | imageresizersub | Amely azonosítja az új esemény-előfizetés nevét. | 
    | **A témakör típusa** |  Tárfiókok | Válassza ki azt a tárolási fiók esemény szolgáltatót. | 
    | **Előfizetés** | Az Ön előfizetése | Alapértelmezés szerint a jelenlegi előfizetéséhez ki.   |
    | **Erőforráscsoport** | myResourceGroup | Válassza ki **meglévő** , és válassza ki az erőforrást használja ebben a témakörben.  |
    | **Példány** |  `<blob_storage_account>` |  Válassza ki a létrehozott Blob storage-fiók. |
    | **Esemény típusa** | A BLOB létrehozása | Törölje a jelet minden típus más, mint **létrehozott Blob**. Csak esemény típusú `Microsoft.Storage.BlobCreated` van átadva a függvénynek.| 
    | **Előfizető végpont** | automatikusan létrehozott | Az Ön létrehozott végpont URL-Címének használata. | 
    | **Előtag-szűrő** | / blobServices/alapértelmezett/tárolók/képek/blobok / | Ha csak a storage-események szűrése a a **képek** tároló.| 

4. Kattintson a **létrehozása** az esemény előfizetés felvételéhez. Ezzel létrehoz egy esemény-előfizetést, amely elindítja a **imageresizerfunc** amikor blob hozzáadódik a **képek** tároló. Átméretezett képek hozzáadódnak a **OK** tároló.

Most, hogy a háttér-szolgáltatás van konfigurálva, a lemezkép átméretezési funkció kell tesztelni a minta webalkalmazást. 

## <a name="test-the-sample-app"></a>A minta-alkalmazás tesztelése

Kép átméretezése a webes alkalmazás teszteléséhez tallózással keresse meg a közzétett alkalmazás URL-CÍMÉT. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

Kattintson a **fénykép feltöltése** régiót válassza ki, és a fájl feltöltése. Egy fénykép húzhatja ebbe a régióba is. 

Figyelje meg, hogy után eltűnik a feltöltött lemezkép, a feltöltött lemezkép egy példányát megjeleníti a **jön létre a miniatűrök** forgódob. Ez a rendszerkép lett átméretezték, ezért a függvény a OK tárolóhoz adni és tölti le a webes ügyféllel. 

![Az Edge böngészőben a közzétett webes alkalmazás](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Általános Azure Storage-fiók létrehozása
> * Az Azure Functions használatával kiszolgáló nélküli kód telepítése
> * Előfizetés létrehozása a Blob storage esemény esemény rácsban

Előzetes három részét a tárolási oktatóanyag adatsorozat áttekintésével megismerheti, hogyan biztosíthat biztonságos hozzáférést a tárfiókhoz.

> [!div class="nextstepaction"]
> [Az alkalmazások adatokhoz a felhőben való biztonságos hozzáférés](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Esemény rács kapcsolatos további információkért lásd: [megismerkedhet az Azure Event rács](overview.md). 
+ Próbálja meg egy másik oktatóanyag, amely az Azure Functions, lásd: [hozzon létre egy függvényt, amely az Azure Logic Apps](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md