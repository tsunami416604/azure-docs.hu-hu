---
title: Reagálás az Azure Blob-tárolási eseményekre | Microsoft dokumentumok
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: e4dd6bab6198546dc5acab78ec59d92387328dbb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755001"
---
# <a name="reacting-to-blob-storage-events"></a>Reagálás Blob Storage-eseményekre

Az Azure Storage-események lehetővé teszik, hogy az alkalmazások reagáljanak az eseményekre, például a blobok létrehozására és törlésére. Ezt anélkül teszi, hogy bonyolult kódra vagy drága és nem hatékony közvélemény-kutatásra lenne szükség. A legjobb az egészben, hogy csak azért fizetsz, amit használsz.

A Blob storage-események et az [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) használatával lelökik az előfizetőknek, például az Azure Functionsnek, az Azure Logic Apps-nek vagy akár a saját http-figyelőjének. Event Grid megbízható eseménykézbesítést biztosít az alkalmazások gazdag újrapróbálkozási szabályzatok és a holtinamenting keresztül.

Tekintse meg a [Blob storage-események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) cikket a Blob storage által támogatott események teljes listájának megtekintéséhez.

A Gyakori Blob-tárolási eseményforgatókönyvek közé tartozik a kép- vagy videofeldolgozás, a keresési indexelés vagy bármely fájlorientált munkafolyamat. Az aszinkron fájlfeltöltések kiválóan alkalmasak az eseményekre. Ha a módosítások ritkán fordulnak elő, de a forgatókönyv azonnali válaszidőt igényel, az eseményalapú architektúra különösen hatékony lehet.

Ha meg szeretné próbálni a blobstorage-eseményeket, tekintse meg az alábbi rövid útmutató cikkeket:

|Ha használni szeretné ezt az eszközt:    |Lásd ezt a cikket: |
|--|-|
|Azure Portal    |[Rövid útmutató: Blob-tárolási események irányítása a webvégpontra az Azure Portalon](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Rövid útmutató: Tárolási események irányítsa a webes végpontra a PowerShell segítségével](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Rövid útmutató: Tárolási események irányítása a webvégpontra az Azure CLI-vel](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

A Blob storage-eseményekre az Azure-függvények használatával történő reagálás részletes példáinak megtekintéséhez tekintse meg az alábbi cikkeket:

- [Oktatóanyag: Az Azure Data Lake Storage Gen2 események használatával frissítheti a Databricks Delta-táblát.](data-lake-storage-events.md)
- [Oktatóanyag: A feltöltött képek átméretezésének automatizálása az Event Grid használatával](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Csak **storageV2 típusú (általános célú v2)**, **BlockBlobStorage**és **BlobStorage** támogatási esemény integráció. **A storage (genral purpose v1)** *nem* támogatja az Event Griddel való integrációt.

## <a name="the-event-model"></a>Az eseménymodell

Az Event Grid [esemény-előfizetéseket](../../event-grid/concepts.md#event-subscriptions) használ az eseményüzenetek előfizetőknek való továbbítására. Ez a kép az eseményközzétevők, az esemény-előfizetések és az eseménykezelők közötti kapcsolatot mutatja be.

![Eseményrács-modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Előfizetni egy végpontra egy eseményre. Ezután egy esemény aktiválásakor az Event Grid szolgáltatás adatokat küld az eseményről a végpontnak.

Tekintse meg a [Blob storage-események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) cikkmegtekintéséhez:

> [!div class="checklist"]
> * A Blob storage-események teljes listája, és az egyes események aktiválásának módját.
> * Példa az eseményrács által az egyes eseményekre küldött adatokra.
> * Az adatokban megjelenő kulcsértékpárok célja.

## <a name="filtering-events"></a>Szűrési események

A [blobesemények szűrhetők](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) az esemény típusa, a tároló neve vagy a létrehozott/törölt objektum neve szerint. Az Event Grid szűrői megegyeznek a tárgy kezdetével vagy végével, így a megfelelő témával rendelkező események az előfizetőhöz kerülnek.

A szűrők alkalmazásáról az [Eseményrács eseményeinek szűrése (Eseményrács) témakörben olvashat bővebben.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

A Blob-tárolási események tárgya a következő formátumot használja:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

A tárfiók összes eseményének egyeztetéséhez üresen hagyhatja a tárgyszűrőket.

Az előtaggal közös tárolókban létrehozott blobok eseményeinek `subjectBeginsWith` egyeztetéséhez használjon a következőhöz hasonló szűrőt:

```
/blobServices/default/containers/containerprefix
```

Az adott tárolóban létrehozott blobok eseményeinek egyeztetéséhez használjon a következő höz hasonló szűrőt: `subjectBeginsWith`

```
/blobServices/default/containers/containername/
```

Ha egy blobnév-előtagot megosztó adott tárolóban létrehozott `subjectBeginsWith` blobok eseményeit szeretné egyeztetni, használjon a következőhöz hasonló szűrőt:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

A blob-utótagot megosztó, adott tárolóban létrehozott blobok `subjectEndsWith` eseményeinek egyeztetéséhez használjon egy olyan szűrőt, mint a ".log" vagy a ".jpg". További információ: [Event Grid Concepts](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Az események fogyasztásának gyakorlata

A Blob-tárolási eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot kell követniük:
> [!div class="checklist"]
> * Mivel több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa, fontos, hogy ne feltételezze, hogy az események egy adott forrásból származnak, hanem ellenőrizze az üzenet témáját, hogy megbizonyosodjon arról, hogy az a várt tárfiókból származik.
> * Hasonlóképpen ellenőrizze, hogy az eventType olyan, amelyet fel kell dolgoznia, és ne feltételezze, hogy az összes kapott esemény a várt típusú lesz.
> * Mivel az üzenetek némi késéssel érkezhetnek, az etag mezők segítségével tisztában lehet azzal, hogy az objektumokkal kapcsolatos adatai még mindig naprakészek-e. Az etag mező használatáról a [Blob storage egyidejűsítése](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)című témakörben olvashat. 
> * Mivel az üzenetek nem sorrendben érkezhetnek, használja a szekvenszer mezőket az adott objektum eseményeinek sorrendjének megértéséhez. A szekvenszer mező egy karakterlánc-érték, amely egy adott blobnév események logikai sorozatát jelöli. A szabványos karakterlánc-összehasonlítás segítségével megismerheti az azonos blobnéven lévő két esemény relatív sorrendjét.
> A tárolási események legalább egyszer garantálják az előfizetőknek történő kézbesítést, ami biztosítja az összes üzenet kimenetét. Az újrapróbálkozások vagy az előfizetések rendelkezésre állása miatt azonban időnként ismétlődő üzenetek is előfordulhatnak.
> * A blobType mező segítségével annak megértéséhez, hogy milyen típusú műveletek engedélyezettek a blobon, és milyen ügyfélkódtár-típusokat kell használnia a blob eléréséhez. Az érvényes `BlockBlob` értékek `PageBlob`vagy vagy . 
> * Használja az URL-mezőt a `CloudBlockBlob` és `CloudAppendBlob` a konstruktorok a blob eléréséhez.
> * Figyelmen kívül hagyja azolyan mezőket, amelyeket nem ért. Ez a gyakorlat segít megőrizni a rugalmas új funkciók, amelyek a jövőben hozzáadott.
> * Ha biztosítani szeretné, hogy a **Microsoft.Storage.BlobCreated** esemény csak akkor aktiválódjon, amikor a `CopyBlob` `PutBlob`blokkblob teljesen véglegesítve van, szűrje a , , `PutBlockList` vagy `FlushWithClose` REST API-hívások eseményét. Ezek az API-hívások csak akkor indítják el a **Microsoft.Storage.BlobCreated** eseményt, ha az adatok teljes mértékben véglegesítve vannak egy blokkblobban. A szűrő létrehozásáról az [Eseményrács eseményeinek szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)című témakörben olvashat.


## <a name="next-steps"></a>További lépések

További információ az Event Gridről, és a Blob-tárolási események kipróbálása:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [Blob-tárolási események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob-tárolási események útvonala egyéni webes végpontra](storage-blob-event-quickstart.md)
