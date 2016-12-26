---
title: "Oktatóanyag az SQL Database használatához: Kiszolgáló, kiszolgálószintű tűzfalszabály, mintaadatbázis és adatbázisszintű tűzfalszabály létrehozása, és csatlakozás az SQL Server Management Studióhoz | Microsoft Docs"
description: "Megtudhatja, hogyan kell SQL Database-alapú logikai kiszolgálót és a hozzá tartozó tűzfalszabályt beállítani, valamint SQL Database-adatbázist létrehozni és mintaadatokat betölteni. Megtanulhatja az ügyféleszközökhöz való kapcsolódás, továbbá a felhasználók és az adatbázishoz tartozó tűzfalszabály konfigurálásának módját is."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c2252fc81f97019391ca2ba957f8402c4e97a9c2
ms.openlocfilehash: f9b17c1cc77918fb1989b94b5bb359a697ceea7c


---
# <a name="get-started-with-azure-sql-database-servers-databases-and-firewall-rules-by-using-the-azure-portal-and-sql-server-management-studio"></a>Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései

Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan hajthatja végre a következő műveleteket az Azure Portalon:

* Új Azure-erőforráscsoport létrehozása
* Azure SQL logikai kiszolgáló létrehozása
* Azure SQL logikai kiszolgáló tulajdonságainak megtekintése
* Kiszolgálószintű tűzfalszabály létrehozása
* Adventure Works LT mintaadatbázis létrehozása önálló adatbázisként
* Adventure Works LT mintaadatbázis tulajdonságainak megtekintése az Azure-ban

Ez az oktatóanyag azt is bemutatja, hogyan hajthatja végre a következő műveleteket az SQL Server Management Studio legújabb verziójában:

* Csatlakozás a logikai kiszolgálóhoz és annak master adatbázisához
* A főadatbázis lekérdezése
* Csatlakozás a mintaadatbázishoz
* A mintaadatbázis lekérdezése

Mire végez az oktatóanyaggal, rendelkezésére fog állni egy Azure-erőforráscsoportban futó és logikai kiszolgálóhoz csatlakozó minta- és egy üres adatbázis. Ezen kívül rendelkezésére áll majd egy kiszolgálószintű tűzfalszabály is, amely úgy van konfigurálva, hogy engedélyezze a kiszolgálószintű rendszerbiztonsági tagnak a kiszolgálóra történő bejelentkezést egy megadott IP-címről (vagy egy IP-címtartományból). 

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

> [!TIP]
> Ugyanezen feladatokat [C#](sql-database-get-started-csharp.md) nyelven vagy a [PowerShell](sql-database-get-started-powershell.md) segítségével is végrehajthatja egy, a kezdeti lépéseket ismertető oktatóanyagban.
>

### <a name="sign-in-by-using-your-existing-account"></a>Bejelentkezés meglévő fiókkal
[Meglévő előfizetés](https://account.windowsazure.com/Home/Index) használata esetén kövesse az alábbi lépéseket az Azure Portalhoz való csatlakozáshoz.

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server-in-the-azure-portal"></a>Új logikai SQL-kiszolgáló létrehozása az Azure Portalon

1. Kattintson az **Új** gombra, írja be az **sql server** kifejezést, majd nyomja le az **ENTER** billentyűt.

    ![logikai sql server](./media/sql-database-get-started/logical-sql-server.png)
2. Kattintson az **SQL Server (logikai kiszolgáló)** elemre.
   
    ![létrehozás – logikai sql server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Az új SQL Server (logikai kiszolgáló) panel megnyitásához kattintson a **Létrehozás** gombra.

    ![ú j– logikai sql server](./media/sql-database-get-started/new-logical-sql-server.png)
3. A Kiszolgáló neve szövegbeviteli mezőben adjon meg egy érvényes nevet az új logikai kiszolgálónak. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![új kiszolgáló neve](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Az új kiszolgáló teljes neve a következő lesz: <kiszolgáló_neve>.database.windows.net.
    >
    
4. A Kiszolgáló rendszergazdájának felhasználóneve szövegbeviteli mezőben adjon meg egy felhasználónevet a kiszolgáló SQL-hitelesítéséhez. Az ilyen bejelentkezést kiszolgálószintű bejelentkezésnek nevezik. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![SQL-rendszergazda felhasználóneve](./media/sql-database-get-started/sql-admin-login.png)
5. A **Jelszó** és a **Jelszó megerősítése** szövegbeviteli mezőben adjon meg egy jelszót a kiszolgálószintű bejelentkezési fiókhoz. Zöld pipa jelzi, hogy érvényes jelszót adott meg.
    
    ![SQL-rendszergazda jelszava](./media/sql-database-get-started/sql-admin-password.png)
6. Válasszon ki egy olyan előfizetést, amelyben rendelkezik objektumok létrehozásához szükséges engedéllyel.

    ![előfizetést](./media/sql-database-get-started/subscription.png)
7. Az Erőforráscsoport szövegbeviteli mezőben válassza az **Új létrehozása** lehetőséget, majd adjon egy érvényes nevet az új erőforráscsoportnak az erőforráscsoport mezőjében (egy korábban létrehozott erőforráscsoport is használhat, amennyiben rendelkezik ilyennel). Zöld pipa jelzi, hogy érvényes nevet adott meg.

    ![új erőforráscsoport](./media/sql-database-get-started/new-resource-group.png)

8. A **Hely** szövegbeviteli mezőben válasszon a tartózkodási helyének megfelelő adatközpontot (pl. „Kelet-Ausztrália”).
    
    ![kiszolgáló helye](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > Az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzet állapota ezen a panelen nem módosítható. Ezt a beállítást a kiszolgálói tűzfal paneljén módosíthatja. További információkért lásd a [biztonságos használat első lépéseit](sql-database-get-started-security.md) ismertető témakört.
    >
    
9. Kattintson a **Létrehozás** gombra.

    ![létrehozás gomb](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-sql-server-properties-in-the-azure-portal"></a>A logikai SQL Server tulajdonságainak megtekintése az Azure Portalon

1. Az Azure Portalon kattintson a **További szolgáltatások** elemre.

    ![további szolgáltatások](./media/sql-database-get-started/more-services.png)
2. A Szűrő szövegmezőbe írja be az **SQL** kifejezést, majd kattintson a SQL-kiszolgálók mellett található csillagra, hogy az SQL-kiszolgálókat kedvencként jelölje meg az Azure-ban. 

    ![beállítás kedvencként](./media/sql-database-get-started/favorite.png)
3. Az alapértelmezett panelen kattintson az **SQL-kiszolgálók** elemre az Azure-előfizetésében található SQL-kiszolgálók listájának megnyitásához. 

    ![új SQL-kiszolgáló](./media/sql-database-get-started/new-sql-server.png)

4. Kattintson az új SQL-kiszolgálóra, hogy megtekinthesse a tulajdonságait az Azure Portalon. További oktatóanyagok segítségével megismerheti a panelen elérhető beállítási lehetőségeket.

    ![sql server panel](./media/sql-database-get-started/sql-server-blade.png)
5. A logikai SQL Server különböző tulajdonságainak megtekintéséhez kattintson a **Tulajdonságok** elemre a Beállítások területen.

    ![sql server tulajdonságai](./media/sql-database-get-started/sql-server-properties.png)
6. Másolja a vágólapra a teljes kiszolgálónevet, hogy később is használhassa az oktatóanyag teljesítése során.

    ![sql server teljes neve](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

1. Az SQL Server tűzfalához tartozó panel megnyitásához kattintson a Beállítások terület alatt található **Tűzfal** elemre az SQL Server paneljén.

    ![sql server tűzfal](./media/sql-database-get-started/sql-server-firewall.png)

2. Ellenőrizze a megjelenő ügyfél IP-címet, és egy internetes böngészőben győződjön meg róla, hogy valóban ez az Ön által használt IP-cím (vannak olyan weboldalak, amelyek megadják ezt az információt). Időnként előfordulhat, hogy az IP-címek nem egyeznek.

    ![a használt IP-cím](./media/sql-database-get-started/your-ip-address.png)

3. Amennyiben az IP-címek egyeznek, kattintson az **Ügyfél IP-címének hozzáadása** elemre az eszköztárban.

    ![ügyfél IP-címének hozzáadása](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Az SQL Database-tűzfalat a kiszolgálón egyetlen IP-cím vagy egy teljes IP-címtartomány előtt is megnyithatja. A tűzfal megnyitását követően az SQL-rendszergazdák és -felhasználók a kiszolgáló bármely olyan adatbázisába bejelentkezhetnek, amelyhez érvényes hitelesítő adatokkal rendelkeznek.
    >

4. A kiszolgálószintű tűzfalszabály mentéséhez kattintson a **Mentés** gombra az eszköztárban, majd kattintson az **OK** gombra.

    ![ügyfél IP-címének hozzáadása](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-sql-server-using-sql-server-management-studio-ssms"></a>Csatlakozás az SQL Serverhez az SQL Server Management Studio (SSMS) használatával

1. Amennyiben ezt még nem tette meg, töltse le és telepítése az SSMS legújabb verzióját az [SQL Server Management Studio letöltését lehetővé tévő](https://msdn.microsoft.com/library/mt238290.aspx) weboldalon. Ahhoz, hogy naprakész maradhasson, az SSMS legújabb verziója értesíti arról, ha egy új verzió válik letölthetővé.

2. Telepítés után írja be a Windows keresőmezőbe a **Microsoft SQL Server Management Studio** kifejezést, majd nyomja le az **Enter** billentyűt az SSMS megnyitásához:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. Adja meg a megfelelő adatokat a Connect to Server (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, hogy SQL Server-hitelesítéssel csatlakozhasson az SQL Serverhez.

    ![kapcsolódás a kiszolgálóhoz](./media/sql-database-get-started/connect-to-server.png)
4. Kattintson a **Connect** (Csatlakozás) gombra.

    ![csatlakoztatva a kiszolgálóhoz](./media/sql-database-get-started/connected-to-server.png)
5. Az objektumok megtekintéséhez az Object Explore master adatbázisában bontsa ki a **Databases** (Adatbázisok), **System Databases** (Rendszeradatbázisok), **master** elemet.

    ![master adatbázis](./media/sql-database-get-started/master-database.png)
6. Kattintson jobb gombbal a **master** elemre, majd kattintson az **New Query** (Új lekérdezés) gombra.

    ![master adatbázis lekérdezése](./media/sql-database-get-started/query-master-database.png)

8. A lekérdezési ablakba írja be a következőt:

   ```select * from sys.objects```

9.  Ha a master adatbázisban található összes rendszerobjektumot le szeretné kérni, kattintson az **Execute** (Végrehajtás) gombra az eszköztárban.

    ![master adatbázis rendszerobjektumainak lekérdezése](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Az SQL-biztonsággal kapcsolatos további információkért tekintse meg az [SQL-biztonságra vonatkozó első lépéseket](sql-database-get-started-security.md) ismertető leírást.
    >

## <a name="create-new-database-in-the-azure-portal-using-adventure-works-lt-sample"></a>Új adatbázis létrehozása az Azure Portalon Adventure Works LT-sablon használatával

1. Az Azure Portal alapértelmezett paneljén kattintson az **SQL-adatbázisok** elemre.

    ![sql-adatbázisok](./media/sql-database-get-started/new-sql-database.png)
2. Az SQL-adatbázisok panelen kattintson a **Hozzáadás** gombra.

    ![sql-adatbázis hozzáadása](./media/sql-database-get-started/add-sql-database.png)
3. Az SQL-adatbázisok panelen tekintse át a megadott információkat.

    ![sql-adatbázis panel](./media/sql-database-get-started/sql-database-blade.png)
4. Adjon meg egy érvényes adatbázisnevet.

    ![sql-adatbázis neve](./media/sql-database-get-started/sql-database-name.png)
5. A Forrás kiválasztása területen kattintson a **Minta**, majd a Minta kiválasztása területen kattintson az **AdventureWorksLT [V12]** elemre.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
6. A Kiszolgáló területen adja meg a kiszolgálói rendszergazda felhasználónevét és jelszavát.

    ![kiszolgáló hitelesítő adatai](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > Az adatbázisokat a kiszolgálóhoz hozzáadhatja önálló adatbázisként (ez az alapértelmezés szerinti megoldás), vagy hozzáadhatja egy rugalmas készlet részeként. A rugalmas készletekről további információt a [Rugalmas készletek](sql-database-elastic-pool.md) leírásában talál.
    >

7. A Tarifacsomag területen válassza az **Alapszintű** lehetőséget. (A későbbiekben igény szerint válthat másik tarifacsomagra, de tanulási célokra a legalacsonyabb tarifacsomag használatát ajánljuk.)

    ![tarifacsomag](./media/sql-database-get-started/pricing-tier.png)
8. Kattintson a **Létrehozás** gombra.

    ![létrehozás gomb](./media/sql-database-get-started/create.png)

## <a name="view-database-properties-in-the-azure-portal"></a>Adatbázis tulajdonságainak megtekintése az Azure Portalon

1. Az SQL-adatbázisok panelen kattintson az új adatbázisra, hogy megtekinthesse a tulajdonságait az Azure Portalon. További oktatóanyagok segítségével megismerheti a panelen elérhető beállítási lehetőségeket. 

    ![új mintaadatbázis panel](./media/sql-database-get-started/new-sample-db-blade.png)
2. Az adatbázissal kapcsolatos további információkért kattintson a **Tulajdonságok** gombra.

    ![új mintaadatbázis tulajdonságai](./media/sql-database-get-started/new-sample-db-properties.png)

3. Kattintson az: **Adatbázis-kapcsolati karakterláncok megjelenítése** elemre.

    ![új mintaadatbázis kapcsolati karakterláncai](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Kattintson az **Áttekintés** gombra, majd kattintson a kiszolgálónévre az Alapvető erőforrások panelen.
    
    ![új mintaadatbázis alapvető erőforrások panelje](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. A kiszolgáló Alapvető erőforrások paneljén megtekintheti újonnan hozzáadott adatbázisait.

    ![új mintaadatbázis kiszolgálójának alapvető erőforrások panelje](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="connect-and-query-sample-database-using-sql-server-management-studio"></a>Mintaadatbázis csatlakoztatása és lekérdezése az SQL Server Management Studióval

1. A mintaadatbázis megtekintéséhez lépjen be az SQL Server Management Studióba, kattintson a **Databases** (Adatbázisok) elemre az Object Explorerben, majd kattintson a **Frissítés** gombra az eszköztárban.

    ![új mintaadatbázis az ssms-ben](./media/sql-database-get-started/new-sample-db-ssms.png)
2. Az objektumok megtekintéséhez bontsa ki az új adatbázist az Object Explorerben.

    ![új mintaadatbázis-objektumok az ssms-ben](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Kattintson a jobb gombbal a mintaadatbázisra, majd kattintson a **New Query** (Új lekérdezés) menüpontra.

    ![új mintaadatbázis lekérdezése az ssms-ben](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. A lekérdezési ablakba írja be a következőt:

   ```select * from sys.objects```
   
9.  Ha a mintaadatbázisban található összes rendszerobjektumot le szeretné kérni, kattintson az **Execute** (Végrehajtás) gombra az eszköztárban.

    ![új mintaadatbázis-rendszerobjektumok lekérdezése az ssms-ben](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-new-blank-database-using-sql-server-management-studio"></a>Új üres adatbázis létrehozása az SQL Server Management Studióval

1. Az Object Explorerben kattintson a jobb gombbal a **Databases** (Adatbázisok), majd kattintson a **New database** (Új adatbázis) elemre.

    ![új üres adatbázis az ssms-ben](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > Az SSMS-sel adatbázisszkriptet is összeállíthat, hogy létrehozhasson egy új adatbázist a Transact-SQL használatával.
    >

2. A New Database (Új adatbázis) párbeszédpanelen adja meg az adatbázis nevét a Database name (Adatbázis neve) szövegbeviteli mezőben. 

    ![új üres adatbázis neve az ssms-ben](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. A New Database (Új adatbázis) párbeszédpanelen kattintson az **Options** (Beállítások) elemre, majd állítsa az Edition (Kiadás) tulajdonságot **Basic** (Alapszintű) értékre.

    ![új üres adatbázis beállításai az ssms-ben](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Ezen a párbeszédpanelen az Azure SQL Database többi módosítható beállítását is áttekintheti. A beállításokkal kapcsolatos további információért lásd az [adatbázis létrehozásával](https://msdn.microsoft.com/library/dn268335.aspx) foglalkozó témakört.
    >

4. Az üres adatbázis létrehozásához kattintson az **OK** gombra.
5. Ha ezzel készen van, frissítse a Database (Adatbázis) csomópontot az Object Explorerben, hogy megjelenjen az újonnan létrehozott üres adatbázis. 

    ![új üres adatbázis az object explorerben](./media/sql-database-get-started/new-blank-database-object-explorer.png)

> [!TIP]
> Tanulás közben akár pénzt is megtakaríthat a használaton kívüli adatbázisok törlésével. Az Alapszintű kiadású adatbázisokat hét napon belül visszaállíthatja. Kiszolgálót azonban semmiképp ne töröljön. Ha így tesz, sem a kiszolgálót, sem annak törölt adatbázisait nem tudja visszaállítani.
>


## <a name="next-steps"></a>Következő lépések
Most, hogy az oktatóanyag végére ért, más oktatóanyagokat is szeretnénk a figyelmébe ajánlani, amelyekkel tovább bővítheti már meglévő tudását. 

* Ha további információkat szeretne szerezni az Azure SQL Database biztonságával kapcsolatban, tekintse át az [adatbázis-biztonságot ismertető](sql-database-get-started-security.md) témakört.
* Ha munkája során az Excelt használja, olvassa el a [Connect to a SQL database in Azure with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Azure-ban az Excellel) című cikket.
* Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez) című cikk alapján.
* Ha át szeretné helyezni helyszíni SQL Server-adatbázisait az Azure-ba, olvassa el az [adatbázisok SQL Database-be történő áttelepítésével](sql-database-cloud-migrate.md) foglalkozó témakört.
* Ha a BCP parancssori eszköz használatával szeretne adatokat betölteni egy CSV-fájlból egy új táblába, olvassa el a [Loading data into SQL Database from a CSV file using BCP](sql-database-load-from-csv-with-bcp.md) (Adatok betöltése az SQL Database-be CSV-fájlból a BCP használatával) című cikket.
* Ha táblákat és más objektumokat szeretne létrehozni, tekintse meg a [táblák létrehozásával](https://msdn.microsoft.com/library/ms365315.aspx) foglalkozó oktatóanyag táblák létrehozását részletező fejezetét.

## <a name="additional-resources"></a>További források

- A műszaki áttekintést lásd a [Mi az SQL Database?](sql-database-technical-overview.md) című részben.
- Díjszabási információkért tekintse meg [az Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/) ismertető cikket.




<!--HONumber=Dec16_HO3-->


