---
title: "Alkalmazásadatok legyen az Azure-ban magas rendelkezésre állású |} Microsoft Docs"
description: "Az alkalmazásadatok magas rendelkezésre állásúvá írásvédett georedundáns tárolás használata"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 63ca91c2eadf7b003427e9716d99621fca1b1a19
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Ellenőrizze az alkalmazás adataihoz az Azure storage magas rendelkezésre állású

Ez az oktatóanyag egy sorozat része. Az oktatóanyag bemutatja, hogyan végezheti el az alkalmazás adataihoz az Azure-ban magas rendelkezésre állású. Amikor végzett, lehetősége van a .NET core-Konzolalkalmazás, amely feltölti és egy blobot lekéri a [írásvédett georedundáns](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) storage-fiók. RA-GRS működik az elsődleges tranzakciók replikálni a másodlagos régióba. A replikációs folyamatot garantálja, hogy idővel konzisztenssé a másodlagos régióba adatai. Az alkalmazás használja a [áramköri megszakító](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) mintáját, és megállapítsa, melyik végponthoz kapcsolódni. Az alkalmazás másodlagos végponti vált, ha hiba szimulálják.

A rész az adatsorozatok megismerheti, hogyan:

> [!div class="checklist"]
> * Create a storage account
> * A minta letöltése
> * A kapcsolati karakterlánc beállítása
> * Futtassa a konzolalkalmazást

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
  - **Azure-fejlesztés**

  ![Azure fejlesztési (a webes & felhő)](media/storage-create-geo-redundant-storage/workloads.png)

* Töltse le és telepítse [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Create a storage account

A storage-fiók egy egyedi névteret tárolhatja és érheti el az Azure storage-adatobjektumok biztosít.

Kövesse az alábbi lépéseket írásvédett georedundáns tárolás-fiók létrehozása:

1. Válassza ki a **új** gomb az Azure portál bal felső sarkában található.

2. Válassza ki **tárolási** a a **új** lapon, és válassza ki **tárfiók - blob, a fájl, a tábla, a várólista** alatt **kiemelt**.
3. Töltse ki a tárolási fiók űrlapot a következő információkat, ahogy az alábbi kép, és válassza ki az **létrehozása**:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name (Név)** | mystorageaccount | A tárfiók egyedi érték |
   | **Telepítési modell** | Resource Manager  | Erőforrás-kezelő tartalmazza a legújabb funkciókat.|
   | **Fiók típusa** | Általános célú | További részletek a fiókok típusával: [storage-fiókok típusai](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Teljesítmény** | Standard | Standard elegendő-e a példa. |
   | **Replikáció**| Írásvédett georedundáns tárolás (RA-GRS) | Ez a minta működéséhez szükség. |
   |**Szükséges biztonságos átvitele** | Letiltva| Biztonságos átvitele nincs szükség ehhez a forgatókönyvhöz. |
   |**Előfizetés** | Az előfizetés |Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   |**Erőforráscsoport** | myResourceGroup |Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   |**Hely** | USA keleti régiója | A hely kiválasztásához. |

![Storage-fiók létrehozása](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>A minta letöltése

[A minta-projekt letöltése](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

Extract (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt.
A minta-projekt tartalmazza egy konzolalkalmazást.

## <a name="set-the-connection-string"></a>A kapcsolati karakterlánc beállítása

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati karakterláncot. Javasoljuk, hogy ez belül egy környezeti változó a kapcsolati karakterlánc tárolni a helyi számítógépen, amelyen az alkalmazást. Kövesse az alábbi példák attól függően, hogy a környezeti változó létrehozása az operációs rendszer egyik.

Az Azure portálon lépjen a tárfiókhoz. Válassza ki **hívóbetűk** alatt **beállítások** tárfiókba. Másolás a **kapcsolati karakterlánc** az az elsődleges vagy másodlagos kulcsot. Cserélje le \<yourconnectionstring\> a tényleges kapcsolattal karakterlánc a következő parancsok egyikét futtatja az operációs rendszer alapján. Ez a parancs a helyi számítógép egy környezeti változó menti. A Windows, a környezeti változó nincs elérhető töltse be újra a **parancssor** vagy rendszerhéj használ. Cserélje le  **\<storageConnectionString\>**  a következő mintában:

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

![alkalmazás-konfigurációs fájl](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Futtassa a konzolalkalmazást

A Visual Studio, nyomja le a **F5** , vagy válasszon **Start** az alkalmazás hibakeresését végzi el. A Visual studio automatikusan hiányzik a NuGet-csomagok visszaállítások Ha konfigurálva, látogasson el a [telepítése és újratelepítése csomag visszaállításával csomagok](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) további.

A konzolablakban elindul, és megkezdi az alkalmazás futtatása. Az alkalmazás feltöltését a **HelloWorld.png** lemezképét a megoldás a tárfiókhoz. Az alkalmazás ellenőrzi, hogy a kép biztosításához replikálása megtörtént-e a másodlagos RA-GRS-végpontot. Majd megkezdi a lemezkép letöltése legfeljebb 999 alkalommal. Minden egyes által képviselt egy **P** vagy egy **S**. Ha **P** jelöli az elsődleges végpont és **S** a másodlagos végpontot jelenti.

![Konzolalkalmazás fut](media/storage-create-geo-redundant-storage/figure3.png)

Példakód a `RunCircuitBreakerAsync` a feladat a `Program.cs` fájllal a tárolási fiók a lemezkép letöltése a [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet) metódust. A letöltés előtt egy [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) van definiálva. A műveletkörnyezetet határozza meg az esemény kezelők, a letöltés végeztével sikeresen, vagy ha a letöltés sikertelen, és újra próbálkozik.

### <a name="retry-event-handler"></a>Ismételje meg a eseménykezelő

A `OperationContextRetrying` eseménykezelő nevezzük, amikor a lemezkép letöltése sikertelen, és állítsa be, majd ismételje meg. Ha eléri a maximális számú újrapróbálkozást, az alkalmazásban definiált, a [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) a kérelem változott `SecondaryOnly`. Ez a beállítás kényszeríti az alkalmazást, a lemezkép letöltése a másodlagos végponti kísérletet. Ez a konfiguráció csökkenti a lemezképet kérik, mint az elsődleges végpont nem a rendszer ismét megkísérli határozatlan ideig szükséges idő.

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

### <a name="request-completed-event-handler"></a>A kérelem befejeződött eseménykezelő

A `OperationContextRequestCompleted` eseménykezelő nevezik, ha a lemezkép letöltése sikeres volt. Ha az alkalmazás nem használja a másodlagos végponti, az alkalmazás továbbra is használni ezt a végpontot, legfeljebb 20 alkalommal. 20 alkalommal, az alkalmazás beállítása után a [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) vissza `PrimaryThenSecondary` és az elsődleges végpont újrapróbálja a telepítést. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpont olvasni.

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

## <a name="next-steps"></a>További lépések

Az adatsorozat egyik részében megismerte és magas rendelkezésre állású tárfiókokkal RA-GRS, például az alkalmazás:

> [!div class="checklist"]
> * Create a storage account
> * A minta letöltése
> * A kapcsolati karakterlánc beállítása
> * Futtassa a konzolalkalmazást

Előzetes a sorozat Útmutató: hiba szimulálása és annak kikényszerítéséhez, az alkalmazás használata a másodlagos RA-GRS-végpont két részét.

> [!div class="nextstepaction"]
> [Az elsődleges tárolási kapcsolat hibája miatt szimulálása](storage-simulate-failure-ragrs-account-app.md)
