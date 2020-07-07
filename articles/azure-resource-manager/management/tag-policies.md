---
title: Erőforrások címkézésére vonatkozó szabályzatok
description: Ismerteti azokat az Azure-szabályzatokat, amelyeket hozzárendelhet a címke megfelelőségének biztosításához.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147867"
---
# <a name="assign-policies-for-tag-compliance"></a>Szabályzatok kiosztása a címkézési megfelelőséghez

A [Azure Policy](../../governance/policy/overview.md) a címkézési szabályok és konvenciók betartatására használható. A szabályzat létrehozásával elkerülhető, hogy az előfizetéshez tartozó erőforrások milyen helyzetben legyenek, és hogy ne legyenek a szervezete számára várt címkék. A címkék manuális alkalmazása vagy a nem megfelelő erőforrások keresése helyett hozzon létre egy szabályzatot, amely automatikusan alkalmazza a szükséges címkéket az üzembe helyezés során. A címkék mostantól a meglévő erőforrásokra is alkalmazhatók az új [módosítási](../../governance/policy/concepts/effects.md#modify) effektussal és [szervizelési feladattal](../../governance/policy/how-to/remediate-resources.md). A következő szakasz példákat mutat be a címkékre.

## <a name="policies"></a>Házirendek

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>További lépések

* Az erőforrások címkézésével kapcsolatos további tudnivalókért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md).
* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
