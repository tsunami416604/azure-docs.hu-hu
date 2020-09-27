---
title: Lassú lekérdezési naplók konfigurálása – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a lassú lekérdezési naplókat Azure Database for MySQL rugalmas kiszolgálóról a Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a73a2bc82c7f4e77808f751bb8ba24adcacd2e31
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400383"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése Azure Database for MySQL rugalmas kiszolgálóhoz a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Beállíthatja, listázhatja és letöltheti a Azure Database for MySQL rugalmas kiszolgáló [lassú lekérdezési naplóit](concepts-slow-query-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések megkövetelik, hogy [rugalmas kiszolgálóval](quickstart-create-server-portal.md)rendelkezzen.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a MySQL lassú lekérdezési napló elérését. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki a rugalmas kiszolgálót.

1. Az oldalsáv **Beállítások** szakaszában válassza ki a **kiszolgáló paramétereit**.
   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot of Server logs options":::-->

1. Frissítse a **slow_query_log** paramétert **a**következőre:.

1. Módosítsa a többi szükséges paramétert (pl. `long_query_time`, `log_slow_admin_statements`). További paraméterekért tekintse meg a [lassú lekérdezési naplók](./concepts-slow-query-logs.md#configure-slow-query-logging) dokumentációját.  

1. Kattintson a **Mentés** gombra. 

   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot of slow query log parameters and save."::: -->

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="set-up-diagnostics"></a>Diagnosztika beállítása

A lassú lekérdezési naplók integrálva vannak Azure Monitor diagnosztikai beállításokkal, amelyek lehetővé teszik a naplók Azure Monitor naplókba, Event Hubs vagy Azure Storage-ba történő átadását.

1. Az oldalsáv **figyelés** szakaszában válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítások**elemet.

   <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings options":::-->

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely célhelyek küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub vagy Log Analytics munkaterület).

1. Válassza a **MySqlSlowLogs** lehetőséget a napló típusaként.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options":::-->

1. Miután konfigurálta az adatnyelőket a lassú lekérdezések naplóinak a csatornába való konfigurálásához, válassza a **Mentés**lehetőséget.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options, with Save highlighted":::-->

1. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

Ha a naplókat Azure Monitor naplókba (Log Analytics) használja, tekintse meg az elemzéshez használható [példákat](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs) . 

## <a name="next-steps"></a>További lépések
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- További információ a [lassú lekérdezési naplókról](concepts-slow-query-logs.md)
- A paraméter-definíciókkal és a MySQL-naplózással kapcsolatos további információkért tekintse meg a MySQL dokumentációját a [naplókon](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- A [naplók](concepts-audit-logs.md) ismertetése
