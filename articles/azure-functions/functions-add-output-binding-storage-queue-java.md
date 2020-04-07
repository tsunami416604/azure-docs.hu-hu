---
title: A Java-függvény csatlakoztatása az Azure Storage-hoz
description: Ismerje meg, hogyan csatlakoztathat egy HTTP-aktivált Java-függvényt az Azure Storage-hoz egy várólista-tárolási kimeneti kötés használatával.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: d9815fd27a57acc8b418962e610d2ae1c106edde
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673314"
---
# <a name="connect-your-java-function-to-azure-storage"></a>A Java-függvény csatlakoztatása az Azure Storage-hoz

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan integrálhatja az [előző rövid útmutató cikkben](functions-create-first-java-maven.md) létrehozott függvényt egy Azure Storage-várólistával. A függvényhez hozzáadott kimeneti kötés http-kérelemből adatokat ír a várólistában lévő üzenetbe.

A legtöbb kötéshez olyan tárolt kapcsolati karakterlánc szükséges, amelyet a Functions a kötött szolgáltatás eléréséhez használ. A kapcsolat megkönnyítése érdekében használja a storage-fiókot, amelya függvényalkalmazással létrehozott. A fiókhoz való kapcsolat már a neve `AzureWebJobsStorage`s. alkalmazásbeállításban van tárolva.  

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt hajtsa végre [a Java gyorsútmutató első részének lépéseit.](functions-create-first-java-maven.md)

## <a name="download-the-function-app-settings"></a>A függvényalkalmazás beállításainak letöltése

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Bővítménycsomagok engedélyezése

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Most már hozzáadhatja a Storage kimenetkötést a projekthez.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Most már készen áll, hogy helyileg próbálja ki az új kimenetkötést.

## <a name="run-the-function-locally"></a>Függvény helyi futtatása

A hogy korábban is, a következő paranccsal hozd létre a projektet, és helyileg indítsa el a Functions futásidejűt:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Mivel engedélyezte a bővítménykötegeket a host.json ban, a [Storage kötésbővítményt](functions-bindings-storage-blob.md#add-to-your-functions-app) az indítás során letöltötte és telepítette, a többi Microsoft-kötési bővítménylel együtt.

Mint korábban, indítsa el a funkciót a parancssorból a cURL használatával egy új terminálablakban:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Ezúttal a kimeneti kötés is `outqueue` létrehoz egy várólistát a storage-fiókban, és hozzáad egy üzenetet ugyanezzel a karakterlánccal.

Ezután az Azure CLI használatával megtekintheti az új várólistát, és ellenőrizze, hogy egy üzenet lett-e hozzáadva. A várólistát a Microsoft [Azure Storage Explorer][Azure Storage Explorer] vagy az [Azure Portal](https://portal.azure.com)használatával is megtekintheti.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>A projekt újratelepítése 

A közzétett alkalmazás frissítéséhez futtassa újra a következő parancsot:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Ismét használhatja a cURL-t az üzembe helyezett függvény teszteléséhez. A hogy korábban `AzureFunctions` is, adja át az értéket a POST-kérelem törzsében az URL-nek, ahogy ebben a példában is:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

A [Storage-várólista-üzenet](#query-the-storage-queue) ismételt vizsgálatával ellenőrizheti, hogy a kimeneti kötés a várt módon új üzenetet hoz-e létre a várólistában.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Frissítette a HTTP-aktivált függvényt, hogy adatokat írjon egy tárolási várólistába. Ha többet szeretne megtudni az Azure Functions Java-val való fejlesztéséről, olvassa el az [Azure Functions Java fejlesztői útmutatóját](functions-reference-java.md) és az [Azure Functions aktiválási és kötési folyamatait.](functions-triggers-bindings.md) A Java-függvényprojektek reprezentatióját a [Java Functions minták című témakörben](/samples/browse/?products=azure-functions&languages=Java)talál. 

Ezután engedélyeznie kell az Application Insights figyelését a függvényalkalmazáshoz:

> [!div class="nextstepaction"]
> [Application Insights-integráció engedélyezése](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
