---
title: "Létrehozása és kezelése az Azure-adatbázis MySQL-kiszolgáló Azure-portál használatával |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan gyorsan létrehozhat egy új Azure-adatbázist a MySQL-kiszolgáló és az Azure portál használatával kiszolgáló kezeléséhez."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6e9c541aac1241b6af0e4a58f5591d46f9a98c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Létrehozása és kezelése az Azure-adatbázis MySQL-kiszolgáló Azure-portál használatával
Ez a témakör ismerteti, hogyan gyorsan létrehozhat egy új Azure-adatbázis MySQL-kiszolgáló. A kiszolgáló kezelése az Azure portál használatával kapcsolatos adatokat is tartalmaz. Kiszolgáló kezelése tartalmazza a kiszolgáló adatait, és az adatbázisok, a jelszó alaphelyzetbe állítása, és a kiszolgáló törlésével megtekintése.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.
Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Kövesse az alábbi lépéseket az Azure-adatbázis "mysqlserver4demo." nevű MySQL-kiszolgáló létrehozásához

1. Kattintson a **új** gomb az Azure portál bal felső sarkában található.

2. Válassza ki az új oldalon **adatbázisok**, és az adatbázisok lapon válassza ki **MySQL az Azure-adatbázis**.

    > Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis jön létre egy adott csoportján [számítási és tárolási](./concepts-compute-unit-and-storage.md) erőforrásokat. Az adatbázis egy Azure erőforráscsoporton belül, és a MySQL-kiszolgáló egy Azure-adatbázis létrehozása.

   ![– új-kiszolgáló létrehozása](./media/howto-create-manage-server-portal/create-new-server.png)

3. Töltse ki az Azure-adatbázishoz MySQL űrlap az alábbi információk segítségével:

    | **Űrlapmező** | **Mező leírása** |
    |----------------|-----------------------|
    | *Kiszolgálónév* | Azure-beli mysql (a kiszolgáló neve, globálisan egyedi) |
    | *Előfizetés* | MySQLaaS (a legördülő menüből válassza ki) |
    | *Erőforráscsoport* | erőforrás (hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt) |
    | *Kiszolgálói rendszergazdai bejelentkezés* | myadmin (állítsa be a rendszergazdafiók nevét) |
    | *Jelszó* | a telepítő rendszergazda fiók jelszava |
    | *Jelszó megerősítése* | erősítse meg a rendszergazdafiók jelszavát |
    | *Hely* | Észak-Európa (válassza ki a Észak-Európában és USA nyugati régiója között) |
    | *Verzió* | 5.6 (Azure-adatbázis kiválasztása a MySQL-kiszolgáló verziója) |

4. Kattintson a **tarifacsomag** új kiszolgálóját a szolgáltatás és teljesítményszintet szintjének beállításához. Számítási egység konfigurálható közötti 50-100 alapszintű rétegben, 100 és a normál rétegben, 200 között, és adható tárterület a belefoglalt mennyisége alapján. Ez az útmutató útmutató most válassza 50 számítási egység és 50 GB. Kattintson a **OK** a mentéshez.

   ![Hozzon létre-kiszolgáló--tarifacsomagot](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

    > Válassza ki a **rögzítés az irányítópulton** a beállítást, a központi telepítések könnyű nyomon.
    > [!NOTE]
    > Legfeljebb 1000 alapszintű rétegben és 10 000 GB-os normál rétegben tárolási támogatják, de a nyilvános előzetes verziójához a maximális tároló mérete ideiglenesen legfeljebb 1000 GB.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis frissítése
Új kiszolgáló üzembe helyezése után a felhasználó rendelkezik-e egy meglévő kiszolgálóról szerkesztésre két lehetőség közül választhat: rendszergazdai jelszó visszaállítása vagy méretezni a kiszolgáló felfelé vagy lefelé a számítási egység módosításával.

### <a name="change-the-administrator-user-password"></a>A rendszergazda felhasználó jelszavának módosítása
1. A kiszolgálón **áttekintése** panelen kattintson a **jelszó-átállítási** jelszó bemeneti és a megerősítő ablak feltöltéséhez.

2. Adjon meg új jelszót, és az jelszót az ablakban látható módon:

   ![Jelszó alaphelyzetbe állítása](./media/howto-create-manage-server-portal/reset-password.png)

3. Kattintson a **OK** mentse az új jelszót.

### <a name="scale-updown-by-changing-compute-units"></a>Fel/le méretezhető számítási egység módosítása

1. A kiszolgáló panelen alatt **beállítások**, kattintson a **tarifacsomag** MySQL-kiszolgálóhoz tartozó Azure-adatbázis az árazás réteg panel megnyitásához.

2. Hajtsa végre a 4. lépés a **MySQL-kiszolgáló Azure-adatbázis létrehozása** számítási egység az ugyanazon a tarifacsomag módosítása.

## <a name="delete-an-azure-database-for-mysql-server"></a>Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis törlése

1. A kiszolgálón **áttekintése** panelen kattintson a **törlése** parancsgomb törlése megerősítő panel megnyitásához.

2. Kettős megerősítést kér a panel a beviteli mezőbe írja be a helyes kiszolgálónevet.

3. Kattintson a **törlése** megerősítésként az értesítési sáv megjelenő felugró a törlési művelet, és majd várnia kell a "törlése sikeres" gombra.

## <a name="list-the-azure-database-for-mysql-databases"></a>Az Azure-adatbázishoz a MySQL-adatbázisok felsorolása
A kiszolgálón **áttekintése** panelen görgessen lefelé, amíg megjelenik a csempe alsó adatbázis. A táblázatban szereplő összes adatbázist. Kattintson a **törlése** parancsgomb törlése megerősítő panel megnyitásához.

   ![megjelenítése-adatbázisok](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Egy Azure-adatbázis MySQL-kiszolgáló részleteinek megjelenítése
A kiszolgáló panelen alatt **beállítások**, kattintson a **tulajdonságok** megnyitásához a **tulajdonságok** panelt, és ezután tekintse meg az összes részletes információ a kiszolgálóról.

## <a name="next-steps"></a>Következő lépések

[Gyors üzembe helyezés: Azure-adatbázis létrehozása a MySQL-kiszolgáló Azure-portál használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)