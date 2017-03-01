---
title: "Gyors üzembe helyezés: Az első Azure SQL Database-adatbázis | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával. Megtanulhatja azt is, hogyan használhatja együtt az SQL Server Management Studiót és az Azure SQL Database-t."
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
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Az első Azure SQL-adatbázisok létrehozása, csatlakoztatása és lekérdezése az Azure Portalon és az SSMS-sel

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre, csatlakoztathat és kérdezhet le Azure SQL-adatbázisokat az Azure Portalon és az SQL Server Management Studióval. Az oktatóanyag végére:

* Létrehoz egy logikai kiszolgálót, egy kiszolgálószintű tűzfalszabályt és két adatbázist tartalmazó logikai kiszolgálót.
* Tudni fogja, hogyan tekintheti meg a kiszolgáló és az adatbázis tulajdonságait az Azure Portalon az SQL Server Management Studióval.
* Tudni fogja, hogyan kérdezhet le egy adatbázist az Azure Portalon és az SQL Server Management Studióval.

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).

> [!TIP]
> Azt is megtudhatja, hogyan hozhat létre, csatlakoztathat és kérdezhet le egy Azure SQL-adatbázist a [PowerShell](sql-database-get-started-powershell.md) vagy [C#](sql-database-get-started-csharp.md) segítségével.
>

> [!NOTE]
> Ez az oktatóanyag a következő témakörök tartalmát segít elsajátítani: [SQL Database-kiszolgáló áttekintése](sql-database-server-overview.md), [SQL Database – áttekintés](sql-database-overview.md) és [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md). Az SQL Database szolgáltatás áttekintése: [Mi az SQL Database?](sql-database-technical-overview.md).
>  

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-fiók**. [Nyithat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Azure-beli létrehozási engedélyek**. Az előfizetés-tulajdonosi vagy közreműködői szerepkörhöz tartozó fiókok képesek csatlakozni az Azure Portalhoz. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* **SQL Server Management Studio**. Az SQL Server Management Studio (SSMS) telepítéséhez a legújabb verziót az [SQL Server Management Studio letöltését lehetővé tévő](https://msdn.microsoft.com/library/mt238290.aspx) weboldalon töltheti le. Mindig az SSMS legújabb verzióját használja, amikor az Azure SQL Database-hez csatlakozik, mivel folyamatosan új funkciók jelennek meg.

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az eljárás lépései bemutatják, hogyan csatlakozhat az Azure Portalhoz az [Azure-fiókjával](https://account.windowsazure.com/Home/Index).

1. Nyisson meg egy tetszőleges böngészőt, és csatlakozzon az [Azure Portalhoz](https://portal.azure.com/).
2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
3. A **Bejelentkezés** oldalon adja meg az előfizetése hitelesítő adatait.
   
   ![Bejelentkezés](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Új logikai SQL-kiszolgáló létrehozása

Az ebben az eljárásban leírt lépések bemutatják, hogyan hozhat létre az Azure Portallal egy logikai kiszolgálót a kiválasztott régióban. A logikai kiszolgálók olyan objektumok, amelyekben létrehozza az SQL-adatbázisokat, és amelyekben tűzfalszabályokat hoz létre, hogy a felhasználók csatlakozhassanak az Azure SQL Database-tűzfalon keresztül. 

1. Kattintson az **Új** gombra, írja be az **sql server** kifejezést, majd nyomja le az **ENTER** billentyűt.

    ![logikai sql server](./media/sql-database-get-started/logical-sql-server.png)
2. Kattintson az **SQL Server (logikai kiszolgáló)** elemre.
   
    ![létrehozás – logikai sql server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Az új SQL Server (csak logikai kiszolgáló) panel megnyitásához kattintson a **Létrehozás** gombra.

    ![ú j– logikai sql server](./media/sql-database-get-started/new-logical-sql-server.png)
3. A **Kiszolgáló neve** szövegbeviteli mezőben adjon meg egy érvényes nevet az új logikai kiszolgálónak. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![új kiszolgáló neve](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Az új kiszolgáló teljes nevének globálisan egyedinek kell lennie, és a következőképpen fog kinézni: **<kiszolgáló_neve>.database.windows.net**. Az oktatóanyagban később ezzel a teljes kiszolgálónévvel fog csatlakozni a kiszolgálóhoz és az adatbázisokhoz.
    >
    
4. A **Kiszolgáló rendszergazdájának felhasználóneve** szövegbeviteli mezőben adjon meg egy felhasználónevet a kiszolgáló SQL-hitelesítéséhez. Az ilyen felhasználónevet kiszolgálói rendszerbiztonsági taghoz tartozó felhasználónévnek nevezik. Zöld pipa jelzi, hogy érvényes nevet adott meg.
    
    ![SQL-rendszergazda felhasználóneve](./media/sql-database-get-started/sql-admin-login.png)
5. A **Jelszó** és a **Jelszó megerősítése** szövegbeviteli mezőben adjon meg egy jelszót a kiszolgálószintű bejelentkezési fiókhoz. Zöld pipa jelzi, hogy érvényes jelszót adott meg.
    
    ![SQL-rendszergazda jelszava](./media/sql-database-get-started/sql-admin-password.png)
6. Az **Előfizetés** legördülő mezőben válasszon ki egy olyan előfizetést, amelyben rendelkezik objektumok létrehozásához szükséges engedéllyel.

    ![előfizetést](./media/sql-database-get-started/subscription.png)
7. Az **Erőforráscsoport** szövegbeviteli mezőben válassza ki az **Új létrehozása** elemet, és adjon meg egy érvényes nevet az új erőforráscsoportnak. Zöld pipa jelzi, hogy érvényes nevet adott meg.

    ![új erőforráscsoport](./media/sql-database-get-started/new-resource-group.png)

8. A **Hely** szövegbeviteli mezőben válasszon ki egy adatközpontot, amelyben létrehozza a logikai kiszolgálóját.
    
    ![kiszolgáló helye](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > Az **Azure-szolgáltatások kiszolgálói hozzáférésének engedélyezése** jelölőnégyzet állapota ezen a panelen nem módosítható. Ezt a beállítást a kiszolgálói tűzfal paneljén módosíthatja. További információkért lásd a [biztonságos használat első lépéseit](sql-database-control-access-sql-authentication-get-started.md) ismertető témakört.
    >
    
9. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet.

10. Kattintson a **Létrehozás** gombra a szkript üzembe helyezéséhez az Azure-ban. Ezzel létrejön a logikai kiszolgáló.

    ![létrehozás gomb](./media/sql-database-get-started/create.png)

11. A kiszolgáló létrehozása után tekintse át a kiszolgáló alapértelmezés szerint megjelenő tulajdonságait. 

    ![sql server panel](./media/sql-database-get-started/sql-server-blade.png)
12. A logikai SQL Server különböző tulajdonságainak megtekintéséhez kattintson a **Tulajdonságok** elemre.

    ![sql server tulajdonságai](./media/sql-database-get-started/sql-server-properties.png)
13. Másolja a vágólapra a teljes kiszolgálónevet, hogy később is használhassa az oktatóanyag teljesítése során.

    ![sql server teljes neve](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az ebben az eljárásban leírt lépések bemutatják, hogyan hozhat létre a kiszolgáló szintű tűzfalszabályt az Azure Portalon. Alapértelmezés szerint az Azure SQL Database-tűzfalak meggátolják a logikai kiszolgáló és az adatbázisai külső kapcsolatait. Ahhoz, hogy csatlakozhasson a kiszolgálójához, létre kell hoznia egy tűzfalszabályt azon számítógép IP-címéről, amelyről a következő eljárásban csatlakozni fog. További információkért lásd: [Az Azure SQL Database-tűzfalszabályok áttekintése](sql-database-firewall-configure.md).

1. A kiszolgáló tűzfalához tartozó panel megnyitásához kattintson a **Tűzfal** elemre az SQL Server paneljén. Megjelenik az ügyfélszámítógép IP-címe.

    ![sql server tűzfal](./media/sql-database-get-started/sql-server-firewall.png)

2. Az eszköztár **Ügyfél IP-címének hozzáadása** elemére kattintva hozzon létre egy tűzfalszabályt az aktuális IP-címhez.

    ![ügyfél IP-címének hozzáadása](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Létrehozhat tűzfalszabályt egyetlen IP-címhez vagy a címek teljes tartományához. A tűzfal megnyitása után az SQL-rendszergazdák és -felhasználók a kiszolgáló bármely olyan adatbázisába bejelentkezhetnek, amelyhez érvényes hitelesítő adatokkal rendelkeznek.
    >

4. A kiszolgálószintű tűzfalszabály mentéséhez kattintson a **Mentés** gombra az eszköztárban, majd kattintson az **OK** gombra a sikeres műveletről tájékoztató párbeszédpanel bezárásához.

    ![sikeres](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Csatlakozás a kiszolgálóhoz az SSMS használatával

Az ebben az eljárásban lévő lépések bemutatják, hogyan csatlakozhat az SQL logikai kiszolgálóhoz az SQL Server Management Studióval. Az SSMS az adatbázis-rendszergazdák elsődleges eszköze az SQL-kiszolgálók és -adatbázisok felügyeletéhez.

1. Nyissa meg az SQL Server Management Studiót (írja be a Windows keresőmezőjébe a **Microsoft SQL Server Management Studio** kifejezést, majd nyomja le az **Enter** billentyűt az SSMS megnyitásához).

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen írja be a teljes kiszolgálónevet az előző eljárásból, válassza az SQL Server-hitelesítést, majd adja meg a kiszolgáló üzembe helyezésekor megadott bejelentkezési nevet és jelszót.

    ![kapcsolódás a kiszolgálóhoz](./media/sql-database-get-started/connect-to-server.png)
4. Kattintson a **Csatlakozás** gombra a kapcsolat elindításához és az Object Explorer megnyitásához az SSMS-ben.

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
    > Az SQL-biztonság használatának kezdő lépéseiért tekintse meg az [SQL-hitelesítésre vonatkozó első lépéseket](sql-database-control-access-sql-authentication-get-started.md) ismertető leírást
    >

## <a name="create-a-database-with-sample-data"></a>Adatbázis létrehozása mintaadatok használatával

Az ebben az eljárásban leírt lépések bemutatják, hogyan hozhat létre adatbázist mintaadatokkal a korábban létrehozott logikai kiszolgálóval társított Azure Portalon. 

1. Az Azure Portal alapértelmezett paneljén kattintson az **SQL-adatbázisok** elemre.

    ![sql-adatbázisok](./media/sql-database-get-started/new-sql-database.png)
2. Az SQL-adatbázisok panelen kattintson a **Hozzáadás** gombra. 

    ![sql-adatbázis hozzáadása](./media/sql-database-get-started/add-sql-database.png)

    ![sql-adatbázis panel](./media/sql-database-get-started/sql-database-blade.png)
3. Az **Adatbázis neve** szövegmezőben adjon meg egy érvényes adatbázisnevet.

    ![sql-adatbázis neve](./media/sql-database-get-started/sql-database-name.png)
4. A **Forrás kiválasztása** területen válassza a **Minta (AdventureWorksLT)** elemet.
   
    ![adventure works lt](./media/sql-database-get-started/adventureworkslt.png)
5. A **Kiszolgáló** területen ellenőrizze, hogy a kiszolgáló ki van-e választva. Azt is figyelje meg, hogy az adatbázisokat önálló adatbázisként (ez az alapértelmezés szerinti megoldás) vagy egy rugalmas készlet részeként is hozzáadhatja egy kiszolgálóhoz. A rugalmas készletekről további információt a [Rugalmas készletek](sql-database-elastic-pool.md) leírásában talál.

6. A **Tarifacsomag** területen módosítsa a tarifacsomagot **Alapszintű** értékre, és kattintson a **Kiválasztás** gombra. A későbbiekben igény szerint válthat másik tarifacsomagra, de tanulási célokra a legalacsonyabb tarifacsomag használatát ajánljuk.

    ![tarifacsomag](./media/sql-database-get-started/pricing-tier.png)
7. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra.

    ![létrehozás gomb](./media/sql-database-get-started/create.png)

8. Az adatbázis létrehozása után megtekintheti a tulajdonságait az Azure Portalon. További oktatóanyagok segítségével megismerheti a panelen elérhető beállítási lehetőségeket. 

    ![új mintaadatbázis panel](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Adatbázis lekérdezése az Azure Portalon

Az ebben az eljárásban leírt lépések bemutatják, hogyan kérdezheti le közvetlenül az adatbázist az Azure Portalon. 

1. Az SQL-adatbázisok panelen kattintson az **Eszközök** elemre az eszköztárban.

    ![eszközök](./media/sql-database-get-started/tools.png)
2. Az Eszközök panelen kattintson a **Lekérdezésszerkesztő (előzetes verzió)** elemre.

    ![lekérdezésszerkesztő](./media/sql-database-get-started/query-editor.png)
3. Kattintson a jelölőnégyzetre – ezzel elfogadja, hogy a lekérdezésszerkesztő egy előzetes verziójú szolgáltatás, majd kattintson az **OK** gombra.
4. A **Lekérdezésszerkesztő** panelen kattintson a **Bejelentkezés** elemre.

    ![lekérdezésszerkesztő panel](./media/sql-database-get-started/query-editor-blade.png)
5. Ellenőrizze a Hitelesítés típusát és a Bejelentkezést, majd adja meg a bejelentkezéshez tartozó jelszót. 

    ![lekérdezésszerkesztő: bejelentkezés](./media/sql-database-get-started/query-editor-login.png)
6. Kattintson az **OK** gombra a bejelentkezés megkísérléséhez.
7. Hitelesítés után írja be a következő lekérdezést a lekérdezés ablakba, majd kattintson a **Futtatás** gombra.

   ```select * from sys.objects```

    ![lekérdezésszerkesztő lekérdezés](./media/sql-database-get-started/query-editor-query.png)

8. Tekintse át a lekérdezési eredményeket az **Eredmények** ablaktáblán.

    ![lekérdezésszerkesztő: eredmények](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>Az adatbázis lekérdezése az SSMS használatával

Az ebben az eljárásban lévő lépések bemutatják, hogyan csatlakozhat az adatbázishoz az SQL Server Management Studióval, majd hogyan kérdezheti le a mintaadatokat az adatbázisban lévő objektumok megtekintéséhez.

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

## <a name="create-a-blank-database-with-ssms"></a>Új üres adatbázis létrehozása az SSMS használatával

Az ebben az eljárásban lévő lépések bemutatják, hogyan hozhat létre új adatbázist az SQL Server Management Studióval.

1. Az Object Explorerben kattintson a jobb gombbal a **Databases** (Adatbázisok), majd kattintson a **New database** (Új adatbázis) elemre.

    ![új üres adatbázis az ssms-ben](./media/sql-database-get-started/new-blank-database-ssms.png)

2. A **New Database** (Új adatbázis) párbeszédpanelen adja meg az adatbázis nevét a Database name (Adatbázis neve) szövegbeviteli mezőben. 

    ![új üres adatbázis neve az ssms-ben](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. A New Database (Új adatbázis) párbeszédpanelen kattintson az **Options** (Beállítások) elemre, majd állítsa az Edition (Kiadás) tulajdonságot **Basic** (Alapszintű) értékre.

    ![új üres adatbázis beállításai az ssms-ben](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Ezen a párbeszédpanelen az Azure SQL Database többi módosítható beállítását is áttekintheti. A beállításokkal kapcsolatos további információért lásd az [adatbázis létrehozásával](https://msdn.microsoft.com/library/dn268335.aspx) foglalkozó témakört.
    >

4. Az üres adatbázis létrehozásához kattintson az **OK** gombra.
5. Ha ezzel készen van, frissítse a Database (Adatbázis) csomópontot az Object Explorerben, hogy megjelenjen az újonnan létrehozott üres adatbázis. 

    ![új üres adatbázis az object explorerben](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Kapcsolatok hibaelhárítása

Hibaüzeneteket kap, amikor meghiúsul az Azure SQL Database-hez való kapcsolódás. A kapcsolat problémáit az SQL Azure-adatbázis újrakonfigurálása, a tűzfalbeállítások, a kapcsolat időtúllépése vagy a helytelen bejelentkezési információk okozhatják. A csatlakozás hibaelhárító eszköze: [Csatlakozási problémák elhárítása a Microsoft Azure SQL Database-szel](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database).

## <a name="delete-a-single-database-in-the-azure-portal"></a>Önálló adatbázis törlése az Azure Portalon

Ezen eljárás lépései bemutatják, hogyan törölhet egy önálló adatbázist az Azure Portallal.

1. Az Azure Portal SQL-adatbázisok paneljén kattintson a törölni kívánt adatbázisra. 
2.  kattintson az SQL-adatbázishoz tartozó a **Törlés** parancsra.

    ![adatbázis-törlése](./media/sql-database-get-started/delete-database.png)
2. Kattintson az **Igen** gombra annak megerősítéséhez, hogy véglegesen törölni kívánja az adatbázist.

    ![adatbázis-törlés-igen](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Az adatbázis a megőrzési ideje alatt visszaállítható a szolgáltatás által automatikusan létrehozott biztonsági másolatokból (ha nem törli magát a kiszolgálót). Az Alapszintű kiadású adatbázisokat hét napon belül visszaállíthatja. Az összes többi kiadásban 35 napon belül állíthatja vissza az adatbázisokat. Ha törli magát a kiszolgálót, sem a kiszolgálót, sem a törölt adatbázisait nem tudja helyreállítani. Az adatbázis biztonsági másolatairól további információt az [Az SQL Database biztonsági mentéseinek megismerése](sql-database-automated-backups.md) című témakörben, az adatbázisok biztonsági másolatból történő helyreállításáról pedig az [Adatbázis helyreállítása](sql-database-recovery-using-backups.md) című témakörben találhat. A törölt adatbázisok visszaállításának részletes útmutatójáért olvassa el a [Törölt Azure SQL-adatbázis visszaállítása az Azure Portal használatával](sql-database-restore-deleted-database-portal.md) című cikket.
>


## <a name="next-steps"></a>Következő lépések
Most, hogy az oktatóanyag végére ért, más oktatóanyagokat is szeretnénk a figyelmébe ajánlani, amelyekkel tovább bővítheti már meglévő tudását. 

- Az SQL Server hitelesítési oktatóanyagának kezdeti lépéseihez tekintse meg az [SQL-hitelesítés és -engedélyezés](sql-database-control-access-sql-authentication-get-started.md) című témakört.
- Az Azure Active Directory hitelesítési oktatóanyagának kezdeti lépéseihez tekintse meg az [AAD-hitelesítés és -engedélyezés](sql-database-control-access-aad-authentication-get-started.md) című témakört.
* Ha a mintaadatbázist szeretné lekérdezni az Azure Portalon, tekintse meg a [Nyilvános előzetes verzió: Interaktív lekérdezések használata SQL-adatbázisok esetén](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/) című témakört.
* Ha munkája során az Excelt használja, olvassa el a [Connect to a SQL database in Azure with Excel](sql-database-connect-excel.md) (SQL-adatbázishoz való kapcsolódás az Azure-ban az Excellel) című cikket.
* Ha készen áll a kódolás elkezdésére, válasszon programozási nyelvet a [Connection libraries for SQL Database and SQL Server](sql-database-libraries.md) (Adatkapcsolattárak az SQL Database-hez és az SQL Serverhez) című cikk alapján.
* Ha át szeretné helyezni helyszíni SQL Server-adatbázisait az Azure-ba, olvassa el az [adatbázisok SQL Database-be történő áttelepítésével](sql-database-cloud-migrate.md) foglalkozó témakört.
* Ha a BCP parancssori eszköz használatával szeretne adatokat betölteni egy CSV-fájlból egy új táblába, olvassa el az [adatok CSV-fájlból az SQL Database-be a BCP használatával történő betöltését](sql-database-load-from-csv-with-bcp.md) ismertető cikket.
* Ha táblákat és más objektumokat szeretne létrehozni, tekintse meg a [táblák létrehozásával](https://msdn.microsoft.com/library/ms365315.aspx) foglalkozó oktatóanyag táblák létrehozását részletező fejezetét.

## <a name="additional-resources"></a>További források

- A műszaki áttekintést lásd a [Mi az SQL Database?](sql-database-technical-overview.md) című részben.
- Díjszabási információkért tekintse meg [az Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/) ismertető cikket.




<!--HONumber=Feb17_HO3-->


