---
title: "Telepítse át, és a webalkalmazások Azure-Felhőszolgáltatás a Visual Studio eszközből közzététele |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse át, és az Azure felhőszolgáltatásban a webes alkalmazás közzététele a Visual Studio használatával."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: d5de4f5a7357cf5adde7773867356d47ad447bab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Hogyan: át, és tegye közzé a Visual Studio egy Azure-Felhőszolgáltatásban a webes alkalmazás
Hogy kihasználják az üzemeltetési szolgáltatásokat, és Azure méretezhetőségét, érdemes át, és tegye közzé a webalkalmazást egy Azure felhőszolgáltatásban. Lefuttathat egy webalkalmazást az Azure-ban legfeljebb minimális változtatásokra a meglévő alkalmazásban.

> [!NOTE]
> Ez a témakör tárgya felhőszolgáltatásokhoz, nem a webhelyek telepítését. Webhelyek központi telepítésével kapcsolatos információkért lásd: [webalkalmazás üzembe helyezése az Azure App Service](app-service/app-service-deploy-local-git.md).
>
>

Visual C# és Visual Basic támogatott adott sablonok listáját című **támogatott Projektsablonjai** a témakör későbbi részében.

Először engedélyeznie kell a webes alkalmazás az Azure-hoz a Visual Studio eszközből. Az alábbi ábrán a fontos lépések az Azure-projekt a telepítéshez hozzáadásával a már meglévő webalkalmazás közzététele. Ez a folyamat egy Azure-szükséges webes szerepkör projekt hozzáadása a megoldás. Alapján a webes projekt, amely rendelkezik, a projekt tulajdonságait szerelvények is frissülnek Ha a szolgáltatáscsomag telepítési további szerelvények igényel.

![Tegye közzé a webalkalmazást a Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

> [!NOTE]
> A **átalakítása**, **Azure Cloud Service-projekt átalakítása** parancs csak a webes projektet a megoldásban a jelenik meg. A parancs például egy Silverlight-projektet a megoldásban a nem érhető el.
> A service-csomag létrehozásakor, vagy tegye közzé az alkalmazást az Azure-ba, a figyelmeztetések vagy hibák fordulhatnak elő. Ezek a figyelmeztetések és hibák segítségével kapcsolatos problémák megoldása az Azure-bA központi telepítése előtt. Például akkor fordulhat elő egy figyelmeztetést jelenít meg hiányzott egy szerelvény. Figyelmeztetéseket hibaként kapcsolatos további információkért lásd: [egy Azure Felhőszolgáltatás-projekt konfigurálása a Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Ha építenie az alkalmazást, majd futtassa helyileg a a compute emulator használatával vagy közzéteszi az Azure merülhetnek fel a következő hiba a **Hibalista** ablak: **a megadott elérési út, a fájlnév vagy mindkettő túl hosszúak**. Ez a hiba akkor fordul elő, mert a teljes Azure-projekt neve túl hosszú. A projekt nevét, a teljes elérési útja, beleértve a hossza nem lehet több mint 146 karakternél. Például azt a projekt teljes nevet, beleértve a fájl elérési útját, amely egy Silverlight-alkalmazást hoz létre Azure-projekt: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Lehetséges, hogy a megoldás áthelyezése egy másik címtárhoz, amely rendelkezik egy rövidebb elérési utat a teljesen minősített projekt neve csökkentése érdekében.
>
>

Telepítse át, és tegye közzé a webalkalmazást, az Azure-bA a Visual Studio eszközből, kövesse az alábbi lépéseket.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Webalkalmazás az Azure való központi telepítésének engedélyezése
### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>A webalkalmazás az Azure való központi telepítésének engedélyezése
1. A webalkalmazás az Azure való központi telepítésének engedélyezéséhez nyissa meg a webes projektek a helyi menüben a megoldás, és válassza ki az Azure-telepítési projekt hozzáadása.

    A következő műveletek történnek meg:

   * Azure-projekt nevű `<name of the web project>.Azure` hozzáadódik a megoldás az alkalmazáshoz.
   * A webes projekt webes szerepkört az Azure-projekt kerül.
   * A **másolása helyi** tulajdonság értéke igaz, MVC 2, MVC 3, MVC a szükséges szerelvényeket, 4, és a Silverlight üzleti alkalmazások. A szerelvények hozzáadása a service-csomagot, amely a központi telepítéshez használt.

   > [!IMPORTANT]
   > Ha más szerelvényeknek vagy a fájlokat, amelyek szükségesek a webalkalmazáshoz, ezek a fájlok tulajdonságait manuálisan meg kell adni. Ezek a tulajdonságok beállításával kapcsolatos információkért lásd: a szakasz **fájlok közé tartoznak a szervizcsomagban** című cikkben.
   >
   > [!NOTE]
   > Ha a megoldás az Azure-projekt már létezik egy meghatározott webes projekt webes szerepkör **átalakítása**, **Azure Cloud Service-projekt átalakítása** nem jelenik meg a webes projekt helyi menüje.
   >
   >

   Ha több webes projektet a webalkalmazásban rendelkezik, és a webes szerepkörök minden webes projekt létrehozásához, az minden webes projekt eljárás hajtsa végre a lépéseket. Ez létrehoz minden webes szerepkör külön Azure projektek. Minden webes projekt külön tehetők közzé. Azt is megteheti manuálisan hozzáadhat egy másik webes szerepkör egy meglévő Azure-projekt webalkalmazásban. Ehhez nyissa meg a helyi menüje a **szerepkörök** Azure célverzióját, válasszon **Hozzáadás**, majd **webes szerepkör projekt megoldásban**, válassza ki a projektet a webes szerepkör hozzáadása, és válassza a **OK** gombra.

## <a name="use-an-azure-sql-database-for-your-application"></a>Az alkalmazás az Azure SQL adatbázis használata
Ha egy kapcsolati karakterláncot a webalkalmazás, amely a helyszíni SQL Server adatbázist használ, módosítania kell a kapcsolati karakterláncot egy SQL-adatbázis-példány használata Azure futtató helyette.

> [!IMPORTANT]
> Az előfizetés engedélyeznie kell, hogy az SQL-adatbázist használja. Ha az előfizetést fér hozzá a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885), meghatározhatja, hogy mely szolgáltatásokat nyújtja az előfizetéshez. Az alábbi utasítások alapján alkalmazni a kiadott [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885). Ha használja a [Azure-portálon](http://portal.microsoft.com), ugorjon a következő eljárással.
>
>

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>A webes szerepkörben használandó SQL Database-példányt a kapcsolati karakterlánc
1. SQL-adatbázis példányt létrehozni a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885), kövesse a következő: [hozzon létre egy SQL-adatbáziskiszolgáló](http://go.microsoft.com/fwlink/?LinkId=225109).

   > [!NOTE]
   > A tűzfalszabályok beállítása az SQL-adatbázis-példány esetében, amikor ki kell választania a **a kiszolgálóhoz való más Azure-szolgáltatások engedélyezése** jelölőnégyzetet.
   >
   >
2. SQL-adatbázis a kapcsolati karakterlánc használandó példány létrehozásához kövesse a következő szakaszban a következő cikkben: [SQL-adatbázis létrehozása](http://go.microsoft.com/fwlink/?LinkId=225110).
3. Másolja az ADO.NET kapcsolati karakterláncot a kapcsolati karakterlánc használandó, hajtsa végre a következő lépéseket a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).  

   1. Válassza ki a **adatbázis** gombra, és nyissa meg az előfizetés, amely az SQL-adatbázis-példány létrehozásához használt csomópont.
   2. SQL-adatbázis elérhető példányok megjelenítéséhez kattintson a **SQL-adatbázisok** csomópont.
   3. Az adatbázis tulajdonságainak megjelenítéséhez, válassza ki az adatbázist. A **tulajdonságok** nézet jelenik meg.

      > [!NOTE]
      > Ha a **tulajdonságok** nézet nem jelenik meg, szükség lehet, hogy megnyissa a elválasztó használatával.
      >
      >
   4. A kapcsolati karakterláncok megjelenítéséhez kattintson a három ponttal (…) gombra a lehetőséget a nézetnél.

      A **kapcsolati karakterláncok** párbeszédpanel jelenik meg.
   5. Másolja az ADO.NET kapcsolati karakterláncot, jelölje ki a szöveget, és válassza a Ctrl + C kulcsok.
   6. A párbeszédpanel bezárásához, válassza ki a **bezárása** gombra.
4. Cserélje le a kapcsolati karakterlánc a web.config fájlban, az SQL-adatbázis ezen példányának használatára, nyissa meg a web.config fájlt, jelölje ki a meglévő kapcsolati karakterlánc bejegyzést, és válassza a Ctrl + V kulcsok. Az ADO.NET kapcsolati karakterláncot az SQL-adatbázis-példány felülírja a meglévő kapcsolati karakterláncot.
5. Is hozzá kell adni a paraméter `MultipleActiveResultSets=True` a kapcsolati karakterlánc módosításait. A kapcsolati karakterlánc a következő formátumban kell rendelkezniük:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```
6. (Választható) A változó a kapcsolati karakterlánc közvetlenül a web.config fájlban alternatív módszert is vegyen fel egy szakaszt egy web.config átalakítása, attól függően, hogy a build konfigurációját, hogy a service-csomag létrehozásához használt fájlt. Nyissa meg a Web.Debug.Config fájl vagy a Web.Release.Config fájlt. Adja hozzá ezt a fájlt a következő szakaszban:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```
7. Mentse a fájlt, amely módosította, és közzé az alkalmazást.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Az SQL-adatbázis egy példányának használatára a klasszikus Azure portál használatával
1. Az a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885), válassza ki az SQL-adatbázisok csomópont.

   * Ha a használni kívánt SQL-adatbázis példánya jelenik meg, válassza a megnyitásához.
   * Ha még nem hozott létre példányok, válassza ki a megfelelő hivatkozásra, és majd hozzon létre egy példányt.
2. Miután megnyitásához, vagy hozzon létre egy adatbázis-példányt, válassza ki azt a **kapcsolati karakterláncok** hivatkozásra.
3. A lap alján kattintson a hivatkozásra kattintva konfigurálja a tűzfal beállításait, és fogadja el az alapértelmezett értékeket, vagy adja meg a beállításokat, amelyekre szüksége van.
4. Másolja az ADO.NET kapcsolati karakterláncot, illessze be a web.config fájlt a helyi adatbázis régi kapcsolati karakterlánca keresztül, és vegye fel `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Tegye közzé a webalkalmazást az Azure-bA
### <a name="to-publish-a-web-application-to-azure"></a>Az Azure-bA webes alkalmazás közzététele
1. Az alkalmazás tesztelése a helyi fejlesztési környezet használatával az Azure compute emulator, nyissa meg a webes szerepkör az Azure-projekt helyi menüjének, és kattintson **beállítás kezdőprojektként**. Válassza a **Debug**, **Start Debugging** (billentyűzet: **F5**).

    A **indítsa el az Azure-hibakeresés környezethez** párbeszédpanel, és az alkalmazás indítása a böngészőben. Részletes adatait a compute emulator a különböző típusú webalkalmazás elindítása lásd: a tábla ebben a szakaszban.
2. Állítsa be az alkalmazás közzététele az Azure services, Microsoft-fiókkal és az Azure-előfizetéssel kell rendelkeznie. Kövesse a lépéseket a szolgáltatások beállítása a következő témakörben: [közzététele, vagy telepítse az Azure-alkalmazások Visual studióból történő előkészítéséhez](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
3. Tegye közzé a webalkalmazást, az Azure-ba, a webes projekt helyi menüjének megnyitásához, és válassza a **Azure közzététel**.

    A **Azure-alkalmazás közzététele** párbeszédpanel, és a Visual Studio megkezdi a telepítési folyamat. További információ az alkalmazás közzététele című **közzététele az Azure-alkalmazás, a Visual Studio** a [közzététele a felhőalapú szolgáltatás, az Azure-eszközökkel](vs-azure-tools-publishing-a-cloud-service.md).

   > [!NOTE]
   > A webalkalmazás az Azure projektből is közzéteheti. Ehhez nyissa meg az Azure-projekt helyi menüjének, és válassza a **közzététel**.
   >
   >
4. Megtekintheti a központi telepítési állapotának megtekintéséhez a **Azure tevékenységnapló** ablak. Ez a napló automatikusan megjelenik a telepítési folyamat indításakor. A sor elem adatait, a műveletnaplóban bővítheti a következő ábrán látható módon:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)
5. (Választható) Megszakítja a telepítési folyamatot, nyissa meg a helyi menüben a sor elem a műveletnaplóban, és válassza a **szakítsa meg, és távolítsa el**. Ez a telepítési folyamat leáll, és törli a környezet az Azure-ból.

   > [!NOTE]
   > A telepítési környezet után üzembe helyezéséig eltávolításához kell használnia a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
6. (Választható) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a telepítési környezet a **Azure számítási** csomópontja **Cloud Explorer** vagy **Server Explorer**. Itt megtekintheti az egyes szerepkör-példányok állapotát.

    Az alábbi ábrán a szerepkörpéldányok **Server Explorer** , amíg azok továbbra is az inicializálás állapotban:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)
7. Telepítés után az alkalmazás eléréséhez, válassza ki a központi telepítés, ha az állapot melletti nyílra **befejezve** jelenik meg a **Azure tevékenységnapló**. Ez megjeleníti a webes alkalmazás URL-CÍMÉT az Azure-ban. Részletekért tekintse meg a következő táblázatot a webes alkalmazás adott típusú indítása az Azure-ból.

    A következő táblázat felsorolja a részleteit elindítani az adott webes alkalmazások az Azure-ból vagy futtatni, vagy helyileg az az Azure Compute Emulator használatával webes alkalmazások hibakeresése:

   | Webes alkalmazás típusa | Futtatás/Debug helyileg a a Compute Emulator használatával | Azure-beli |
   | --- | --- | --- |
   | ASP.NET-webalkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Válassza ki az URL-cím hivatkozás jelenik meg a **telepítési** lapján a **Azure tevékenységnapló** betöltése a kezdőlapot, a böngészőben. |
   | 2. az ASP.NET MVC webalkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Válassza ki az URL-cím hivatkozás jelenik meg a **telepítési** lapján a **Azure tevékenységnapló** betöltése a kezdőlapot, a böngészőben. |
   | 3. az ASP.NET MVC webalkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Válassza ki az URL-cím hivatkozás jelenik meg a **telepítési** lapján a **Azure tevékenységnapló** betöltése a kezdőlapot, a böngészőben. |
   | 4. az ASP.NET MVC webalkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Válassza ki az URL-cím hivatkozás jelenik meg a **telepítési** lapján a **Azure tevékenységnapló** betöltése a kezdőlapot, a böngészőben. |
   | Üres ASP.NET-webalkalmazás |Fel kell vennie az alkalmazáshoz, amely a webes projekthez lehet beállítani a kezdőlapot .aspx lapot. A menüsávban kattintson **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Ha egy alapértelmezett .aspx lapot az alkalmazásban, válassza a megjelenő URL-cím hivatkozás a **telepítési** lapján a **Azure tevékenységnapló** és ezen a lapon be van töltve, a böngészőben. Ha egy másik .aspx lapot, keresse meg az adott oldalra, az URL-cím a következő formátumban kell:`<url for deployment>/<name of page>.aspx` |
   | A Silverlight alkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Nyissa meg az adott lapot az alkalmazáshoz, az URL-cím a következő formátumban kell:`<url for deployment>/<name of page>.aspx` |
   | A Silverlight üzleti alkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Nyissa meg az adott lapot az alkalmazáshoz, az URL-cím a következő formátumban kell:`<url for deployment>/<name of page>.aspx` |
   | A Silverlight navigációs alkalmazás |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Nyissa meg az adott lapot az alkalmazáshoz, az URL-cím a következő formátumban kell:`<url for deployment>/<name of page>.aspx` |
   | WCF-alkalmazás |A WCF-szolgáltatások projekthez, állítsa be az .svc kiterjesztésű fájl kezdőlapként. A menüsávban kattintson **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Keresse meg a svc fájl az alkalmazáshoz, az URL-cím a következő formátumban kell:`<url for deployment>/<name of service file>.svc` |
   | Munkafolyamat-szolgáltatás WCF-alkalmazás |A WCF-szolgáltatások projekthez, állítsa be az .svc kiterjesztésű fájl kezdőlapként. A menüsávban kattintson **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Keresse meg a svc fájl az alkalmazáshoz, az URL-cím a következő formátumban kell:`<url for deployment>/<name of service file>.svc` |
   | Az ASP.NET dinamikus entitások |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Frissítenie kell a kapcsolati karakterlánc (lásd a következő szakaszt). Szükség keresse meg az adott oldalra, az alkalmazás az URL-cím a következő formátumban:`<url for deployment>/<name of page>.aspx` |
   | Az ASP.NET dinamikus adatok Linq to SQL |A menüsávban válassza **Debug**, **Start Debugging** (billentyűzet: válassza ki a **F5** kulcs.). |Meg kell kövesse az alábbi eljárással: SQL Azure adatbázis használata az alkalmazás (Ez a témakör korábbi szakaszában talál). Szükség keresse meg az adott oldalra, az alkalmazás az URL-cím a következő formátumban:`<url for deployment>/<name of page>.aspx` |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Frissítse a kapcsolati karakterláncot az ASP.NET dinamikus entitások
### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>A kapcsolati karakterláncok frissítése az ASP.NET dinamikus entitások
1. Az ASP.NET dinamikus entitások webalkalmazás használható SQL Azure-adatbázis létrehozásához kövesse az eljárás lépéseit **SQL Azure-adatbázis használata az alkalmazás** a témakör korábbi részében.
2. Adja hozzá a táblák és a mezőket, amelyekre szüksége van erre az adatbázisra vonatkozóan a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
3. Az ilyen típusú alkalmazás a kapcsolati karakterlánc formátuma a következő a web.config fájlban:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Frissítés a *connectionString* értéket az SQL Azure database ADO.NET kapcsolati karakterlánccal az alábbiak szerint:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```
4. A kapcsolati karakterlánc módosításait végrehajtott módosítások a web.config fájl mentése, a menüsávban válassza a **fájl**, **menteni a web.config**.

## <a name="supported-project-templates"></a>Támogatott Projektsablonjai
A webalkalmazások Azure közzétételéhez az alkalmazás kell sablonok valamelyikét használja a projekt C# és Visual Basic, az alábbi táblázatban szereplő.

| Sablon projektcsoport | Projektsablon |
| --- | --- |
| Web |ASP.NET-webalkalmazás |
| Web |2. az ASP.NET MVC webalkalmazás |
| Web |3. az ASP.NET MVC webalkalmazás |
| Web |ASP.NET MVC4-webalkalmazás |
| Web |Üres ASP.NET-webalkalmazás |
| Web |ASP.NET MVC 2 üres webalkalmazás |
| Web |Az ASP.NET dinamikus adatok entitások webes alkalmazás |
| Web |Az ASP.NET dinamikus adatok Linq to SQL webes alkalmazás |
| A Silverlight |A Silverlight alkalmazás |
| A Silverlight |A Silverlight üzleti alkalmazás |
| A Silverlight |A Silverlight navigációs alkalmazás |
| WCF |WCF-alkalmazás |
| WCF |Munkafolyamat-szolgáltatás WCF-alkalmazás |
| Munkafolyamat |Munkafolyamat-szolgáltatás WCF-alkalmazás |

## <a name="next-steps"></a>Következő lépések
Közzétételi további információkért lásd: [közzététele, vagy az Azure-alkalmazás, a Visual Studio telepítése előkészítése](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Emellett olvassa el [beállítás mentése nevű hitelesítő adatok](vs-azure-tools-setting-up-named-authentication-credentials.md).
