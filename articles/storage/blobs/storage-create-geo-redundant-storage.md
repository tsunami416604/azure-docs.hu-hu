---
title: Alkalmazásadatok magas rendelkezésre állásának biztosítása az Azure-ban | Microsoft Docs
description: Írásvédett georedundáns tárolás használata az alkalmazásadatok magas rendelkezésre állásának biztosításához
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: ce72c1a68c1dbe5cede33dd42adc1b002a81326e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Az alkalmazásadatok magas rendelkezésre állásának biztosítása az Azure Storage használatával

Ez az oktatóanyag egy sorozat első része, amely azt ismerteti, hogyan biztosítható az alkalmazásadatok magas rendelkezésre állása az Azure-ban. Az oktatóanyagban egy konzolalkalmazást hoz majd létre, amely egy blobot tölt fel és kér le egy [írásvédett georedundáns](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) tárfiókba. Az RA-GRS úgy működik, hogy az elsődleges régióból a másodlagosba replikálja a tranzakciókat. A replikációs folyamat garantálja a másodlagos régió adatainak végső konzisztenciáját. Az alkalmazás az [áramköri megszakító](/azure/architecture/patterns/circuit-breaker) mintával határozza meg, hogy melyik végponthoz kapcsolódjon. Az alkalmazás a másodlagos végpontra vált, ha szimulált hibát tapasztal.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Create a storage account
> * A minta letöltése
> * A kapcsolati karakterlánc beállítása
> * A konzolalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
  - **Azure-fejlesztés**

  ![Azure-fejlesztés (a Web és felhőszolgáltatások alatt)](media/storage-create-geo-redundant-storage/workloads.png)

* (Választható) A [Fiddler](https://www.telerik.com/download/fiddler) letöltése és telepítése
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* Telepítse a [Pythont](https://www.python.org/downloads/).
* A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) letöltése és telepítése
* (Választható) A [Fiddler](https://www.telerik.com/download/fiddler) letöltése és telepítése

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Create a storage account

A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez.

Kövesse az alábbi lépéseket egy írásvédett georedundáns tárfiók létrehozásához:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Az **Új** oldalon válassza a **Tároló** elemet, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőséget a **Kiemelt** területen.
3. Töltse ki a tárfiók űrlapját a következő adatokkal az alábbi képen látható módon, és kattintson a **Létrehozás** elemre:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name (Név)** | mystorageaccount | A tárfiók egyedi neve |
   | **Üzemi modell** | Resource Manager  | A Resource Manager a legújabb funkciókat kínálja.|
   | **Fióktípus** | StorageV2 | A fiókok típusaival kapcsolatos információkért lásd [a tárfiókok típusait](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Teljesítmény** | Standard | A példaforgatókönyvhöz a standard teljesítmény elegendő. |
   | **Replikáció**| Írásvédett georedundáns tárolás (RA-GRS) | Ez szükséges a minta működéséhez. |
   |**Biztonságos átvitelre van szükség** | Letiltva| A biztonságos átvitel nem szükséges ehhez a forgatókönyvhöz. |
   |**Előfizetés** | az Ön előfizetése |Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   |**ResourceGroup** | myResourceGroup |Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   |**Hely** | USA keleti régiója | Válassza ki a helyet. |

![tárfiók létrehozása](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>A minta letöltése

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy konzolalkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Python-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>A kapcsolati karakterlánc beállítása

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Javasoljuk, hogy ezt a kapcsolati karakterláncot egy környezeti változóban tárolja az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati karakterláncát**. Cserélje le a \<yourconnectionstring\> kifejezést a tényleges kapcsolati karakterláncra. Ehhez futtassa a következő parancsok közül az operációs rendszerének megfelelőt. A parancs egy környezeti változót ment a helyi számítógépen. Windows rendszerben a környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort** vagy a rendszerhéjat, amelyet használ. Cserélje le a **\<storageConnectionString\>** kifejezést a következő mintában:

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>A konzolalkalmazás futtatása

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
A Visual Studióban nyomja le az **F5** billentyűt, vagy kattintson a **Start** gombra az alkalmazás hibakeresésének indításához. A Visual Studio automatikusan helyreállítja a hiányzó NuGet-csomagokat, ha konfigurálva vannak. További információ: [Csomagok telepítése és újratelepítése csomag-visszaállítással](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Megnyílik a konzolablak, és az alkalmazás futni kezd. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden egyes olvasást egy **P** vagy egy **S** karakter jelöl, ahol a **P** az elsődleges végpontot, az **S** a másodlagos végpontot jelenti.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `Program.cs` fájlban található `RunCircuitBreakerAsync` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Az alkalmazás terminálon vagy parancssorban való futtatásához lépjen a **circuitbreaker.py** könyvtárra, majd írja be a `python circuitbreaker.py` parancsot. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden egyes olvasást egy **P** vagy egy **S** karakter jelöl, ahol a **P** az elsődleges végpontot, az **S** a másodlagos végpontot jelenti.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `circuitbreaker.py` fájlban található `run_circuit_breaker` metódussal letölt egy képet a tárfiókból a [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) metódus segítségével. 

A Storage-objektum újrapróbálkozási függvénye lineáris újrapróbálkozási szabályzatra van beállítva. Az újrapróbálkozási függvény határozza meg, hogy egy kérelmet újra kell-e próbálni, valamint megadja, hogy hány másodpercnyi várakozás után történjen az újrapróbálkozás. A **retry\_to\_secondary** paramétert állítsa true (igaz) értékre, ha a kérelmet a másodlagos végponton kell újra megkísérelni, amennyiben az elsődleges végpontra irányuló első kérelem sikertelen lenne. A mintaalkalmazásban az egyéni újrapróbálkozási szabályzat a Storage-objektum `retry_callback` függvényében van definiálva.
 
A letöltés előtt meg kell határozni a Service objektum [retry_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) és [response_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) függvényét. Ezek a függvények határozzák meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.  

---

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

A rendszer akkor hívja meg az `OperationContextRetrying` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) paramétere `SecondaryOnly` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

A mintakód a `Program.cs` fájlban található `RunCircuitBreakerAsync` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.
 
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 
A rendszer akkor hívja meg az `retry_callback` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétere `SECONDARY` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.  

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

---


### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

A rendszer akkor hívja meg az `response_callback` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétert `PRIMARY` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

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

---

## <a name="next-steps"></a>További lépések

A sorozat első részében megtudta, hogyan biztosítható az alkalmazások magas rendelkezésre állása az RA-GRS-tárfiókok használatával:

> [!div class="checklist"]
> * Create a storage account
> * A minta letöltése
> * A kapcsolati karakterlánc beállítása
> * A konzolalkalmazás futtatása

Folytassa a sorozat második részével, ha szeretné megismerni, hogyan szimulálhat hibákat és kényszerítheti az alkalmazást, hogy a másodlagos RA-GRS-végpontot használja.

> [!div class="nextstepaction"]
> [Az elsődleges tárolóvégpont kapcsolati hibájának szimulálása](storage-simulate-failure-ragrs-account-app.md)
