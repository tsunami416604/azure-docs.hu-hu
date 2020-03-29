---
title: Naplók kezelése - Azure portal – Azure-adatbázis a PostgreSQL-hez – Egyetlen kiszolgáló
description: Ez a cikk ismerteti, hogyan konfigurálhatja és érheti el a kiszolgálónaplókat (.log fájlokat) az Azure Database for PostgreSQL – Single Server rendszerben az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763691"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Az Azure Database for PostgreSQL konfigurálása és elérése – Az egykiszolgálós naplók az Azure portalról

Konfigurálhatja, listázhatja és letöltheti az [Azure Database for PostgreSQL-naplókat](concepts-server-logs.md) az Azure Portalról.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépésekhez az [Azure Database for PostgreSQL server szükséges.](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a lekérdezési naplókhoz és hibanaplókhoz való hozzáférést. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. 

   ![Képernyőkép a Kiszolgálói naplók beállításairól](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplóparaméterek konfigurálásához.**

5. Módosítsa a módosítani kívánt paramétereket. Az ezen a munkameneten végrehajtott összes módosítás lila színnel van kiemelve.

   A paraméterek módosítása után válassza a **Mentés gombot.** Vagy elvetheti a módosításokat. 

   ![Képernyőkép a Kiszolgálóparaméter-beállításokról](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

A **Kiszolgálóparaméterek** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és letöltési naplók
A naplózás megkezdése után megtekintheti az elérhető naplók listáját, és letöltheti az egyes naplófájlokat. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Az oldalsáv **Figyelés** szakaszában válassza a **Kiszolgálónaplók**lehetőséget. A lapon a naplófájlok listája látható.

   ![Képernyőkép a Kiszolgálói naplók lapról, a kiemelt naplók listájával](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója **postgresql-yyyy-mm-dd_hh0000.log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlok óránként vagy 100 MB-onként forognak, attól függően, hogy melyik következik be előbb.

4. Szükség esetén a keresőmező segítségével gyorsan leszűkítheti egy adott naplóra a dátum és az idő alapján. A keresés a napló nevén van.

   ![Képernyőkép a Kiszolgálói naplók lapról, kiemelve a keresőmezővel és a találatokkal](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Az egyes naplófájlok letöltéséhez jelölje ki a táblázatsor minden naplófájlja melletti lefelé mutató nyílikont.

   ![Képernyőkép a Kiszolgálói naplók lapról, kiemelve a lefelé mutató nyíl ikonnal](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>További lépések
- A naplók programozott letöltését az [Access server cli-ben található naplóiból](howto-configure-server-logs-using-cli.md) megtudhatja.
- További információ a [kiszolgálónaplókról](concepts-server-logs.md) az Azure Database for PostgreSQL szolgáltatásban. 
- A paraméterdefiníciókról és a PostgreSQL naplózásról a PostgreSQL [hibajelentési és naplózási dokumentációjában olvashat bővebben.](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)

