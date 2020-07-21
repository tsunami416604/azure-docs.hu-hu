---
title: Azure VPN Gateway mérőszámok megtekintése
description: VPN Gateway mérőszámok megtekintésének lépései
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531620"
---
# <a name="view-vpn-gateway-metrics"></a>VPN Gateway mérőszámok megtekintése

Azure Monitor használatával figyelheti az Azure VPN-átjárókat. Ez a cikk a portálon keresztül elérhető metrikákat ismerteti. A metrikák egyszerűek, és a közel valós idejű forgatókönyveket támogatják, így a riasztások és a gyors problémák észlelése is hasznos lehet.


|**Metrika**   | **Egység** | **Részletesség** | **Leírás** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bájt/s  | 5 perc| Az átjárón a helyek közötti kapcsolatok átlagos összesített sávszélesség-kihasználtsága.     |
|**P2SBandwidth**| Bájt/s  | 1 perc  | Az átjárón az összes pont – hely kapcsolat átlagos összesített sávszélesség-kihasználtsága.    |
|**P2SConnectionCount**| Darabszám  | 1 perc  | Pont – hely kapcsolatok száma az átjárón.   |
|**TunnelAverageBandwidth** | Bájt/s    | 5 perc  | Az átjárón létrehozott alagutak átlagos sávszélesség-kihasználtsága. |
|**TunnelEgressBytes** | Bájt | 5 perc | Az átjárón létrehozott alagutak kimenő forgalma.   |
|**TunnelEgressPackets** | Darabszám | 5 perc | A kimenő csomagok száma az átjárón létrehozott alagutakon.   |
|**TunnelEgressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalom-választói eltérés által okozott alagutakon eldobott kimenő csomagok száma. |
|**TunnelIngressBytes** | Bájt | 5 perc | Az átjárón létrehozott alagutak bejövő forgalma.   |
|**TunnelIngressPackets** | Darabszám | 5 perc | Az átjárón létrehozott alagutakon bejövő csomagok száma.   |
|**TunnelIngressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalmat kiválasztó eltérések okozta alagutakon eldobott bejövő csomagok száma. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>A következő lépések segítségével megkeresheti és megtekintheti a metrikákat:

1. Navigáljon a virtuális hálózati átjáró erőforráshoz a portálon
2. Az **Áttekintés** lehetőség kiválasztásával megtekintheti a bejövő és a kimenő adatforgalom összes csatornáját.

   ![Riasztási szabály létrehozásához szükséges beállítások](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Nézet")

3. A fent felsorolt egyéb mérőszámok megtekintéséhez. Kattintson a **metrikák** szakaszra a virtuális hálózati átjáró erőforrás alatt, és válassza ki a metrikát a legördülő listából.

   ![A Select (kiválasztás) gomb és a VPN-átjáró az erőforrások listájában](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Válassza ezt:")

## <a name="next-steps"></a>Következő lépések

A riasztások bújtatási metrikákkal való konfigurálásáról lásd: [riasztások beállítása VPN Gateway mérőszámokon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

A riasztások bújtatási erőforrás-naplókban való konfigurálásáról további információt [a riasztások beállítása VPN Gateway erőforrás-naplókon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)című témakörben talál.
