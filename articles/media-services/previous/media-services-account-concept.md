---
title: Az Azure Media Services v2-fiókok kezelése |} A Microsoft Docs
description: Indítsa el a kezelése, titkosítására, kódolás, elemzése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. Ez a cikk ismerteti az Azure Media Services v2 fiókokat szeretne kezelni.
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
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622042"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Az Azure Media Services v2-fiókok kezelése

Indítsa el a kezelése, titkosítására, kódolás, elemzése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services-fiók áthelyezése előfizetések között 

Ha a Media Services-fiók áthelyezése új előfizetésre van szüksége, kell először helyezze át az új előfizetés a Media Services-fiókot tartalmazó teljes erőforráscsoportot. Helyezze át az összes kapcsolódó erőforrás: Az Azure Storage-fiókok, az Azure CDN-profilok stb. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/resource-group-move-resources.md). Csakúgy, mint bármely az Azure-erőforrások, erőforráscsoportok áthelyezési is igénybe vehet egy ideig.

A Media Services v2 nem támogatja a több-bérlős modell. Ha a Media Services-fiók áthelyezése egy új bérlő előfizetésre van szüksége, hozzon létre egy új Azure Active Directory (Azure AD-) alkalmazás az új bérlő. Ezután lépjen a fiók az előfizetést, az új bérlőhöz. A bérlő Áthelyezés befejezése után elkezdheti a v2 API-k használata a Media Services-fiók eléréséhez használt Azure AD-alkalmazást az új bérlőhöz. 

> [!IMPORTANT]
> Vissza kell állítania a [Azure AD-hitelesítés](media-services-portal-get-started-with-aad.md) info Media Services v2 API eléréséhez.  
### <a name="considerations"></a>Megfontolandó szempontok

* Hozzon létre a biztonsági mentések az összes adat a fiókban egy másik előfizetésbe való migrálás előtt.
* Állítsa le a Streamelési végpontok és az élő adások online közvetítése erőforrások kell. A felhasználók nem fognak tudni hozzáférni a tartalmat az erőforrás-csoport áthelyezés idejére. 

> [!IMPORTANT]
> Indul el a folyamatos átviteli végponton, amíg az áthelyezés sikeres végrehajtása után.

### <a name="troubleshoot"></a>Hibaelhárítás 

Ha a Media Services-fiók vagy egy társított Azure Storage-fiók "lecsatlakoznak" az erőforrás-csoport áthelyezése a következő, próbálja meg a Storage-fiók kulcsok. A Storage-fiók kulcsok nem oldja meg a Media Services-fiók "leválasztott" állapotát, ha a fájl egy új támogatási kérést a "Támogatás + hibaelhárítás" a Media Services-fiók menüjéből.  
 
## <a name="next-steps"></a>További lépések

[Fiók létrehozása](media-services-portal-create-account.md)
