---
title: Környezet beállítása a Blueprint Operator számára
description: Ismerje meg, hogyan konfigurálhatja Azure-környezetét a Blueprint Operator Azure beépített szerepkörrel való használatra.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ef911553b4985a6497fb397a1ae405387c6539fb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051457"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>A környezet konfigurálása Blueprint Operatorhoz

A terv-definíciók és a tervrajz-hozzárendelések kezelése különböző csapatokhoz rendelhető. Az építész vagy az irányítási csapat feladata az, hogy a terv definícióinak életciklus-felügyeletéhez felelősek legyenek, miközben egy operatív csapat felelős a központilag ellenőrzött tervrajz-definíciók hozzárendeléseinek kezeléséért.

A **terv operátor** beépített szerepköralapú hozzáférés-vezérlés (RBAC) kifejezetten az ilyen típusú forgatókönyvekben való használatra készült. A szerepkör lehetővé teszi, hogy a műveleti típusok csoportja felügyelje a szervezeti terv definícióinak hozzárendelését, de ne módosítsa őket. Ehhez szükség van néhány konfigurációra az Azure-környezetben, és ez a cikk ismerteti a szükséges lépéseket.

## <a name="grant-permission-to-the-blueprint-operator"></a>Engedély megadása a Blueprint operátornak

Első lépésként adja meg a **terv operátori** szerepkört ahhoz a fiókhoz vagy biztonsági csoporthoz (ajánlott), amely a tervrajzok hozzárendelését fogja végrehajtani. Ezt a műveletet a felügyeleti csoport hierarchiájának legmagasabb szintjén kell végrehajtani, amely magában foglalja az összes felügyeleti csoportot és előfizetést, és az operatív csapatnak terv-hozzárendelési jogosultsággal kell rendelkeznie. Az engedélyek megadásakor ajánlott a legalacsonyabb jogosultsági szint elvének követése.

1. Ajánlott [Biztonsági csoport létrehozása és Tagok hozzáadása](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. A **terv operátor** [szerepkör-hozzárendelésének hozzáadása](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) a fiókhoz vagy a biztonsági csoporthoz

## <a name="user-assign-managed-identity"></a>Felhasználó – felügyelt identitások kiosztása

A tervrajzok definíciója rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitásokat használhat. A **terv operátori** szerepkör használatakor azonban a terv definícióját a felhasználó által hozzárendelt felügyelt identitás használatára kell beállítani. Emellett a **terv operátori** szerepkörrel rendelkező fiókot vagy biztonsági csoportot meg kell adni a **felügyelt identitás-kezelő** szerepkörnek a felhasználó által hozzárendelt felügyelt identitáson. Ezen engedély nélkül a terv-hozzárendelések sikertelenek az engedélyek hiánya miatt.

1. [Felhasználó által hozzárendelt felügyelt identitás létrehozása](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) egy hozzárendelt terv általi használatra

1. [Adja hozzá](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) a **felügyelt identitás-kezelő** szerepkör-hozzárendelését a fiókhoz vagy a biztonsági csoporthoz. Hatókör a szerepkör-hozzárendelés az új, felhasználó által hozzárendelt felügyelt identitáshoz.

1. A **terv operátorként** [rendeljen hozzá egy tervet](../create-blueprint-portal.md#assign-a-blueprint) , amely az új, felhasználó által hozzárendelt felügyelt identitást használja.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).