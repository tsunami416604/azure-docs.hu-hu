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
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839180"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Azure Storage-várólista kötésének hozzáadása a Python-függvényhez

Azure Functions lehetővé teszi az Azure-szolgáltatások és egyéb erőforrások összekapcsolását a funkciókhoz anélkül, hogy saját integrációs kódot kellene írnia. Ezek a *kötések*, amelyek a bemeneti és a kimeneti adatokat jelölik, a függvény definíciójában vannak deklarálva. A kötések adatait paraméterként a függvény kapja meg. Az *trigger* egy speciális típusú bemeneti kötés. Bár a függvénynek csak egy triggere van, több bemeneti és kimeneti kötés is lehet. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ez a cikk bemutatja, hogyan integrálhatja az előző rövid útmutató [cikkében](functions-create-first-function-python.md) létrehozott függvényt egy Azure Storage-üzenetsor használatával. Az ehhez a függvényhez hozzáadott kimeneti kötés egy HTTP-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a függvények a kötött szolgáltatás eléréséhez használnak. A kapcsolódás egyszerűbbé tételéhez használja a Function alkalmazással létrehozott Storage-fiókot. A fiókhoz való kapcsolódás már egy `AzureWebJobsStorage` nevű alkalmazás-beállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt végezze el a [Python rövid útmutató 1. részében](functions-create-first-function-python.md)ismertetett lépéseket.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>A függvény alkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítmény-csomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions esetében minden típusú kötéshez `direction`, `type` és egy egyedi `name` szükséges, amely a function. JSON fájlban adható meg. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

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
> Mivel az előző rövid útmutatóban engedélyezte a bővítmények használatát a Host. JSON fájlban, a rendszer letölti és telepítette a [Storage kötési bővítményt](functions-bindings-storage-blob.md#packages---functions-2x) az indítás során, valamint a többi Microsoft-kötési bővítményt is.

Másolja ki a `HttpTrigger` függvény URL-címét a futtatókörnyezetének kimenetéből, majd illessze be a böngészője címsorába. Fűzze hozzá a `?name=<yourname>` lekérdezési karakterláncot ehhez az URL-címhez, és futtassa a kérelmet. Ugyanezt a választ kell megjelennie a böngészőben, ahogy az előző cikkben is volt.

Ezúttal a kimeneti kötés is létrehoz egy `outqueue` nevű várólistát a Storage-fiókban, és hozzáadja az ugyanezt a karakterláncot tartalmazó üzenetet.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizheti, hogy hozzá lett-e adva üzenet. Az üzenetsor a [Microsoft Azure Storage Explorer][Azure Storage Explorer] vagy a [Azure Portal](https://portal.azure.com)használatával is megtekinthető.

### <a name="set-the-storage-account-connection"></a>A Storage-fiók kapcsolatainak beállítása

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>A tárolási várólista lekérdezése

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

Itt az ideje, hogy újra közzé lehessen tenni a frissített Function alkalmazást az Azure-ban.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

A cURL vagy a böngésző használatával tesztelheti az üzembe helyezett függvényt. Ahogy korábban is, fűzze hozzá a `&name=<yourname>` lekérdezési karakterláncot az URL-címhez, az alábbi példában látható módon:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

[Megvizsgálhatja a tárolási üzenetsor üzenetét](#query-the-storage-queue) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új üzenetet a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-triggert a függvényt, hogy az adatait egy tárolási várólistába írja. Ha többet szeretne megtudni a Python Azure Functions fejlesztéséről, tekintse meg a [Python fejlesztői útmutató Azure functions](functions-reference-python.md) és [Azure functions triggerek és kötések](functions-triggers-bindings.md)című témakört. A Python függvényekben a teljes körű függvények projektjeiről a [Python functions](/samples/browse/?products=azure-functions&languages=python)példákban olvashat. 

Ezután engedélyezze Application Insights figyelését a Function alkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/