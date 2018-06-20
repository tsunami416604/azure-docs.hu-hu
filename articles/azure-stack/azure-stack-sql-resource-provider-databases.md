---
title: Az Azure-veremben SQL Adapter erőforrás-szolgáltató által biztosított adatbázisok használatával |} Microsoft Docs
description: Létrehozása és kezelése az SQL-Adapter erőforrás-szolgáltató használatával kiépített SQL-adatbázisok
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
ms.openlocfilehash: a82db16f2012672f6e2669f2fd8198b177f501f3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264182"
---
# <a name="create-sql-databases"></a>SQL-adatbázis létrehozása

Hozhat létre és kezelheti a felhasználói portálon önkiszolgáló adatbázisokat. Egy Azure verem felhasználónak ajánlattal az SQL-adatbázis szolgáltatást tartalmazó előfizetéssel kell rendelkeznie.

1. Jelentkezzen be a [Azure verem](azure-stack-poc.md) felhasználói portálon.

2. Válassza ki **+ új** &gt; **adatok + tárolás "** &gt; **SQL Server-adatbázis** &gt; **hozzáadása**.

3. A **Create Database**, adja meg a szükséges információkat, például **adatbázisnév** és **maximális méretét megabájtban**.

   >[!NOTE]
   >Az adatbázis méretének legalább 64 MB Méretűnek kell lennie, amely növelheti a adatbázis központi telepítése után.

   Konfigurálja a más beállításokat a környezetéhez szükséges módon.

4. A **Create Database**, jelölje be **SKU**. A **válassza ki a Termékváltozat**, válassza ki az adatbázis a Termékváltozat.

   ![Adatbázis létrehozása](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Üzemeltetési kiszolgáló hozzáadása az Azure-vermet, hozzá vannak rendelve a Termékváltozat. Adatbázisokat üzemeltető kiszolgálók egy termékváltozatban készletében jönnek létre.

5. Válassza ki **bejelentkezési**.
6. A **válassza ki a bejelentkezési**, válasszon egy érvényes bejelentkezési adatokkal, vagy válasszon **+ hozzon létre egy új bejelentkezést**.
7. A **új bejelentkezés**, adjon meg egy nevet **adatbázis-bejelentkezési adatokat** és egy **jelszó**.

   >[!NOTE]
   >Ezek a beállítások esetén, amely a csak ehhez az adatbázishoz való hozzáférés hoz létre SQL hitelesítő. A bejelentkezési felhasználónevét globálisan egyedinek kell lennie. Az azonos Termékváltozat használó adatbázisok bejelentkezési beállítások újrafelhasználhatók.

   ![Hozzon létre egy új adatbázis-bejelentkezési adatokat](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Válassza ki **OK** az adatbázis telepítésének befejezéséhez.

A **Essentials**, az adatbázis telepítését követően bemutatott, tekintse meg a **kapcsolati karakterlánc**. Ez a karakterlánc bármely olyan alkalmazásban, amelyet az SQL Server adatbázisának elérésére használhat.

![A kapcsolat-karakterlánc beolvasása](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On adatbázisok

Úgy lett kialakítva mindig az adatbázisok kezelése másképp mint egy önálló kiszolgáló környezetben. További információkért lásd: [vezet be, az SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>SQL Always On adatbázisok ellenőrzése

Az alábbi képernyőfelvételen látható, hogyan használhatja az SQL Server Management Studio segítségével megvizsgálhatók az adatbázis állapotát SQL Always On.

![AlwaysOn adatbázis állapota](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Always On adatbázisokat kell szinkronizálva, és elérhető az SQL Server-példányok megjelenítése, és rendelkezésre állási csoportok jelennek meg. Az adatbázis-példa az előző képernyőfelvétel-készítés newdb1 állapota pedig **newdb1 (szinkronizálva)**.

### <a name="delete-an-alwayson-database"></a>Az AlwaysOn adatbázis törlése

Ha SQL AlwaysOn adatbázis törlése az erőforrás-szolgáltató, SQL adatbázis törli, az elsődleges replikából, és a rendelkezésre állási csoportból.

SQL majd az adatbázis a visszaállítási állapotba helyezi a a más replikákon, és az adatbázis nem dobható el, kivéve, ha elindul. Ha az adatbázis eldobása nem, a másodlagos replikák állapotba egy nem szinkronizál.

## <a name="next-steps"></a>További lépések

[Az SQL Server erőforrás-szolgáltató karbantartása](azure-stack-sql-resource-provider-maintain.md)
