---
title: Az Azure Stacken SQL Adapter erőforrás-szolgáltató által biztosított adatbázisok használata |} A Microsoft Docs
description: Útmutató az SQL-Adapter erőforrás-szolgáltató használatával az SQL-adatbázisok létrehozása és kezelése
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2f286c48822956c82f99808092c26f6637be5cb1
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968826"
---
# <a name="create-sql-databases"></a>SQL-adatbázisok létrehozása

Hozzon létre, és az adatbázisok önkiszolgáló a felhasználói portálon. Az Azure Stack felhasználói kell rendszerképre szűrjük, amelyek az SQL database szolgáltatás tartalmaz egy előfizetést.

1. Jelentkezzen be a [Azure Stack](azure-stack-poc.md) felhasználói portálon.

2. Válassza ki **+ új** &gt; **adatok + tárolás** &gt; **SQL Server-adatbázis** &gt; **hozzáadása**.

3. A **Create Database**, adja meg a szükséges információkat, például **adatbázisnév** és **maximális mérete (MB)**.

   >[!NOTE]
   >Az adatbázis méretének legalább 64 MB, amely lehet növelni kell lennie az adatbázis üzembe helyezése után.

   Konfigurálja a más beállításokat a környezetéhez szükséges módon.

4. A **Create Database**válassza **Termékváltozat**. A **válassza ki a Termékváltozat**, válassza ki az adatbázis a Termékváltozat.

   ![Adatbázis létrehozása](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Azure Stack üzemeltetési kiszolgáló van hozzáadva, mert hozzá vannak rendelve a Termékváltozat. Az üzemeltető kiszolgálók a Termékváltozat a készletben található adatbázisok jönnek létre.

5. Válassza ki **bejelentkezési**.
6. A **válassza ki a bejelentkezési**, válasszon egy meglévő bejelentkezést, vagy válasszon **+ új bejelentkezés létrehozása**.
7. A **új bejelentkezés**, adjon meg egy nevet **adatbázis bejelentkezési** és a egy **jelszó**.

   >[!NOTE]
   >Ezek a beállítások a jön létre a hozzáférés csak ehhez az adatbázishoz tartozó SQL-hitelesítés hitelesítő adatok. A következő bejelentkezési felhasználónevet globálisan egyedinek kell lennie. Bejelentkezési beállítások ugyanazon Termékváltozat használó más adatbázisok felhasználhatja.

   ![Hozzon létre egy új adatbázis-bejelentkezés](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Válassza ki **OK** az adatbázis telepítésének befejezéséhez.

A **Essentials**, amely az adatbázis üzembe helyezése után jelenik meg, jegyezze fel a **kapcsolati karakterlánc**. Ez a karakterlánc bármely olyan alkalmazásban, amelyet az SQL Server-adatbázisának eléréséhez használható.

![A kapcsolati karakterlánc](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On adatbázisok

A kialakításból fakadóan AlwaysOn adatbázisok másképp kezeli, mint egy önálló kiszolgáló környezetben. További információkért lásd: [bemutatkozik az SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Ellenőrizze az SQL Always On adatbázisok

Az alábbi képernyőfelvételen látható, hogyan használhatja az SQL Server Management Studio és tekintse meg az SQL Always On adatbázis állapota.

![AlwaysOn adatbázis állapota](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

AlwaysOn adatbázisok kell szinkronizálva, és az SQL Server-példányok rendelkezésre megjelenítése és a rendelkezésre állási csoportok jelennek meg. Az adatbázis-példa az előző képernyő-rögzítési newdb1 az állapota pedig **newdb1 (szinkronizálva)**.

### <a name="delete-an-alwayson-database"></a>Az AlwaysOn adatbázis törlése

Ha törli az erőforrás-szolgáltató az SQL AlwaysOn adatbázis, SQL adatbázis törli, az elsődleges replikából, és a rendelkezésre állási csoportból.

SQL ezután a replikákon az adatbázis beteszi a visszaállítási állapotba, és az adatbázis nem dobható el, kivéve, ha által aktivált. Ha az adatbázis eldobása nem, a másodlagos replikákon egy Not Synchronizing állapotba kerülnek.

## <a name="next-steps"></a>További lépések

[Az SQL Server erőforrás-szolgáltató kezelése](azure-stack-sql-resource-provider-maintain.md)
