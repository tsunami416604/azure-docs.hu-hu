---
title: Létrehozása és kezelése olvasható replikák az Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan beállítása és kezelése olvasható replikák az Azure Database for MariaDB a portál használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: eb228138118512c5c64574212910c5f16885ee94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079027"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Hogyan hozhat létre és kezelhet olvassa el replikákat, Azure Database-ben a MariaDB-hez az Azure portal használatával

Ebben a cikkben, megtudhatja, hogyan hozhat létre és kezelhet az Azure Database for MariaDB szolgáltatás az Azure portal használatával olvasható replika.

> [!IMPORTANT]
> Ugyanabban a régióban olvasható replika jelenleg nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Egy [, Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely a fölérendelt kiszolgáló lesz.

> [!IMPORTANT]
> Az olvasási replika funkció csak az általános célú és memóriahasználatra optimalizált tarifacsomagok MariaDB-kiszolgálók az Azure Database érhető el. Győződjön meg, hogy a fölérendelt kiszolgáló árképzési szintek egyikét.

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

Olvasási kiszolgálót az alábbi lépésekkel hozhatók létre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a meglévő Azure Database for MariaDB masterként használni kívánt kiszolgálót. Ez a művelet megnyitja a **áttekintése** lapot.

3. Válassza ki **replikációs** a menüben a **beállítások**.

4. Válassza ki **replika hozzáadása**.

   ![Azure Database for MariaDB - replikáció](./media/howto-read-replica-portal/add-replica.png)

5. Adja meg az adatbázisreplika-kiszolgáló nevét.

    ![Azure Database for MariaDB - replika nevét](./media/howto-read-replica-portal/replica-name.png)

6. Válassza ki **OK** megerősítéséhez, hogy a replika létrehozása.

> [!NOTE]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. A másodpéldány konfigurációjának a létrehozása után módosítható. Javasoljuk, hogy az adatbázisreplika-kiszolgáló konfigurációs kell tárolni annak érdekében, hogy a replika nem tudják tartani a főkiszolgálóval a főkiszolgáló-nál nagyobb vagy egyenlő értéken.

Az adatbázisreplika-kiszolgáló létrehozása után azt tekintheti meg a **replikációs** panelen.

   ![Azure Database for MariaDB - lista replikák](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása

> [!IMPORTANT]
> A kiszolgáló replikációjának leállítása nem vonható vissza. Ha a replikáció leállt, a master és a replika között, nem lehet visszavonni. Az adatbázisreplika-kiszolgáló ezután lesz egy önálló kiszolgáló, és már támogatja az olvasási és írási műveletek. Ez a kiszolgáló nem hajtható végre egy replika be újra.

A fő- és az Azure Portalról replikakiszolgáló közötti replikáció megszüntetéséhez használja az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő Azure Database for MariaDB-kiszolgálót. 

2. Válassza ki **replikációs** a menüben a **beállítások**.

3. Jelölje be szeretné állítani a replikáció az adatbázisreplika-kiszolgálón.

   ![Azure Database for MariaDB - leállítási replikációs kiszolgáló kiválasztása](./media/howto-read-replica-portal/stop-replication-select.png)

4. Válassza ki **replikáció leállítása**.

   ![Azure Database for MariaDB - replikáció leállítása](./media/howto-read-replica-portal/stop-replication.png)

5. Ellenőrizze, hogy meg szeretné szüntetni a replikációs kattintva **OK**.

   ![Azure Database for MariaDB - replikáció leállítása megerősítése](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Adatbázisreplika-kiszolgáló törlése

Az Azure Portalról egy olvasási adatbázisreplika-kiszolgáló törléséhez használja a következő lépéseket:

1. Az Azure Portalon válassza ki a fő Azure Database for MariaDB-kiszolgálót.

2. Válassza ki **replikációs** a menüben a **beállítások**.

3. Válassza ki, hogy törölni kívánja az adatbázisreplika-kiszolgálón.

   ![Azure Database for MariaDB - replika válassza kiszolgáló törlése](./media/howto-read-replica-portal/delete-replica-select.png)

4. Válassza ki **tárolt replika törlése**

   ![Azure Database for MariaDB - tárolt replika törlése](./media/howto-read-replica-portal/delete-replica.png)

5. Adja meg a replikát, majd kattintson a **törlése** a replika törlésének megerősítéséhez.  

   ![Azure Database for MariaDB - tárolt replika törlése megerősítése](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése az összes replika kiszolgálók replikálását, és törli magát a főkiszolgáló. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.

Az Azure Portalról egy fölérendelt kiszolgáló törléséhez használja a következő lépéseket:

1. Az Azure Portalon válassza ki a fő Azure Database for MariaDB-kiszolgálót.

2. Az a **áttekintése**válassza **törlése**.

   ![Azure Database for MariaDB - főkiszolgáló Delete](./media/howto-read-replica-portal/delete-master-overview.png)

3. Írja be a fölérendelt kiszolgáló nevét, és kattintson a **törlése** a fölérendelt kiszolgáló törlésének megerősítéséhez.  

   ![Azure Database for MariaDB - főkiszolgáló Delete](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>A figyelő replikáció

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a figyelni kívánt MariaDB-kiszolgáló a replika Azure-adatbázis.

2. Alatt a **figyelés** oldalsávon válassza szakaszában **metrikák**:

3. Válassza ki **replikációs késés másodpercben** az elérhető mérőszámok a legördülő listából.

   ![Válassza ki a replikációs késés](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi képen egy 30 perces időtartomány kijelölése.

   ![Időintervallum kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Tekintse meg a replikációs késés a kijelölt időtartományban. Az alábbi képen az elmúlt 30 percben nagy számítási jeleníti meg.

   ![Időintervallum kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [replikák olvasása](concepts-read-replicas.md)