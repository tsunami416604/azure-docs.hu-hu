---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665605"
---
**Azure Data Lake Storage Gen2** nem dedikált szolgáltatás-vagy tárolási fióktípus. Ez a big data elemzésekhez dedikált képességek legújabb kiadása.  Ezek a képességek egy általános célú v2-vagy BlockBlobStorage-fiókban érhetők el, és a fiók **hierarchikus névtér** funkciójának engedélyezésével szerezhetők be. A méretezési célokért tekintse meg ezeket a cikkeket. 

- [Méretezési célok a blob Storage](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage)-hoz.
- [A standard Storage-fiókokra vonatkozó célkitűzések méretezése](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

A **Azure Data Lake Storage Gen1** egy dedikált szolgáltatás. Ez egy nagyvállalati szintű Hyper-Scale adattár big data analitikus számítási feladatokhoz. A Data Lake Storage Gen1 használatával bármilyen méretű, típusú és feldolgozási sebességű adatot rögzíthet egyetlen helyen az operatív és a felderítő elemzéshez. A Data Lake Storage Gen1-fiókban tárolható adatmennyiségnek nincs korlátja.

| **Erőforrás** | **Korlát** | **Megjegyzések** |
| --- | --- | --- |
| Data Lake Storage Gen1 fiókok maximális száma, előfizetések régiónként |10 | A korlát növelésének igényléséhez forduljon az ügyfélszolgálathoz. |
| Hozzáférési ACL-ek maximális száma fájl vagy mappa alapján |32 | Ez egy rögzített korlát. Csoportok használata a kevesebb bejegyzéssel való hozzáférés kezelésére. |
| Alapértelmezett ACL-ek maximális száma fájl vagy mappa alapján |32 | Ez egy rögzített korlát. Csoportok használata a kevesebb bejegyzéssel való hozzáférés kezelésére. |
