---
title: A MySQL-Adapter RP a AzureStack által biztosított adatbázisok használatával |} Microsoft Docs
description: Létrehozása és kezelése a MySQL-adatbázisok kiépítése a MySQL Adapter erőforrás-szolgáltató használatával
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031835"
---
# <a name="create-mysql-databases"></a>MySQL-adatbázis létrehozása

Hozhat létre és kezelheti a felhasználói portálon önkiszolgáló adatbázisokat. Egy Azure verem felhasználónak ajánlattal a MySQL-adatbázis szolgáltatást tartalmazó előfizetéssel kell rendelkeznie.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>MySQL-adatbázis létrehozásával a központi telepítés tesztelése

1. Jelentkezzen be a Azure verem felhasználói portálra.
2. Válassza ki **+ új** > **adatok + tárolás** > **MySQL-adatbázis** > **hozzáadása**.
3. A **MySQL-adatbázis létrehozása**, adja meg az adatbázis nevét, és konfigurálja a más beállításokat a környezetéhez szükséges módon.

    ![Egy teszt MySQL-adatbázis létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. A **Create Database**, jelölje be **SKU**. A **válassza ki a MySQL-SKU**, válassza ki az adatbázis a Termékváltozat.

    ![Válassza ki a MySQL-SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >Üzemeltetési kiszolgáló hozzáadása az Azure-vermet, hozzá vannak rendelve a Termékváltozat. Adatbázisokat üzemeltető kiszolgálók egy termékváltozatban készletében jönnek létre.

5. A **bejelentkezési**, jelölje be ***kötelező beállítások konfigurálása***.
6. A **válassza ki a bejelentkezési**, válasszon egy érvényes bejelentkezési adatokkal, vagy válasszon **+ hozzon létre egy új bejelentkezést** állíthat be egy új fiókot.  Adjon meg egy **adatbázis-bejelentkezési adatokat** nevét és **jelszó**, majd válassza ki **OK**.

    ![Hozzon létre egy új adatbázis-bejelentkezési adatokat](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Az adatbázis-bejelentkezési nevet a hossza nem haladhatja meg a MySQL 5.7 32 karakter hosszú lehet. A korábbi kiadásokban nem haladhatja meg 16 karakter hosszúságú lehet.

7. Válassza ki **létrehozása** az adatbázis beállításának befejezéséhez.

Miután az adatbázis telepítve lett, tekintse meg a **kapcsolati karakterlánc** alatt **Essentials**. Bármely olyan alkalmazás el tudja érni a MySQL-adatbázis is használhatja ezt a karakterláncot.

![A MySQL-adatbázis a kapcsolati karakterlánc beolvasása](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Frissítés a rendszergazdai jelszó

A MySQL server-példányon módosításával módosíthatja a jelszót.

1. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL**. Válassza ki az üzemeltetési kiszolgálóhoz.
2. A **beállítások**, jelölje be **jelszó**.
3. A **jelszó**, adja meg az új jelszót, és válassza ki **mentése**.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató karbantartása](azure-stack-mysql-resource-provider-maintain.md)