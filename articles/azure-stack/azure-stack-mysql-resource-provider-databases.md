---
title: A MySQL-Adapter RP az AzureStack által biztosított adatbázisok használata |} A Microsoft Docs
description: Hogyan hozhat létre és kezelheti a MySQL-adatbázisok, a MySQL Adapter erőforrás-szolgáltató használatával építettek ki
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 78c4e7c6cdc8cf118a351d9030416318e51c69af
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365101"
---
# <a name="create-mysql-databases"></a>MySQL-adatbázisok létrehozása

Hozzon létre, és az adatbázisok önkiszolgáló a felhasználói portálon. Az Azure Stack felhasználói rendszerképre szűrjük, amelyek a MySQL-adatbázis szolgáltatás tartalmaz egy előfizetés szükséges.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Az üzemelő példány teszteléséhez hozzon létre egy MySQL-adatbázis

1. Jelentkezzen be az Azure Stack felhasználói portálon.
2. Válassza ki **+ erőforrás létrehozása** > **adatok + tárolás** > **MySQL-adatbázis** > **Hozzáadás**.
3. A **MySQL-adatbázis létrehozása**, adja meg az adatbázis nevét, és az egyéb beállításokat állítsa be a környezetéhez szükséges.

    ![Hozzon létre egy tesztet a MySQL-adatbázis](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. A **Create Database**válassza **Termékváltozat**. A **válassza ki a MySQL Termékváltozatot**, válassza ki az adatbázis a Termékváltozat.

    ![Válassza ki a MySQL a Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Azure Stack üzemeltetési kiszolgáló van hozzáadva, mert hozzá vannak rendelve a Termékváltozat. Az üzemeltető kiszolgálók a Termékváltozat a készletben található adatbázisok jönnek létre.

5. A **bejelentkezési**válassza ***kötelező beállítások konfigurálása***.
6. Alatt **válassza ki a bejelentkezési**, választhat egy meglévő bejelentkezést, vagy válasszon **+ új bejelentkezés létrehozása** új bejelentkezés beállításához.  Adjon meg egy **adatbázis bejelentkezési** neve és **jelszó**, majd válassza ki **OK**.

    ![Hozzon létre egy új adatbázis-bejelentkezés](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Az adatbázis-bejelentkezési név hossza legfeljebb 32 karakter hosszú lehet a MySQL 5.7-es. Korábbi kiadások legfeljebb 16 karakter.

7. Válassza ki **létrehozás** az adatbázis beállításának befejezéséhez.

Az adatbázis telepítése után jegyezze fel a **kapcsolati karakterlánc** alatt **Essentials**. Ez a karakterlánc használhatja bármely olyan alkalmazásban, el kell érnie a MySQL-adatbázishoz.

![A MySQL-adatbázis a kapcsolati sztring lekérése](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>A rendszergazdai jelszó frissítése

A jelszót a MySQL server-példányon módosításával módosíthatja.

1. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL**. Válassza ki az üzemeltető kiszolgálót.
2. A **beállítások**válassza **jelszó**.
3. A **jelszó**, adja meg az új jelszót, majd **mentése**.

![A rendszergazdai jelszó frissítése](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató frissítése](azure-stack-mysql-resource-provider-update.md)
