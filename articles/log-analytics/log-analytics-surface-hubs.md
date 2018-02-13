---
title: "Az Azure Naplóelemzés hubok figyelése |} Microsoft Docs"
description: "A Surface Hub megoldást használni a Surface hub állapotának nyomon követése és megérteni, hogyan kívánják felhasználni."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>A Naplóelemzési nyomon követéséhez az állapotukat Surface hub figyelése

![Surface Hub szimbólum](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Ez a cikk ismerteti, hogyan használhatja a Surface Hub-megoldás a Log Analyticshez figyelése a Microsoft Surface Hub eszközöket. Naplóelemzési segítségével nyomon követheti a Surface hub állapotát, valamint megérteni, hogyan kívánják felhasználni.

Minden egyes Surface Hub a Microsoft Monitoring Agent telepítve van. Annak az ügynököt, hogy küldhet adatokat a Surface Hub a Naplóelemzési keresztül. Naplófájlok a Surface hub és a olvasni, majd Naplóelemzési küldött. Problémák, például kiszolgálók nem kapcsolódik, a naptár nem szinkronizálja, vagy ha a fiók nem tud bejelentkezni, a Surface Hub irányítópult Naplóelemzési jelennek meg a Skype. Az adatok az irányítópult használatával azonosíthatja az eszközök nem fut, vagy amelyek egyéb problémákat, amelynek, és potenciálisan alkalmazása az észlelt probléma javítását.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
Az alábbi információk segítségével telepítse és konfigurálja a megoldást. A Naplóelemzési a Surface hub kezeléséhez, a következőkre lesz szüksége:

* A [Naplóelemzési előfizetés](https://azure.microsoft.com/pricing/details/log-analytics/) szintje, amely támogatja a figyelni kívánt eszközök számára. Napló Analytics díjszabásáról változó attól függően, hogy hány eszköz regisztrálva van, és mennyi adatot azt folyamatokat. Érdemes figyelembe venni a, a Surface Hub-bevezetés tervezése során.

A következő lesz hozzáadása egy meglévő Naplóelemzési munkaterület vagy hozzon létre egy újat. Részletes utasításokat a következő módszerek egyikével érték [Ismerkedés a Naplóelemzési](log-analytics-get-started.md). Ha konfigurálva van a Naplóelemzési munkaterület, két módon lehet regisztrálni kell az Surface Hub eszközöket:

* Automatikusan Intune-nal
* Manuálisan osztályig **beállítások** Surface Hub eszközén.

## <a name="set-up-monitoring"></a>Állítsa be a figyelést,
Állapotát és tevékenységét a Surface Hub Naplóelemzési használatával figyelheti. A Surface Hub regisztrálása az Intune-ban, vagy helyileg segítségével **beállítások** a Surface hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Hubok csatlakoztatása az Intune szolgáltatáshoz
A munkaterület Azonosítóját és kulcsát a Naplóelemzési munkaterület, a Surface hub kezelésére lesz szüksége. A munkaterület beállításai megfelelők kaphat az Azure portálon.

Intune-ban egy Microsoft-termék, amely lehetővé teszi a Naplóelemzési konfigurációs beállításait egy vagy több eszköz központi kezelését. Kövesse az alábbi lépéseket az eszközök Intune-nal:

1. Jelentkezzen be az Intune-hoz.
2. Navigáljon a **beállítások** > **csatlakoztatott adatforrások**.
3. Hozzon létre, vagy szerkesztheti a házirendet, a Surface Hub-sablon alapján.
4. Lépjen a OMS (Azure Operational Insights) szakaszra a házirend, és adja hozzá a Naplóelemzési *munkaterület azonosítója* és *Munkaterületkulcsot* a házirendhez.
5. A házirend mentéséhez.
6. A megfelelő csoporthoz az eszközök társítja a házirendet.

   ![Az Intune-házirend](./media/log-analytics-surface-hubs/intune.png)

Az Intune majd szinkronizál a Log Analytics-beállítások az eszközök a célcsoportban, ha regisztrálja azokat a Naplóelemzési munkaterület.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Adatforrások csatlakoztatása Surface hub használatával a beállítások alkalmazásának szolgáltatáshoz
A munkaterület Azonosítóját és kulcsát a Naplóelemzési munkaterület, a Surface hub kezelésére lesz szüksége. Azok a beállítások a Naplóelemzési munkaterület az Azure portálon kérheti le.

Ha az Intune használatával nem a környezete kezeléséhez, manuálisan keresztül az eszközök regisztrálása **beállítások** minden Surface hub:

1. Nyissa meg a Surface Hub **beállítások**.
2. Adja meg az eszköz rendszergazdai hitelesítő adatokat.
3. Kattintson a **az eszköz**, és az alatt **figyelés**, kattintson a **OMS-beállítások konfigurálása**.
4. Válassza ki **engedélyezze a megfigyelést**.
5. Az OMS-beállításai párbeszédpanelen írja be a Naplóelemzési **munkaterület azonosítója** , és írja be a **Munkaterületkulcsot**.  
   ![settings](./media/log-analytics-surface-hubs/settings.png)
6. Kattintson a **OK** a konfigurálás befejezéséhez.

Megerősítést felszólító-e a konfiguráció sikeresen alkalmazta a eszköz jelenik meg. Ha igen, megjelenik egy üzenet arról, hogy az ügynök sikeresen csatlakoztatva Naplóelemzési. Az eszköz ezután elindítja az adatok küldése a Naplóelemzési, ahol megtekintheti és a működésre.

## <a name="monitor-surface-hubs"></a>A figyelő hubok
Figyelés, a Surface hub Naplóelemzési használata hasonlóan a további regisztrált eszközök figyelését.

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a Naplóelemzési munkaterületet, és válassza ki **áttekintése**.
2. Kattintson a Surface Hub csempére.
3. Az eszköz állapota akkor jelenik meg.

   ![Surface Hub irányítópult](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Létrehozhat [riasztások](log-analytics-alerts.md) meglévő vagy egyéni napló keresés alapján. Naplóelemzési gyűjti össze a Surface hub adatok használatával is kereshet problémákat és az eszközök meghatározó feltételeket a riasztást.

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) Surface Hub részletes adatainak megtekintéséhez.
* Hozzon létre [riasztások](log-analytics-alerts.md) értesítse arról, ha a probléma lép fel a Surface hub.
