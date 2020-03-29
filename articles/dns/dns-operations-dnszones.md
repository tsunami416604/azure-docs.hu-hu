---
title: DNS-zónák kezelése az Azure DNS-ben - PowerShell | Microsoft dokumentumok
description: A DNS-zónák az Azure Powershell használatával kezelheti. Ez a cikk bemutatja, hogyan frissíthetők, törölhetők és hozhatnak létre DNS-zónákat az Azure DNS-ben
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.openlocfilehash: 0120501aab7f0a63721126bfb5b3d04d9deb42fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936802"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>A DNS-zónák kezelése a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Azure klasszikus parancssori felület](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan kezelheti a DNS-zónák az Azure PowerShell használatával. A DNS-zónák is kezelheti a többplatformos [Azure CLI](dns-operations-dnszones-cli.md) vagy az Azure Portal használatával.

Ez az útmutató kifejezetten a nyilvános DNS-zónákkal foglalkozik. Az Azure PowerShell azure-beli DNS-zónák kezeléséhez való használatáról az [Azure DNS-alapú magánhálózati zónák használatának első lépései](private-dns-getstarted-powershell.md)című témakörben talál további információt.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzureRmDnsZone` parancsmag használatával hozható létre.

A következő példa létrehoz egy *contoso.com* nevű DNS-zónát a *MyResourceGroup*nevű erőforráscsoportban:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

A következő példa bemutatja, hogyan hozhat létre DNS-zónát két [Azure Resource Manager-címkével](dns-zones-records.md#tags), *projekt = demo* és *env = teszt:*

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Az Azure DNS is támogatja a privát DNS-zónák.  További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái PowerShell segítségével történő használatának első lépéseit](./private-dns-getstarted-powershell.md) ismertető cikkben olvashat.

## <a name="get-a-dns-zone"></a>DNS-zóna beszerezni

DNS-zóna beolvasásához `Get-AzureRmDnsZone` használja a parancsmabot. Ez a művelet egy AZURE DNS-zónának megfelelő DNS-zónaobjektumot ad vissza. Az objektum adatokat tartalmaz a zónáról (például a rekordhalmazok számát), `Get-AzureRmDnsRecordSet`de nem tartalmazza a rekordkészleteket (lásd ).

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

A DNS-zóna erőforrásai a `Set-AzureRmDnsZone` parancsmaggal módosíthatók. Ez a parancsmag nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd a [DNS-rekordok kezeléséről szóló részt](dns-operations-recordsets.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Az írható zóna tulajdonságai jelenleg a [zónaerőforrás Azure Resource Manager "címkékre" korlátozódnak.](dns-zones-records.md#tags)

Dns-zóna frissítéséhez használja az alábbi két módszer egyikét:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>A zóna megadása a zóna nevével és erőforráscsoporttal

Ez a megközelítés a meglévő zónacímkéket a megadott értékekkel helyettesíti.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

Ez a megközelítés lekéri a meglévő zónaobjektumot, módosítja a címkéket, majd véglegesíti a módosításokat. Ily módon a meglévő címkék megőrizhetők.

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

Ha `Set-AzureRmDnsZone` $zone objektummal használ, [az Etag-ellenőrzések](dns-zones-records.md#etags) biztosítják, hogy az egyidejű módosítások ne legyenek felülírva. A választható `-Overwrite` kapcsolóval letilthatja ezeket az ellenőrzéseket.

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

A DNS-zónák a `Remove-AzureRmDnsZone` parancsmag használatával törölhetők.

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

A `Set-AzureRmDnsZone`zónához egy `$zone` objektum használatával történő megadásához is lehetővé teszi az Etag-ellenőrzések ellenőrzését, hogy az egyidejű módosítások ne törlődjenek. A `-Overwrite` kapcsolóval letilthatja ezeket az ellenőrzéseket.

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` és `Remove-AzureRmDnsZone` parancsmagok mind támogatják a megerősítési kérések használatát.

A `New-AzureRmDnsZone` és a `Set-AzureRmDnsZone` egyaránt kér megerősítést, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. A DNS-zónák lehetséges súlyos hatásai miatt a `Remove-AzureRmDnsZone` parancsmag felkéri a művelet megerősítésére, ha a `$ConfirmPreference` PowerShell-változó értéke más, mint `None`.

Mivel a `$ConfirmPreference` alapértelmezett értéke `High`, csak a `Remove-AzureRmDnsZone` parancsmag kér alapértelmezés szerint megerősítést.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést.

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](/powershell/module/microsoft.powershell.core/about/about_preference_variables) tartalmazó részben talál.

## <a name="next-steps"></a>További lépések

További információ a dns-zónában lévő [rekordkészletek és rekordok kezeléséről.](dns-operations-recordsets.md)
<br>
Ismerje meg, hogyan [delegálhatja tartományát az Azure DNS-re.](dns-domain-delegation.md)
<br>
Tekintse át az [Azure DNS PowerShell referenciadokumentációját.](/powershell/module/azurerm.dns)

