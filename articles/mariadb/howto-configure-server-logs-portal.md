---
title: Konfigurálja és hozzáférési kiszolgálói naplók az Azure Database for MariaDB az Azure Portalon
description: Ez a cikk azt ismerteti, konfigurálása és elérése a kiszolgálónaplók, Azure Database-ben az Azure Portalról a MariaDB-hez.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0784dffc48aeddc854aa540cba24f1834cd41827
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952834"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurálja és hozzáférés-kiszolgáló naplóit az Azure Portalon

Konfigurálhatja, listázása, és töltse le a [, Azure Database for MariaDB kiszolgálónaplók](concepts-server-logs.md) az Azure Portalról.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
A lassú lekérdezések naplója történő hozzáférés konfigurálásához. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

2. Válassza ki az Azure Database for MariaDB-kiszolgáló.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. 
   ![Válassza ki a kiszolgálónaplók, kattintson ide a konfiguráláshoz](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Válassza ki a fejléc **naplóinak engedélyezése és a naplózási paraméterek konfigurálásához kattintson ide** a kiszolgáló paramétereinek megtekintéséhez.

5. Módosítsa a paramétereket, ki kell kikapcsolását a "slow_query_log" "Be" értékre. Ebben a munkamenetben összes módosítás lila szín jelöli ki vannak emelve. 

   Miután módosította a paramétereket, kattinthat **mentése**. Vagy beállíthatja a **elveti** a módosításokat.

   ![Kattintson a Mentés gombra, vagy vesse el](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Kattintva térjen vissza a naplók listáját a **Bezárás gomb** (X ikon) a a **Kiszolgálóparaméterekkel** lapot.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltéséhez
Naplózás megkezdése után a naplók megtekintéséhez, és töltse le a webkiszolgáló-naplókkal panelen az egyes naplófájlok. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for MariaDB-kiszolgáló.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. Az oldal mutatja a naplófájlok listáját, látható módon:

   ![Naplók listája](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció **mysql – lassú – < a kiszolgáló neve >-yyyymmddhh.log**. A dátum és idő, a fájl neve a használt az, amikor a napló lett kiállítva. Naplók fájlok vannak-e forgatni minden éjjel vagy 7,5 GB, amelyik először bekövetkezik.

4. Ha szükséges, használja a **keresőmezőbe** gyorsan szűkítéséhez egy konkrét naplófájlokból, dátum és idő alapján. A keresés el, a napló nevét.

5. Az egyes naplófájlok használatával töltse le a **letöltése** látható módon mellett a táblázat található minden egyes naplófájl (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a letöltési ikon](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [kiszolgálónaplók](concepts-server-logs.md) MariaDB-hez készült Azure Database-ben.
- A paraméterdefiníciókra és naplózási kapcsolatos további információkért tekintse meg a MariaDB dokumentáció [naplók](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!-- - See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->