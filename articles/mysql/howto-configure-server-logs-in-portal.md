---
title: Konfigurálja és eléréséhez lassú lekérdezések naplóinak az Azure Database for MySQL-hez az Azure Portalon
description: Ez a cikk ismerteti, hogyan konfigurálhat és elérni a lassú naplók az Azure Database MySQL-hez az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b16ac525d41eb2423828a647fdb75fd3f4a80a31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052719"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Konfigurálja, és hozzáférést a lassú lekérdezések naplózza az Azure Portalon

Konfigurálhatja, listázása, és töltse le a [, Azure Database for MySQL lassú lekérdezések naplóinak](concepts-server-logs.md) az Azure Portalról.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
A MySQL lassú lekérdezések naplója történő hozzáférés konfigurálásához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. 
   ![Válassza ki a kiszolgálónaplók, kattintson ide a konfiguráláshoz](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Válassza ki a fejléc **naplóinak engedélyezése és a naplózási paraméterek konfigurálásához kattintson ide** a kiszolgáló paramétereinek megtekintéséhez.

5. Módosítsa a paramétereket, hogy módosítani kell. Ebben a munkamenetben összes módosítás lila szín jelöli ki vannak emelve. 

   Miután módosította a paramétereket, kattinthat **mentése**. Vagy beállíthatja a **elveti** a módosításokat.

   ![Kattintson a Mentés gombra, vagy vesse el](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Kattintva térjen vissza a naplók listáját a **Bezárás gomb** (X ikon) a a **Kiszolgálóparaméterekkel** lapot.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltéséhez
Naplózás megkezdése után a rendelkezésre álló lassú lekérdezések naplóinak listájának megtekintéséhez, és töltse le a webkiszolgáló-naplókkal panelen az egyes naplófájlok.

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure Database for MySQL-kiszolgálóhoz.

3. Alatt a **figyelés** szakasz az oldalsávon válassza **kiszolgálónaplók**. Az oldal mutatja a naplófájlok listáját, látható módon:

   ![Naplók listája](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció **mysql – lassú – < a kiszolgáló neve >-yyyymmddhh.log**. A dátum és idő, a fájl neve a használt az, amikor a napló lett kiállítva. Naplók fájlok vannak-e forgatni minden éjjel vagy 7,5 GB, amelyik először bekövetkezik.

4. Ha szükséges, használja a **keresőmezőbe** gyorsan szűkítéséhez egy konkrét naplófájlokból, dátum és idő alapján. A keresés el, a napló nevét.

5. Az egyes naplófájlok használatával töltse le a **letöltése** látható módon mellett a táblázat található minden egyes naplófájl (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a letöltési ikon](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="next-steps"></a>További lépések
- Lásd: [hozzáférést a lassú lekérdezések naplózza a CLI-ben](howto-configure-server-logs-in-cli.md) megtudhatja, hogyan töltheti le a lassú lekérdezések naplóinak programozott módon.
- Tudjon meg többet [lassú lekérdezések naplóinak](concepts-server-logs.md) az Azure Database for MySQL-hez.
- A paraméterdefiníciókra és a MySQL-naplózás kapcsolatos további információkért tekintse meg a MySQL dokumentációja [naplók](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).