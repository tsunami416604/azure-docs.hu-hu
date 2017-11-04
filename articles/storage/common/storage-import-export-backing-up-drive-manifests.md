---
title: "Biztonsági mentése Azure Import/Export meghajtó jegyzékfájlokban |} Microsoft Docs"
description: "Útmutató a Microsoft Azure Import/Export szolgáltatás automatikusan biztonsági másolat a meghajtó jegyzékfájlokban."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Meghajtó biztonsági másolatának akkor jelentkezik, az Azure importálási/exportálási feladatok

Meghajtó jegyzékfájlokban automatikusan biztonsági másolat készíthető a blobok úgy, hogy a `BackupDriveManifest` tulajdonságot `true` a a [Put feladat](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) vagy [frissítés Feladattulajdonság](/rest/api/storageimportexport/jobs#Jobs_Update) REST API-műveleteket. Alapértelmezés szerint a meghajtó jegyzékfájlokat nem készül biztonsági mentés. A meghajtó jegyzék másolatoknak blokkblobként egy tárolót a feladathoz társított tárfiókon belül. Alapértelmezés szerint a tároló neve van `waimportexport`, de megadhat egy másik nevet a a `DiagnosticsPath` tulajdonság meghívásakor a `Put Job` vagy `Update Job Properties` műveletek. A biztonsági mentési jegyzék blob neve a következő formátumban: `waies/jobname_driveid_timestamp_manifest.xml`.

 Az URI-azonosítója egy feladat a biztonsági mentési meghajtó jegyzékfájlokban meghívásával kérheti le a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) műveletet. A blob URI eredmény abban az esetben a `ManifestUri` minden meghajtó tulajdonság.

## <a name="next-steps"></a>Következő lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
