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
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648797"
---
# <a name="reacting-to-blob-storage-events"></a>A blob Storage eseményeire való reagálás

Az Azure Storage eseményei lehetővé teszik az alkalmazások számára, hogy a modern kiszolgáló nélküli architektúrák használatával reagálnak az eseményekre, például a Blobok létrehozására és törlésére. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra.

Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az előfizetők, például a Azure functions, a Azure Logic apps, vagy akár a saját egyéni HTTP-figyelő számára is leküldik, és csak azért kell fizetnie, amit ténylegesen használ.

A blob Storage-események megbízhatóan érkeznek a Event Grid szolgáltatásba, amely megbízható kézbesítési szolgáltatásokat biztosít az alkalmazásai számára a részletes újrapróbálkozási szabályzatok és a kézbesítetlen levelek kézbesítése révén.

Az általános blob Storage-események közé tartozik például a képek vagy a videók feldolgozása, a keresések indexelése vagy bármilyen fájl alapú munkafolyamat. Az aszinkron fájlfeltöltés nagyszerű illeszkedést biztosít az eseményekhez. Ha a módosítások nem ritkák, de a forgatókönyvnek azonnali reagálásra van szüksége, az eseményvezérelt architektúra különösen hatékony lehet.

Ha most szeretné kipróbálni ezt a lehetőséget, tekintse meg az alábbi rövid útmutató cikkeit:

|Ha ezt az eszközt szeretné használni:    |Tekintse meg ezt a cikket: |
|--|-|
|Azure Portal    |[Rövid útmutató: BLOB Storage-események átirányítása webes végpontra a Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Rövid útmutató: Tárolási események továbbítása webes végponthoz a PowerShell használatával](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Rövid útmutató: Tárolási események továbbítása webes végponthoz az Azure CLI-vel](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Ha a fiók hierarchikus névtérrel rendelkezik, az oktatóanyag bemutatja, hogyan lehet összekapcsolni egy Event Grid-előfizetést, egy Azure-függvényt és egy [feladatot](https://docs.azuredatabricks.net/user-guide/jobs.html) a Azure Databricksban: [Oktatóanyag Databricks-különbözeti tábla](data-lake-storage-events.md)frissítéséhez Azure Data Lake Storage Gen2 eseményeket használjon.

## <a name="the-event-model"></a>Az esemény modellje

A Event Grid [esemény](../../event-grid/concepts.md#event-subscriptions) -előfizetések használatával irányítja az üzeneteket az előfizetőknek. Ez a rendszerkép az esemény-közzétevők, az esemény-előfizetések és az eseménykezelők közötti kapcsolatot mutatja be.

![Event Grid modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Először fizessen elő egy végpontot egy eseményre. Ezt követően az esemény indításakor a Event Grid szolgáltatás elküldi az eseményre vonatkozó információkat a végpontnak.

A megtekintéshez tekintse meg a [blob Storage-események sémáját](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető cikket:

> [!div class="checklist"]
> * A blob Storage-események teljes listája és az egyes események aktiválásának módja.
> * Egy példa arra, amelyet a Event Grid az egyes eseményekhez küld.
> * Az egyes kulcs-érték párok célja, amely megjelenik az adatban.

## <a name="filtering-events"></a>Események szűrése

A blob esemény-előfizetések az esemény típusa és a létrehozott vagy törölt objektum tároló neve és blob neve alapján szűrhetők.  A szűrők az esemény-előfizetés [létrehozásakor](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) vagy [egy későbbi időpontban](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)is alkalmazhatók az események előfizetésére. A Event Grid a "kezdete" és a "végződik" kifejezéssel ellátott tulajdonosi szűrők a megfelelő tulajdonossal rendelkező eseményeket továbbítják az előfizetőnek.

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
> * Mivel az üzenetek nem érkeznek meg a sorrendbe, és némi késés után a ETAG mezőkből megtudhatja, hogy az objektumokkal kapcsolatos információk továbbra is naprakészek-e.  Emellett a Sequencer mezőket is használhatja az események sorrendjének megismeréséhez egy adott objektumra vonatkozóan.
> * A blobType mező segítségével megtudhatja, hogy milyen típusú műveletek engedélyezettek a blobon, és hogy melyik ügyféloldali függvénytár-típust kell használnia a blob eléréséhez. Az `BlockBlob` érvényes értékek vagy vagy `PageBlob`. 
> * A blob eléréséhez használja a `CloudBlockBlob` és `CloudAppendBlob` a konstruktorok URL-mezőjét.
> * Figyelmen kívül hagyhatja a nem értelmezhető mezőket. Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
> * Ha biztosítani szeretné, hogy a **Microsoft. Storage. BlobCreated** esemény csak akkor legyen aktiválva, ha egy blokk blobja teljesen véglegesítve van, akkor szűrje `CopyBlob`a `PutBlob`, `PutBlockList` vagy `FlushWithClose` a REST API hívások eseményeit. Ezek az API-hívások csak azt követően indítják el a **Microsoft. Storage. BlobCreated** eseményt, hogy az adatgyűjtés teljes mértékben véglegesítve lett egy blokk blobban. A szűrők létrehozásával kapcsolatos további információkért lásd: [Event Grid események szűrése](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>További lépések

További információ a Event Gridről és a blob Storage-események kiosztásáról:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [BLOB Storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
