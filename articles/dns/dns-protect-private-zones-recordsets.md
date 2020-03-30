---
title: Magánjellegű DNS-zónák és -rekordok védelme – Azure DNS
description: Ebben a tanulási útvonalon ismerkedhet meg a microsoft azure DNS-ben a magánDNS-zónák és rekordkészletek védelmével.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: da94c9aa97483ab5792e917d6a8f60f846b0722e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473063"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>A magándns-zónák és -rekordok védelme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A magán DNS-zónák és rekordok kritikus erőforrások. Dns-zóna vagy egyetlen DNS-rekord törlése szolgáltatáskimaradást eredményezhet. Fontos, hogy a DNS-zónák és -rekordok védve legyenek a jogosulatlan vagy véletlen módosításokkal szemben.

Ez a cikk bemutatja, hogy az Azure DNS hogyan teszi lehetővé a privát DNS-zónák és rekordok védelmét az ilyen változások ellen.  Az Azure Resource Manager által biztosított két hatékony értékpapír-funkciót alkalmazunk: [szerepköralapú hozzáférés-vezérlést](../role-based-access-control/overview.md) és [erőforrászárolást](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi a részletes hozzáférés-kezelés az Azure-felhasználók, csoportok és erőforrások. Az RBAC segítségével biztosíthatja a felhasználók számára szükséges hozzáférési szintet. Ha többet szeretne tudni arról, hogy az RBAC hogyan segíti a hozzáférés kezelését, [olvassa el a Mi a szerepköralapú hozzáférés-vezérlés.](../role-based-access-control/overview.md)

### <a name="the-private-dns-zone-contributor-role"></a>A privát DNS-zóna közreműködői szerepkör

A magánDNS-zóna közreműködői szerepkör beépített szerepkör a magánDNS-erőforrások kezeléséhez. Ez a felhasználóra vagy csoportra alkalmazott szerepkör lehetővé teszi a magánDNS-erőforrások kezelését.

A *myPrivateDNS* erőforráscsoport öt zónát tartalmaz a Contoso Corporation számára. A DNS-rendszergazda privát DNS-zóna közreműködői engedélyeinek megadásával lehetővé teszi az adott DNS-zónák teljes körű ellenőrzését. Elkerüli a szükségtelen engedélyek megadását. A DNS-rendszergazda nem hozhat létre és nem állíthat le virtuális gépeket.

Az RBAC-engedélyek hozzárendelésének legegyszerűbb módja [az Azure Portalon keresztül](../role-based-access-control/role-assignments-portal.md)történik.  

Nyissa meg az erőforráscsoport **hozzáférés-vezérlését ( IAM),** válassza a **Hozzáadás**lehetőséget, majd válassza a **Privát DNS-zóna közreműködői** szerepkört. Válassza ki az engedélyek megadásához szükséges felhasználókat vagy csoportokat.

![Erőforráscsoport szintű RBAC az Azure portalon keresztül](./media/dns-protect-private-zones-recordsets/rbac1.png)

Az Azure [PowerShell használatával is adhatók engedélyek:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

Az ezzel egyenértékű parancs [az Azure CLI-n keresztül](../role-based-access-control/role-assignments-cli.md)is elérhető:

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-rbac"></a>Privát zóna szintű RBAC

Az Azure RBAC-szabályok egy előfizetésre, egy erőforráscsoportra vagy egy adott erőforrásra is alkalmazhatók. Ez az erőforrás lehet egyedi DNS-zóna vagy egyéni rekordkészlet.

A *myPrivateDNS* erőforráscsoport például tartalmazza a *private.contoso.com* zónát és egy alzónát *customers.private.contoso.com.* A CNAME rekordok minden vevőszámlához létrejönnek. A CNAME rekordok kezeléséhez használt rendszergazdai fiók engedélyeket kap a *customers.private.contoso.com* zónában lévő rekordok létrehozásához. A fiók csak *customers.private.contoso.com* kezelhető.

Zónaszintű RBAC-engedélyek az Azure Portalon keresztül adhatók meg.  Nyissa meg a zóna **hozzáférés-vezérlését (IAM),** válassza a **Hozzáadás**lehetőséget, majd jelölje ki a **Privát DNS-zóna közreműködői** szerepkört. Válassza ki az engedélyek megadásához szükséges felhasználókat vagy csoportokat.

![Dns Zone szintű RBAC az Azure portalon keresztül](./media/dns-protect-private-zones-recordsets/rbac2.png)

Az Azure [PowerShell használatával is adhatók engedélyek:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

Az ezzel egyenértékű parancs [az Azure CLI-n keresztül](../role-based-access-control/role-assignments-cli.md)is elérhető:

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-rbac"></a>Rekordkészlet-szint RBAC

Az engedélyek a rekordhalmaz szintjén lesznek alkalmazva.  A felhasználó szabályozhatja a szükséges bejegyzéseket, és nem tud más módosításokat végrehajtani.

A rekordkészlet-szintű RBAC-engedélyek konfigurálhatók az Azure Portalon keresztül a **hozzáférés-vezérlés (IAM)** gombbal a rekordkészlet-lapon:

![Rekordhalmazszintű RBAC az Azure portalon keresztül](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Rekordhalmazszintű RBAC az Azure portalon keresztül](./media/dns-protect-private-zones-recordsets/rbac4.png)

Rekord-készlet szintű RBAC engedélyeket is [meg lehet adni az Azure PowerShell használatával:](../role-based-access-control/role-assignments-powershell.md)

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

Az ezzel egyenértékű parancs [az Azure CLI-n keresztül](../role-based-access-control/role-assignments-cli.md)is elérhető:

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A beépített privát DNS-zóna közreműködői szerepkör lehetővé teszi a DNS-erőforrások teljes körű vezérlését. Lehetőség van saját egyéni Azure-szerepkörök létrehozására, hogy finomabb szemcsés vezérlést biztosítson.

A CNAMEs kezelésére használt fiók csak a CNAME rekordok kezelésére jogosult. A fiók nem tudja módosítani más típusú rekordokat. A fiók nem tud zónaszintű műveleteket, például zónatörlést végezni.

A következő példa csak a CNAME rekordok kezelésére szolgáló egyéni szerepkör-definíciót mutat be:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
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
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A Műveletek tulajdonság a következő DNS-specifikus engedélyeket határozza meg:

* `Microsoft.Network/privateDnsZones/CNAME/*`teljes ellenőrzést biztosít a CNAME rekordok felett
* `Microsoft.Network/privateDNSZones/read`engedélyt ad a DNS privát zónáinak olvasására, de nem módosítja azokat, lehetővé téve, hogy láthassa azt a zónát, amelyben a CNAME létrejön.

> [!NOTE]
> Az egyéni RBAC szerepkör használata a rekordkészletek törlésének megakadályozására, miközben továbbra is lehetővé teszi azok frissítését, nem hatékony vezérlő. Megakadályozza a rekordhalmazok törlését, de a módosításukat nem.  Az engedélyezett módosítások közé tartozik a rekordok hozzáadása és eltávolítása a rekordkészletből, beleértve az összes rekord eltávolítását, hogy üres rekordhalmazt hagyjon. Ennek ugyanaz a hatása, mint a rekordkészlet DNS-feloldási szempontból történő törlése.

Egyéni szerepkör-definíciók jelenleg nem határozható meg az Azure Portalon keresztül. A szerepkör-definíción alapuló egyéni szerepkör az Azure PowerShell használatával hozható létre:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Az Azure CLI-n keresztül is létrehozható:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

A szerepkör ezután ugyanúgy rendelhető hozzá, mint a beépített szerepkörök, ahogy azt a cikk korábbi leírtak szerint.

Az egyéni szerepkörök létrehozásáról, kezeléséről és hozzárendeléséről az [Egyéni szerepkörök az Azure RBAC-ban](../role-based-access-control/custom-roles.md)című témakörben talál további információt.

## <a name="resource-locks"></a>Erőforrás-zárolások

Az Azure Resource Manager támogatja a biztonsági ellenőrzés egy másik típusát, az erőforrások zárolásának lehetőségét. Az erőforrászárolások az erőforrásra vonatkoznak, és minden felhasználóés szerepkör esetében érvényesek. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

Az erőforrás-zárolásnak két típusa van: **CanNotDelete** és **ReadOnly**. Ezek a zárolási típusok alkalmazhatók egy privát DNS-zónára vagy egy egyéni rekordkészletre.  A következő szakaszok számos gyakori forgatókönyvet, és hogyan támogatják őket az erőforrás-zárolások használatával.

### <a name="protecting-against-all-changes"></a>Védelem minden változás ellen

A módosítások elkerülése érdekében alkalmazzon csak olvasható zárolást a zónára. Ez a zárolás megakadályozza az új rekordkészletek létrehozását, valamint a meglévő rekordkészletek módosítását vagy törlését.

Zónaszintű erőforrás-zárolások az Azure Portalon keresztül hozhatók létre.  A DNS-zóna lapon válassza a **Zárolások**lehetőséget, majd a **+Hozzáadás**lehetőséget:

![Zónaszintű erőforrászárolások az Azure portalon keresztül](./media/dns-protect-private-zones-recordsets/locks1.png)

Zónaszintű erőforrászárolások az [Azure PowerShellen](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock?view=latest)keresztül is létrehozhatók:

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

Az ezzel egyenértékű parancs [az Azure CLI-n keresztül](https://docs.microsoft.com/cli/azure/lock?view=azure-cli-latest#az-lock-create)is elérhető:

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Az egyes bejegyzések védelme

Ha meg szeretné akadályozni, hogy egy meglévő DNS-rekordkészlet módosításellen edzzen, alkalmazzon csak olvasható zárolást a rekordkészletre.

> [!NOTE]
> A CanNotDelete zárolás rekordkészletre történő alkalmazása nem hatékony vezérlő. Megakadályozza a rekordkészlet törlését, de a módosítását nem.  Az engedélyezett módosítások közé tartozik a rekordok hozzáadása és eltávolítása a rekordkészletből, beleértve az összes rekord eltávolítását, hogy üres rekordhalmazt hagyjon. Ennek ugyanaz a hatása, mint a rekordkészlet DNS-feloldási szempontból történő törlése.

Rekordkészlet-szintű erőforrás-zárolások jelenleg csak az Azure PowerShell használatával konfigurálhatók.  Ezek nem támogatottak az Azure Portalon vagy az Azure CLI-ben.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Védelem a zóna törlésével szemben

Amikor egy zóna törlődik az Azure DNS-ben, a zónában lévő összes rekordkészlet törlődik.  Ezt a műveletet nem lehet visszavonni. A kritikus zóna véletlen törlése jelentős üzleti hatással járhat.  Fontos, hogy megvédje a véletlen zóna törlés.

A CanNotDelete zárolás zónára történő alkalmazása megakadályozza a zóna törlését. A zárolásokat a gyermekerőforrások öröklik. A zárolás megakadályozza a zóna rekordkészletei törlését. A fenti feljegyzésben leírtak szerint hatástalan, mivel a rekordok továbbra is eltávolíthatók a meglévő rekordhalmazokból.

Másik lehetőségként alkalmazzon CanNotDelete zárolást a zónában lévő rekordhalmazra, például a SOA rekordkészletre. A zóna nem törlődik a rekordhalmazok törlése nélkül. Ez a zárolás védelmet nyújt a zóna törlésével szemben, miközben továbbra is lehetővé teszi a zónán belüli rekordkészletek szabadon történő módosítását. Ha kísérletet tesz a zóna törlésére, az Azure Resource Manager észleli ezt az eltávolítást. Az eltávolítás is törli a SOA rekordkészletet, az Azure Resource Manager blokkolja a hívást, mert a SOA zárolva van.  A nem törlődik rekordhalmaz.

A következő PowerShell-parancs canNotdelete zárolást hoz létre az adott zóna SOA rekordja ellen:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
A véletlen zónatörlés megakadályozására egy másik lehetőség egy egyéni szerepkör használata. Ez a szerepkör biztosítja, hogy a zónák kezeléséhez használt fiókok nem rendelkeznek zónatörlési engedélyekkel. 

Ha törölnie kell egy zónát, kétlépésben kényszerítheti a törlést:

 - Először is, a támogatási zóna törlési engedélyei
 - Másodszor, adjon engedélyeket a zóna törlésére.

Az egyéni szerepkör a fiókok által elért összes zónában működik. A zónatörlési engedélyekkel rendelkező fiókok, például az előfizetés tulajdonosa, továbbra is véletlenül törölhetnek egy zónát.

Mindkét megközelítés – erőforrászárolás okán és egyéni szerepkörök – egy időben, a DNS-zóna védelmének mélyreható védelmeként is használható.

## <a name="next-steps"></a>További lépések

* Az RBAC használatával kapcsolatos további tudnivalókért olvassa [el a Hozzáférés-kezelés az Azure Portalon című témakört.](../role-based-access-control/overview.md)
* Az erőforrászárolások használatával kapcsolatos további tudnivalókért olvassa el az [Erőforrások zárolása az Azure Resource Manager rel](../azure-resource-manager/management/lock-resources.md)című témakört.
