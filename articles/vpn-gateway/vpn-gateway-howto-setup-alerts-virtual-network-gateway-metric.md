---
title: Riasztásokat állíthat be az Azure VPN Gateway-metrikák
description: A VPN-átjáró metrikák riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509904"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Riasztásokat állíthat be a VPN Gateway-metrikák

Ez a cikk segít az Azure VPN Gateway metrikákhoz riasztásokat állíthat be. Az Azure Monitor lehetővé teszi az Azure-erőforrások riasztásokat állíthat be. Beállíthat riasztásokat a "VPN" típusú virtuális hálózati átjárókhoz.


|**Metrika**   | **Egység** | **Részletessége** | **Leírás** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| bájt/s  | 5 perc| Átlagos összesített sávszélesség kihasználtsága az összes hely – hely kapcsolat az átjárón.     |
|**P2SBandwidth**| bájt/s  | 1 perc  | Átlagos összesített sávszélesség-használata minden pont – hely kapcsolat az átjárón.    |
|**P2SConnectionCount**| Count  | 1 perc  | Az átjáró a pont – hely kapcsolatok száma.   |
|**TunnelAverageBandwidth** | bájt/s    | 5 perc  | Átlagos alagutak létre az átjáró a sávszélesség használata. |
|**TunnelEgressBytes** | Bájt | 5 perc | Az átjáró a létrehozott alagúton kimenő forgalmat.   |
|**TunnelEgressPackets** | Count | 5 perc | Az átjáró a létrehozott alagúton kimenő csomagok száma.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 perc | Kimenő csomagok száma forgalomválasztó eltérés által okozott alagúton eldobva. |
|**TunnelIngressBytes** | Bájt | 5 perc | Az átjáró a létrehozott alagúton bejövő forgalmat.   |
|**TunnelIngressPackets** | Count | 5 perc | Az átjáró a létrehozott alagúton bejövő csomagok száma.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 perc | Forgalomválasztó eltérés által okozott alagúton eldobott bejövő csomagok száma. |


## <a name="setup"></a>Állítsa be az Azure Monitor riasztások metrikák alapján az Azure portal használatával

A következő példa lépései egy adott átjáróhoz tartozó riasztás hozza létre:

- **Metrika:** TunnelAverageBandwidth
- **Feltétel:** A sávszélesség > 10 bájt / s
- **Window:** 5 perc
- **Értesítési művelet:** E-mail-cím



1. Nyissa meg a virtuális hálózati átjáró erőforrás, és válassza ki **riasztások** származó a **figyelés** fülre. Ezután hozzon létre egy új riasztási szabályt, vagy egy meglévő riasztási szabály szerkesztése.

   ![Riasztási szabály létrehozására vonatkozó beállításokat](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "létrehozása")

2. Válassza ki a VPN-átjáró és az erőforrásnak.

   ![A Kijelölés gombra, és a VPN-átjárót az erőforrások listájához a](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "kiválasztása")

3. Válassza ki a riasztás konfigurálása egy metrikát.

   ![Kiválasztott metrika mérőszámok listája](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "kiválasztása")
4. A jellogika konfigurálása. Ez a három összetevőből áll:

    a. **Méretek**: Ha a metrika dimenziókat, kiválaszthatja megadott dimenzió értékeit, úgy, hogy a riasztást kiértékeli az adott dimenzió csak adatokat. Ezek a nem kötelező.

    b. **A feltétel**: Ez a metrika értéke kiértékelheti, hogy a műveletet.

    c. **Idő**: Adja meg a metrikaadatok granularitásán, és azt az időszakot, a riasztás kiértékeléséhez.

   ![Konfigurálása jel logikájának](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "kiválasztása")

5. A konfigurált szabályok megtekintéséhez jelölje ki **riasztási szabályok kezelése**.

   ![Riasztási szabályok kezelésére szolgáló gomb](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "kiválasztása")

## <a name="next-steps"></a>További lépések

Riasztások beállítása az alagutat a diagnosztikai naplók: [állítsa be a VPN-átjáró diagnosztikai naplók](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
