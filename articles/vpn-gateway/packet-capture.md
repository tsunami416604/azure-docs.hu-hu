---
title: 'Azure VPN-átjáró: Csomagrögzítéskonfigurálása'
description: Információ a VPN-átjárókon használható csomagrögzítési funkciókról.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353517"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Csomagrögzítések konfigurálása VPN-átjárókhoz

A kapcsolódási és a teljesítménnyel kapcsolatos problémák gyakran bonyolultak, és jelentős időt és erőfeszítést igényelnek a probléma okának leszűkítéséhez. A csomagrögzítés képessége nagyban csökkenti a probléma hatókörének a hálózat bizonyos részeire történő leszűkítéséhez szükséges időt, például azt, hogy a probléma a hálózat ügyféloldalán, a hálózat Azure-oldalán vagy valahol a kettő között van-e. Miután a probléma már szűkült le, ez sokkal hatékonyabb a hibakeresés, és megteszi a korrekciós intézkedéseket.

A csomagrögzítéshez van néhány általánosan elérhető eszköz. Azonban a releváns csomagrögzítések beszerzése ezekkel az eszközökkel gyakran nehézkes, különösen akkor, ha nagy mennyiségű forgalmi forgatókönyvekkel dolgozik. A VPN-átjárócsomag-rögzítés által biztosított szűrési képességek jelentős különbséget jelentenek. A gyakran elérhető csomagrögzítő eszközök mellett vpn-átjárócsomag-rögzítést is használhat.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN-átjáró csomagrögzítési szűrési képességei

A VPN-átjárócsomag-rögzítések az ügyfél igényeitől függően futtathatók az átjárón vagy egy adott kapcsolaton. A csomagrögzítéseket egyszerre több alagúton is futtathatja. Egy- vagy kétirányú forgalmat, ike- és ESP-forgalmat, valamint belső csomagokat, valamint a VPN-átjárószűrést rögzíthet.

Az 5 tuples szűrő (forrásalhálózat, célalhálózat, forrásport, célport, protokoll) és TCP-jelzők (SYN, ACK, FIN, URG, PSH, RST) használata akkor hasznos, ha nagy mennyiségű forgalom esetén problémákat old meg.

A csomagrögzítés futtatásakor tulajdonságonként csak egy beállítás használható.

## <a name="setup-packet-capture-using-powershell"></a>Csomagrögzítés beállítása a PowerShell használatával

Tekintse meg az alábbi példákat a PowerShell-parancsok at a csomagrögzítés indításához és leállításához. A paraméterbeállításokkal (például a szűrő létrehozásának módjával) kapcsolatos további információkért tekintse meg ezt a [PowerShell-dokumentumot.](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Csomagrögzítés indítása VPN-átjáróhoz

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Választható paraméter **-FilterData** szűrő alkalmazására használható.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN-átjáró csomagrögzítésének leállítása

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Csomagrögzítés indítása VPN-átjárókapcsolathoz

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Választható paraméter **-FilterData** szűrő alkalmazására használható.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Csomagrögzítés leállítása VPN-átjárókapcsolaton

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Fő szempontok

- A csomagrögzítések futtatása befolyásolhatja a teljesítményt. Ne felejtse el leállítani a csomagrögzítést, ha nincs rá szükség.
- A csomagrögzítés javasolt minimális időtartama 600 másodperc. Előfordulhat, hogy a rövidebb csomagrögzítés idotartama nem biztosít teljes adatokat, mivel az elérési út több összetevője között szinkronizálási problémák vannak.
- A csomagrögzítési adatfájlok PCAP formátumban jönnek létre. Használja wireshark vagy más általánosan elérhető alkalmazások megnyitásához PCAP fájlokat.

## <a name="next-steps"></a>További lépések

A VPN-átjáróról a [VPN-átjáróról című témakörben](vpn-gateway-about-vpngateways.md) talál további információt
