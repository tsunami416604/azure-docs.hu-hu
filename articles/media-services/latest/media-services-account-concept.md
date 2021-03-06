---
title: Azure Media Services v3-fiókok kezelése | Microsoft Docs
description: Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. Ez a cikk a Azure Media Services v3-fiókok kezelését ismerteti.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: inhenkel
ms.openlocfilehash: 77453aa2d8989a0542beffb1c88ebe7ee53d1506
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "96008327"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3-fiókok kezelése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. További információ: Storage- [fiókok](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services fiók áthelyezése az előfizetések között 

Ha egy Media Services fiókot egy új előfizetésre kell áthelyeznie, előbb át kell helyeznie a teljes erőforráscsoportot, amely tartalmazza a Media Services fiókot az új előfizetéshez. Az összes csatolt erőforrást át kell helyeznie: Azure Storage-fiókokat, Azure CDN profilokat stb. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Az Azure-beli erőforrásokhoz hasonlóan az erőforráscsoportok áthelyezése is eltarthat egy ideig.

> [!NOTE]
> Media Services v3 támogatja a több-bérlős modellt.

### <a name="considerations"></a>Megfontolandó szempontok

* Hozzon létre biztonsági másolatokat a fiókban lévő összes adattal, mielőtt másik előfizetésre migrál.
* Le kell állítania az összes streaming-végpontot és az élő adatfolyam-továbbítási erőforrásokat. A felhasználók nem fognak tudni hozzáférni a tartalomhoz az erőforráscsoport áthelyezésének időtartama alatt. 

> [!IMPORTANT]
> Ne indítsa el a folyamatos átviteli végpontot, amíg az áthelyezés sikeresen be nem fejeződik.

### <a name="troubleshoot"></a>Hibaelhárítás

Ha egy Media Services fiók vagy egy társított Azure Storage-fiók "leválasztva" válik az erőforráscsoport áthelyezése után, próbálja meg elforgatni a Storage-fiók kulcsait. Ha a Storage-fiók kulcsainak elforgatása nem oldja meg a Media Services fiók "leválasztott" állapotát, a Media Services fiók "támogatás + hibaelhárítás" menüjéből új támogatási kérelmet kell benyújtani.  

## <a name="next-steps"></a>További lépések

[Fiók létrehozása](./create-account-howto.md)
