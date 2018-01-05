---
title: "Konfigurálja, és el a kiszolgáló naplóiban PostgreSQL az Azure portálon |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan lehet konfigurálni és a kiszolgálói naplók az Azure-adatbázis hozzáférését az Azure portálról PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: a2f67b21293a1a0456b27cad9043be01fdd5274a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban olvashatók az Azure-portálon

Konfigurálja, listában, és töltse le a [Azure adatbázis PostgreSQL server naplók](concepts-server-logs.md) Azure-portálról.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [Azure-adatbázis PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
A lekérdezés naplók és a hibanaplókat való hozzáférés konfigurálása. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

2. Válassza ki az Azure-adatbázis PostgreSQL-kiszolgáló.

3. Az a **figyelés** az oldalsávon, jelölje be a szakasz **kiszolgáló naplóiban**. 

   ![Válassza ki a kiszolgáló naplóiban, és válassza a "Kattintson ide a engedélyezése..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Válassza ki a címsor **kattintson ide a naplók engedélyezése és konfigurálása a napló paraméterek** server paraméterek megtekintéséhez.

5. Válassza ki a **megjelenítése további** bővítő felhasználható paraméterek egy kiterjesztett listájának megtekintéséhez. 

   További információ a definíciók paraméterek dokumentációja a PostgreSQL [jelentéskészítési és naplózási hiba](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

   ![Napló paraméterek rövid listája. Kattintson a több hosszú ideig megjelenítése](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Módosítsa a paramétereket, akkor módosítania kell. Ebben a munkamenetben összes módosítások vannak kiemelve lila szín jelöli.

   Ha módosította a paramétereket, akkor kattinthat **mentése**. Illetve **elvetése** a módosításokat. 

   ![Paraméterek módosításainak mentése vagy elvetése listája túl hosszú](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. A naplók listája gombra kattintva térjen vissza a **Bezárás gomb** (X ikonra) a a **Server paraméterek** lap.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltése
Naplózás megkezdése után megtekintheti a naplók listáját, és töltse le a kiszolgáló naplóiban panelen külön naplófájlba. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki az Azure-adatbázis PostgreSQL-kiszolgáló.

3. Az a **figyelés** az oldalsávon, jelölje be a szakasz **kiszolgáló naplóiban**. A lap a naplófájlok listáját láthatja, látható módon:

   ![Naplók kiszolgálólista](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció: **postgresql-éééé-hh-dd_hh0000.log**. A dátum és idő, a fájl nevében használt a, amikor a napló lett kiállítva. A naplófájlok elforgatása minden egy óra vagy 100 MB-os méret, amelyik előbb következik be.

4. Szükség esetén használja a **keresőmezőbe** gyorsan szűkítéséhez adott naplóba – dátum és idő alapján. A keresés be van kapcsolva a napló nevét.

   ![Példa keresés a napló neve](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Töltse le az egyes naplófájlokat a **letöltése** látható mellett minden naplófájl a tábla sorban (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a Letöltés ikon](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>További lépések
- Lásd: [hozzáférést kiszolgálónaplókban olvashatók a CLI](howto-configure-server-logs-using-cli.md) megtudhatja, hogyan programozott módon a naplók letöltéséhez.
- További információ [kiszolgálónaplókban](concepts-server-logs.md) az PostgreSQL az Azure-Adatbázisba. 
- A paraméterdefiníciókra és PostgreSQL naplózási kapcsolatos további információkért tekintse meg a PostgreSQL-dokumentáció [hibajelentés és a naplózás](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

