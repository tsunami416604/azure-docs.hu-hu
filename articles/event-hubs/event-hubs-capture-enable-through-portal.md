---
title: Event Hubs – Streamelési események rögzítése az Azure Portalon
description: Ez a cikk ismerteti, hogyan engedélyezheti az események streamelését az Azure Event Hubs-on keresztül az Azure Portalhasználatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187405"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Események streamelésének engedélyezése az Azure Event Hubs-on keresztül

Az Azure [Event Hubs Capture][capture-overview] lehetővé teszi, hogy automatikusan továbbítsa a streamelési adatokat az Event Hubs-ban egy [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) vagy Azure Data Lake [Storage Gen1 vagy Gen 2](https://azure.microsoft.com/services/data-lake-store/) fiókba.

A Capture-t konfigurálhatja az eseményközpont létrehozásakor az [Azure-portálon](https://portal.azure.com). Az adatokat rögzítheti egy Azure Blob storage-tárolóba, vagy egy [Azure Data Lake Storage Gen 1 vagy Gen 2](https://azure.microsoft.com/services/data-lake-store/) fiókba. [Blob storage](https://azure.microsoft.com/services/storage/blobs/)

További információk: [Az Event Hubs Capture áttekintése][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Adatok rögzítése az Azure Storage-ba

Eseményközpont létrehozásakor a **Be** gombra kattintva engedélyezheti a Capture-t az **Eseményközpont létrehozása** portál képernyőjén. Ezután a **Capture-szolgáltató** mező **Azure Storage** gombjára kattintva adhatja meg a Storage-fiókot és -tárolót. Mivel az Event Hubs Capture szolgáltatások közötti hitelesítést használ a tárolóval, nem kell megadnia egy tárfiók kapcsolati sztringjét. Az erőforrás-választó automatikusan kiválasztja az erőforrás URI-azonosítóját a tárfiókhoz. Az Azure Resource Manager használatakor explicit módon meg kell adnia ezt az URI-t sztringként.

Az időkeret alapértelmezett értéke 5 perc. A minimális értéke 1, a maximális 15. A **Méret** ablak 10–500 MB tartománnyal rendelkezik.

![Rögzítési időablak][1]

> [!NOTE]
> Engedélyezheti vagy letilthatja az üres fájlok kibocsátását, ha a Rögzítés ablakban nem történik esemény. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Adatok rögzítése az Azure Data Lake Storage Gen 2 szolgáltatásba 

1. Azure Storage-fiók létrehozásához kövesse [a Tárfiók](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) létrehozása cikket. Állítsa **hierarchikus névtér** **engedélyezve** a **Speciális** lapon, hogy ez egy Azure Data Lake Storage Gen 2 fiók.
2. Eseményközpont létrehozásakor tegye a következő lépéseket: 

    1. Válassza **a Be** lehetőséget a **rögzítéshez.** 
    2. Válassza ki az **Azure Storage-t** a rögzítésszolgáltatóként. Az **Azure Data Lake Store** beállítás, amelyet a **rögzítési szolgáltatónál** lát, az Azure Data Lake Storage Gen 1-hez tartozik. Az Azure Data Lake Storage Gen 2 szolgáltatásának használatához válassza az **Azure Storage lehetőséget.**
    2. Válassza a **Tároló kiválasztása** gombot. 

        ![Rögzítés engedélyezése a Data Lake Storage Gen 2-re](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Válassza ki az **Azure Data Lake Storage Gen 2** fiókot a listából. 

    ![Adattó-tároló gen 2 kiválasztása](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Válassza ki a **tárolót** (fájlrendszer a Data Lake Storage Gen 2-ben).

    ![Fájlrendszer kiválasztása a tárolóban](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Az **Eseményközpont létrehozása** lapon válassza a **Létrehozás gombot.** 

    ![Válassza a Létrehozás gombot](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Az Azure Data Lake Storage Gen 2-ben ezzel a felhasználói felülettel létrehozott tároló a Storage Explorer **Fájlrendszerek** területén jelenik **meg.** Hasonlóképpen a Data Lake Storage Gen 2 fiókban létrehozott fájlrendszer tárolóként jelenik meg ebben a felhasználói felületben. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Adatok rögzítése az Azure Data Lake Storage Gen 1-re 

Az Azure Data Lake Storage Gen 1-re történő adatok rögzítéséhez hozzon létre egy Data Lake Storage Gen 1 fiókot és egy eseményközpontot:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Azure Data Lake Storage Gen 1 fiók és mappák létrehozása

1. Hozzon létre egy Data Lake Storage-fiókot az Azure Data Lake Storage Gen 1 használatának első lépései című útmutatóban [az Azure Portalon.](../data-lake-store/data-lake-store-get-started-portal.md)
2. Kövesse az Engedélyek hozzárendelése az [Eseményközpontokhoz](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) című szakaszutasításait, és hozzon létre egy mappát a Data Lake Storage Gen 1 fiókban, amelyben rögzíteni szeretné az adatokat az Event Hubs-ból, és engedélyeket szeretne rendelni az Event Hubs-hoz, hogy adatokat írhass a Data Lake Storage Gen 1 fiókba.  


### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Az eseményközpontnak ugyanabban az Azure-előfizetésben kell lennie, mint az Ön által létrehozott Azure Data Lake Storage Gen 1 fióknak. Eseményközpont létrehozásához kattintson a **Be** gombra a **Capture** területen az **Eseményközpont létrehozása** portál oldalán. 
2. Az **Eseményközpont létrehozása** portál oldalán válassza az **Azure Data Lake Store** lehetőséget a **Capture-szolgáltató** mezőben.
3. A Data Lake **Store** legördülő lista melletti **Áruház kiválasztása** listában adja meg a korábban létrehozott Data Lake Storage Gen 1 fiókot, és a **Data Lake Path** mezőben adja meg a létrehozott adatmappa elérési útját.

    ![A Data Lake Storage-fiók kiválasztása][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>A Capture hozzáadása vagy konfigurálása egy meglévő eseményközponton

A Capture-t olyan meglévő eseményközpontokon konfigurálhatja, amelyek az Event Hubs-névterekben találhatók. A Capture meglévő eseményközponton történő engedélyezéséhez vagy a Capture beállításainak módosításához kattintson a névtérre az áttekintő képernyő betöltéséhez, majd kattintson arra az eseményközpontra, amelyen engedélyezni vagy módosítani kívánja a Capture beállításait. Végül kattintson a megnyitott oldal bal oldalán lévő **Capture** elemre, majd szerkessze a beállításokat a következő ábrák szerint:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Az Azure Blob Storage konfigurálása][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Az Azure Data Lake Storage Gen 2 konfigurálása](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Az Azure Data Lake Storage konfigurálása][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>További lépések

- További információ az Event Hub Capture-ről: [Az Event Hubs Capture áttekintése][capture-overview].
- Az Azure Resource Manager-sablonok használatával is konfigurálhatja az Event Hubs Capture-t. További információkért lásd: [Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Megtudhatja, hogyan hozhat létre Azure Event Grid-előfizetést forrásként egy Event Hubs-névtérrel.](store-captured-data-data-warehouse.md)
- [Ismerkedés az Azure Data Lake Store-ral az Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
