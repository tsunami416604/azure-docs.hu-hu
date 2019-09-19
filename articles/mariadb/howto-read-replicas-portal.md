---
title: Olvasási replikák létrehozása és kezelése a Azure Database for MariaDBban
description: Ez a cikk bemutatja, hogyan állíthat be és kezelhet olvasási replikákat Azure Database for MariaDB a portál használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: cceb1814089436efe4d4f9352f40f24d6eae790d
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123593"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva.

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg, hogy a fölérendelt kiszolgáló árképzési szintek egyikét.

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika

Az olvasási replika kiszolgáló a következő lépések segítségével hozható létre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a meglévő Azure Database for MariaDB kiszolgálót, amelyet főkiszolgálóként kíván használni. Ez a művelet megnyitja a **áttekintése** lapot.

3. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

4. Válassza a **replika hozzáadása**lehetőséget.

   ![Azure Database for MariaDB – replikálás](./media/howto-read-replica-portal/add-replica.png)

5. Adja meg a replika kiszolgáló nevét.

    ![Azure Database for MariaDB – replika neve](./media/howto-read-replica-portal/replica-name.png)

6. Válassza ki a replika-kiszolgáló helyét. Az alapértelmezett hely megegyezik a főkiszolgálóval.

    ![Azure Database for MariaDB – replika helye](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > A régiók közötti replikáció előzetes verzióban érhető el. Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

7. A replika létrehozásának jóváhagyásához kattintson **az OK gombra** .

> [!NOTE]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. A másodpéldány konfigurációjának a létrehozása után módosítható. Javasoljuk, hogy az adatbázisreplika-kiszolgáló konfigurációs kell tárolni annak érdekében, hogy a replika nem tudják tartani a főkiszolgálóval a főkiszolgáló-nál nagyobb vagy egyenlő értéken.

A replika-kiszolgáló létrehozása után a **replikáció** panelről is megtekinthető.

   ![Azure Database for MariaDB – replikák listázása](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Az adatbázisreplika-kiszolgáló replikáció leállítása

> [!IMPORTANT]
> A kiszolgáló replikációjának leállítása nem vonható vissza. Ha a replikáció leállt, a master és a replika között, nem lehet visszavonni. Az adatbázisreplika-kiszolgáló ezután lesz egy önálló kiszolgáló, és már támogatja az olvasási és írási műveletek. Ez a kiszolgáló nem hajtható végre egy replika be újra.

Ha le szeretné állítani a replikációt egy fő és egy replika kiszolgáló között a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót. 

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelyen le szeretné állítani a replikálást.

   ![Azure Database for MariaDB – replikálás leállítása kiszolgáló kiválasztása](./media/howto-read-replica-portal/stop-replication-select.png)

4. Válassza a **replikálás leállítása**lehetőséget.

   ![Azure Database for MariaDB – replikálás leállítása](./media/howto-read-replica-portal/stop-replication.png)

5. Az **OK**gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

   ![Azure Database for MariaDB – replikálás leállítása – megerősítés](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Adatbázisreplika-kiszolgáló törlése

Ha törölni szeretne egy olvasási replika kiszolgálót a Azure Portalről, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót.

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki a törölni kívánt replika-kiszolgálót.

   ![Azure Database for MariaDB – replika törlése kiszolgáló kiválasztása](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Replika törlésének** kiválasztása

   ![Azure Database for MariaDB – replika törlése](./media/howto-read-replica-portal/delete-replica.png)

5. Írja be a replika nevét, és kattintson a **Törlés** gombra a replika törlésének megerősítéséhez.  

   ![Azure Database for MariaDB – replika törlése – megerősítés](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése az összes replika kiszolgálók replikálását, és törli magát a főkiszolgáló. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.

A főkiszolgáló a Azure Portalból való törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót.

2. Az **Áttekintés**területen válassza a **Törlés**lehetőséget.

   ![Azure Database for MariaDB – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-overview.png)

3. Írja be a főkiszolgáló nevét, és kattintson a **Törlés** gombra a főkiszolgáló törlésének megerősítéséhez.  

   ![Azure Database for MariaDB – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replikáció figyelése

1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt replikát Azure Database for MariaDB-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a metrikák elemet:

3. A rendelkezésre álló metrikák legördülő listájában válassza a **replikálás késése másodpercben** lehetőséget.

   ![Replikációs késés kiválasztása](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi képen egy 30 perces időtartomány van kiválasztva.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. A kijelölt időtartomány replikációs késésének megtekintése. Az alábbi képen egy nagy munkaterhelés esetében az elmúlt 30 perc látható.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [replikák olvasása](concepts-read-replicas.md)