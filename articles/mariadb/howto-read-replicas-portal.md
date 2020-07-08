---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthat be és kezelhet olvasási replikákat Azure Database for MariaDB a portál használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 703879ab2dba59327b9224781eb93ee106ae10d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707967"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MariaDB szolgáltatásban a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Database for MariaDB kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva.

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a főkiszolgáló a fenti díjszabási szintek egyikében van.

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

Az olvasási replika kiszolgáló a következő lépések segítségével hozható létre:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/).

2. Válassza ki a meglévő Azure Database for MariaDB kiszolgálót, amelyet főkiszolgálóként kíván használni. Ez a művelet megnyitja az **Áttekintés** lapot.

3. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

4. Válassza a **replika hozzáadása**lehetőséget.

   ![Azure Database for MariaDB – replikálás](./media/howto-read-replica-portal/add-replica.png)

5. Adja meg a replika kiszolgáló nevét.

    ![Azure Database for MariaDB – replika neve](./media/howto-read-replica-portal/replica-name.png)

6. Válassza ki a replika-kiszolgáló helyét. Az alapértelmezett hely megegyezik a főkiszolgálóval.

    ![Azure Database for MariaDB – replika helye](./media/howto-read-replica-portal/replica-location.png)

7. A replika létrehozásának jóváhagyásához kattintson **az OK gombra** .

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a főkiszolgálónál egyenlő vagy nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

A replika-kiszolgáló létrehozása után a **replikáció** panelről is megtekinthető.

   ![Azure Database for MariaDB – replikák listázása](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást egy fő és egy replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

Ha le szeretné állítani a replikációt egy fő és egy replika kiszolgáló között a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót. 

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelyen le szeretné állítani a replikálást.

   ![Azure Database for MariaDB – replikálás leállítása kiszolgáló kiválasztása](./media/howto-read-replica-portal/stop-replication-select.png)

4. Válassza a **replikálás leállítása**lehetőséget.

   ![Azure Database for MariaDB – replikálás leállítása](./media/howto-read-replica-portal/stop-replication.png)

5. Az **OK**gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

   ![Azure Database for MariaDB – replikálás leállítása – megerősítés](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Ha törölni szeretne egy olvasási replika kiszolgálót a Azure Portalről, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót.

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki a törölni kívánt replika-kiszolgálót.

   ![Azure Database for MariaDB – replika törlése kiszolgáló kiválasztása](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Replika törlésének** kiválasztása

   ![Azure Database for MariaDB – replika törlése](./media/howto-read-replica-portal/delete-replica.png)

5. Írja be a replika nevét, és kattintson a **Törlés** gombra a replika törlésének megerősítéséhez.  

   ![Azure Database for MariaDB – replika törlése – megerősítés](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A főkiszolgáló a Azure Portalból való törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MariaDB kiszolgálót.

2. Az **Áttekintés**területen válassza a **Törlés**lehetőséget.

   ![Azure Database for MariaDB – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-overview.png)

3. Írja be a főkiszolgáló nevét, és kattintson a **Törlés** gombra a főkiszolgáló törlésének megerősítéséhez.  

   ![Azure Database for MariaDB – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replikáció monitorozása

1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt replikát Azure Database for MariaDB-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **metrikák**elemet:

3. A rendelkezésre álló metrikák legördülő listájában válassza a **replikálás késése másodpercben** lehetőséget.

   ![Replikációs késés kiválasztása](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi képen egy 30 perces időtartomány van kiválasztva.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. A kijelölt időtartomány replikációs késésének megtekintése. Az alábbi képen egy nagy munkaterhelés esetében az elmúlt 30 perc látható.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>További lépések

- További információ az [olvasási replikáról](concepts-read-replicas.md)