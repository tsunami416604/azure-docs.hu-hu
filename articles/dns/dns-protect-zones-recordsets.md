---
title: DNS-zónák és -rekordok védelme |} A Microsoft Docs
description: Hogyan védheti meg DNS-zónák és -rekordhalmazok a Microsoft Azure DNS-ben.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: victorh
ms.openlocfilehash: ff20c16c89ca5bf27bfddc654119b428cc425d2d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173467"
---
# <a name="how-to-protect-dns-zones-and-records"></a>DNS-zónák és -rekordok védelme

DNS-zónák és -rekordok a kiemelt fontosságú erőforrásait. A DNS-zónát, vagy csak egyetlen DNS-rekord törlése egy teljes szolgáltatás-kimaradás eredményezhet.  Ezért fontos, hogy kritikus DNS-zónák és rekordok védve legyenek a jogosulatlan vagy véletlen módosításokat.

Ez a cikk bemutatja, hogyan Azure DNS lehetővé teszi, hogy a DNS-zónák és -rekordok az ilyen változások elleni védelme.  Azure Resource Manager által biztosított két hatékony biztonsági funkciókészlet alkalmazunk: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) és [erőforrászárat](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-kezelés az Azure-felhasználók, csoportok és erőforrások lehetővé teszi. Az RBAC használatával, akkor is pontosan a mértékű hozzáférést biztosítson a felhasználóknak frissíteniük kell a munkája elvégzéséhez. Hogyan segít az RBAC-hozzáférés kezelése kapcsolatos további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>A "DNS-zóna Közreműködője" szerepkör

A "DNS-zóna Közreműködője" szerepkör, a DNS-erőforrások kezelése az Azure által biztosított beépített szerepkör.  DNS-zóna Közreműködője jogosultságok hozzárendelése egy felhasználóhoz vagy csoporthoz lehetővé teszi, hogy a csoport DNS-erőforrásokat, de nem bármilyen más típusú erőforrások kezeléséhez.

Tegyük fel, hogy a csoport "myzones erőforrás" Contoso Corporation öt zónák tartalmazza. A DNS-rendszergazda az adott erőforráscsoportba tartozó "DNS-zóna Közreműködője" engedélyek megadását, lehetővé teszi, hogy a teljes körűen felügyelve az adott DNS-zónák. Azt is elkerülhetők a szükségtelen engedélyek megadását, például a DNS-rendszergazda nem hozható létre, vagy állítsa le a virtuális gépek.

RBAC-engedélyek hozzárendelése a legegyszerűbb [az Azure Portalon keresztül](../role-based-access-control/role-assignments-portal.md).  Az erőforráscsoport a "Hozzáférés-vezérlés (IAM)" panel megnyitásához, majd kattintson a "Hozzáadás", majd válassza a "DNS-zóna Közreműködője" szerepkört és engedélyek megadása a szükséges felhasználók vagy csoportok kiválasztása.

![Erőforráscsoport szintjén RBAC az Azure Portalon](./media/dns-protect-zones-recordsets/rbac1.png)

Engedélyeket is lehet [kapnak az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Az egyenértékű parancs egyben [az Azure CLI-n keresztül elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zóna szintjét RBAC

Az Azure RBAC-szabályok alkalmazhatók, előfizetés, erőforráscsoport vagy egyedi erőforrásokat. Esetén az Azure DNS-beli erőforrás lehet egy egyedi DNS-zónát, vagy akár egy egyedi rekordhalmaz.

Tegyük fel, hogy az erőforrás "myzones" csoport tartalmazza a "contoso.com" zóna és a egy subzone "customers.contoso.com", amelyben CNAME-rekordok jönnek létre az egyes felhasználói fiókok számára.  A CNAME rekordokkal kezeléséhez használt fiókot hozzá kell rendelni csak a "customers.contoso.com" zóna a rekordok létrehozásához szükséges engedélyek, a hozzáférés a más zónákban nem rendelkezhet.

Zónaszintű RBAC-engedélyek az Azure Portalon is megadható.  A zóna a "Hozzáférés-vezérlés (IAM)" panel megnyitásához, majd kattintson a "Hozzáadás", majd válassza a "DNS-zóna Közreműködője" szerepkört és az engedélyek megadása szükséges felhasználók vagy csoportok kiválasztása.

![DNS-zóna szintű RBAC az Azure Portalon](./media/dns-protect-zones-recordsets/rbac2.png)

Engedélyeket is lehet [kapnak az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Az egyenértékű parancs egyben [az Azure CLI-n keresztül elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Rekord RBAC szintjének beállítása

Azt is egy lépéssel tovább. Fontolja meg a levelezési rendszergazda a Contoso Corporation, el kell érnie a MX és a txt típusú rekordok a "contoso.com" zóna tetején található rekordokra.  Marcela nem szükséges bármely más MX vagy a txt típusú rekordot vagy rekordokat bármilyen más típusú hozzáférést.  Az Azure DNS lehetővé teszi a rekordhalmaz szintű engedélyek hozzárendelése a pontosan azokat a rekordokat, amelyek a mail rendszergazdának hozzá kell férnie.  Az e-mail a rendszergazdák pontosan a vezérlő ő van szüksége, és semmilyen más módosítást nem kapnak.

A rekordhalmaz szintű RBAC-engedélyek az Azure Portalon, a rekordhalmaz panelen a "Felhasználók" gomb segítségével konfigurálható:

![A rekordhalmaz szint RBAC az Azure Portalon](./media/dns-protect-zones-recordsets/rbac3.png)

A rekordhalmaz szintű RBAC-engedélyek is lehet [kapnak az Azure PowerShell-lel](../role-based-access-control/role-assignments-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Az egyenértékű parancs egyben [az Azure CLI-n keresztül elérhető](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Egyéni szerepkörök

A "DNS-zóna Közreműködője" beépített szerepkör lehetővé teszi a DNS-erőforrásai teljes körű irányítását. Akkor is létre lehet hozni saját ügyfél Azure szerepköröket, hogy adja meg a vezérlő még részletesebben.

Tekintse meg ismét a példát, amelyben egy CNAME rekordot a zóna "customers.contoso.com" jön létre minden egyes Contoso Corporation felhasználói fiókhoz.  A fiók a CNAME-rekordok kezelésére szolgáló kell adható hozzáférés csak a CNAME-rekordok kezelése.  Ezután nem (például az MX-rekordok módosítása) a más típusú rekordok módosítása vagy egyéb műveleteket zónaszintű zóna törlése.

Az alábbi példa bemutatja egy egyéni szerepkör-definíció csak a CNAME-rekordok kezelésére szolgáló:

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

A műveleti tulajdonság határozza meg a következő DNS-specifikus engedélyek:

* `Microsoft.Network/dnsZones/CNAME/*` teljes hozzáférést biztosít a CNAME-rekordokat keresztül
* `Microsoft.Network/dnsZones/read` engedélyt ad a DNS-zónák olvasni, de nem módosíthatja őket, lehetővé téve a tekintse meg a zónát, amelyben a CNAME létrehozása folyamatban van.

A fennmaradó műveletek másolja át a [DNS-zóna Közreműködője beépített szerepkörrel](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Egy egyéni RBAC-szerepkör használatával, hogy a rekordhalmaz törlése, bár továbbra is lehetővé teheti, hogy frissíteni nem hatékony ellenőrzése. Megakadályozza, hogy rekordhalmazok törlése folyamatban van, de nem akadályozza meg azokat megakadályozható azok módosítása.  Engedélyezett módosításokat például hozzáadása és eltávolítása a bejegyzések a beállított rekord, beleértve az összes rekordot, hogy egy "empty" rekordhalmaz eltávolítása. Ez ugyanaz a hatása, egy DNS-feloldási szempontból a rekordhalmaz törlése.

Egyéni szerepkör-definíciók jelenleg nem lehet meghatározni, az Azure Portalon keresztül. Ez a szerepkör-definíció alapján egyéni szerepkör Azure PowerShell-lel hozhatók létre:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Azt is létrehozhatók az Azure CLI-n keresztül:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

A szerepkör majd lehet hozzárendelni a ugyanúgy, mint a beépített szerepkörök ebben a cikkben korábban leírtaknak megfelelően.

A létrehozása, kezelése és egyéni szerepkörök hozzárendelése további információkért lásd: [egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Erőforrás-zárolások

Mellett, az RBAC Azure Resource Manager támogatja a más típusú biztonsági ellenőrzést, azaz "lock" erőforrások lehetővé teszi. Ahol RBAC-szabályok lehetővé teszik a vezérlőelem a műveletek adott felhasználók és csoportok, erőforrás-zárolások érvénybe lépnek az erőforrást, és hatékony összes felhasználók és szerepkörök. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-lock-resources.md).

Erőforrás-zárolás két típusa van: **DoNotDelete** és **ReadOnly**. Ezek a DNS-zónát, vagy egy egyéni rekordhalmaz alkalmazhatók.  A következő szakaszokban számos gyakori forgatókönyv, és hogyan támogatja az erőforrás-zárolások használatával.

### <a name="protecting-against-all-changes"></a>Minden módosítás ellen védelmet biztosító

Minden végrehajtott változtatások elkerülése érdekében érvényesek a zóna egy írásvédett zárolását.  Ez megakadályozza, hogy új rekordhalmazok létrehozása, és a meglévő rekordhalmazt módosítás vagy Törlés folyamatban van.

Szolgáltatói erőforrás-zárolások zóna az Azure Portalon lehet létrehozni.  A DNS-zóna panelen kattintson az "Zárolását", majd "hozzáadása":

![Szolgáltatói erőforrás-zárolások zónában az Azure Portalon](./media/dns-protect-zones-recordsets/locks1.png)

Zónaszintű erőforrás-zárolások is létrehozhatók az Azure PowerShell-lel:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Azure-erőforrászárolás konfigurálása jelenleg nem támogatott az Azure CLI-n keresztül.

### <a name="protecting-individual-records"></a>Az egyes rekordok védelme

Egy meglévő DNS-rekordhalmaz elleni módosításának megakadályozása érdekében érvényesek a rekordhalmaz egy írásvédett zárolását.

> [!NOTE]
> Rekordhalmaz DoNotDelete zárolást alkalmazása nem hatékony ellenőrzése. Ez megakadályozza, hogy az adott rekordhalmaz törlése folyamatban van a, de nem akadályozza meg, már nem módosítható.  Engedélyezett módosításokat például hozzáadása és eltávolítása a bejegyzések a beállított rekord, beleértve az összes rekordot, hogy egy "empty" rekordhalmaz eltávolítása. Ez ugyanaz a hatása, egy DNS-feloldási szempontból a rekordhalmaz törlése.

Rekordhalmaz szintű erőforrás-zárolások is jelenleg csak konfigurált Azure PowerShell-lel.  Az Azure Portalon vagy az Azure CLI-vel ezek nem támogatottak.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Védekezés a zóna törlése

Ha a zóna Azure DNS-ben törölnek, a zónában lévő összes rekordhalmazt is törlődik.  Ez a művelet nem vonható vissza.  Egy kritikus fontosságú zóna véletlen törlése nem jelentős üzleti hatással van.  Ezért nagyon fontos zóna véletlen törlés elleni védelem érdekében.

A zónához DoNotDelete zárolást alkalmazása megakadályozza, hogy a zóna törlése folyamatban van.  Azonban mivel zárolások gyermekerőforrásait örökölt, is megakadályozza, hogy bármely rekordhalmazok törölhető, a zónában, esetlegesen nemkívánatos.  Ezenkívül a megjegyzést a fenti leírtak is hatástalan óta rekordok továbbra is eltávolíthatja a meglévő rekordhalmazt.

Alternatív megoldásként fontolja meg, egy rekordot a zónában, például a SOA típusú rekordhalmaz DoNotDelete zárolást alkalmazására.  A zóna is törlése a rekordhalmazok nélkül nem lehet törölni, mivel ez zóna törlése, miközben továbbra is lehetővé teszi a rekordhalmazok szabadon módosítani a zónán belüli ellen védi. Kísérlet történik a zóna törlése, ha Azure Resource Manager azt észleli, ez is törölhető a SOA típusú rekordhalmaz, és letiltja a hívást, mert a SOA típusú.  Rekordhalmazok nem törlődnek.

A következő PowerShell-parancsot a SOA típusú rekordját az adott zóna elleni DoNotDelete zárolást hoz létre:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Zóna véletlen törlés megelőzése érdekében egy másik úgy, hogy egy egyéni szerepkör segítségével győződjön meg, hogy az operátor és a zónák kezelésére szolgáló szolgáltatásfiókok nem rendelkezik a zóna vonatkozó engedélyeinek törlése. Ha törölni szeretne egy zónát, kényszerítheti egy kétlépéses törlése, első próbáltak zóna törlési jogosultsággal (a hatókörben zóna, törölje a nem megfelelő zónát elkerülése érdekében), második törölje a zónát.

A második megközelítéssel próbálkozzon az előnnyel jár, amely minden zóna elérhető ezeket a fiókokat, hozzon létre minden olyan zárolásokat nélkül működik. Ennek a hátránya, hogy minden olyan fiókok zóna delete engedélyekkel, például az előfizetés tulajdonosa véletlenül továbbra is törölheti a kritikus fontosságú zóna rendelkezik.

Mindkét módszerénél - erőforrás-zárolások és egyéni szerepkörök - használata egy időben, mint egy DNS-zóna védelmi defense jellegű megközelítése lehetőség.

## <a name="next-steps"></a>További lépések

* Az RBAC használatával kapcsolatos további információkért lásd: [hozzáférés-kezelés az Azure portal – első lépések](../role-based-access-control/overview.md).
* Erőforrás-zárolások használatáról további információkért lásd: [zárolhat erőforrásokat az Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

