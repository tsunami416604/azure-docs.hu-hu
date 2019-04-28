---
title: Az Azure VPN Gateway-metrikák riasztások beállítása
description: A VPN-átjáró metrikák riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769466"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>A VPN-átjáró metrikák riasztások beállítása

Ez a cikk segítséget nyújt a VPN-átjáró metrikákhoz riasztásokat állíthat be. Az Azure monitor lehetővé teszi az Azure-erőforrások riasztásokat állíthat be. Riasztások "VPN" típusú virtuális hálózati átjárókhoz állíthat be.


|**Metrika**   | **Egység** | **Részletessége** | **Leírás** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| bájt/s  | 5 perc| Átlagos összesített sávszélesség kihasználtsága az összes hely – hely kapcsolat az átjárón.     |
|**P2SBandwidth**| bájt/s  | 1 perc  | Átlagos összesített sávszélesség-használata minden pont – hely kapcsolat az átjárón.    |
|**P2SConnectionCount**| Darabszám  | 1 perc  | Az átjáró száma a P2S-kapcsolatokkal.   |
|**TunnelAverageBandwidth** | bájt/s    | 5 perc  | Átlagos alagutak létre az átjáró a sávszélesség használata. |
|**TunnelEgressBytes** | Bájt | 5 perc | Az átjáró a létrehozott alagúton kimenő forgalmat.   |
|**TunnelEgressPackets** | Darabszám | 5 perc | Az átjáró a létrehozott alagúton kimenő csomagok száma.   |
|**TunnelEgressPacketDropTSMismatch** | Darabszám | 5 perc | Kimenő csomagok száma okozza a TS eltérés alagúton eldobva. |
|**TunnelIngressBytes** | Bájt | 5 perc | Az átjáró a létrehozott alagúton bejövő forgalmat.   |
|**TunnelIngressPackets** | Darabszám | 5 perc | Az átjáró a létrehozott alagúton bejövő csomagok száma.   |
|**TunnelIngressPacketDropTSMismatch** | Darabszám | 5 perc | TS eltérés által okozott alagúton eldobott bejövő csomagok száma. |


## <a name="setup"></a>A portál használatával metrikák alapján az Azure Monitor riasztások beállítása

Az alábbi lépéseket a példa egy adott átjáróhoz tartozó riasztás hozza létre: <br>

**Metrika:** Átlagos sávszélesség alagút <br>
**Feltétel:** sávszélesség > 10 bájt / s <br>
**Window:** 5 perc <br>
**Értesítési művelet:** E-mail <br>



1. Keresse meg a virtuális hálózati átjáró erőforrás, és válassza az "Értesítések" a figyelés lapon, majd hozzon létre egy új riasztási szabály, vagy egy meglévő riasztási szabály szerkesztése.

![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "létrehozása")

2. Válassza ki a VPN-átjáró és az erőforrásnak.

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. Válassza ki egy metrikát a riasztás konfigurálásához ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "kiválasztása")
4. A jellogika konfigurálása. A jel logikájának három összetevőből áll:

    a. Méretek: Ha a metrika dimenziókat, adott dimenzió értékeit, hogy a riasztás csak kiértékeli az adott dimenzió adatokat lehet kiválasztani. Ezek a nem kötelező.<br>
    b. Feltétel: A művelet a metrikaérték kiértékeléséhez.<br>
    c. Idő: Adja meg a metrikaadatok granularitásán, és azt az időszakot, értékelheti ki a riasztást a.<br>

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. A konfigurált szabályok megtekintéséhez kattintson a "Riasztási szabályok kezelése" ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "kiválasztása")

## <a name="next-steps"></a>További lépések

Riasztások beállítása a diagnosztikai naplók alagút: [a VPN-átjáró diagnosztikai naplók riasztások beállításának módjai](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
