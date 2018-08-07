---
title: Az Azure Import/Export meghajtójegyzékek biztonsági mentése |} A Microsoft Docs
description: Megtudhatja, hogyan szeretné, hogy a Microsoft Azure Import/Export szolgáltatás automatikusan biztonsági másolat a meghajtójegyzékek.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 933c0121a4f718ff812fc921bd6e04983fc69931
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520537"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Azure Import/Export-feladatok meghajtójegyzékek biztonsági mentése a meghajtó

Meghajtójegyzékek automatikusan biztonsági másolat készíthető a blobokhoz beállításával a `BackupDriveManifest` tulajdonságot `true` a a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) vagy [feladat tulajdonságainak](/rest/api/storageimportexport/jobs#Jobs_Update) REST API-műveleteket. Alapértelmezés szerint a meghajtójegyzékek nem készül biztonsági másolat. A meghajtó jegyzékfájl biztonsági másolatokat egy tárolóban, a feladathoz hozzárendelt a tárfiókban található blokkblobok formájában tárolja. Alapértelmezés szerint a tároló neve a `waimportexport`, de megadhat egy másik nevet a a `DiagnosticsPath` tulajdonság hívásakor a `Put Job` vagy `Update Job Properties` műveleteket. A biztonsági mentési jegyzékfájl blob a következő formátumban lesznek elnevezve: `waies/jobname_driveid_timestamp_manifest.xml`.

 URI-ját egy feladat a biztonsági mentési meghajtójegyzékek meghívásával lehet lekérdezni a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet. A blob URI-t adja vissza a `ManifestUri` minden meghajtó tulajdonság.

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
