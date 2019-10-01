---
title: Kiszolgálói naplók konfigurálása és elérése Azure Portal Azure Database for MariaDBhoz
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a kiszolgálói naplókat a Azure Portal Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c8be9519d3393330b3022fadd2de6a49e58ecdcf
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703498"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Kiszolgálói naplók konfigurálása és elérése a Azure Portalban

Beállíthatja, listázhatja és letöltheti az [Azure Database for MariaDB lassú lekérdezési naplókat](concepts-server-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a lassú lekérdezési naplóhoz való hozzáférést. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a Azure Database for MariaDB-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. 
   @no__t – 0Select-kiszolgáló naplófájljai, kattintson ide a @ no__t-1 konfigurálásához

4. Jelölje be a fejlécet, **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához** a kiszolgáló paramétereinek megtekintéséhez.

5. Módosítsa a módosítani kívánt paramétereket, beleértve a "slow_query_log" bekapcsolását a következőre:. Az ebben a munkamenetben végrehajtott módosítások a lila színnel vannak kiemelve. 

   A paraméterek módosítása után kattintson a **Mentés**gombra. Vagy elvetheti a módosításokat.

   ![Kattintson a Mentés vagy az Elvetés gombra.](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. A naplók listájához való visszatéréshez kattintson a **Bezárás gombra** (X ikon) a **kiszolgáló paraméterek** lapján.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat a kiszolgálói naplók ablaktáblán. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki a Azure Database for MariaDB-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. A lap a naplófájlok listáját jeleníti meg, ahogy az alábbi ábrán látható:

   ![Naplók listája](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója **MySQL-Slow-< a kiszolgáló nevét >-yyyymmddhh. log**. A fájlnévben használt dátum és idő az az idő, amikor a naplót kiállították. A naplófájlokat 24 óránként vagy 7,5 GB-ban forgatják el, attól függően, hogy melyik érkezik először.

4. Ha szükséges, a **keresőmező** használatával gyorsan leszűkítheti az adott naplót dátum/idő alapján. A keresés a napló nevében található.

5. Töltse le az egyes naplófájlokat a táblázat soraiban a **Letöltés** gomb (lefelé mutató nyíl ikon) mellett az alábbi ábrán látható módon:

   ![Kattintson a letöltés ikonra](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** területén válassza a **diagnosztikai beállítások**elemet.

1. Kattintson a "+ diagnosztikai beállítás hozzáadása" ![Add diagnosztikai beállítás @ no__t-1 elemre.

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely adatnyelők küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub és/vagy Log Analytics munkaterület).

1. Válassza a "MySqlSlowLogs" lehetőséget a napló típusaként.
@no__t – 0Configure diagnosztikai beállítás @ no__t-1

1. Miután konfigurálta az adatvesztést, hogy a lassú lekérdezési naplók a következőre legyenek beállítva, kattintson a **Save (Mentés**) gombra.
@no__t – 0Save diagnosztikai beállítás @ no__t-1

1. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
- A lassú lekérdezési naplók programozott módon történő letöltésének megismeréséhez lásd: [a lassú lekérdezési naplók elérése a CLI-ben](howto-configure-server-logs-cli.md) .
- További információ a Azure Database for MariaDB [lassú lekérdezési naplóiról](concepts-server-logs.md) .
- A paraméter-definíciókkal és a naplózással kapcsolatos további információkért tekintse meg a [naplók](https://mariadb.com/kb/en/library/slow-query-log-overview/)MariaDB dokumentációját.