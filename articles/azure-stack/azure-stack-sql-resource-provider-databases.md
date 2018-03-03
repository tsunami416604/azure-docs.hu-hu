---
title: "Az SQL-Adapter RP Azure veremben által biztosított adatbázisok használatával |} Microsoft Docs"
description: "Létrehozása és kezelése az SQL-adatbázisok kiépített SQL Adapter erőforrás-szolgáltató használatával"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 39f6cc30191f07a7c891446a9132222a6d264dc4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="create-sql-databases"></a>SQL-adatbázis létrehozása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Önkiszolgáló adatbázisok találhatók a felhasználói portál kezelőfelület segítségével. A felhasználó számára az ajánlat, amely tartalmazza az adatbázis-szolgáltatás rendelkező előfizetés szükséges.

1. Jelentkezzen be a [Azure verem](azure-stack-poc.md) felhasználói portal (szolgáltatás-rendszergazdák is használhatja a a felügyeleti portál).

2. Kattintson a **+ új** &gt; **adatok + tárolás "** &gt; **SQL Server-adatbázis (előzetes verzió)** &gt; **Hozzáadás**.

3. Töltse ki az űrlapot az adatbázis adatait, többek között a **adatbázisnév**, **maximális mérete**, és szükség szerint módosíthatja a más paramétereket. Válassza ki az adatbázis egy SKU kérni. Üzemeltetési kiszolgáló hozzáadása, a Termékváltozat hozzá vannak rendelve. A Termékváltozat alkotó kiszolgálók tároló készlethez tartozó adatbázisok jönnek létre.

  ![Új adatbázis](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Az adatbázis méretének legalább 64 MB Méretűnek kell lennie. Lehet növelni, beállítások használatával.

4. Adja meg a bejelentkezési beállítások: **adatbázis-bejelentkezési adatokat**, és **jelszó**. Ezek a beállítások esetén, amely a csak ehhez az adatbázishoz való hozzáférés hoz létre SQL hitelesítő. A bejelentkezési felhasználónevét globálisan egyedinek kell lennie. Hozzon létre egy új bejelentkezési beállítást, vagy válasszon egy meglévőt. Az azonos Termékváltozat használó más adatbázisok esetében a bejelentkezési beállítások újrafelhasználhatók.

    ![Hozzon létre egy új adatbázis-bejelentkezési adatokat](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Az űrlap elküldése, és várja meg, a telepítés befejezéséhez.

    Az eredményül kapott panelen láthatja a "Kapcsolati karakterlánc" mező. Bármely alkalmazásban (például a webes alkalmazás) SQL Server-hozzáférést igénylő karakterláncokat használhatja az Azure-készletben.

    ![A kapcsolat-karakterlánc beolvasása](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>SQL-adatbázis törlése
A portálról

>[!NOTE]
>
>Ha SQL AlwaysOn adatbázis töröltek, amelyek a függő Entitás, az elsődleges és az AlwaysOn rendelkezésre állási csoporthoz való törlése sikeresen megtörtént, de tervezési SQL rendelkezésre állási csoport által helyezi az adatbázist visszaállítási állapotban minden replika és nem csökken az adatbázis, kivéve, ha indított. Egy adatbázis között nem szakad meg, ha a másodlagos replikákon nem szinkronizálása az állapot lép. Új adatbázis újbóli hozzáadását azonos RP keresztül a rendelkezésre állási csoport továbbra is működik. Lásd: ![egy másodlagos adatbázis eltávolítása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Adatbázis-hitelesítő adatok kezelése
Adatbázis-hitelesítő adatok (bejelentkezési beállítások) segítségével frissítheti.

## <a name="verify-sql-alwayson-databases"></a>Ellenőrizze az SQL AlwaysOn adatbázisok
AlwaysOn adatbázisok meg kell jelennie, mert szinkronizálva és elérhető összes példánya és a rendelkezésre állási csoportban. A feladatátvétel után az adatbázis zökkenőmentesen csatlakoznia. SQL Server Management Studio segítségével ellenőrizze, hogy egy adatbázis szinkronizálását végzi:

![AlwaysOn ellenőrzése](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
