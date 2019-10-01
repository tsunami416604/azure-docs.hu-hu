---
title: Lassú lekérdezési naplók konfigurálása és elérése Azure Portal Azure Database for MySQLhoz
description: Ez a cikk bemutatja, hogyan konfigurálhatja és érheti el a Azure Database for MySQL lassú naplóit a Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703440"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése a Azure Portal

Beállíthatja, listázhatja és letöltheti az [Azure Database for MySQL lassú lekérdezési naplókat](concepts-server-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a MySQL lassú lekérdezési napló elérését. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. 
   @no__t – 0Select-kiszolgáló naplófájljai, kattintson ide a @ no__t-1 konfigurálásához

4. Jelölje be a fejlécet, **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához** a kiszolgáló paramétereinek megtekintéséhez.

5. Módosítsa a módosítani kívánt paramétereket. Az ebben a munkamenetben végrehajtott módosítások a lila színnel vannak kiemelve. 

   A paraméterek módosítása után kattintson a **Mentés**gombra. Vagy elvetheti a módosításokat.

   ![Kattintson a Mentés vagy az Elvetés gombra.](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. A naplók listájához való visszatéréshez kattintson a **Bezárás gombra** (X ikon) a **kiszolgáló paraméterek** lapján.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat a kiszolgálói naplók ablaktáblán.

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. A lap a naplófájlok listáját jeleníti meg, ahogy az alábbi ábrán látható:

   ![Naplók listája](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója **MySQL-Slow-< a kiszolgáló nevét >-yyyymmddhh. log**. A fájlnévben használt dátum és idő az az idő, amikor a naplót kiállították. A naplófájlokat 24 óránként vagy 7,5 GB-ban forgatják el, attól függően, hogy melyik érkezik először.

4. Ha szükséges, a **keresőmező** használatával gyorsan leszűkítheti az adott naplót dátum/idő alapján. A keresés a napló nevében található.

5. Töltse le az egyes naplófájlokat a táblázat soraiban a **Letöltés** gomb (lefelé mutató nyíl ikon) mellett az alábbi ábrán látható módon:

   ![Kattintson a letöltés ikonra](./media/howto-configure-server-logs-in-portal/5-download.png)

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
- A lassú lekérdezési naplók programozott módon történő letöltésének megismeréséhez lásd: [a lassú lekérdezési naplók elérése a CLI-ben](howto-configure-server-logs-in-cli.md) .
- További információ a Azure Database for MySQL [lassú lekérdezési naplóiról](concepts-server-logs.md) .
- A paraméter-definíciókkal és a MySQL-naplózással kapcsolatos további információkért tekintse meg a MySQL dokumentációját a [naplókon](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).