---
title: Szakítsa meg és az Azure importálási/exportálási feladat törlése |} A Microsoft Docs
description: Megtudhatja, hogyan megszakíthatja és törölheti a Microsoft Azure Import/Export szolgáltatás feladatok.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698689"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Megszakítása és törlése az Azure importálási és exportálási feladatokhoz

 Kérni, hogy egy feladat szakítható meg annak a előtt a `Packaging` állapotban van, hívja a [frissítési feladat tulajdonságai](/rest/api/storageimportexport/jobs#Jobs_Update) műveletet, és állítsa be a `CancelRequested` elem `true`. A feladat megszakítása legjobb történik. Adatok átvitele folyamatban meghajtók esetén adatok továbbra is át lehet adni törlését kérte után is.

 A megszakított feladatok átkerül a `Completed` állapot, és ekkor törölné a rendszer 90 napig megőrzi.

 A feladat törléséhez hívja a [törlése feladat](/rest/api/storageimportexport/jobs#Jobs_Delete) művelet, mielőtt a feladat leszállítását (azt jelenti, amíg a feladat a `Creating` állapot). Törölheti is a feladat amikor a `Completed` állapota. Egy feladat törlését követően annak adatait és állapotát nem lesznek a REST API-t vagy az Azure Portalon keresztül érhető el.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
