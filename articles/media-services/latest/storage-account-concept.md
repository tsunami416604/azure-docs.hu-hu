---
title: Tárfiókok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használja a Media Services a storage-fiókok.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 90e01f39fa6b31095d76d0dfae2f700b4fa2ca3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182867"
---
# <a name="storage-accounts"></a>Tárfiókok

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. 

>[!NOTE]
> A blobfiókok nem megengedettek **elsődleges** tárfiókként. 

Javasoljuk a GPv2, használja, így kihasználhatja, gyakori elérésű közötti választáshoz, és lassú elérésű szinteket annak. Storage-fiókokkal kapcsolatos további tudnivalókért lásd: [Azure Storage-fiók áttekintése](../../storage/common/storage-account-overview.md). 

A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Erősen ajánlott a storage-fiókok használatához a Media Services-fiók ugyanazon a helyen a további késleltetés és az adatok kimenő adatforgalmi költségek elkerülése érdekében

## <a name="assets-in-a-storage-account"></a>Storage-fiókban lévő eszközök

A Media Services v3-as a Storage API-k segítségével fájlok feltöltése. Tekintse meg a bemeneti fájlok feltöltése Media Services Storage API-k használata, tekintse át [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md). 

> [!Note]
> Meg nem próbálja meg módosítani a blob-tárolók, a Media Services SDK által előállított, a Media Services API-k használata nélkül tartalmát.

## <a name="next-steps"></a>További lépések

Tárfiók csatolása a Media Services-fiókhoz való kezelésével kapcsolatos információkért lásd: [hozzon létre egy fiókot](create-account-cli-quickstart.md).
