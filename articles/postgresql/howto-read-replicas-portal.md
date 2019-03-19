---
title: Kezelése olvasható replikák az Azure Database for postgresql-hez az Azure Portalról
description: Ismerje meg, hogyan kezelhető az Azure Database for PostgreSQL, olvassa el a replikákat az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 24a37775298d6c6b40ec49f34158fcb77f26a379
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113214"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Hozzon létre, és olvasási replikák kezelése az Azure Portalról

Ebből a cikkből megismerheti, hogyan hozhat létre és kezelése olvasható replikák az Azure Database for postgresql-hez az Azure Portalról. Olvasási replikák kapcsolatos további információkért tekintse meg a [áttekintése](concepts-read-replicas.md).

> [!IMPORTANT]
> Az olvasási replika funkció nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek
Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) kell a fölérendelt kiszolgáló.

## <a name="prepare-the-master-server"></a>A fölérendelt kiszolgáló előkészítése
Az általános célú és memóriahasználatra optimalizált szinten főkiszolgálóvá előkészítése ezeket a lépéseket kell használni.

A `azure.replication_support` paramétert állítsa **REPLIKA** a fölérendelt kiszolgálón. Amikor ez a paraméter módosul, a kiszolgáló újraindítására szükség a módosítás érvénybe léptetéséhez.

1. Az Azure Portalon válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálóhoz használandó masterként.

2. A bal oldali menüben válassza ki a **kiszolgáló paramétereinek**.

3. Keresse meg a `azure.replication_support` paraméter.

   ![Keresse meg a azure.replication_support paraméter](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Állítsa be a `azure.replication_support` paraméterérték **REPLIKA**. Válassza ki **mentése** megtartja a módosításokat.

   ![A paraméter értéke REPLIKA, és mentse a módosításokat](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. A módosítások mentése után értesítést kap:

   ![Értesítési mentése](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Indítsa újra a kiszolgálót, a módosítások alkalmazásához. Ismerje meg, hogyan indítsa újra a kiszolgálót, tekintse meg [egy Azure Database for PostgreSQL-kiszolgáló újraindítása](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót a fő kiszolgálóként használni. 

2. A kiszolgáló menü alatt **beállítások**válassza **replikációs**.

   Ha nem állított be a `azure.replication_support` paramétert **REPLIKA** az általános célú vagy Memóriaoptimalizált kiszolgáló fő és a kiszolgáló újraindul, értesítést kap. Ezeket a lépéseket a replika létrehozása előtt.

3. Válassza ki **replika hozzáadása**.

   ![A replika hozzáadása](./media/howto-read-replicas-portal/add-replica.png)

4. Adja meg az olvasási-replika nevét. Válassza ki **OK** , a replika létrehozásának megerősítése.

   ![A replika neve](./media/howto-read-replicas-portal/name-replica.png) 

A kiszolgáló ugyanazt a konfigurációt a master létrehoztak egy replikát. Replika létrehozása után több beállítások egymástól függetlenül lehet módosítani a főkiszolgálóról: számítási generáció, virtuális maggal, tárolási és biztonsági másolat megőrzési ideje. A tarifacsomag egymástól függetlenül is módosítható, vagy az alapszintű csomag kivételével.

> [!IMPORTANT]
> Egy fölérendelt kiszolgáló konfigurációs frissül az új értékekre, mielőtt frissíteni a replikát konfigurációt egyenlő vagy nagyobb értékre. Ez a művelet biztosítja, hogy a replika továbbra is a fő végzett módosítások.

Az olvasási replika létrehozása után azt tekintheti meg a **replikációs** ablakban:

![Az új replikára megtekintése a replikációs ablakban](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replikáció leállítása
Egy fölérendelt kiszolgáló és a egy olvasási replika közötti replikációt is leállíthatja.

> [!IMPORTANT]
> Miután leállította egy fölérendelt kiszolgáló és a egy olvasási replika-replikáció, nem lehet visszavonni. Az olvasási replika egy önálló kiszolgáló, amely támogatja az olvasásokat és az írásokat válik. Az önálló kiszolgáló nem hajtható végre egy replika be újra.

Egy fölérendelt kiszolgáló és az Azure Portalról olvasható replika közötti replikáció megszüntetéséhez kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2. A kiszolgáló menü alatt **beállítások**válassza **replikációs**.

3. Válassza ki az adatbázisreplika-kiszolgáló, amelyhez a replikáció leállítása.

   ![Válassza ki a replika](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza ki **replikáció leállítása**.

   ![Válassza ki a replikáció leállítása](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Válassza ki **OK** replikációleállítási.

   ![Replikációleállítási megerősítése](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése
Egy fölérendelt kiszolgáló törléséhez használhatja ugyanazokat a lépéseket, hogy egy különálló Azure Database for PostgreSQL-kiszolgáló törlése. 

> [!IMPORTANT]
> Ha töröl egy fölérendelt kiszolgáló, a replikáció az összes olvasható replika le van állítva. Az olvasási replikák önálló kiszolgálók által mostantól támogatják az olvasásokat és az írásokat válnak.

Kiszolgáló törlése az Azure Portalról, kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2. Nyissa meg a **áttekintése** a kiszolgálóhoz tartozó lapon. Válassza a **Törlés** elemet.

   ![A kiszolgáló Áttekintés lapján válassza ki a fölérendelt kiszolgáló törlése](./media/howto-read-replicas-portal/delete-server.png)
 
3. Adja meg a főkiszolgáló törlése nevét. Válassza ki **törlése** a fölérendelt kiszolgáló törlésének megerősítéséhez.

   ![Erősítse meg a főkiszolgáló törlését](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Replika törlése
Hogyan törli egy fölérendelt kiszolgáló hasonló olvasható replika törölheti.

- Az Azure Portalon nyissa meg a **áttekintése** lap olvasási replikára. Válassza a **Törlés** elemet.

   ![A replika – Áttekintés lapon válassza ki a replika törlése](./media/howto-read-replicas-portal/delete-replica.png)
 
Törölheti is a olvasható replika kapacitástervező a **replikációs** ablakban az alábbi lépéseket:

1. Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2. A kiszolgáló menü alatt **beállítások**válassza **replikációs**.

3. Válassza ki a olvasható replika törlése.

   ![Válassza ki a replika törlése](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza ki **tárolt replika törlése**.

   ![Válassza ki a replika törlése](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Adja meg a nevét, a replika törlése. Válassza ki **törlése** a replika törlésének megerősítéséhez.

   ![Győződjön meg arról, te replika törlése](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>A figyelő egy replika
Két mérőszám olvasási replikák figyelése érhetők el.

### <a name="max-lag-across-replicas-metric"></a>A metrika Lag között replikák maximális száma
A **Lag között replikák maximális száma** metrika a fölérendelt kiszolgáló és a legtöbb elmaradt replika közötti jeleníti meg a késés. 

1.  Az Azure Portalon válassza ki a fő, Azure Database for PostgreSQL-kiszolgálóhoz.

2.  Válassza a **Metrika** lehetőséget. Az a **metrikák** ablakban válassza **Lag között replikák maximális száma**.

    ![A maximális késésnek figyelése replikák](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Az a **összesítési**válassza **maximális**.


### <a name="replica-lag-metric"></a>Replika Lag metrika
A **replika Lag** metrika időpontot jeleníti meg, mert az utolsó játssza vissza a tranzakciót egy replikát. Ha nem léteznek tranzakciók folyamatban lévő a master, a metrika az idő elteltével jeleníti meg.

1. Az Azure Portalon válassza ki az Azure Database for PostgreSQL, olvassa el a replika.

2. Válassza a **Metrika** lehetőséget. Az a **metrikák** ablakban válassza **replika Lag**.

   ![A replika lag figyelése](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Az a **összesítési**válassza **maximális**. 
 
## <a name="next-steps"></a>További lépések
Tudjon meg többet [olvassa el a replikákat az Azure Database for postgresql-hez](concepts-read-replicas.md).