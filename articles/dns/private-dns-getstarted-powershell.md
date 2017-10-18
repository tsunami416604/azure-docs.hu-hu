---
title: "Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan hozhat létre saját DNS-zónát és -rekordot az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel a PowerShell használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját."
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: c5d4b33682f9b5d33e74d45973d5fbd76cf16465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Az Azure DNS saját zónáival kapcsolatos első lépések PowerShell-lel

Ez a cikk végigvezeti az első saját DNS-zóna és -rekord Azure PowerShell-lel való létrehozásának lépésein.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket „feloldási hálózatoknak” nevezzük.  Megadhat olyan virtuálishálózat-készletet is, amelyhez az Azure DNS gazdanév-rekordokat biztosít a virtuális gépek létrehozásakor, IP-címének módosításakor vagy megsemmisülésekor.  Ezeket „regisztrációs hálózatoknak” nevezzük.

Mivel ez a szolgáltatás jelenleg felügyelt előzetes verzióként érhető el, így a rendszer biztosít hozzá egy előzetes verziójú PowerShell-modult.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A DNS-zóna létrehozása előtt egy erőforráscsoportot kell létrehozni, amely a DNS-zónát tartalmazza majd. Az alábbiakban a parancs látható.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

A DNS-zóna az `New-AzureRmDnsZone` parancsmag használatával hozható létre. Az alábbi példaparancs a *MyResourceGroup* erőforráscsoportban létrehozza a *contoso.local* DNS-zónát, majd elérhetővé teszi azt a *MyAzureVnet* nevű virtuális hálózat számára. A példát követve, és az értékeket a sajátjaira cserélve hozza létre a DNS-zónát.

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

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépést:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

További információk a saját DNS-zónákról: [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md).

DNS-rekordok az Azure DNS-ben való kezelésével kapcsolatos további információért lásd [a DNS-rekordok és -rekordhalmazok az Azure DNS-ben a PowerShell-lel való kezelésével kapcsolatos](dns-operations-recordsets.md) témakört.

