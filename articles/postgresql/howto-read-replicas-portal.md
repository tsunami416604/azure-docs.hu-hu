---
title: Azure Database for PostgreSQL – egyetlen kiszolgáló olvasási replikáinak kezelése a Azure Portal
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL – egyetlen kiszolgáló a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d0f29e2c01d6295935ac56cb19c37e1ad6bbd21b
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907385"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL a Azure Portalból. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg [](concepts-read-replicas.md)az áttekintést.

> [!IMPORTANT]
> Létrehozhat egy olvasási replikát a főkiszolgálóval megegyező régióban, vagy bármely más, az Ön által választott Azure-régióban is. A régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.


## <a name="prerequisites"></a>Előfeltételek
Egy [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-database-portal.md) a főkiszolgálóként.

## <a name="prepare-the-master-server"></a>A fő kiszolgáló előkészítése
Ezeket a lépéseket kell használni a főkiszolgáló előkészítéséhez a általános célú vagy a memória optimalizált szintjein. Az Azure. replication_support paraméter beállításával a főkiszolgáló készen áll a replikálásra. Ha a replikációs paraméter módosul, a módosítás érvénybe léptetéséhez újra kell indítani a kiszolgálót. A Azure Portal a két lépést egyetlen gombnyomással ágyazza be, **engedélyezze a replikáció támogatását**.

1. A Azure Portal válassza ki a meglévő Azure Database for PostgreSQL-kiszolgálót, amelyet főkiszolgálóként kíván használni.

2. A kiszolgáló oldalsávjának **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza a **replikáció támogatásának engedélyezése**lehetőséget. 

   ![Replikáció támogatásának engedélyezése](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Erősítse meg, hogy szeretné engedélyezni a replikáció támogatását. Ez a művelet újraindítja a főkiszolgálót. 

   ![Replikációs támogatás engedélyezésének megerősítése](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. A művelet befejezését követően két Azure Portal értesítést fog kapni. A kiszolgálói paraméter frissítése egyetlen értesítéssel történik. A kiszolgáló azonnali újraindítására vonatkozóan egy másik értesítés is van, amely azonnal következik.

   ![Sikeres értesítések – engedélyezés](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Frissítse a Azure Portal lapot a replikálási eszköztár frissítéséhez. Most már létrehozhat olvasási replikákat ehhez a kiszolgálóhoz.

   ![Frissített eszköztár](./media/howto-read-replicas-portal/updated-toolbar.png)
   
A replikáció támogatásának engedélyezése a főkiszolgálón egyszeri művelet. Az Ön kényelme érdekében meg kell adni a **replikálási támogatás letiltása** gombot. Nem javasoljuk a replikálás támogatásának letiltását, hacsak nem biztos benne, hogy soha nem fog replikát létrehozni ezen a főkiszolgálón. A replikálás támogatása nem tiltható le, amíg a főkiszolgáló meglévő replikákkal rendelkezik.


## <a name="create-a-read-replica"></a>Hozzon létre egy olvasható replika
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki azt a meglévő Azure Database for PostgreSQL kiszolgálót, amelyet főkiszolgálóként kíván használni. 

2. A kiszolgáló oldalsávjának **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza a **replika hozzáadása**lehetőséget.

   ![Replika hozzáadása](./media/howto-read-replicas-portal/add-replica.png)

4. Adja meg az olvasási replika nevét. 

    ![A replika neve](./media/howto-read-replicas-portal/name-replica.png)

5. Válassza ki a replika helyét. Az alapértelmezett hely megegyezik a főkiszolgálóval.

    ![Válasszon ki egy helyet](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

6. A replika létrehozásának megerősítéséhez kattintson **az OK gombra** .

A replika ugyanazokkal a számítási és tárolási beállításokkal jön létre, mint a főkiszolgáló. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentési megőrzési időszak. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> A főkiszolgálói beállítás új értékre való frissítése előtt frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

Az olvasási replika létrehozása után a replikálási ablakból lehet megtekinteni :

![Az új replika megtekintése a replikálási ablakban](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja a replikációt egy főkiszolgáló és egy olvasási replika között.

> [!IMPORTANT]
> Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

Ha le szeretné állítani a főkiszolgáló és egy olvasási replika közötti replikációt a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló menü **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelynél le szeretné állítani a replikálást.

   ![A replika kijelölése](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza a **replikálás leállítása**lehetőséget.

   ![Válassza a replikáció leállítása lehetőséget](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. A replikáció leállításához kattintson **az OK gombra** .

   ![A replikáció leállításának megerősítése](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Egy fölérendelt kiszolgáló törlése
A főkiszolgálók törléséhez ugyanazokat a lépéseket kell használni, mint az önálló Azure Database for PostgreSQL-kiszolgáló törléséhez. 

> [!IMPORTANT]
> Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

Ha törölni szeretne egy kiszolgálót a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. Nyissa meg a kiszolgáló **Áttekintés** lapját. Válassza a **Törlés** elemet.

   ![A kiszolgáló áttekintése lapon válassza a főkiszolgáló törlését](./media/howto-read-replicas-portal/delete-server.png)
 
3. Adja meg a törlendő főkiszolgáló nevét. A főkiszolgáló törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

   ![A fő kiszolgáló törlésének megerősítése](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Replika törlése
A főkiszolgálók törléséhez hasonlóan törölhet egy olvasási replikát is.

- A Azure Portalban nyissa meg az olvasási replika **Áttekintés** lapját. Válassza a **Törlés** elemet.

   ![A replika áttekintése lapon válassza a (z) lehetőséget a replika törléséhez.](./media/howto-read-replicas-portal/delete-replica.png)
 
Az olvasási replikát a replikálási ablakból is törölheti a következő lépések végrehajtásával:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló menü **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza ki a törlendő olvasási replikát.

   ![Válassza ki a törölni kívánt replikát](./media/howto-read-replicas-portal/select-replica.png)
 
4. Válassza a **replika törlése**lehetőséget.

   ![Replika törlésének kiválasztása](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Adja meg a törlendő replika nevét. A replika törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

   ![A te replika törlésének megerősítése](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Replika figyelése
Az olvasási replikák figyelésére két metrika áll rendelkezésre.

### <a name="max-lag-across-replicas-metric"></a>Maximális késés a replikák metrikája között
A **replikák közötti maximális késés** a főkiszolgáló és a legnagyobb késleltetésű replika közötti késést mutatja bájtban. 

1.  A Azure Portal válassza ki a Master Azure Database for PostgreSQL-kiszolgálót.

2.  Válassza a **Metrika** lehetőséget. A **metrikák** ablakban válassza a **maximális késés a replikák között**lehetőséget.

    ![A replikák közötti maximális késés figyelése](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Az **összesítéshez**válassza a **Max**lehetőséget.


### <a name="replica-lag-metric"></a>Replika késésének mérőszáma
A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt jeleníti meg a replikán. Ha nincs tranzakció a főkiszolgálón, a metrika ezt az időbeli késést tükrözi.

1. A Azure Portal válassza ki a Azure Database for PostgreSQL olvasási replikát.

2. Válassza a **Metrika** lehetőséget. A **metrikák** ablakban válassza a **replika késése**elemet.

   ![A replika késésének figyelése](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Az **összesítéshez**válassza a **Max**lehetőséget. 
 
## <a name="next-steps"></a>További lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI-ben](howto-read-replicas-cli.md).