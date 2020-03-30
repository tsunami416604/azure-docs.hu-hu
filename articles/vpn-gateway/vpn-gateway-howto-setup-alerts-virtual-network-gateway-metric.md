---
title: Riasztások beállítása az Azure VPN-átjáró metrikáiban
description: A VPN-átjáró metrikáira vonatkozó riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605234"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Riasztások beállítása a VPN-átjáró mérőszámain

Ez a cikk segít riasztások beállításával az Azure VPN-átjáró metrikák. Az Azure Monitor lehetővé teszi az Azure-erőforrások riasztási beállítását. A "VPN" típusú virtuális hálózati átjárókhoz riasztásokat állíthat be.


|**Metrika**   | **Egység** | **Részletesség** | **Leírás** | 
|---       | ---        | ---       | ---            | ---       |
|**Átlagos sávszélesség**| Bájt/s  | 5 perc| Az átjáróösszes hely-hely kapcsolatának átlagos kombinált sávszélesség-kihasználtsága.     |
|**P2SBandwidth (P2SBandwidth)**| Bájt/s  | 1 perc  | Az átjáró összes pont-hely kapcsolatának átlagos kombinált sávszélesség-kihasználtsága.    |
|**P2SConnectioncount között**| Darabszám  | 1 perc  | Az átjáró pont-hely kapcsolatainak száma.   |
|**TunnelAverageBandwidth (CsatornaÁtlagos sávszélessége)** | Bájt/s    | 5 perc  | Az átjárón létrehozott alagutak átlagos sávszélesség-kihasználása. |
|**TunnelEgressBytes** | Bájt | 5 perc | Az átjárón létrehozott alagutak kimenő forgalma.   |
|**TunnelEgressPackets** | Darabszám | 5 perc | Az átjárón létrehozott alagutak kimenő csomagok száma.   |
|**TunnelEgressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalomválasztó eltérése által az alagutakban eldobott kimenő csomagok száma. |
|**TunnelIngressBytes** | Bájt | 5 perc | Bejövő forgalom az átjárón létrehozott alagutakban.   |
|**TunnelIngressPacketsCsomagok** | Darabszám | 5 perc | Az átjárón létrehozott alagutakbejövő csomagok száma.   |
|**TunnelIngressPacketDropTSMismatch** | Darabszám | 5 perc | A forgalomválasztó eltérése miatt az alagutakban eldobott bejövő csomagok száma. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Az Azure Monitor-riasztások beállítása metrikák alapján az Azure Portal használatával

A következő példalépések riasztást hoznak létre egy átjárón:

- **Mérőszám:** TunnelAverageBandwidth (CsatornaÁtlagos sávszélessége)
- **Feltétel:** Sávszélesség > 10 bájt/másodperc
- **Ablak:** 5 perc
- **Riasztási művelet:** E-mail



1. Nyissa meg a virtuális hálózati átjáró erőforrást, és válassza a **Figyelés** lapon a **Riasztások** lehetőséget. Ezután hozzon létre egy új riasztási szabályt, vagy szerkesztsen egy meglévő riasztási szabályt.

   ![Kijelölés a figyelmeztetési szabály létrehozásához](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Létrehozás")

2. Válassza ki a VPN-átjárót erőforrásként.

   ![A Kijelölés gomb és a VPN-átjáró az erőforrások listájában](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Válassza ezt:")

3. Válassza ki a riasztáshoz konfigurálandó metrikát.

   ![Kiválasztott mérőszám a mérőszámok listájában](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Válassza ezt:")
4. Állítsa be a jellogikát. Három összetevője van:

    a. **Dimenziók:** Ha a metrika dimenziókkal rendelkezik, kiválaszthat bizonyos dimenzióértékeket, hogy a riasztás csak az adott dimenzió adatait értékelje ki. Ezek nem kötelezőek.

    b. **Feltétel**: Ez a metrikaérték kiértékelésére alkalmas művelet.

    c. **Idő**: Adja meg a metrikaadatok részletességét és a riasztás kiértékelésének időtartamát.

   ![A jellogika konfigurálásának részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Válassza ezt:")

5. A beállított szabályok megtekintéséhez válassza a **Riasztási szabályok kezelése**lehetőséget.

   ![A riasztási szabályok kezeléséhez szolgáló gomb](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Válassza ezt:")

## <a name="next-steps"></a>További lépések

A bújtatási diagnosztikai naplókriasztási beállításokkonfigurálása a [VPN-átjáró diagnosztikai naplóihoz](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)való riasztások beállítása című témakörben található.
