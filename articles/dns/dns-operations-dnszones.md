---
title: "Az Azure DNS - PowerShell DNS-zónák kezelése |} Microsoft Docs"
description: "DNS-zónák Azure Powershell használatával kezelheti. Ez a cikk ismerteti, hogyan frissítés, törlés és DNS-zóna létrehozása az Azure DNS szolgáltatásra"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: gwallace
ms.openlocfilehash: 3f28e70bb6ef46f53375d256a520db40fcb71ad0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell-lel DNS-zónák kezelése

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan a DNS-zónák kezelése az Azure PowerShell használatával. Emellett kezelhetők a DNS-zónák használata a platformok közötti [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure-portálon.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzureRmDnsZone` parancsmag használatával hozható létre.

Az alábbi példa létrehoz egy DNS-zónát *contoso.com* erőforráscsoportban nevű *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

A következő példa bemutatja, hogyan hozzon létre egy DNS-zónát és két [Azure Resource Manager címkéket](dns-zones-records.md#tags), *project = demo* és *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Az Azure DNS most is támogatja a saját DNS-zóna (jelenleg előzetes verziójú funkciók).  Például a saját DNS-zóna létrehozását, [Ismerkedés az Azure DNS-titkos zónák PowerShell-lel](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>A DNS-zóna beolvasása

A DNS-zónák lekéréséhez használja a `Get-AzureRmDnsZone` parancsmag. Ez a művelet egy meglévő az Azure DNS-zónához tartozó DNS-zóna objektum adja vissza. Az objektum tartalmazza a zóna (például a rekordhalmazok száma) adatait, de nem tartalmaz a rekordhalmazok maguk (lásd: `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Lista DNS-zónák

A zóna nevét a kihagyásával őriznek `Get-AzureRmDnsZone`, minden zóna erőforráscsoportban enumerálása. Ez a művelet a zóna objektumok tömbjét adja vissza.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

A zóna nevét, mind az erőforráscsoport neve a kihagyásával őriznek `Get-AzureRmDnsZone`, az Azure-előfizetés az összes zóna enumerálása.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>A DNS-zóna frissítéséhez

A DNS-zóna erőforrás is lehet módosítani a `Set-AzureRmDnsZone`. Ez a parancsmag nem frissíti a DNS-rekordhalmazok a zónán belül (lásd: [kezelése DNS-rekordok hogyan](dns-operations-recordsets.md)). Csak a zóna erőforrás maga tulajdonságainak frissítésére szolgál. Az írható zóna tulajdonságai jelenleg csak a [Azure Resource Manager "címkék" a zóna erőforrás](dns-zones-records.md#tags).

A DNS-zóna frissítéséhez az alábbi két módszer egyikével:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Adja meg a zóna, a zóna nevét és az erőforrás csoport használata

Ez a megközelítés lecseréli a meglévő zóna címkék megadott értékek.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Adjon meg egy $zone objektum használatával

Ez a megközelítés átveszi a meglévő zóna objektumot, módosítja a címkék és majd véglegesíti a módosításokat. Ezzel a módszerrel meglévő címkék megőrizhetők.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Használata esetén `Set-AzureRmDnsZone` $zone objektummal [Etag ellenőrzi](dns-zones-records.md#etags) biztosítják az egyidejű változtatások nem íródnak felül. A választható használható `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

## <a name="delete-a-dns-zone"></a>A DNS-zóna törlése

DNS-zónák segítségével törölhetők a `Remove-AzureRmDnsZone` parancsmag.

> [!NOTE]
> Is egy DNS-zóna törlésével törli az összes DNS-rekordokat a zónán belül. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zóna szolgáltatásokat sikertelen lesz a zóna törlődik.
>
>A zóna véletlen törlés elleni védelem érdekében, lásd: [hogyan védi a DNS-zónák és rekordok](dns-protect-zones-recordsets.md).


Használja a DNS-zóna törlése a következő két módszer egyikével:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Adja meg a zóna nevét és az erőforráscsoport-név használatával

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Adjon meg egy $zone objektum használatával

Megadhatja a zónát, hogy törölni kell a `$zone` által visszaadott objektum `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

A zóna objektum is átirányítható paraméterként átadott helyett:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

A `Set-AzureRmDnsZone`, adja meg a zóna használatával egy `$zone` objektum lehetővé teszi, hogy a Etag ellenőrzi, hogy ellenőrizze a egyidejű változtatások nem törlődnek. Használja a `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

## <a name="confirmation-prompts"></a>Megerősítés

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone`, és `Remove-AzureRmDnsZone` parancsmagok minden megerősítés támogatja.

Mindkét `New-AzureRmDnsZone` és `Set-AzureRmDnsZone` megerősítés kérése, ha a `$ConfirmPreference` PowerShell preferenciaváltozót értéke `Medium` vagy alacsonyabb. Törli a DNS-zónát, potenciálisan nagy jelentőségű miatt a `Remove-AzureRmDnsZone` parancsmag kell megerősítést kérni fogja, ha a `$ConfirmPreference` PowerShell változó értéke bármely eltérő `None`.

Az alapértelmezett érték óta `$ConfirmPreference` van `High`, csak `Remove-AzureRmDnsZone` alapértelmezés szerint rákérdez.

Ha szeretné felülbírálni az aktuális `$ConfirmPreference` használatának beállítása a `-Confirm` paraméter. Ha megad `-Confirm` vagy `-Confirm:$True` , a parancsmag megerősítést kér, mielőtt futtatja. Ha megad `-Confirm:$False` , a parancsmag nem figyelmeztet megerősítést kér.

További információ `-Confirm` és `$ConfirmPreference`, lásd: [kapcsolatos Preferenciaváltozók](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [rekordhalmazokat és rekordokat kezelése](dns-operations-recordsets.md) a DNS-zónában.
<br>
Megtudhatja, hogyan [tartomány delegálása az Azure DNS-](dns-domain-delegation.md).
<br>
Tekintse át a [Azure DNS PowerShell referenciadokumentációt](/powershell/module/azurerm.dns).

