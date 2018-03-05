---
title: "Létrehozása és kezelése az Azure-adatbázis MySQL-kiszolgáló Azure-portál használatával"
description: "Ez a cikk ismerteti, hogyan gyorsan létrehozhat egy új Azure-adatbázist a MySQL-kiszolgáló és az Azure portál használatával kiszolgáló kezeléséhez."
services: mysql
author: ajlam
ms.author: nolanwu
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0e274c0ada3de5e9000ae41516e5b9b67ef1490b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Létrehozása és kezelése az Azure-adatbázis MySQL-kiszolgáló Azure-portál használatával
Ez a témakör ismerteti, hogyan gyorsan létrehozhat egy új Azure-adatbázis MySQL-kiszolgáló. A kiszolgáló kezelése az Azure portál használatával kapcsolatos adatokat is tartalmaz. Kiszolgáló kezelése tartalmazza a kiszolgáló adatait, és az adatbázisok, a jelszó alaphelyzetbe állításával erőforrások méretezés és a kiszolgáló törlésével megtekintése.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Kövesse az alábbi lépéseket az Azure-adatbázis "mydemoserver." nevű MySQL-kiszolgáló létrehozásához

1. Kattintson a **hozzon létre egy erőforrást** gomb az Azure portál bal felső sarkában található.

2. Válassza ki az új oldalon **adatbázisok**, és az adatbázisok lapon válassza ki **MySQL az Azure-adatbázis**.

    > Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis jön létre egy adott csoportján [számítási és tárolási](./concepts-pricing-tiers.md) erőforrásokat. Az adatbázis egy Azure erőforráscsoporton belül, és a MySQL-kiszolgáló egy Azure-adatbázis létrehozása.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Töltse ki az Azure-adatbázishoz MySQL űrlap az alábbi információk segítségével:

    | **Űrlapmező** | **Mező leírása** |
    |----------------|-----------------------|
    | *Kiszolgálónév* | mydemoserver (a kiszolgáló neve, globálisan egyedi) |
    | *Előfizetés* | mysubscription (a legördülő menüből válassza ki) |
    | *Erőforráscsoport* | myresourcegroup (hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt) |
    | *Forrás kiválasztása* | Üres (hozzon létre egy üres MySQL-kiszolgáló) |
    | *Kiszolgálói rendszergazdai bejelentkezés* | myadmin (állítsa be a rendszergazdafiók nevét) |
    | *Jelszó* | készlet rendszergazda fiók jelszava |
    | *Jelszó megerősítése* | erősítse meg a rendszergazdafiók jelszavát |
    | *Hely* | Délkelet-Ázsia (válassza ki a Észak-Európában és USA nyugati régiója között) |
    | *Verzió* | 5.7 (Azure-adatbázis kiválasztása a MySQL-kiszolgáló verziója) |

4. Kattintson a **tarifacsomag** új kiszolgálóját a szolgáltatás és teljesítményszintet szintjének beállításához. Válassza ki a **általános célú** fülre. A **Számítási generáció**, **Virtuális mag**, **Tárolás** és **Biztonsági mentések megőrzési időszaka** beállítások alapértelmezett értékei a következők: *4. generációs*, *2 virtuális mag*, *5 GB* és *7 nap*. A csúszkákat nem szükséges módosítania. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

    > Válassza ki a **rögzítés az irányítópulton** a beállítást, a központi telepítések könnyű nyomon.

## <a name="update-an-azure-database-for-mysql-server"></a>Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis frissítése
Után az új kiszolgáló van megadva, a felhasználó rendelkezik-e a meglévő kiszolgáló, beleértve a rendszergazdai jelszó alaphelyzetbe állítása és a méretezésről vCore vagy tárolási módosításával a kiszolgáló felfelé vagy lefelé konfigurálása számos lehetőség közül választhat.

### <a name="change-the-administrator-user-password"></a>A rendszergazda felhasználó jelszavának módosítása
1. A kiszolgáló **áttekintése**, kattintson a **jelszó-átállítási** a jelszó-átállítási ablak megjelenítése.

   ![Áttekintés](./media/howto-create-manage-server-portal/overview.png)

2. Adjon meg egy új jelszót, és az jelszót az ablakban látható módon:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Kattintson a **OK** mentse az új jelszót.

### <a name="scale-vcores-updown"></a>Skála vCores fel/le

1. Kattintson a **tarifacsomag**, amelynek alapján **beállítások**.

2. Módosítsa a **vCore** beállítását a csúszkát a kívánt értéket.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

### <a name="scale-storage-up"></a>Skála tárolási mentése

1. Kattintson a **tarifacsomag**, amelynek alapján **beállítások**.

2. Módosítsa a **tárolási** beállítását a csúszkát a kívánt értéket.

    ![skála-tároló](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-an-azure-database-for-mysql-server"></a>Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis törlése

1. A kiszolgáló **áttekintése**, kattintson a **törlése** gombra kattintva nyissa meg a törlési megerősítés.

    ![törlés](./media/howto-create-manage-server-portal/delete.png)

2. Kettős megerősítést kér a beviteli mezőbe írja be annak a kiszolgálónak a nevét.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kattintson a **törlése** gombra annak megerősítéséhez, hogy a kiszolgáló törlése. Várjon, amíg a "törlése sikeresen megtörtént a MySQL-kiszolgáló" csatlakozási pont legfeljebb az értesítési sáv jelenik meg.

## <a name="list-the-azure-database-for-mysql-databases"></a>Az Azure-adatbázishoz a MySQL-adatbázisok felsorolása
A kiszolgáló **áttekintése**, görgessen lefelé, amíg megjelenik a csempe alsó adatbázis. A táblázatban szereplő összes adatbázis a kiszolgálón.

   ![megjelenítése-adatbázisok](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Egy Azure-adatbázis MySQL-kiszolgáló részleteinek megjelenítése
Kattintson a **tulajdonságok**, amelynek alapján **beállítások** a kiszolgálókkal kapcsolatos részletes információk megtekintéséhez.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>További lépések

[Gyors üzembe helyezés: Azure-adatbázis létrehozása a MySQL-kiszolgáló Azure-portál használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)