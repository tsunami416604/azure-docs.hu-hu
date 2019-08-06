---
title: Riasztások beállítása az Azure-beli diagnosztikai naplók eseményeiről VPN Gateway
description: A riasztások VPN Gateway diagnosztikai napló eseményein való konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: c84d457c51f71bdf315bbbcec674ff1186dd905f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249016"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Riasztások beállítása a diagnosztikai naplózási eseményekről VPN Gateway

Ebből a cikkből megtudhatja, hogyan állíthatja be a riasztásokat az Azure VPN Gateway diagnosztikai naplózási eseményei alapján az Azure Log Analytics használatával. 

Az Azure-ban az alábbi naplók érhetők el:

|***Name*** | ***Leírás*** |
|---        | ---               |
|GatewayDiagnosticLog | Diagnosztikai naplókat tartalmaz az átjáró konfigurációs eseményeihez, az elsődleges módosításokhoz és a karbantartási eseményekhez. |
|TunnelDiagnosticLog | Bújtatási állapot változási eseményeit tartalmazza. Az alagút kapcsolódási/leválasztási eseményeinek összefoglaló oka van az állapot változásához, ha van ilyen. |
|RouteDiagnosticLog | A statikus útvonalak és az átjárón előforduló BGP-események változásainak naplózása |
|IKEDiagnosticLog | Az átjárón lévő IKE-vezérlési üzenetek és események naplózása |
|P2SDiagnosticLog | A pont – hely típusú vezérlő üzeneteinek és eseményeinek naplózása az átjárón |

## <a name="setup"></a>Riasztások beállítása

A következő példa egy riasztást hoz létre egy leválasztási eseményhez, amely a helyek közötti VPN-alagutat foglalja magában:


1. A Azure Portal a **minden szolgáltatás** területen keresse meg a **log Analytics** , és válassza a **log Analytics**munkaterületek lehetőséget.

   ![Log Analytics munkaterületek kijelölésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Létrehozás")

2. Válassza a **Létrehozás** lehetőséget a **log Analytics** oldalon.

   ![Log Analytics lap létrehozás gombbal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Válassza ki")

3. Válassza az **új létrehozása** lehetőséget, és adja meg a részleteket.

   ![Log Analytics munkaterület létrehozásával kapcsolatos részletek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Válassza ki")

4. Keresse meg a VPN-átjárót a **figyelő** > **diagnosztika beállításai** panelen.

   ![A VPN Gateway diagnosztikai beállításokban való keresésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Válassza ki")

5. A diagnosztika bekapcsolásához kattintson duplán az átjáróra, majd válassza a **diagnosztika**bekapcsolása elemet.

   A ![diagnosztika bekapcsolásának kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Válassza ki")

6. Adja meg a részleteket, és győződjön meg arról, hogy a **küldés log Analytics** és **TunnelDiagnosticLog** be van jelölve. Válassza ki a 3. lépésben létrehozott Log Analytics munkaterületet.

   ![Bejelölt jelölőnégyzetek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Válassza ki")

7. Nyissa meg a virtuális hálózati átjáró erőforrásának áttekintését, és a **figyelés** lapon válassza a **riasztások** lehetőséget. Ezután hozzon létre egy új riasztási szabályt, vagy szerkesszen egy meglévő riasztási szabályt.

   ![Új riasztási szabály létrehozásának kijelölése](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ki")

   ![pont – hely] kapcsolat (./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ki")
8. Válassza ki a Log Analytics munkaterületet és az erőforrást.

   ![Munkaterület és erőforrás kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Válassza ki")

9. Válassza az **egyéni naplók keresése** lehetőséget a **feltétel hozzáadása**alatt lévő jel logikaként.

   ![Egyéni naplók keresésének kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Válassza ki")

10. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben. Szükség szerint cserélje le a < > értékeit.

    ```
    AzureDiagnostics |
      where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
      remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"
    ```

    Állítsa 0 értékre a küszöbértéket, és válassza a **kész**lehetőséget.

    ![Lekérdezés megadása és küszöbérték kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Válassza ki")

11. A **szabály létrehozása** lapon válassza az **új létrehozása** lehetőséget a **műveleti csoportok** szakaszban. Adja meg a részleteket, és kattintson **az OK gombra**.

    ![Új műveleti csoport részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Válassza ki")

12. A **szabály létrehozása** lapon adja meg a **műveletek testreszabásának** részleteit, és győződjön meg arról, hogy a megfelelő név jelenik meg a **műveleti csoport neve** szakaszban. A szabály létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    ![Szabály létrehozására szolgáló kiválasztások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Válassza ki")

## <a name="next-steps"></a>További lépések

A riasztások bújtatási metrikákkal való konfigurálásáról lásd: [riasztások beállítása VPN Gateway mérőszámokon](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
