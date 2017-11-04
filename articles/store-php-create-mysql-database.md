---
title: "MySQL-adatbázis létrehozása és kapcsolódás hozzá az Azure-ban"
description: "Ismerje meg, hogyan használható az Azure-portálon MySQL-adatbázis létrehozása, és csatlakoztassa azt egy PHP webalkalmazást az Azure-ban a."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>MySQL-adatbázis létrehozása és kapcsolódás hozzá az Azure-ban
Ez az oktatóanyag bemutatja, hogyan a MySQL-adatbázis létrehozása a [Azure-portálon](https://portal.azure.com) (szolgáltató [ClearDB](http://www.cleardb.com/)) és egy PHP webalkalmazást futtatja a csatlakoztatása [Azure App Service](app-service/app-service-web-overview.md).

> [!NOTE]
> MySQL-adatbázis részeként is létrehozhat egy <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">piactér app sablon</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>MySQL-adatbázis létrehozása az Azure-portálon
MySQL-adatbázis létrehozása az Azure portálon, tegye a következőket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson a bal oldali menü **új** > **adatok + tárolás** > **MySQL-adatbázis**.

    ![Azure - start MySQL-adatbázis létrehozása](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Az új MySQL-adatbázisban [panel](azure-portal-overview.md), az alábbiak szerint konfigurálja az új MySQL-adatbázis (*panel*: a portálon megjelenő vízszintesen):

   * **Adatbázis neve**: Adjon meg egy egyedi módon azonosítható nevet
   * **Előfizetés**: válassza ki az előfizetés használatára
   * **Adatbázis-típus**: válasszon **megosztott** alacsony költségű vagy szabad rétege számára vagy **dedikált** dedikált erőforrások eléréséhez.
   * **Erőforráscsoport**: a MySQL-adatbázis hozzáadása egy meglévő [erőforráscsoport](azure-resource-manager/resource-group-overview.md) vagy helyezze egy újat. Erőforrások ugyanabban a csoportban is könnyen kezelhető együtt.
   * **Hely**: Válasszon Önhöz legközelebb eső helyet. Amikor ad hozzá egy meglévő erőforráscsoportot, akkor az erőforráscsoport helye van kapcsolva.
   * **IP-címek**: kattintson a **Tarifacsomagot**, válassza ki a tarifacsomagot lehetőséget (**Merkúr** érvényben lévő korlát miatt szabad), és kattintson a **kiválasztása**.
   * **Jogi feltételek**: kattintson a **jogi feltételeket**, tekintse át a vásárlás részletei, és kattintson a **beszerzési**.
   * **Rögzítés az irányítópulton**: akkor válassza, ha közvetlenül az irányítópultról eléréséhez. Ez akkor hasznos, ha nem ismeri még portálnavigációjával.

     Az alábbi képernyőfelvételen értéke csak egy példa a MySQL adatbázis konfigurálásához.  
     ![MySQL-adatbázis létrehozása az Azure - konfigurálása](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Ha elkészült konfigurálásával, kattintson a **létrehozása**.

    ![MySQL-adatbázis létrehozása az Azure-ban – létrehozása](./media/store-php-create-mysql-database/create-db-3-create.png)

    Megjelenik egy előugró értesítések azzal kapcsolatban, akkor tudni, hogy az üzembe helyezés megkezdődött.

    ![Azure - MySQL-adatbázis létrehozása folyamatban](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Elérhetővé válik egy másik előugró központi telepítés sikeres befejezése után. A portál is megnyílik a MySQL adatbázis panel automatikusan.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Csatlakozás a MySQL-adatbázis
Új MySQL-adatbázis kapcsolati adatainak megtekintéséhez kattintson **tulajdonságok** a webalkalmazás panelen.

![MySQL-adatbázis létrehozása az Azure - MySQL adatbázis paneljén](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Használhatja a kapcsolat adatait bármely webalkalmazásban. Egy minta bemutatja, hogyan szeretné használni a kapcsolat egy egyszerű PHP-alkalmazásokban érhető el [Itt](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="next-steps"></a>Következő lépések
További információkért lásd: a [PHP fejlesztői központ](/develop/php/).
