---
title: Lassú lekérdezési naplók konfigurálása és elérése Azure Database for MySQL a Azure Portal
description: Ez a cikk bemutatja, hogyan konfigurálhatja és érheti el a Azure Database for MySQL lassú naplóit a Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 7eeeb729973e484e9acb26f3ac8cc42693f72eea
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841587"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése a Azure Portal

Beállíthatja, listázhatja és letöltheti az [Azure Database for MySQL lassú lekérdezési naplókat](concepts-server-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához Azure Database for MySQL- [kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)szükséges.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a MySQL lassú lekérdezési napló elérését. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. 
   @no__t – 0Screenshot-beállítások (@ no__t-1)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához**.

5. Módosítsa a módosítani kívánt paramétereket. Az ebben a munkamenetben végrehajtott módosítások a lila színnel vannak kiemelve. 

   A paraméterek módosítása után válassza a **Mentés**lehetőséget. Vagy elvetheti a módosításokat.

   ![A kiszolgálói paraméterek beállításainak képernyőképe](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat.

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. A lap megjeleníti a naplófájlok listáját.

   ![Képernyőkép a kiszolgálói naplók lapról, a Kiemelt naplók listájával](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója **MySQL-Slow-< a kiszolgáló nevét >-yyyymmddhh. log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlokat 24 óránként vagy 7,5 GB-ban forgatják el, attól függően, hogy melyik következik be először. 

4. Ha szükséges, a keresőmező használatával gyorsan leszűkítheti egy adott naplóra a dátum és idő alapján. A keresés a napló nevében található.

5. Az egyes naplófájlok letöltéséhez kattintson az egyes naplófájlok melletti lefelé mutató nyíl ikonra a táblázat sorában.

   ![Képernyőkép a kiszolgálói naplók lapról, a lefelé mutató nyíl ikon kiemelve](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** szakaszában válassza a **diagnosztikai beállítások**@no__t – 2**diagnosztikai beállítások hozzáadása**elemet.

   ![A diagnosztikai beállítások beállításainak képernyőképe](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely adatnyelők küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub vagy Log Analytics munkaterület).

1. Válassza a **MySqlSlowLogs** lehetőséget a napló típusaként.
@no__t – a diagnosztikai beállítások konfigurációs beállításainak 0Screenshot @ no__t-1

1. Miután konfigurálta az adatnyelőket a lassú lekérdezések naplóinak a csatornába való konfigurálásához, válassza a **Mentés**lehetőséget.
@no__t – a diagnosztikai beállítások konfigurációs beállításainak 0Screenshot, a Kiemelt beállítások mentése @ no__t-1

1. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
- A lassú lekérdezési naplók programozott módon történő letöltésének megismeréséhez lásd: [a lassú lekérdezési naplók elérése a CLI-ben](howto-configure-server-logs-in-cli.md) .
- További információ a Azure Database for MySQL [lassú lekérdezési naplóiról](concepts-server-logs.md) .
- A paraméter-definíciókkal és a MySQL-naplózással kapcsolatos további információkért tekintse meg a MySQL dokumentációját a [naplókon](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).