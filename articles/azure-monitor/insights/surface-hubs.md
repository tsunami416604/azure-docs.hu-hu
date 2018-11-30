---
title: Az Azure Log Analytics Surface hubok monitorozása |} A Microsoft Docs
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
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 761140b1022f5a1c1cd523c6e0e52193ff2a9700
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428563"
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>A Log Analyticsben, hogy azok állapotáról, a Surface hubok monitorozása

![Surface Hub szimbólum](./media/surface-hubs/surface-hub-symbol.png)

Ez a cikk bemutatja, hogyan használhatja a Surface Hub-megoldás a Log Analytics figyelése a Microsoft Surface Hub-eszközöket. A log Analytics segítségével nyomon követheti a Surface Hubokban állapotát, valamint megismerheti, hogyan van használva.

Minden egyes Surface Hub a Microsoft Monitoring Agent telepítve van. Annak, hogy küldhet adatokat a Surface hubon a Log Analytics-ügynökkel. Naplófájlok a Surface hubok és a olvasni, majd a rendszer a Log Analytics felé küldött. Problémák, például kiszolgálók nem kapcsolódott, a naptár, a szinkronizálás nem, vagy ha a fiók nem tud bejelentkezni a Skype jelennek-e a Log Analytics a Surface Hub irányítópultján. Az irányítópulton lévő adatok felhasználásával azonosíthatja eszközök, amelyek nem futnak, vagy, amely más problémák merülnek fel, és esetleg a alkalmazni az észlelt hibák javításait.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat. A Log Analytics a Surface Hubokban kezeléséhez, a következő lesz szüksége:

* A [Log Analytics-előfizetést](https://azure.microsoft.com/pricing/details/log-analytics/) szintet, amely támogatja a figyelni kívánt eszközök számát. Log Analytics díjszabása függ attól függően, hogy hány eszköz regisztrált, és mennyi adatot, folyamatokat. Érdemes figyelembe venni ezt, a Surface Hub bevezetésének megtervezésekor.

Ezután fogja hozzáadni egy meglévő Log Analytics-munkaterületet vagy hozzon létre egy újat. Jelenleg mindkét módszer használatával történő szinkronizálásának részletes útmutatásáért [Log Analytics-munkaterület létrehozása az Azure Portalon](../../log-analytics/log-analytics-quick-create-workspace.md). Miután konfigurálta a Log Analytics-munkaterületet, két módja a Surface Hub-eszközök regisztrálásához:

* Automatikusan Intune-nal
* Manuálisan keresztül **beállítások** a Surface Hub eszköz.

## <a name="set-up-monitoring"></a>Állítsa be a figyelést,
Az állapotát és tevékenységét a Surface Hub Log Analytics használatával figyelheti. A Surface Hub is regisztrálásához, Intune-nal vagy a helyi használatával **beállítások** a Surface hubon.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Csatlakozás a Surface hubokon a Log Analytics az Intune-ban
A Log Analytics-munkaterületet, amely kezelni fogja a Surface Hubokban szüksége a munkaterület-Azonosítót és a munkaterületkulcsot. A munkaterület beállításait szerintiek kaphat az Azure Portalon.

Intune-nal, a Microsoft-termék, amely lehetővé teszi, hogy központilag kezelheti a Log Analytics-konfigurációs beállításait, amelyek egy vagy több eszközön érvénybe lépnek. Kövesse az alábbi lépéseket az eszközök Intune-nal végzett konfigurálásához:

1. Jelentkezzen be az Intune-hoz.
2. Navigáljon a **beállítások** > **csatlakoztatott források**.
3. Hozzon létre, vagy a szabályzatot a Surface Hub-sablon alapján.
4. Keresse meg a szabályzat az Azure Operational Insights szakaszát, és adja hozzá a Log Analytics *munkaterület-Azonosítót* és *Munkaterületkulcsot* a szabályzathoz.
5. A házirend mentéséhez.
6. A házirendet társítani az eszközök a megfelelő csoporthoz.

   ![Intune-szabályzat](./media/surface-hubs/intune.png)

Intune az eszközök a célcsoportban, regisztrálja őket a Log Analytics-munkaterület majd szinkronizál a Log Analytics-beállításait.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Surface hubok csatlakoztatása a Log Analytics szolgáltatásnak történő a gépház alkalmazás
A Log Analytics-munkaterületet, amely kezelni fogja a Surface Hubokban szüksége a munkaterület-Azonosítót és a munkaterületkulcsot. Az Azure Portalon a Log Analytics-munkaterület beállításait szerintiek kérheti le.

Ha a környezet felügyeletéhez az Intune nem használja, manuálisan keresztül eszközöket regisztrálni **beállítások** egyes Surface hub:

1. Nyissa meg a Surface Hub **beállítások**.
2. Adja meg az eszköz rendszergazdai hitelesítő adatokat.
3. Kattintson a **az eszköz**, és a alatt **figyelés**, kattintson a **Log Analytics beállításainak konfigurálása**.
4. Válassza ki **engedélyezze a monitorozást**.
5. A Log Analytics-beállítások párbeszédpanelen írja be a Log Analytics **munkaterület-Azonosítót** , és írja be a **Munkaterületkulcsot**.  
   ![Beállítások](./media/surface-hubs/settings.png)
6. Kattintson a **OK** konfigurálásának befejezéséhez.

Egy megerősítő felszólító-e a konfiguráció sikeresen alkalmazta az eszközre jelenik meg. Amennyiben igen, megjelenik egy üzenet arról, hogy az ügynök sikeresen csatlakoztatva a Log Analytics szolgáltatásba. Az eszköz elindítja a Log Analytics, melyen megtekintheti, és reagálhat rájuk, hogy adatokat küld a.

## <a name="monitor-surface-hubs"></a>A figyelő a Surface hubokon
A Surface Hubokban figyelés a Log Analytics használata lényegében ugyanúgy figyelése a regisztrált eszközöket.

1. Jelentkezzen be az Azure portálra.
2. A Log Analytics-munkaterületen keresse meg és válassza **áttekintése**.
2. Kattintson a Surface Hub csempére.
3. Az Eszközállapot akkor jelenik meg.

   ![Surface Hub-irányítópulton](./media/surface-hubs/surface-hub-dashboard.png)

Létrehozhat [riasztások](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) meglévő vagy egyéni naplókeresések alapján. Használja az adatok Log Analytics gyűjti össze a Surface Hubokban, kereshet problémákat és az eszközök meghatározó feltételeket meg a riasztás.

## <a name="next-steps"></a>További lépések
* Használat [Log Analytics naplóbeli kereséseivel](../../log-analytics/log-analytics-queries.md) Surface Hub részletes adatainak megtekintéséhez.
* Hozzon létre [riasztások](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) arra az esetre, ha a probléma lép fel a Surface Hubokban.
