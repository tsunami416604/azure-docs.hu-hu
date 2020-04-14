---
title: Lassú lekérdezési naplók elérése – Azure portal – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a lassú naplók az Azure Database for MySQL az Azure Portalon.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273604"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure Portalról

Konfigurálhatja, listázhatja és letöltheti az [Azure Database for MySQL lassú lekérdezési naplók](concepts-server-logs.md) az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések hez az [Azure Database for MySQL server szükséges.](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a mySQL lassú lekérdezési naplóhoz való hozzáférést. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure-adatbázis t a MySQL-kiszolgálóhoz.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. 
   ![Képernyőkép a Kiszolgálói naplók beállításairól](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplóparaméterek konfigurálásához.**

5. Kapcsolja **be slow_query_log** **.**

6. Adja meg, hogy hol szeretné kiadni a naplókat **log_output**használatával. Ha naplókat szeretne küldeni a helyi tárolóba és az Azure Monitor diagnosztikai naplóiba, válassza a **Fájl**lehetőséget. 

7. Módosítsa a szükséges egyéb paramétereket. 

8. Kattintson a **Mentés** gombra. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Képernyőkép a lassú lekérdezési napló paramétereiről és mentéséről.":::

A **Kiszolgálóparaméterek** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és letöltési naplók
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat.

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure-adatbázis t a MySQL-kiszolgálóhoz.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. A lapon a naplófájlok listája látható.

   ![Képernyőkép a Kiszolgálói naplók lapról, a kiemelt naplók listájával](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Az elnevezési konvenció a napló **mysql-lassú-< a szerver nevét>-yyyymmddhh.log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlok at 24 óránként vagy 7,5 GB-onként forgatják el, attól függően, hogy melyik következik be előbb. 

4. Szükség esetén a keresőmező segítségével gyorsan leszűkítheti egy adott naplóra a dátum és az idő alapján. A keresés a napló nevén van.

5. Az egyes naplófájlok letöltéséhez jelölje ki a táblázatsor minden naplófájlja melletti lefelé mutató nyílikont.

   ![Képernyőkép a Kiszolgálói naplók lapról, kiemelve a lefelé mutató nyíl ikonnal](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **Figyelés** szakaszában válassza a **Diagnosztikai beállítások** > **Diagnosztikai beállítások hozzáadása**lehetőséget.

   ![Képernyőkép a Diagnosztikai beállítások beállításairól](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítás nevét.

1. Adja meg, hogy mely adatgyűjtők küldjék el a lassú lekérdezési naplókat (tárfiók, eseményközpont vagy Log Analytics-munkaterület).

1. Válassza ki **a MySqlSlowLogs** naplótípust.
![Képernyőkép a Diagnosztikai beállítások konfigurációs beállításairól](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Miután beállította az adatgyűjtőket, hogy a lassú lekérdezési naplókat átadják, válassza a **Mentés gombot.**
![Képernyőkép a Diagnosztikai beállítások konfigurációs beállításairól, kiemelt Mentés lehetőséggel](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. A lassú lekérdezési naplók elérése a konfigurált adatgyűjtőkben való felfedezésükvel. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
- Az [Access lassú lekérdezési naplói a CLI-ben](howto-configure-server-logs-in-cli.md) részben ismerteti, hogy miként töltheti le programozott módon a lassú lekérdezési naplókat.
- További információ a [lassú lekérdezési naplók az](concepts-server-logs.md) Azure Database for MySQL.
- A paraméterdefiníciókról és a MySQL naplózásról további információt a [MySQL naplókdokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)talál.