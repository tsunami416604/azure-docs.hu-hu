---
title: Az Azure Application Insights pillanatkép-hibakeresőjének frissítése
description: A Snapshot Debugger frissítése a .NET-alkalmazásokhoz az Azure App Services legújabb verziójára vagy Nuget-csomagokon keresztül
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671392"
---
# <a name="upgrading-the-snapshot-debugger"></a>A pillanatkép-hibakereső frissítése

Az adatok lehető legjobb biztonsága érdekében a Microsoft eltávolodik a TLS 1.0-tól és a TLS 1.1-től, amelyekről bebizonyosodott, hogy ki vannak téve a meghatározott támadóknak. Ha a helybővítmény régebbi verzióját használja, a munka folytatásához frissítésre lesz szükség. Ez a dokumentum ismerteti a snapshot hibakereső legújabb verzióra való frissítéséhez szükséges lépéseket. Két elsődleges frissítési útvonal van attól függően, hogy engedélyezte-e a Snapshot Debuggert egy helybővítmény használatával, vagy az alkalmazáshoz hozzáadott SDK/Nuget-et használta. Az alábbiakban mindkét frissítési útvonalat tárgyaljuk. 

## <a name="upgrading-the-site-extension"></a>A helybővítmény frissítése

> [!IMPORTANT]
> Az Application Insights régebbi verziói egy privát webhelybővítményt, _az Application Insights-bővítményt használták az Azure App Service-hez._ A jelenlegi Application Insights-élmény engedélyezve van, ha úgy állítja be az Alkalmazásbeállításokat, hogy egy előre telepített webhelybővítményt világítson meg.
> Az ütközések elkerülése érdekében, amelyek a webhely leállását okozhatják, fontos, hogy először törölje a privát webhelybővítményt. Lásd az alábbi 4.

Ha engedélyezte a Snapshot hibakeresőt a webhelybővítmény használatával, az alábbi eljárással frissíthet:

1. Jelentkezzen be az Azure portálra.
2. Keresse meg az erőforrást, amely az Application Insights és a Snapshot hibakereső engedélyezve van. Egy webalkalmazás esetében például keresse meg az App Service-erőforrást:

   ![Képernyőkép a DiagService01 nevű egyes App Service-erőforrásról](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Miután megkereste az erőforrást, kattintson a Bővítmények panelre, és várja meg, amíg a bővítmények listája feltölti:

   ![Képernyőkép az Alkalmazásszolgáltatás-bővítményekről, amelyen telepítve van az Application Insights-bővítmény az Azure App Service-hez](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Ha az _Application Insights-bővítmény_ bármely verziója telepítve van, jelölje ki, és kattintson a Törlés gombra. Erősítse meg **az Igen** gombot a bővítmény törléséhez, és várja meg, amíg a törlés befejeződik, mielőtt továbblépne a következő lépésre.

   ![Képernyőkép az Alkalmazásszolgáltatás-bővítményekről, amely az Application Insights-bővítményt jeleníti meg az Azure App Service-hez, kiemelve a Törlés gombot](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Nyissa meg az erőforrás áttekintése panelt, és kattintson az Application Insights elemre:

   ![Képernyőkép három gombról. Az Application Insights nevű Középre gomb van kiválasztva](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Ha ez az első alkalom, hogy megtekintette az Application Insights panel az app service, a rendszer kéri, hogy kapcsolja be az Application Insights. Válassza **az Application Insights bekapcsolása**lehetőséget.
 
   ![Képernyőkép az Application Insights panel első élményéről, amelyen kiemelt en van az Application Insights bekapcsolása gomb](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Az Application Insights aktuális beállításai jelennek meg. Hacsak nem szeretné kihasználni a lehetőséget a beállítások módosítására, akkor hagyja őket úgy, ahogy van. A panel alján található **Alkalmaz** gomb alapértelmezés szerint nincs engedélyezve, és a gomb aktiválásához be kell kapcsolnia az egyik beállítást. Nem kell módosítania a tényleges beállításokat, inkább módosíthatja a beállítást, majd azonnal visszamódosíthatja. Javasoljuk, hogy kapcsolja be a Profiler beállítást, majd válassza **az Alkalmaz**lehetőséget.

   ![Képernyőkép az Application Insights App Service konfigurációja lapról, piros színnel kiemelve az Alkalmazás gombbal](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Miután az **Alkalmaz gombra**kattintott, a rendszer megkéri a módosítások megerősítésére.

    > [!NOTE]
    > A webhely a frissítési folyamat részeként újraindul.

   ![Képernyőkép az App Service alkalmazásfigyelési parancsáról. A szövegmező a következő üzenetet jeleníti meg: "Mostantól alkalmazzuk az alkalmazásbeállítások módosításait, és telepítjük az eszközeinket, hogy összekapcsoljuk az Application Insights-erőforrást a webalkalmazással. Ez újraindítja a helyet. Folytatja?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Kattintson az **Igen** gombra a módosítások alkalmazásához, és várja meg, amíg a folyamat befejeződik.

A webhely most már frissítve lett, és készen áll a használatra.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Pillanatkép-hibakereső frissítése SDK/Nuget használatával

Ha az alkalmazás az `Microsoft.ApplicationInsights.SnapshotCollector` 1.3.1-es verzió alatti verziót használja, a munka folytatásához frissíteni kell egy [újabb verzióra.](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)
