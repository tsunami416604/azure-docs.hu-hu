---
title: Olvasási replikák kezelése - Azure portal – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan állíthatók be és kezelhetők az olvasási replikák a MariaDB Azure Database-ben a portál használatával
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025084"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a MariaDB Azure Database-ben az Azure Portal használatával

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat az Azure Database for MariaDB szolgáltatásban az Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- A Főkiszolgálóként használt [Azure-adatbázis a MariaDB-kiszolgálóhoz.](quickstart-create-mariadb-server-database-using-azure-portal.md)

> [!IMPORTANT]
> Az olvasási replika funkció csak az Azure Database for MariaDB-kiszolgálók az általános célú vagy a memória optimalizált tarifacsomagok hoz érhető el. Győződjön meg arról, hogy a főkiszolgáló a következő tarifacsomagok egyikében található.

## <a name="create-a-read-replica"></a>Olvasott kópia létrehozása

Az olvasási replikakiszolgáló a következő lépésekkel hozható létre:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki a meglévő Azure-adatbázis MariaDB-kiszolgáló, amely szeretné használni, mint a fő. Ez a művelet **megnyitja** az Áttekintés lapot.

3. Válassza a **Menü Replikáció parancsát** a **BEÁLLÍTÁSOK csoportban.**

4. Válassza **a Replika hozzáadása**lehetőséget.

   ![Azure Database for MariaDB – Replikáció](./media/howto-read-replica-portal/add-replica.png)

5. Adja meg a replikakiszolgáló nevét.

    ![Azure Database for MariaDB – Replika neve](./media/howto-read-replica-portal/replica-name.png)

6. Válassza ki a replikakiszolgáló helyét. Az alapértelmezett hely megegyezik a főkiszolgáló ével.

    ![Azure-adatbázis a MariaDB-hez – Replika helye](./media/howto-read-replica-portal/replica-location.png)

7. A replika létrehozásának megerősítéséhez válassza az **OK gombot.**

> [!NOTE]
> Az olvasási kópiák a főkiszolgálóval azonos kiszolgálókonfigurációval jönnek létre. A replikakiszolgáló konfigurációja a létrehozás után módosítható. Ajánlott, hogy a replikakiszolgáló konfigurációját a főkiszolgálóval azonos vagy nagyobb értékeken kell tartani annak biztosítása érdekében, hogy a replika lépést tud tartani a főkiszolgálóval.

A replikakiszolgáló létrehozása után a **replikáció panelről** tekinthető meg.

   ![Azure Database for MariaDB – Replikák listája](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>A replikakiszolgálóra irányuló replikáció leállítása

> [!IMPORTANT]
> A kiszolgálóra irányuló replikáció leállítása visszafordíthatatlan. Ha a replikáció leállt a főkiszolgáló és a replika között, azt nem lehet visszavonni. A replikakiszolgáló ezután önálló kiszolgálóvá válik, és mostantól támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható elő újra kópiává.

Ha le szeretné állítani a replikánikiszolgáló és a replikakiszolgáló közötti replikációt az Azure Portalról, kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő Azure-adatbázis MariaDB-kiszolgáló. 

2. Válassza a **Menü Replikáció parancsát** a **BEÁLLÍTÁSOK csoportban.**

3. Válassza ki azt a replikakiszolgálót, amelynek replikációját le szeretné állítani.

   ![Azure Database for MariaDB – A replikáció leállítása a kiszolgáló kiválasztása korban](./media/howto-read-replica-portal/stop-replication-select.png)

4. Válassza **a Replikáció leállítása**lehetőséget.

   ![Azure Database for MariaDB – A replikáció leállítása](./media/howto-read-replica-portal/stop-replication.png)

5. Az **OK**gombra kattintva erősítse meg a replikáció leállítását.

   ![Azure Database for MariaDB – A replikáció leállítása megerősítés](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Replikakiszolgáló törlése

Ha törölni szeretne egy olvasási replikakiszolgálót az Azure Portalról, kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő Azure-adatbázis MariaDB-kiszolgáló.

2. Válassza a **Menü Replikáció parancsát** a **BEÁLLÍTÁSOK csoportban.**

3. Jelölje ki a törölni kívánt replikakiszolgálót.

   ![Azure Database for MariaDB – Replikaválasztó kiszolgáló törlése](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Kópia törlése** lehetőséget

   ![Azure Database for MariaDB – Replika törlése](./media/howto-read-replica-portal/delete-replica.png)

5. Írja be a kópia nevét, és a kópia törlésének megerősítéséhez kattintson a **Törlés** gombra.  

   ![Azure Database for MariaDB – Replika-megerősítés törlése](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

Ha törölni szeretne egy főkiszolgálót az Azure Portalról, kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő Azure-adatbázis MariaDB-kiszolgáló.

2. Az **Áttekintés területen**válassza a **Törlés**lehetőséget.

   ![Azure-adatbázis a MariaDB-hez – Főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-overview.png)

3. Írja be a főkiszolgáló nevét, és a **törlés gombra** kattintva erősítse meg a főkiszolgáló törlését.  

   ![Azure-adatbázis a MariaDB-hez – Főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replikáció monitorozása

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a figyelni kívánt Azure Database for MariaDB-kiszolgáló replika.

2. Az oldalsáv **Figyelés** szakaszában válassza a **Metrikák**lehetőséget:

3. Válassza ki **a replikációs késést másodpercben** az elérhető metrikák legördülő listájából.

   ![Replikációs késés kiválasztása](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi kép 30 perces időtartományt jelöl ki.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. A kijelölt időtartomány replikációs késésének megtekintése. Az alábbi kép nagy munkaterhelés esetén az utolsó 30 percet jeleníti meg.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>További lépések

- További információ az [olvasott a kópiákról](concepts-read-replicas.md)