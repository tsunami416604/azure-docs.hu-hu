---
title: Oktatóanyag – Saját Azure DNS-zóna létrehozása az Azure PowerShell használatával
description: Ebben az oktatóanyagban egy saját DNS-zónát és -rekordot hozunk létre és tesztelünk az Azure DNS-ben. Ez egy lépésenkénti útmutató, amellyel az Azure PowerShell használatával létrehozhatja és kezelheti az első saját DNS-zónáját és -rekordját.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/24/2018
ms.author: victorh
ms.openlocfilehash: 872227e0521bd54e6bf7fdbe3626dfca34170863
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257725"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Oktatóanyag: Saját Azure DNS-zóna létrehozása az Azure PowerShell használatával

Ez az oktatóanyag végigvezeti az első saját DNS-zóna és -rekord Azure PowerShell-lel való létrehozásának lépésein.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket *feloldási virtuális hálózatnak* nevezzük. Megadhat olyan virtuális hálózatot is, amelyhez az Azure DNS eszköznévrekordokat biztosít a virtuális gépek létrehozásakor, IP-címének módosításakor vagy törlésekor.  Ezt *regisztrációs virtuális hálózatnak* nevezzük.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Saját DNS-zóna létrehozása
> * Tesztelési célú virtuális gépek létrehozása
> * További DNS-rekord létrehozása
> * A saját zóna tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Igény szerint az oktatóanyag az [Azure CLI](private-dns-getstarted-cli.md) használatával is elvégezhető.

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Először hozzon létre egy erőforráscsoportot, amely a DNS-zónát tartalmazza majd: 

```azurepowershell
New-AzureRMResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Saját DNS-zóna létrehozása

DNS-zóna létrehozásához használja a `New-AzureRmDnsZone` parancsmagot, és adja meg a *Private* értéket a **ZoneType** paraméterhez. Az alábbi példaparancs a **MyAzureResourceGroup** erőforráscsoportban létrehozza a **contoso.local** DNS-zónát, majd elérhetővé teszi azt a **MyAzureVnet** nevű virtuális hálózat számára.

A **ZoneType** paraméter kihagyása esetén a zóna nyilvános zónaként jön létre, ezért a paraméter megadása kötelező a privát zónák létrehozásához. 

```azurepowershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Ha csupán a névfeloldáshoz kívánt zónát létrehozni (gazdagépnevek automatikus létrehozása nélkül), a *RegistrationVirtualNetworkId* paraméter helyett használhatja a *ResolutionVirtualNetworkId* paramétert.

> [!NOTE]
> Ekkor az automatikusan létrehozott gazdagépnév-rekordok nem jelennek meg. Később azonban a tesztelés során megbizonyosodhat róla, hogy léteznek.

### <a name="list-dns-private-zones"></a>Privát DNS-zónák listázása

Ha kihagyja a zóna nevét a `Get-AzureRmDnsZone` parancsból, felsorolhatja az egy erőforráscsoportba tartozó összes zónát. Ez a művelet zónaobjektumok tömbjét adja vissza.

```azurepowershell
Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Ha a zóna és az erőforráscsoport nevét is kihagyja a `Get-AzureRmDnsZone` parancsból, felsorolhatja az Azure-előfizetéshez tartozó összes zónát.

```azurepowershell
Get-AzureRmDnsZone
```

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzureRmVm `
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

Rekordhalmazt a `New-AzureRmDnsRecordSet` parancsmag használatával hozhat létre. Az alábbi példa a **MyAzureResourceGroup** erőforráscsoport **contoso.local** DNS-zónájában hoz létre egy rekordot **db** relatív néven. A rekordkészlet teljes neve **db.contoso.local**. A rekord típusa „A”, az IP-címe „10.2.0.4”, az élettartama pedig 3600 másodperc.

```azurepowershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>A DNS-rekordok megtekintése

A zónájában lévő DNS-rekordokat a következő paranccsal listázhatja:

```azurepowershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyAzureResourceGroup
```
Ne feledje, hogy a két tesztelési célú virtuális gép automatikusan létrehozott A rekordjai nem jelennek meg.

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a **contoso.local** privát zóna névfeloldását.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa az alábbi parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Ismételje meg ezt a myVM02 gép esetében is.

### <a name="ping-the-vms-by-name"></a>Virtuális gépek pingelése név alapján

1. A myVM02 gép Windows PowerShell parancssorából pingelje meg a myVM01 gépet az automatikusan regisztrált gazdagépnév használatával:
   ```
   ping myVM01.contoso.local
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
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
   ping db.contoso.local
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rájuk szükség, a **MyAzureResourceGroup** erőforráscsoport törlésével törölheti az oktatóanyagban létrehozott erőforrásokat.

```azurepowershell
Remove-AzureRMResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy privát DNS-zónát, létrehozott egy DNS-rekordot, és tesztelte a zónát.
Ezután behatóbban megismerheti a DNS-zónákat.

> [!div class="nextstepaction"]
> [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md)




