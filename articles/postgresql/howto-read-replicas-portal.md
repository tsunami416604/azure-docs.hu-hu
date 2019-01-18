---
title: Olvasható replikák az Azure Portalon kezelése az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a kezelése, Azure Database for PostgreSQL, olvassa el a replikákat az Azure Portalon.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383527"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Hogyan hozhat létre és kezelhet, olvassa el a replikákat az Azure Portalon
Ebben a cikkben, megtudhatja, hogyan hozhat létre és kezelhet az Azure Database for PostgreSQL szolgáltatás az Azure portal használatával olvasható replika. További információ olvasható replikák [a fogalmak dokumentáció](concepts-read-replicas.md).

## <a name="prerequisites"></a>Előfeltételek
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) , amely a fölérendelt kiszolgáló lesz.

## <a name="prepare-the-master-server"></a>A fölérendelt kiszolgáló előkészítése
Ez a fő előkészítési lépés csak általános célú és memóriahasználatra optimalizált kiszolgálókra vonatkozik.

A **azure.replication_support** REPLIKA értékre kell állítani a paraméter a fölérendelt kiszolgálón. Ez a paraméter módosítása érvénybe léptetéséhez kiszolgáló újraindítását igényli.

1. Az Azure Portalon válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót, egy fő használni kívánt.

2. Válassza ki **kiszolgáló paramétereinek** a bal oldali menüből.

3. Keresse meg **azure.replication_support**.

   ![Azure Database for PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Állítsa be **azure.replication_support** replikára. **Mentés** a módosítást.

   ![Azure Database for PostgreSQL - REPLIKA, és mentse](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Mentés befejezése után kapni fog egy értesítést.

   ![Azure Database for PostgreSQL – értesítés mentése](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Indítsa újra a kiszolgálót, a módosítás alkalmazása után a rendszer menti. Lásd: [az újraindítás dokumentáció](howto-restart-server-portal.md) megtudhatja, hogyan indítsa újra a kiszolgálót.

## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika
Olvassa el a replikákat az alábbi lépésekkel hozhatók létre:
1.  Válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót, egy fő használni kívánt. 

2.  Válassza ki a replikációs beállítások alatt a menüből.

   Ha nem állított be **azure.replication_support** replikára az általános célú vagy memóriahasználatra optimalizált fő és újraindítása sikeresen megtörtént a kiszolgálón, megjelenik egy üzenet, amely tartalmazza ennek a végrehajtására. Ehhez a létrehozás folytatása előtt.

3.  Válassza a replika hozzáadása.

   ![Azure Database for PostgreSQL - replika hozzáadása](./media/howto-read-replicas-portal/add-replica.png)

4.  Adja meg az adatbázisreplika-kiszolgáló nevét, és kattintson az OK gombra a replika létrehozásának megerősítése.

   ![Azure Database for PostgreSQL - replika nevét](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> A kiszolgáló konfigurációval megegyező a fő olvasható replikák jönnek létre. Replika létrehozása után, a tarifacsomag (kivéve a, illetve onnan alapszintű), számítási generáció, a virtuális magok, a storage és a biztonsági másolat megőrzési idejének módosítható egymástól függetlenül a fölérendelt kiszolgálótól.

> [!IMPORTANT]
> Egy master kiszolgálókonfiguráció frissül az új értékekre, mielőtt a replikák konfigurációs egyenlő vagy nagyobb értékre kell frissíteni. Tegye próbál egyébként hibát jelez. Ez biztosítja, hogy a replikák meg tudják tartani a fő végzett módosításokat. 


Az adatbázisreplika-kiszolgáló létrehozása után azt a replikációs ablakból is megtekinthetők.

![Azure Database for PostgreSQL – új replika](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replikáció leállítása

> [!IMPORTANT]
> A kiszolgáló replikációjának leállítása nem vonható vissza. Ha a replikáció leállt, a master és a replika között, nem lehet visszavonni. Az adatbázisreplika-kiszolgáló ezután lesz egy önálló kiszolgáló, és már támogatja az olvasási és írási műveletek. Ez a kiszolgáló nem hajtható végre egy replika be újra.

A master és a egy replikát, az Azure Portalról közötti replikáció megszüntetéséhez használja az alábbi lépéseket:
1.  Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Válassza ki a replikációs beállítások alatt a menüből.

3.  Jelölje be szeretné állítani a replikáció az adatbázisreplika-kiszolgálón.

   ![Azure Database for postgresql-hez - válassza replika](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Válassza ki a replikáció leállítása.

   ![Azure Database for PostgreSQL - replikáció válassza leállítása](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Erősítse meg az OK gombra kattintva állítsa le a replikációt.

   ![Azure Database for PostgreSQL – győződjön meg róla replikáció leállítása](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>A minta törlése

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló törlése leállítja a replikáció az összes replika kiszolgálók. Replikakiszolgáló önálló kiszolgálók által mostantól támogatják az olvasási és írási műveletek válnak.
Ugyanazokat a lépéseket, mint egy önálló Azure Database for PostgreSQL-kiszolgáló törlése a fő követi. Kiszolgáló törlése az Azure Portalról, tegye a következőket:

1.  Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Az ismeretanyagot kattintson a törlés.

   ![Azure Database for PostgreSQL - kiszolgáló törlése](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Írja be a fölérendelt kiszolgáló nevét, és válassza a Törlés a fölérendelt kiszolgáló törlésének megerősítéséhez.

   ![Azure Database for PostgreSQL - törlésének megerősítése](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Replika törlése
Olvasási replika törlése, kövesse a lépéseket csakúgy, mint a fenti fő kiszolgáló törlése. Először nyissa meg a replika – Áttekintés lapján, majd válassza a törlés.

   ![Azure Database for PostgreSQL - tárolt replika törlése](./media/howto-read-replicas-portal/delete-replica.png)
 
Másik lehetőségként törölheti a replikációs ablakból.
1.  Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Válassza ki a replikációs beállítások alatt a menüből.

3.  Válassza ki, hogy törölni kívánja az adatbázisreplika-kiszolgálón. 

   ![Azure Database for postgresql-hez - válassza replika](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Válassza ki a tárolt replika törlése.

   ![Azure Database for postgresql-hez - válassza replika törlése](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Adja meg a replikát a, és válassza a törlés megerősítéséhez, hogy a replika törlését.

   ![Azure Database for PostgreSQL – győződjön meg róla replika törlése](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>A figyelő egy replika
### <a name="max-lag-across-replicas"></a>Maximális késés replika között
A **replikák közötti maximális késésnek** metrika a master és a legtöbb elmaradt replika közötti késés időpontot jeleníti meg. 

1.  Az Azure Portalon válassza ki a **fő** , Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Kiválaszthatja a metrikákat. A metrikák ablakban válassza ki a **Lag között replikák maximális száma**.

    ![Azure Database for PostgreSQL - figyelő maximális késésnek replika között](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Válassza ki **maximális** , az összesítést. 

### <a name="replica-lag"></a>Replika késés
A **replika Lag** metrika időpontot jeleníti meg, mert az utolsó játssza vissza a tranzakciót a replikán. Ha nem léteznek tranzakciók folyamatban lévő a master, a metrika az idő elteltével jeleníti meg.

1.  Az Azure Portalon válassza ki a **replika** , Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Kiválaszthatja a metrikákat. A metrikák ablakban válassza ki a **replika Lag**.

   ![Azure Database for PostgreSQL - figyelő replika késés](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Válassza ki **maximális** , az összesítést. 
 
## <a name="next-steps"></a>További lépések
- Tudjon meg többet [olvassa el a replikákat az Azure Database for postgresql-hez](concepts-read-replicas.md).