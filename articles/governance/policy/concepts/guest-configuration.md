---
title: Megismerheti a tartalmát, a virtuális gépek naplózása
description: Ismerje meg, hogyan Azure Policy segítségével Vendég konfigurációs naplózási beállítások egy Azure virtuális gépen belül.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c11d6519986cf7a0e70d1fe004ef527c3df247d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277723"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy Vendég konfiguráció ismertetése

Naplózás mellett és [szervizelés](../how-to/remediate-resources.md) is, a naplózási beállítások egy virtuális gépen az Azure-erőforrások, az Azure Policy. A Vendég Configuration bővítmény és az ügyfél az érvényesítést végzi. A bővítményt, az ügyfélen, érvényesíti a konfigurációt az operációs rendszer, alkalmazás-konfigurációs vagy jelenléte, környezeti beállítások és beállításait.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Bővítmény és az ügyfél

A naplózási beállítások egy virtuális gépen egy [virtuálisgép-bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van. A bővítmény letölti a megfelelő szabályzat-hozzárendelés és a megfelelő konfiguráció definíciója.

### <a name="register-guest-configuration-resource-provider"></a>Vendég-konfigurációs erőforrás-szolgáltató regisztrálása

Vendég-konfiguráció használata előtt regisztrálnia kell az erőforrás-szolgáltató. A portálon keresztül vagy a Powershellen keresztül lehet regisztrálni. Az erőforrás-szolgáltató automatikusan regisztrálva van, ha a Vendég-konfigurációs szabályzat hozzárendelése a portálon keresztül történik.

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

### <a name="validation-frequency"></a>Ellenőrzés gyakorisága

Új tartalom 5 percenként ellenőrzi a Vendég konfigurációs ügyfél. Miután egy Vendég-hozzárendelés érkezik, a beállításokat egy 15 perces időközönként ellenőrzi. Eredmények küldése a Vendég-konfigurációs erőforrás-szolgáltató, amint az ellenőrzés befejeződött. Ha egy szabályzat [értékelési eseményindító](../how-to/get-compliance-data.md#evaluation-triggers) történik, a számítógép állapotának írása a Vendég-konfigurációs erőforrás-szolgáltató. Ennek hatására az Azure Policy az Azure Resource Manager-tulajdonságok kiértékelése. Egy igény szerinti szabályzat-kiértékelés átveszi a Vendég-konfigurációs erőforrás-szolgáltató a legújabb értékeket. Azonban ez nem következik be egy új naplózási a konfiguráció a virtuális gépen.

### <a name="supported-client-types"></a>Támogatott ügyfél típusú

Az alábbi táblázat az Azure-rendszerképek támogatott operációs rendszerek listája látható:

|Közzétevő|Name (Név)|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14.04-es, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenterhez, a 2012 R2 Datacenter, a 2016 Datacenter, a 2019 adatközpont|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Vendég konfigurációs van egy támogatott operációs rendszert futtató csomópontok naplózására.  Ha szeretné egy egyéni rendszerképet használó virtuális gépek naplózása, szeretné-e ismétlődő a **DeployIfNotExists** definíció és módosíthatja a **Ha** című szakaszt a lemezkép tulajdonságai.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

A Windows Server Nano Server bármelyik verziója nem támogatott.

### <a name="guest-configuration-extension-network-requirements"></a>Vendég Configuration bővítmény hálózati követelmények

Az Azure-ban a Vendég-konfigurációs erőforrás-szolgáltató kommunikálni, virtuális gépeknél szükség van az Azure-adatközpontok porton a kimenő hozzáférést **443-as**. Ha egy privát virtuális hálózatot használ, az Azure-ban, és nem engedélyezi a kimenő forgalom, kivételeket kell konfigurálnia [hálózati biztonsági csoport](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) szabályokat. Jelenleg a szolgáltatás címke az Azure Vendég Szabályzatkonfiguráció nem létezik.

IP-cím listák, letöltheti [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ez a fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat. Csak kell az IP-címek a régióban, a virtuális gépek telepítve vannak-e kimenő hozzáférésének engedélyezéséhez.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlt a Microsoft Azure-adatközpontok az által használt IP-címtartományok listája. A fájl a compute, SQL és storage tartományokat tartalmaz.
> A frissített hetente tesznek közzé. A fájl a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat tükrözi. A fájlban megjelenő új tartományokat legalább egy hétig nem használják az adatközpontokban.
> Célszerű letölteni az új XML-fájlt minden héten. Ezután frissítse a helyet, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy ez a fájl minden hónap első hetében Azure címterületek a Border Gateway Protocol (BGP) hirdetés frissítésére használatos.

## <a name="guest-configuration-definition-requirements"></a>Vendég konfigurációkra definíciója

Minden egyes naplózási Vendég konfiguráció futtatása szükséges két szabályzatdefiníció egy **DeployIfNotExists** definíció és a egy **naplózási** definíciója. A **DeployIfNotExists** meghatározása a virtuális gép előkészítése a Vendég konfigurációs ügynök és a más összetevőkkel támogatására szolgál a [érvényesítési eszközök](#validation-tools).

A **DeployIfNotExists** szabályzatdefiníció ellenőrzi és javítja a következő elemek:

- Ellenőrizze a virtuális gép egy konfigurációs kiértékelheti, hogy hozzá lett rendelve. Ha nincs hozzárendelés nem található, a hozzárendelés beolvasása, és készítse elő a virtuális gép által:
  - A virtuális gép történő hitelesítése egy [identitás](../../../active-directory/managed-identities-azure-resources/overview.md)
  - A legújabb verziójának telepítése a **Microsoft.GuestConfiguration** bővítmény
  - Telepítés [érvényesítési eszközök](#validation-tools) és függőségei, szükség esetén

Ha a **DeployIfNotExists** hozzárendelés egy nem megfelelő, egy [javítási feladat](../how-to/remediate-resources.md#create-a-remediation-task) is használható.

Miután a **DeployIfNotExists** hozzárendelés egy megfelelő, a **naplózási** szabályzat-hozzárendelés a helyi érvényesítés eszközök segítségével határozza meg, ha a konfiguráció hozzárendelés-e a megfelelő vagy nem megfelelő.
A fürtérvényesítési eszköz biztosít a Vendég konfigurációs ügyfél az eredményeket. Az ügyfél a Vendég a bővítmény elérhetővé teszi azokat a Vendég-konfigurációs erőforrás-szolgáltatón keresztül továbbítja az eredményeket.

Az Azure Policy használja a Vendég-konfigurációs erőforrás-szolgáltatók **complianceStatus** való megfelelőség jelentéséhez a tulajdonság a **megfelelőségi** csomópont. További információkért lásd: [megfelelőségi adatok](../how-to/getting-compliance-data.md).

> [!NOTE]
> Minden egyes vendég konfigurációs definíciós mind a **DeployIfNotExists** és **naplózási** szabályzatdefiníciók léteznie kell.

Beépített Vendég konfigurációs szabályzatainak csoportra vonatkozó definíciókat használja a hozzárendelések kezdeményezések szerepelnek. A beépített kezdeményezés nevű *[előzetes verzió]: Naplózási jelszó biztonsági beállítások a Linux és Windows virtuális gépeken belül* 18 szabályzatokat tartalmaz. Hat **DeployIfNotExists** és **naplózási** Windows és Linux-három pár párokat. Minden esetben a logika belül a definíció érvényesíti csak a cél operációs rendszer alapján értékeli ki a [felügyeletiházirend-szabálya](definition-structure.md#policy-rule) definíciója.

## <a name="client-log-files"></a>Ügyfelek naplófájljai

A Vendég Configuration bővítmény írja a naplófájlokat a következő helyekre:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Ahol `<version>` a jelenlegi verziószám hivatkozik.

## <a name="guest-configuration-samples"></a>Vendég-konfigurációs minták

Vendég Szabályzatkonfiguráció-minták az alábbi helyeken érhetők el:

- [A minták index - Vendég-konfiguráció](../samples/index.md#guest-configuration)
- [Az Azure Policy-minták GitHub-adattárat](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy minták](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [megfelelőségi adatok](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md).
- Tekintse át a felügyeleti csoport van [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../../management-groups/index.md).
