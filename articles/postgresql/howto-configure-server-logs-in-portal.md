---
title: Konfigurálja és kiszolgálói naplók elérése az Azure Portalon a PostgreSQL-hez
description: Ez a cikk ismerteti, hogyan konfigurálhat és elérése a kiszolgálónaplók, Azure database for postgresql-hez az Azure Portalról.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: be889d7b0bf6060b3b93244da6746fa6f13024da
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985505"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurálja és hozzáférés-kiszolgáló naplóit az Azure Portalon

Konfigurálhatja, listázása, és töltse le a [, Azure Database for PostgreSQL-kiszolgáló naplói](concepts-server-logs.md) az Azure Portalról.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
A lekérdezések naplói és a hibanaplók történő hozzáférés konfigurálásához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. 

   ![Válassza ki a webkiszolgáló-naplókkal, és válassza a "Ide kattintva engedélyezheti..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Válassza ki a fejléc **naplóinak engedélyezése és a naplózási paraméterek konfigurálásához kattintson ide** a kiszolgáló paramétereinek megtekintéséhez.

5. Módosítsa a paramétereket, hogy módosítani kell. Ebben a munkamenetben összes módosítás lila szín jelöli ki vannak emelve.

   Miután módosította a paramétereket, kattinthat **mentése**. Vagy beállíthatja a **elveti** a módosításokat. 

   ![A módosítások mentésére vagy elvetésére szolgáló paraméterek hosszú listája](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Kattintva térjen vissza a naplók listáját a **Bezárás gomb** (X ikon) a a **Kiszolgálóparaméterekkel** lapot.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltéséhez
Naplózás megkezdése után a naplók megtekintéséhez, és töltse le a webkiszolgáló-naplókkal panelen az egyes naplófájlok. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for PostgreSQL kiszolgálóját.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. Az oldal mutatja a naplófájlok listáját, látható módon:

   ![Kiszolgálói naplók listája](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció **postgresql-éééé-hh-dd_hh0000.log**. A dátum és idő, a fájl neve a használt az, amikor a napló lett kiállítva. A naplófájlok elforgatása minden egy óra vagy 100 MB-os mérete, amelyiket hamarabb.

4. Ha szükséges, használja a **keresőmezőbe** gyorsan szűkítéséhez egy konkrét naplófájlokból, dátum és idő alapján. A keresés el, a napló nevét.

   ![Példa keresési napló nevek alapján](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Az egyes naplófájlok használatával töltse le a **letöltése** látható módon mellett a táblázat található minden egyes naplófájl (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a letöltési ikon](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>További lépések
- Lásd: [hozzáférés a kiszolgálói naplókhoz a CLI](howto-configure-server-logs-using-cli.md) megtudhatja, hogyan programozott módon a naplók letöltéséhez.
- Tudjon meg többet [kiszolgálónaplók](concepts-server-logs.md) PostgreSQL-hez készült Azure DB-ben. 
- A paraméterdefiníciókra és a PostgreSQL-naplózás kapcsolatos további információkért tekintse meg a PostgreSQL-dokumentáció [hibajelentés és a naplózás](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

