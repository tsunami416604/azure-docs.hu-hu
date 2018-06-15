---
title: Storage-fiókok Azure Media Services |} Microsoft Docs
description: A cikk ismerteti, hogyan használja a Media Services a storage-fiókok.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788066"
---
# <a name="storage-accounts"></a>Tárfiókok

Egy Media Services-fiók létrehozásakor kell megadnia egy Azure Storage-fiók erőforrás nevét. A megadott tárfiók a Media Services-fiókhoz csatolva van. 

Kell egy **elsődleges** tárfiók, és bármely számú lehet **másodlagos** a Media Services-fiókhoz társított tárfiókokat. Media Services szolgáltatásban **általános célú v2** (GPv2) vagy **általános célú v1** (GPv1) fiókok. 

>[!NOTE]
> Csak a BLOB-fiókok használata nem engedélyezett **elsődleges**. 

Azt javasoljuk, hogy így választás az érdekes előnyeinek kihasználása és a tárolási rétegek cool GPv2, használjon. Storage-fiókok kapcsolatos további információkért lásd: [Azure Storage-fiók beállításai](../../storage/common/storage-account-options.md). 

## <a name="assets-in-a-storage-account"></a>A tárfiókban lévő eszközök

A Storage API-k Media Services v3 használ a fájlok feltöltéséhez. Storage API-k használata a Media Services a bemeneti fájlok feltöltéséhez megtekintéséhez tekintse meg [létrehozni a feladat bemeneti helyi fájlból](job-input-from-local-file-how-to.md). 

> [!Note]
> Ne próbáljon a Media Services API-k használata nélkül a Media Services SDK által előállított blob-tároló tartalmának módosításához.

## <a name="next-steps"></a>További lépések

A Media Services-fiókhoz csatolni egy tárfiókot, lásd: [-fiók létrehozása](create-account-cli-quickstart.md).
