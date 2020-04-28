---
title: Azure Media Services v2-fiókok kezelése | Microsoft Docs
description: Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. Ez a cikk a Azure Media Services v2-fiókok kezelését ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981945"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services v2-fiókok kezelése

Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services fiók áthelyezése az előfizetések között 

Ha egy Media Services fiókot egy új előfizetésre kell áthelyeznie, előbb át kell helyeznie a teljes erőforráscsoportot, amely tartalmazza a Media Services fiókot az új előfizetéshez. Az összes csatolt erőforrást át kell helyeznie: Azure Storage-fiókokat, Azure CDN profilokat stb. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Az Azure-beli erőforrásokhoz hasonlóan az erőforráscsoportok áthelyezése is eltarthat egy ideig.

A Media Services v2 nem támogatja a több-bérlős modellt. Ha egy új bérlőn át kell helyeznie egy Media Services fiókot egy előfizetésbe, hozzon létre egy új Azure Active Directory (Azure AD) alkalmazást az új bérlőn. Ezután helyezze át a fiókját az előfizetésbe az új bérlőn. A bérlő áthelyezése után megkezdheti egy Azure AD-alkalmazás használatát az új bérlőről, hogy a v2 API-k használatával hozzáférhessen a Media Services fiókhoz. 

> [!IMPORTANT]
> Media Services v2 API eléréséhez alaphelyzetbe kell állítania az [Azure ad-hitelesítési](media-services-portal-get-started-with-aad.md) adatokat.  
### <a name="considerations"></a>Megfontolandó szempontok

* Hozzon létre biztonsági másolatokat a fiókban lévő összes adattal, mielőtt másik előfizetésre migrál.
* Le kell állítania az összes streaming-végpontot és az élő adatfolyam-továbbítási erőforrásokat. A felhasználók nem fognak tudni hozzáférni a tartalomhoz az erőforráscsoport áthelyezésének időtartama alatt. 

> [!IMPORTANT]
> Ne indítsa el a folyamatos átviteli végpontot, amíg az áthelyezés sikeresen be nem fejeződik.

### <a name="troubleshoot"></a>Hibaelhárítás 

Ha egy Media Services fiók vagy egy társított Azure Storage-fiók "leválasztva" válik az erőforráscsoport áthelyezése után, próbálja meg elforgatni a Storage-fiók kulcsait. Ha a Storage-fiók kulcsainak elforgatása nem oldja meg a Media Services fiók "leválasztott" állapotát, a Media Services fiók "támogatás + hibaelhárítás" menüjéből új támogatási kérelmet kell benyújtani.  
 
## <a name="next-steps"></a>További lépések

[Fiók létrehozása](media-services-portal-create-account.md)
