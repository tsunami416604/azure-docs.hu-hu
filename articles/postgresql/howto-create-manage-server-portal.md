---
title: Az Azure-adatbázis kezelése a PostgreSQL számára – Azure portál
description: Ismerje meg, hogyan kezelheti az Azure-adatbázis PostgreSQL-kiszolgálók az Azure Portalon.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535690"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Azure-adatbázis kezelése a PostgreSQL-kiszolgálóhoz az Azure Portal használatával
Ez a cikk bemutatja, hogyan kezelheti az Azure Database for PostgreSQL-kiszolgálók. A felügyeleti feladatok közé tartozik a számítási és tárolási méretezés, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló részleteinek megtekintése.

## <a name="sign-in"></a>Bejelentkezés
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-server"></a>A kiszolgáló létrehozása
A [rövid útmutatóból](quickstart-create-server-database-portal.md) megtudhatja, hogyan hozhat létre és kezdheti el a PostgreSQL-kiszolgálóhoz készült Azure-adatbázist.

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
- További információ a [hangolási és figyelési lehetőségekről az Azure Database for PostgreSQL-ben](concepts-monitoring.md)
