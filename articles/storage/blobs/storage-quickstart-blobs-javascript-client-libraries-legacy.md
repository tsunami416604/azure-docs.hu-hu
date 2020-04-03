---
title: 'Rövid útmutató: Azure Blob storage for JavaScript v10 a böngészőben'
description: Ismerje meg a blobok feltöltését, listázását és törlését javascript v10 SDK használatával egy HTML-lapon.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 5219af0ec343a6e7f87a07e4a7280ac5f4e85cd3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619115"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Gyorsútmutató: Blobok kezelése JavaScript v10 SDK-val a böngészőben

Ebben a rövid útmutatóban megtudhatja, hogy a blobok teljes mértékben a böngészőben futó JavaScript-kód használatával kezelhetők. A blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatot képesek tárolni, beleértve a képeket, dokumentumokat, adatfolyam-továbbítási és archiválási adatokat. A blob storage-fiókhoz való védett hozzáférés biztosítása érdekében a szükséges biztonsági intézkedéseket fogja használni.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- Egy helyi webszerver. Ez a cikk [a Node.js programot](https://nodejs.org) használja egy alapkiszolgáló megnyitásához.
- [Visual Studio kód](https://code.visualstudio.com).
- A VS Code bővítmény a böngésző hibakereséséhez, például [a Chrome hibakeresője](vscode:extension/msjsdiag.debugger-for-chrome) vagy [a Microsoft Edge hibakeresője](vscode:extension/msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Tárfiók CORS-szabályainak beállítása

Ahhoz, hogy a webalkalmazás hozzáférhessen egy blobtárhoz az ügyfélről, konfigurálnia kell a fiókját, hogy engedélyezze [a forrásközi erőforrások megosztását](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)vagy a CORS-t.

Lépjen vissza az Azure Portalra, és válassza ki a tárfiókot. Új CORS-szabály definiálásához keresse meg a **Beállítások szakaszt,** és kattintson a **CORS** hivatkozásra. Ezután kattintson a **Hozzáadás** gombra a **CORS-szabály hozzáadása** ablak megnyitásához. Ebben a rövid útmutatóban egy nyitott CORS-szabályt hozunk létre:

![Azure Blob Storage-fiók CORS-beállításai](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

A következő tábla az egyes CORS-beállításokat írja le, és ismerteti a szabály meghatározásához használt értékeket.

|Beállítás  |Érték  | Leírás |
|---------|---------|---------|
| Engedélyezett eredetek | * | Elfogadható eredetekként beállított tartományok vesszővel tagolt listáját fogadja el. A `*` érték beállításakor minden tartomány hozzáfér a tárfiókhoz. |
| Engedélyezett módszerek     | törlés, lekérés, fej, egyesítés, közzététel, beállítások és áthelyezés | A tárfiókon futtatható HTTP-műveleteket listázza. Ebben a rövid útmutatóban válassza ki az összes elérhető beállítást. |
| Engedélyezett fejlécek | * | A tárfiókban engedélyezett kérelemfejlécek listáját határozza meg (beleértve az előtaggal ellátott fejléceket). A `*` érték beállítása minden fejléc hozzáférését engedélyezi. |
| Közzétett fejlécek | * | A fiók által engedélyezett válaszfejléceket listázza. A `*` érték beállítása esetén a fiók bármilyen fejlécet küldhet.  |
| Maximális életkor (másodperc) | 86400 | A maximális idő, ameddig a böngésző gyorsítótárazza az előzetes OPTIONS kérést. A *86400* érték lehetővé teszi, hogy a gyorsítótár egy teljes napig megmaradjon. |

> [!IMPORTANT]
> Győződjön meg arról, hogy az éles környezetben használt beállítások a tárfiókhoz a biztonságos hozzáférés fenntartásához szükséges minimális hozzáférési mennyiséget teszik elérhetővé. Az itt leírt CORS-beállítások megfelelőek ehhez a rövid útmutatóhoz, mert laza biztonsági szabályzatot határoznak meg. Nem ajánlottak azonban valós környezetekben.

Ezután az Azure Cloud Shell-lel létrehoz egy biztonsági jogkivonatot.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Megosztott hozzáférésű aláírás létrehozása

A böngészőben futó kód a közös hozzáférésű jogosultságkóddal (SAS) engedélyezteti a Blob Storage-ba érkező kéréseket. A SAS használatával az ügyfél a fiók hozzáférési kulcsa vagy kapcsolati sztringje nélkül is elvégezheti a tárolási erőforrásokhoz való hozzáférés engedélyeztetését. Az SAS-sel kapcsolatos további információkat a [közös hozzáférésű jogosultságkód (SAS) használatát ismertető](../common/storage-sas-overview.md) cikkben olvashat.

SAS-t hozhat létre az Azure CLI használatával az Azure felhőshellen keresztül, vagy az Azure Portalon vagy az Azure Storage Explorerben. Az alábbi táblázat azokat a paramétereket ismerteti, amelyekhez meg kell adnia a SAS-t a CLI-vel való létrehozásához.

| Paraméter      |Leírás  | Helyőrző |
|----------------|-------------|-------------|
| *Lejárat*       | A hozzáférési jogkivonat lejárati dátuma ÉÉÉÉ-HH-NN formátumban. Írja be a holnapi dátumot ehhez a rövid útmutatóhoz. | *FUTURE_DATE* |
| *fióknév* | A tárfiók neve. Használja a korábbi lépésben félretett nevet. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | A tárfiókkulcs. Használja a korábbi lépésben félretett kulcsot. | *YOUR_STORAGE_ACCOUNT_KEY* |

Használja a következő CLI parancsot, az egyes helyőrzők tényleges értékeivel, és hozzon létre egy SAS-t, amelyet a JavaScript-kódban használhat.

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
| *Engedélyek*    | racwdl  | Ez az SAS *olvasási*, *hozzáfűzési*, *létrehozási*, *írási*, *törlési* és *listázási* képességeket engedélyez. |
| *resource-types* | sco     | Az SAS a *szolgáltatás*, a *tároló* és az *objektum* erőforrásokra van hatással. |
| *Szolgáltatások*       | b       | Az SAS a *Blob* szolgáltatásra van hatással. |

Most, hogy a SAS létrejött, másolja a visszatérési értéket, és mentse el valahol egy közelgő lépésben való használatra. Ha a SAS-t az Azure CLI-n kívüli módszerrel hozta `?` létre, el kell távolítania a kezdeti, ha az jelen van. Ez a karakter egy URL-elválasztó, amely a témakör későbbi részében, ahol a SAS is szerepel, az URL-sablonban már szerepel.

> [!IMPORTANT]
> Éles környezetben mindig adja át a SAS-jogkivonatokat a TLS használatával. Ezenkívül az SAS-jogkivonatokat a kiszolgálón kell létrehozni, és a HTML-oldalra kell küldeni, hogy a rendszer visszaadja azokat az Azure Blob Storage-ba. Érdemes például kiszolgáló nélküli függvényt használni SAS-jogkivonatok létrehozásához. Az Azure Portal olyan függvénysablonokat tartalmaz, amelyekkel JavaScript-függvény használatával hozható létre SAS.

## <a name="implement-the-html-page"></a>A HTML-oldal implementálása

Ebben a szakaszban létrehoz egy egyszerű weblapot, és konfigurálja a VS Code-ot az oldal elindításához és hibakereséséhez. Az indítás előtt azonban a Node.js programot kell használnia egy helyi webkiszolgáló indításához, és az oldal kiszolgálásához, amikor a böngésző kéri. Ezután javascript-kódot ad hozzá a különböző blobstorage API-k hívásához és az eredmények megjelenítéséhez az oldalon. Ezek a hívások eredményeit az [Azure Portalon](https://portal.azure.com), az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)ben és a VS-kód Azure Storage [bővítményében](vscode:extension/ms-azuretools.vscode-azurestorage) is megtekintheti.

### <a name="set-up-the-web-application"></a>A webalkalmazás beállítása

Először hozzon létre egy *azure-blobs-javascript* nevű új mappát, és nyissa meg a VS-kódban. Ezután hozzon létre egy új fájlt a VS-kódban, adja hozzá a következő HTML-kódot, és mentse *index.html* néven az *azure-blobs-javascript* mappába.

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

A hibakereső bővítmény vs kódban való beállításához válassza a **Debug > Configuration...** lehetőséget, majd a **Chrome** vagy az **Edge**lehetőséget attól függően, hogy melyik bővítményt telepítette korábban az Előfeltételek szakaszban. Ez a művelet létrehoz egy *launch.json* fájlt, és megnyitja a szerkesztőben.

Ezután módosítsa a *launch.json* `url` fájlt `/index.html` úgy, hogy az érték a következőképpen jelenjen meg:

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

Ez a konfiguráció közli a VS Code-tal, hogy melyik böngészőt indítsa el, és melyik URL-t töltse be.

### <a name="launch-the-web-server"></a>A webkiszolgáló elindítása

A helyi Node.js webkiszolgáló elindításához válassza **> terminál megtekintése** lehetőséget a VS Code konzolablakának megnyitásához, majd írja be a következő parancsot.

```console
npx http-server
```

Ez a parancs telepíti a *http-server* csomagot, és elindítja a kiszolgálót, így az aktuális mappa elérhetővé válik az alapértelmezett URL-eken keresztül, beleértve az előző lépésben jelzettet is.

### <a name="start-debugging"></a>Hibakeresés indítása

Az *index.html* fájl indításához a VS Code hibakeresővel rendelkező böngészőben válassza a **Debug > Start Debugging** (V5) lehetőséget a VS Code alkalmazásban.

A megjelenő felhasználói felület még nem csinál semmit, de a következő szakaszban javaScript-kódot adhat hozzá az egyes megjelenített funkciók megvalósításához. Ezután beállíthat töréspontokat, és kommunikálhat a hibakeresővel, amikor az szünetel a kódban.

Amikor módosítja az *index.html*fájlt, ügyeljen arra, hogy újratöltse a lapot, hogy a változások a böngészőben jelenjenek meg. A VS Code alkalmazásban **kiválaszthatja a Debug > Újraindítás hibakeresés** vagy nyomja le a CTRL + SHIFT + F5 billentyűkombinációt.

### <a name="add-the-blob-storage-client-library"></a>A blobstorage-ügyféltár hozzáadása

A blob storage API-hoz való hívások engedélyezéséhez először [töltse le az Azure Storage SDK javascripthez – Blob ügyfélkönyvtárat, bontsa](https://aka.ms/downloadazurestoragejsblob)ki a zip tartalmát, és helyezze az *azure-storage-blob.js* fájlt az *azure-blobs-javascript* mappába.

Ezután illessze be a következő `</body>` HTML-kódot az *index.html* fájlba a záró címke után, és helyettesítse a helyőrző megjegyzést.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ez a kód hozzáad egy hivatkozást a parancsfájlhoz, és helyet biztosít a saját JavaScript-kódjának. Ennek a rövid útmutatónak a céljaira az *azure-storage-blob.js* parancsfájlt használjuk, hogy megnyithassa a VS Code-ban, elolvashassa annak tartalmát, és töréspontokat állíthasson be. Éles környezetben a kompaktabb *azure-storage.blob.min.js* fájlt kell használnia, amely a zip-fájlban is szerepel.

Az egyes blobtárolási függvényekről a [referenciadokumentációban olvashat bővebben.](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index) Ne feledje, hogy az SDK egyes funkciói csak a Node.js vagy a böngészőben érhetők el.

Az *azure-storage-blob.js* kódja egy `azblob`globális változót exportál, amelyet a JavaScript-kódban fog használni a blobstorage API-k eléréséhez.

### <a name="add-the-initial-javascript-code"></a>Adja hozzá a kezdeti JavaScript-kódot

Ezután illessze be `<script>` a következő kódot az előző kódblokkban látható elembe, és cserélje le a helyőrző megjegyzést.

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

Ez a kód mezőket hoz létre minden egyes HTML-elemhez, amelyet a következő kód használ, és egy `reportStatus` függvényt valósít meg a kimenet megjelenítéséhez.

A következő szakaszokban adja hozzá a JavaScript-kód minden új blokkját az előző blokk után.

### <a name="add-your-storage-account-info"></a>Tárfiók adatainak hozzáadása

Ezután adjon hozzá kódot a tárfiók eléréséhez, lecserélve a helyőrzőket a fiók nevére és az előző lépésben létrehozott SAS-re.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Ez a kód a fiókadatait és a SAS-t használja egy [ContainerURL-példány](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) létrehozásához, amely hasznos egy tárolótároló létrehozásához és kezeléséhez.

### <a name="create-and-delete-a-storage-container"></a>Tárolótároló létrehozása és törlése

Ezután adjon hozzá kódot a tárolótároló létrehozásához és törléséhez, amikor megnyomja a megfelelő gombot.

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

Ez a kód meghívja a [ContainerURL-t a](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) [megszakítási](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) példány használata nélkül létrehozott és [törölt](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) függvényeket. Ahhoz, hogy a dolgok egyszerű ek ebben a rövid útmutatóban, ez a kód feltételezi, hogy a tárfiók létrehozása és engedélyezve van. Az éles kódban a megszakításpéldány használatával adja hozzá az időtúltöltési funkciót.

### <a name="list-blobs"></a>Blobok listázása

Ezután adjon hozzá kódot a tárolótároló tartalmának listázásához, amikor megnyomja a **Fájlok listája** gombot.

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

Ez a kód meghívja a [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) függvényt egy ciklusban annak érdekében, hogy az összes szegmens lekérése meglegyen. Minden szegmensben hurkok felett blob elemek listáját tartalmaz, és frissíti a **Fájlok** listáját.

### <a name="upload-blobs"></a>Blobok feltöltése

Ezután adjon hozzá kódot a fájlok feltöltéséhez a tárolóba, amikor megnyomja a **Fájlok kiválasztása és feltöltése** gombot.

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

Ez a kód a **Fájlok kijelölése és feltöltése** gombot kapcsolja a rejtett `file-input` elemhez. Ily módon a `click` gomb esemény elindítja `click` a fájlbeviteli eseményt, és megjeleníti a fájlválasztót. A fájlok kijelölése és a `input` párbeszédpanel bezárása után `uploadFiles` az esemény bekövetkezik, és a függvény neve megtörténik. Ez a függvény minden kiválasztott fájlhoz meghívja a csak böngészőre [szolgáló uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) függvényt. Minden hívás egy ígéret, amely hozzáadódik a listához, hogy azok mind egyszerre megvárhatók legyenek, ami a fájlok párhuzamos feltöltését okozza.

### <a name="delete-blobs"></a>Blobok törlése

Ezután adjon hozzá kódot a fájlok törléséhez a tárolóból, amikor megnyomja a **Kijelölt fájlok törlése** gombot.

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

Ez a kód meghívja a [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) függvényt a listában kijelölt összes fájl eltávolításához. Ezután meghívja a `listFiles` korábban megjelenített függvényt a **Fájlok** lista tartalmának frissítéséhez.

### <a name="run-and-test-the-web-application"></a>A webalkalmazás futtatása és tesztelése

Ezen a ponton elindíthatja az oldalt, és kísérletezhet, hogy megismerhesse a blobstorage működését. Ha bármilyen hiba történik (például amikor a tároló létrehozása előtt megpróbálja listálni a fájlokat), az **Állapot** ablaktábla megjeleníti a kapott hibaüzenetet. A JavaScript-kódban töréspontokat is beállíthat a tárolási API-k által visszaadott értékek vizsgálatához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során létrehozott erőforrások karbantartásához nyissa meg az [Azure Portalon,](https://portal.azure.com) és törölje az Előfeltételek szakaszban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű webhelyet, amely böngészőalapú JavaScript-ből éri el a blobstorage-tárolót. Ha meg szeretné tudni, hogyan üzemeltethet webhelyet a blobstorage-tárhelyen, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Statikus webhely üzemeltetése a Blob Storage webhelyen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
