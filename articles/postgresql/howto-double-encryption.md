---
title: Infrastruktúra – kettős titkosítás – Azure Portal – Azure Database for PostgreSQL
description: Ismerje meg, hogyan állíthatja be és kezelheti az infrastruktúra kettős titkosítását a Azure Database for PostgreSQL számára.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2eb1c196e73644eeefa77d66562dc2a55d6f221a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034855"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Infrastruktúra kettős titkosítása Azure Database for PostgreSQL

Ismerje meg, hogyan állíthatja be és felügyelheti a Azure Database for PostgreSQL infrastruktúra kettős titkosítását.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – portál

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót a Azure Portal infrastruktúra-dupla titkosítással:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL**elemet. A PostgreSQL-t a keresőmezőbe is megadhatja a szolgáltatás megtalálásához. Az **egykiszolgálós** üzembe helyezési lehetőség engedélyezése.

   ![A menü "Azure Database for PostgreSQL"](./media/quickstart-create-database-portal/1-create-database.png)

3. Adja meg a kiszolgáló alapszintű információit. Válassza a **További beállítások** lehetőséget, és engedélyezze az **infrastruktúra kettős titkosítás** jelölőnégyzetet a paraméter beállításához.

    ![Azure Database for PostgreSQL kijelölések](./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png)

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez.

    ![Azure Database for PostgreSQL összegzése](./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png)

5. A kiszolgáló létrehozása után ellenőrizheti az infrastruktúra kettős titkosítását, ha ellenőrzi az állapotot az **adattitkosítási** kiszolgáló paneljén.

    ![Azure Database for MySQL érvényesítése](./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása infrastruktúra-kettős titkosítással – parancssori felület

Az alábbi lépéseket követve hozzon létre egy Azure Database for MySQL-kiszolgálót az infrastruktúra kettős titkosításával a CLI-ből:

Ez a példa létrehoz egy nevű erőforráscsoportot `myresourcegroup` a `westus` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
A következő példa létrehoz egy PostgreSQL 11 kiszolgálót az USA nyugati régiójában, amely a `mydemoserver` `myresourcegroup` kiszolgálói rendszergazdai bejelentkezéssel van ellátva az erőforráscsoporthoz `myadmin` . Ez egy **Gen 4** **általános célú** -kiszolgáló **2 virtuális mag**. Ezzel a megoldással a kiszolgáló által létrehozott kettős titkosítás is engedélyezve lesz. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>További lépések

További tudnivalók az adattitkosításról: [Azure Database for PostgreSQL adatinfrastruktúra kettős titkosítása](concepts-Infrastructure-double-encryption.md).

