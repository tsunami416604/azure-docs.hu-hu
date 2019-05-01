---
title: Az Azure Application Gateway hibás átjáró (502) hibák elhárítása
description: Ismerje meg az Application Gateway 502-es kapcsolatos hibák elhárítása
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: amsriva
ms.openlocfilehash: 2a1c7e480e896da6852949c9d765d17290e4e9ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697163"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Az Application Gatewayben hibás átjáróval kapcsolatos hibák elhárítása

Ismerje meg, hogyan háríthatók el a hibás átjáró (502) hibák az Azure Application Gateway használata esetén.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Az application gateway konfigurációját követően látni hibákat egyik "kiszolgálóhiba: 502 – Az átjárói vagy proxykiszolgálói feladatokat ellátó webkiszolgáló érvénytelen választ kapott.” hibaüzenet jelenik meg, Ez a hiba a következő fő okok miatt fordulhat elő:

* NSG-t, az URD vagy az egyéni DNS blokkolja a háttérkészlet érintett tagjai.
* Háttérbeli virtuális gépeket vagy virtuálisgép-méretezési csoport példányainak az alapértelmezett az állapotfigyelő mintavételező nem válaszol.
* Az egyéni állapotmintákat érvénytelen vagy nem megfelelő konfigurációja.
* Az Azure Application Gateway [háttérkészlet nem konfigurált vagy üres](#empty-backendaddresspool).
* A virtuális gépek vagy a példányok egyike [virtuálisgép-méretezési csoportban kifogástalan](#unhealthy-instances-in-backendaddresspool).
* [Kérelem időtúllépés vagy csatlakozási problémák](#request-time-out) a felhasználói kérelmek.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Hálózati biztonsági csoport, a felhasználó által megadott útvonal, vagy az egyéni DNS-probléma

### <a name="cause"></a>Ok

A háttérrendszer a hozzáférést egy NSG-t, az URD vagy az egyéni DNS miatt le van tiltva, ha az application gateway-példány nem érhető el a háttérkészlethez. Ennek hatására a mintavételi hiba 502 hibákat eredményez.

Az NSG/UDR jelen lehet az application gateway alhálózatának vagy az alhálózat, az alkalmazás virtuális gépeire telepítve vannak-e.

Ehhez hasonlóan a virtuális hálózat egy egyéni DNS jelenléte is hibát okozhat. Háttérkészlet-tagokra használt teljes tartománynév lehet, hogy feloldása nem megfelelő a felhasználó konfigurált DNS-kiszolgáló által a virtuális hálózathoz.

### <a name="solution"></a>Megoldás

NSG-t, az udr-t és a DNS-konfiguráció érvényesítése és haladjon végig a következő lépéseket:

* Ellenőrizze, hogy az application gateway alhálózatának társított NSG-ket. Győződjön meg arról, hogy háttérbeli kommunikációt nem blokkolja-e.
* Ellenőrizze, hogy az application gateway alhálózatának tartozó udr-t. Győződjön meg arról, hogy az udr-t nem irányítja a forgalmat a háttérbeli alhálózat távolabbi. Például ellenőrizze a hálózati virtuális berendezések vagy az application gateway alhálózatának ExpressRoute/VPN-en keresztül hirdetett útvonalaknak alapértelmezett útvonalakat útválasztási.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Ellenőrizze a hatékony NSG-t és a háttérbeli Virtuálisgép-útvonal

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* A virtuális hálózat egyéni DNS meglétének ellenőrzése. DNS is ellenőrizni kell a kimenetben a VNet tulajdonságainak részletek megtekintésével.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Ha jelen van, győződjön meg arról, hogy a DNS-kiszolgáló képes legyen feloldani a háttérrendszer háttérkészlettag teljes tartománynevéből megfelelően.

## <a name="problems-with-default-health-probe"></a>Alapértelmezett állapotadat-mintavétel kapcsolatos problémák

### <a name="cause"></a>Ok

502 hibákat is lehet, hogy az alapértelmezett állapotminta a háttérbeli virtuális géppel nem tudja elérni a gyakori mutatók.

Amikor egy application gateway-példány üzembe van helyezve, automatikusan egy alapértelmezett állapotmintát a BackendHttpSetting tulajdonságainak használata minden egyes értékre állítja be. Ez a Hálózatfigyelő beállítása nincs felhasználói bevitel szükséges. Terheléselosztási szabály konfigurálásakor a társítás van tenni egy BackendHttpSetting és a egy értékre között. Egy alapértelmezett mintavételt minden ilyen társítások van beállítva, és az application gateway minden példány rendszeres ellenőrzése kapcsolatot indítja el a BackendHttpSetting elemben megadott port, az értékre. 

Az alábbi táblázat az alapértelmezett állapotminta társított értékeit sorolja fel:

| Mintavétel tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Teszt URL-címe |`http://127.0.0.1/` |URL-cím |
| Interval |30 |Mintavételi időköz (másodperc) |
| Időtúllépés |30 |Mintavétel időkorlátja másodpercben |
| Nem kifogástalan állapot küszöbértéke |3 |Mintavételi újrapróbálkozások számát. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

### <a name="solution"></a>Megoldás

* Győződjön meg arról, hogy egy alapértelmezett hely van konfigurálva, és a 127.0.0.1 figyel.
* Ha a BackendHttpSetting határozza meg, nem a 80-as portot, az alapértelmezett hely a porton figyeljen kell konfigurálni.
* A hívás `http://127.0.0.1:port` egy 200-as HTTP-eredménykód adja vissza. Ez a rendszer visszalépteti a 30 másodperces időkorláton belül.
* Győződjön meg arról, hogy a konfigurált port nyitva-e, hogy nincsenek-e tűzfalszabályok vagy az Azure hálózati biztonsági csoportok, amelyek a konfigurált port bejövő vagy kimenő forgalmát blokkolja.
* Az Azure klasszikus virtuális gépek vagy Cloud Service egy teljesen minősített tartománynév vagy a nyilvános IP-cím használata esetén győződjön meg arról, hogy a megfelelő [végpont](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) nyitja meg.
* Ha a virtuális gép az Azure Resource Manageren keresztül történik, és kívül esik a virtuális hálózathoz, ahol az application gateway telepíti, egy [hálózati biztonsági csoport](../virtual-network/security-overview.md) kell konfigurálni a kívánt port a hozzáférés engedélyezéséhez.

## <a name="problems-with-custom-health-probe"></a>Egyéni állapotadat-mintavétel kapcsolatos problémák

### <a name="cause"></a>Ok

Egyéni állapot-mintavételei lehetővé teszik a rugalmasságra, az alapértelmezett viselkedés-tesztelés. Egyéni mintavétel használata esetén konfigurálhatja úgy a mintavételi időköz, az URL-cím, tesztelje elérési útját és fogadja el a háttérkészlet-példány állapota nem megfelelőként való megjelölése előtt hány sikertelen válaszokat.

A következő további tulajdonságok lettek hozzáadva:

| Mintavétel tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A mintavétel neve. Ez a név segítségével tekintse meg a mintavétel a háttér-HTTP-beállítások. |
| Protocol |A mintavétel küldéséhez használt protokoll. A mintavétel a háttér-HTTP-beállítások meghatározott protokollt használ. |
| Gazdagép |Állomásnév küldeni a Hálózatfigyelő. Akkor alkalmazható, csak akkor, ha az application gateway többhelyes kapcsolat van konfigurálva. Ez eltér a virtuális gép állomásnevét. |
| Útvonal |A mintavétel relatív elérési útja. Az érvényes elérési utat indul (/). A mintavétel küldendő \<protokoll\>://\<gazdagép\>:\<port\>\<elérési útja\> |
| Interval |Mintavétel gyakorisága másodpercben. Ez a két egymást követő mintavételek közötti időintervallum. |
| Időtúllépés |Mintavétel időkorlátja másodpercben. Ha egy érvényes válasz az időkorláton belül nem kapott, a mintavétel van megjelölve nem sikerült. |
| Nem kifogástalan állapot küszöbértéke |Mintavételi újrapróbálkozások számát. A háttérkiszolgáló-van megjelölve, miután az egymást követő mintavételi hiba száma eléri a nem kifogástalan állapot küszöbértéke. |

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy az egyéni Állapotminta megfelelően van konfigurálva, az előző táblázatban. Az előző hibaelhárítási lépések mellett is gondoskodjon a következőkről:

* Győződjön meg arról, hogy a mintavétel megfelelően van-e megadva megfelelően a [útmutató](application-gateway-create-probe-ps.md).
* Ha az application gateway egy adott hely van konfigurálva, a gazdagépen alapértelmezés szerint nevét kell megadni `127.0.0.1`, kivéve, ha más módon konfigurált egyéni mintavétel.
* Ellenőrizze, hogy a http:// hívás\<gazdagép\>:\<port\>\<elérési út\> 200-as HTTP eredmény kódot ad vissza.
* Győződjön meg arról, hogy időköz, időtúllépés és UnhealtyThreshold a teljesítményük elfogadható tartományon belül.
* Egy HTTPS-kapcsolaton keresztüli mintavételi, ügyeljen arra, hogy a háttérkiszolgáló SNI nincs szükség a háttérkiszolgálón magát a fallback tanúsítvány konfigurálásával.

## <a name="request-time-out"></a>Kérelem időtúllépése

### <a name="cause"></a>Ok

Amikor egy felhasználói kérelem érkezik, az application gateway alkalmazza a konfigurált szabályokat a kérelemre, és a egy háttérkészlet-példányba továbbítja azt. A háttér-példány válaszára konfigurálható alapidőponthoz vár. Alapértelmezés szerint ez az időtartam alatt a **20** másodperc. Ha az application gateway nem ez az időtartam alatt a háttéralkalmazás válasz érkezik, a felhasználói kérelem beolvasása egy 502 hiba.

### <a name="solution"></a>Megoldás

Az Application Gateway lehetővé teszi, hogy ez a beállítás a BackendHttpSetting, amely ezután alkalmazható különböző készletek keresztül. Különböző háttér-készletek különböző BackendHttpSetting, és a egy másik kérelem időtúllépés konfigurált rendelkezhet.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Empty BackendAddressPool

### <a name="cause"></a>Ok

Ha az application gateway nem rendelkezik virtuális gépek vagy virtuálisgép-méretezési csoportot a háttér-címkészlet konfigurálva, nem hajthat végre útválasztást minden ügyfélkérés és küld egy hibás átjáró hiba.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a háttér-címkészlet nem üres. Ezt megteheti, vagy a PowerShell, a parancssori felület vagy a portálon keresztül.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A fenti parancsmag kimenetében nem üres háttér címkészletet kell tartalmaznia. Az alábbi példa bemutatja, hogy két készletet ad vissza, amely a teljes tartománynév vagy egy IP-címeket biztosítani a háttérbeli virtuális gépek vannak konfigurálva. A kiépítési állapota a értékre kell lennie "sikeres".

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

Nem megfelelő állapotú értékre összes példánya esetén az application gateway összes útvonal felhasználói kérés háttér nem rendelkezik. Az eset is Ez akkor lehet, ha a háttérkiszolgáló-példányokhoz kifogástalan állapotú, de nem rendelkezik a szükséges alkalmazás telepítése.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a példányok kifogástalan állapotú, és az alkalmazás megfelelően van konfigurálva. Ellenőrizze, hogy ha a háttérkiszolgáló-példányokhoz válaszolhat a pingelés az azonos virtuális hálózaton található másik virtuális gépről. Ha be van állítva egy nyilvános végpontot, győződjön meg arról a böngésző kérést a webalkalmazásnak tarthatók karban.

## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem oldják meg a probléma, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).

