---
title: Az Azure erőforrás - használata egyéni szerepkörök PIM tárolóbeállítások privileged Identity Management |} Microsoft Docs
description: Ismerteti, hogyan lehet a PIM használandó egyéni szerepkörök Azure-erőforrások.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Egyéni szerepkörök cél PIM beállítások használatával

Elképzelhető, hogy szükséges a szerepkör egyes tagjai ugyanakkor biztosítható a nagyobb önállóan mások szigorú PIM beállításokat alkalmazni. Fontolja meg egy olyan forgatókönyvet, ahol a szervezet bízza sevral szerződés társult, amely egy Azure-előfizetés alkalmazás fejlesztésének segítésére. 

Erőforrás-rendszergazdaként szeretné, hogy a szervezet alkalmazottai jogosult hozzáférjenek nélkül jóváhagyás szükséges, de minden szerződés társult kell pozícionálni jóváhagyásra, amikor kérnek aktiválást. Kövesse az alábbi vázlat az Azure erőforrás-szerepkörök célzott PIM-beállítások engedélyezése a folyamat lépéseket.

## <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

[Ez az útmutató segítségével hozzon létre egy egyéni biztonsági szerepkört erőforrás](../../role-based-access-control/custom-roles.md).

Úgy tud jegyezni mely beépített szerepkör védelméről kíván-e ismétlődő tartalmaznia kell egy leíró nevet.

>[!NOTE]
>Győződjön meg arról, az egyéni biztonsági szerepkört a kívánt szerepkör duplikált, és a hatókör megegyezik-e a beépített szerepkör.

## <a name="apply-pim-settings"></a>A PIM beállításainak alkalmazása

Az Ön bérelt szolgáltatásának a szerepkör létrehozása után PIM meg, és válassza ki az erőforrást, a szerepkör hatókörébe tartozik.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[A PIM szerepkör beállításainak konfigurálása](pim-resource-roles-configure-role-settings.md) ezekről a tagokról kell vonatkozniuk, amely

Végezetül [szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md) tagok, amelyekre ezeket a beállításokat kívánja a különböző csoporthoz.

## <a name="next-steps"></a>További lépések

[Tekintse át az előfizetés tulajdonosai](pim-resource-roles-perform-access-review.md)
