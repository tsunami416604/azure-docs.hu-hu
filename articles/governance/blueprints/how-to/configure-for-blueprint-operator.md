---
title: A környezet beállítása a Blueprint Operator számára
description: Ismerje meg, hogyan konfigurálhatja az Azure-környezetet a Blueprint Operator beépített szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörrel való használatra.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873216"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>A környezet konfigurálása Blueprint Operatorhoz

A tervezetdefiníciók és a tervezet-hozzárendelések kezelése különböző csoportokhoz rendelhető. Gyakori, hogy egy építész vagy cégirányítási csapat felelős a tervdefiníciók életciklus-kezeléséért, míg egy műveleti csapat felelős a központilag ellenőrzött tervdefiníciók hozzárendeléseinek kezeléséért.

A **Blueprint Operator** beépített szerepköralapú hozzáférés-vezérlés (RBAC) kifejezetten az ilyen típusú forgatókönyv. A szerepkör lehetővé teszi, hogy a műveletek típusú csapatok a szervezetek tervezetdefinícióinak hozzárendelését kezeljék, de nem módosíthatja őket. Ehhez némi konfigurációt igényel az Azure-környezetben, és ez a cikk ismerteti a szükséges lépéseket.

## <a name="grant-permission-to-the-blueprint-operator"></a>Engedély megadása a Blueprint Operátornak

Az első lépés az, hogy megadja a **Blueprint Operator** szerepkört a fiók vagy biztonsági csoport (ajánlott), amely lesz hozzárendeli tervrajzok. Ezt a műveletet a felügyeleticsoport-hierarchia legmagasabb szintjén kell végrehajtani, amely magában foglalja az összes felügyeleti csoportot és előfizetést, amelyhez a műveleti csapatnak rendelkeznie kell a blueprint-hozzárendelési hozzáféréssel. Javasoljuk, hogy kövesse a minimális jogosultság elvét, amikor megadja ezeket az engedélyeket.

1. (Ajánlott) [Biztonsági csoport létrehozása és tagok hozzáadása](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. Blueprint **Operator** [szerepkör-hozzárendelésének hozzáadása](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) a fiókhoz vagy a biztonsági csoporthoz

## <a name="user-assign-managed-identity"></a>Felhasználó-hozzárendelés felügyelt identitás

A tervezetdefiníció rendszer- vagy felhasználó által hozzárendelt felügyelt identitásokat használhat. Azonban a **Blueprint Operator** szerepkör használatakor a tervezet definícióját úgy kell konfigurálni, hogy egy felhasználó által hozzárendelt felügyelt identitást használjon. Emellett a **rendszerrajzoperátori** szerepkört kapó fióknak vagy biztonsági csoportnak meg kell adni a **felügyelt identitásoperátor szerepkört** a felhasználó által hozzárendelt felügyelt identitáson. Ezen engedély nélkül a tervezet-hozzárendelések az engedélyek hiánya miatt sikertelenek.

1. [Felhasználó által hozzárendelt felügyelt identitás létrehozása](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) hozzárendelt terv szerint

1. A **Felügyelt identitáskezelő** [szerepkör-hozzárendelésének hozzáadása](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) a fiókhoz vagy a biztonsági csoporthoz. A szerepkör-hozzárendelés hatóköre az új, felhasználó által hozzárendelt felügyelt identitáshoz.

1. A **Blueprint Operátor,** [rendeljen hozzá egy tervezetet,](../create-blueprint-portal.md#assign-a-blueprint) amely az új, felhasználó által hozzárendelt felügyelt identitást használja.

## <a name="next-steps"></a>További lépések

- További információ a [tervterv életciklusáról.](../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../concepts/resource-locking.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.