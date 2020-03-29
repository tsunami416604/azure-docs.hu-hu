---
title: Olvasási replikák kezelése - Azure portal - Azure Database for PostgreSQL - Single Server
description: Ismerje meg, hogyan kezelheti az Azure Database for PostgreSQL – Single Server replikák olvasásának kezelését az Azure Portalról.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768951"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése az Azure Database for PostgreSQL -Single Server szolgáltatásban az Azure portalról

Ebben a cikkben megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat az Azure Database for PostgreSQL-ben az Azure Portalon. Ha többet szeretne megtudni az olvasott replikákról, olvassa el az [áttekintést.](concepts-read-replicas.md)


## <a name="prerequisites"></a>Előfeltételek
Egy [Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-portal.md) a fő kiszolgáló.

## <a name="prepare-the-master-server"></a>A főkiszolgáló előkészítése
Ezeket a lépéseket kell használni, hogy készítsen egy fő kiszolgáló az általános célú vagy memória optimalizált szintek. A főkiszolgáló az azure.replication_support paraméter beállításával előkészíti a replikációt. A replikációs paraméter módosításakor a módosítás érvénybe léptetéséhez kiszolgáló-újraindításra van szükség. Az Azure Portalon ezt a két lépést egyetlen gomb, a **Replikáció támogatásának engedélyezése**foglalja magában.

1. Az Azure Portalon válassza ki a meglévő Azure Database for PostgreSQL-kiszolgáló használni, mint egy fő.

2. A kiszolgáló oldalsávján a **BEÁLLÍTÁSOK**csoportban válassza a **Replikáció**lehetőséget.

> [!NOTE] 
> Ha a **Replikáció simára című témakör** szürkén jelenik meg, a replikációs beállítások alapértelmezés szerint már be vannak állítva a kiszolgálón. Kihagyhatja a következő lépéseket, és létrehozhat egy olvasott replika. 

3. Válassza **a Replikációtámogatás engedélyezése**lehetőséget. 

   ![Replikációs támogatás engedélyezése](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Ellenőrizze, hogy engedélyezni szeretné-e a replikáció támogatását. Ez a művelet újraindítja a főkiszolgálót. 

   ![Replikációs támogatás engedélyezése](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. A művelet befejezése után két Azure Portal-értesítést fog kapni. A kiszolgálóparaméter frissítéséhez egy értesítés tartozik. Van egy másik értesítés a kiszolgáló újraindításáról, amely azonnal következik.

   ![Sikerértesítések - engedélyezés](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Frissítse az Azure Portal lapot a Replikáció eszköztár frissítéséhez. Most már létrehozhat olvasási replikákat ehhez a kiszolgálóhoz.

   ![Frissített eszköztár](./media/howto-read-replicas-portal/updated-toolbar.png)
   
A replikációs támogatás engedélyezése főkiszolgálónként egyszeri művelet. A **replikáció támogatásának letiltása** gomb az Ön kényelmét szolgálja. Nem javasoljuk a replikációs támogatás letiltását, hacsak nem biztos abban, hogy soha nem hoz létre kópiát ezen a főkiszolgálón. A replikációs támogatás nem tiltható le, amíg a főkiszolgáló meglévő replikákkal rendelkezik.


## <a name="create-a-read-replica"></a>Olvasott kópia létrehozása
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki a postgreSQL-kiszolgáló meglévő Azure-adatbázisát főkiszolgálóként. 

2. A kiszolgáló oldalsávján a **BEÁLLÍTÁSOK**csoportban válassza a **Replikáció**lehetőséget.

3. Válassza **a Replika hozzáadása**lehetőséget.

   ![Kópia hozzáadása](./media/howto-read-replicas-portal/add-replica.png)

4. Adja meg az olvasott replika nevét. 

    ![A kópia elnevezése](./media/howto-read-replicas-portal/name-replica.png)

5. Válassza ki a kópia helyét. Az alapértelmezett hely megegyezik a főkiszolgáló ével.

    ![Hely kiválasztása](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre kópiát, olvassa el a [replikaolvasással kapcsolatos útmutatóról szóló cikket.](concepts-read-replicas.md) 

6. A kópia létrehozásának megerősítéséhez válassza az **OK gombot.**

A kópia a főkiszolgálóval azonos számítási és tárolási beállításokkal jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági másolat megőrzési időszak. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.

> [!IMPORTANT]
> Mielőtt a főkiszolgáló-beállítás új értékre frissülne, frissítse a replikabeállítást azonos vagy nagyobb értékre. Ez a művelet segít a kópiának lépést tartani a főkiszolgálón végzett módosításokkal.

Az olvasott kópia létrehozása után a **replikációs** ablakból tekinthető meg:

![Az új kópia megtekintése a Replikáció ablakban](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replikáció leállítása
A főkiszolgáló és az olvasási kópia közötti replikáció leállíthatja.

> [!IMPORTANT]
> Miután leállította a replikációt a főkiszolgálóra és az olvasási replikára, azt nem lehet visszavonni. Az olvasási replika önálló kiszolgálóvá válik, amely támogatja az olvasást és az írást. Az önálló kiszolgáló nem alakítható újra kópiává.

A főkiszolgáló és az Azure Portalról származó olvasási replika közötti replikáció leállításához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon válassza ki a master Azure Database for PostgreSQL-kiszolgáló.

2. A kiszolgáló menü **Beállítások**csoportjában válassza a **Replikáció**lehetőséget.

3. Válassza ki azt a replikakiszolgálót, amelynek replikációját le szeretné állítani.

   ![A kópia kijelölése](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza **a Replikáció leállítása**lehetőséget.

   ![A leállítási replikáció kiválasztása](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. A replikáció leállításához válassza az **OK gombot.**

   ![A replikáció leállításának megerősítése](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Főkiszolgáló törlése
A főkiszolgáló törléséhez ugyanazokat a lépéseket kell használnia, mint egy önálló Azure-adatbázis törléséhez a PostgreSQL-kiszolgálóhoz. 

> [!IMPORTANT]
> Főkiszolgáló törlésekor az összes olvasási replikára történő replikáció leáll. Az olvasási replikák önálló kiszolgálókká válnak, amelyek mostantól támogatják az olvasást és az írást is.

Kiszolgáló törléséhez az Azure Portalról kövesse az alábbi lépéseket:

1. Az Azure Portalon válassza ki a master Azure Database for PostgreSQL-kiszolgáló.

2. Nyissa **meg** a kiszolgáló Áttekintés lapját. Válassza a **Törlés** elemet.

   ![A kiszolgáló áttekintése lapon válassza a főkiszolgáló törlését.](./media/howto-read-replicas-portal/delete-server.png)
 
3. Adja meg a törölni kívánt főkiszolgáló nevét. A **főkiszolgáló** törlésének megerősítéséhez válassza a Törlés lehetőséget.

   ![A főkiszolgáló törlésének megerősítése](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Replika törlése
A főkiszolgáló törléséhez hasonló olvasott kópiát törölhet.

- Az Azure Portalon nyissa meg az olvasási replika **áttekintése** lapot. Válassza a **Törlés** elemet.

   ![A kópia áttekintése lapon válassza a kópia törlését](./media/howto-read-replicas-portal/delete-replica.png)
 
Az olvasási kópiát a Replikáció ablakból az alábbi lépések végrehajtásával is **törölheti:**

1. Az Azure Portalon válassza ki a master Azure Database for PostgreSQL-kiszolgáló.

2. A kiszolgáló menü **Beállítások**csoportjában válassza a **Replikáció**lehetőséget.

3. Jelölje ki a törölni kívánt olvasott kópiát.

   ![A törölni kívánt kópia kijelölése](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza **a Replika törlése**lehetőséget.

   ![Kópia törlése jelölése](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Adja meg a törlendő kópia nevét. A **kópia** törlésének megerősítéséhez válassza a Törlés lehetőséget.

   ![A te replika törlésének megerősítése](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Replika figyelése
Két metrikák érhetők el az olvasási replikák figyelésére.

### <a name="max-lag-across-replicas-metric"></a>Maximális késés a replikák között metrika
A **Max Lag Across Replicas metrika** a fő kiszolgáló és a legelmaradottabb replika közötti késést jeleníti meg bájtban. 

1.  Az Azure Portalon válassza ki a postgreSQL-kiszolgáló fő Azure-adatbázisát.

2.  Válassza a **Metrika** lehetőséget. A **Metrikák ablakban** válassza a **Kópiák maximális késésének**maximális eltérése lehetőséget.

    ![A replikák maximális késésének figyelése](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Az **összesítéshez**válassza a **Max**lehetőséget.


### <a name="replica-lag-metric"></a>Replika késés metrikája
A **Replika késés** metria a replika utolsó visszajátszott tranzakciója óta eltelt időt mutatja. Ha nincsenek tranzakciók a mesteren, a metrika ezt az időeltolódást tükrözi.

1. Az Azure Portalon válassza ki az Azure Database for PostgreSQL olvasási replika.

2. Válassza a **Metrika** lehetőséget. A **Metrikák ablakban** válassza a **Replikaka késés**e lehetőséget.

   ![A replika késésének figyelése](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Az **összesítéshez**válassza a **Max**lehetőséget. 
 
## <a name="next-steps"></a>További lépések
* További információ a [kópiák olvasásáról az Azure Database for PostgreSQL-ben.](concepts-read-replicas.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és REST API-ban.](howto-read-replicas-cli.md)