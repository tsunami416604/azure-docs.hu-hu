---
title: Az Azure DNS - PowerShell DNS-zónák kezelése |} Microsoft Docs
description: DNS-zónák Azure Powershell használatával kezelheti. Ez a cikk ismerteti, hogyan frissítés, törlés és DNS-zóna létrehozása az Azure DNS szolgáltatásra
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: kumud
ms.openlocfilehash: e7b0bc32d3fa8fbcf73298b6988655fca7cfa793
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell-lel DNS-zónák kezelése

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan a DNS-zónák kezelése az Azure PowerShell használatával. Emellett kezelhetők a DNS-zónák használata a platformok közötti [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure-portálon.

Ez az útmutató konkrétan a nyilvános DNS-zónák foglalkozik. További információ az Azure PowerShell használatával az Azure DNS-saját zónák kezelése,: [Ismerkedés az Azure DNS saját zónák az Azure PowerShell](private-dns-getstarted-powershell.md).

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

Az Azure DNS már támogatja a privát DNS-zónák használatát (jelenleg nyilvános előzetes verzióban érhető el).  További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái PowerShell segítségével történő használatának első lépéseit](./private-dns-getstarted-powershell.md) ismertető cikkben olvashat.

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

## <a name="list-dns-zones"></a>DNS-zónák listázása

Ha kihagyja a zóna nevét a `Get-AzureRmDnsZone` parancsból, felsorolhatja az egy erőforráscsoportba tartozó összes zónát. Ez a művelet zónaobjektumok tömbjét adja vissza.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ha a zóna és az erőforráscsoport nevét is kihagyja a `Get-AzureRmDnsZone` parancsból, felsorolhatja az Azure-előfizetéshez tartozó összes zónát.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>DNS-zóna frissítése

A DNS-zóna erőforrásai a `Set-AzureRmDnsZone` parancsmaggal módosíthatók. Ez a parancsmag nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd a [DNS-rekordok kezeléséről szóló részt](dns-operations-recordsets.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Az írható zóna tulajdonságai jelenleg csak a [Azure Resource Manager "címkék" a zóna erőforrás](dns-zones-records.md#tags).

A DNS-zóna frissítéséhez az alábbi két módszer egyikével:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Adja meg a zóna, a zóna nevét és az erőforrás csoport használata

Ez a megközelítés lecseréli a meglévő zóna címkék megadott értékek.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

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
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.


DNS-zónák törléséhez használja az alábbi két módszer egyikét:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Zóna megadása a zóna és az erőforráscsoport nevének használatával

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

A törölni kívánt zóna egy, a `Get-AzureRmDnsZone` parancs által visszaadott `$zone` objektummal adható meg.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

A zónaobjektum paraméterként való továbbítása helyett át is adhatja azt.

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

A `Set-AzureRmDnsZone`, adja meg a zóna használatával egy `$zone` objektum lehetővé teszi, hogy a Etag ellenőrzi, hogy ellenőrizze a egyidejű változtatások nem törlődnek. Használja a `-Overwrite` kapcsoló ezen ellenőrzések letiltásához.

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` és `Remove-AzureRmDnsZone` parancsmagok mind támogatják a megerősítési kérések használatát.

A `New-AzureRmDnsZone` és a `Set-AzureRmDnsZone` egyaránt kér megerősítést, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. A DNS-zónák lehetséges súlyos hatásai miatt a `Remove-AzureRmDnsZone` parancsmag felkéri a művelet megerősítésére, ha a `$ConfirmPreference` PowerShell-változó értéke más, mint `None`.

Mivel a `$ConfirmPreference` alapértelmezett értéke `High`, csak a `Remove-AzureRmDnsZone` parancsmag kér alapértelmezés szerint megerősítést.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést.

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) tartalmazó részben talál.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [rekordhalmazokat és rekordokat kezelése](dns-operations-recordsets.md) a DNS-zónában.
<br>
Megtudhatja, hogyan [tartomány delegálása az Azure DNS-](dns-domain-delegation.md).
<br>
Tekintse át a [Azure DNS PowerShell referenciadokumentációt](/powershell/module/azurerm.dns).

