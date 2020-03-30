---
title: Erőforrások címkézésével kapcsolatos házirendek
description: Ismerteti az Azure-szabályzatok, amelyek a címke megfelelőségének biztosítása érdekében rendelhető.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147017"
---
# <a name="assign-policies-for-tag-compliance"></a>Házirendek hozzárendelése a címkemegfelelőséghez

Az [Azure-szabályzat](../../governance/policy/overview.md) használatával kényszerítheti a címkézési szabályokat és konvenciókat. Egy szabályzat létrehozásával elkerülheti az olyan erőforrások üzembe helyezésének forgatókönyvét, amelyek nem rendelkeznek a szervezet várt címkéivel. Ahelyett, hogy manuálisan alkalmazna címkéket, vagy nem megfelelő erőforrásokat keresne, olyan szabályzatot hoz létre, amely automatikusan alkalmazza a szükséges címkéket a telepítés során. A címkék mostantól az új [Módosítás](../../governance/policy/concepts/effects.md#modify) irásési és [szervizelési feladattal](../../governance/policy/how-to/remediate-resources.md)rendelkező meglévő erőforrásokra is alkalmazhatók. A következő szakasz a címkék rekedő szabályzatait mutatja be.

## <a name="policies"></a>Házirendek

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>További lépések

* Az erőforrások címkézéséről az [Azure-erőforrások rendszerezéséhez címkék használata.](tag-resources.md)
* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy alkalmazhat-e címkét egy erőforrástípusra, olvassa el [az Azure-erőforrások támogatásának címkézése.](tag-support.md)
