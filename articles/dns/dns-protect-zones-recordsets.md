---
title: DNS-zónák és rekordok védelme – Azure DNS
description: Ebben a képzési tervben a DNS-zónák és-rekordhalmazok védelme Microsoft Azure DNS-ben című cikkben olvasható.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: allensu
ms.openlocfilehash: c87f9d51c69c4f4d330862e83e5cc8e8e849a988
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969026"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS-zónák és-rekordok elleni védelem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A DNS-zónák és-rekordok kritikus erőforrások. Egy DNS-zóna törlése, vagy akár csak egyetlen DNS-rekord is eredményezhet a szolgáltatás teljes kimaradását.  Ezért fontos, hogy a kritikus DNS-zónák és-rekordok védve legyenek a jogosulatlan vagy véletlen változásokkal szemben.

Ez a cikk azt ismerteti, hogyan teszi lehetővé a Azure DNS a DNS-zónák és-rekordok védelemét az ilyen változásokkal szemben.  A Azure Resource Manager által biztosított két hatékony biztonsági funkciót alkalmazzuk: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) és [erőforrás-zárolások](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú Access Control (RBAC) részletes hozzáférés-kezelést tesz lehetővé az Azure-felhasználók,-csoportok és-erőforrások számára. A RBAC használatával pontosan megadhatja, hogy a felhasználóknak milyen hozzáférési jogosultságokkal kell elvégezniük a munkájukat. További információ arról, hogyan segít a RBAC a hozzáférés kezelésében: [Mi a szerepköralapú Access Control](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A DNS-zóna közreműködői szerepköre

A DNS-zóna közreműködő szerepköre az Azure által a DNS-erőforrások kezeléséhez biztosított beépített szerepkör.  A DNS-zóna közreműködői engedélyeinek egy felhasználóhoz vagy csoporthoz rendelése lehetővé teszi a csoport számára a DNS-erőforrások kezelését, de más típusú erőforrásokat nem.

Tegyük fel például, hogy az erőforráscsoport *myzones* öt zónát tartalmaz a contoso Corporation számára. A DNS-rendszergazda DNS-zóna közreműködői engedélyeinek megadása az adott erőforráscsoporthoz, lehetővé teszi a DNS-zónák teljes körű felügyeletét. Emellett megakadályozza a szükségtelen engedélyek megadását, például a DNS-rendszergazda nem tudja létrehozni vagy leállítani a Virtual Machines.

A RBAC engedélyek hozzárendelésének legegyszerűbb módja [a Azure Portalon keresztül](../role-based-access-control/role-assignments-portal.md)történik.  Nyissa meg az erőforráscsoport **hozzáférés-vezérlés (iam)** elemét, majd válassza a **Hozzáadás**lehetőséget, majd válassza ki a **DNS-zóna közreműködői** szerepkört, és válassza ki a szükséges felhasználókat vagy csoportokat az engedélyek megadásához.

![Erőforráscsoport-szintű RBAC a Azure Portal keresztül](./media/dns-protect-zones-recordsets/rbac1.png)

[Az engedélyek a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zóna szintű RBAC

Az Azure RBAC-szabályok egy előfizetésre, egy erőforráscsoport vagy egy adott erőforrásra is alkalmazhatók. Azure DNS esetén az erőforrás lehet egy egyedi DNS-zóna, vagy akár egy különálló rekordazonosító is.

Tegyük fel például, hogy az erőforráscsoport *myzones* tartalmazza a *contoso.com* zónát és egy alzóna- *customers.contoso.com* , amelyben minden egyes ügyfél fiókhoz létre kell hozni CNAME rekordot.  A CNAME rekordok kezeléséhez használt fióknak engedélyeket kell rendelnie ahhoz, hogy csak a *customers.contoso.com* zónában hozzon létre rekordokat, ezért nem szabad a többi zónához hozzáférni.

A zóna szintű RBAC engedélyek a Azure Portalon keresztül adhatók meg.  Nyissa meg a zóna **hozzáférés-vezérlés (iam)** elemét, majd válassza a **Hozzáadás**lehetőséget, majd válassza ki a **DNS-zóna közreműködői** szerepkört, és válassza ki a szükséges felhasználókat vagy csoportokat az engedélyek megadásához.

![DNS-zóna szintjének RBAC a Azure Portal keresztül](./media/dns-protect-zones-recordsets/rbac2.png)

[Az engedélyek a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Rögzítési szint RBAC

Egy lépéssel tovább mehetünk. Vegye fontolóra a contoso Corporation levelezési rendszergazdáját, akinek hozzáférést kell biztosítania az MX és a TXT rekordokhoz a contoso.com zóna csúcsán.  Nincs szüksége más MX-vagy TXT-rekordokhoz vagy más típusú rekordokhoz való hozzáférésre.  Azure DNS lehetővé teszi, hogy a rekordhalmaz szintjén rendeljen engedélyeket, hogy pontosan azok a rekordok legyenek, amelyekre a levelezési rendszergazdának hozzá kell férnie.  A levelezési rendszergazda pontosan a szükséges vezérléssel rendelkezik, és nem tudja végrehajtani a többi módosítást.

A Record-set szintű RBAC engedélyek a Azure Portalon keresztül konfigurálhatók, a rekordok beállítása lapon a **felhasználók** gomb használatával:

![Rögzített szintű RBAC a Azure Portal használatával](./media/dns-protect-zones-recordsets/rbac3.png)

A Record-set szintű RBAC engedélyek [a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md)is megadhatók:

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Az egyenértékű parancs az [Azure CLI-n keresztül is elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A beépített DNS-zóna közreműködői szerepkör lehetővé teszi a DNS-erőforrások teljes körű felügyeletét. Saját Azure-szerepköröket is létrehozhat, így még finomabban szabályozható.

Gondolja át újra azt a példát, amelyben a zóna *customers.contoso.com* található CNAME rekord létrejön minden contoso Corporation-ügyfél fiókhoz.  A CNAME rekordok kezeléséhez használt fióknak engedélyt kell adni a csak CNAME rekordok kezelésére.  Ezután nem tudja módosítani a más típusú rekordokat (például az MX-rekordok módosítását), vagy olyan zóna szintű műveleteket hajt végre, mint például a zóna törlése.

Az alábbi példa egy egyéni szerepkör-definíciót mutat be kizárólag a CNAME rekordok kezelésére:

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

A műveletek tulajdonság a következő DNS-specifikus engedélyeket határozza meg:

* `Microsoft.Network/dnsZones/CNAME/*` teljes hozzáférést biztosít a CNAME-rekordokhoz
* `Microsoft.Network/dnsZones/read` engedélyt ad a DNS-zónák olvasására, de nem módosítja őket, így megtekintheti azt a zónát, amelyben a CNAME-t létrehozták.

A fennmaradó műveleteket a rendszer a [DNS-zóna közreműködői beépített szerepkörből](../role-based-access-control/built-in-roles.md#dns-zone-contributor)másolja.

> [!NOTE]
> Egyéni RBAC-szerepkör használata a rekordhalmazok törlésének megakadályozása érdekében, miközben továbbra is lehetővé teszi a frissítések frissítését, nem érvényes vezérlő. Megakadályozza a rekordhalmazok törlését, de nem akadályozza meg őket a módosításban.  Az engedélyezett módosítások közé tartoznak a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását is, ha üres rekordhalmazt szeretne hagyni. Ennek ugyanaz a hatása, mint a rekord DNS-feloldási nézőpontból való törlése.

Az egyéni szerepkör-definíciók jelenleg nem definiálhatók a Azure Portalon keresztül. A szerepkör-definíción alapuló egyéni szerepkört Azure PowerShell használatával lehet létrehozni:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Az Azure CLI használatával is létrehozható:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A szerepkör ezután ugyanúgy hozzárendelhető a beépített szerepkörökhöz, mint a jelen cikk korábbi részében leírtak szerint.

Az egyéni szerepkörök létrehozásával, kezelésével és hozzárendelésével kapcsolatos további információkért lásd: [Egyéni szerepkörök az Azure RBAC-ben](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Erőforrás-zárolások

A RBAC mellett a Azure Resource Manager támogatja egy másik típusú biztonsági vezérlést is, amely lehetővé teszi az erőforrások zárolását. Ahol a RBAC-szabályok lehetővé teszik az egyes felhasználók és csoportok műveleteinek szabályozását, az erőforrás-zárolások érvénybe lépnek, és az összes felhasználóra és szerepkörre érvényesek. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

Két típusú erőforrás-zárolás létezik: **CanNotDelete** és **readonly**. Ezek egy DNS-zónára vagy egy különálló készletre is alkalmazhatók.  Az alábbi szakaszok számos gyakori forgatókönyvet ismertetnek, valamint azt, hogyan támogatják őket az erőforrás-zárolások használatával.

### <a name="protecting-against-all-changes"></a>Védelem az összes módosítás ellen

A módosítások elkerülése érdekében a rendszer írásvédett zárolást alkalmaz a zónára.  Ez megakadályozza, hogy új rekordhalmazok jöjjenek létre, és a meglévő rekordhalmazok módosítva vagy törölve legyenek.

A zóna szintű erőforrás-zárolások a Azure Portal használatával hozhatók létre.  A DNS-zóna lapon válassza a **zárolások**, majd a **+ Hozzáadás**lehetőséget:

![A zóna szintű erőforrások zárolása a Azure Portal keresztül](./media/dns-protect-zones-recordsets/locks1.png)

A zóna szintű erőforrások zárolása Azure PowerShell használatával is létrehozható:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Az Azure-erőforrások zárolásának konfigurálása jelenleg nem támogatott az Azure CLI-n keresztül.

### <a name="protecting-individual-records"></a>Egyéni rekordok védelme

Ha meg szeretné akadályozni, hogy egy meglévő DNS-rekordot módosítson a módosítással, alkalmazza a írásvédett zárolást a rekordra.

> [!NOTE]
> A CanNotDelete zárolásának egy bejegyzéstípusra való alkalmazása nem hatékony vezérlő. Megakadályozza a rekordok törlését, de nem akadályozza meg a módosítást.  Az engedélyezett módosítások közé tartoznak a rekordok hozzáadása és eltávolítása a rekordhalmazból, beleértve az összes rekord eltávolítását is, ha üres rekordhalmazt szeretne hagyni. Ennek ugyanaz a hatása, mint a rekord DNS-feloldási nézőpontból való törlése.

A set szintű erőforrás-zárolások jelenleg csak Azure PowerShell használatával konfigurálhatók.  Nem támogatottak a Azure Portal vagy az Azure CLI-ben.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>A zónák törlésének védelme

Amikor töröl egy zónát Azure DNSban, a zónában lévő összes rekordhalmaz is törlődik.  Ez a művelet nem vonható vissza.  Ha véletlenül töröl egy kritikus zónát, azzal jelentős hatással lehet az üzleti tevékenységre.  Ezért nagyon fontos a véletlen zónák törlésével szembeni védelem.

A CanNotDelete zárolásának zónában való alkalmazása megakadályozza a zóna törlését.  Mivel azonban a zárolásokat a gyermek erőforrások öröklik, ez megakadályozza a zónában lévő rekordhalmazok törlését is, ami esetleg nemkívánatos lehet.  Továbbá a fenti megjegyzésben leírtak szerint az is hatástalan, mivel a rekordok továbbra is eltávolíthatók a meglévő rekordhalmazokból.

Alternatív megoldásként érdemes lehet egy CanNotDelete-zárolást alkalmazni egy olyan rekordra, amely a zónában van, például a SOA-rekord készlete.  Mivel a zóna nem törölhető a rekordhalmazok törlése nélkül, ez védelmet biztosít a zónák törlésével szemben, miközben továbbra is engedélyezi a zónán belüli rekordhalmazok szabadon történő módosítását. Ha kísérlet történt a zóna törlésére, Azure Resource Manager észleli, hogy a SOA-rekordot is törli, és blokkolja a hívást, mert a SOA zárolva van.  Nincsenek rekordhalmazok törölve.

A következő PowerShell-parancs egy CanNotDelete-zárolást hoz létre a megadott zóna SOA-rekordjával:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

A zónák véletlen törlésének egy másik módja egy egyéni szerepkör használata annak biztosítására, hogy a zónák kezeléséhez használt operátori és szolgáltatási fiókok ne rendelkezzenek a zóna törléséhez szükséges engedélyekkel. Ha törölni kell egy zónát, egy kétlépéses törlést kell kikényszeríteni, először adja meg a zóna törlésére vonatkozó engedélyeket (a zóna hatókörében, a nem megfelelő zóna törlésének megakadályozásához) és a másodikat a zóna törléséhez.

Ennek a második megközelítésnek az az előnye, hogy az adott fiókok által elért összes zónához működik, anélkül, hogy meg kellene emlékeznie a zárolások létrehozására. Ez a hátránya, hogy a zónák törlésére vonatkozó engedélyekkel rendelkező fiókok (például az előfizetés tulajdonosa) továbbra is véletlenül törölhetik a kritikus zónákat.

Mindkét módszert – az erőforrás-zárolásokat és az egyéni szerepköröket – egyszerre is használhatja a DNS-zónák védelmének mélyreható megközelítésével.

## <a name="next-steps"></a>Következő lépések

* További információ a RBAC használatáról: [a Azure Portal hozzáférés-kezelésének első lépései](../role-based-access-control/overview.md).
* További információ az erőforrás-zárolások használatáról: [erőforrások zárolása Azure Resource Managersal](../azure-resource-manager/management/lock-resources.md).
