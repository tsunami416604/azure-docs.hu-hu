---
title: Rövid útmutató – blob létrehozása az Azure Storage-ban a JavaScript és a HTML használatával a böngészőben
description: Megtudhatja, hogyan tölthet fel, listázhat és törölhet blobokat a JavaScript használatával egy HTML-oldalon.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 08/29/2019
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 3eb6f68a443e29a7d4c7b4dedad38783f838dee5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686667"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascripthtml-in-the-browser"></a>Rövid útmutató: Blobok feltöltése, listázása és törlése a böngészőben az Azure Storage v10 SDK-val a JavaScripthez/HTML-hez

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti az [Azure Storage SDK-t a JavaScript-blob-](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob#readme) kódtár használatával a Blobok a böngészőben teljes egészében futó JavaScript-kódból való kezeléséhez. Az itt bemutatott megközelítés szemlélteti a megfelelő biztonsági eszközök használatát is a Blob Storage-fiók biztonságos elérése érdekében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Az Azure Storage JavaScript ügyféloldali kódtárai nem működnek közvetlenül a fájlrendszerből, és egy webkiszolgálóról kell kiszolgálni. Ez a témakör a [Node. js](https://nodejs.org) használatával indít el egy alapszintű kiszolgálót. Ha nem szeretné telepíteni a csomópontot, bármilyen más eszközt használhat helyi webkiszolgáló futtatásához.

A hibakeresés lépéseinek követéséhez szüksége lesz a [Visual Studio Code](https://code.visualstudio.com) -ra és a [Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge) bővítményhez készült [Chrome](vscode:extension/msjsdiag.debugger-for-chrome) vagy Debugger hibakeresőre.

## <a name="setting-up-storage-account-cors-rules"></a>Tárfiók CORS-szabályainak beállítása

Ahhoz, hogy a webalkalmazás hozzáférhessen a blob Storage-hoz az ügyféltől, konfigurálnia kell a fiókját, hogy engedélyezze a [több eredetű erőforrás-megosztást](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)vagy a CORS.

Lépjen vissza az Azure Portalra, és válassza ki a tárfiókot. Új CORS-szabály definiálásához navigáljon a **Beállítások** szakaszra, és kattintson a **CORS** hivatkozásra. Ezután kattintson a **Hozzáadás** gombra a **CORS-szabály hozzáadása** ablak megnyitásához. Ebben a rövid útmutatóban egy nyitott CORS-szabályt hozunk létre:

![Azure Blob Storage-fiók CORS-beállításai](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

A következő tábla az egyes CORS-beállításokat írja le, és ismerteti a szabály meghatározásához használt értékeket.

|Beállítás  |Érték  | Leírás |
|---------|---------|---------|
| Engedélyezett eredetek | * | Elfogadható eredetekként beállított tartományok vesszővel tagolt listáját fogadja el. A `*` érték beállításakor minden tartomány hozzáfér a tárfiókhoz. |
| Engedélyezett műveletek     | törlés, lekérés, fej, egyesítés, közzététel, beállítások és áthelyezés | A tárfiókon futtatható HTTP-műveleteket listázza. Ebben a rövid útmutatóban válassza ki az összes elérhető beállítást. |
| Engedélyezett fejlécek | * | A tárfiókban engedélyezett kérelemfejlécek listáját határozza meg (beleértve az előtaggal ellátott fejléceket). A `*` érték beállítása minden fejléc hozzáférését engedélyezi. |
| Közzétett fejlécek | * | A fiók által engedélyezett válaszfejléceket listázza. A `*` érték beállítása esetén a fiók bármilyen fejlécet küldhet.  |
| Maximális időtartam (másodperc) | 86400 | A maximális idő, ameddig a böngésző gyorsítótárazza az előzetes OPTIONS kérést. A *86400* érték lehetővé teszi, hogy a gyorsítótár egy teljes napig megmaradjon. |

> [!IMPORTANT]
> Győződjön meg arról, hogy az éles környezetben használt összes beállítás elérhetővé teszi a Storage-fiók számára a biztonságos hozzáférés fenntartásához szükséges minimális hozzáférést. Az itt leírt CORS-beállítások megfelelőek ehhez a rövid útmutatóhoz, mert laza biztonsági szabályzatot határoznak meg. Nem ajánlottak azonban valós környezetekben.

Ezután az Azure Cloud Shell-lel létrehoz egy biztonsági jogkivonatot.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Közös hozzáférési aláírás létrehozása

A böngészőben futó kód a közös hozzáférésű jogosultságkóddal (SAS) engedélyezteti a Blob Storage-ba érkező kéréseket. A SAS használatával az ügyfél a fiók hozzáférési kulcsa vagy kapcsolati sztringje nélkül is elvégezheti a tárolási erőforrásokhoz való hozzáférés engedélyeztetését. Az SAS-sel kapcsolatos további információkat a [közös hozzáférésű jogosultságkód (SAS) használatát ismertető](../common/storage-sas-overview.md) cikkben olvashat.

Létrehozhat egy SAS-t az Azure CLI használatával az Azure Cloud shellben vagy a Azure Portal vagy Azure Storage Explorer. A következő táblázat azokat a paramétereket ismerteti, amelyekkel a rendszernek értékeket kell megadnia a parancssori felülettel való SAS létrehozásához.

| Paraméter      |Leírás  | Helyőrző |
|----------------|-------------|-------------|
| *expiry*       | A hozzáférési jogkivonat lejárati dátuma ÉÉÉÉ-HH-NN formátumban. Írja be a holnapi dátumot ehhez a rövid útmutatóhoz. | *FUTURE_DATE* |
| *account-name* | A tárfiók neve. Használja a korábbi lépésben félretett nevet. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | A tárfiókkulcs. Használja a korábbi lépésben félretett kulcsot. | *YOUR_STORAGE_ACCOUNT_KEY* |

Használja az alábbi CLI-parancsot az egyes helyőrzők tényleges értékeivel, hogy létrehozzon egy SAS-t, amelyet használhat a JavaScript-kódban.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

A paraméterek után szereplő értékek nehezen érthetőek lehetnek. Ezek a paraméterértékek a megfelelő engedélyek első betűiből vannak összeállítva. A következő táblázat az értékek eredetét írja le:

| Paraméter        | Érték   | Leírás  |
|------------------|---------|---------|
| *permissions*    | racwdl  | Ez az SAS *olvasási*, *hozzáfűzési*, *létrehozási*, *írási*, *törlési* és *listázási* képességeket engedélyez. |
| *resource-types* | sco     | Az SAS a *szolgáltatás*, a *tároló* és az *objektum* erőforrásokra van hatással. |
| *services*       | b       | Az SAS a *Blob* szolgáltatásra van hatással. |

Most, hogy létrehozta az SAS-t, másolja át a visszaadott értéket, és mentse valahova egy későbbi lépésben való használatra. Ha az SAS-t az Azure CLI-től eltérő módszerrel hozta létre, akkor el kell távolítania a kezdeti `?`, ha van ilyen. Ez a karakter egy URL-elválasztó, amely már szerepel az URL-sablonban a témakör későbbi részében, ahol a SAS használatban van.

> [!IMPORTANT]
> Éles környezetben mindig SSL használatával adja át az SAS-jogkivonatot. Ezenkívül az SAS-jogkivonatokat a kiszolgálón kell létrehozni, és a HTML-oldalra kell küldeni, hogy a rendszer visszaadja azokat az Azure Blob Storage-ba. Érdemes például kiszolgáló nélküli függvényt használni SAS-jogkivonatok létrehozásához. Az Azure Portal olyan függvénysablonokat tartalmaz, amelyekkel JavaScript-függvény használatával hozható létre SAS.

## <a name="implement-the-html-page"></a>A HTML-oldal implementálása

Ebben a szakaszban létre fog hozni egy alapszintű weblapot, és a VS Code-t kell konfigurálnia az oldal elindításához és hibakereséséhez. A elindítása előtt azonban a Node. js-t kell használnia egy helyi webkiszolgáló elindításához és az oldal kiszolgálásához, amikor a böngésző kéri azt. Ezután adja hozzá a JavaScript-kódot a különböző blob Storage API-k meghívásához, és jelenítse meg az eredményeket az oldalon. A hívások eredményeit a [Azure Portal](https://portal.azure.com), a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)és az [Azure Storage bővítményben](vscode:extension/ms-azuretools.vscode-azurestorage) is láthatja a vs Code-ban.

### <a name="set-up-the-web-application"></a>A webalkalmazás beállítása

Először hozzon létre egy *Azure-Blobs-JavaScript* nevű új mappát, és nyissa meg a vs Code-ban. Ezután hozzon létre egy új fájlt a VS Code-ban, adja hozzá a következő HTML-kódot, és mentse az *index. html* néven az *Azure-Blobs-JavaScript* mappába.

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>A hibakereső konfigurálása

A Debugger bővítmény a VS Code-ban való beállításához válassza a **hibakeresés > konfiguráció hozzáadása...** lehetőséget, majd a **Chrome** vagy a **Edge**lehetőséget, attól függően, hogy az előfeltételek szakaszban milyen bővítményt telepített. Ez a művelet létrehoz egy *Launch. JSON* fájlt, és megnyitja a szerkesztőben.

Ezután módosítsa a *Launch. JSON* fájlt úgy, hogy az `url` érték tartalmazza a `/index.html` az alábbiak szerint:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Ez a konfiguráció közli a VS Code-val, hogy melyik böngészőt kell elindítani, és melyik URL-címet kell betölteni.

### <a name="launch-the-web-server"></a>A webkiszolgáló elindítása

A helyi Node. js-webkiszolgáló indításához válassza a **> terminál megtekintése** lehetőséget, hogy a vs Code-ban nyissa meg a konzol ablakát, majd írja be a következő parancsot.

```console
npx http-server
```

Ez a parancs telepíti a *http-Server* csomagot, és elindítja a kiszolgálót, így az aktuális mappa az alapértelmezett URL-címeken keresztül érhető el, beleértve az előző lépésben jelzetteket is.

### <a name="start-debugging"></a>Hibakeresés indítása

Ha az *index. html fájlt* a BÖNGÉSZŐBEN a vs Code Debuggerrel együtt szeretné elindítani, válassza a **hibakeresés > a hibakeresés elindítása** lehetőséget, vagy nyomja le az F5 billentyűt a vs Code-ban.

A megjelenített felhasználói felület még nem csinál semmit, de a következő szakaszban a JavaScript-kódot fogja hozzáadni az egyes függvények megvalósításához. Ezután megadhatja a töréspontokat, és használhatja a hibakeresőt, ha szüneteltetve van a kódban.

Ha módosítja az *index. html fájlt*, ne felejtse el újra a lapot a böngészőben megjelenő módosítások megtekintéséhez. A VS Code-ban a hibakeresés **> a hibakeresés újraindítása** vagy a CTRL + SHIFT + F5 billentyűkombinációt is használhatja.

### <a name="add-the-blob-storage-client-library"></a>A blob Storage ügyféloldali kódtár hozzáadása

A blob Storage API meghívásának engedélyezéséhez először [töltse le a JavaScript-blob ügyféloldali kódtár Azure Storage SDK-](https://aka.ms/downloadazurestoragejsblob)ját, bontsa ki a zip tartalmát, majd helyezze el a *Azure-Storage-blob. js* fájlt az *Azure-Blobs-JavaScript* mappába.

Ezután illessze be a következő HTML-kódot az *index. html* fájlba a `</body>` záró címke után, és cserélje le a helyőrző megjegyzését.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ez a kód egy hivatkozást ad hozzá a parancsfájlhoz, és helyet biztosít a saját JavaScript-kódjához. Ebben a rövid útmutatóban a *Azure-Storage-blob. js* parancsfájlt használjuk, hogy megnyissa a vs Code-ban, olvassa be a tartalmát, és állítsa be a töréspontokat. Éles környezetben a zip-fájlban is megadott Compact *Azure-Storage. blob. min. js* fájlt kell használnia.

Az egyes blob Storage-függvényekről a [dokumentációban](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index)talál további információt. Vegye figyelembe, hogy az SDK egyes funkciói csak a Node. js-ben vagy csak a böngészőben érhetők el.

A *Azure-Storage-blob. js* fájlban található kód egy `azblob`nevű globális változót exportál, amelyet a JavaScript-kódban fog használni a blob Storage API-k eléréséhez.

### <a name="add-the-initial-javascript-code"></a>A kezdeti JavaScript-kód hozzáadása

Ezután illessze be a következő kódot az előző kódrészletben látható `<script>` elembe, és cserélje le a helyőrző megjegyzését.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Ez a kód mezőket hoz létre minden olyan HTML-elemhez, amelyet a következő kód fog használni, és végrehajt egy `reportStatus` függvényt a kimenet megjelenítéséhez.

A következő részekben adja hozzá a JavaScript-kód minden új blokkját az előző blokk után.

### <a name="add-your-storage-account-info"></a>A Storage-fiók adatainak hozzáadása

Ezután vegyen fel kódot a Storage-fiók eléréséhez, és cserélje le a helyőrzőket a fiók nevére és az előző lépésben létrehozott SAS-ra.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Ez a kód a fiókadatok és az SAS használatával hozza létre a [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) -példányt, ami hasznos lehet a tárolók létrehozásához és kezeléséhez.

### <a name="create-and-delete-a-storage-container"></a>Tároló létrehozása és törlése

Ezután adja hozzá a Storage-tároló létrehozásához és törléséhez szükséges kódot a megfelelő gomb megnyomásakor.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Ez a kód meghívja a ContainerURL [create](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#create-aborter--icontainercreateoptions-) és [delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#delete-aborter--icontainerdeletemethodoptions-) függvényt a [megszakítási](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) példányok használata nélkül. Ha a rövid útmutatóhoz egyszerűen szeretné megtartani a dolgokat, ez a kód feltételezi, hogy a Storage-fiók létrejött, és engedélyezve van. Az éles kódban a megszakító példány használatával adja hozzá az időtúllépési funkciót.

### <a name="list-blobs"></a>Blobok listázása

Ezután adjon hozzá egy kódot a tároló tartalmának listázásához a **fájlok listázása** gomb megnyomásakor.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Ez a kód meghívja a [ContainerURL. listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL?view=azure-node-preview#listblobflatsegment-aborter--undefined---string--icontainerlistblobssegmentoptions-) függvényt egy hurokban, így biztosítva az összes szegmens lekérését. Minden szegmensnél hurkot vesz fel a benne található blob-elemek listáján, és frissíti a **fájlok** listáját.

### <a name="upload-blobs"></a>Blobok feltöltése

Ezután adja hozzá a fájlokat a Storage-tárolóba a **fájlok kiválasztása és feltöltése** gomb megnyomásakor.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Ez a kód csatlakoztatja a **fájlok kiválasztása és feltöltése** gombot a rejtett `file-input` elemhez. Így a gomb `click` esemény elindítja a fájl bemeneti `click` eseményét, és megjeleníti a fájl-választót. Miután kiválasztotta a fájlokat, és bezárta a párbeszédpanelt, a `input` esemény bekövetkezik, és a rendszer meghívja a `uploadFiles` függvényt. Ez a függvény meghívja a csak böngészőalapú [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/#uploadbrowserdatatoblockblob-aborter--blob---arraybuffer---arraybufferview--blockbloburl--iuploadtoblockbloboptions-) függvényt minden kiválasztott fájlhoz. Minden hívás egy ígéretet ad vissza, amely egy listához kerül, így egyszerre csak egyszer lehet várni, így a fájlok párhuzamosan tölthetők fel.

### <a name="delete-blobs"></a>Blobok törlése

Ezután adjon hozzá kódot a fájlok törléséhez a tárolóból a **kijelölt fájlok törlése** gomb megnyomásakor.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Ez a kód meghívja a [BlobURL. Delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) függvényt, hogy eltávolítsa a listában kijelölt összes fájlt. Ezután meghívja a korábban bemutatott `listFiles` függvényt a **fájlok** listájának tartalmának frissítéséhez.

### <a name="run-and-test-the-web-application"></a>A webalkalmazás futtatása és tesztelése

Ezen a ponton elindíthatja a lapot, és kísérletet tehet a blob Storage működésének megismerésére. Ha bármilyen hiba történik (például ha a tároló létrehozása előtt próbál meg fájlokat listázni), az **állapot** ablaktáblán megjelenik a kapott hibaüzenet. A JavaScript-kódban töréspontokat is beállíthat a tárolási API-k által visszaadott értékek vizsgálatához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során létrehozott erőforrások törléséhez nyissa meg a [Azure Portal](https://portal.azure.com) , és törölje az előfeltételek szakaszban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű webhelyet, amely hozzáfér a blob Storage-hoz a böngésző alapú JavaScriptből. Ha szeretné megtudni, hogyan üzemeltetheti a webhelyeket a blob Storage szolgáltatásban, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Statikus webhely üzemeltetése Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
