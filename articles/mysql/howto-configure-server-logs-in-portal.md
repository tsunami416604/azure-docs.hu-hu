---
title: "Konfigurálja és Azure-adatbázis el a kiszolgáló naplóiban MySQL az Azure portálon |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan lehet konfigurálni és MySQL az Azure portálról a kiszolgálói naplók az Azure-adatbázis hozzáférését."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 89674c133b458c16fbdacd771be24830624dde7c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurálja, és hozzáférést kiszolgálónaplókban olvashatók az Azure-portálon

Konfigurálja, listában, és töltse le a [Azure-adatbázis a MySQL-kiszolgáló naplók](concepts-server-logs.md) Azure-portálról.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- [MySQL-kiszolgálóhoz tartozó Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Naplózás konfigurálása
A MySQL lassú lekérdezési naplóba való hozzáférés konfigurálása. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

2. Válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

3. Az a **figyelés** az oldalsávon, jelölje be a szakasz **kiszolgáló naplóiban**. 
   ![Jelölje be kiszolgálónaplókban, kattintson konfigurálása](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Válassza ki a címsor **kattintson ide a naplók engedélyezése és konfigurálása a napló paraméterek** server paraméterek megtekintéséhez.

5. Válassza ki a **megjelenítése további** bővítő felhasználható paraméterek egy kiterjesztett listájának megtekintéséhez. 

   További információ a definíciók paraméterek dokumentációja a MySQL [naplók](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

   ![Kattintson a megjelenítés több hosszabb lista esetén](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Módosítsa a paramétereket, akkor módosítania kell. Ebben a munkamenetben összes módosítások vannak kiemelve lila szín jelöli. 

   Ha módosította a paramétereket, akkor kattinthat **mentése**. Illetve **elvetése** a módosításokat.

   ![Kattintson a Mentés gombra, vagy vesse el](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. A naplók listája gombra kattintva térjen vissza a **Bezárás gomb** (X ikonra) a a **Server paraméterek** lap.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltése
Naplózás megkezdése után megtekintheti a naplók listáját, és töltse le a kiszolgáló naplóiban panelen külön naplófájlba. 

1. Nyissa meg az Azure-portálon.

2. Válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

3. Az a **figyelés** az oldalsávon, jelölje be a szakasz **kiszolgáló naplóiban**. A lap a naplófájlok listáját láthatja, látható módon:

   ![Naplók listája](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció: **mysql - lassú – < a kiszolgáló neve >-yyyymmddhh.log**. A dátum és idő, a fájl nevében használt a, amikor a napló lett kiállítva. Naplók fájlok legyenek-e elforgatva minden 24 óra vagy 7.5 GB, amelyik előbb eléri.

4. Szükség esetén használja a **keresőmezőbe** gyorsan szűkítéséhez adott naplóba – dátum és idő alapján. A keresés be van kapcsolva a napló nevét.

5. Töltse le az egyes naplófájlokat a **letöltése** látható mellett minden naplófájl a tábla sorban (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a Letöltés ikon](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Következő lépések
- Lásd: [hozzáférést kiszolgálónaplókban olvashatók a CLI](howto-configure-server-logs-in-cli.md) megtudhatja, hogyan programozott módon a naplók letöltéséhez.
- További információ [kiszolgálónaplókban](concepts-server-logs.md) MySQL az Azure-adatbázisban. 
- A paraméter meghatározásokat és MySQL naplózási kapcsolatos további információkért tekintse meg a MySQL-dokumentáció [naplók](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

