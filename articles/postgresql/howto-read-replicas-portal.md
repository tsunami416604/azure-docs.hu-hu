---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL – egyetlen kiszolgáló a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 623b9c1eccefe5d7e6027ddbed61c89720d98e9a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884481"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL a Azure Portalból. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).


## <a name="prerequisites"></a>Előfeltételek
Egy [Azure Database for PostgreSQL kiszolgáló](quickstart-create-server-database-portal.md) a főkiszolgálóként.

## <a name="azure-replication-support"></a>Azure-beli replikálás támogatása

Az [olvasási replikák](concepts-read-replicas.md) és a [logikai dekódolás](concepts-logical.md) a postgres írási előre megadott naplójától (Wal) függ. Ehhez a két szolgáltatáshoz különböző naplózási szintek szükségesek a postgres. A logikai dekódolás magasabb szintű naplózást igényel, mint az olvasási replikák.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-beli replikáció támogatásának három beállítási lehetősége van:

* **Off** – a legkevesebb információt helyezi el a Wal-ben. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – részletesebb, mint a **kikapcsolás**. Az [olvasási replikák](concepts-read-replicas.md) működéséhez szükséges naplózás minimális szintje. Ez a beállítás az alapértelmezett a legtöbb kiszolgálón.
* **Logikai** – részletesebb, mint a **replika**. Ez a minimális szintű naplózás a logikai dekódolás működéséhez. Ebben a beállításban az olvasási replikák is működnek.

A kiszolgálót újra kell indítani a paraméter módosítása után. Belsőleg ez a paraméter a és a postgres paramétereket állítja be `wal_level` `max_replication_slots` `max_wal_senders` .

## <a name="prepare-the-master-server"></a>A fő kiszolgáló előkészítése

1. A Azure Portal válasszon ki egy meglévő Azure Database for PostgreSQL-kiszolgálót, amelyet főkiszolgálóként kíván használni.

2. A kiszolgáló menüjében válassza a **replikálás**lehetőséget. Ha az Azure-replikáció támogatásának értéke legalább **replika**, létrehozhat olvasási replikákat. 

3. Ha az Azure-beli replikálás támogatása nem a legalább **replika**értékre van beállítva, állítsa be. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL – replikálás – replika beállítása és mentés":::

4. A módosítás alkalmazásához indítsa újra a kiszolgálót az **Igen**lehetőség kiválasztásával.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL – replikálás – újraindítás megerősítése":::

5. A művelet befejezését követően két Azure Portal értesítést fog kapni. A kiszolgálói paraméter frissítése egyetlen értesítéssel történik. A kiszolgáló azonnali újraindítására vonatkozóan egy másik értesítés is van, amely azonnal következik.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Sikeres értesítések":::

6. Frissítse a Azure Portal lapot a replikálási eszköztár frissítéséhez. Most már létrehozhat olvasási replikákat ehhez a kiszolgálóhoz.
   

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válasszon ki egy meglévő Azure Database for PostgreSQL kiszolgálót, amelyet főkiszolgálóként kíván használni. 

2. A kiszolgáló oldalsávjának **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza a **replika hozzáadása**lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Replika hozzáadása":::

4. Adja meg az olvasási replika nevét. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="A replika neve":::

5. Válassza ki a replika helyét. Az alapértelmezett hely megegyezik a főkiszolgálóval.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Válasszon helyet":::

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

6. A replika létrehozásának megerősítéséhez kattintson **az OK gombra** .

Az olvasási replika létrehozása után a **replikálási** ablakból lehet megtekinteni:

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Az új replika megtekintése a replikálási ablakban":::
 

> [!IMPORTANT]
> Tekintse át az [olvasási replika áttekintése című témakör szempontjait](concepts-read-replicas.md#considerations).
>
> A főkiszolgálói beállítás új értékre való frissítése előtt frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja a replikációt egy főkiszolgáló és egy olvasási replika között.

> [!IMPORTANT]
> Miután leállította a replikálást egy főkiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

Ha le szeretné állítani a főkiszolgáló és egy olvasási replika közötti replikációt a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló menü **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelynél le szeretné állítani a replikálást.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="A replika kijelölése":::
 
4. Válassza a **replikálás leállítása**lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Válassza a replikáció leállítása lehetőséget":::
 
5. A replikáció leállításához kattintson **az OK gombra** .

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="A replikáció leállításának megerősítése":::
 

## <a name="delete-a-master-server"></a>Főkiszolgáló törlése
A főkiszolgálók törléséhez ugyanazokat a lépéseket kell használni, mint az önálló Azure Database for PostgreSQL-kiszolgáló törléséhez. 

> [!IMPORTANT]
> Főkiszolgáló törlésekor a rendszer leállítja a replikálást az összes olvasási replikára. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

Ha törölni szeretne egy kiszolgálót a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. Nyissa meg a kiszolgáló **Áttekintés** lapját. Válassza a **Törlés** elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="A kiszolgáló áttekintése lapon válassza a főkiszolgáló törlését":::
 
3. Adja meg a törlendő főkiszolgáló nevét. A főkiszolgáló törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="A fő kiszolgáló törlésének megerősítése":::
 

## <a name="delete-a-replica"></a>Replika törlése
A főkiszolgálók törléséhez hasonlóan törölhet egy olvasási replikát is.

- A Azure Portalban nyissa meg az olvasási replika **Áttekintés** lapját. Válassza a **Törlés** elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="A replika áttekintése lapon válassza a (z) lehetőséget a replika törléséhez.":::
 
Az olvasási replikát a **replikálási** ablakból is törölheti a következő lépések végrehajtásával:

1. A Azure Portal válassza ki a fő Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló menü **Beállítások**területén válassza a **replikálás**elemet.

3. Válassza ki a törlendő olvasási replikát.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Válassza ki a törölni kívánt replikát":::
 
4. Válassza a **replika törlése**lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Replika törlésének kiválasztása":::
 
5. Adja meg a törlendő replika nevét. A replika törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="A te replika törlésének megerősítése":::
 

## <a name="monitor-a-replica"></a>Replika figyelése
Az olvasási replikák figyelésére két metrika áll rendelkezésre.

### <a name="max-lag-across-replicas-metric"></a>Maximális késés a replikák metrikája között
A **replikák közötti maximális késés** a főkiszolgáló és a legnagyobb késleltetésű replika közötti késést mutatja bájtban. 

1.  A Azure Portal válassza ki a Master Azure Database for PostgreSQL-kiszolgálót.

2.  Válassza a **Metrikák** lehetőséget. A **metrikák** ablakban válassza a **maximális késés a replikák között**lehetőséget.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="A replikák közötti maximális késés figyelése":::
 
3.  Az **összesítéshez**válassza a **Max**lehetőséget.


### <a name="replica-lag-metric"></a>Replika késésének mérőszáma
A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt jeleníti meg a replikán. Ha nincs tranzakció a főkiszolgálón, a metrika ezt az időbeli késést tükrözi.

1. A Azure Portal válassza ki a Azure Database for PostgreSQL olvasási replikát.

2. Válassza a **Metrikák** lehetőséget. A **metrikák** ablakban válassza a **replika késése**elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="A replika késésének figyelése":::
 
3. Az **összesítéshez**válassza a **Max**lehetőséget. 
 
## <a name="next-steps"></a>Következő lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI-ben és a REST APIban](howto-read-replicas-cli.md).