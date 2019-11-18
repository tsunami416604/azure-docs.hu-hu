---
title: 'Azure VPN Gateway: csomagok rögzítésének konfigurálása'
description: Ismerje meg a VPN-átjárók által használható csomag-rögzítési funkciókat.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 41c36d302605bb619899131a8ace649b0f1439b2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151858"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>A csomagok rögzítésének konfigurálása a VPN-átjárók számára

A kapcsolat és a teljesítménnyel kapcsolatos problémák gyakran sokszor bonyolultak, és jelentős mennyiségű időt és erőfeszítést igényelnek a probléma okának csökkentése érdekében. A csomagok rögzítésének képessége nagy mértékben segít csökkenteni a probléma hatókörét a hálózat bizonyos részeire, például azt, hogy a probléma a hálózat ügyfél oldalán, a hálózat Azure-oldalán vagy valahol a között van-e. A probléma szűkítése után sokkal hatékonyabb a hibakeresés és a javító művelet végrehajtása.

Vannak általánosan elérhető eszközök a csomagok rögzítéséhez. Ha azonban ezekkel az eszközökkel lekérdezi a kapcsolódó csomagokat, gyakran nehézkes, különösen nagy mennyiségű forgalmi forgatókönyvek használata esetén. A VPN Gateway-csomagok rögzítése által biztosított szűrési képességek jelentős differenciáló válnak. A VPN Gateway-csomagok rögzítését az általánosan elérhető csomagok rögzítése eszközein kívül is használhatja.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>A VPN Gateway csomagok rögzítésének szűrési képességei

A VPN Gateway-csomagok rögzítése az ügyfél igényeitől függően az átjárón vagy egy adott kapcsolaton is futtatható. A csomagok rögzítését egyszerre több alagúton is futtathatja. A VPN-átjárón való szűrés mellett egyetlen vagy kétirányú forgalmat, IKE-és ESP-forgalmat, valamint belső csomagokat is rögzíthet.

Az 5 rekordok-szűrő (forrás alhálózat, célként használt alhálózat, forrásport, célport, protokoll) és a TCP-jelzők (SYN, ACK, FIN, URG, PSH, első) használata hasznos lehet a nagy mennyiségű forgalom problémáinak elkülönítéséhez.

## <a name="setup-packet-capture-using-powershell"></a>Csomagok rögzítésének beállítása a PowerShell használatával

A csomagok rögzítésének elindításához és leállításához tekintse meg az alábbi példákat a PowerShell-parancsokhoz. A paraméterek beállításaival kapcsolatos további információkért (például szűrők létrehozásához) tekintse meg ezt a PowerShell- [dokumentumot](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének elindítása VPN-átjáró esetén

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Nem kötelező paraméter **– a FilterData** a szűrők alkalmazására használható.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének leállítása a VPN-átjárón

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>A csomagok rögzítésének elindítása a VPN Gateway-kapcsolathoz

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Nem kötelező paraméter **– a FilterData** a szűrők alkalmazására használható.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>A csomagok rögzítésének leállítása a VPN Gateway-kapcsolaton

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Fő szempontok

- A csomagok rögzítésének futtatása hatással lehet a teljesítményre. Ne felejtse el leállítani a csomagok rögzítését, ha nincs rá szükség.
- A csomagok minimális rögzítési időtartama 600 másodperc. Előfordulhat, hogy a csomagok rögzítésének rövidebb időtartama nem biztosít teljes körű adatmennyiséget, mert az elérési út több összetevője között szinkronizálja a problémákat.
- A csomag-rögzítési adatfájlok PCAP vagy ETL formátumban jönnek létre. Előfordulhat, hogy a netmon-elemzőnek szüksége van az adatelemzésre.

## <a name="next-steps"></a>További lépések

További információ a VPN Gatewayről: [about VPN Gateway](vpn-gateway-about-vpngateways.md)