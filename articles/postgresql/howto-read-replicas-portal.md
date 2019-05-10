---
title: Olvasási replikák kezelése az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure Portalról
description: Megtudhatja, hogyan kezelheti olvasható replikákat, Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 87371f91d9ea1f556d0f78beebd73b8a28977b71
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510387"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Létrehozása és kezelése olvasható replikák az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure Portalról

Ebből a cikkből megismerheti, hogyan hozhat létre és kezelése olvasható replikák az Azure Database for postgresql-hez az Azure Portalról. Olvasási replikák kapcsolatos további információkért tekintse meg a [áttekintése](concepts-read-replicas.md).

> [!IMPORTANT]
> Olvasási replikát hozhat létre, mint a fölérendelt kiszolgáló ugyanabban a régióban, vagy bármely más Azure-régióban a választott. Régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.


## <a name="prerequisites"></a>Előfeltételek
Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) kell a fölérendelt kiszolgáló.

## <a name="prepare-the-master-server"></a>A fölérendelt kiszolgáló előkészítése
Az általános célú és memóriahasználatra optimalizált szinten főkiszolgálóvá előkészítése ezeket a lépéseket kell használni. A fölérendelt kiszolgáló replikáció kész azure.replication_support paraméterének beállításával. A replikációs paraméter módosul, a kiszolgáló újraindítására a módosítás érvénybe léptetéséhez szükség. Az Azure Portalon ezeket a lépéseket két vannak ágyazva egy-egy gomb, amelyet **replikációs támogatásának engedélyezése**.

1. Az Azure Portalon válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálóhoz használandó masterként.

2. A kiszolgáló oldalsávon alatt **beállítások**válassza **replikációs**.

3. Válassza ki **replikációs támogatásának engedélyezése**. 

   ![Replikációs támogatásának engedélyezése](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Erősítse meg a replikációs támogatásának engedélyezése. Ez a művelet újraindítja a főkiszolgáló. 

   ![Erősítse meg a replikációs-támogatás engedélyezése](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Két Azure portal-értesítést fog kapni, a művelet befejeződése után. Nincs a kiszolgáló paraméterének frissítésére szolgáló egy értesítést. A kiszolgáló újraindítására, a következő azonnal értesítést egy másik van.

   ![Sikeres értesítések – engedélyezése](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Frissítse az Azure portal oldalán, a replikáció eszköztár frissíteni. Mostantól létrehozhat olvasható replikák ehhez a kiszolgálóhoz.

   ![Frissített eszköztár](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Replikációs támogatásának engedélyezése a műveletet egyszer kell egy fő kiszolgálón. A **replikációs támogatásának letiltása** gomb áll a kényelmi célokat szolgál. Nem ajánlott Replikációs támogatja, ha nem biztos a fő kiszolgálón soha nem fog létrehozni egy replikát. Replikációs támogatása nem tiltható le, míg a fölérendelt kiszolgáló a meglévő replikákat.


## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót a fő kiszolgálóként használni. 

2. A kiszolgáló oldalsávon alatt **beállítások**válassza **replikációs**.

3. Válassza ki **replika hozzáadása**.

   ![A replika hozzáadása](./media/howto-read-replicas-portal/add-replica.png)

4. Adja meg az olvasási-replika nevét. 

    ![A replika neve](./media/howto-read-replicas-portal/name-replica.png)

5. Válasszon egy helyet a replika számára. Létrehozhat egy replikát bármelyik Azure-régióban. Az alapértelmezett hely a ugyanaz, mint a főkiszolgáló.

    ![Válasszon ki egy helyet](./media/howto-read-replicas-portal/location-replica.png)

6. Válassza ki **OK** , a replika létrehozásának megerősítése.

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