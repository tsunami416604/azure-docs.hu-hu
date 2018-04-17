---
title: DNS-zónák és a rekordok védelme |} Microsoft Docs
description: Hogyan védi a DNS-zónák és a Microsoft Azure DNS rekordhalmazok.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: a23f5fa296be6d883229d3810e0387224b6708ff
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS-zónák és rekordok védelme

DNS-zónák és rekordok a fontos erőforrásokhoz. A DNS-zónához vagy akár csak egyetlen DNS-rekord törlése azt eredményezheti, hogy a teljes szolgáltatáskimaradás.  Ezért fontos, hogy a kritikus DNS-zónák és rekordok védett véletlen vagy illetéktelen módosítások ellen.

Ez a cikk azt ismerteti, hogyan Azure DNS lehetővé teszi a DNS-zónák és a rekordok ilyen változások elleni védelmét.  Azure Resource Manager által biztosított két hatékony funkciókat érvénybe lépni: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) és [erőforrás zárolások](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy a részletes hozzáféréskezelést az Azure-felhasználók, csoportok és erőforrásokat. Az RBAC használ, meg lehet adni pontosan olyan mértékű hozzáférést a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Hogyan segít az RBAC-hozzáférés kezelése kapcsolatos további információkért lásd: [Mi az szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A "DNS-zóna közreműködői" szerepkör

A "DNS-zóna közreműködői" szerepkör a rendszer beépített Azure által biztosított DNS-erőforrások kezelése.  DNS-zóna közreműködői engedélyekkel hozzárendelése egy felhasználóhoz vagy csoporthoz lehetővé teszi, hogy a DNS-erőforrás, de nem bármilyen más típusú erőforrások kezelése a csoportot.

Tegyük fel, hogy az erőforrás csoport "myzones" Contoso Corporation öt zónák tartalmazza. A DNS-rendszergazda engedélyekkel "DNS-zóna közreműködői" erőforráscsoport, lehetővé teszi számára teljes hozzáférést adott DNS-zónák. Azt is elkerüli a szükségtelen engedélyeket ad, például a DNS-rendszergazda nem hozható létre, vagy állítsa le a virtuális gépek.

A legegyszerűbb RBAC engedélyek hozzárendelése [az Azure-portálon](../role-based-access-control/role-assignments-portal.md).  Az erőforráscsoport "Hozzáférés-vezérlés (IAM)" panel megnyitásához, majd kattintson a "Hozzáadás", majd válassza ki a "DNS-zóna közreműködői" szerepkör és válassza ki a szükséges felhasználók vagy csoportok olyan engedélyek megadásához.

![Erőforráscsoport szintjén RBAC az Azure-portálon](./media/dns-protect-zones-recordsets/rbac1.png)

Engedélyek is lehet [kap, az Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Az egyenértékű parancs egyben [elérhető az Azure parancssori felület](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zóna szintjét RBAC

Az Azure RBAC-szabályok előfizetés, egy erőforráscsoport vagy egy egyéni erőforrást lehet alkalmazni. Esetén az Azure DNS-ben, hogy az erőforrás egy egyéni DNS-zónát, vagy még egyedi rekordhalmaz lehet.

Tegyük fel, hogy az erőforrás "myzones" csoport tartalmazza a "contoso.com" zóna és a subzone "customers.contoso.com", amelyben a CNAME-rekordok létrejönnek az egyes felhasználói fiókokhoz.  A CNAME-rekordok kezelése használt fiókot hozzá kell rendelni rekordok csak a "customers.contoso.com" zónában való létrehozásához szükséges engedélyek, azt nem rendelkeznek hozzáféréssel a más zónákhoz.

Zónaszintű RBAC hozzárendelhető az Azure-portálon.  A zóna a "Hozzáférés-vezérlés (IAM)" panel megnyitásához, majd kattintson a "Hozzáadás", majd válassza ki a "DNS-zóna közreműködői" szerepkör és válassza ki a szükséges felhasználók vagy csoportok olyan engedélyek megadásához.

![DNS-zóna szintű RBAC az Azure-portálon](./media/dns-protect-zones-recordsets/rbac2.png)

Engedélyek is lehet [kap, az Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Az egyenértékű parancs egyben [elérhető az Azure parancssori felület](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>A rekordhalmaz RBAC szint

Azt is egy lépéssel további. Vegye figyelembe a levelezési rendszergazda a Contoso Corporation, hozzá kell férnie a "contoso.com" zóna tetején MX és TXT rekord.  Nem szükséges, ő hozzáférést minden olyan egyéb MX vagy TXT rekord vagy bármilyen más típusú rekordok.  Az Azure DNS lehetővé teszi a rekordhalmaz szintű engedélyek hozzárendelése a pontosan az azt jelzi, hogy a levelezési rendszergazdai hozzáférésre van szüksége.  A levelezési rendszergazda kap pontosan a vezérlő egyenként kell, és nem tud más módosítást.

Rekordhalmaz szintű RBAC engedélyek konfigurálható az Azure-portál, a "Felhasználók" gombra kattintva a rekordhalmaz panelen:

![A rekordhalmaz szint RBAC az Azure-portálon](./media/dns-protect-zones-recordsets/rbac3.png)

Rekordhalmaz szintű RBAC engedélyeket is lehet [kap, az Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Az egyenértékű parancs egyben [elérhető az Azure parancssori felület](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A beépített "DNS-zóna közreműködői" szerepkör lehetővé teszi, hogy a DNS-erőforrásrekordok teljes ellenőrzést. A rendszer is létre lehet hozni a saját felhasználói Azure szerepkörökhöz, akkor pontosabban megbízhatatlanná vezérlő.

Tekintse meg ismét a példát, amelyben egy olyan CNAME rekordot a zónában "customers.contoso.com" minden Contoso Corporation felhasználói fiók jön létre.  A CNAME kezeléséhez használt fiók engedélyt fognak CNAME rekordok csak kezelését.  Majd nem elvégezni a szükséges (például az MX-rekordok módosítása) más típusú rekordjainak módosítására és zóna szintű műveletek, például a zóna törlése.

A következő példa bemutatja egy egyéni biztonsági szerepkört definíciója csak CNAME-rekordok kezelése:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A műveletek tulajdonság határozza meg a következő DNS-specifikus engedélyeket:

* `Microsoft.Network/dnsZones/CNAME/*` teljes hozzáférést biztosít a CNAME-rekordok
* `Microsoft.Network/dnsZones/read` engedélyt ad a DNS-zónák olvasni, de nem módosíthatók, amely lehetővé teszi, hogy a zóna, ahol a CNAME létrehozása folyamatban van.

A fennmaradó műveletek másolja át a [DNS-zóna közreműködői beépített szerepkör](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Egyéni RBAC szerepkör alapján rekordhalmazok törlése közben továbbra is lehetővé teheti, hogy frissíthető nem hatékony ellenőrzése érdekében. Megakadályozza, hogy rekordhalmazok törlés alatt áll, de nem akadályozza meg azok már nem módosítható.  Engedélyezett módosítások hozzáadása és eltávolítása a bejegyzések a rekordhalmaz, beleértve az "empty" rekordhalmaz, hogy az összes rekord eltávolítását a tartalmazza. Ugyanaz, mintha egy DNS-feloldási szempontból a rekordhalmaz törlése azt.

Egyéni szerepkör-definíciók jelenleg nem lehet meghatározni az Azure-portálon. Egy egyéni biztonsági szerepkört a szerepkör-definíció alapján Azure PowerShell használatával hozhatók létre:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Is létrehozhatók az Azure parancssori felületen keresztül:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A szerepkör lehet hozzárendelni a beépített szerepkörök azonos módon az ebben a cikkben leírtak szerint.

Létrehozására, kezelésére és egyéni szerepkörök hozzárendelése kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Erőforrás-zárolások

Mellett RBAC Azure Resource Manager támogatja a más típusú biztonsági ellenőrzést, nevezetesen erőforrások "lock" lehetőséget. Ahol RBAC szabályok lehetővé teszik annak vezérléséhez a műveletek a megadott felhasználók és csoportok, erőforrás zárolások lesznek alkalmazva az erőforráshoz, és összes felhasználók és szerepkörök érvényben. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-lock-resources.md).

Erőforrás-zárolás két típusa van: **DoNotDelete** és **ReadOnly**. Ezek a DNS-zónát, vagy egy egyéni rekordhalmaz alkalmazhatók.  A következő szakaszok ismertetik a számos gyakori helyzetek, és hogyan őket támogató erőforrás zárolások.

### <a name="protecting-against-all-changes"></a>Minden változást elleni védelem

Megakadályozza a módosítását, végeznek, a zóna a csak olvasható zárolási vonatkozik.  Ez megakadályozza, hogy új rekordhalmazok létrehozása, és a meglévő rekordhalmazok módosítani vagy törlés alatt.

Az Azure-portálon zóna szintű erőforrás zárolások hozhatók létre.  A DNS-zóna paneljén kattintson a "Zárolások", majd "hozzáadása":

![Zóna szintű erőforrás zárolások az Azure-portálon](./media/dns-protect-zones-recordsets/locks1.png)

Zóna szintű erőforrás zárolások is Azure PowerShell használatával hozható létre:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Azure-erőforrás zárolásainak beállítása jelenleg nem támogatott az Azure parancssori felület használatával.

### <a name="protecting-individual-records"></a>Az egyes rekordok védelme

Egy meglévő DNS-rekordot szemben beállítása megelőzése érdekében a rekordhalmaz csak olvasható zárolást vonatkozik.

> [!NOTE]
> A DoNotDelete zárolási alkalmazása rekordhalmaz nincs hatékony ellenőrzése. Megakadályozza, hogy a rekordhalmaz törlését, de nem akadályozza meg, már nem módosítható.  Engedélyezett módosítások hozzáadása és eltávolítása a bejegyzések a rekordhalmaz, beleértve az "empty" rekordhalmaz, hogy az összes rekord eltávolítását a tartalmazza. Ugyanaz, mintha egy DNS-feloldási szempontból a rekordhalmaz törlése azt.

Rekordhalmaz szintű erőforrás zárolások is jelenleg csak Azure PowerShell használatával konfigurálhatók.  Az Azure-portálon vagy az Azure parancssori felület azok nem támogatottak.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Zóna törlés elleni védelem

Az Azure DNS-zóna törlése után a zónában minden rekordkészletet is törlődnek.  Ez a művelet nem vonható vissza.  A kritikus zóna véletlen törlése magában hordozza a jelentős üzleti hatással van.  Ezért nagyon fontos zóna véletlen törlés elleni védelem érdekében.

A DoNotDelete zárolási alkalmazása a zóna megakadályozza, hogy a zóna törlés alatt áll.  Azonban mivel zárolások gyermekszintű erőforrása által örökölt, is megakadályozza, hogy bármely rekordhalmazok törlését, a zónában, esetlegesen nemkívánatos.  Ezenkívül lásd: a fenti megjegyzést, ez a beállítás is hatástalan mivel rekordok továbbra is távolíthatók el a meglévő rekordhalmazok.

Alternatív megoldásként vegye figyelembe a DoNotDelete zárolási alkalmazása egy rekordot a zónában, például a SOA típusú rekordhalmaz.  A zóna a rekordhalmazok is törlése nélkül nem lehet törölni, mivel ez véd zóna törlése, miközben továbbra is lehetővé teszi a rekordhalmazok szabadon lehet módosítani a zónán belül. Ha törli a zóna tett kísérlet, Azure Resource Manager észleli, ez is törölni a SOA típusú rekordkészlet, és blokkolja a hívást, mert a SOA zárolva van.  Nincs rekordhalmazok törlődnek.

A következő PowerShell-parancsot a megadott zónában SOA típusú rekordjának elleni DoNotDelete zárolást hoz létre:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Úgy is, hogy a zóna véletlen törlés annak biztosítása érdekében a következő operátor egy egyéni biztonsági szerepkört használatával, és a zónák kezelése használt szolgáltatásfiókokat nem rendelkezik engedélyekkel törlése zóna. Ha törölni szeretne egy zónát, kényszerítheti a kétlépéses törlési, első támogatást nyújtó zóna törlése engedélyek (a hatókörben zóna, törölje a nem megfelelő zónát megelőzése érdekében), második törölni a zónát.

Ez a második megközelítés azzal az előnnyel jár, a működését, az összes olyan zónára, ezek a fiókok, anélkül, hogy meg kellene jegyezni a zárolások létrehozásához érhetők el. A hátránya, hogy minden zóna törlési engedéllyel, például az előfizetés tulajdonosa rendelkező fiókok véletlenül továbbra is törölheti a kritikus zóna rendelkezik.

Akkor lehet egy időben, a DNS-zóna védelemre védelemmel az olyan jellegű megközelítés mindkét megközelítés - erőforrás zárolások és egyéni szerepkör - használatát.

## <a name="next-steps"></a>További lépések

* Az RBAC használatáról további információk: [Ismerkedés az Azure-portálon kezelési](../role-based-access-control/overview.md).
* Működő erőforrás zárral kapcsolatos további információkért lásd: [erőforrások az Azure Resource Manager zárolása](../azure-resource-manager/resource-group-lock-resources.md).

