---
title: Azure Firewall DNS-beállítások (előzetes verzió)
description: A Azure Firewall a DNS-kiszolgáló és a DNS-proxy beállításainak használatával konfigurálhatja.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: efe608437f350a29147cf10989cdb6c17a23196d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397994"
---
# <a name="azure-firewall-dns-settings-preview"></a>Azure Firewall DNS-beállítások (előzetes verzió)

> [!IMPORTANT]
> Azure Firewall DNS-beállítások jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Beállíthat egy egyéni DNS-kiszolgálót, és engedélyezheti a DNS-proxyt a Azure Firewall számára. Ezeket a beállításokat a **DNS-beállítások** lapon konfigurálhatja a tűzfal telepítésekor vagy később is.

## <a name="dns-servers"></a>DNS-kiszolgálók

A DNS-kiszolgáló karbantartja és feloldja a tartományneveket az IP-címekre. Alapértelmezés szerint a Azure Firewall Azure DNS használ a névfeloldáshoz. A **DNS-kiszolgáló** beállítással konfigurálhatja a saját DNS-kiszolgálókat Azure Firewall névfeloldáshoz. Egyetlen vagy több kiszolgálót is beállíthat.

> [!NOTE]
> A Azure Firewall Managerrel felügyelt Azure-tűzfalak esetén a DNS-beállítások a társított Azure Firewall házirendben vannak konfigurálva.

### <a name="configure-custom-dns-servers-preview---azure-portal"></a>Egyéni DNS-kiszolgálók konfigurálása (előzetes verzió) – Azure Portal

1. A Azure Firewall **Beállítások** területen válassza a **DNS-beállítások** elemet.
2. A **DNS-kiszolgálók** területen beírhatja vagy hozzáadhat olyan meglévő DNS-kiszolgálókat, amelyek korábban meg lettek adva a Virtual Network.
3. Válassza a **Mentés** lehetőséget.
4. A tűzfal most a névfeloldáshoz a megadott DNS-kiszolgáló (k) re irányítja a DNS-forgalmat.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-kiszolgálók":::

### <a name="configure-custom-dns-servers-preview---azure-cli"></a>Egyéni DNS-kiszolgálók konfigurálása (előzetes verzió) – Azure CLI

Az alábbi példa frissíti a Azure Firewall egyéni DNS-kiszolgálókkal az Azure CLI használatával.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Ehhez a parancshoz `az network firewall` telepíteni kell az Azure CLI bővítményt `azure-firewall` . A parancs használatával telepíthető `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers-preview---azure-powershell"></a>Egyéni DNS-kiszolgálók konfigurálása (előzetes verzió) – Azure PowerShell

Az alábbi példa a Azure PowerShell használatával frissíti a Azure Firewall egyéni DNS-kiszolgálókkal.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy-preview"></a>DNS-proxy (előzetes verzió)

Beállíthatja, hogy a Azure Firewall DNS-proxyként működjön. A DNS-proxy közvetítőként működik az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérelmek esetében. Ha egyéni DNS-kiszolgálót konfigurál, engedélyeznie kell a DNS-proxyt, hogy elkerülje a DNS-feloldás eltérését, és engedélyezze a teljes tartománynevek szűrését a hálózati szabályokban.

Ha nem engedélyezi a DNS-proxyt, előfordulhat, hogy az ügyféltől érkező DNS-kérések egy másik időpontban utaznak a DNS-kiszolgálóra, vagy egy másik választ adnak vissza a tűzfalhoz képest. A DNS-proxy Azure Firewall az ügyfelek kéréseinek elérési útjában az inkonzisztencia elkerülése érdekében.

A DNS-proxy konfigurálásához három lépés szükséges:
1. Engedélyezze a DNS-proxyt Azure Firewall DNS-beállításokban.
2. Igény szerint konfigurálhatja az egyéni DNS-kiszolgálót, vagy használhatja a megadott alapértelmezett értéket.
3. Végül konfigurálnia kell a Azure Firewall magánhálózati IP-címét egyéni DNS-címként a virtuális hálózat DNS-kiszolgálójának beállításaiban. Ez biztosítja, hogy a DNS-forgalom Azure Firewallre legyen irányítva.

### <a name="configure-dns-proxy-preview---azure-portal"></a>DNS-proxy konfigurálása (előzetes verzió) – Azure Portal

A DNS-proxy konfigurálásához konfigurálnia kell a virtuális hálózati DNS-kiszolgálók beállítást a tűzfal magánhálózati IP-címének használatára. Ezután engedélyezze a DNS-proxyt Azure Firewall **DNS-beállításokban**.

#### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása 

1. Válassza ki azt a virtuális hálózatot, ahol a DNS-forgalom a Azure Firewall keresztül lesz átirányítva.
2. A **Beállítások** területen válassza a **DNS-kiszolgálók** elemet.
3. Válassza az **Egyéni** lehetőséget a **DNS-kiszolgálók** területen.
4. Adja meg a tűzfal magánhálózati IP-címét.
5. Válassza a **Mentés** lehetőséget.
6. Indítsa újra a virtuális hálózathoz csatlakozó virtuális gépeket, hogy azok hozzá legyenek rendelve az új DNS-kiszolgáló beállításaihoz. A virtuális gépek továbbra is a jelenlegi DNS-beállításokat használják, amíg újra nem indítják őket.

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy engedélyezése (előzetes verzió)

1. Válassza ki a Azure Firewall.
2. A **Beállítások** területen válassza a **DNS-beállítások** elemet.
3. Alapértelmezés szerint a **DNS-proxy** le van tiltva. Ha engedélyezve van, a tűzfal figyeli a 53-es portot, és továbbítja a DNS-kéréseket a konfigurált DNS-kiszolgálókra.
4. Tekintse át a **DNS-kiszolgálók** konfigurációját, és győződjön meg arról, hogy a beállítások megfelelőek-e a környezetéhez.
5. Válassza a **Mentés** lehetőséget.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-proxy":::

### <a name="configure-dns-proxy-preview---azure-cli"></a>DNS-proxy konfigurálása (előzetes verzió) – Azure CLI

A DNS-proxybeállítások konfigurálása a Azure Firewall és a virtuális hálózatok frissítése a Azure Firewall használatára a DNS-kiszolgáló az Azure CLI használatával végezhető el.

#### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

Ez a példa úgy konfigurálja a VNet, hogy Azure Firewall használjon DNS-kiszolgálóként.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy engedélyezése (előzetes verzió)

Ez a példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

### <a name="configure-dns-proxy-preview---azure-powershell"></a>DNS-proxy konfigurálása (előzetes verzió) – Azure PowerShell

A DNS-proxy beállításainak konfigurálása és a virtuális hálózatok frissítése a Azure Firewall használatára a DNS-kiszolgáló Azure PowerShell használatával végezhető el.

#### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

 Ez a példa úgy konfigurálja a VNet, hogy Azure Firewall használjon DNS-kiszolgálóként.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

#### <a name="enable-dns-proxy-preview"></a>DNS-proxy engedélyezése (előzetes verzió)

Ez a példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>További lépések

[FQDN-szűrés a hálózati szabályokban](fqdn-filtering-network-rules.md)
