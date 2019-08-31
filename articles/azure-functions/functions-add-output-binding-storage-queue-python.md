---
title: Azure Storage-várólista kötésének hozzáadása a Python-függvényhez
description: Ismerje meg, hogyan adhat hozzá egy Azure Storage-üzenetsor kimeneti kötést a Python-függvényhez az Azure CLI és a functions Core eszközök használatával.
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
ms.openlocfilehash: 9fdbf3466256c5e24de17541770fa2095fcf38a4
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171087"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Azure Storage-várólista kötésének hozzáadása a Python-függvényhez

Azure Functions lehetővé teszi az Azure-szolgáltatások és egyéb erőforrások összekapcsolását a funkciókhoz anélkül, hogy saját integrációs kódot kellene írnia. Ezeka kötések, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatait paraméterként a függvény kapja meg. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan integrálhatja az előző rövid útmutató [cikkében](functions-create-first-function-python.md) létrehozott függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A kapcsolódás egyszerűbbé tételéhez használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy nevű `AzureWebJobsStorage`alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt végezze el a [Python](functions-create-first-function-python.md)rövid útmutató 1. részében ismertetett lépéseket.

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

Az előző rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban, a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. A következő Azure functions Core Tools parancs futtatásával töltse le a beállításokat a local. Settings. `<APP_NAME>` JSON fájlra, és cserélje le a függvény alkalmazásának nevét az előző cikkből:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem kerül közzétételre, és ki kell zárni a forrás-vezérlőelemből.

Szüksége lesz az értékre `AzureWebJobsStorage`, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions szolgáltatásban a kötések mindegyike `direction`megköveteli, `type`hogy az a, `name` a, és egy egyedi legyen definiálva a function. JSON fájlban. A kötési típustól függően szükség lehet további tulajdonságokra. A [várólista kimeneti konfigurációja](functions-bindings-storage-queue.md#output---configuration) leírja az Azure Storage-várólista kötéséhez szükséges mezőket.

Kötés létrehozásához adjon hozzá egy kötési konfigurációs objektumot a function. JSON fájlhoz. Szerkessze az HttpTrigger mappában található Function. JSON fájlt, és adjon hozzá egy objektumot `bindings` a tömbhöz, amely a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Érték | Leírás |
| -------- | ----- | ----------- |
| **`name`** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **`type`** | `queue` | A kötés egy Azure Storage-várólista kötése. |
| **`direction`** | `out` | A kötés kimeneti kötés. |
| **`queueName`** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a `queueName` nem létezik, a kötés létrehozza az első használatkor. |
| **`connection`** | `AzureWebJobsStorage` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

A function. JSON fájlnak most az alábbi példához hasonlóan kell kinéznie:

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

A `name` konfigurálása után elkezdheti használni a kötést metódus attribútumként a függvény aláírásában. A következő példában `msg` a a [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)egy példánya.

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

Ha kimeneti kötést használ, nem kell használnia az Azure Storage SDK kódját a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Ahogy korábban is, használja a következő parancsot a functions futtatókörnyezet helyi elindításához:

```bash
func host start
```

> [!NOTE]  
> Mivel az előző rövid útmutatóban engedélyezte a bővítmények használatát a Host. JSON fájlban, a rendszer letölti és telepítette a [Storage kötési bővítményt](functions-bindings-storage-blob.md#packages---functions-2x) az indítás során, valamint a többi Microsoft-kötési bővítményt is.

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` ehhez az URL-címhez, és futtassa a kérést. Ugyanezt a választ kell megjelennie a böngészőben, ahogy az előző cikkben is volt.

Ebben az esetben a kimeneti kötés is létrehoz egy nevű `outqueue` várólistát a Storage-fiókban, és hozzáadja az ugyanezt a karakterláncot tartalmazó üzenetet.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizheti, hogy hozzá lett-e adva üzenet. Az üzenetsor a [Microsoft Azure Storage Explorer][Azure Storage Explorer] vagy a [Azure Portal](https://portal.azure.com)használatával is megtekinthető.

### <a name="set-the-storage-account-connection"></a>A Storage-fiók kapcsolatainak beállítása

Nyissa meg a local. Settings. JSON fájlt, és `AzureWebJobsStorage`másolja ki a értékét, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Állítsa a `AZURE_STORAGE_CONNECTION_STRING` környezeti változót a kapcsolódási karakterláncra a bash parancs használatával:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Ha a kapcsolati karakterláncot a `AZURE_STORAGE_CONNECTION_STRING` környezeti változóban állítja be, akkor anélkül férhet hozzá a Storage-fiókhoz, hogy minden alkalommal hitelesítést kellene biztosítania.

### <a name="query-the-storage-queue"></a>A tárolási várólista lekérdezése

A [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) paranccsal megtekintheti a fiókban lévő tárolási várólistákat, ahogy az alábbi példában is látható:

```azurecli-interactive
az storage queue list --output tsv
```

A parancs kimenete tartalmaz egy nevű `outqueue`várólistát, amely a függvény futtatásakor létrehozott üzenetsor.

Ezután a [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) parancs használatával tekintse meg az ebben a várólistában lévő üzeneteket, az alábbi példában látható módon:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

A visszaadott karakterláncnak meg kell egyeznie a függvény teszteléséhez küldött üzenettel.

> [!NOTE]  
> Az előző példa dekódolja a visszaadott karakterláncot a Base64-ből. Ennek az az oka, hogy a várólista-tárolási kötések az Azure Storage-ban [Base64](functions-bindings-storage-queue.md#encoding)-karakterláncként írnak és olvashatók.

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

A cURL vagy a böngésző használatával tesztelheti az üzembe helyezett függvényt. Ahogy korábban is, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-címhez, az alábbi példában látható módon:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Megvizsgálhatja [a tárolási üzenetsor üzenetét](#query-the-storage-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-triggert a függvényt, hogy az adatait egy tárolási várólistába írja. Ha többet szeretne megtudni a Python Azure Functions fejlesztéséről, tekintse meg a [Python fejlesztői útmutató Azure functions](functions-reference-python.md) és [Azure functions triggerek és kötések](functions-triggers-bindings.md)című témakört. A Python függvényekben a teljes körű függvények projektjeiről a [Python functions](/samples/browse/?products=azure-functions&languages=python)példákban olvashat. 

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/