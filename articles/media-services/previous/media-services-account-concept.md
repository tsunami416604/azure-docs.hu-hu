---
title: Azure Media Services v2-es fiókjainak kezelése | Microsoft dokumentumok
description: A médiatartalmak azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. Ez a cikk bemutatja, hogyan kezelheti az Azure Media Services v2-fiókok.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981945"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Az Azure Media Services v2-es fiókjainak kezelése

A médiatartalmak azure-beli kezelésének, titkosításának, elemzésének és streamelésének megkezdéséhez létre kell hoznia egy Media Services-fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services-fiók áthelyezése előfizetések között 

Ha egy Media Services-fiókot át kell helyeznie egy új előfizetésbe, először át kell helyeznie a Media Services-fiókot tartalmazó teljes erőforráscsoportot az új előfizetésbe. Az összes kapcsolódó erőforrást át kell helyeznie: Azure Storage-fiókokat, Azure CDN-profilokat stb. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) Az Azure-beli erőforrásokhoz is eltarthat egy ideig az erőforráscsoport áthelyezése.

A Media Services v2 nem támogatja a több-bérlős modellt. Ha át kell helyeznie egy Media Services-fiókot egy új bérlőelőfizetésbe, hozzon létre egy új Azure Active Directory (Azure AD) alkalmazást az új bérlőben. Ezután helyezze át a fiókját az előfizetésbe az új bérlőben. Miután a bérlő áthelyezése befejeződött, megkezdheti egy Azure AD-alkalmazás az új bérlő a Media Services-fiók eléréséhez a v2 API-k használatával. 

> [!IMPORTANT]
> A Media Services v2 API eléréséhez alaphelyzetbe kell állítania az [Azure AD hitelesítési](media-services-portal-get-started-with-aad.md) adatait.  
### <a name="considerations"></a>Megfontolandó szempontok

* Mielőtt átkontatna egy másik előfizetésbe, készítsen biztonsági másolatot a fiókjában lévő összes adatról.
* Le kell állítania az összes streamelési végpontot és az élő streamelési erőforrásokat. A felhasználók nem fogják tudni elérni a tartalmat az erőforráscsoport áthelyezése idejére. 

> [!IMPORTANT]
> Ne indítsa el a streamelési végpontot, amíg az áthelyezés sikeresen be nem fejeződik.

### <a name="troubleshoot"></a>Hibaelhárítás 

Ha egy Media Services-fiók vagy egy társított Azure Storage-fiók "leválasztva" válik az erőforráscsoport áthelyezése után, próbálja meg elforgassa a storage-fiók kulcsokat. Ha a tárfiók kulcsok elforgatása nem oldja fel a Media Services-fiók "leválasztott" állapotát, nyújtson be új támogatási kérelmet a Media Services-fiók "Támogatás + hibaelhárítás" menüjéből.  
 
## <a name="next-steps"></a>További lépések

[Fiók létrehozása](media-services-portal-create-account.md)
