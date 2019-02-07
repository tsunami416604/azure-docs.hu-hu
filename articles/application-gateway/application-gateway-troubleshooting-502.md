---
title: Az Azure Application Gateway hibás átjáró (502) hibák elhárítása |} A Microsoft Docs
description: Ismerje meg az Application Gateway 502-es kapcsolatos hibák elhárítása
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 1d431ead-d318-47d8-b3ad-9c69f7e08813
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: amsriva
ms.openlocfilehash: 1db16f203755f9afc265495daba056313138a5dc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819450"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Az Application Gatewayben hibás átjáróval kapcsolatos hibák elhárítása

Ismerje meg, hogyan háríthatók el a hibás átjáró (502) hibák az application gateway használata esetén.

## <a name="overview"></a>Áttekintés

Az application gateway konfigurációját követően a felhasználók esetleg előforduló hibák egyike a "kiszolgálóhiba: 502 – Az átjárói vagy proxykiszolgálói feladatokat ellátó webkiszolgáló érvénytelen választ kapott.” hibaüzenet jelenik meg, Ez a hiba a következő fő okok miatt fordulhat elő:

* NSG-t, az URD vagy az egyéni DNS blokkolja a háttérkészlet érintett tagjai.
* Háttérbeli virtuális gépeket vagy virtuálisgép-méretezési csoport példányai nem válaszol, az alapértelmezett állapotadat-mintavétel.
* Az egyéni állapotmintákat érvénytelen vagy nem megfelelő konfigurációja.
* Az Azure Application Gateway [háttér-címkészletben nincs konfigurált vagy nem üres](#empty-backendaddresspool).
* A virtuális gépek vagy a példányok egyike [virtuálisgép-méretezési csoportban kifogástalan](#unhealthy-instances-in-backendaddresspool).
* [Kérelem időtúllépés vagy csatlakozási problémák](#request-time-out) a felhasználói kérelmek.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Hálózati biztonsági csoport, a felhasználó által megadott útvonal, vagy az egyéni DNS-probléma

### <a name="cause"></a>Ok

NSG-t, az URD vagy az egyéni DNS jelenléte miatt a háttérrendszer való hozzáférés le van tiltva, ha a Application Gateway-példány nem fogja tudni elérni a háttérkészlet, és a mintavételi hiba okozza a 502 hibákat eredményez. Vegye figyelembe, hogy az NSG/UDR jelen lehet az Application Gateway-alhálózatot, vagy az alhálózat, az alkalmazás virtuális gépeire telepítve vannak-e. Ehhez hasonlóan a virtuális hálózat egyéni DNS jelenléte is hibát okozhat ha teljes Tartománynevet háttérkészlet-tagokra használja, és nem oldja meg helyesen a felhasználó konfigurált DNS-kiszolgáló a virtuális hálózathoz.

### <a name="solution"></a>Megoldás

NSG-t, az udr-t és a DNS-konfiguráció érvényesítése és haladjon végig a következő lépéseket:
* Ellenőrizze az Application Gateway alhálózathoz társított hálózati biztonsági csoportok. Győződjön meg arról, hogy nem blokkolja-e háttér-kommunikációt.
* Ellenőrizze az Application Gateway alhálózathoz társított udr-t. Győződjön meg arról, hogy UDR van nem irányítja a forgalmat erről a háttérrendszer alhálózatának – például ellenőrizze a hálózati virtuális berendezések vagy Alkalmazásátjáró-alhálózat ExpressRoute/VPN-en keresztül hirdetett útvonalaknak alapértelmezett útvonalakat útválasztási.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzureRmVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Ellenőrizze a hatékony NSG-t és a háttérbeli Virtuálisgép-útvonal

```powershell
Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzureRmEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* A virtuális hálózat egyéni DNS meglétének ellenőrzése. DNS is ellenőrizni kell a kimenetben a VNet tulajdonságainak részletek megtekintésével.

```json
Get-AzureRmVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Ha jelen van, győződjön meg arról, hogy a DNS-kiszolgáló tudja megfelelően feloldani a háttérrendszer háttérkészlettag teljes tartománynevéből.

## <a name="problems-with-default-health-probe"></a>Alapértelmezett állapotadat-mintavétel kapcsolatos problémák

### <a name="cause"></a>Ok

502 hibákat is lehet, hogy az alapértelmezett állapotminta ne legyen képes elérni a háttérbeli virtuális géppel a gyakori mutatók. Amikor egy Application Gateway-példány üzembe van helyezve, automatikusan egy alapértelmezett állapotmintát a BackendHttpSetting tulajdonságainak használata minden egyes értékre állítja be. Ez a Hálózatfigyelő beállítása nincs felhasználói bevitel szükséges. Terheléselosztási szabály konfigurálásakor a társítás van tenni egy BackendHttpSetting és értékre között. Egy alapértelmezett mintavétel van konfigurálva, az egyes ezeket a hozzárendeléseket, és az Application Gateway az értékre, a BackendHttpSetting elemben meghatározott port minden példány rendszeres ellenőrzése kapcsolatot kezdeményez. Következő táblázat az alapértelmezett állapotminta tartozó értékek.

| Mintavétel tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Teszt URL-címe |http://127.0.0.1/ |URL-cím |
| Intervallum |30 |Mintavételi időköz (másodperc) |
| Időtúllépés |30 |Mintavétel időkorlátja másodpercben |
| Nem kifogástalan állapot küszöbértéke |3 |Mintavételi újrapróbálkozások számát. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

### <a name="solution"></a>Megoldás

* Győződjön meg arról, hogy egy alapértelmezett hely van konfigurálva, és a 127.0.0.1 figyel.
* Ha a BackendHttpSetting határozza meg, nem a 80-as portot, az alapértelmezett hely a porton figyeljen kell konfigurálni.
* A hívás http://127.0.0.1:port egy 200-as HTTP-eredménykód adja vissza. Ez a rendszer visszalépteti az 30 másodpercet időkorláton belül.
* Győződjön meg arról, hogy a konfigurált port nyitva-e, hogy nincsenek-e tűzfalszabályok vagy az Azure hálózati biztonsági csoportok, amelyek a konfigurált port bejövő vagy kimenő forgalmát blokkolja.
* Ha az Azure klasszikus virtuális gép vagy Felhőszolgáltatás használja a teljes tartománynév vagy nyilvános IP-cím, győződjön meg arról, hogy a megfelelő [végpont](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) nyitja meg.
* Ha a virtuális gép az Azure Resource Manageren keresztül történik, és kívül esik a virtuális hálózathoz, ahol az Application Gateway üzemel, [hálózati biztonsági csoport](../virtual-network/security-overview.md) kell konfigurálni a kívánt port a hozzáférés engedélyezéséhez.

## <a name="problems-with-custom-health-probe"></a>Egyéni állapotadat-mintavétel kapcsolatos problémák

### <a name="cause"></a>Ok

Egyéni állapot-mintavételei lehetővé teszik a rugalmasságra, az alapértelmezett viselkedés-tesztelés. Egyéni mintavétel használata esetén a felhasználók beállíthatják a mintavételi időköz, az URL-címet, és teszteléséhez elérési útja és a háttérkészlet-példány állapota nem megfelelőként jelölés elfogadásának hány sikertelen válaszokat. A következő további tulajdonságokkal is bővül.

| Mintavétel tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A mintavétel neve. Ez a név segítségével tekintse meg a mintavétel a háttér-HTTP-beállítások. |
| Protokoll |A mintavétel küldéséhez használt protokoll. A mintavétel a háttér-HTTP-beállítások meghatározott protokollt használ. |
| Gazdagép |Állomásnév küldeni a Hálózatfigyelő. Akkor alkalmazható, ha csak az Application Gateway többhelyes kapcsolat van konfigurálva. Ez eltér a virtuális gép állomásnevét. |
| Útvonal |A mintavétel relatív elérési útja. Az érvényes elérési utat indul (/). A mintavétel küldendő \<protokoll\>://\<gazdagép\>:\<port\>\<elérési útja\> |
| Intervallum |Mintavétel gyakorisága másodpercben. Ez a két egymást követő mintavételek közötti időintervallum. |
| Időtúllépés |Mintavétel időkorlátja másodpercben. Ha az időkorláton belül nem érkezik meg egy érvényes válasz, a mintavétel van megjelölve nem sikerült. |
| Nem kifogástalan állapot küszöbértéke |Mintavételi újrapróbálkozások számát. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy az egyéni Állapotminta megfelelően van konfigurálva, az előző táblázatban. Az előző hibaelhárítási lépések mellett is gondoskodjon a következőkről:

* Győződjön meg arról, hogy a mintavétel megfelelően van-e megadva megfelelően a [útmutató](application-gateway-create-probe-ps.md).
* Application Gateway egy adott hely van konfigurálva, ha a gazdagépen alapértelmezés szerint nevét kell megadni, "127.0.0.1", kivéve, ha más módon konfigurált egyéni mintavétel.
* Ellenőrizze, hogy a http:// hívás\<gazdagép\>:\<port\>\<elérési út\> 200-as HTTP eredmény kódot ad vissza.
* Győződjön meg arról, hogy időköz, időtúllépés és UnhealtyThreshold a teljesítményük elfogadható tartományon belül.
* Egy HTTPS-kapcsolaton keresztüli mintavételi, ügyeljen arra, hogy a háttérkiszolgáló SNI nincs szükség a háttérkiszolgálón magát a fallback tanúsítvány konfigurálásával.

## <a name="request-time-out"></a>Kérelem időtúllépése

### <a name="cause"></a>Ok

Ha egy felhasználói kérelem érkezik, az Application Gateway alkalmazza a konfigurált szabályokat a kérelemre, és továbbítja azt a háttérkészlet-példány. A háttér-példány válaszára konfigurálható alapidőponthoz vár. Alapértelmezés szerint ez az időtartam alatt a **30 másodperc**. Az Application Gateway nem kap választ Ez az időtartam alatt a háttéralkalmazás, felhasználói kérelem jelennének meg 502 hiba.

### <a name="solution"></a>Megoldás

Az Application Gateway lehetővé teszi a felhasználóknak a BackendHttpSetting, amely ezután alkalmazható különböző készletek keresztül beállítás megadásához. Különböző háttér-készletek különböző BackendHttpSetting és konfigurálva, ezért másik kérés időtúllépése rendelkezhet.

```powershell
    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Empty BackendAddressPool

### <a name="cause"></a>Ok

Ha az Application Gateway nem rendelkezik virtuális gépek vagy virtuálisgép-méretezési csoportot konfigurálva a háttér-címkészletben levő, nem hajthat végre útválasztást bármely ügyfél kérelem és a hibás átjáró hibát jelez.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a háttér-címkészlet nem üres. Ezt megteheti, vagy a PowerShell, a parancssori felület vagy a portálon keresztül.

```powershell
Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A fenti parancsmag kimenetében nem üres háttér címkészletet kell tartalmaznia. Az alábbiakban a példaként ahol két készletek visszaadott amelyre a háttérbeli virtuális gép teljes Tartományneve vagy IP-címmel vannak konfigurálva. A kiépítési állapota a értékre kell lennie "sikeres".

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Nem megfelelő állapotú példányokat a értékre

### <a name="cause"></a>Ok

Ha értékre összes példánya nem megfelelő állapotú, majd az Application Gateway sem kell bármilyen útvonalat felhasználói kérés háttér. Az eset is ennek oka lehet, háttérpéldányok kifogástalan állapotú, de nem rendelkezik a szükséges alkalmazás telepítése.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a példányok kifogástalan állapotú, és az alkalmazás megfelelően van konfigurálva. Ellenőrizze, hogy az azonos virtuális hálózaton található másik virtuális gépről egy pingelés válaszolhassanak-e a háttérkiszolgáló-példányokhoz. Ha be van állítva egy nyilvános végpontot, győződjön meg arról, hogy egy böngésző kérést a webalkalmazásnak tarthatók karban.

## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem a probléma megoldásához nyissa meg a [támogatási jegyet](https://azure.microsoft.com/support/options/).

