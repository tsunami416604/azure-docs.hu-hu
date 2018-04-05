---
title: Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel | Microsoft Docs
description: A cikkből megtudhatja, hogyan hozhat létre saját DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel a PowerShell használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel

Ez a cikk végigvezeti az első saját DNS-zóna és -rekord Azure PowerShell-lel való létrehozásának lépésein.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket „feloldási virtuális hálózatoknak” nevezzük.  Megadhat olyan virtuális hálózatot is, amelyhez az Azure DNS eszköznévrekordokat biztosít a virtuális gépek létrehozásakor, IP-címének módosításakor vagy megsemmisülésekor.  Ezt „regisztrációs virtuális hálózatnak” nevezzük.

# <a name="get-the-preview-powershell-modules"></a>Az előzetes verziójú PowerShell-modulok beszerzése
Ezek az utasítások feltételezik, hogy már telepítette az Azure Powershellt, és be is jelentkezett, illetve meggyőződött arról, hogy rendelkezik a Private Zone szolgáltatáshoz szükséges modulokkal. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A DNS-zóna létrehozása előtt egy erőforráscsoportot kell létrehozni, amely a DNS-zónát tartalmazza majd. Az alábbi példában a parancs látható:

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Saját DNS-zóna létrehozása

DNS-zóna létrehozásához használja a `New-AzureRmDnsZone` parancsmagot, és adja meg a „Private” értéket a ZoneType paraméterhez. Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.local* DNS-zónát, majd elérhetővé teszi azt a *MyAzureVnet* nevű virtuális hálózat számára. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

Vegye figyelembe, hogy a ZoneType paraméter kihagyása esetén a zóna nyilvános zónaként jön létre, ezért a paraméter megadása kötelező ahhoz, hogy privát zónát hozhasson létre. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Ha arra van szükség, hogy az Azure automatikusan hozzon létre gazdanév-rekordokat a zónában, a *ResolutionVirtualNetworkId* helyett használja a *RegistrationVirtualNetworkId* paramétert.  A feloldás a regisztrációs virtuális hálózatok számára automatikusan engedélyezett.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

Rekordhalmazt a `New-AzureRmDnsRecordSet` parancsmag használatával hozhat létre. Az alábbi példa a „MyResourceGroup” erőforráscsoport „contoso.local” DNS-zónájában egy „db” relatív nevű rekordot hoz létre. A beállított rekord teljes neve „db.contoso.local”. A rekord típusa „A”, az IP-címe „10.0.0.4”, az élettartama pedig 3600 másodperc.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Más rekordtípusok, több rekordot tartalmazó rekordhalmazok és meglévő rekordok módosítása esetén lásd: [DNS-rekordok és -rekordhalmazok kezelése az Azure PowerShell használatával](dns-operations-recordsets.md). 

## <a name="view-records"></a>A rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Privát DNS-zónák listázása

Ha kihagyja a zóna nevét a `Get-AzureRmDnsZone` parancsból, felsorolhatja az egy erőforráscsoportba tartozó összes zónát. Ez a művelet zónaobjektumok tömbjét adja vissza.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ha a zóna és az erőforráscsoport nevét is kihagyja a `Get-AzureRmDnsZone` parancsból, felsorolhatja az Azure-előfizetéshez tartozó összes zónát.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Privát DNS-zóna frissítése

A DNS-zóna erőforrásai a `Set-AzureRmDnsZone` parancsmaggal módosíthatók. Ez a parancsmag nem frissíti a zóna egy DNS-rekordhalmazát sem (lásd a [DNS-rekordok kezeléséről szóló részt](dns-operations-recordsets.md)). Csak a zónaerőforrás tulajdonságainak frissítésére használatos. Az írható zónatulajdonságok jelenleg korlátozva vannak az [Azure Resource Manager zónaerőforrásokra vonatkozó „címkéire”](dns-zones-records.md#tags), valamint a privát zónák „RegistrationVirtualNetworkId” és „ResolutionVirtualNetworkId” paraméterére.

Az alábbi példa kicseréli a zónához kapcsolódó regisztrációs virtuális hálózatot egy újra: MyNewAzureVnet.

Fontos, hogy a létrehozással ellentétben a frissítéshez nem kötelező megadni a ZoneType paramétert. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

Az alábbi példa kicseréli a zónához kapcsolódó feloldási virtuális hálózatot egy új, MyNewAzureVnet nevű virtuális hálózatra.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Privát DNS-zóna törlése

A privát DNS-zónák a nyilvános zónákhoz hasonlóan, a `Remove-AzureRmDnsZone` parancsmag használatával törölhetők.

> [!NOTE]
> A DNS-zónák törlésével a zónában található összes DNS-rekord is törlődni fog. Ez a művelet nem vonható vissza. Ha a DNS-zóna használatban van, a zónát használó szolgáltatások futtatása meghiúsul a zóna törlésekor.
>
>A zónák véletlen törlése elleni védelemről további információt talál a [DNS-zónák és -rekordok védelme](dns-protect-zones-recordsets.md) című szakaszban.

DNS-zónák törléséhez használja az alábbi két módszer egyikét:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Zóna megadása a zóna és az erőforráscsoport nevének használatával

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Zóna megadása egy $zone objektum használatával

A törölni kívánt zóna egy, a `Get-AzureRmDnsZone` parancs által visszaadott `$zone` objektummal adható meg.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

A zónaobjektum paraméterként való továbbítása helyett át is adhatja azt.

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Megerősítési kérések

A `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` és `Remove-AzureRmDnsZone` parancsmagok mind támogatják a megerősítési kérések használatát.

A `New-AzureRmDnsZone` és a `Set-AzureRmDnsZone` egyaránt kér megerősítést, ha a `$ConfirmPreference` PowerShell-preferenciaváltozó értéke `Medium` vagy kisebb. A DNS-zónák lehetséges súlyos hatásai miatt a `Remove-AzureRmDnsZone` parancsmag felkéri a művelet megerősítésére, ha a `$ConfirmPreference` PowerShell-változó értéke más, mint `None`.

Mivel a `$ConfirmPreference` alapértelmezett értéke `High`, csak a `Remove-AzureRmDnsZone` parancsmag kér alapértelmezés szerint megerősítést.

A jelenlegi `$ConfirmPreference` beállítás a `-Confirm` paraméter használatával írható felül. Ha `-Confirm` vagy `-Confirm:$True` értéket ad meg, a parancsmag megerősítést fog kérni a futtatása előtt. Ha `-Confirm:$False` értéket ad meg, a parancsmag nem kér megerősítést.

A `-Confirm` és `$ConfirmPreference` értékekkel kapcsolatos további információt [a preferenciaváltozók bemutatását](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) tartalmazó részben talál.


## <a name="delete-all-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépést:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

Néhány gyakori forgatókönyv ([Privát zónák – Forgatókönyvek](./private-dns-scenarios.md)), amelyek megvalósíthatók az Azure DNS privát zónáiban.

DNS-rekordok az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure DNS-ben a PowerShell-lel való kezelésével kapcsolatos](dns-operations-recordsets.md) témakört.

