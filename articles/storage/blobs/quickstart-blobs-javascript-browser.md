---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – JavaScript egy böngészőben'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtárat a JavaScripthez a böngészőben. Létrehoz egy tárolót és egy objektumot a blob Storage-ban. Következő lépésként megtudhatja, hogyan listázhatja egy tároló összes blobját. Végezetül megismerheti a Blobok törlését és a tárolók törlését.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/18/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 3b23eac4ab50401e68e17064d0964dacc3c17d32
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120505"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Gyors útmutató: Blobok kezelése a JavaScript V12 SDK-val egy böngészőben

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A Blobok olyan objektumok, amelyek szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a streaming adathordozókat és az archiválási adatok. Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a JavaScript böngészőben való használatával. Fel kell töltenie és listáznia kell a blobokat, és létre kell hoznia és törölnia kell a tárolókat.

[API-referenciák dokumentációs](/javascript/api/@azure/storage-blob) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [-csomagjához (NPM)](https://www.npmjs.com/package/@azure/storage-blob) | tartozó[minták](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

> [!NOTE]
> A korábbi SDK-verzió használatának megkezdéséhez lásd [: gyors útmutató: Blobok kezelése a JavaScript v10 SDK-val a Node. js-ben](storage-quickstart-blobs-nodejs-legacy.md).

## <a name="prerequisites"></a>Előfeltételek

* [Aktív előfizetéssel rendelkező Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Azure Storage-fiók](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Microsoft Visual Studio Code](https://code.visualstudio.com)
* Egy Visual Studio Code-bővítmény a böngésző hibakereséséhez, például:
    * [Hibakereső a Microsoft Edge-hez](vscode:extension/msjsdiag.debugger-for-edge)
    * [A Chrome hibakeresője](vscode:extension/msjsdiag.debugger-for-chrome)
    * [Hibakereső a Firefoxhoz](vscode:extension/firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Objektummodell

A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

Ebben a rövid útmutatóban az alábbi JavaScript-osztályokat fogja használni a következő erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): a `ContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): a `BlockBlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet a JavaScripthez készült Azure Blob Storage ügyféloldali kódtáraval való együttműködésre.

### <a name="create-a-cors-rule"></a>CORS-szabály létrehozása

Ahhoz, hogy a webalkalmazás hozzáférhessen a blob Storage-hoz az ügyfélről, konfigurálnia kell a fiókját, hogy engedélyezze a [több eredetű erőforrás-megosztást](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)vagy a CORS.

A Azure Portal válassza ki a Storage-fiókját. Új CORS-szabály definiálásához navigáljon a **Beállítások** szakaszra, és válassza a **CORS**lehetőséget. Ebben a rövid útmutatóban egy nyitott CORS-szabályt hozunk létre:

![Azure Blob Storage-fiók CORS-beállításai](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

A következő tábla az egyes CORS-beállításokat írja le, és ismerteti a szabály meghatározásához használt értékeket.

|Beállítás  |Érték  | Leírás |
|---------|---------|---------|
| **ENGEDÉLYEZETT EREDETEK** | **\*** | Elfogadható eredetekként beállított tartományok vesszővel tagolt listáját fogadja el. A `*` érték beállításakor minden tartomány hozzáfér a tárfiókhoz. |
| **ENGEDÉLYEZETT METÓDUSOK** | **Törlés**, **beolvasás**, **fej**, **Egyesítés**, **Közzététel**, **Beállítások**és **put** | A tárfiókon futtatható HTTP-műveleteket listázza. Ebben a rövid útmutatóban válassza ki az összes elérhető beállítást. |
| **ENGEDÉLYEZETT FEJLÉCEK** | **\*** | A tárfiókban engedélyezett kérelemfejlécek listáját határozza meg (beleértve az előtaggal ellátott fejléceket). A `*` érték beállítása minden fejléc hozzáférését engedélyezi. |
| **ELÉRHETŐ FEJLÉCEK** | **\*** | A fiók által engedélyezett válaszfejléceket listázza. A `*` érték beállítása esetén a fiók bármilyen fejlécet küldhet. |
| **MAXIMÁLIS ÉLETKOR** | **86400** | Az a maximális időtartam, ameddig a böngésző gyorsítótárazza az elővizsgálati beállítások kérését másodpercben. A *86400* érték lehetővé teszi, hogy a gyorsítótár egy teljes napig megmaradjon. |

Miután kitöltötte az ebben a táblázatban szereplő értékekkel rendelkező mezőket, kattintson a **Save (Mentés** ) gombra.

> [!IMPORTANT]
> Győződjön meg arról, hogy az éles környezetben használt összes beállítás elérhetővé teszi a Storage-fiók számára a biztonságos hozzáférés fenntartásához szükséges minimális hozzáférést. Az itt leírt CORS-beállítások megfelelőek ehhez a rövid útmutatóhoz, mert laza biztonsági szabályzatot határoznak meg. Nem ajánlottak azonban valós környezetekben.

### <a name="create-a-shared-access-signature"></a>Közös hozzáférési aláírás létrehozása

A közös hozzáférésű aláírást (SAS) a böngészőben futó kód használja az Azure Blob Storage-kérelmek engedélyezéséhez. A SAS használatával az ügyfél a fiók hozzáférési kulcsa vagy kapcsolati sztringje nélkül is elvégezheti a tárolási erőforrásokhoz való hozzáférés engedélyeztetését. Az SAS-sel kapcsolatos további információkat a [közös hozzáférésű jogosultságkód (SAS) használatát ismertető](../common/storage-sas-overview.md) cikkben olvashat.

A Blob service SAS URL-címének beszerzéséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a Storage-fiókját.
2. Navigáljon a **Beállítások** szakaszra, és válassza a **közös hozzáférési aláírás**lehetőséget.
3. Görgessen lefelé, és kattintson az **sas létrehozása és a kapcsolatok sztringje** gombra.
4. Görgessen le tovább, és keresse meg a **blob Service sas URL-címe** mezőt
5. Kattintson a **Másolás a vágólapra** gombra a **blob Service sas URL-cím** mező jobb szélső oldaláról.
6. Mentse a másolt URL-címet valahol egy későbbi lépésben való használatra.

### <a name="add-the-azure-blob-storage-client-library"></a>Az Azure Blob Storage ügyféloldali kódtár hozzáadása

A helyi számítógépen hozzon létre egy új, *Azure-Blobok-js-Browser* nevű mappát, és nyissa meg a Visual Studio Code-ban.

Válassza a **> terminál megtekintése** lehetőséget, hogy a konzol ablakát a Visual Studio Code-ban nyissa meg. Futtassa a következő Node. js Package Manager (NPM) parancsot a terminál ablakban egy [Package. JSON](https://docs.npmjs.com/files/package.json) fájl létrehozásához.

```console
npm init -y
```

Az Azure SDK számos különböző csomagból áll. Kiválaszthatja, hogy mely csomagok szükségesek a használni kívánt szolgáltatások alapján. Futtassa a `npm` `@azure/storage-blob` következő parancsot a terminál ablakban a csomag telepítéséhez.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>Az Azure Blob Storage ügyféloldali kódtára

Ha az Azure SDK-kódtárat egy webhelyen szeretné használni, alakítsa át a kódot a böngészőben való működésre. Ezt egy köteg nevű eszköz használatával teheti meg. A csomagban a [Node. js](https://nodejs.org) -konvenciók használatával írt JavaScript-kód a böngészők által értelmezhető formátumba konvertálható. Ez a rövid útmutató a [csomagok](https://parceljs.org/) kötegét használja.

Telepítse a csomagot a következő `npm` parancs futtatásával a terminál ablakban:

```console
npm install -g parcel-bundler
```

A Visual Studio Code-ban nyissa meg a *Package. JSON* fájlt `browserlist` , és `license` adja `dependencies` hozzá a és a bejegyzéseket. Ez `browserlist` a három népszerű böngésző legújabb verzióját célozza meg. A teljes *Package. JSON* fájlnak most így kell kinéznie:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Mentse a *Package. JSON* fájlt.

### <a name="import-the-azure-blob-storage-client-library"></a>Az Azure Blob Storage ügyféloldali kódtár importálása

Az Azure SDK-kódtárak JavaScripten belüli használatához `@azure/storage-blob` importálja a csomagot. Hozzon létre egy új fájlt a Visual Studio Code-ban, amely tartalmazza a következő JavaScript-kódot.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Mentse a fájlt *index. js* néven az *Azure-Blob-js-Browser* könyvtárba.

### <a name="implement-the-html-page"></a>A HTML-oldal implementálása

Hozzon létre egy új fájlt a Visual Studio Code-ban, és adja hozzá a következő HTML-kódot.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Mentse a fájlt *index. html* néven az *Azure-Blobs-js-Browser* mappába.

## <a name="code-examples"></a>Kódpéldák

A példában szereplő kód bemutatja, hogyan hajthatja végre a következő feladatokat az Azure Blob Storage ügyféloldali kódtára használatával a JavaScripthez:

* [A felhasználói felületi elemek mezőinek deklarálása](#declare-fields-for-ui-elements)
* [A Storage-fiók adatainak hozzáadása](#add-your-storage-account-info)
* [Ügyfélobjektumok létrehozása](#create-client-objects)
* [Tároló létrehozása és törlése](#create-and-delete-a-storage-container)
* [Blobok listázása](#list-blobs)
* [Blobok feltöltése](#upload-blobs)
* [Blobok törlése](#delete-blobs)

A kód futtatása után az összes kódrészletet hozzáadja az *index. js* fájlhoz.

### <a name="declare-fields-for-ui-elements"></a>A felhasználói felületi elemek mezőinek deklarálása

Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Mentse az *index. js* fájlt.

Ez a kód minden HTML-elemhez deklarálja a mezőket, `reportStatus` és végrehajt egy függvényt a kimenet megjelenítéséhez.

A következő részekben adja hozzá a JavaScript-kód minden új blokkját az előző blokk után.

### <a name="add-your-storage-account-info"></a>A Storage-fiók adatainak hozzáadása

Kód hozzáadása a Storage-fiók eléréséhez. Cserélje le a helyőrzőt a korábban létrehozott Blob service SAS URL-címére. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Mentse az *index. js* fájlt.

### <a name="create-client-objects"></a>Ügyfélobjektumok létrehozása

Hozzon létre [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) és [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) objektumokat az Azure Blob Storage szolgáltatással való interakcióhoz. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Mentse az *index. js* fájlt.

### <a name="create-and-delete-a-storage-container"></a>Tároló létrehozása és törlése

Hozza létre és törölje a tárolót, ha rákattint a megfelelő gombra a weblapon. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Mentse az *index. js* fájlt.

### <a name="list-blobs"></a>Blobok listázása

A tárolási tároló tartalmának listázása a **fájlok listázása** gombra kattintva. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Mentse az *index. js* fájlt.

Ez a kód meghívja a [ContainerClient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) függvényt, majd egy iteráció használatával kéri le a visszaadott [blobelemet](/javascript/api/@azure/storage-blob/blobitem) nevét. Mindegyiknél `BlobItem`frissíti a **fájlok** listáját a [Name (név](/javascript/api/@azure/storage-blob/blobitem#name) ) tulajdonság értékével.

### <a name="upload-blobs"></a>Blobok feltöltése

Töltse fel a fájlokat a Storage-tárolóba, amikor rákattint a **fájlok kiválasztása és feltöltése** gombra. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Mentse az *index. js* fájlt.

Ez a kód csatlakoztatja a **fájlok kiválasztása és feltöltése** gombot a `file-input` rejtett elemhez. A Button `click` esemény elindítja a fájl bemeneti `click` eseményét, és megjeleníti a fájl-választót. Miután kiválasztotta a fájlokat, és bezárta a `input` párbeszédpanelt, az esemény `uploadFiles` bekövetkezik, és meghívja a függvényt. Ez a függvény létrehoz egy [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) objektumot, majd meghívja a csak böngészőalapú [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) függvényt minden kiválasztott fájlhoz. Minden hívás egy `Promise`értéket ad vissza. Mindegyiket `Promise` hozzáadjuk egy listához, hogy azok egyszerre is megtekinthetők legyenek, így a fájlok párhuzamosan lesznek feltöltve.

### <a name="delete-blobs"></a>Blobok törlése

Ha a **kijelölt fájlok törlése** gombra kattint, törölje a fájlokat a tároló-tárolóból. Adja hozzá az alábbi kódot az *index. js* fájl végéhez.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Mentse az *index. js* fájlt.

Ez a kód meghívja a [ContainerClient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) függvényt, hogy eltávolítsa a listában kijelölt összes fájlt. Ezután meghívja a `listFiles` korábban bemutatott függvényt a **fájlok** listájának tartalmának frissítéséhez.

## <a name="run-the-code"></a>A kód futtatása

A kód a Visual Studio Code debuggerben való futtatásához konfigurálja a *Launch. JSON* fájlt a böngészőjében.

### <a name="configure-the-debugger"></a>A hibakereső konfigurálása

A hibakereső bővítmény beállítása a Visual Studio Code-ban:

1. Válassza a **futtatás > konfiguráció hozzáadása** lehetőséget
2. Válassza ki a **Edge**, a **Chrome**vagy a **Firefox**elemet attól függően, hogy melyik bővítményt telepítette a korábbi [Előfeltételek](#prerequisites) szakaszban.

Az új konfiguráció hozzáadásával létrehoz egy *Launch. JSON* fájlt, és megnyithatja azt a szerkesztőben. Módosítsa a *Launch. JSON* fájlt úgy `http://localhost:1234/index.html`, hogy `url` az az alábbi módon jelenjen meg:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

A frissítés után mentse a *Launch. JSON* fájlt. Ez a konfiguráció közli a Visual Studio Code-val, hogy melyik böngésző nyílik meg, és melyik URL-címet kell betölteni.

### <a name="launch-the-web-server"></a>A webkiszolgáló elindítása

A helyi fejlesztési webkiszolgáló elindításához válassza a **megtekintés > terminál** lehetőséget, hogy a Visual Studio Code-ban nyissa meg a konzol ablakát, majd írja be a következő parancsot.

```console
parcel index.html
```

A csomag becsomagolja a kódot, és elindít egy helyi fejlesztési kiszolgálót `http://localhost:1234/index.html`az oldalára. Az *index. js* -ben végrehajtott módosításokat a rendszer automatikusan felépíti és megjeleníti a fejlesztői kiszolgálón, valahányszor menti a fájlt.

Ha olyan üzenetet kap, amely szerint a 1234-es **konfigurált portot nem lehet használni**, a parancs `parcel -p <port#> index.html`futtatásával módosíthatja a portot. A *Launch. JSON* fájlban frissítse a portot az URL-cím útvonalán a megfeleltetéshez.

### <a name="start-debugging"></a>Hibakeresés indítása

Futtassa az oldalt a hibakeresőben, és Ismerje meg, hogyan működik a blob Storage. Ha bármilyen hiba fordul elő, a weblap **állapot** ablaktáblája megjeleníti a kapott hibaüzenetet.

Ha meg szeretné nyitni az *index. html fájlt* a böngészőben a Visual Studio Code Debugger csatolt fájljában, válassza a **Futtatás > a hibakeresés elindítása** vagy az F5 billentyű lenyomása a Visual Studio Code-ban lehetőséget.

### <a name="use-the-web-app"></a>A webalkalmazás használata

A [Azure Portal](https://portal.azure.com)a következő lépésekkel ELLENŐRIZHETI az API-hívások eredményeit.

#### <a name="step-1---create-a-container"></a>1. lépés – tároló létrehozása

1. A webalkalmazásban válassza a **tároló létrehozása**lehetőséget. Az állapot azt jelzi, hogy egy tároló létrejött.
2. A Azure Portal ellenőrzéséhez válassza ki a Storage-fiókját. A **blob Service**területen válassza a **tárolók**lehetőséget. Ellenőrizze, hogy az új tároló megjelenik-e. (Lehetséges, hogy a **frissítés**lehetőséget kell választania.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>2. lépés – blob feltöltése a tárolóba

1. A helyi számítógépen hozzon létre és mentsen egy tesztoldalt, például a *test. txt*fájlt.
2. A webalkalmazásban kattintson a **kiválasztás és a fájlok feltöltése**elemre.
3. Keresse meg a teszt fájlt, majd kattintson a **Megnyitás**gombra. Az állapot azt jelzi, hogy a fájl fel lett töltve, és a rendszer beolvasta a fájlok listáját.
4. A Azure Portal válassza ki a korábban létrehozott új tároló nevét. Ellenőrizze, hogy megjelenik-e a teszt fájl.

#### <a name="step-3---delete-the-blob"></a>3. lépés – a blob törlése

1. A webalkalmazásban a **fájlok**területen válassza ki a teszt fájlt.
2. Válassza a **kijelölt fájlok törlése**lehetőséget. Az állapot azt jelzi, hogy a fájl törölve lett, és a tároló nem tartalmaz fájlokat.
3. A Azure Portal válassza a **frissítés**lehetőséget. Győződjön meg arról, hogy **nem találhatók Blobok**.

#### <a name="step-4---delete-the-container"></a>4. lépés – a tároló törlése

1. A webalkalmazásban válassza a **tároló törlése**lehetőséget. Az állapot azt jelzi, hogy a tárolót törölték.
2. A Azure Portal válassza ki a ** \<fiók nevét\> | Tárolók** hivatkozás a portál ablaktábla bal felső részén.
3. Válassza a **frissítés**lehetőséget. Az új tároló eltűnik.
4. A webalkalmazás bezárásához.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Kattintson a **terminál** konzolra a Visual Studio Code-ban, és nyomja le a CTRL + C billentyűkombinációt a webkiszolgáló leállításához.

A rövid útmutató során létrehozott erőforrások törléséhez nyissa meg a [Azure Portal](https://portal.azure.com) , és törölje az [Előfeltételek](#prerequisites) szakaszban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, listázhat és törölhet blobokat a JavaScript használatával. Azt is megtanulta, hogyan hozhat létre és törölhet egy blob Storage-tárolót.

Az oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk a következő címen találhatók:

> [!div class="nextstepaction"]
> [Azure a JavaScripthez – dokumentáció](/azure/javascript/)

* További információért lásd az [Azure Blob Storage ügyféloldali kódtárat a javascripthez](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)című témakört.
* Ha szeretné megtekinteni a blob Storage-beli példákat, folytassa az [Azure Blob Storage ügyféloldali kódtár V12 JavaScript-mintáit](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
