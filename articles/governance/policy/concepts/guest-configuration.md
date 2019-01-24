---
title: Megtudhatja, hogyan hajtsa végre a virtuális gépen belüli naplózás
description: Ismerje meg, hogyan Azure Policy segítségével Vendég konfigurációs naplózási beállítások egy Azure virtuális gépen belül.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0a571084819c5dfed3f8d6891b59032ef2eecdd6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856400"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy Vendég konfiguráció ismertetése

Naplózás mellett és [szervizelés](../how-to/remediate-resources.md) Azure-erőforrások, az Azure Policy a következőkre képes naplózási beállításait egy virtuális gépen. A Vendég Configuration bővítmény és az ügyfél az érvényesítést végzi. A bővítményt, az ügyfélen, érvényesíti a konfigurációt az operációs rendszer, alkalmazás-konfigurációs vagy jelenléte, környezeti beállítások és beállításait.

> [!IMPORTANT]
> Jelenleg csak **beépített** Vendég konfigurációs szabályzatok használata támogatott.

## <a name="extension-and-client"></a>Bővítmény és az ügyfél

A naplózási beállítások egy virtuális gépen egy [virtuálisgép-bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a megfelelő szabályzat-hozzárendelés és a megfelelő konfiguráció definíciója.

### <a name="register-guest-configuration-resource-provider"></a>Vendég-konfigurációs erőforrás-szolgáltató regisztrálása

Vendég-konfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltató. A portálon keresztül vagy a Powershellen keresztül lehet regisztrálni.

#### <a name="registration---portal"></a>Regisztráció – portál

Az erőforrás-szolgáltató regisztrálása Vendég konfiguráció az Azure Portalon keresztül, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Portalon, és kattintson a **minden szolgáltatás**. Keresse meg és válassza **előfizetések**.

1. Keresse meg és kattintson arra az előfizetésre, amely engedélyezi a Vendég konfigurációját.

1. A bal oldali menüben lévő a **előfizetés** kattintson **erőforrás-szolgáltatók**.

1. Állítson be szűrőt, és görgessen, amíg meg nem találja **Microsoft.GuestConfiguration**, majd kattintson a **regisztrálása** ugyanabban a sorban.

#### <a name="registration---powershell"></a>Regisztráció – PowerShell

Vendég konfigurációs PowerShell-lel az erőforrás-szolgáltató regisztrálásához futtassa a következő parancsot:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Érvényesítési eszközök

A virtuális gépen belül a Vendég konfigurációs ügyfélnél a helyi eszközök futtatásához a naplózási.

Az alábbi táblázat az egyes támogatott operációs rendszeren használja a helyi eszközök listáját:

|Operációs rendszer|Fürtérvényesítési eszköz|Megjegyzések|
|-|-|-|
|Windows|[A Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[A Chef InSpec](https://www.chef.io/inspec/)| Ruby és Python telepíti a Vendég Configuration bővítményt. |

### <a name="supported-client-types"></a>Támogatott ügyfél típusú

Az alábbi táblázat az Azure-rendszerképek támogatott operációs rendszerek listája látható:

|Közzétevő|Name (Név)|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04-es, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenterhez, a 2012 R2 Datacenter, a 2016-Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Vendég konfigurációs jelenleg nem támogatott az egyéni virtuálisgép-lemezképek.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

Az alábbi táblázat a nem támogatott operációs rendszerek:

|Operációs rendszer|Megjegyzések|
|-|-|
|Windows-ügyfél | Ügyféloldali operációs rendszerek (például Windows 7 és Windows 10-es) nem támogatottak.
|A Windows Server 2016 Nano Server | Nem támogatott.|

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációkra definíciója

Minden egyes naplózási Vendég konfiguráció futtatása szükséges két szabályzatdefiníció egy **DeployIfNotExists** és **AuditIfNotExists**. **DeployIfNotExists** a virtuális gép előkészítése a Vendég konfigurációs ügynök és a más összetevőkkel támogatására szolgál a [érvényesítési eszközök](#validation-tools).

A **DeployIfNotExists** szabályzatdefiníció ellenőrzi és javítja a következő elemek:

- Ellenőrizze a virtuális gép egy konfigurációs kiértékelheti, hogy hozzá lett rendelve. Ha nincs hozzárendelés nem található, a hozzárendelés beolvasása, és készítse elő a virtuális gép által:
  - A virtuális gép történő hitelesítése egy [identitás](../../../active-directory/managed-identities-azure-resources/overview.md)
  - A legújabb verziójának telepítése a **Microsoft.GuestConfiguration** bővítmény
  - Telepítés [érvényesítési eszközök](#validation-tools) és függőségei, szükség esetén

Egyszer a **DeployIfNotExists** megfelelő, akkor a **AuditIfNotExists** szabályzat-definíció a helyi érvényesítés eszközök segítségével határozza meg, a hozzárendelt konfigurációs hozzárendelés megfelelő-e, vagy Nem megfelelő. A fürtérvényesítési eszköz biztosít a Vendég konfigurációs ügyfél az eredményeket. Az ügyfél a Vendég a bővítmény elérhetővé teszi azokat a Vendég-konfigurációs erőforrás-szolgáltatón keresztül továbbítja az eredményeket.

Az Azure Policy használja a Vendég-konfigurációs erőforrás-szolgáltatók **complianceStatus** való megfelelőség jelentéséhez a tulajdonság a **megfelelőségi** csomópont. További információkért lásd: [megfelelőségi adatok](../how-to/getting-compliance-data.md).

> [!NOTE]
> Minden egyes vendég konfigurációs definíciós mind a **DeployIfNotExists** és **AuditIfNotExists** szabályzatdefiníciók léteznie kell.

Beépített Vendég konfigurációs szabályzatainak csoportra vonatkozó definíciókat használja a hozzárendelések kezdeményezések szerepelnek. A beépített kezdeményezés nevű *[előzetes verzió]: Naplózási jelszó biztonsági beállítások a Linux és Windows virtuális gépeken belül* 18 szabályzatokat tartalmaz. Hat **DeployIfNotExists** és **AuditIfNotExists** Windows és Linux-három pár párokat. Minden esetben a logika belül a definíció érvényesíti csak a cél operációs rendszer alapján értékeli ki a [felügyeletiházirend-szabálya](definition-structure.md#policy-rule) definíciója.

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](definition-structure.md)
- Felülvizsgálat [házirend hatások ismertetése](effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](../how-to/getting-compliance-data.md)
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/index.md) ismertető részt.