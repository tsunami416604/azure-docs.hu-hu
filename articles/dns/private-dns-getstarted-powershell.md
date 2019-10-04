---
title: Rövid útmutató – Azure privát DNS-zóna létrehozása Azure PowerShell használatával
description: Ebben a cikkben egy privát DNS-zónát és-rekordot hoz létre és tesztel a Azure DNSban. Ez egy lépésenkénti útmutató, amellyel az Azure PowerShell használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: cf9ca1070461effc69d67614a11b1abd05363310
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162123"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Gyors útmutató: Azure Private DNS-zóna létrehozása Azure PowerShell használatával

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Ez a cikk végigvezeti az első saját DNS-zóna és -rekord Azure PowerShell-lel való létrehozásának lépésein.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket *csatolt* virtuális hálózatoknak nevezzük. Ha engedélyezve van az automatikus regisztráció, a Azure DNS a zóna rekordjait is frissíti, amikor létrejön egy virtuális gép, megváltoztatja az IP-címét, vagy törli azt.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Privát DNS-zóna létrehozása
> * Tesztelési célú virtuális gépek létrehozása
> * További DNS-rekord létrehozása
> * A saját zóna tesztelése

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha szeretné, elvégezheti ezt a rövid útmutatót az [Azure CLI](private-dns-getstarted-cli.md)használatával.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Először hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

A DNS-zóna az `New-AzPrivateDnsZone` parancsmag használatával hozható létre.

A következő példa egy **myAzureVNet**nevű virtuális hálózatot hoz létre. Ezután létrehoz egy **Private.contoso.com** nevű DNS-zónát a **MyAzureResourceGroup** -erőforráscsoporthoz, összekapcsolja a DNS-zónát a **MyAzureVnet** virtuális hálózattal, és engedélyezi az automatikus regisztrációt.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Ha csak névfeloldáshoz szeretne zónát létrehozni (automatikus állomásnév-regisztráció nélkül), akkor kihagyhatja a `-EnableRegistration` paramétert.

### <a name="list-dns-private-zones"></a>Privát DNS-zónák listázása

Ha kihagyja a zóna nevét a `Get-AzPrivateDnsZone` parancsból, felsorolhatja az egy erőforráscsoportba tartozó összes zónát. Ez a művelet zónaobjektumok tömbjét adja vissza.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Ha a zóna és az erőforráscsoport nevét is kihagyja a `Get-AzPrivateDnsZone` parancsból, felsorolhatja az Azure-előfizetéshez tartozó összes zónát.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Ez eltarthat pár percig.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

Rekordhalmazt a `New-AzPrivateDnsRecordSet` parancsmag használatával hozhat létre. A következő példa létrehoz egy rekordot a relatív name **db** -vel a DNS-zóna **Private.contoso.com**, az erőforráscsoport **MyAzureResourceGroup**. A **db.Private.contoso.com**teljesen minősített neve. A rekord típusa „A”, az IP-címe „10.2.0.4”, az élettartama pedig 3600 másodperc.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>A DNS-rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a **Private.contoso.com** saját zónájának névfeloldását.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa a következő parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Ismételje meg ezt a myVM02 gép esetében is.

### <a name="ping-the-vms-by-name"></a>Virtuális gépek pingelése név alapján

1. A myVM02 gép Windows PowerShell parancssorából pingelje meg a myVM01 gépet az automatikusan regisztrált gazdagépnév használatával:

   ```
   ping myVM01.private.contoso.com
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Most pingelje meg a korábban létrehozott **db** nevet:

   ```
   ping db.private.contoso.com
   ```

   A következőhöz hasonló kimenetnek kell megjelennie:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rá szükség, törölje a **MyAzureResourceGroup** erőforráscsoportot az ebben a cikkben létrehozott erőforrások törléséhez.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure DNS Private Zones forgatókönyvek](private-dns-scenarios.md)
