---
title: Reagálás az Azure Blob Storage-eseményekre | Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 78ec5b6d330f03d78dcb4e798b23d588fd93398e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835963"
---
# <a name="reacting-to-blob-storage-events"></a>A blob Storage eseményeire való reagálás

Az Azure Storage eseményei lehetővé teszik, hogy az alkalmazások reagálni tudjon az eseményekre, például a Blobok létrehozását és törlését. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra.

Az eseményeket a [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az előfizetők, például a Azure functions, a Azure Logic apps, vagy akár a saját HTTP-figyelő használatával küldi el. A legjobb rész csak a ténylegesen felhasznált funkciókért kell fizetnie.

A blob Storage az eseményeket olyan Event Grid küldi, amely megbízható esemény-kézbesítést biztosít az alkalmazásai számára a részletes újrapróbálkozási házirendekkel és a kézbesítetlen levelekkel.

Az általános blob Storage-események közé tartozik például a képek vagy a videók feldolgozása, a keresések indexelése vagy bármilyen fájl alapú munkafolyamat. Az aszinkron fájlfeltöltés nagyszerű illeszkedést biztosít az eseményekhez. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Ha most szeretné kipróbálni ezt a lehetőséget, tekintse meg az alábbi rövid útmutató cikkeit:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a cikket: |
|--|-|
|Azure portál    |[Gyors útmutató: blob Storage-események átirányítása webes végpontra a Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Gyors útmutató: tárolási események továbbítása webes végponthoz a PowerShell használatával](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure parancssori felület (CLI)    |[Gyors útmutató: tárolási események továbbítása webes végponthoz az Azure CLI-vel](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Ha a fiókja hierarchikus névtérrel rendelkezik, az oktatóanyag bemutatja, hogyan lehet összekapcsolni egy Event Grid-előfizetést, egy Azure-függvényt és egy [feladatot](https://docs.azuredatabricks.net/user-guide/jobs.html) a Azure Databricksban: [oktatóanyag: a Databricks-különbözeti tábla frissítéséhez használjon Azure Data Lake Storage Gen2 eseményeket](data-lake-storage-events.md).

>[!NOTE]
> Csak a **StorageV2 (általános célú v2)** és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (Genral Purpose v1)** *nem* támogatja a Event Grid integrációját.

## <a name="the-event-model"></a>Az esemény modellje

A Event Grid [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az üzeneteket az előfizetőknek. Ez a rendszerkép az esemény-közzétevők, az esemény-előfizetések és az eseménykezelők közötti kapcsolatot mutatja be.

![Event Grid modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Először fizessen elő egy végpontot egy eseményre. Ezt követően az esemény indításakor a Event Grid szolgáltatás elküldi az eseményre vonatkozó információkat a végpontnak.

A megtekintéshez tekintse meg a [blob Storage-események sémáját](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető cikket:

> [!div class="checklist"]
> * A blob Storage-események teljes listája és az egyes események aktiválásának módja.
> * Egy példa arra, amelyet a Event Grid az egyes eseményekhez küld.
> * Az egyes kulcs-érték párok célja, amely megjelenik az adatban.

## <a name="filtering-events"></a>Események szűrése

A blob-eseményeket az esemény típusa, a tároló neve vagy a létrehozott/törölt objektum neve alapján [lehet szűrni](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) . A Event Grid lévő szűrők megegyeznek a tárgy elejével vagy végével, hogy a megfelelő tárgyú események lépjenek az előfizetőre.

A szűrők alkalmazásával kapcsolatos további információkért lásd: [Események szűrése Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

A blob Storage-események tárgya a formátumot használja:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Egy Storage-fiók összes eseményének megfeleltetéséhez üresen hagyhatja a tulajdonos szűrőit.

Egy előtaggal rendelkező tárolók készletében létrehozott Blobok eseményeinek egyeztetéséhez használjon `subjectBeginsWith` szűrőt, például:

```
/blobServices/default/containers/containerprefix
```

Egy adott tárolóban létrehozott Blobok eseményeinek megfeleltetéséhez használjon `subjectBeginsWith` szűrőt, például:

```
/blobServices/default/containers/containername/
```

Ha egy adott tárolóban létrehozott Blobok eseményeinek egyeztetéséhez a blob nevének előtagját kell megosztania, használjon egy `subjectBeginsWith` szűrőt, például:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Ha egy blob-utótagot megosztó tárolóban lévő Blobok eseményeit szeretné egyeztetni, használjon egy `subjectEndsWith` szűrőt, például: ". log" vagy ". jpg". További információ: [Event Grid fogalmak](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

A blob Storage-eseményeket kezelő alkalmazásoknak néhány ajánlott eljárást kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek nem érkeznek meg a sorrendbe, és némi késés után a ETAG mezőkből megtudhatja, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e.  Emellett a Sequencer mezőket is használhatja az események sorrendjének megismeréséhez egy adott objektumra vonatkozóan.
> * A blobType mező segítségével megtudhatja, hogy milyen típusú műveletek engedélyezettek a blobon, és hogy melyik ügyféloldali függvénytár-típust kell használnia a blob eléréséhez. Az érvényes értékek `BlockBlob` vagy `PageBlob`. 
> * A blob eléréséhez használja a `CloudBlockBlob` és `CloudAppendBlob` konstruktorok URL-mezőjét.
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje a `CopyBlob`, `PutBlob`, `PutBlockList` vagy `FlushWithClose` REST API hívásokat. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Következő lépések

További információ a Event Gridről és a blob Storage-események kiosztásáról:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [BLOB Storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
