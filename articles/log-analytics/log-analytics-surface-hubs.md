---
title: "Az Azure Naplóelemzés hubok figyelése |} Microsoft Docs"
description: "A Surface Hub megoldást használni a Surface hub állapotának nyomon követése és megérteni, hogyan kívánják felhasználni."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6ecd0d09589fec85c1633f528afc1165c346b7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>A Naplóelemzési nyomon követéséhez az állapotukat Surface hub figyelése

![Surface Hub szimbólum](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Ez a cikk ismerteti, hogyan használhatja a Surface Hub-megoldás a Log Analyticshez figyelése a Microsoft Surface Hub eszközöket a Microsoft Operations Management Suite (OMS). Naplóelemzési segítségével nyomon követheti a Surface hub állapotát, valamint megérteni, hogyan kívánják felhasználni.

Minden egyes Surface Hub a Microsoft Monitoring Agent telepítve van. Annak az ügynököt, hogy küldhet adatokat a Surface Hub az OMS keresztül. Naplófájlok a Surface hub és a olvasni, majd az OMS szolgáltatáshoz kerülnek. Problémák, például kiszolgálók nem kapcsolódik, a naptár, a szinkronizálás nem, vagy ha a fiók nem tud bejelentkezni az Skype OMS láthatók, a Surface Hub irányítópulton. Az adatok az irányítópult használatával azonosíthatja az eszközök nem fut, vagy amelyek egyéb problémákat, amelynek, és potenciálisan alkalmazása az észlelt probléma javítását.

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása
Az alábbi információk segítségével telepítse és konfigurálja a megoldást. Ahhoz, hogy a Surface hub kezelése a Microsoft Operations Management Suite (OMS) származó, a következőkre lesz szüksége:

* Egy érvényes előfizetést [OMS](http://www.microsoft.com/oms).
* Egy [OMS előfizetés](https://azure.microsoft.com/pricing/details/log-analytics/) szintje, amely támogatja a figyelni kívánt eszközök számára. OMS árképzési változó attól függően, hogy hány eszköz regisztrálva van, és mennyi adatot azt folyamatokat. Érdemes figyelembe venni a, a Surface Hub-bevezetés tervezése során.

A következő fog egy OMS-előfizetés hozzáadása a meglévő Microsoft Azure-előfizetés vagy hozzon létre egy új munkaterületet közvetlenül az OMS-portálon keresztül. Részletes utasításokat a következő módszerek egyikével érték [Ismerkedés a Naplóelemzési](log-analytics-get-started.md). Az OMS-előfizetés beállítása után két módon lehet regisztrálni kell az Surface Hub eszközöket:

* Automatikusan Intune-nal
* Manuálisan osztályig **beállítások** Surface Hub eszközén.

## <a name="set-up-monitoring"></a>Állítsa be a figyelést,
Állapotát és tevékenységét a Surface Hub OMS Naplóelemzési használatával figyelheti. A Surface Hub az OMS regisztrálása az Intune-ban, vagy helyileg segítségével **beállítások** a Surface hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Hubok csatlakoztatni az OMS Szolgáltatáshoz Intune-nal
A munkaterület Azonosítóját és kulcsát az OMS-munkaterület, a Surface hub kezelésére lesz szüksége. Az OMS-portálon szerintiek kérheti le.

Intune-ban egy Microsoft-termék, amely lehetővé teszi az OMS konfigurációs beállításait egy vagy több eszköz központi kezelését. Kövesse az alábbi lépéseket az eszközök Intune-nal:

1. Jelentkezzen be az Intune-hoz.
2. Navigáljon a **beállítások** > **csatlakoztatott adatforrások**.
3. Hozzon létre, vagy szerkesztheti a házirendet, a Surface Hub-sablon alapján.
4. Lépjen a OMS (Azure Operational Insights) szakaszra a házirend, és adja hozzá a *munkaterület azonosítója* és *Munkaterületkulcsot* a házirendhez.
5. A házirend mentéséhez.
6. A megfelelő csoporthoz az eszközök társítja a házirendet.

   ![Az Intune-házirend](./media/log-analytics-surface-hubs/intune.png)

Az Intune majd szinkronizál az OMS-beállítások az eszközök a célcsoportban, ha regisztrálja azokat az OMS-munkaterület.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Surface hub csatlakoztatni az OMS Szolgáltatáshoz, a beállítások alkalmazással
A munkaterület Azonosítóját és kulcsát az OMS-munkaterület, a Surface hub kezelésére lesz szüksége. Az OMS-portálon szerintiek kérheti le.

Ha az Intune használatával nem a környezete kezeléséhez, manuálisan keresztül az eszközök regisztrálása **beállítások** minden Surface hub:

1. Nyissa meg a Surface Hub **beállítások**.
2. Adja meg az eszköz rendszergazdai hitelesítő adatokat.
3. Kattintson a **az eszköz**, és az alatt **figyelés**, kattintson a **OMS-beállítások konfigurálása**.
4. Válassza ki **engedélyezze a megfigyelést**.
5. Az OMS-beállításai párbeszédpanelen írja be a **munkaterület azonosítója** , és írja be a **Munkaterületkulcsot**.  
   ![beállítások](./media/log-analytics-surface-hubs/settings.png)
6. Kattintson a **OK** a konfigurálás befejezéséhez.

Megerősítést felszólító-e az OMS-konfigurációja sikeresen alkalmazta az eszköz jelenik meg. Ha igen, megjelenik egy üzenet arról, hogy az ügynök sikeresen csatlakozott az OMS szolgáltatáshoz. Az eszköz ezután elindítja az adatok küldése az OMS-be, ahol megtekintheti és a működésre.

## <a name="monitor-surface-hubs"></a>A figyelő hubok
Figyelés, a Surface hub OMS használata sokkal hasonlóan a további regisztrált eszközök figyelését.

1. Jelentkezzen be az OMS-portálon.
2. Nyissa meg a Surface Hub megoldás pack irányítópult.
3. Az eszköz állapota akkor jelenik meg.

   ![Surface Hub irányítópult](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Létrehozhat [riasztások](log-analytics-alerts.md) meglévő vagy egyéni napló keresés alapján. Az OMS gyűjti össze a Surface hub adatok használatával is kereshet problémákat és az eszközök meghatározó feltételeket a riasztást.

## <a name="next-steps"></a>Következő lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) Surface Hub részletes adatainak megtekintéséhez.
* Hozzon létre [riasztások](log-analytics-alerts.md) értesítse arról, ha a probléma lép fel a Surface hub.
