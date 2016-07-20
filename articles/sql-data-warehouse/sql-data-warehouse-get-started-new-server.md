<properties
   pageTitle="SQL Data Warehouse-adatbázis létrehozása az Azure portálon | Microsoft Azure"
   description="Tudnivalók Azure SQL Data Warehouse létrehozásáról az Azure portálon"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# Új logikai kiszolgáló létrehozása

Az SQL Database-ben és az SQL Data Warehouse-ban minden egyes adatbázis hozzá van rendelve egy kiszolgálóhoz, és minden egyes kiszolgáló egy földrajzi helyhez van hozzárendelve. A kiszolgálót logikai SQL-kiszolgálónak nevezzük.

> [AZURE.NOTE] <a name="note"></a>A logikai SQL-kiszolgáló:
  >
  > + Lehetővé teszi, hogy egységes módon legyen konfigurálva több adatbázis ugyanazon földrajzi helyen.
  > + Ez nem fizikai hardver, mint a helyszíni kiszolgálók. A szolgáltatás szoftveres oldalához tartozik. Ezért nevezik *logikai kiszolgálónak*.
  > + Több adatbázist is képes kiszolgálni a teljesítmény csökkenése nélkül.
  > + A nevében a *server* szó lefordítandó. Az SQL-**kiszolgáló** egy Azure logikai kiszolgáló, míg az SQL **Server** a Microsoft helyszíni adatbázisterméke.

1. Kattintson a **Server** (Kiszolgáló)  > **Create a new server** (Új kiszolgáló létrehozása) lehetőségre. A kiszolgáló használata díjmentes. Ha már rendelkezik V12 logikai SQL-kiszolgálóval, válassza ki meglévő kiszolgálóját, és folytassa a következő lépéssel.

    ![Új kiszolgáló létrehozása](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. Adja meg a **New server** (Új kiszolgáló) adatait.

    - **Server Name** (Kiszolgáló neve): Adja meg a logikai kiszolgáló nevét. Ez minden földrajzi helyen egyedi lesz.
    - **Server Admin Name** (Kiszolgáló rendszergazdájának neve): Adja meg a kiszolgáló rendszergazdai fiókjának nevét.
    - **Password** (Jelszó): Adja meg a kiszolgáló rendszergazdai jelszavát.
    - **Location** (Helyszín): Válasszon egy földrajzi helyet a kiszolgáló számára. A minél kisebb adatátviteli idő érdekében érdemes úgy elhelyezni a kiszolgálót, hogy az földrajzilag minél közelebb legyen az adatbázis által elért adatforrásokhoz.
    - **Create V12 Server** (V12 kiszolgáló létrehozása): az SQL Data Warehouse esetében kizárólag a YES (IGEN) lehetőség választható.
    - **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése**: Ez az SQL Data Warehouse esetében mindig be van jelölve

    >[AZURE.NOTE] A kiszolgáló nevét, a kiszolgálói rendszergazda nevét és jelszavát mindenképp jegyezze fel.  Ezekre az információkra szüksége lesz a kiszolgálóra való bejelentkezéshez.

3. Kattintson az **OK** gombra a logikai SQL Server konfigurációs beállításainak mentéséhez és az SQL Data Warehouse panelre való visszatéréshez.

    ![Új kiszolgáló konfigurálása](./media/sql-data-warehouse-get-started-provision/configure-server.png)



<!--HONumber=Jun16_HO2-->


