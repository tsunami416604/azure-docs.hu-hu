---
title: Azure Database for MySQL-kiszolgáló létrehozása és kezelése Azure Portal használatával
description: Ez a cikk azt ismerteti, hogyan hozhat létre gyorsan új Azure Database for MySQL-kiszolgálót, és hogyan kezelheti a kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142092"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Azure Database for MySQL-kiszolgáló létrehozása és kezelése Azure Portal használatával
Ez a témakör azt ismerteti, hogyan hozhat létre gyorsan új Azure Database for MySQL-kiszolgálót. Emellett információkat is tartalmaz arról, hogyan kezelheti a kiszolgálót a Azure Portal használatával. A Kiszolgálókezelő tartalmazza a kiszolgáló adatainak és adatbázisainak megtekintését, a jelszó alaphelyzetbe állítását, az erőforrások méretezését és a kiszolgáló törlését.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Az alábbi lépéseket követve hozzon létre egy "mydemoserver" nevű Azure Database for MySQL-kiszolgálót.

1. Kattintson a Azure Portal bal felső sarkában található **erőforrás létrehozása** gombra.

2. Az új lapon válassza az **adatbázisok**lehetőséget, majd az adatbázisok lapon válassza a **Azure Database for MySQL**lehetőséget.

    > A Azure Database for MySQL-kiszolgáló a [számítási és tárolási](./concepts-pricing-tiers.md) erőforrások egy meghatározott készletével jön létre. Az adatbázis egy Azure-erőforráscsoport és egy Azure Database for MySQL-kiszolgáló között jön létre.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Töltse ki a Azure Database for MySQL űrlapot az alábbi információk alapján:

    | **Űrlapmező** | **Mező leírása** |
    |----------------|-----------------------|
    | *Kiszolgálónév* | mydemoserver (a kiszolgáló neve globálisan egyedi) |
    | *Előfizetés* | mysubscription (válasszon a legördülő menüből) |
    | *Erőforráscsoport* | myresourcegroup (hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt) |
    | *Forrás kiválasztása* | Üres (üres MySQL-kiszolgáló létrehozása) |
    | *Kiszolgálói rendszergazdai bejelentkezés* | myadmin (állítsa be a rendszergazdafiók nevét) |
    | *Jelszó* | rendszergazdai fiók jelszavának beállítása |
    | *Jelszó megerősítése* | erősítse meg a rendszergazdafiók jelszavát |
    | *Location* | Délkelet-Ázsia (válasszon az Észak-Európa és az USA nyugati régiója között) |
    | *Verzió* | 5,7 (Azure Database for MySQL kiszolgáló verziójának kiválasztása) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Az új kiszolgáló szolgáltatási szintjének és teljesítményszint megadásához kattintson az **árképzési szint** lehetőségre. Válassza a **általános célú** lapot. A **Számítási generáció**, **Virtuális mag**, **Tárolás** és **Biztonsági mentések megőrzési időszaka** beállítások alapértelmezett értékei a következők: *5. generációs*, *2 virtuális mag*, *5 GB* és *7 nap*. A csúszkákat nem szükséges módosítania. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

    > Válassza a **rögzítés** az irányítópulton lehetőséget az üzembe helyezések egyszerű nyomon követéséhez.

## <a name="update-an-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló frissítése
Az új kiszolgáló üzembe helyezése után a felhasználónak számos lehetősége van a meglévő kiszolgáló konfigurálására, beleértve a rendszergazdai jelszó alaphelyzetbe állítását, az árképzési réteg módosítását és a kiszolgáló méretezését a virtuális mag vagy a tárterület módosításával.

### <a name="change-the-administrator-user-password"></a>A rendszergazda felhasználói jelszavának módosítása
1. A kiszolgáló **áttekintése**lapon kattintson a **jelszó** alaphelyzetbe állítása elemre a jelszó-visszaállítási ablak megjelenítéséhez.

   ![áttekintés](./media/howto-create-manage-server-portal/overview.png)

2. Adjon meg egy új jelszót, és erősítse meg a jelszót az ablakban, ahogy az a következőképpen jelenik meg:

   ![jelszó alaphelyzetbe állítása](./media/howto-create-manage-server-portal/reset-password.png)

3. Az új jelszó mentéséhez kattintson **az OK** gombra.

### <a name="change-the-pricing-tier"></a>Árképzési szintek módosítása
> [!NOTE]
> A skálázás csak a általános célútól a memóriába optimalizált szolgáltatási rétegekben támogatott, és fordítva. Vegye figyelembe, hogy a kiszolgáló létrehozása után az alapszintű díjszabási szintről és a rendszerre való váltás nem támogatott Azure Database for MySQLban.
> 
1. Kattintson az **árképzési**csomag elemre a **Beállítások**területen.
2. Válassza ki azt az **árképzési szintet** , amelyet módosítani szeretne.

    ![változás – díjszabás – réteg](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Kattintson az **OK** gombra a módosítások mentéséhez. 

### <a name="scale-vcores-updown"></a>Virtuális mag felfelé/lefelé méretezése

1. Kattintson az **árképzési**csomag elemre a **Beállítások**területen.

2. Módosítsa a **virtuális mag** beállítást úgy, hogy áthelyezi a csúszkát a kívánt értékre.

    ![méretezés – számítás](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

### <a name="scale-storage-up"></a>A tárterület felskálázása

1. Kattintson az **árképzési**csomag elemre a **Beállítások**területen.

2. Módosítsa a **tárolási** beállításokat úgy, hogy áthelyezi a csúszkát a kívánt értékre.

    ![méretezés – tárterület](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kattintson az **OK** gombra a módosítások mentéséhez.

## <a name="delete-an-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló törlése

1. A kiszolgáló **áttekintése**elemre kattintva nyissa meg a törlés megerősítésére vonatkozó kérést.

    ![törlés](./media/howto-create-manage-server-portal/delete.png)

2. A dupla megerősítéshez írja be a kiszolgáló nevét a beviteli mezőbe.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kattintson a **Törlés** gombra a kiszolgáló törlésének megerősítéséhez. Várjon, amíg a "MySQL-kiszolgáló sikeresen törölve" felirat jelenik meg az értesítési sávon.

## <a name="list-the-azure-database-for-mysql-databases"></a>A Azure Database for MySQL adatbázisok listázása
A kiszolgáló **áttekintése**oldalon görgessen lefelé, amíg meg nem jelenik az adatbázis csempéje alul. A-kiszolgáló összes adatbázisa szerepel a táblázatban.

   ![adatbázisok megjelenítése](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló részleteinek megjelenítése
Kattintson a **Tulajdonságok**elemre a **Beállítások** területen a kiszolgáló részletes adatainak megtekintéséhez.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)