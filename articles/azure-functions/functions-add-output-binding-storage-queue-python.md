---
title: Egy Azure Storage-üzenetsor kötést ad hozzá a Python-függvény
description: Ismerje meg, hogyan adhat hozzá egy Azure Storage üzenetsor kimeneti kötése a Python-függvényt az Azure CLI-vel és a Functions Core Tools használatával.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870039"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>A függvényt egy Azure Storage-üzenetsor kötés hozzáadása

Az Azure Functions lehetővé teszi a saját integrációs kód írása nélkül funkciók Azure-szolgáltatások és más erőforrásokhoz kapcsolódni. Ezek *kötések*, amelyek képviselik, bemeneti és kimeneti is deklarált belül a függvény definícióját. A függvény kötések származó adatok megadott paraméterekként. Egy trigger egy speciális típusú bemeneti kötést. Függvény csak egy eseményindító tartozik, míg képes rendelkezik több bemeneti és kimeneti kötéseit. További tudnivalókért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan integrálható a létrehozott függvény a [előző rövid útmutatóban a cikk](functions-create-first-function-python.md) az Azure Storage üzenetsorába. A kimeneti kötést, amely a függvény ad hozzá a HTTP-kérelemben szereplő adatokat ír az üzenetsorban lévő üzenetet. 

A legtöbb kötések egy tárolt kapcsolati karakterlánccal, amely függvények a kötött szolgáltatáshoz való hozzáféréshez szükséges. Könnyebben, használhatja a Storage-fiók, amely létrehozta a függvényalkalmazást. Ez a fiók a kapcsolat már tárolták a nevű beállításhoz alkalmazás `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a Kezdés előtt hajtsa végre a [, a Python a rövid útmutató 1. rész](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Töltse le a függvényalkalmazás-beállításokat

Az előző cikkben a rövid útmutatóban létrehozott egy függvényalkalmazást és egy tárfiókot az Azure-ban. Ez a fiók kapcsolati karakterláncára lesz biztonságosan tárolva alkalmazás beállításai az Azure-ban. Ebben a cikkben írt üzenetek ugyanazzal a fiókkal az üzenetsor-tárolóba. Csatlakozhat a tárfiókhoz a függvény helyi futtatás során, a beállítások töltse le a local.settings.json fájlhoz. Futtassa a következő az Azure Functions Core Tools parancsot letöltési beállítások local.Settings.JSON fájlhoz, és cserélje le `<APP_NAME>` az előző cikkben a függvényalkalmazás nevére:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy kell jelentkezzen be az Azure-fiókjával.

> [!IMPORTANT]  
> Titkos kódok tartalmaz, mert a local.settings.json fájllal soha nem lekérdezi közzétenni, és ki kell zárni a forráskezelőből.

Az érték szükséges `AzureWebJobsStorage`, azaz a Tárfiók kapcsolati sztringje. Ez a kapcsolat segítségével győződjön meg arról, hogy a kimeneti kötés megfelelően működik-e.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A függvények, minden típusú kötés van szükség, egy `direction`, `type`, és a egy egyedi `name` a function.json fájlban kell definiálni. A kötés típusától függően szükség lehet további tulajdonságok. A [üzenetsor kimeneti konfigurációs](functions-bindings-storage-queue.md#output---configuration) bemutatja egy Azure Storage-üzenetsor kötés szükséges mezőket.

Hozzon létre egy kötést, adjon hozzá egy kötelező konfigurációs objektumot a `function.json` fájlt. Szerkessze a function.json fájlt a HttpTrigger mappában való objektum hozzáadása a `bindings` tömb, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Érték | Leírás |
| -------- | ----- | ----------- |
| **`name`** | `msg` | A kötési paraméter a kódban hivatkozott azonosító név. |
| **`type`** | `queue` | A kötés egy Azure Storage-üzenetsor kötést. |
| **`direction`** | `out` | A kötés egy kimeneti kötés. |
| **`queueName`** | `outqueue` | Az üzenetsorba író a kötés neve. Ha a *queueName* nem létezik, a kötést hoz létre, azt először használ. |
| **`connection`** | `AzureWebJobsStorage` | A tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a tárfiók, függvényalkalmazás létrehozott kapcsolati karakterláncára. |

A function.json fájlban az alábbihoz hasonlóan kell kinéznie:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

Miután konfigurálta, megkezdheti a `name` a kötés hozzá a függvényaláíráshoz a metódus attribútumaként. A következő példában `msg` példánya a [ `azure.functions.InputStream class` ](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Kimeneti kötés használatával nem kell az Azure Storage SDK kódot használja a hitelesítéshez, üzenetsor-hivatkozás beszerzése vagy írásáért. A Functions futtatókörnyezete és üzenetsorának kimeneti kötést, hajtsa végre ezeket a feladatokat.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Mint korábban az alábbi parancs segítségével indítsa el a Functions futtatókörnyezete helyileg:

```bash
func host start
```

> [!NOTE]  
> Mivel az előző cikkben korábban is engedélyezheti a host.json a bővítmény csomagok a [tárolási kötési bővítmény](functions-bindings-storage-blob.md#packages---functions-2x) letöltötte és telepítette a rendszerindítás során.

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Az URL-címhez fűzze hozzá a `?name=<yourname>` lekérdezési sztringet, és hajtsa végre a kérelmet. Ahogyan az előző cikkben kell megjelennie a böngészőben ugyanazt a választ.

Ezúttal a kimeneti kötés is létrehoz egy üzenetsort, nevű `outqueue` a Storage-fiók és a egy üzenetet küld az azonos karakterláncra.

Ezután használhatja az Azure parancssori felület az új várólista megtekintéséhez, és a egy üzenet hozzáadásának ellenőrzéséhez. Az üzenetsor segítségével is megtekintheti a [Microsoft Azure Storage Explorer] [ Azure Storage Explorer] vagy a a [az Azure portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Állítsa be a Tárfiók kapcsolata

Nyissa meg a local.settings.json fájlban, és másolja az értéket a `AzureWebJobsStorage`, azaz a Tárfiók kapcsolati sztringje. Állítsa be a `AZURE_STORAGE_CONNECTION_STRING` környezeti változó a kapcsolati karakterlánc az alábbi Bash-parancs használatával:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

A kapcsolati karakterlánc beállítása az a `AZURE_STORAGE_CONNECTION_STRING` környezeti változót, hozzáférhet a tárfiókhoz anélkül, hogy a hitelesítés minden alkalommal, amikor kellene.

### <a name="query-the-storage-queue"></a>Lekérdezés a tárolási üzenetsor

Használhatja a [ `az storage queue list` ](/cli/azure/storage/queue#az-storage-queue-list) parancsot a tároló-üzenetsorok megtekintéséhez a fiókjában, az alábbi példában látható módon:

```azurecli-interactive
az storage queue list --output tsv
```

Ez a parancs kimenete tartalmazza a nevű üzenetsor `outqueue`, azaz a várólista létrehozásának, a függvény futtatásakor.

Ezután a [ `az storage message peek` ](/cli/azure/storage/message#az-storage-message-peek) parancsot az alábbi példában látható módon, az üzenetsorban lévő üzenetek megjelenítése.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Visszaadott karakterlánc lehet ugyanaz, mint az üzenet, a függvény teszteléséhez.

> [!NOTE]  
> Az előző példa a visszaadott karakterláncban a Base64 kódolású anyag dekódol. Ennek az az oka, hogy a tárolási üzenetsor kötései írása, és olvassa el az Azure Storage-ot [Base64 kódolású karakterláncok](functions-bindings-storage-queue.md#encoding).

Most, a frissített függvény alkalmazás az Azure-ban való ismételt közzétételéhez ideje.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Ismét használhatja a cURL vagy a böngésző az üzembe helyezett függvény teszteléséhez. Ahogy korábban is fűzze hozzá a lekérdezési karakterlánc `&name=<yourname>` az URL-címhez, akkor az alábbi példában látható módon:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Is [vizsgálja meg a tárolási üzenetsorbeli üzenetet](#query-the-storage-queue) ellenőrizheti a kimeneti kötés újra az üzenetsor új üzenetet állít elő.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Hogy frissítette a HTTP által aktivált függvényt az adatok írása az üzenetsor-tárolóba. Az Azure Functions Python használatával való fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure Functions Python fejlesztői útmutató](functions-reference-python.md) és [Azure Functions eseményindítók és kötések](functions-triggers-bindings.md).

Ezután engedélyeznie kell Application Insights-figyelést a függvényalkalmazás számára:

> [!div class="nextstepaction"]
> [Az Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/