---
title: Reagálás az Azure Blob storage-események |} A Microsoft Docs
description: Az Azure Event Griddel előfizethet Blob Storage-eseményekre.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444682"
---
# <a name="reacting-to-blob-storage-events"></a>Reagálás Blob storage-események

Az Azure Storage-események lehetővé teszik az alkalmazások reagálhat rájuk, létrehozását és törlését a blobok, például a modern, kiszolgáló nélküli architektúra használatával. Így összetettebb kódja vagy költséges és hatékony lekérdezési szolgáltatások nélkül hajtja végre.

Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) előfizetők számára, például az Azure Functions az Azure Logic Apps, vagy akár saját egyéni http-figyelőt, és csak mit kell fizetni.

BLOB storage-események megbízhatóan kapnak az Event Grid szolgáltatás, amely az alkalmazások gazdag újrapróbálkozási szabályzatok és a kézbesíthetetlen levelek kézbesítési megbízható kézbesítést szolgáltatásokat biztosít.

Blob storage esemény gyakori forgatókönyvek kép vagy videó feldolgozása, keresési indexelő vagy minden fájl alapú munkafolyamat tartalmazza. Aszinkron fájlfeltöltéseket egy kiválóan alkalmas eseményeket a rendszer. Módosítások ritkák, de a forgatókönyvhöz szükséges azonnali válaszképességét, eseményalapú architektúrát különösen hatékony is lehet.

Ha azt szeretné, ez most már kipróbálhatja, tekintse meg az alábbi rövid útmutató cikkek valamelyikére:

|Ha szeretne ezzel az eszközzel:    |Ebben a cikkben talál: |
|--|-|
|Azure Portal    |[Rövid útmutató: Webes végpont az Azure Portallal Blob storage-események átirányítása](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Rövid útmutató: Storage-események átirányítása webes végpontra a PowerShell-lel](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Rövid útmutató: Storage-események átirányítása az Azure CLI-vel webes végpontra](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Az esemény-modell

Event Grid használ [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Ez a rendszerkép eseménykezelőket, esemény-közzétevők és esemény-előfizetések közötti kapcsolatot illusztráltuk.

![Event Grid-modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Első lépésként fizessen elő a egy végpontot egy eseményt. Ezt követően esemény kiváltásakor az Event Grid szolgáltatás adatokat küld, hogy az eseményről a végpont.

Tekintse meg a [Blob storage-események sémája](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) cikk megtekintéséhez:

> [!div class="checklist"]
> * Teljes listáját a Blob storage-események, és hogyan minden eseményt akkor indítja el.
> * Az adatokat az Event Grid egy példát az egyes ezeket az eseményeket küld el.
> * Minden kulcs-érték párt az adatokban megjelenő célját.

## <a name="filtering-events"></a>Események szűrése

Esemény-előfizetések a BLOB az esemény típusa, valamint a tároló neve és a létrehozott vagy törölt objektum blob neve alapján szűrhetők.  Szűrők alkalmazható esemény-előfizetések vagy során a [létrehozási](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) az esemény-előfizetés vagy [egy későbbi időpontban](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Az Event Grid során alapján tárgy szűrőket "kezdődik" és "vége" egyezést, így az előfizető kézbesíti az eseményeket egy megfelelő témát.

Szűrők alkalmazásával kapcsolatos további tudnivalókért lásd: [események szűréséhez állítsa be az Event Gridhez](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

A Blob storage-események tárgya a formátumot használja:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

A storage-fiókhoz tartozó összes esemény megfelel, a tárgy szűrőket üresen hagyhatja.

Események megosztása előtag tárolók készletét létrehozott blobok megfelelően, használja a `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containerprefix
```

Egyezik meg a létrehozott adott tárolóhoz blobot eseményeit, használja a `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containername/
```

Létrehozott egy blobnév előtagja megosztása adott tárolóhoz blobot események megfeleltetéséhez használja egy `subjectBeginsWith` szűrheti, például:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Egyezik meg a létrehozott blob utótag megosztása adott tárolóhoz blobot eseményeit, használja a `subjectEndsWith` például ".log" vagy "jpg" szűrőt. További információkért lásd: [Event Griddel kapcsolatos fogalmak](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Eljárások az események felhasználásához

Az alkalmazásokat, amelyek a Blob storage-események kezeléséhez kövesse kell néhány ajánlott eljárást:
> [!div class="checklist"]
> * Több előfizetés is beállíthatók úgy, hogy az azonos eseménykezelő események átirányítása, fontos, nem egy adott forrásból származó események feltételezik, de ellenőrzéséhez győződjön meg arról, hogy a tárfiók várt származnak üzenet a témakörben.
> * Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy folyamat kész, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
> * Üzenetek érkezésekor is sorrendben, és némi várakozás után, ha még mindig naprakész állapotban-e az adatok és objektumok megértése az etag-mezők használatával.  A sequencer mezőket is, használja a tudni, hogy egy adott objektumra események sorrendje.
> * A blobType mező segítségével megismerheti, hogy milyen típusú műveletek engedélyezettek a blob, és melyik ügyféloldali kódtár típusait, a blob eléréséhez használ. Érvényes értékek a következők egyikét `BlockBlob` vagy `PageBlob`. 
> * Az URL-cím mezőt használja a `CloudBlockBlob` és `CloudAppendBlob` konstruktorok hozzáférjen a blobhoz.
> * Hagyja figyelmen kívül nem ismeri a mezőket. Ez az eljárás fog megakadályozhatja, hogy rugalmas, előfordulhat, hogy a jövőben hozzáadott új funkciókhoz.
> * Ha azt szeretné, hogy a **Microsoft.Storage.BlobCreated** az esemény akkor váltódik csak akkor, ha teljesen elkötelezte magát a Blokkblobok, a vonatkozó esemény szűrése a `CopyBlob`, `PutBlob`, `PutBlockList` vagy `FlushWithClose` REST API-hívások. Ezen API-hívások eseményindító a **Microsoft.Storage.BlobCreated** esemény csak azután adatok elkötelezett a Blokkblobok. Egy szűrő létrehozásával kapcsolatban lásd: [események szűréséhez állítsa be az Event Gridhez](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>További lépések

További információ az Event Grid és a Blob storage-események próbálja:

- [Bevezetés az Event Grid használatába](../../event-grid/overview.md)
- [A Blob storage-események átirányítása egyéni webes végpontra](storage-blob-event-quickstart.md)
