---
title: Lassú lekérdezési naplók elérése – Azure portal – Azure Database for MariaDB
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a lassú lekérdezési naplók az Azure Database for MariaDB az Azure Portalon.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531396"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure Portalról

Konfigurálhatja, listázhatja és letöltheti az [Azure Database for MariaDB lassú lekérdezési naplókat](concepts-server-logs.md) az Azure Portalról.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépésekhez az [Azure Database for MariaDB server szükséges.](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a lassú lekérdezési naplóhoz való hozzáférést. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza ki az Azure-adatbázis Tiamikiszolgálóhoz.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. 
   ![Képernyőkép a Kiszolgálói naplók beállításairól](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplóparaméterek konfigurálásához.**

5. Módosítsa a módosítani kívánt paramétereket, például **kapcsolja be slow_query_log** BE **beállításra.** Az ezen a munkameneten végrehajtott összes módosítás lila színnel van kiemelve. 

   A paraméterek módosítása után válassza a **Mentés gombot.** Vagy elvetheti a módosításokat.

   ![Képernyőkép a Kiszolgálóparaméter-beállításokról](./media/howto-configure-server-logs-portal/3-save-discard.png)

A **Kiszolgálóparaméterek** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és letöltési naplók
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure-adatbázis Tiamikiszolgálóhoz.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. A lapon a naplófájlok listája látható.

   ![Képernyőkép a Kiszolgálói naplók lapról, a kiemelt naplók listájával](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Az elnevezési konvenció a napló **mysql-lassú-< a szerver nevét>-yyyymmddhh.log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlok at 24 óránként vagy 7,5 GB-onként forgatják el, attól függően, hogy melyik következik be előbb.

4. Szükség esetén a keresőmező segítségével gyorsan leszűkítheti egy adott naplóra a dátum és az idő alapján. A keresés a napló nevén van.

5. Az egyes naplófájlok letöltéséhez jelölje ki a táblázatsor minden naplófájlja melletti lefelé mutató nyílikont.

   ![Képernyőkép a Kiszolgálói naplók lapról, kiemelve a lefelé mutató nyíl ikonnal](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **Figyelés** szakaszában válassza a **Diagnosztikai beállítások** > **Diagnosztikai beállítás hozzáadása**lehetőséget.

   ![Képernyőkép a Diagnosztikai beállítások beállításairól](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítás nevét.

1. Adja meg, hogy mely adatgyűjtők küldjék el a lassú lekérdezési naplókat (tárfiók, eseményközpont vagy Log Analytics-munkaterület).

1. Válassza ki **a MySqlSlowLogs** naplótípust.
![Képernyőkép a Diagnosztikai beállítások konfigurációs beállításairól](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Miután beállította az adatgyűjtőket, hogy a lassú lekérdezési naplókat átadják, válassza a **Mentés gombot.**
![Képernyőkép a Diagnosztikai beállítások konfigurációs beállításairól, kiemelt Mentés lehetőséggel](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. A lassú lekérdezési naplók elérése a konfigurált adatgyűjtőkben való felfedezésükvel. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
- A lassú lekérdezésnaplók programozott módon való letöltéséről az [Access lassú lekérdezési naplóiban](howto-configure-server-logs-cli.md) olvashat.
- További információ a [lassú lekérdezési naplókról](concepts-server-logs.md) a MariaDB Azure Database-ben.
- A paraméterdefiníciókról és a naplózásról a MariaDB dokumentációjában olvashat [bővebben.](https://mariadb.com/kb/en/library/slow-query-log-overview/)