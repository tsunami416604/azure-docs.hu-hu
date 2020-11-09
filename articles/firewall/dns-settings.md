---
title: Azure Firewall DNS-beállítások
description: A Azure Firewall a DNS-kiszolgáló és a DNS-proxy beállításainak használatával konfigurálhatja.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380946"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall DNS-beállítások

Beállíthat egy egyéni DNS-kiszolgálót, és engedélyezheti a DNS-proxyt a Azure Firewall számára. Ezeket a beállításokat a **DNS-beállítások** lapon konfigurálhatja a tűzfal telepítésekor vagy később is.

## <a name="dns-servers"></a>DNS-kiszolgálók

A DNS-kiszolgáló karbantartja és feloldja a tartományneveket az IP-címekre. Alapértelmezés szerint a Azure Firewall Azure DNS használ a névfeloldáshoz. A **DNS-kiszolgáló** beállítással konfigurálhatja a saját DNS-kiszolgálókat Azure Firewall névfeloldáshoz. Egyetlen vagy több kiszolgálót is beállíthat.

> [!NOTE]
> A Azure Firewall Managerrel felügyelt Azure-tűzfalak esetén a DNS-beállítások a társított Azure Firewall házirendben vannak konfigurálva.

### <a name="configure-custom-dns-servers---azure-portal"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure Portal

1. A Azure Firewall **Beállítások** területen válassza a **DNS-beállítások** elemet.
2. A **DNS-kiszolgálók** területen beírhatja vagy hozzáadhat olyan meglévő DNS-kiszolgálókat, amelyek korábban meg lettek adva a Virtual Network.
3. Kattintson a **Mentés** gombra.
4. A tűzfal most a névfeloldáshoz a megadott DNS-kiszolgáló (k) re irányítja a DNS-forgalmat.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-kiszolgálók":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure CLI

Az alábbi példa frissíti a Azure Firewall egyéni DNS-kiszolgálókkal az Azure CLI használatával.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Ehhez a parancshoz `az network firewall` telepíteni kell az Azure CLI bővítményt `azure-firewall` . A parancs használatával telepíthető `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure PowerShell

Az alábbi példa a Azure PowerShell használatával frissíti a Azure Firewall egyéni DNS-kiszolgálókkal.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS-proxy

Beállíthatja, hogy a Azure Firewall DNS-proxyként működjön. A DNS-proxy közvetítőként működik az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérelmek esetében. Ha egyéni DNS-kiszolgálót konfigurál, engedélyeznie kell a DNS-proxyt, hogy elkerülje a DNS-feloldás eltérését, és engedélyezze a teljes tartománynevek szűrését a hálózati szabályokban.

Ha nem engedélyezi a DNS-proxyt, előfordulhat, hogy az ügyféltől érkező DNS-kérések egy másik időpontban utaznak a DNS-kiszolgálóra, vagy egy másik választ adnak vissza a tűzfalhoz képest. A DNS-proxy Azure Firewall az ügyfelek kéréseinek elérési útjában az inkonzisztencia elkerülése érdekében.

Két gyorsítótárazási függvény létezik, ha Azure Firewall DNS-proxy:

- Pozitív gyorsítótár – a DNS-feloldás sikeres. A tűzfal a csomag vagy objektum TTL (élettartam) értékét használja. 

- Negatív gyorsítótár – a DNS-feloldás nem válaszol, vagy nincs megoldás. A tűzfal egy órára gyorsítótárazza ezt az információt.

A DNS-proxy az összes megoldott IP-címet tárolja a hálózati szabályok teljes tartománynevéről. Ajánlott eljárásként használjon olyan teljes tartományneveket, amelyek egy IP-címhez vannak feloldva.  

### <a name="dns-proxy-configuration"></a>DNS-proxy konfigurációja

A DNS-proxy konfigurálásához három lépés szükséges:
1. Engedélyezze a DNS-proxyt Azure Firewall DNS-beállításokban.
2. Igény szerint konfigurálhatja az egyéni DNS-kiszolgálót, vagy használhatja a megadott alapértelmezett értéket.
3. Végül konfigurálnia kell a Azure Firewall magánhálózati IP-címét egyéni DNS-címként a virtuális hálózat DNS-kiszolgálójának beállításaiban. Ez biztosítja, hogy a DNS-forgalom Azure Firewallre legyen irányítva.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS-proxy konfigurálása – Azure Portal

A DNS-proxy konfigurálásához konfigurálnia kell a virtuális hálózati DNS-kiszolgálók beállítást a tűzfal magánhálózati IP-címének használatára. Ezután engedélyezze a DNS-proxyt Azure Firewall **DNS-beállításokban**.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása 

1. Válassza ki azt a virtuális hálózatot, ahol a DNS-forgalom a Azure Firewall keresztül lesz átirányítva.
2. A **Beállítások** területen válassza a **DNS-kiszolgálók** elemet.
3. Válassza az **Egyéni** lehetőséget a **DNS-kiszolgálók** területen.
4. Adja meg a tűzfal magánhálózati IP-címét.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális hálózathoz csatlakozó virtuális gépeket, hogy azok hozzá legyenek rendelve az új DNS-kiszolgáló beállításaihoz. A virtuális gépek továbbra is a jelenlegi DNS-beállításokat használják, amíg újra nem indítják őket.

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

1. Válassza ki a Azure Firewall.
2. A **Beállítások** területen válassza a **DNS-beállítások** elemet.
3. Alapértelmezés szerint a **DNS-proxy** le van tiltva. Ha engedélyezve van, a tűzfal figyeli a 53-es portot, és továbbítja a DNS-kéréseket a konfigurált DNS-kiszolgálókra.
4. Tekintse át a **DNS-kiszolgálók** konfigurációját, és győződjön meg arról, hogy a beállítások megfelelőek-e a környezetéhez.
5. Kattintson a **Mentés** gombra.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS-proxy konfigurálása – Azure CLI

Az Azure CLI-vel konfigurálhatja a DNS-proxybeállításokat a Azure Firewallban, és a virtuális hálózatokat a Azure Firewall DNS-kiszolgálóként való használatára is frissítheti.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

Ez a példa úgy konfigurálja a VNet, hogy Azure Firewall használjon DNS-kiszolgálóként.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

Ez a példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS-proxy konfigurálása – Azure PowerShell

A Azure PowerShell használatával konfigurálhatja a DNS-proxybeállításokat Azure Firewall és frissítheti a virtuális hálózatokat a Azure Firewall DNS-kiszolgálóként való használatára.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

 Ez a példa úgy konfigurálja a VNet, hogy Azure Firewall használjon DNS-kiszolgálóként.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

Ez a példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Következő lépések

[FQDN-szűrés a hálózati szabályokban](fqdn-filtering-network-rules.md)
