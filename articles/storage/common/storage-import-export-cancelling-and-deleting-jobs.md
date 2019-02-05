---
title: Szakítsa meg és az Azure importálási/exportálási feladat törlése |} A Microsoft Docs
description: Megtudhatja, hogyan megszakíthatja és törölheti a Microsoft Azure Import/Export szolgáltatás feladatok.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700128"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Megszakítása és törlése az Azure importálási és exportálási feladatokhoz

 Kérni, hogy egy feladat szakítható meg annak a előtt a `Packaging` állapotban van, hívja a [frissítési feladat tulajdonságai](/rest/api/storageimportexport/jobs) műveletet, és állítsa be a `CancelRequested` elem `true`. A feladat megszakítása legjobb történik. Adatok átvitele folyamatban meghajtók esetén adatok továbbra is át lehet adni törlését kérte után is.

 A megszakított feladatok átkerül a `Completed` állapot, és ekkor törölné a rendszer 90 napig megőrzi.

 A feladat törléséhez hívja a [törlése feladat](/rest/api/storageimportexport/jobs) művelet, mielőtt a feladat leszállítását (azt jelenti, amíg a feladat a `Creating` állapot). Törölheti is a feladat amikor a `Completed` állapota. Egy feladat törlését követően annak adatait és állapotát nem lesznek a REST API-t vagy az Azure Portalon keresztül érhető el.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
