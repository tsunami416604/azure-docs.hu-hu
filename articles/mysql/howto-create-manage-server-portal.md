---
title: Hozzon létre és kezelhető az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával
description: Ez a cikk bemutatja, hogyan gyorsan hozzon létre egy új, Azure Database for MySQL-kiszolgáló és a kiszolgáló, az Azure Portal használata kezelheti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 39ffe1b71fb002658110c63c825e0515f38e2b9e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537324"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Hozzon létre és kezelhető az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával
Ez a témakör ismerteti, hogyan gyorsan létrehozhat egy új, Azure Database for MySQL-kiszolgálóhoz. A kiszolgáló kezelése az Azure portal használatával kapcsolatos információkat is tartalmaz. Felügyeleti kiszolgáló többek között a kiszolgáló adatait, és adatbázisok, a jelszó alaphelyzetbe állítása, erőforrások skálázása és a kiszolgáló törlése megtekintésére.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Kövesse az alábbi lépéseket követve létrehozhat egy Azure Database for MySQL-kiszolgáló neve "mydemoserver."

1. Kattintson a **erőforrás létrehozása** az Azure portal bal felső sarkában található gomb.

2. Az új oldalon válassza ki a **adatbázisok**, és az adatbázisok lapon válassza ki **, Azure Database for MySQL**.

    > Egy meghatározott készletével együtt jön létre egy Azure Database for MySQL-kiszolgáló [számítási és tárolási](./concepts-pricing-tiers.md) erőforrásokat. Az adatbázis létrehozása az Azure-erőforráscsoporton belül, és a egy Azure Database for MySQL-kiszolgálóhoz.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Adja meg az Azure Database for MySQL-űrlapot a következő információk alapján:

    | **Űrlapmező** | **Mező leírása** |
    |----------------|-----------------------|
    | *Kiszolgálónév* | mydemoserver (a kiszolgáló nevét a globálisan egyedi) |
    | *Előfizetés* | mysubscription (a legördülő menüből válassza ki) |
    | *Erőforráscsoport* | myresourcegroup (hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt) |
    | *Forrás kiválasztása* | Üres (hozzon létre egy üres MySQL-kiszolgáló) |
    | *Kiszolgálói rendszergazdai bejelentkezés* | myadmin (állítsa be a rendszergazdafiók nevét) |
    | *Jelszó* | rendszergazdai fiók jelszó beállítása |
    | *Jelszó megerősítése* | erősítse meg a rendszergazdafiók jelszavát |
    | *Hely* | Délkelet-Ázsia (Észak-Európa és az USA nyugati RÉGIÓJA között kijelölés) |
    | *Verzió* | (válassza ki az Azure Database for MySQL-kiszolgáló verziójától) 5.7 |

4. Kattintson a **tarifacsomag** , adja meg a szolgáltatási szint és a teljesítmény szint az új kiszolgáló. Válassza ki a **általános célú** fülre. A **Számítási generáció**, **Virtuális mag**, **Tárolás** és **Biztonsági mentések megőrzési időszaka** beállítások alapértelmezett értékei a következők: *4. generációs*, *2 virtuális mag*, *5 GB* és *7 nap*. A csúszkákat nem szükséges módosítania. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

    > Válassza ki a **rögzítés az irányítópulton** lehetőség kiválasztásával egyszerűen nyomon követheti az üzembe helyezést.

## <a name="update-an-azure-database-for-mysql-server"></a>Egy Azure Database for MySQL-kiszolgáló frissítése
Miután az új kiszolgáló kiépítését követően a felhasználók számára számos lehetőség közül választhat a meglévő kiszolgáló, többek között a rendszergazdai jelszó alaphelyzetbe állítása és méretezéssel a kiszolgáló virtuális mag, vagy tárolási módosításával konfigurálása.

### <a name="change-the-administrator-user-password"></a>A rendszergazda felhasználói jelszó módosítása
1. A kiszolgáló **áttekintése**, kattintson a **jelszó alaphelyzetbe állítása** a jelszó-visszaállítási ablak megjelenítése.

   ![Áttekintés](./media/howto-create-manage-server-portal/overview.png)

2. Adjon meg egy új jelszót, és a jelszavát az ablakban látható módon:

   ![jelszó alaphelyzetbe állítása](./media/howto-create-manage-server-portal/reset-password.png)

3. Kattintson a **OK** mentse az új jelszót.

### <a name="scale-vcores-updown"></a>Felfelé és lefelé méretezési csoport virtuális magok

1. Kattintson a **tarifacsomag**, alatt található **beállítások**.

2. Módosítsa a **virtuális mag** a csúszka mozgatásával a kívánt értékre állítja.

    ![számítási bővítés](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

### <a name="scale-storage-up"></a>A tárhely méretezése felfelé

1. Kattintson a **tarifacsomag**, alatt található **beállítások**.

2. Módosítsa a **tárolási** a csúszka mozgatásával a kívánt értékre állítja.

    ![a tárhely méretezése](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-an-azure-database-for-mysql-server"></a>Egy Azure Database for MySQL-kiszolgáló törlése

1. A kiszolgáló **áttekintése**, kattintson a **törlése** gombra kattintva nyissa meg a törlés megerősítésének kérését.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. A kiszolgáló nevét a mezőbe írja be a bemeneti dupla megerősítést.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kattintson a **törlése** gombra annak megerősítéséhez, hogy a kiszolgáló törlése. Várjon, amíg a "a MySQL-kiszolgáló törlése sikeresen megtörtént" pop akár az értesítési sáv jelenik meg.

## <a name="list-the-azure-database-for-mysql-databases"></a>Az Azure Database for MySQL-adatbázisok listája
A kiszolgáló **áttekintése**, görgessen lefelé, amíg meg nem látja a csempe alsó adatbázis. A táblázatban felsorolt összes adatbázis a kiszolgálón.

   ![show-adatbázisok](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Egy Azure Database for MySQL-kiszolgáló részleteinek megjelenítése
Kattintson a **tulajdonságok**, alatt található **beállítások** a kiszolgálókkal kapcsolatos részletes információk megtekintéséhez.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>További lépések

[Gyors útmutató: Hozzon létre az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)