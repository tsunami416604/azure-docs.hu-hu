---
title: Hibás átjáróhibák elhárítása - Azure Application Gateway
description: 'Megtudhatja, hogy miként hárítható el az Application Gateway Server Hiba: 502 – A webkiszolgáló érvénytelen választ kapott, miközben átjáróként vagy proxykiszolgálóként működt.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130469"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Hibás átjárókkal kapcsolatos hibák elhárítása az Application Gatewayben

Ismerje meg, hogyan háríthatja el az Azure Application Gateway használata során kapott hibás átjáróhibákat (502).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Az alkalmazásátjáró konfigurálása után az egyik előforduló hiba a következő: "Kiszolgálóhiba: 502 – A webkiszolgáló érvénytelen választ kapott, miközben átjáróként vagy proxykiszolgálóként működt". Ez a hiba a következő fő okok miatt fordulhat elő:

* Az NSG, az UDR vagy az egyéni DNS blokkolja a háttérkészlet-tagok hoz való hozzáférést.
* A háttérvirtuális gépek vagy a virtuálisgép-méretezési csoport példányai nem válaszolnak az alapértelmezett állapotmintára.
* Az egyéni állapotminta-próbák érvénytelen vagy nem megfelelő konfigurációja.
* Az Azure Application Gateway [háttérkészlete nincs konfigurálva vagy üresen.](#empty-backendaddresspool)
* A [virtuális gép méretezési csoportában](#unhealthy-instances-in-backendaddresspool)lévő virtuális gépek és példányok egyike sem kifogástalan.
* [Idő-meghosszabbítási vagy kapcsolódási problémák kérése](#request-time-out) a felhasználói kérelmekkel kapcsolatban.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Hálózati biztonsági csoport, felhasználó által definiált útvonal vagy egyéni DNS-probléma

### <a name="cause"></a>Ok

Ha a háttérrendszerhez való hozzáférés nsg, UDR vagy egyéni DNS miatt le van tiltva, az alkalmazásátjáró-példányok nem érik el a háttérkészletet. Ez mintavételi hibákat okoz, ami 502 hibát eredményez.

Az NSG/UDR lehet az alkalmazásátjáró alhálózatában vagy abban az alhálózatban, ahol az alkalmazás virtuális gépei telepítve vannak.

Hasonlóképpen egy egyéni DNS jelenléte a virtuális hálózatban is problémákat okozhat. Előfordulhat, hogy a háttérkészlet-tagokhoz használt teljes tartományhálózat-azonosítót a virtuális hálózathoz konfigurált DNS-kiszolgáló nem oldja fel megfelelően.

### <a name="solution"></a>Megoldás

Az NSG-, UDR- és DNS-konfiguráció ellenőrzése a következő lépések végrehajtásával:

* Ellenőrizze az alkalmazásátjáró alhálózathoz társított NSG-ket. Győződjön meg arról, hogy a háttérrendszer kommunikációja nincs letiltva.
* Ellenőrizze az alkalmazásátjáró alhálózathoz társított UDR-t. Győződjön meg arról, hogy az UDR nem irányítja a forgalmat a háttér-alhálózattól. Például ellenőrizze, hogy a hálózati virtuális készülékekvagy az alapértelmezett útvonalak hirdetett az alkalmazás átjáró alhálózat expressroute/VPN keresztül.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Ellenőrizze a hatékony NSG-t és útvonalat a háttérvirtuális gépekkel

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Ellenőrizze az egyéni DNS jelenlétét a virtuális hálózatban. DNS ellenőrizhető a kimenetben lévő virtuális hálózat tulajdonságainak részleteinek vizsgálatával.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Ha van ilyen, győződjön meg arról, hogy a DNS-kiszolgáló képes helyesen feloldani a háttérkészlet-tag teljes tartománynatagját.

## <a name="problems-with-default-health-probe"></a>Problémák az alapértelmezett állapotmintával

### <a name="cause"></a>Ok

502 hibák is gyakori jelzők, amelyek az alapértelmezett állapotminta nem tudja elérni a háttérvirtuális gépek.

Ha egy alkalmazásátjáró-példány ki van építve, automatikusan konfigurálja az alapértelmezett állapotminta minden BackendAddressPool a BackendHttpSetting tulajdonságai használatával. A mintavétel beállításához nincs szükség felhasználói bevitelre. Pontosabban, ha egy terheléselosztási szabály van konfigurálva, egy kapcsolat jön létre a BackendHttpSetting és a BackendAddressPool között. Az alapértelmezett mintavétel minden egyes társításhoz konfigurálva van, és az alkalmazásátjáró rendszeres állapot-ellenőrző kapcsolatot indít a BackendAddressPool minden példányához a BackendHttpSetting elemben megadott porton. 

Az alábbi táblázat az alapértelmezett állapotmintához társított értékeket sorolja fel:

| Szonda tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Mintavétel URL-címe |`http://127.0.0.1/` |URL elérési útja |
| Intervallum |30 |Mintavételi időköz másodpercben |
| Időtúllépés |30 |A szonda időmeghosszabbítása másodpercben |
| Nem kifogástalan állapot küszöbértéke |3 |A mintavétel újrapróbálkozások száma. A háttérkiszolgáló le van jelölve, miután az egymást követő mintavételi hibák száma eléri a nem megfelelő küszöbértéket. |

### <a name="solution"></a>Megoldás

* Győződjön meg arról, hogy az alapértelmezett hely konfigurálva van, és a 127.0.0.1-es értéken figyel.
* Ha a Backend HttpSetting 80-tól eltérő portot ad meg, az alapértelmezett helyet úgy kell konfigurálni, hogy az adott porton figyelje.
* A hívásnak `http://127.0.0.1:port` 200-as HTTP-eredménykódot kell visszaadnia. Ezt a 30 másodperces időout-időszakon belül vissza kell küldeni.
* Győződjön meg arról, hogy a konfigurált port meg van nyitva, és hogy nincsenek tűzfalszabályok vagy az Azure hálózati biztonsági csoportok, amelyek blokkolják a bejövő vagy kimenő forgalmat a konfigurált porton.
* Ha az Azure klasszikus virtuális gépek vagy felhőszolgáltatás teljes tartománynnnel vagy nyilvános IP-címvel van használva, győződjön meg arról, hogy a megfelelő [végpont](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) meg van nyitva.
* Ha a virtuális gép az Azure Resource Manageren keresztül konfigurálva van, és azon a virtuális hálózaton kívül van, ahol az alkalmazásátjáró telepítve van, egy [hálózati biztonsági csoportot](../virtual-network/security-overview.md) kell konfigurálni, hogy engedélyezze a hozzáférést a kívánt porton.

## <a name="problems-with-custom-health-probe"></a>Problémák az egyéni állapotvizsgálóval

### <a name="cause"></a>Ok

Az egyéni állapotminta-vizsgálatok további rugalmasságot tesznek lehetővé az alapértelmezett mintavételi viselkedéshez. Egyéni mintavételek használatakor konfigurálhatja a mintavételi időközt, az URL-címet, a tesztelendő elérési utat és a háttérkészlet-példány nem megfelelőállapotúként való megjelölése előtt elfogadandó sikertelen válaszokat.

A következő további tulajdonságokkal egészül ki:

| Szonda tulajdonság | Leírás |
| --- | --- |
| Név |A szonda neve. Ez a név a háttérHTTP-beállításokban szereplő mintavételre hivatkozik. |
| Protocol (Protokoll) |A szonda küldéséhez használt protokoll. A mintavétel a háttérHTTP-beállításokban definiált protokollt használja |
| Gazdagép |A mintavétel elküldéséhez állomásnév. Csak akkor alkalmazható, ha többhely van konfigurálva az alkalmazásátjárón. Ez eltér a virtuális gép állomásnevétől. |
| Útvonal |A szonda relatív útvonala. Az érvényes elérési út a következőtől indul: '/'. A mintavétel a \<\>protokoll\<\>://\<\>\<host : port elérési útja\> |
| Intervallum |Mintavételi időköz másodpercben. Ez két egymást követő mintavétel közötti időintervallum. |
| Időtúllépés |A szonda időmeghosszabbítása másodpercben. Ha ez az időbeli elállási időszakon belül nem érkezik érvényes válasz, a mintavétel sikertelenként van megjelölve. |
| Nem kifogástalan állapot küszöbértéke |A mintavétel újrapróbálkozások száma. A háttérkiszolgáló le van jelölve, miután az egymást követő mintavételi hibák száma eléri a nem megfelelő küszöbértéket. |

### <a name="solution"></a>Megoldás

Ellenőrizze, hogy az egyéni állapotminta megfelelően van-e konfigurálva az előző táblaként. Az előző hibaelhárítási lépésekmellett a következőket is biztosítani kell:

* Ellenőrizze, hogy a szonda helyesen van-e megadva a [vezetővonal](application-gateway-create-probe-ps.md)szerint.
* Ha az alkalmazásátjáró egyetlen helyhez van konfigurálva, alapértelmezés szerint `127.0.0.1`az állomásnevet a , hacsak az egyéni mintavételben másként nem van konfigurálva.
* Győződjön meg arról,\<\>\<hogy\> http://\<állomáshívás:\>a port elérési útja 200-as HTTP-eredménykódot ad vissza.
* Győződjön meg arról, hogy az Interval, timeout és UnhealtyThreshold az elfogadható tartományon belül van.
* HTTPS-mintavétel használata esetén győződjön meg arról, hogy a háttérkiszolgáló nem igényel SNI-t egy tartalék tanúsítvány konfigurálásával a háttérkiszolgálón.

## <a name="request-time-out"></a>Időkérés időkérés

### <a name="cause"></a>Ok

Felhasználói kérelem érkezésekor az alkalmazásátjáró alkalmazza a konfigurált szabályokat a kérelemre, és egy háttérkészlet-példányhoz irányítja azt. Megvárja a konfigurálható időintervallumot a háttérpéldány válaszára. Alapértelmezés szerint ez az időköz **20** másodperc. Ha az alkalmazásátjáró nem kap választ a háttéralkalmazás ebben az időszakban, a felhasználói kérelem 502-es hibát kap.

### <a name="solution"></a>Megoldás

Az Application Gateway lehetővé teszi, hogy konfigurálja ezt a beállítást a BackendHttpSetting, amely ezután különböző készletekre alkalmazható. A különböző háttérkészletek különböző háttérrendszerhttpsetting, és egy másik kérelem időkorlát konfigurálva.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Üres háttérvégpont-címkészlet

### <a name="cause"></a>Ok

Ha az alkalmazásátjáró nem rendelkezik virtuális gépekkel vagy virtuálisgép-méretezési készletpel a háttércímkészletben konfigurálva, nem tud semmilyen ügyfélkérelmet továbbirányítani, és rossz átjáróhibát küld.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a háttércímkészlet nem üres. Ez a PowerShell, a CLI vagy a portál on keresztül végezhető el.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Az előző parancsmag kimenetének nem üres háttércímkészletet kell tartalmaznia. A következő példa két visszaadott készletek, amelyek konfigurálva vannak egy teljes tartománynév vagy ip-címek a háttérbeli virtuális gépek. A BackendAddressPool létesítési állapotának "Sikeres" állapotúnak kell lennie.

HáttérendAddressPoolsText:

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

## <a name="unhealthy-instances-in-backendaddresspool"></a>Nem megfelelő állapotú példányok a BackendAddressPool szolgáltatásban

### <a name="cause"></a>Ok

Ha a BackendAddressPool összes példánya nem kifogástalan, akkor az alkalmazásátjáró nem rendelkezik háttérrendszer-kezelővel a felhasználói kérelmek továbbításához. Ez akkor is előfordulhat, ha a háttérpéldányok kifogástalan állapotúak, de nem rendelkeznek a szükséges alkalmazás üzembe helyezésével.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a példányok kifogástalanállapotúak, és az alkalmazás megfelelően van konfigurálva. Ellenőrizze, hogy a háttérpéldányok válaszolhatnak-e egy másik virtuális gép pingelésére ugyanabban a virtuális hálózatban. Ha nyilvános végpontdal van konfigurálva, győződjön meg arról, hogy a webalkalmazás böngészőkérése javítható.

## <a name="next-steps"></a>További lépések

Ha az előző lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/)

