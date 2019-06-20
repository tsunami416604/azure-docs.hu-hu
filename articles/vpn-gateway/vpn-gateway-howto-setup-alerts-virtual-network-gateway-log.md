---
title: Riasztásokat állíthat be a diagnosztikai naplót események Azure VPN Gateway átjárón
description: A VPN-átjáró diagnosztikai napló események riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 6c85f47a2e3691306d59c5c44856fd08c07f2d36
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202894"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Riasztásokat állíthat be az esemény-diagnosztikai naplót a VPN-átjáró

Ez a cikk segít az Azure Log Analytics használatával az Azure VPN Gateway diagnosztikai napló események alapján riasztásokat állíthat be. 

A következő naplók kapcsolódnak az Azure-ban érhetők el:

|***Name (Név)*** | ***Leírás*** |
|---        | ---               |
|GatewayDiagnosticLog | Tartalmazza az átjáró-konfigurációs események, a módosítások elsődleges és a karbantartási események diagnosztikai naplók |
|TunnelDiagnosticLog | Állapotváltozási események alagút tartalmazza. Alagút csatlakoztatása vagy leválasztása események rendelkezik az állapotváltozás körülményeiről összesített okát, ha van ilyen |
|RouteDiagnosticLog | Statikus útvonal és a BGP-eseményeket, amelyek az átjáró módosításainak naplók |
|IKEDiagnosticLog | Naplózza az IKE-vezérlő üzenetek és események az átjárón |
|P2SDiagnosticLog | Naplózza a pont – hely vezérlő üzenetek és események az átjárón |

## <a name="setup"></a>Riasztások beállítása

A következő példa lépései létrehoz egy riasztást, amely magában foglalja a site-to-site VPN-alagút leválasztási esemény:


1. Az Azure Portalon keresse meg **Log Analytics** alatt **minden szolgáltatás** válassza **Log Analytics-munkaterületek**.

   ![Lépjen a Log Analytics-munkaterületek szolgáló kiválasztások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "létrehozása")

2. Válassza ki **létrehozás** a a **Log Analytics** lapot.

   ![Log Analytics lapot a Létrehozás gombbal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "kiválasztása")

3. Válassza ki **hozzon létre új** , és adja meg az adatokat.

   ![Log Analytics-munkaterület létrehozásához részletek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "kiválasztása")

4. Keresse meg a VPN-átjáró a a **figyelő** > **diagnosztikai beállítások** panelen.

   ![A VPN-átjáró megkeresni a diagnosztikai beállítások a választott beállításokat](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "kiválasztása")

5. Engedélyezze a diagnosztikát, kattintson duplán az átjárót, majd **diagnosztika bekapcsolása**.

   ![Diagnosztika bekapcsolása szolgáló kiválasztások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "kiválasztása")

6. Adja meg az adatokat, és ellenőrizze, hogy **Küldés a Log Analyticsnek** és **TunnelDiagnosticLog** ki van jelölve. Válassza ki a 3. lépésben létrehozott Log Analytics-munkaterületet.

   ![A kijelölt jelölőnégyzetek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "kiválasztása")

7. Nyissa meg a virtuális hálózati átjáró erőforrás-áttekintés, és válassza ki **riasztások** származó a **figyelés** fülre. Ezután hozzon létre egy új riasztási szabályt, vagy egy meglévő riasztási szabály szerkesztése.

   ![Egy új riasztási szabály létrehozására vonatkozó beállításokat](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "kiválasztása")

   ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Válassza ki a Log Analytics-munkaterületet és az erőforrás.

   ![Kiválasztott munkaterület és erőforrás](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "kiválasztása")

9. Válassza ki **egyéni naplóbeli keresés** , a jel logikájának alatt **feltétel hozzáadása**.

   ![Egyéni Naplókeresés szolgáló kiválasztások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "kiválasztása")

10. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben. Cserélje le az értékeket szükség szerint <>.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    A küszöbérték állítsa 0-ra, és válassza ki **kész**.

    ![Írja be a lekérdezést, majd válassza a küszöbértéket](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "kiválasztása")

11. A a **létrehozás szabály** lapon jelölje be **hozzon létre új** alatt a **MŰVELETCSOPORTOK** szakaszban. Adja meg a részleteket, és válasszon **OK**.

    ![Új műveletcsoport részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "kiválasztása")

12. A a **szabály létrehozása** lap, adja meg az adatokat a **műveletek testreszabása** , és győződjön meg arról, hogy a megfelelő neve megjelenik a **MŰVELETCSOPORT neve** szakaszban. Válassza ki **riasztási szabály létrehozása** a szabály létrehozásához.

    ![Egy szabály létrehozására vonatkozó beállításokat](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "kiválasztása")

## <a name="next-steps"></a>További lépések

Riasztások beállítása a alagút metrikák: [riasztásokat állíthat be a VPN Gateway-metrikák](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
