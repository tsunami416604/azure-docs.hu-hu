---
title: "Szakítsa meg, és az Azure importálási/exportálási feladatok törlése |} Microsoft Docs"
description: "Megtudhatja, hogyan megszakításához és a Microsoft Azure Import/Export szolgáltatás feladatok törlése."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1e989c72fc03697bf6d2e515ff53003703665d1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>A megszakítás és az Azure importálási/exportálási feladatok törlése

 Hogy egy feladatot az előbb szakad meg, kérje a `Packaging` állapot, hívja a [frissítés Feladattulajdonság](/rest/api/storageimportexport/jobs#Jobs_Update) művelet, és állítsa a `CancelRequested` elem `true`. A feladat megszakítása van, elérhető legjobb alapon. Ha meghajtók jelenleg másolja át az adatokat, adatok továbbra is helyezhető át, akkor is megszakítását kérték.

 A megszakított feladatok átkerül a `Completed` állapot, és ekkor törölt 90 napig maradnak.

 Törli a feladatot, hívja meg a [törlése feladat](/rest/api/storageimportexport/jobs#Jobs_Delete) művelet előtt a feladat rendelkezik-e kiadva (Ez azt jelenti, hogy amíg a feladat a `Creating` állapot). Törölheti is a feladat a esetén a `Completed` állapotát. Egy feladat törlését követően annak adatait és állapotát már nincs a REST API-t vagy az Azure-portálon keresztül érhető el.

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
