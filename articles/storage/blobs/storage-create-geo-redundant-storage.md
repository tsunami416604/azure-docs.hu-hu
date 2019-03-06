---
title: 'Oktatóanyag: A Blob storage - Azure Storage magas rendelkezésre állású alkalmazás létrehozása'
description: Írásvédett georedundáns tárolás használata az alkalmazásadatok magas rendelkezésre állásának biztosításához
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: ad6c5f8537fd96e835731a4604ae7c10ba62e350
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434862"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Oktatóanyag: A Blob storage magas rendelkezésre állású alkalmazás létrehozása

Ez az oktatóanyag egy sorozat első része. A, elsajátíthatja az alkalmazásadatok magas rendelkezésre állásúvá az Azure-ban.

Ez az oktatóanyag befejezése után, hogy egy konzolalkalmazást, amely feltölti és beolvassa a blobot egy [olvasási hozzáférésű georedundáns](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS) tárfiók.

RA-GRS működik, tranzakciók replikál egy elsődleges régióról egy másodlagos régióba. A replikációs folyamat garantálja a másodlagos régió adatainak végső konzisztenciáját. Az alkalmazás használja a [áramkör-megszakító](/azure/architecture/patterns/circuit-breaker) mintával határozza meg, melyik végponthoz csatlakozik, automatikusan kódhibáiként végpontok közötti váltáskor és helyreállítása simulated vannak.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * A kapcsolati sztring beállítása
> * A konzolalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
  - **Azure-fejlesztés**

  ![Azure-fejlesztés (a Web és felhőszolgáltatások alatt)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Telepítse a [Pythont](https://www.python.org/downloads/).
* A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) letöltése és telepítése

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

* A [Maven](http://maven.apache.org/download.cgi) telepítése, és konfigurálása a parancssorból való működésre
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) telepítése és konfigurálása

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

* Telepítés [Node.js](https://nodejs.org).

---

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Storage-fiók tárolhatja és érheti el az Azure Storage-adatobjektumok egy egyedi névteret biztosít.

Kövesse az alábbi lépéseket egy írásvédett georedundáns tárfiók létrehozásához:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza ki **tárolási** származó a **új** lapot.
3. Válassza ki **tárfiók – blob, fájl, tábla, üzenetsor** alatt **kiemelt**.
4. Töltse ki a tárfiók űrlapjának a következő információkat az alábbi képen látható módon, és válassza ki **létrehozás**:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Előfizetés** | az Ön előfizetése |Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup |Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Tárfiók neve** | mystorageaccount | A tárfiók egyedi neve |
   | **Hely** | USA keleti régiója | Válassza ki a helyet. |
   | **Teljesítmény** | Standard | A példaforgatókönyvhöz a standard teljesítmény elegendő. |
   | **Fióktípus** | StorageV2 | A fiókok típusaival kapcsolatos információkért lásd [a tárfiókok típusait](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikáció**| Írásvédett georedundáns tárolás (RA-GRS) | Ez szükséges a minta működéséhez. |
   | **Hozzáférési szint** | Gyakori | Gyakori elérésű elegendő a példaforgatókönyvhöz a. |

![tárfiók létrehozása](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>A minta letöltése

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy konzolalkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Python-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs), és bontsa ki a storage-java-ragrs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Java-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) , és bontsa ki a fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Node.js-alkalmazás.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>A minta konfigurálásához

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Ezt a kapcsolati sztringet tárolhatja egy környezeti változóban az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Az operációs rendszer alapján a következő parancsok egyikét futtatja cseréje \<yourconnectionstring\> tényleges kapcsolati karakterláncra. A parancs egy környezeti változót ment a helyi számítógépen. A Windows, a környezeti változó nem érhető el addig, amíg újból betölti a **parancssor** vagy rendszerhéj használ.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```PowerShell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alkalmazásban meg kell adnia a tárfiók hitelesítő adatait. Ezeket az adatokat tárolhatja a környezeti változókat az alkalmazást futtató helyi gépen. Kövesse az alábbi példák a környezeti változók létrehozásához az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Illessze be a **tárfióknevet** és **kulcs** értékeket cserélheti le a következő parancsokat, és cserélje le a \<youraccountname\> és \<youraccountkey\>helyőrzőket. Ez a parancs a környezeti változók menti a helyi gépen. A Windows, a környezeti változó nem érhető el addig, amíg újból betölti a **parancssor** vagy rendszerhéj használ.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```
### <a name="windows"></a>Windows

```PowerShell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

Ez a minta szükséges, hogy biztonságosan tárolja a nevét és a tárfiók kulcsára. A környezeti változókat a géphez, amely a minta futtatása lesz helyi Store őket. A környezeti változók létrehozásához használja a Linux- vagy a Windows például az operációs rendszerének megfelelően. 

### <a name="linux-example"></a>Linuxos példa

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Windowsos példa

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

Annak ezeket az adatokat az Azure Portalon lépjen a tárfiókhoz, majd válasszon **hozzáférési kulcsok** a a **beállítások** szakaszban. 

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához, hozzá kell adnia a tárfiók hitelesítő adatait, a `.env.example` fájlt, és nevezze át `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Annak ezeket az adatokat az Azure Portalon lépjen a tárfiókhoz, majd válasszon **hozzáférési kulcsok** a a **beállítások** szakaszban. 

Emellett telepítenie kell a szükséges függőségeknek. Ehhez nyisson meg egy parancssort, lépjen a mappába, majd adja meg `npm install`.

---

## <a name="run-the-console-application"></a>A konzolalkalmazás futtatása

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

A Visual Studióban nyomja le az ENTER **F5** , vagy válasszon **Start** az alkalmazás hibakeresésének indításához. A Visual studio automatikusan helyreállítja a hiányzó NuGet-csomagok, ha konfigurálva van, látogassa meg [telepítése és újratelepítése csomag-visszaállítással csomagok](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) további.

Megnyílik a konzolablak, és az alkalmazás futni kezd. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden egyes olvasást képviseli egy **P** vagy egy **S**. ahol a **P** az elsődleges végpontot, az **S** a másodlagos végpontot jelenti.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `Program.cs` fájlban található `RunCircuitBreakerAsync` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alkalmazás terminálon vagy parancssorban való futtatásához lépjen a **circuitbreaker.py** könyvtárra, majd írja be a `python circuitbreaker.py` parancsot. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden egyes olvasást képviseli egy **P** vagy egy **S**. ahol a **P** az elsődleges végpontot, az **S** a másodlagos végpontot jelenti.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `circuitbreaker.py` fájlban található `run_circuit_breaker` metódussal letölt egy képet a tárfiókból a [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) metódus segítségével. 

A Storage-objektum újrapróbálkozási függvénye lineáris újrapróbálkozási szabályzatra van beállítva. Az újrapróbálkozási függvény határozza meg, hogy egy kérelmet újra kell-e próbálni, valamint megadja, hogy hány másodpercnyi várakozás után történjen az újrapróbálkozás. A **retry\_to\_secondary** paramétert állítsa true (igaz) értékre, ha a kérelmet a másodlagos végponton kell újra megkísérelni, amennyiben az elsődleges végpontra irányuló első kérelem sikertelen lenne. A mintaalkalmazásban az egyéni újrapróbálkozási szabályzat a Storage-objektum `retry_callback` függvényében van definiálva.

A letöltés előtt meg kell határozni a Service objektum [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) és [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) függvényét. Ezek a függvények határozzák meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.  

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

A minta futtatásához a Maven használata a parancssorban.

1. Nyisson meg egy rendszerhéjat, és keresse meg a **storage-blobs-java-v10-tutorial-master** belül a klónozott könyvtárra.
2. Írja be a `mvn compile exec:java` (igen) kifejezést.

Ez a minta egy tesztfájlt hoz létre az alapértelmezett címtárban, a windows-felhasználók ebben a könyvtárban **AppData\Local\Temp**. A minta ezután jeleníti meg a parancsok, amelyeket megadhat a következő beállításokat:

- Adja meg **P** egy put blob művelet végrehajtásához a feltölt egy ideiglenes fájlt a tárfiókhoz.
- Adja meg **L** blob list művelet végrehajtása, a lista a jelenleg a tárolóban lévő blobokat.
- Adja meg **G** get blob művelet végrehajtására, ez letölti egy fájlt a tárfiókból a helyi gépen.
- Adja meg **D** blob delete műveletet végrehajtani, ez a művelet törli a blobot a tárfiókból.
- Adja meg **E** gombra kattintva zárja be a mintát, ez is összes erőforrást törli a létrehozott minta.

A következő példa az alkalmazás Windows rendszeren végzett futtatásakor látható kimenetet mutatja.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

A mintát a felhasználó vezérli, így parancsokat kell megadnia a kód futtatásához. Bemenet-és nagybetűk.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához nyisson meg egy parancssort, lépjen a mappába, majd adja meg `node index.js`.

A minta létrehoz egy tárolót a Blob storage-fiókban, feltölti **HelloWorld.png** a tárolóba, majd ismételten ellenőrzi, hogy a tároló és a kép replikálása a másodlagos régióba. Replikációt követően a rendszer kérni, hogy adja meg **D** vagy **Q** (ENTER követ) letöltéséhez, vagy a kilépéshez. A kimenet a következő példához hasonlóan kell kinéznie:

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

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `OperationContextRetrying` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) paramétere `SecondaryOnly` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.
 
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

A rendszer akkor hívja meg az `OperationContextRequestCompleted` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) paramétert `PrimaryThenSecondary` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

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

# <a name="pythontabpython"></a>[Python](#tab/python) 

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `retry_callback` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétere `SECONDARY` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
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

# <a name="java-v10-sdktabjava-v10"></a>[Java V10 SDK](#tab/java-v10)

A Java SDK V10-kell visszahívás-kezelőket nem szükséges, és az SDK-val most már rendelkezik néhány alapvető különbség a 7-es verziója SDK-ból. Helyett LocationMode, van egy másodlagos **folyamat**. Egy másodlagos folyamatot adhat meg a **RequestRetryOptions** és, ha definiálva van, lehetővé teszi az alkalmazás automatikusan átvált a másodlagos folyamat nem az adatok elsődleges keresztül éri el.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A Node.js SDK V10-visszahívás-kezelőket nem szükségesek. Ehelyett a minta létrehoz egy folyamatot, az újrapróbálkozási beállításokat és a egy másodlagos végpont konfigurálva. Ez lehetővé teszi az alkalmazás automatikusan átvált a másodlagos folyamat nem az adatok elsődleges keresztül éri el. 

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

A sorozat első részében megismerkedett biztosítható az alkalmazások magas rendelkezésre állású az RA-GRS-tárfiókok.

Folytassa a sorozat második részével, ha szeretné megismerni, hogyan szimulálhat hibákat és kényszerítheti az alkalmazást, hogy a másodlagos RA-GRS-végpontot használja.

> [!div class="nextstepaction"]
> [Az elsődleges tárolóvégpont kapcsolati hibájának szimulálása](storage-simulate-failure-ragrs-account-app.md)
