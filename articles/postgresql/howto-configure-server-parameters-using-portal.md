---
title: Állítsa be a kiszolgáló adatait az Azure Database PostgreSQL Azure-portálon
description: Ez a cikk ismerteti, hogyan paramétereinek a konfigurálása a kiszolgáló Azure-adatbázisban a PostgreSQL az Azure portálon keresztül.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6d43cac79c19e117385549b1678a464dc5731bd7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="configure-server-parameters-in-azure-portal"></a>Kiszolgáló paramétereinek a konfigurálása Azure-portálon
Listáról, megjelenítése és konfigurációs paraméterek az Azure-adatbázis az Azure portálon keresztül PostgreSQL-kiszolgáló frissítése.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit kell:
- [Azure-adatbázis PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="viewing-and-editing-parameters"></a>Megtekintésére és szerkesztésére paraméterek
1. Nyissa meg az [Azure portált](https://portal.azure.com).

2. Válassza ki az Azure-adatbázis PostgreSQL-kiszolgáló.

3. Az a **beállítások** szakaszban jelölje be **Server paraméterek**. A lapon lévő paraméterek, az értékek és leírások listáját jeleníti meg.
![A paraméterek – áttekintés oldalra](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Válassza ki a **legördülő** gombra a client_min_messages például számba Típusparaméterek lehetséges értékei.
![Lefelé eldobási számbavétele](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Válassza ki, vagy vigye a **i** (információ) gombra a lehetséges értékek cpu_index_tuple_cost például numerikus paraméterekhez.
![információ gombjára kattintva](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Szükség esetén használja a **keresőmezőbe** adott paraméter szűkítéséhez. A művelet nevének és a paraméterek leírását.
![keresési eredmények](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Módosítsa úgy, hogy milyen paraméterértékeket. A munkamenet összes módosítások vannak kiemelve lila szín jelöli. Miután megváltoztatta az értékeket, kijelölheti a **mentése**. Illetve **elvetése** a módosításokat.
![Mentés vagy a módosítások elvetése](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Ha mentette a paraméter új értékét, mindig visszaállíthatja az alapértelmezett értékekre mindent kiválasztásával **minden, az alapértelmezett visszaállítása**.
![Minden, az alapértelmezett visszaállítása](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>További lépések
További tudnivalók:
- [A kiszolgáló paraméterek PostgreSQL az Azure-adatbázis – áttekintés](concepts-servers.md)
- [Az Azure parancssori felület használatával paraméterek konfigurálása](howto-configure-server-parameters-using-cli.md)
