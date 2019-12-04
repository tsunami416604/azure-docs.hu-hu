---
title: Naplók kezelése – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a kiszolgálói naplókat (. naplófájlokat) Azure Database for PostgreSQL egyetlen kiszolgálón a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763691"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure Database for PostgreSQL – egy kiszolgáló naplófájljainak konfigurálása és elérése a Azure Portal

A Azure Portalból konfigurálhatja, listázhatja és letöltheti a [Azure Database for PostgreSQL naplókat](concepts-server-logs.md) .

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához Azure Database for PostgreSQL- [kiszolgáló](quickstart-create-server-database-portal.md)szükséges.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a lekérdezési naplók és a hibanapló elérését. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. 

   ![A kiszolgálói naplók beállításainak képernyőképe](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához**.

5. Módosítsa a módosítani kívánt paramétereket. Az ebben a munkamenetben végrehajtott módosítások a lila színnel vannak kiemelve.

   A paraméterek módosítása után válassza a **Mentés**lehetőséget. Vagy elvetheti a módosításokat. 

   ![A kiszolgálói paraméterek beállításainak képernyőképe](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető naplók listáját, és letöltheti az egyes naplófájlokat. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. A lap megjeleníti a naplófájlok listáját.

   ![Képernyőkép a kiszolgálói naplók lapról, a Kiemelt naplók listájával](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója: **PostgreSQL-éééé-hh-dd_hh0000. log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlok óránként, vagy 100 MB-ban forognak, attól függően, hogy melyik következik be először.

4. Ha szükséges, a keresőmező használatával gyorsan leszűkítheti egy adott naplóra a dátum és idő alapján. A keresés a napló nevében található.

   ![Képernyőkép a kiszolgálói naplók lapról, a keresőmező és az eredmények kiemelésével](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Az egyes naplófájlok letöltéséhez kattintson az egyes naplófájlok melletti lefelé mutató nyíl ikonra a táblázat sorában.

   ![Képernyőkép a kiszolgálói naplók lapról, a lefelé mutató nyíl ikon kiemelve](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Következő lépések
- A naplófájlok programozott módon történő letöltésének megismeréséhez tekintse meg [a hozzáférési kiszolgáló naplófájljai a CLI-ben](howto-configure-server-logs-using-cli.md) című témakört.
- További információ a Azure Database for PostgreSQL [kiszolgálói naplóiról](concepts-server-logs.md) . 
- A paraméter-definíciókkal és a PostgreSQL-naplózással kapcsolatos további információkért tekintse meg a PostgreSQL dokumentációját a [hibajelentés és a naplózás](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)témakörben.

