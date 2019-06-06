---
title: Rövid útmutató – blob létrehozása az Azure Storage-ban a böngészőben JavaScript és HTML használatával
description: Megtudhatja, hogyan tölthet fel, listázhat és törölhet blobokat egy BlobService-példánnyal és JavaScripttel egy HTML-oldalon.
services: storage
keywords: tároló, javascript, html
author: KarlErickson
ms.custom: mvc
ms.service: storage
ms.author: karler
ms.reviewer: seguler
ms.date: 05/20/2019
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: b16bbeee299f4879c14856a8041e6517968efebf
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481382"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascripthtml-in-the-browser"></a>Gyors útmutató: Feltöltése, listázása és törlése a blobok Azure Storage v10 készült SDK-val JavaScript/HTML böngészőben

Ez a rövid útmutatóban azt ismerteti, hogyan használhatók a [Azure Storage SDK V10 JavaScript - Blob](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob#readme) szalagtár kezelheti a blobokat böngészőben futó JavaScript-kódot. Az itt bemutatott megközelítés szemlélteti a megfelelő biztonsági eszközök használatát is a Blob Storage-fiók biztonságos elérése érdekében.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Az Azure Storage JavaScript-ügyfélkódtárai nem fog működni, közvetlenül a fájlrendszerből, és egy webkiszolgáló-ről legyen szolgáltatva kell. Ez a témakör használja [Node.js](https://nodejs.org) alapszintű kiszolgáló indításához. Ha nem szeretné telepíteni a csomópontra, a más módon fut egy helyi webkiszolgálót is használhatja.

A lépések a hibakeresés, kell [Visual Studio Code](https://code.visualstudio.com) és vagy a [hibakeresőt a Chrome](vscode:extension/msjsdiag.debugger-for-chrome) vagy [hibakeresőt a Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge) bővítmény.

## <a name="setting-up-storage-account-cors-rules"></a>Tárfiók CORS-szabályainak beállítása

A webes alkalmazások férjenek hozzá egy blob storage-bA az ügyfélről, konfigurálnia kell a fiók engedélyezése [eltérő eredetű erőforrások megosztása](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services), vagy a CORS.

Lépjen vissza az Azure Portalra, és válassza ki a tárfiókot. Új CORS-szabály meghatározásához, keresse meg a **beállítások** szakaszt, és kattintson a a **CORS** hivatkozásra. Ezután kattintson a **Hozzáadás** gombra a **CORS-szabály hozzáadása** ablak megnyitásához. Ebben a rövid útmutatóban egy nyitott CORS-szabályt hozunk létre:

![Azure Blob Storage-fiók CORS-beállításai](media/storage-quickstart-blobs-javascript-client-libraries/azure-blob-storage-cors-settings.png)

A következő tábla az egyes CORS-beállításokat írja le, és ismerteti a szabály meghatározásához használt értékeket.

|Beállítás  |Value (Díj)  | Leírás |
|---------|---------|---------|
| Engedélyezett eredetek | * | Elfogadható eredetekként beállított tartományok vesszővel tagolt listáját fogadja el. A `*` érték beállításakor minden tartomány hozzáfér a tárfiókhoz. |
| Engedélyezett műveletek     | törlés, lekérés, fej, egyesítés, közzététel, beállítások és áthelyezés | A tárfiókon futtatható HTTP-műveleteket listázza. Ebben a rövid útmutatóban válassza ki az összes elérhető beállítást. |
| Engedélyezett fejlécek | * | A tárfiókban engedélyezett kérelemfejlécek listáját határozza meg (beleértve az előtaggal ellátott fejléceket). A `*` érték beállítása minden fejléc hozzáférését engedélyezi. |
| Közzétett fejlécek | * | A fiók által engedélyezett válaszfejléceket listázza. A `*` érték beállítása esetén a fiók bármilyen fejlécet küldhet.  |
| Maximális időtartam (másodperc) | 86400 | A maximális idő, ameddig a böngésző gyorsítótárazza az előzetes OPTIONS kérést. A *86400* érték lehetővé teszi, hogy a gyorsítótár egy teljes napig megmaradjon. |

> [!IMPORTANT]
> Győződjön meg arról, éles környezetben használt beállítások tegye elérhetővé a minimálisan szükséges a biztonságos hozzáférés fenntartása a tárfiókhoz való hozzáférést. Az itt leírt CORS-beállítások megfelelőek ehhez a rövid útmutatóhoz, mert laza biztonsági szabályzatot határoznak meg. Nem ajánlottak azonban valós környezetekben.

Ezután az Azure Cloud Shell-lel létrehoz egy biztonsági jogkivonatot.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Egy közös hozzáférésű jogosultságkód létrehozása

A böngészőben futó kód a közös hozzáférésű jogosultságkóddal (SAS) engedélyezteti a Blob Storage-ba érkező kéréseket. A SAS használatával az ügyfél a fiók hozzáférési kulcsa vagy kapcsolati sztringje nélkül is elvégezheti a tárolási erőforrásokhoz való hozzáférés engedélyeztetését. Az SAS-sel kapcsolatos további információkat a [közös hozzáférésű jogosultságkód (SAS) használatát ismertető](../common/storage-dotnet-shared-access-signature-part-1.md) cikkben olvashat.

Létrehozhat egy SAS-t az Azure CLI használatával az Azure cloud shellen keresztül vagy az Azure Portalon vagy az Azure Storage Explorerben. Az alábbi táblázat ismerteti a paramétereket, meg kell adnia értékeket a parancssori felülettel SAS létrehozásához.

| Paraméter      |Leírás  | Helyőrző |
|----------------|-------------|-------------|
| *expiry*       | A hozzáférési jogkivonat lejárati dátuma ÉÉÉÉ-HH-NN formátumban. Írja be a holnapi dátumot ehhez a rövid útmutatóhoz. | *FUTURE_DATE* |
| *account-name* | A tárfiók neve. Használja a korábbi lépésben félretett nevet. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | A tárfiókkulcs. Használja a korábbi lépésben félretett kulcsot. | *YOUR_STORAGE_ACCOUNT_KEY* |

Minden egyes helyőrző tényleges értékekre a következő CLI-parancs használatával hozzon létre egy SAS-t, a JavaScript-kódot is használhat.

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
| *engedélyek*    | racwdl  | Ez az SAS *olvasási*, *hozzáfűzési*, *létrehozási*, *írási*, *törlési* és *listázási* képességeket engedélyez. |
| *resource-types* | sco     | Az SAS a *szolgáltatás*, a *tároló* és az *objektum* erőforrásokra van hatással. |
| *services*       | b       | Az SAS a *Blob* szolgáltatásra van hatással. |

Most, hogy létrejött az SAS, másolja a visszaadott érték, és a egy későbbi lépésben használatra mentse valahova. Ha létrehozta az SAS, az Azure CLI eltérő módszerrel, szüksége lesz, távolítsa el a kezdeti `?` Ha jelen. Ezt a karaktert a már megtalálható a jelen témakör későbbi részében URL-cím sablon URL-CÍMÉT az elválasztó, ahol a biztonsági Társítások használt.

> [!IMPORTANT]
> Éles környezetben mindig SSL használatával adja át az SAS-jogkivonatot. Ezenkívül az SAS-jogkivonatokat a kiszolgálón kell létrehozni, és a HTML-oldalra kell küldeni, hogy a rendszer visszaadja azokat az Azure Blob Storage-ba. Érdemes például kiszolgáló nélküli függvényt használni SAS-jogkivonatok létrehozásához. Az Azure Portal olyan függvénysablonokat tartalmaz, amelyekkel JavaScript-függvény használatával hozható létre SAS.

## <a name="implement-the-html-page"></a>A HTML-oldal implementálása

Ebben a szakaszban fog létrehozni egy alapszintű weboldal és elindításához és az oldal hibakeresése a VS Code konfigurálása. Mielőtt indíthatja el, azonban szüksége indítsa el a helyi webkiszolgálót, és szolgálja ki a lapon, ha a böngésző kéri, a Node.js használatával. Ezután különböző a blob storage API-k meghívása, és az eredmények megjelennek a lap JavaScript-kódot fogja hozzáadni. Megtekintheti az eredményeket a hívások is a [az Azure portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer), és a [Azure Storage-bővítmény](vscode:extension/ms-azuretools.vscode-azurestorage) a VS Code alkalmazáshoz.

### <a name="set-up-the-web-application"></a>A webalkalmazás beállítása

Először hozzon létre egy új mappát *azure-blobs-javascript* , és megnyithatja a VS Code-ban. Ezután hozzon létre egy új fájlt a VS Code-ban, adja hozzá a következő HTML-kódot, és mentse a fájt *index.html* a a *azure-blobs-javascript* mappát.

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

A VS Code-ban a hibakeresőt bővítményt, válassza ki **Debug > konfiguráció hozzáadása...** , majd **Chrome** vagy **Edge**, attól függően, melyik az Előfeltételek szakaszban korábban telepített bővítményt. Ez a művelet létrehoz egy *launch.json* fájlt, és megnyílik a szerkesztő a.

Ezután módosítsa a *launch.json* fájlt úgy, hogy a `url` értéket tartalmaz `/index.html` látható módon:

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

Ez a konfiguráció a VS Code arra utasítja a indítása böngésző és betölteni melyik URL-CÍMÉT.

### <a name="launch-the-web-server"></a>A webkiszolgáló indításához

Válassza ki a helyi Node.js-webkiszolgáló indításához **Nézet > terminálon** , nyisson meg egy konzolablakot, a VS Code belül, majd adja meg a következő parancsot.

```console
npx http-server
```

Ez a parancs telepíti a *http-kiszolgáló* csomagot, majd indítsa el a kiszolgáló alapértelmezett URL-címeket, beleértve az előző lépésben megadott keresztül elérhetővé az aktuális mappába.

### <a name="start-debugging"></a>Hibakeresés indítása

Indítsa el a *index.html* a böngészőben a VS Code hibakereső csatolt, jelölje be a **Debug > Start Debugging** vagy az F5 billentyűt a VS Code-ban.

A felhasználói felületen megjelenített csinál, de a JavaScript-kódot fogja hozzáadni a következő szakaszban látható minden egyes függvény végrehajtásához. Ezután állítson be töréspontokat, és a hibakeresőt kommunikáljanak, ha a kódot fel van függesztve.

Ha módosítja a *index.html*, ügyeljen arra, hogy frissítse az oldalt láthatják a módosításokat a böngészőben. A VS Code-ban is választhat **hibakeresés > Hibakeresés indítsa újra a** vagy nyomja le a CTRL + SHIFT + F5 billentyűkombinációt.

### <a name="add-the-blob-storage-client-library"></a>A blob storage ügyféloldali kódtár hozzáadása

A blob Storage API-hívások engedélyezéséhez először [töltse le az Azure Storage SDK for JavaScript - klienskódtár Blob](https://aka.ms/downloadazurestoragejsblob), bontsa ki a zip-fájl tartalmát, és helyezze el a *azure-storage.blob.js* a fájlt*azure-blobs-javascript* mappát.

Ezt követően illessze be az alábbi HTML- *index.html* után a `</body>` záró címke, lecserélve a helyőrző megjegyzést.

```html
<script src="azure-storage.blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ez a kód hozzáad egy hivatkozást a parancsfájlt, és a saját JavaScript-kódot lehetőséget biztosít. Ebben a rövid útmutatóban használjuk a *azure-storage.blob.js* parancsfájlt, hogy megnyithatja a VS Code-ban olvassa el a tartalmát, és állítson be töréspontokat. Éles környezetben használjon a több CD *azure-storage.blob.min.js* fájlt a zip-fájlt is megtalálható.

További információk a minden egyes blob storage függvényt annak a [referenciadokumentációt](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Vegye figyelembe, hogy az SDK-ban a függvények csak a node.js-ben elérhető vagy csak a böngészővel érhető el.

A kód *azure-storage.blob.js* exportálja egy globális változó nevű `azblob`, fogjuk használni a JavaScript-kódban a blob storage API-k eléréséhez.

### <a name="add-the-initial-javascript-code"></a>Adja hozzá a kezdeti JavaScript-kódot

Ezt követően illessze be az alábbi kódot a `<script>` elem az előző kódblokkot, lecserélve a helyőrző megjegyzés látható.

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

Ez a kód létrehozza az egyes HTML-elem, amely a következő kódot fogja használni, és forráshálózati mezőket egy `reportStatus` függvényre kattintva jelenítse meg a kimenetet.

A következő szakaszokban hozzáadása után az előző blokk minden egyes új JavaScript kódblokkot.

### <a name="add-your-storage-account-info"></a>Adja hozzá a tárolási fiók adatai

Ezután adja hozzá a tárfiók eléréséhez, és cserélje le a helyőrzőket a fiók nevét és az előző lépésben létrehozott SAS kódot.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential)));
```

Ez a kód a fiók adatai és a SAS létrehozásához használja a [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) példányt, amely hasznos létrehozására és kezelésére szolgáló tárolót.

### <a name="create-and-delete-a-storage-container"></a>Hozzon létre vagy töröljön egy storage-tárolóba

Ezután adja hozzá a kódot hozhat létre, és törli a storage-tárolót, a megfelelő gomb megnyomásakor.

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

Ez a kód meghívja a ContainerURL [létrehozása](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#create-aborter--icontainercreateoptions-) és [törlése](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#delete-aborter--icontainerdeletemethodoptions-) függvények használata nélkül egy [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) példány. Egyszerűség ebben a rövid, ez a kód feltételezi, hogy a tárfiók létrejött és engedélyezve van. Az éles kódban Aborter példány használatával időtúllépési funkciókkal.

### <a name="list-blobs"></a>Blobok listázása

Ezután adja hozzá a kódot, hogy a tároló tartalmának listázásához, amikor lenyomja a **a fájlok listázhatók** gombra.

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

Ez a kód meghívja a [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL#listblobflatsegment-aborter--string--icontainerlistblobssegmentoptions-) egy hurkot, és győződjön meg arról, hogy a rendszer beolvassa az összes szegmensek függvényt. Minden egyes szegmens, hurkokat fölé elemlistát blobot tartalmaz, és frissíti a **fájlok** listája.

### <a name="upload-blobs"></a>Blobok feltöltése

Ezután adja hozzá a kódot a fájlok feltöltése a tárolóba, amikor lenyomja a **kiválasztása és feltöltése a fájlok** gombra.

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
fileInput.addEventListener("input", uploadFiles);
```

Ez a kód csatlakozik a **kiválasztása és feltöltése a fájlok** gombra kattintva a rejtett `file-input` elemet. Ezzel a módszerrel a gomb `click` esemény aktiválása a bemeneti fájllal `click` esemény, és megjeleníti a Fájlkereső. Miután válassza ki a fájlokat, és zárja be a párbeszédpanelt, a `input` esemény következik be, és a `uploadFiles` függvény neve. Ez a függvény meghívja a böngésző csak [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/#uploadbrowserdatatoblockblob-aborter--blob---arraybuffer---arraybufferview--blockbloburl--iuploadtoblockbloboptions-) függvény az összes kiválasztott fájl esetében. Minden hívás adnak hozzá egy listát, úgy, hogy azok is összes kell várni a egyszerre, párhuzamosan feltölteni kívánt fájlok okozó ígéret adja vissza.

### <a name="delete-blobs"></a>Blobok törlése

Ezután adja hozzá a kódot, hogy törölje a fájlokat a storage-tárolóba, amikor lenyomja a **törli a kijelölt fájlok** gombra.

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

Ez a kód meghívja a [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) függvény eltávolítása a listából kiválasztott fájlok. Ezután meghívja a `listFiles` függvény látható korábbi tartalmának frissítéséhez a **fájlok** listája.

### <a name="run-and-test-the-web-application"></a>Futtatása és a webalkalmazás tesztelése

Ezen a ponton elindíthatja a, és betekintést nyerhet abba, hogy hogyan kísérlet a blob storage működésével. Ha bármilyen hiba történik (például, ha a tároló létrehozása előtt próbál tulajdonságlista-fájlok), a **állapot** panelen a kapott hibaüzenet jelenik meg. A JavaScript-kódot vizsgálja meg a storage API-k által visszaadott értékek töréspontok is beállíthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a rövid útmutató során létrehozott erőforrások törléséhez nyissa meg a [az Azure portal](https://portal.azure.com) , és törölje az Előfeltételek szakaszban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban létrehozott egy olyan egyszerű webhely, hogy hozzáfér a blob storage-böngészőalapú JavaScript-alapú. Ismerje meg, hogyan üzemeltethet magát egy webhelyet a blob storage-ba, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [A Blob Storage statikus webhely üzemeltetése](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
