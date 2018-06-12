---
title: Konfigurálja, és hozzáférést kiszolgálónaplókban az Azure Database MySQL az Azure portálon
description: A cikkből megtudhatja, hogyan lehet konfigurálni és MySQL az Azure portálról a kiszolgálói naplók az Azure-adatbázis hozzáférését.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: eb35563bc21fc48d304f216e7b34cc9a77f35e83
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265362"
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

5. Módosítsa a paramétereket, akkor módosítania kell. Ebben a munkamenetben összes módosítások vannak kiemelve lila szín jelöli. 

   Ha módosította a paramétereket, akkor kattinthat **mentése**. Illetve **elvetése** a módosításokat.

   ![Kattintson a Mentés gombra, vagy vesse el](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. A naplók listája gombra kattintva térjen vissza a **Bezárás gomb** (X ikonra) a a **Server paraméterek** lap.

## <a name="view-list-and-download-logs"></a>Lista megtekintése és a naplók letöltése
Naplózás megkezdése után megtekintheti a naplók listáját, és töltse le a kiszolgáló naplóiban panelen külön naplófájlba. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki a MySQL-kiszolgálóhoz tartozó Azure-adatbázis.

3. Az a **figyelés** az oldalsávon, jelölje be a szakasz **kiszolgáló naplóiban**. A lap a naplófájlok listáját láthatja, látható módon:

   ![Naplók listája](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenció: **mysql - lassú – < a kiszolgáló neve >-yyyymmddhh.log**. A dátum és idő, a fájl nevében használt a, amikor a napló lett kiállítva. Naplók fájlok legyenek-e elforgatva minden 24 óra vagy 7.5 GB, amelyik előbb eléri.

4. Szükség esetén használja a **keresőmezőbe** gyorsan szűkítéséhez adott naplóba – dátum és idő alapján. A keresés be van kapcsolva a napló nevét.

5. Töltse le az egyes naplófájlokat a **letöltése** látható mellett minden naplófájl a tábla sorban (lefelé mutató nyíl ikonnal) gombra:

   ![Kattintson a Letöltés ikon](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>További lépések
- Lásd: [hozzáférést kiszolgálónaplókban olvashatók a CLI](howto-configure-server-logs-in-cli.md) megtudhatja, hogyan programozott módon a naplók letöltéséhez.
- További információ [kiszolgálónaplókban](concepts-server-logs.md) MySQL az Azure-adatbázisban. 
- A paraméter meghatározásokat és MySQL naplózási kapcsolatos további információkért tekintse meg a MySQL-dokumentáció [naplók](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

