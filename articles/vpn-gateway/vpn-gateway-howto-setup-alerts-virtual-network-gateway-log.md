---
title: Az Azure VPN Gateway diagnosztikai napló esemény-riasztások beállítása
description: A VPN-átjáró diagnosztikai napló események riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769736"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>A VPN-átjáró diagnosztikai napló események riasztások beállítása

Ez a cikk segítséget nyújt a VPN-átjáró diagnosztikai napló események alapján riasztásokat állíthat be.


## <a name="setup"></a>A portál használatával diagnosztikai napló események alapján az Azure Monitor riasztások beállítása

A példa az alábbi lépéseket site-to-site VPN alagút leválasztási esemény riasztást hoz létre.



1. Keressen a "Log Analytics" csoportban az összes szolgáltatás, és válassza ki a "Log Analytics-munkaterületek" ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "létrehozása")

2. A Log Analytics lapon kattintson a "Létrehozás".
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. Ellenőrizze az "Új létrehozása" munkaterület, és adja meg az adatokat.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. Keresse meg a VPN-átjáró mellett a "figyelés" > "Diagnosztikai beállítások" panelen ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "kiválasztása")

5. Diagnosztika bekapcsolásához kattintson duplán az átjárón, és kattintson a "Diagnosztika bekapcsolása" ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "kiválasztása")

6. Adja meg az adatokat, és győződjön meg arról, hogy "Küldés a Log Analytics" és "TunnelDiagnosticLog" ellenőrzi. Válassza ki a 3. lépésben létrehozott Log Analytics-munkaterületet.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

7. Keresse meg a virtuális hálózati átjáró erőforrás-áttekintés, és válassza az "Értesítések" a figyelés lapon, majd hozzon létre egy új riasztási szabály, vagy egy meglévő riasztási szabály szerkesztése.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

8. Válassza ki a Log Analytics-munkaterületet és az erőforrás.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. Válassza "egyéni naplók keresése", a jel logikájának "Feltétel hozzáadása" alatt ![pont – hely](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "kiválasztása")

10. Adja meg a következő lekérdezést a "Keresési lekérdezés" szövegmezőbe, és cserélje le az értékeket szükség szerint <>.

    AzureDiagnostics |} ahol kategória == "TunnelDiagnosticLog" és az erőforrás-azonosító == toupper ("<RESOURCEID OF GATEWAY>") és TimeGenerated > ago(5m) és remoteIP_s == "<REMOTE IP OF TUNNEL>" és a status_s == "Leválasztott"

    A küszöbérték beállítása 0, és kattintson a "Kész" gombra

    ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. A "Létrehozás szabály" lapon kattintson az "Új létrehozása" a MŰVELETCSOPORTOK szakaszában. Adja meg az adatokat, és kattintson az OK gombra

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. A "Létrehozás szabály" lapon adja meg az adatokat az "Action testreszabása", és győződjön meg arról, hogy megjelenik-e a megfelelő műveleti csoport neve az "Műveletcsoport neve" szakaszban. "A riasztási szabály létrehozása" gombra a szabály létrehozásához.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="next-steps"></a>További lépések

Riasztások beállítása a alagút metrikák: [a VPN-átjáró metrikák riasztások beállításának módjai](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
