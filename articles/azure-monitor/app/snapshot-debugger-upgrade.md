---
title: Azure Application Insights Snapshot Debugger .NET-alkalmazások frissítése | Microsoft Docs
description: Snapshot Debugger frissítése az Azure App Services legújabb verziójára vagy Nuget-csomagokon keresztül
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899839"
---
# <a name="upgrading-the-snapshot-debugger"></a>A Snapshot Debugger frissítése

Az adatok lehető legjobb biztonságának biztosítása érdekében a Microsoft elmozdul a TLS 1,0 és a TLS 1,1 rendszertől, amelyek kimutatták, hogy sebezhetőek voltak a meghatározott támadók számára. Ha a hely bővítmény egy régebbi verzióját használja, akkor a működés folytatásához frissítésre van szükség. Ez a dokumentum a Snapshot Debugger legújabb verzióra való frissítéséhez szükséges lépéseket ismerteti. Ha engedélyezte a Snapshot Debuggert a hely kiterjesztése alapján, vagy ha az alkalmazáshoz hozzáadott SDK/Nuget használta, két elsődleges frissítési útvonal van. A frissítési útvonalakat az alábbiakban tárgyaljuk. 

## <a name="upgrading-the-site-extension"></a>A hely kiterjesztésének frissítése

Ha engedélyezte a pillanatkép-hibakeresőt a hely bővítmény használatával, a következő eljárással könnyedén frissítheti:

1. Jelentkezzen be az Azure portálra.
2. Navigáljon az erőforráshoz, amelyen engedélyezve van a Application Insights és a Snapshot Debugger. Egy webalkalmazás esetében például navigáljon a App Service erőforráshoz:

   ![Képernyőkép a DiagService01 nevű egyéni App Service erőforrásról](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Miután megtörtént az erőforráshoz való navigálása, kattintson Application Insights az Áttekintés panelen:

   ![Képernyőfelvétel három gombról. Application Insights nevű középső gomb van kiválasztva](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Ekkor megnyílik egy új panel a jelenlegi beállításokkal. Hacsak nem szeretné módosítani a beállításokat, meghagyhatja őket. A panel alján található **Apply (alkalmaz** ) gomb alapértelmezés szerint nincs engedélyezve, és a gomb aktiválásához be kell váltania az egyik beállítást. Nem kell módosítania az aktuális beállításokat, hanem módosíthatja a beállítást, majd azonnal visszaállíthatja azt. Javasoljuk, hogy állítsa be a Profiler-beállítást, majd válassza az **alkalmaz**lehetőséget.

   ![Képernyőkép a Application Insights App Service konfigurációs oldaláról a piros színnel jelölt Apply (alkalmaz) gombbal](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Miután rákattintott az **alkalmaz**gombra, a rendszer megkéri, hogy erősítse meg a módosításokat.

    > [!NOTE]
    > A rendszer a frissítési folyamat részeként újraindítja a helyet.

   ![Képernyőfelvétel: App Service alkalmazás-figyelési kérése. A szövegmező üzenet jelenik meg: "mostantól alkalmazjuk az Alkalmazásbeállítások módosításait, és a Application Insights erőforrás a webalkalmazáshoz való összekapcsolására szolgáló eszközöket telepítjük. Ekkor a rendszer újraindítja a helyet. Folytatja? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. A módosítások alkalmazásához kattintson az **Igen** gombra. A folyamat során egy értesítés jelenik meg, amely bemutatja, hogy a módosítások érvénybe lépnek:

   ![Képernyőkép a módosítások alkalmazása – bővítmények frissítése üzenet, amely megjelenik a jobb felső sarokban](./media/snapshot-debugger-upgrade/updating-extensions.png)

Ha elkészült, a rendszer a **"módosítások alkalmazása"** értesítést jeleníti meg.

   ![A módosítások érvénybe lépését jelző üzenet képernyőképe](./media/snapshot-debugger-upgrade/changes-are-applied.png)

A hely már frissítve lett, és készen áll a használatra.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Snapshot Debugger frissítése SDK/Nuget használatával

Ha az alkalmazás a 1.3.1-es verzió `Microsoft.ApplicationInsights.SnapshotCollector` verzióját használja, akkor a működés folytatásához frissítenie kell egy [újabb verzióra](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) .
