---
title: Oktatóanyag – Magas rendelkezésre állású alkalmazás létrehozása a Blob storage segítségével
titleSuffix: Azure Storage
description: Az olvasási hozzáférésű georedundáns tárolás sal magas szintű elérhetővé teheti az alkalmazásadatokat.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0eabd918b5f8f52049792ceb28ef8055945d6475
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77162174"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Oktatóanyag: Magas rendelkezésre állású alkalmazás létrehozása a Blob storage segítségével

Ez az oktatóanyag egy sorozat első része. Ebben megtudhatja, hogyan teheti elérhetővé az alkalmazásadatokat az Azure-ban.

Miután befejezte ezt az oktatóanyagot, lesz egy konzolalkalmazás, amely feltölti és lekéri a blobot egy [olvasási hozzáférésű georedundáns](../common/storage-redundancy.md) (RA-GRS) tárfiókból.

Az RA-GRS úgy működik, hogy tranzakciókat replikál egy elsődleges régióból egy másodlagos régióba. A replikációs folyamat garantálja a másodlagos régió adatainak végső konzisztenciáját. Az alkalmazás az [áramkör-megszakító](/azure/architecture/patterns/circuit-breaker) minta segítségével határozza meg, hogy melyik végponthoz csatlakozzon, automatikusan váltva a végpontok között, mivel a hibák és a helyreállítások szimulálva vannak.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Create a storage account
> * A kapcsolati sztring beállítása
> * A konzolalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

# <a name="net"></a>[.NET](#tab/dotnet)

* Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/downloads/) az **Azure fejlesztési** munkaterhelésével.

  ![Azure-fejlesztés (a Web és felhőszolgáltatások alatt)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python"></a>[Python](#tab/python)

* [Python](https://www.python.org/downloads/) telepítése
* A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) letöltése és telepítése

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Telepítse [a Node.js programot.](https://nodejs.org)

---

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-storage-account"></a>Create a storage account

A tárfiók egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és eléréséhez.

Kövesse az alábbi lépéseket egy írásvédett georedundáns tárfiók létrehozásához:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza a **Tárolás** lehetőséget az **Új** lapon.
3. Válassza **a Tárfiók - blob, fájl, tábla, várólista** **a Kiemelt**csoportban lehetőséget.
4. Töltse ki a tárfiók űrlapját a következő adatokkal az alábbi képen látható módon, és kattintson a **Létrehozás** elemre:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Név** | mystorageaccount | A tárfiók egyedi neve |
   | **Üzemi modell** | Resource Manager  | A Resource Manager a legújabb funkciókat kínálja.|
   | **Számla fajta** | StorageV2 | A fiókok típusaival kapcsolatos információkért lásd [a tárfiókok típusait](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Teljesítmény** | Standard | A példaforgatókönyvhöz a standard teljesítmény elegendő. |
   | **Replikáció**| Írásvédett georedundáns tárolás (RA-GRS) | Ez szükséges a minta működéséhez. |
   |**Előfizetés** | az Ön előfizetése |Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.azure.com/Subscriptions) ismertető cikket. |
   |**Erőforráscsoport** | myResourceGroup |Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
   |**Helyen** | USA keleti régiója | Válassza ki a helyet. |

![tárfiók létrehozása](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>A minta letöltése

# <a name="net"></a>[.NET](#tab/dotnet)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy konzolalkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python"></a>[Python](#tab/python)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Python-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[Töltse le a mintaprojektet,](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) és csomagolja ki a fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt egy alapvető Node.js alkalmazást tartalmaz.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>A minta konfigurálása

# <a name="net"></a>[.NET](#tab/dotnet)

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Ezt a kapcsolati sztringet tárolhatja egy környezeti változóban az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Futtassa az alábbi parancsok egyikét \<az operációs\> rendszer alapján, és cserélje le a kapcsolati karakterláncot a tényleges kapcsolati karakterláncra. A parancs egy környezeti változót ment a helyi számítógépen. A Windows rendszerben a környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort** vagy a rendszerhéjat.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python"></a>[Python](#tab/python)

Az alkalmazásban meg kell adnia a tárfiók hitelesítő adatait. Ezeket az információkat az alkalmazást futtató helyi számítógépen környezeti változókban tárolhatja. Kövesse az alábbi példák egyikét az operációs rendszertől függően a környezeti változók létrehozásához.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Illessze be a **Tárfiók nevét** és **a kulcsértékeket** a következő parancsokba, lecserélve a fióknevét \<\> és \<a fiókkulcs\> helyőrzőit. Ez a parancs a környezeti változókat a helyi számítógépre menti. A Windows rendszerben a környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort** vagy a rendszerhéjat.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához hozzá kell adnia `.env.example` a tárfiók hitelesítő `.env`adatait a fájlhoz, majd át kell neveznie a rendszerre.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Ezeket az információkat az Azure Portalon találhatja meg, ha a tárfiókra navigál, és a **Beállítások** szakaszban kiválasztja az **Access-kulcsokat.**

Telepítse a szükséges függőségeket. Ehhez nyisson meg egy parancssort, keresse meg `npm install`a mintamappát, majd írja be a következőt:

---

## <a name="run-the-console-application"></a>A konzolalkalmazás futtatása

# <a name="net"></a>[.NET](#tab/dotnet)

A Visual Studio alkalmazásban nyomja le **az F5 billentyűt,** vagy válassza az **Indítás** gombot az alkalmazás hibakeresésének megkezdéséhez. A Visual Studio automatikusan visszaállítja a hiányzó NuGet csomagokat, ha konfigurálva van, további információért látogasson el [a Csomagok telepítése és újratelepítése csomag-visszaállítással](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) című témakörbe.

Megnyílik a konzolablak, és az alkalmazás futni kezd. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást **p** vagy **S**képvisel. Ahol **P** az elsődleges végpontot, **az S** pedig a másodlagos végpontot jelöli.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `Program.cs` fájlban található `RunCircuitBreakerAsync` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="python"></a>[Python](#tab/python)

Az alkalmazás terminálon vagy parancssorban való futtatásához lépjen a **circuitbreaker.py** könyvtárra, majd írja be a `python circuitbreaker.py` parancsot. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást **p** vagy **S**képvisel. Ahol **P** az elsődleges végpontot, **az S** pedig a másodlagos végpontot jelöli.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `circuitbreaker.py` fájlban található `run_circuit_breaker` metódussal letölt egy képet a tárfiókból a [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) metódus segítségével.

A Storage-objektum újrapróbálkozási függvénye lineáris újrapróbálkozási szabályzatra van beállítva. Az újrapróbálkozási függvény határozza meg, hogy egy kérelmet újra kell-e próbálni, valamint megadja, hogy hány másodpercnyi várakozás után történjen az újrapróbálkozás. A **retry\_to\_secondary** paramétert állítsa true (igaz) értékre, ha a kérelmet a másodlagos végponton kell újra megkísérelni, amennyiben az elsődleges végpontra irányuló első kérelem sikertelen lenne. A mintaalkalmazásban az egyéni újrapróbálkozási szabályzat a Storage-objektum `retry_callback` függvényében van definiálva.

A letöltés előtt a Szolgáltatás objektum [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) és [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) függvény definiálva van. Ezek a függvények határozzák meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához nyisson meg egy parancssort, `node index.js`keresse meg a mintamappát, majd írja be a parancsot.

A minta létrehoz egy tárolót a Blob storage-fiókban, feltölti a **HelloWorld.png-t** a tárolóba, majd ismételten ellenőrzi, hogy a tároló és a rendszerkép replikálódott-e a másodlagos régióba. A replikáció után a letöltéshez vagy a kilépéshez a **D** vagy **Q** (majd az ENTER) értéket kéri. A kimenetnek a következő példához hasonlóan kell kinéznie:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

### <a name="net"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `OperationContextRetrying` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétere `SecondaryOnly` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `OperationContextRequestCompleted` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétert `PrimaryThenSecondary` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="python"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `retry_callback` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétere `SECONDARY` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `response_callback` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétert `PRIMARY` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

A Node.js V10 SDK,visszahívási kezelők szükségtelen. Ehelyett a minta létrehoz egy folyamatot, amely újrapróbálkozási beállításokkal és egy másodlagos végponttal van konfigurálva. Ez lehetővé teszi, hogy az alkalmazás automatikusan átváltson a másodlagos folyamatra, ha nem éri el az adatokat az elsődleges folyamaton keresztül.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>További lépések

A sorozat első részében megtanulta, hogy egy alkalmazás magas rendelkezésre állású az RA-GRS tárfiókok.

Folytassa a sorozat második részével, ha szeretné megismerni, hogyan szimulálhat hibákat és kényszerítheti az alkalmazást, hogy a másodlagos RA-GRS-végpontot használja.

> [!div class="nextstepaction"]
> [Hiba szimulálása az elsődleges területről történő olvasásban](storage-simulate-failure-ragrs-account-app.md)
