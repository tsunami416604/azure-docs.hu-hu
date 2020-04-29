---
title: Reagálás az Azure Blob Storage-eseményekre | Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: d9c666fd6fcf020908b6fc5bdd639261853ad9c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811543"
---
# <a name="reacting-to-blob-storage-events"></a>Reagálás Blob Storage-eseményekre

Az Azure Storage eseményei lehetővé teszik, hogy az alkalmazások reagálni tudjon az eseményekre, például a Blobok létrehozását és törlését. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra. A legjobb rész csak a ténylegesen felhasznált funkciókért kell fizetnie.

A blob Storage-események [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) használatával lesznek leküldve, például Azure Functions, Azure Logic apps vagy akár a saját http-figyelőhöz. A Event Grid megbízható esemény-kézbesítést biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek használatával.

Tekintse meg a blob Storage- [események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) című cikket a blob Storage által támogatott események teljes listájának megtekintéséhez.

Az általános blob Storage-események közé tartozik például a képek vagy a videók feldolgozása, a keresések indexelése vagy bármilyen fájl alapú munkafolyamat. Az aszinkron fájlfeltöltés nagyszerű illeszkedést biztosít az eseményekhez. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Ha a blob Storage-eseményeket szeretné kipróbálni, tekintse meg az alábbi rövid útmutatókat:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a cikket: |
|--|-|
|Azure Portal    |[Gyors útmutató: blob Storage-események átirányítása webes végpontra a Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Gyors útmutató: tárolási események továbbítása webes végponthoz a PowerShell használatával](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Gyors útmutató: tárolási események továbbítása webes végponthoz az Azure CLI-vel](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Ha az Azure functions használatával szeretné megtekinteni a blob Storage-eseményekre való reagálással kapcsolatos részletes példákat, tekintse meg a következő cikkeket:

- [Oktatóanyag: Azure Data Lake Storage Gen2-események használata egy Databricks-különbözeti tábla frissítéséhez](data-lake-storage-events.md).
- [Oktatóanyag: feltöltött képek átméretezésének automatizálása Event Grid használatával](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Csak a **StorageV2 (általános célú v2)**, a **BlockBlobStorage**és a **BlobStorage** típusú tárolási fiókok támogatják az események integrálását. A **Storage (Genral Purpose v1)** *nem* támogatja a Event Grid integrációját.

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

Egy előtaggal rendelkező tárolók készletében létrehozott Blobok eseményeinek egyeztetéséhez használjon a `subjectBeginsWith` következőhöz hasonló szűrőt:

```
/blobServices/default/containers/containerprefix
```

Ha egy adott tárolóban létrehozott Blobok eseményeinek egyeztetését `subjectBeginsWith` szeretné megfeleltetni, használjon a következőhöz hasonló szűrőt:

```
/blobServices/default/containers/containername/
```

Ha egy adott tárolóban létrehozott Blobok eseményeinek egyeztetéséhez a blob nevének előtagját `subjectBeginsWith` kell megosztania, használjon a következőhöz hasonló szűrőt:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Ha egy blob-utótagot megosztó tárolóban lévő Blobok eseményeit szeretné `subjectEndsWith` egyeztetni, használjon olyan szűrőt, mint például a ". log" vagy a ". jpg". További információ: [Event Grid fogalmak](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

A blob Storage-eseményeket kezelő alkalmazásoknak néhány ajánlott eljárást kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
> * Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
> * Mivel az üzenetek némi késés után is megérkeznek, a ETAG mezők használatával megismerheti, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e. A ETAG mező használatának megismeréséhez tekintse meg a [Egyidejűség kezelése a blob Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)című témakört. 
> * Mivel az üzenetek megérkeznek a sorrendbe, a Sequencer mezőivel megismerheti az események sorrendjét egy adott objektumra vonatkozóan. A Sequencer mező egy karakterlánc-érték, amely az események logikai sorozatot jelöli az adott blob nevénél. A standard sztringek összehasonlításával megtudhatja, hogy az adott blob nevében két esemény relatív sorozatot kell-e használni.
> * A tárolási események garantálják a legalább egyszeri kézbesítést az előfizetőknek, így biztosítva, hogy az összes üzenet le legyen erősítve. Az újrapróbálkozások vagy az előfizetések rendelkezésre állása miatt azonban időnként előfordulhat, hogy duplikált üzenetek történnek. Ha többet szeretne megtudni az üzenetek kézbesítéséről, és próbálkozzon újra, tekintse meg [Event Grid üzenet kézbesítését, és próbálkozzon újra](../../event-grid/delivery-and-retry.md).
> * A blobType mező segítségével megtudhatja, hogy milyen típusú műveletek engedélyezettek a blobon, és hogy melyik ügyféloldali függvénytár-típust kell használnia a blob eléréséhez. Az érvényes értékek `BlockBlob` vagy vagy `PageBlob`. 
> * A blob eléréséhez használja a `CloudBlockBlob` és `CloudAppendBlob` a konstruktorok URL-mezőjét.
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje `CopyBlob`a `PutBlob`, `PutBlockList` vagy `FlushWithClose` a REST API hívások eseményeit. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>További lépések

További információ a Event Gridről és a blob Storage-események kiosztásáról:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [BLOB Storage-események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB Storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
