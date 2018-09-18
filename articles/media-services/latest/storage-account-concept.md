---
title: Tárfiókok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használja a Media Services a storage-fiókok.
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
ms.openlocfilehash: 883aecaa82f1a83af22bf345d364c2b43776e559
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732209"
---
# <a name="storage-accounts"></a>Tárfiókok

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

Rendelkeznie kell egy **elsődleges** storage-fiókot, és tetszőleges számú rendelkezhet **másodlagos** a Media Services-fiókjához társított storage-fiókok. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. 

>[!NOTE]
> A blobfiókok nem megengedettek **elsődleges** tárfiókként. 

Javasoljuk a GPv2, használja, így kihasználhatja, gyakori elérésű közötti választáshoz, és lassú elérésű szinteket annak. Storage-fiókokkal kapcsolatos további tudnivalókért lásd: [Azure Storage-fiók áttekintése](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Storage-fiókban lévő eszközök

A Media Services v3-as a Storage API-k segítségével fájlok feltöltése. Tekintse meg a bemeneti fájlok feltöltése Media Services Storage API-k használata, tekintse át [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md). 

> [!Note]
> Meg nem próbálja meg módosítani a blob-tárolók, a Media Services SDK által előállított, a Media Services API-k használata nélkül tartalmát.

## <a name="next-steps"></a>További lépések

Tárfiók csatolása a Media Services-fiókhoz való kezelésével kapcsolatos információkért lásd: [hozzon létre egy fiókot](create-account-cli-quickstart.md).
