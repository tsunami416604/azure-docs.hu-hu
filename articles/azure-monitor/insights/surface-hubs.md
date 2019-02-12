---
title: Surface hubok monitorozása az Azure az Azure Monitor szolgáltatással |} A Microsoft Docs
description: A Surface Hub-megoldás segítségével nyomon követheti a Surface Hubokban állapotát, és megismerheti, hogyan van használva.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 902cf62e53581785caf2730f63af3633d8e1e498
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005437"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Surface hubok monitorozása az Azure Monitor nyomon követéséhez az állapotfigyelő szolgáltatással

![Surface Hub szimbólum](./media/surface-hubs/surface-hub-symbol.png)

Ez a cikk bemutatja, hogyan használhatja a Surface Hub-megoldás az Azure monitorban figyelése a Microsoft Surface Hub-eszközöket. A megoldás segítségével nyomon követheti a Surface Hubokban állapotát, valamint megismerheti, hogyan van használva.

Minden egyes Surface Hub a Microsoft Monitoring Agent telepítve van. Annak, hogy küldhet adatokat a Surface hubon a Log Analytics-munkaterületet az Azure Monitor-ügynökkel. Naplófájlok a Surface hubok és a olvasni, majd az Azure Monitor érkeznek. Problémák, például kiszolgálók nem kapcsolódott, a naptár nem szinkronizálja, vagy ha a fiók nem tud bejelentkezni Skype a Azure Monitor a Surface Hub irányítópultján jelenik meg. Az irányítópulton lévő adatok felhasználásával azonosíthatja eszközök, amelyek nem futnak, vagy, amely más problémák merülnek fel, és esetleg a alkalmazni az észlelt hibák javításait.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat. Az Azure monitorban a Surface Hubokban kezeléséhez, a következő lesz szüksége:

* A [Log Analytics-előfizetést](https://azure.microsoft.com/pricing/details/log-analytics/) szintet, amely támogatja a figyelni kívánt eszközök számát. Log Analytics díjszabása függ attól függően, hogy hány eszköz regisztrált, és mennyi adatot, folyamatokat. Érdemes figyelembe venni ezt, a Surface Hub bevezetésének megtervezésekor.

Ezután fogja hozzáadni egy meglévő Log Analytics-munkaterületet vagy hozzon létre egy újat. Jelenleg mindkét módszer használatával történő szinkronizálásának részletes útmutatásáért [Log Analytics-munkaterület létrehozása az Azure Portalon](../learn/quick-create-workspace.md). Miután konfigurálta a Log Analytics-munkaterületet, két módja a Surface Hub-eszközök regisztrálásához:

* Automatikusan Intune-nal
* Manuálisan keresztül **beállítások** a Surface Hub eszköz.

## <a name="set-up-monitoring"></a>Állítsa be a figyelést,
Az állapotát és tevékenységét a Surface hubon, az Azure Monitor használatával figyelheti. A Surface Hub is regisztrálásához, Intune-nal vagy a helyi használatával **beállítások** a Surface hubon.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>A Surface hubokon csatlakozhat az Azure Monitor az Intune-ban
A Log Analytics-munkaterületet, amely kezelni fogja a Surface Hubokban szüksége a munkaterület-Azonosítót és a munkaterületkulcsot. A munkaterület beállításait szerintiek kaphat az Azure Portalon.

Intune-nal, a Microsoft-termék, amely lehetővé teszi, hogy központilag kezelheti a Log Analytics munkaterület konfigurációs beállításait, amelyek egy vagy több eszközön érvénybe lépnek. Kövesse az alábbi lépéseket az eszközök Intune-nal végzett konfigurálásához:

1. Jelentkezzen be az Intune-hoz.
2. Navigáljon a **beállítások** > **csatlakoztatott források**.
3. Hozzon létre, vagy a szabályzatot a Surface Hub-sablon alapján.
4. Keresse meg a szabályzat az Azure Operational Insights szakaszát, és adja hozzá a Log Analytics *munkaterület-Azonosítót* és *Munkaterületkulcsot* a szabályzathoz.
5. A házirend mentéséhez.
6. A házirendet társítani az eszközök a megfelelő csoporthoz.

   ![Intune-szabályzat](./media/surface-hubs/intune.png)

Intune az eszközök a célcsoportban, regisztrálja őket a Log Analytics-munkaterület majd szinkronizál a Log Analytics-beállításait.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>Csatlakozás az Azure Monitor használatával a gépház alkalmazás Surface hubok
A Log Analytics-munkaterületet, amely kezelni fogja a Surface Hubokban szüksége a munkaterület-Azonosítót és a munkaterületkulcsot. Az Azure Portalon a Log Analytics-munkaterület beállításait szerintiek kérheti le.

Ha a környezet felügyeletéhez az Intune nem használja, manuálisan keresztül eszközöket regisztrálni **beállítások** egyes Surface hub:

1. Nyissa meg a Surface Hub **beállítások**.
2. Adja meg az eszköz rendszergazdai hitelesítő adatokat.
3. Kattintson a **az eszköz**, és a alatt **figyelés**, kattintson a **Log Analytics beállításainak konfigurálása**.
4. Válassza ki **engedélyezze a monitorozást**.
5. A Log Analytics-beállítások párbeszédpanelen írja be a Log Analytics **munkaterület-Azonosítót** , és írja be a **Munkaterületkulcsot**.  
   ![Beállítások](./media/surface-hubs/settings.png)
6. Kattintson a **OK** konfigurálásának befejezéséhez.

Egy megerősítő felszólító-e a konfiguráció sikeresen alkalmazta az eszközre jelenik meg. Amennyiben igen, megjelenik egy üzenet, amely megállapítja, hogy az ügynök sikeresen csatlakozik az Azure Monitor. Az eszköz akkor elindítja az Azure Monitor, melyen megtekintheti, és reagálhat rájuk, hogy adatokat küld a.

## <a name="monitor-surface-hubs"></a>A figyelő a Surface hubokon
A Surface Hubokban figyelési az Azure Monitor használatával lényegében ugyanúgy figyelése a regisztrált eszközöket.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

A Surface Hub csempére kattint, az eszköz állapota jelenik meg.

   ![Surface Hub-irányítópulton](./media/surface-hubs/surface-hub-dashboard.png)

Létrehozhat [riasztások](../platform/alerts-overview.md) meglévő vagy egyéni naplókeresések alapján. Az adatok a Surface Hubokban gyűjti össze az Azure Monitor használatával, kereshet problémákat és az eszközök meghatározó feltételeket meg a riasztás.

## <a name="next-steps"></a>További lépések
* Használat [lekérdezések jelentkezzen be az Azure Monitor](../log-query/log-query-overview.md) Surface Hub részletes adatainak megtekintéséhez.
* Hozzon létre [riasztások](../platform/alerts-overview.md) arra az esetre, ha a probléma lép fel a Surface Hubokban.
