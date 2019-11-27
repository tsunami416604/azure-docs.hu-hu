---
title: Azure Storage-várólista kötésének hozzáadása a Python-függvényhez
description: Ismerje meg, hogyan adhat hozzá egy Azure Storage-üzenetsor kimeneti kötését a Python-függvényhez.
ms.date: 10/02/2019
ms.topic: quickstart
ms.openlocfilehash: dede135da56e9ed1eaaed2ae0f7b5cd14d08195c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231235"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Azure Storage-várólista kötésének hozzáadása a Python-függvényhez

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan integrálhatja az előző rövid útmutató [cikkében](functions-create-first-function-python.md) létrehozott függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A kapcsolódás egyszerűbbé tételéhez használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy `AzureWebJobsStorage`nevű alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt végezze el a [Python rövid útmutató 1. részében](functions-create-first-function-python.md)ismertetett lépéseket.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions szolgáltatásban minden típusú kötéshez szükség van egy `direction`, `type`ra és egy egyedi `name`ra, amelyet a function. JSON fájlban kell meghatározni. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Ha kimeneti kötést használ, nem kell használnia az Azure Storage SDK kódját a hitelesítéshez, a várólista-hivatkozás beszerzéséhez vagy az adatíráshoz. A functions futtatókörnyezet és a várólista kimeneti kötése elvégzi ezeket a feladatokat.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

Ahogy korábban is, használja a következő parancsot a functions futtatókörnyezet helyi elindításához:

```bash
func host start
```

> [!NOTE]  
> Mivel engedélyezte a bővítmények használatát a Host. JSON fájlban, a [Storage kötési bővítmény](functions-bindings-storage-blob.md#packages---functions-2x) le lett töltve és telepítve lett az indításakor, valamint a többi Microsoft-kötési bővítménysel együtt.

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Fűzze hozzá a lekérdezési karakterláncot `?name=<yourname>` ehhez az URL-címhez, és futtassa a kérést. Ugyanezt a választ kell megjelennie a böngészőben, ahogy az előző cikkben is volt.

Ezúttal a kimeneti kötés is létrehoz egy `outqueue` nevű várólistát a Storage-fiókban, és hozzáadja az ugyanezt a karakterláncot tartalmazó üzenetet.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizheti, hogy hozzá lett-e adva üzenet. Az üzenetsor a [Microsoft Azure Storage Explorer][Azure Storage Explorer] vagy a [Azure Portal](https://portal.azure.com)használatával is megtekinthető.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>A projekt újbóli üzembe helyezése 

A közzétett alkalmazás frissítéséhez használja a [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) Core Tools parancsot a projekt kódjának az Azure-ban való üzembe helyezéséhez. Ebben a példában a `<APP_NAME>` helyére írja be az alkalmazás nevét.

```command
func azure functionapp publish <APP_NAME> --build remote
```

A cURL vagy a böngésző használatával tesztelheti az üzembe helyezett függvényt. Ahogy korábban is, fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez, az alábbi példában látható módon:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

[Megvizsgálhatja a tárolási üzenetsor üzenetét](#query-the-storage-queue) annak ellenőrzéséhez, hogy a kimeneti kötés a várt módon létrehoz egy új üzenetet a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP-triggert a függvényt, hogy az adatait egy tárolási várólistába írja. Ha többet szeretne megtudni a Python Azure Functions fejlesztéséről, tekintse meg a [Python fejlesztői útmutató Azure functions](functions-reference-python.md) és [Azure functions triggerek és kötések](functions-triggers-bindings.md)című témakört. A Python függvényekben a teljes körű függvények projektjeiről a [Python functions](/samples/browse/?products=azure-functions&languages=python)példákban olvashat. A díjszabással kapcsolatos további információkért tekintse meg a [functions díjszabási oldalát](https://azure.microsoft.com/pricing/details/functions/) , valamint a [felhasználási terv költségeinek becslése](functions-consumption-costs.md) című cikket.

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/