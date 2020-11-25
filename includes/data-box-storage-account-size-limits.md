---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025951"
---
Itt láthatók a Storage-fiókba másolt adatmennyiség korlátai. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. A korlátozásokkal kapcsolatos legfrissebb információk: a [blob Storage skálázhatósági és teljesítményi céljai](../articles/storage/blobs/scalability-targets.md) , valamint a [méretezhetőség és a teljesítmény-Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Az Azure Storage-fiókba másolt adatmennyiség                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| BLOB és oldal blobjának letiltása                                            | A maximális korlát megegyezik az Azure- [előfizetéshez definiált tárolási korláttal](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) , és az összes forrásból, köztük a Data boxból származó adatokkal is rendelkezik. |
| Azure Files                                                          | Data Box támogatja a nagyméretű fájlmegosztást (100TiB), ha engedélyezve van a Data Box rendelés létrehozása előtt. <br> Ha nincs engedélyezve a megrendelés létrehozása előtt, a maximális fájlmegosztás mérete 5 TiB. <br> A prémium fájlmegosztás még nem támogatott.<br> A *StorageAccount_AzureFiles* alatti összes mappának ezt a korlátot kell követnie. <br> További információ: [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |