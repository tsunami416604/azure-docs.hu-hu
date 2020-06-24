---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for MySQL
description: Megtudhatja, hogyan állíthatja be és kezelheti az olvasási replikákat a Azure Database for MySQL a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 17771cfa9ffa73d22d2e136836dcf0f19e68f8cb
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707208"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a Azure Database for MySQL a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for MySQL szolgáltatásban a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md) , amely főkiszolgálóként lesz felhasználva.

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy a főkiszolgáló a fenti díjszabási szintek egyikében van.

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása

> [!IMPORTANT]
> Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

Az olvasási replika kiszolgáló a következő lépések segítségével hozható létre:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/).

2. Válassza ki a meglévő Azure Database for MySQL kiszolgálót, amelyet főkiszolgálóként kíván használni. Ez a művelet megnyitja az **Áttekintés** lapot.

3. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

4. Válassza a **replika hozzáadása**lehetőséget.

   ![Azure Database for MySQL – replikálás](./media/howto-read-replica-portal/add-replica.png)

5. Adja meg a replika kiszolgáló nevét.

    ![Azure Database for MySQL – replika neve](./media/howto-read-replica-portal/replica-name.png)

6. Válassza ki a replika-kiszolgáló helyét. Az alapértelmezett hely megegyezik a főkiszolgálóval.

    ![Azure Database for MySQL – replika helye](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

7. A replika létrehozásának jóváhagyásához kattintson **az OK gombra** .

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a főkiszolgáló. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. A replika-kiszolgáló mindig ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a főkiszolgáló. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) . Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a főkiszolgálónál egyenlő vagy nagyobb értékekkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

A replika-kiszolgáló létrehozása után a **replikáció** panelről is megtekinthető.

   ![Azure Database for MySQL – replikák listázása](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Replikálás megszakítása egy másodpéldány-kiszolgálón

> [!IMPORTANT]
> A kiszolgálók replikálásának leállítása visszafordíthatatlan. Miután leállította a replikálást egy fő és egy replika között, nem vonható vissza. A replika-kiszolgáló ezután önálló kiszolgáló lesz, és már támogatja az olvasást és az írást is. Ez a kiszolgáló nem hozható létre újra replikába.

Ha le szeretné állítani a replikációt egy fő és egy replika kiszolgáló között a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MySQL kiszolgálót. 

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelyen le szeretné állítani a replikálást.

   ![Azure Database for MySQL – replikálás leállítása kiszolgáló kiválasztása](./media/howto-read-replica-portal/stop-replication-select.png)

4. Válassza a **replikálás leállítása**lehetőséget.

   ![Azure Database for MySQL – replikálás leállítása](./media/howto-read-replica-portal/stop-replication.png)

5. Az **OK**gombra kattintva erősítse meg, hogy le kívánja állítani a replikálást.

   ![Azure Database for MySQL – replikálás leállítása – megerősítés](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Ha törölni szeretne egy olvasási replika kiszolgálót a Azure Portalről, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MySQL kiszolgálót.

2. Válassza a **replikálás** lehetőséget a menü **Beállítások**területén.

3. Válassza ki a törölni kívánt replika-kiszolgálót.

   ![Azure Database for MySQL – replika törlése kiszolgáló kiválasztása](./media/howto-read-replica-portal/delete-replica-select.png)

4. **Replika törlésének** kiválasztása

   ![Azure Database for MySQL – replika törlése](./media/howto-read-replica-portal/delete-replica.png)

5. Írja be a replika nevét, és kattintson a **Törlés** gombra a replika törlésének megerősítéséhez.  

   ![Azure Database for MySQL – replika törlése – megerősítés](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Főkiszolgáló törlése

> [!IMPORTANT]
> A főkiszolgáló törlése leállítja a replikálást az összes replikakiszolgálón, magát a főkiszolgálót pedig törli. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

A főkiszolgáló a Azure Portalból való törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki a fő Azure Database for MySQL kiszolgálót.

2. Az **Áttekintés**területen válassza a **Törlés**lehetőséget.

   ![Azure Database for MySQL – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-overview.png)

3. Írja be a főkiszolgáló nevét, és kattintson a **Törlés** gombra a főkiszolgáló törlésének megerősítéséhez.  

   ![Azure Database for MySQL – főkiszolgáló törlése](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Replikáció monitorozása

1. A [Azure Portal](https://portal.azure.com/)válassza ki a figyelni kívánt replikát Azure Database for MySQL-kiszolgálót.

2. Az oldalsáv **figyelés** szakaszában válassza a **metrikák**elemet:

3. A rendelkezésre álló metrikák legördülő listájában válassza a **replikálás késése másodpercben** lehetőséget.

   ![Replikációs késés kiválasztása](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Válassza ki a megtekinteni kívánt időtartományt. Az alábbi képen egy 30 perces időtartomány van kiválasztva.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. A kijelölt időtartomány replikációs késésének megtekintése. Az alábbi képen az elmúlt 30 perc látható.

   ![Időtartomány kiválasztása](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>További lépések

- További információ az [olvasási replikáról](concepts-read-replicas.md)
