---
title: "Azure Application Gateway hibás átjáró (502-es) hibáinak elhárítása |} Microsoft Docs"
description: "Ismerje meg az alkalmazás átjáró 502 hibák elhárítása"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 6a24e9598362b7c4ff9e2d3371d619fbbd41907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Az Alkalmazásátjáró hibás átjáró hibák elhárítása

Megtudhatja, hogyan Alkalmazásátjáró használatakor kapott hibás átjáró (502-es) hibák elhárítását.

## <a name="overview"></a>Áttekintés

Miután olyan átjárót, a felhasználók esetleg előforduló hibák egyik "kiszolgálóhiba: 502 - webkiszolgáló érvénytelen választ kapott miközben átjáróként vagy proxyként működött". Ez a hiba a következő fő okok miatt fordulhat elő:

* NSG-t, UDR vagy egyéni DNS korlátozza a hozzáférést a háttérrendszer a készlet tagjainak.
* Háttér-virtuális gépek vagy virtuálisgép-méretezési csoport példányait [nem válaszol az alapértelmezett állapotmintáihoz](#problems-with-default-health-probe.md).
* Érvénytelen vagy nem megfelelő [egyéni állapotteljesítmény konfigurációjának](#problems-with-custom-health-probe.md).
* Az Azure Application Gateway [háttér-címkészlet nincs konfigurálva üres](#empty-backendaddresspool).
* A virtuális gépek vagy a példányok egyike [virtuálisgép-méretezési csoport kifogástalan](#unhealthy-instances-in-backendaddresspool).
* [Időtúllépés vagy csatlakozási problémák kérelem](#request-time-out) a felhasználói kérelmek.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Hálózati biztonsági csoport, felhasználó által definiált útvonal vagy egyéni DNS-probléma

### <a name="cause"></a>Ok

Háttér-hozzáférését blokkolja az NSG-t, UDR vagy egyéni DNS miatt, ha az Application Gateway példányok nem fogja tudni elérni a háttérkészlet, és sikertelen mintavételek 502 hibát okozó eredményezne. Vegye figyelembe, hogy az NSG/UDR jelen lehet Application Gateway alhálózat vagy az alhálózat ahol az alkalmazás virtuális gépek vannak telepítve. Hasonlóképpen virtuális egyéni DNS jelenléte is hibát okozhat, ha teljes Tartománynevet háttér a készlet tagjainak használja, és nem oldja meg helyesen a felhasználó konfigurált DNS-kiszolgáló a vnet.

### <a name="solution"></a>Megoldás

NSG-t, a UDR és a DNS-konfiguráció érvényesítése haladjon végig a következő lépéseket:
* Ellenőrizze az Alkalmazásátjáró alhálózathoz társított NSG-ket. Győződjön meg arról, hogy nem blokkolja-e háttér-kommunikációt.
* Alkalmazásátjáró alhálózathoz társított UDR ellenőrzése. Győződjön meg arról, UDR nem arra utasíthatja a forgalom másik lapra a backend alhálózathoz – például ellenőrizze a hálózati virtuális készülékek vagy az alapértelmezett útvonalak hirdetett alkalmazás átjáróalhálózatot ExpressRoute/VPN-en keresztül történő továbbítást.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Ellenőrizze a hatékony NSG-t és a Virtuálisgép-fájlok útvonal

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Ellenőrizze a vnet egyéni DNS jelenlétét. DNS ellenőrizhetők a VNet tulajdonságainak a kimenetben részleteinek megtekintésével.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Ha telepítve van, győződjön meg arról, hogy a DNS-kiszolgáló tudja megfelelően feloldani háttér címkészletet tag FQDN.

## <a name="problems-with-default-health-probe"></a>Alapértelmezett állapotmintáihoz kapcsolatos problémák

### <a name="cause"></a>Ok

502 hibákat is lehet, hogy az alapértelmezett állapotmintáihoz nincs tudni érnie a háttér-virtuális gépek gyakori mutatók. Ha egy alkalmazás átjárópéldány ki van építve, egy alapértelmezett állapotmintáihoz az egyes annak a BackendHttpSetting tulajdonságai BackendAddressPool automatikusan konfigurálja. Ez a Hálózatfigyelő beállítása nincs felhasználói bevitel szükséges. Terheléselosztási szabály konfigurálásakor társítás van tenni egy BackendHttpSetting és BackendAddressPool között. Egy alapértelmezett mintavételt a társításokat mindegyikének van beállítva, és Application Gateway-példányokhoz csatlakoznak, a BackendHttpSetting elemben megadott port BackendAddressPool a rendszeres ellenőrzés kapcsolatot kezdeményez. Következő táblázat az alapértelmezett állapotmintáihoz tartozó értékek.

| Mintavételi tulajdonság | Érték | Leírás |
| --- | --- | --- |
| A mintavételi URL-címe |http://127.0.0.1/ |URL-címe |
| időköz |30 |Mintavételi időköz másodpercben |
| Időtúllépés |30 |Mintavételi időkorlátja másodpercben |
| Sérült küszöbérték |3 |Mintavételi újrapróbálkozások maximális számát. A háttér-kiszolgálófiók van megjelölve, miután az egymást követő mintavételi hiba száma eléri a sérült küszöbérték. |

### <a name="solution"></a>Megoldás

* Győződjön meg arról, hogy egy alapértelmezett webhelyet van konfigurálva, és a 127.0.0.1 figyel.
* Ha BackendHttpSetting határozza meg a 80-as portra, az alapértelmezett webhely adott porton figyeljen kell megadni.
* Http://127.0.0.1:port hívása egy 200-as HTTP-eredménykódja kell visszaadnia. Ez vissza kell adni az 30 másodpercet időkorláton belül.
* Győződjön meg arról, hogy a konfigurált port nyitva-e, és hogy nincsenek-e a tűzfalszabályok vagy Azure hálózati biztonsági csoportok, amelyek a konfigurált port bejövő vagy kimenő forgalmát blokkolja.
* Az Azure klasszikus virtuális gépek vagy a felhőalapú szolgáltatás FQDN vagy a nyilvános IP-cím használata esetén győződjön meg arról, hogy a megfelelő [végpont](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) már meg van nyitva.
* Ha a virtuális gép Azure Resource Manager használatával van konfigurálva, és az Application Gateway telepítési helyét, VNet kívül esik [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) engedélyezi a hozzáférést a kívánt porton kell konfigurálni.

## <a name="problems-with-custom-health-probe"></a>Egyéni állapotmintáihoz kapcsolatos problémák

### <a name="cause"></a>Ok

Egyéni állapotteljesítmény az alapértelmezett viselkedés probing további rugalmasságával lehetővé. Egyéni mintavételt használata esetén a felhasználók beállíthatják a mintavételi időköznek, az URL-címet, és elérési útja tesztelése és fogadja el, hogy megsérült a háttér-készlet példányhoz való megjelölése előtt hány hibás válaszokat. A következő további tulajdonságokkal is bővül.

| Mintavételi tulajdonság | Leírás |
| --- | --- |
| Név |A mintavétel neve. Ez a név segítségével tekintse meg a mintavétel a háttér-HTTP-beállításait. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttér-HTTP-beállítások között megadott protokollt használ. |
| Gazdagép |A mintavétel küldendő állomásnevet. Akkor alkalmazható, csak akkor, ha több hely úgy van konfigurálva, az alkalmazás-átjárón. Ez eltér a virtuális gép állomásnevét. |
| Útvonal |A mintavétel relatív elérési útja. Az érvényes elérési utat elindítja a "/". A mintavétel küldött \<protokoll\>://\<állomás\>:\<port\>\<elérési útja\> |
| időköz |Mintavételi időköz másodpercben. Ez a két egymást követő mintavételek menüpontban közötti időközt. |
| Időtúllépés |Mintavételi időtúllépés másodpercben. Egy érvényes válasz nem érkezett meg a megadott időn belül, ha a mintavételi hibás jelölést. |
| Sérült küszöbérték |Mintavételi újrapróbálkozások maximális számát. A háttér-kiszolgálófiók van megjelölve, miután az egymást követő mintavételi hiba száma eléri a sérült küszöbérték. |

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy az egyéni állapot-mintavételi megfelelően van konfigurálva, az előző táblázatban. Az előző hibaelhárítási lépések mellett is ellenőrizze a következőket:

* Győződjön meg arról, hogy a mintavétel helyesen van-e megadva megfelelően a [útmutató](application-gateway-create-probe-ps.md).
* Ha Alkalmazásátjáró egy adott hely van konfigurálva, a gazdagépen alapértelmezés szerint nevét kell megadni a "127.0.0.1", kivéve, ha az egyéni tesztműveleti egyéb konfigurálni.
* Győződjön meg arról, hogy http:// hívása\<állomás\>:\<port\>\<elérési\> 200-as HTTP eredmény kódot ad vissza.
* Ellenőrizze, hogy időköz, időtúllépési és UnhealtyThreshold belül elfogadható tartományban.
* Ha egy HTTPS-kapcsolaton keresztül mintavételi, győződjön meg arról, hogy a háttérkiszolgáló SNI nincs szükség a háttérkiszolgálón magát a fallback tanúsítvány konfigurálásával. 
* Gondoskodjon arról, hogy időköz, időtúllépési és UnhealtyThreshold belül elfogadható tartományban.

## <a name="request-time-out"></a>Kérelem időtúllépése

### <a name="cause"></a>Ok

Amikor egy felhasználói kérelem érkezik, Application Gateway konfigurált szabályok vonatkozik a kérelmet, és továbbítja a háttér-készlet példánya. A konfigurálható időtartam, a háttér-példányból választ vár. Alapértelmezés szerint ez az időtartam alatt van **30 másodperces**. Ha a Alkalmazásátjáró nem kapott választ háttéralkalmazás ezen időszak, felhasználói kérelem 502 hiba jelenik meg.

### <a name="solution"></a>Megoldás

Alkalmazásátjáró BackendHttpSetting, majd alkalmazhatja különböző készletek, amelyek segítségével a beállítás lehetővé teszi. Különböző háttér-készletek különböző BackendHttpSetting és így különböző kérelem időkorlátja konfigurált rendelkezhet.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Üres BackendAddressPool

### <a name="cause"></a>Ok

Az Alkalmazásátjáró e egyetlen virtuális gépek vagy virtuálisgép-méretezési készletben konfigurálva az a háttér-címkészlethez, azt nem hajt végre semmilyen felhasználói kérelem és a hibás átjáró hibát jelez.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a háttér-címkészlet nem üres. Ezt megteheti, vagy PowerShell, a parancssori felületen vagy a portálon.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A fenti parancsmag kimenetében nem üres háttér címkészletet kell tartalmaznia. Az alábbiakban látható egy példa ahol két rendelkezik visszaadott amely háttérkiszolgáló virtuális gépek teljes Tartománynevét vagy IP-címmel vannak konfigurálva. A kiépítési állapotát a BackendAddressPool kell kell "sikeres".

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Nem megfelelő állapotú példány BackendAddressPool

### <a name="cause"></a>Ok

Ha BackendAddressPool összes példánya sérült állapotban, majd Alkalmazásátjáró nem kellene bármely háttér útvonal felhasználói kérés. Az eset is ennek oka lehet, ha háttér-példányok kifogástalan állapotú, de nem rendelkezik a szükséges alkalmazás telepítése.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a példány állapota kifogástalan és az alkalmazás megfelelően van konfigurálva. Ellenőrizze, hogy a háttér-példányok képesek-e egy másik virtuális gép ugyanazon virtuális válaszol a pingelésre. Ha be van állítva egy nyilvános végpontot, győződjön meg arról, hogy a böngésző kérést a webalkalmazásnak használható.

## <a name="next-steps"></a>Következő lépések

Ha az előző lépések nem a probléma megoldásához nyissa meg a [támogatja a jegy](https://azure.microsoft.com/support/options/).

