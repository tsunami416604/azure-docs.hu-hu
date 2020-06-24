---
title: 'Azure VPN Gateway: csomagok rögzítésének konfigurálása'
description: Ismerje meg a VPN-átjárók által használható csomag-rögzítési funkciókat.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 6edfe0228ce4cbe21ad4ae0eb8b7316a92f1da31
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987156"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>A csomagok rögzítésének konfigurálása a VPN-átjárók számára

A kapcsolat és a teljesítménnyel kapcsolatos problémák gyakran sokszor bonyolultak, és jelentős mennyiségű időt és erőfeszítést igényelnek a probléma okának csökkentése érdekében. A csomagok rögzítésének képessége nagy mértékben segít csökkenteni a probléma hatókörét a hálózat bizonyos részeire, például azt, hogy a probléma a hálózat ügyfél oldalán, a hálózat Azure-oldalán vagy valahol a között van-e. A probléma szűkítése után sokkal hatékonyabb a hibakeresés és a javító művelet végrehajtása.

Vannak általánosan elérhető eszközök a csomagok rögzítéséhez. Ha azonban ezekkel az eszközökkel lekérdezi a kapcsolódó csomagokat, gyakran nehézkes, különösen nagy mennyiségű forgalmi forgatókönyvek használata esetén. A VPN Gateway-csomagok rögzítése által biztosított szűrési képességek jelentős differenciáló válnak. A VPN Gateway-csomagok rögzítését az általánosan elérhető csomagok rögzítése eszközein kívül is használhatja.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>A VPN Gateway csomagok rögzítésének szűrési képességei

A VPN Gateway-csomagok rögzítése az ügyfél igényeitől függően az átjárón vagy egy adott kapcsolaton is futtatható. A csomagok rögzítését egyszerre több alagúton is futtathatja. A VPN-átjárón való szűrés mellett egyetlen vagy kétirányú forgalmat, IKE-és ESP-forgalmat, valamint belső csomagokat is rögzíthet.

Az 5 rekordok szűrő (forrásoldali alhálózat, célként használt alhálózat, forrásport, célport, protokoll) és a TCP-jelzők (SYN, ACK, FIN, URG, PSH, első) használata hasznos lehet a nagy mennyiségű forgalom problémáinak elkülönítéséhez.

A csomagok rögzítésének futtatásakor csak egy lehetőséget használhat tulajdonságként.

## <a name="setup-packet-capture-using-powershell"></a>Csomagok rögzítésének beállítása a PowerShell használatával

A csomagok rögzítésének elindításához és leállításához tekintse meg az alábbi példákat a PowerShell-parancsokhoz. A paraméter beállításaival kapcsolatos további információkért (például a szűrő létrehozásához) tekintse meg ezt a PowerShell- [dokumentumot](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének elindítása VPN-átjáró esetén

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Opcionális paraméter **– a FilterData** használható a szűrő alkalmazásához.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>A csomagok rögzítésének leállítása a VPN-átjárón

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>A csomagok rögzítésének elindítása a VPN Gateway-kapcsolathoz

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Opcionális paraméter **– a FilterData** használható a szűrő alkalmazásához.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>A csomagok rögzítésének leállítása a VPN Gateway-kapcsolaton

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Fő szempontok

- A csomagok rögzítésének futtatása hatással lehet a teljesítményre. Ne felejtse el leállítani a csomagok rögzítését, ha nincs rá szükség.
- A csomagok minimális rögzítési időtartama 600 másodperc. Előfordulhat, hogy a csomagok rögzítésének rövidebb időtartama nem biztosít teljes körű adatmennyiséget, mert az elérési út több összetevője között szinkronizálja a problémákat.
- A csomag-rögzítési adatfájlok PCAP formátumban jönnek létre. A PCAP-fájlok megnyitásához használjon Wireshark vagy más általánosan elérhető alkalmazásokat.

## <a name="next-steps"></a>További lépések

További információ a VPN Gatewayről: [about VPN Gateway](vpn-gateway-about-vpngateways.md)
