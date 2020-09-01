---
title: Hozzon létre egy Storage-fiókot Azure Data Lake Storage Gen2
description: Megtudhatja, hogyan hozhat létre Azure Data Lake Storage Gen2 használatával használható Storage-fiókot.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270165"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2hez használandó Storage-fiók létrehozása

Data Lake Storage Gen2 képességek használatához hozzon létre egy hierarchikus névteret tartalmazó Storage-fiókot.

## <a name="choose-a-storage-account-type"></a>Válassza ki a Storage-fiók típusát

A Data Lake Storage képességek a következő típusú Storage-fiókok esetén támogatottak:

- Általános célú v2
- BlockBlobStorage

További információ a közöttük való választásról: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Hierarchikus névtérrel rendelkező Storage-fiók létrehozása

Hozzon létre egy [általános célú v2-fiókot](../common/storage-account-create.md) vagy egy [BlockBlobStorage](storage-blob-create-account-block-blob.md) -fiókot, amelyen engedélyezve van a **hierarchikus névtér** beállítása.

A fiók létrehozásakor Data Lake Storage képességek zárolásának feloldásához engedélyezze a **hierarchikus névtér** beállítást a **Storage-fiók létrehozása** lap **speciális** lapján. A fiók létrehozásakor engedélyeznie kell ezt a beállítást. Ezt követően nem engedélyezheti.

A következő képen a **Storage-fiók létrehozása** lapon látható ez a beállítás.

> [!div class="mx-imgBorder"]
> ![Hierarchikus névtér beállítása](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Ha rendelkezik egy meglévő Storage-fiókkal, amelyet Data Lake Storagehoz kíván használni, és a hierarchikus névtér beállítása le van tiltva, akkor az új Storage-fiókba kell áttelepítenie az adatkészletet, amelyen engedélyezve van a beállítás.

## <a name="next-steps"></a>További lépések

- [A Storage-fiók áttekintése](../common/storage-account-overview.md)
- [A Azure Data Lake Storage Gen2 használata big data követelményekhez](data-lake-storage-data-scenarios.md)
- [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](data-lake-storage-access-control.md)