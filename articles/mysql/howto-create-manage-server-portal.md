---
title: Kiszolgáló kezelése - Azure portal – Azure Database for MySQL
description: Ismerje meg, hogyan kezelheti az Azure Database for MySQL-kiszolgálókat az Azure Portalon.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c0bee0b628a49746a19545d14b8b8761d0e880d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062405"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure-adatbázis kezelése a MySQL-kiszolgálóhoz az Azure Portal használatával
Ez a cikk bemutatja, hogyan kezelheti az Azure Database for MySQL-kiszolgálók. A felügyeleti feladatok közé tartozik a számítási és tárolási méretezés, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló részleteinek megtekintése.

## <a name="sign-in"></a>Bejelentkezés
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-server"></a>A kiszolgáló létrehozása
A [rövid útmutatóból](quickstart-create-mysql-server-database-using-azure-portal.md) megtudhatja, hogyan hozhat létre és kezdheti el a MySQL-kiszolgálóhoz készült Azure-adatbázist.

## <a name="scale-compute-and-storage"></a>A számítás és a tárolás méretezése

A kiszolgáló létrehozása után az általános célú és a memóriaoptimalizált rétegek között az igények változása kor skálázható. A virtuális magok növelésével vagy csökkentésével a számítási és a memória méretezése is. A tárhely skálázható (azonban nem lehet a tárolót csökkenteni).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Általános célú és memóriaoptimalizált rétegek közötti méretezés

Az Általános célról a memóriaoptimalizáltra skálázható, és fordítva. A kiszolgáló létrehozása után nem érdemes az alapszintre és az alapszintről való váltást. 

1. Válassza ki a kiszolgálót az Azure Portalon. Válassza a **Tarifacsomag**lehetőséget, amely a **Beállítások** szakaszban található.

2. Válassza az **Általános cél** vagy a **Memória optimalizált**lehetőséget attól függően, hogy mire skáláz. 

    ![változás-árképzési szint](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > A rétegek módosítása a kiszolgáló újraindítását okozza.

4. A módosítások mentéséhez válassza az **OK gombot.**


### <a name="scale-vcores-up-or-down"></a>Virtuális magok méretezése fel- vagy leskálán

1. Válassza ki a kiszolgálót az Azure Portalon. Válassza a **Tarifacsomag**lehetőséget, amely a **Beállítások** szakaszban található.

2. Módosítsa a **virtuálismag-beállítást** úgy, hogy a csúszkát a kívánt értékre mozgatja.

    ![méretarányos számítás](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > A virtuális magok méretezése a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez válassza az **OK gombot.**


### <a name="scale-storage-up"></a>A tárolás méretezése felskálázva

1. Válassza ki a kiszolgálót az Azure Portalon. Válassza a **Tarifacsomag**lehetőséget, amely a **Beállítások** szakaszban található.

2. Módosítsa a **Tároló** beállítást úgy, hogy a csúszkát a kívánt értékre húzza.

    ![méretezési tároló](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > A tároló nem csökkenthető.

3. A módosítások mentéséhez válassza az **OK gombot.**


## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése
Módosíthatja a rendszergazdai szerepkör jelszavát az Azure Portalhasználatával.

1. Válassza ki a kiszolgálót az Azure Portalon. Az **Áttekintés ablakban** válassza a **Jelszó alaphelyzetbe állítása lehetőséget.**

   ![Áttekintés](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegdoboz kérni fogja a jelszó összetettségére vonatkozó követelményeket.

   ![alaphelyzetbe állítás](./media/howto-create-manage-server-portal/reset-password.png)

3. Az új jelszó mentéséhez válassza az **OK gombot.**


## <a name="delete-a-server"></a>Kiszolgáló törlése

Törölheti a kiszolgálót, ha már nincs rá szüksége. 

1. Válassza ki a kiszolgálót az Azure Portalon. Az **Áttekintés ablakban** válassza a **Törlés**lehetőséget.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Írja be a kiszolgáló nevét a beviteli mezőbe, és ellenőrizze, hogy ez az a kiszolgáló, amelyet törölni szeretne.

    ![megerősítés-törlés](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > A kiszolgáló törlése visszafordíthatatlan.

3. Válassza a **Törlés** elemet.


## <a name="next-steps"></a>További lépések
- További információ a biztonsági mentések és [a kiszolgáló-visszaállítás szolgáltatásról](howto-restore-server-portal.md)
- További információ a [hangolási és figyelési lehetőségekről az Azure Database for MySQL-ben](concepts-monitoring.md)
