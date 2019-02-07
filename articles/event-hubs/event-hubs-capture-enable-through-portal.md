---
title: Rögzítése az események streamelése az Azure portal – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet engedélyezni az Azure Portalon keresztül az Azure Event Hubs streamelési események rögzítése.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 2cfd984129097b598c08e53b3698dc32ed616cca
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810661"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Az események keresztül az Azure Event Hubs rögzítés engedélyezése

Az Azure [Event Hubs Capture][capture-overview] lehetővé teszi a streamelt Event Hubs-adatok tetszőleges [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- vagy [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-fiókba történő automatikus továbbítását.

A Capture-t konfigurálhatja az eseményközpont létrehozásakor az [Azure-portálon](https://portal.azure.com). Az adatokat Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-tárolóban vagy [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-fiókban is rögzítheti.

További információk: [Az Event Hubs Capture áttekintése][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Adatok rögzítése Azure Storage-fiókba  

Eseményközpont létrehozásakor a **Be** gombra kattintva engedélyezheti a Capture-t az **Eseményközpont létrehozása** portál képernyőjén. Ezután a **Capture-szolgáltató** mező **Azure Storage** gombjára kattintva adhatja meg a Storage-fiókot és -tárolót. Mivel az Event Hubs Capture szolgáltatások közötti hitelesítést használ a tárolóval, nem kell megadnia egy tárfiók kapcsolati sztringjét. Az erőforrás-választó automatikusan kiválasztja az erőforrás URI-azonosítóját a tárfiókhoz. Az Azure Resource Manager használatakor explicit módon meg kell adnia ezt az URI-t sztringként.

Az időkeret alapértelmezett értéke 5 perc. A minimális értéke 1, a maximális 15. A **Méret** ablak 10–500 MB tartománnyal rendelkezik.

![Ez alatt az időszak a rögzítéshez][1]

> [!NOTE]
> Engedélyezheti vagy letilthatja a kibocsátó üres fájlok nincsenek események bekövetkezésekor a rögzítési időszak alatt. 

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Adatok rögzítése egy Azure Data Lake Store-fiókba

Az adatok Azure Data Lake Store-ban történő rögzítéséhez létre kell hoznia egy Data Lake Store-fiókot és egy eseményközpontot:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Azure Data Lake Store-fiók és -mappák létrehozása

1. A Data Lake Store-fiók létrehozásához kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md) című témakör utasításait.
2. Kövesse az [engedélyek az Event Hubs számára való kiosztását ismertető](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) szakasz utasításait egy, a Data Lake Store-fiókon belüli mappa létrehozásához, amelyben rögzíteni szeretné az Event Hubs adatait, és engedélyek kiosztásához az Event Hubs számára, hogy adatokat írhasson a Data Lake Store-fiókba.  

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Vegye figyelembe, hogy az eseményközpontnak ugyanabban az Azure-előfizetésben kell lennie, mint amelyben az imént létrehozott Azure Data Lake Store van. Eseményközpont létrehozásához kattintson a **Be** gombra a **Capture** területen az **Eseményközpont létrehozása** portál oldalán. 
2. Az **Eseményközpont létrehozása** portál oldalán válassza az **Azure Data Lake Store** lehetőséget a **Capture-szolgáltató** mezőben.
3. A **Data Lake Store kiválasztása** mezőben adja meg a korábban létrehozott Data Lake Store-fiókot, a **Data Lake elérési útja** mezőben pedig a létrehozott adatmappa elérési útját.

    ![Válassza ki a Data Lake Storage-fiók][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>A Capture hozzáadása vagy konfigurálása egy meglévő eseményközponton

A Capture-t olyan meglévő eseményközpontokon konfigurálhatja, amelyek az Event Hubs-névterekben találhatók. A Capture meglévő eseményközponton történő engedélyezéséhez vagy a Capture beállításainak módosításához kattintson a névtérre az áttekintő képernyő betöltéséhez, majd kattintson arra az eseményközpontra, amelyen engedélyezni vagy módosítani kívánja a Capture beállításait. Végül kattintson a megnyitott oldal bal oldalán lévő **Capture** elemre, majd szerkessze a beállításokat a következő ábrák szerint:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Az Azure Blob-tároló konfigurálása][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![Az Azure Data Lake Storage konfigurálása][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>További lépések

- További információ az Event Hub Capture-ről: [Az Event Hubs Capture áttekintése][capture-overview].
- Az Azure Resource Manager-sablonok használatával is konfigurálhatja az Event Hubs Capture-t. További információkért lásd: [Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Megtudhatja, hogyan hozhat létre Azure Event Grid-előfizetést forrásként egy Event Hubs-névtérrel.](store-captured-data-data-warehouse.md)
- [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
