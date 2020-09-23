---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024803"
---
Itt láthatók a Storage-fiókba másolt adatmennyiség korlátai. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. A korlátozásokkal kapcsolatos legfrissebb információk: a [blob Storage skálázhatósági és teljesítményi céljai](../articles/storage/blobs/scalability-targets.md) , valamint a [méretezhetőség és a teljesítmény-Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Az Azure Storage-fiókba másolt adatmennyiség                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| BLOB és oldal blobjának letiltása                                            | A maximális korlát megegyezik az Azure- [előfizetéshez definiált tárolási korláttal](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) , és az összes forrásból, köztük a Data boxból származó adatokkal is rendelkezik. |
| Azure Files                                                          | Data Box támogatja a nagyméretű fájlmegosztást (100TiB), ha engedélyezve van a Data Box rendelés létrehozása előtt. <br> Ha nincs engedélyezve a megrendelés létrehozása előtt, a maximális fájlmegosztás mérete 5 TiB. <br> A prémium fájlmegosztás még nem támogatott.<br> A *StorageAccount_AzureFiles* alatti összes mappának ezt a korlátot kell követnie. <br> További információ: [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
