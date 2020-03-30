---
title: Azure Media Services v3-as fiókjainak kezelése | Microsoft dokumentumok
description: A médiatartalmak azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. Ez a cikk bemutatja, hogyan kezelheti az Azure Media Services v3-fiókok.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980383"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3-as fiókjainak kezelése

A médiatartalmak azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. További információ: [Storage-fiókok](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services-fiók áthelyezése előfizetések között 

Ha egy Media Services-fiókot át kell helyeznie egy új előfizetésbe, először át kell helyeznie a Media Services-fiókot tartalmazó teljes erőforráscsoportot az új előfizetésbe. Az összes kapcsolódó erőforrást át kell helyeznie: Azure Storage-fiókokat, Azure CDN-profilokat stb. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) Az Azure-beli erőforrásokhoz is eltarthat egy ideig az erőforráscsoport áthelyezése.

> [!NOTE]
> A Media Services v3 támogatja a több-bérlős modellt.

### <a name="considerations"></a>Megfontolandó szempontok

* Mielőtt átkontatna egy másik előfizetésbe, készítsen biztonsági másolatot a fiókjában lévő összes adatról.
* Le kell állítania az összes streamelési végpontot és az élő streamelési erőforrásokat. A felhasználók nem fogják tudni elérni a tartalmat az erőforráscsoport áthelyezése idejére. 

> [!IMPORTANT]
> Ne indítsa el a streamelési végpontot, amíg az áthelyezés sikeresen be nem fejeződik.

### <a name="troubleshoot"></a>Hibaelhárítás 

Ha egy Media Services-fiók vagy egy társított Azure Storage-fiók "leválasztva" válik az erőforráscsoport áthelyezése után, próbálja meg elforgassa a storage-fiók kulcsokat. Ha a tárfiók kulcsok elforgatása nem oldja fel a Media Services-fiók "leválasztott" állapotát, nyújtson be új támogatási kérelmet a Media Services-fiók "Támogatás + hibaelhárítás" menüjéből.  

## <a name="next-steps"></a>További lépések

[Fiók létrehozása](create-account-cli-quickstart.md)
