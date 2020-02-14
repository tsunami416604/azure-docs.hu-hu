---
title: Event Hubs – folyamatos átviteli események rögzítése Azure Portal használatával
description: Ez a cikk ismerteti, hogyan lehet engedélyezni az Azure Portalon keresztül az Azure Event Hubs streamelési események rögzítése.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187405"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Az Azure Event Hubson keresztül továbbított események rögzítésének engedélyezése

Az Azure [Event Hubs Capture][capture-overview] szolgáltatással automatikusan továbbíthatja az adatfolyam-továbbítási adatait a Event Hubs egy [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) -ba, vagy a kívánt [Azure Data Lake Storage Gen1 vagy Gen 2](https://azure.microsoft.com/services/data-lake-store/) fiókba.

A Capture-t konfigurálhatja az eseményközpont létrehozásakor az [Azure-portálon](https://portal.azure.com). Az Azure [blob Storage](https://azure.microsoft.com/services/storage/blobs/) -tárolóba vagy egy [Azure Data Lake Storage Gen 1 vagy Gen 2](https://azure.microsoft.com/services/data-lake-store/) fiókba rögzítheti az adatmennyiséget.

További információt a [Event Hubs rögzítésének áttekintése][capture-overview]című témakörben talál.

## <a name="capture-data-to-azure-storage"></a>Az Azure Storage-ba való adatrögzítés

Eseményközpont létrehozásakor a **Be** gombra kattintva engedélyezheti a Capture-t az **Eseményközpont létrehozása** portál képernyőjén. Ezután a **Capture-szolgáltató** mező **Azure Storage** gombjára kattintva adhatja meg a Storage-fiókot és -tárolót. Mivel az Event Hubs Capture szolgáltatások közötti hitelesítést használ a tárolóval, nem kell megadnia egy tárfiók kapcsolati sztringjét. Az erőforrás-választó automatikusan kiválasztja az erőforrás URI-azonosítóját a tárfiókhoz. Az Azure Resource Manager használatakor explicit módon meg kell adnia ezt az URI-t sztringként.

Az időkeret alapértelmezett értéke 5 perc. A minimális értéke 1, a maximális 15. A **Méret** ablak 10–500 MB tartománnyal rendelkezik.

![Ez alatt az időszak a rögzítéshez][1]

> [!NOTE]
> Engedélyezheti vagy letilthatja az üres fájlok kibocsátását, ha a rögzítési időszak során nem történik esemény. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Az adatrögzítés Azure Data Lake Storage 2. gen-be 

1. Az Azure Storage-fiók létrehozásához kövesse [a Storage-fiók létrehozása](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) című cikket. A **speciális** lapon állítsa be a **hierarchikus névteret** **úgy,** hogy egy Azure Data Lake Storage 2. generációs fiókot hozzon létre.
2. Az Event hub létrehozásakor hajtsa végre a következő lépéseket: 

    1. A **rögzítéshez**válassza **a** be lehetőséget. 
    2. Válassza az **Azure Storage** lehetőséget a rögzítési szolgáltatóként. A **rögzítési szolgáltatónál** megjelenő **Azure Data Lake Storei** beállítás a Azure Data Lake Storage Gen 1. Azure Data Lake Storage 2. generációs használatának kiválasztásához válassza az **Azure Storage**lehetőséget.
    2. Kattintson a **tároló kiválasztása** gombra. 

        ![Rögzítés engedélyezése a 2. generációs Data Lake Storage](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Válassza ki a **Azure Data Lake Storage Gen 2** fiókot a listából. 

    ![2\. generációs Data Lake Storage kiválasztása](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Válassza ki a **tárolót** (a fájlrendszert Data Lake Storage Gen 2).

    ![Válassza ki a fájlrendszert a tárolóban.](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Az **Event hub létrehozása** lapon válassza a **Létrehozás**lehetőséget. 

    ![Létrehozás gomb kiválasztása](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > A Azure Data Lake Storage Gen **2-ben** létrehozott tároló a következő felhasználói felülettel (UI) jelenik meg **Storage Explorerban**. Hasonlóképpen, a Data Lake Storage Gen 2 fiókban létrehozott fájlrendszer a felhasználói felületen tárolóként jelenik meg. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Az Azure Data Lake Storage 1. generációs adatrögzítés 

Ha az 1. generációs Azure Data Lake Storageba kívánja az adatrögzítést, hozzon létre egy Data Lake Storage Gen 1 fiókot és egy Event hubot:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>1\. generációs Azure Data Lake Storage fiók és mappák létrehozása

1. Hozzon létre egy Data Lake Storage fiókot, kövesse az első [lépések a Azure Data Lake Storage Gen 1 használatával a Azure Portal segítségével](../data-lake-store/data-lake-store-get-started-portal.md)című témakör útmutatását.
2. Kövesse az [engedélyek Kiosztása Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) szakasz utasításait, és hozzon létre egy mappát a Data Lake Storage Gen 1 fiókban, amelyben az adatok rögzítését szeretné a Event Hubsból, és rendeljen engedélyeket Event Hubs, hogy az adatok a Data Lake Storage Gen 1 fiókba is írhatók legyenek.  


### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Az Event hub-nak ugyanahhoz az Azure-előfizetéshez kell tartoznia, mint a létrehozott Azure Data Lake Storage 1. generációs fióknak. Eseményközpont létrehozásához kattintson a **Be** gombra a **Capture** területen az **Eseményközpont létrehozása** portál oldalán. 
2. Az **Eseményközpont létrehozása** portál oldalán válassza az **Azure Data Lake Store** lehetőséget a **Capture-szolgáltató** mezőben.
3. A **Data Lake Store** legördülő lista melletti **tároló kiválasztása** lapon adja meg a korábban létrehozott Data Lake Storage Gen 1 fiókot, és a **Data Lake elérési útja** mezőben adja meg a létrehozott adatmappa elérési útját.

    ![Válassza ki a Data Lake Storage-fiók][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>A Capture hozzáadása vagy konfigurálása egy meglévő eseményközponton

A Capture-t olyan meglévő eseményközpontokon konfigurálhatja, amelyek az Event Hubs-névterekben találhatók. A Capture meglévő eseményközponton történő engedélyezéséhez vagy a Capture beállításainak módosításához kattintson a névtérre az áttekintő képernyő betöltéséhez, majd kattintson arra az eseményközpontra, amelyen engedélyezni vagy módosítani kívánja a Capture beállításait. Végül kattintson a megnyitott oldal bal oldalán lévő **Capture** elemre, majd szerkessze a beállításokat a következő ábrák szerint:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Az Azure Blob-tároló konfigurálása][2]

### <a name="azure-data-lake-storage-gen-2"></a>2\. generációs Azure Data Lake Storage

![A 2. generációs Azure Data Lake Storage konfigurálása](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>1\. generációs Azure Data Lake Storage 

![Az Azure Data Lake Storage konfigurálása][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Következő lépések

- További információ a Event Hubs rögzítésről: [Event Hubs rögzítés áttekintése][capture-overview].
- Az Azure Resource Manager-sablonok használatával is konfigurálhatja az Event Hubs Capture-t. További információkért lásd: [Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Megtudhatja, hogyan hozhat létre Azure Event Grid-előfizetést forrásként egy Event Hubs-névtérrel.](store-captured-data-data-warehouse.md)
- [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
