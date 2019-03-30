---
title: .NET-alkalmazások az Azure Application Insights Snapshot Debugger frissítés |} A Microsoft Docs
description: Hogyan frissítse a Snapshot Debuggert a legújabb verzióra, vagy az Azure App Services Nuget-csomagok
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632373"
---
# <a name="upgrading-the-snapshot-debugger"></a>A pillanatkép-hibakereső frissítése

Adja meg a lehetséges legjobb biztonsági adataihoz, a Microsoft a távolodnak a TLS 1.0 és a TLS 1.1, amelyek sebezhetővé meghatározott lehet látható. Ha egy régebbi verzióját a webhely-bővítményt használ, ehhez szükség lesz a frissítés a munka folytatásához. Ez a dokumentum frissítése a legújabb verzióra a pillanatkép-hibakereső szükséges lépéseket ismerteti. Nincsenek két elsődleges frissítési útvonalak attól függően, ha engedélyezte a pillanatkép-hibakereső webhelybővítmény használatával, vagy ha az SDK/Nuget hozzáadása az alkalmazáshoz használt. Mindkét frissítési útvonalak az alábbiak ismertetik. 

## <a name="upgrading-the-site-extension"></a>A site-bővítmény frissítése

Ha engedélyezte a pillanatkép-hibakereső a webhely-bővítménnyel, könnyen frissítheti az alábbi eljárást követve:

1. Jelentkezzen be az Azure portálra.
2. Keresse meg az erőforrás, amelynek az Application Insights és a pillanatkép-hibakereső engedélyezve van. Például egy Web App alkalmazásban nyissa meg az App Service-erőforrást:

   ![Képernyőkép az egyes App Service-erőforrások nevű DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Miután megnyit az erőforrást, az Áttekintés panelen kattintson az Application Insights:

   ![Képernyőfelvétel a gombok három. Középső gomb az Application Insights neve van kiválasztva](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Új megnyílik egy panel az aktuális beállításokkal. Szeretnénk megragadni a lehetőséget a beállítások módosításáról, kivéve azokat, hagyhatja. A **alkalmaz** alapértelmezés szerint nincs engedélyezve a gombra a panel alján, és váltsa át a beállításokat a gomb aktiválásához egyik kell. Nem kell a tényleges beállítások módosításához, inkább a beállítást, és majd azonnal módosítsa azt vissza. Azt javasoljuk, hogy a beállítást, és válassza a Profiler vizualizációtól **alkalmaz**.

   ![A vörös színnel alkalmaz gomb képernyőképe az Application Insights App Service konfigurációjának lap](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Miután rákattint **alkalmaz**, meg kell adnia a hagyja jóvá a módosításokat.

    > [!NOTE]
    > A frissítési folyamat részeként újraindítja a helyet.

   ![Képernyőkép az App Service a alkalmazni figyelési parancssort. Szövegmező üzenetet jeleníti meg: "Azt fogja most alkalmazza az alkalmazásbeállítások módosításait és telepíti az eszközöket az Application Insights-erőforrás összekapcsolása a webalkalmazást. A művelet újraindítja a helyet. Biztosan folytatja?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Kattintson a **Igen** a módosítások életbe léptetéséhez. A folyamat során egy értesítés jelenik meg, hogy módosításai alkalmazásra kerülnek megjelenítése:

   ![Képernyőkép a módosítások alkalmazása – a jobb felső sarkában megjelenő üzenet bővítmények frissítése](./media/snapshot-debugger-upgrade/updating-extensions.png)

Ha befejeződött, egy **"Lépnek életbe a módosítások"** értesítés jelenik meg.

   ![Képernyőkép a üzenet figyelmezteti a módosítások alkalmazása](./media/snapshot-debugger-upgrade/changes-are-applied.png)

A hely frissítve lett, és készen áll a használatra.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK-t és a Nugetet használó pillanatkép-hibakereső frissítése

Ha az alkalmazás egy verzióját `Microsoft.ApplicationInsights.SnapshotCollector` verziónál 1.3.1, el kell frissíteni, hogy egy [újabb verzióra](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) a munka folytatásához.
