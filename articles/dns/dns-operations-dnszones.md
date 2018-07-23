---
title: Kezelheti a DNS-zónák az Azure DNS – PowerShell |} A Microsoft Docs
description: DNS-zónák az Azure Powershell használatával kezelheti. Ez a cikk bemutatja, hogyan frissítése, törlése, és hozzon létre DNS-zónák az Azure DNS-ben
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: 59f76d4c06a2a60cb513fc37a9ca3e27e13820b9
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173608"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell-lel DNS-zónák kezelése

> [!div class="op_single_selector"]
> * [Portál](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Ez a cikk bemutatja, hogyan a DNS-zónák kezelése az Azure PowerShell használatával. Emellett kezelheti a DNS-zónák használata a platformfüggetlen [Azure CLI-vel](dns-operations-dnszones-cli.md) vagy az Azure Portalon.

Ez az útmutató kifejezetten a nyilvános DNS-zónák foglalkozik. Az Azure PowerShell használatával való kezeléséről az Azure DNS Private Zones információkért lásd: [Ismerkedés az Azure PowerShell-lel az Azure DNS Private Zones](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzureRmDnsZone` parancsmag használatával hozható létre.

A következő példában létrehozunk egy DNS-zónát *contoso.com* az erőforráscsoport neve *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Az alábbi példa bemutatja, hogyan hozhat létre két DNS-zóna [Azure Resource Manager-címkék](dns-zones-records.md#tags), *project = demo* és *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Az Azure DNS már támogatja a privát DNS-zónák használatát (jelenleg nyilvános előzetes verzióban érhető el).  További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md). További információt a privát DNS-zónák létrehozásáról [az Azure DNS privát zónái PowerShell segítségével történő használatának első lépéseit](./private-dns-getstarted-powershell.md) ismertető cikkben olvashat.

## <a name="get-a-dns-zone"></a>DNS-zóna lekérése

DNS-zóna lekéréséhez használja a `Get-AzureRmDnsZone` parancsmagot. A művelet egy DNS-zóna egy meglévő zóna Azure DNS-ben megfelelő objektum adja vissza. Az objektum a zóna (például a rekordhalmazok száma) kapcsolatos adatokat tartalmaz, de nem tartalmazza a rekordhalmazok magukat (lásd: `Get-AzureRmDnsRecordSet`).

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

A DNS-zóna erőforrásai a `Set-AzureRmDnsZone` parancsmaggal módosíthatók. Ez a parancsmag nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd a [DNS-rekordok kezeléséről szóló részt](dns-operations-recordsets.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Az írható zónatulajdonságok jelenleg korlátozva a [Azure Resource Manager "címkék" zóna erőforrás](dns-zones-records.md#tags).

DNS-zóna frissítéséhez az alábbi két módszer valamelyikével:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Adja meg a zónát, a zóna nevére és erőforráscsoportjára csoport használata

Ez a megközelítés lecseréli a meglévő zóna címkék megadott értékeket.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

Ez a megközelítés kérdezi le a meglévő zóna objektumot, módosítja a címkéket, és ezután véglegesíti a módosításokat. Ezzel a módszerrel megőrizhetőek a meglévő címkéit.

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

Használata esetén `Set-AzureRmDnsZone` és a egy $zone objektum [Etag ellenőrzi](dns-zones-records.md#etags) biztosítva a párhuzamos módosítások nem lettek felülírva. Használhatja a választható `-Overwrite` kapcsoló le ezeket az ellenőrzéseket.

## <a name="delete-a-dns-zone"></a>DNS-zóna törlése

DNS-zónák törléséhez használja a `Remove-AzureRmDnsZone` parancsmagot.

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

A `Set-AzureRmDnsZone`, adja meg a zóna használatával egy `$zone` objektum lehetővé teszi, hogy az Etag-ellenőrzéseket, amelyek biztosítják a egyidejű módosításokat nem törlődnek. Használja a `-Overwrite` kapcsoló le ezeket az ellenőrzéseket.

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` és `Remove-AzureRmDnsZone` parancsmagok mind támogatják a megerősítési kérések használatát.

A `New-AzureRmDnsZone` és a `Set-AzureRmDnsZone` egyaránt kér megerősítést, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. A DNS-zónák lehetséges súlyos hatásai miatt a `Remove-AzureRmDnsZone` parancsmag felkéri a művelet megerősítésére, ha a `$ConfirmPreference` PowerShell-változó értéke más, mint `None`.

Mivel a `$ConfirmPreference` alapértelmezett értéke `High`, csak a `Remove-AzureRmDnsZone` parancsmag kér alapértelmezés szerint megerősítést.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést.

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) tartalmazó részben talál.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [rekordhalmazok és -rekordok kezelése](dns-operations-recordsets.md) a DNS-zónában.
<br>
Ismerje meg, hogyan [tartomány delegálása az Azure DNS](dns-domain-delegation.md).
<br>
Tekintse át a [Azure DNS PowerShell-referenciáinak dokumentációja](/powershell/module/azurerm.dns).

