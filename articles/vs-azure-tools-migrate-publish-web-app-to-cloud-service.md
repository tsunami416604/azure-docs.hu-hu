---
title: "Telepítse át, és a webalkalmazások Azure-Felhőszolgáltatás a Visual Studio eszközből közzététele |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse át, és az Azure felhőszolgáltatásban a webes alkalmazás közzététele a Visual Studio használatával"
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
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: d5d41ab47c17a024900efc88ba0a006da63ab246
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Hogyan: át, és tegye közzé a Visual Studio egy Azure-Felhőszolgáltatásban a webes alkalmazás

Hogy kihasználják az üzemeltetési szolgáltatásokat, és Azure méretezési képességét, érdemes áttelepítését, valamint az Azure felhőszolgáltatásban a webes alkalmazás. Csak minimális módosításokra szükség. Ez a cikk ismerteti, hogy csak; a felhőalapú szolgáltatások telepítése App Service, lásd: [webalkalmazás üzembe helyezése az Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Ez az áttelepítés csak az adott ASP.NET, a Silverlight, a WCF és a WCF-munkafolyamat-projektek esetében támogatott. Az ASP.NET Core projektek nem támogatott. Lásd: [Projektsablonjai támogatott](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Telepítse át a projekt cloud services csomag

1. Kattintson a jobb gombbal a webes projektet, és válassza ki **konvertálása > Microsoft Azure Cloud Service-projekt átalakítása**. (, Hogy ez a parancs nem jelenik meg, ha már egy webes szerepkör projekt a megoldás.)
1. Visual Studio létrehoz egy felhőszolgáltatás-projektet a megoldás, amely tartalmazza a szükséges webes szerepkör. A projekt neve megegyezik az alkalmazás projekt és az utótag `.Azure`.
1. A Visual Studio is meghatározza a **másolása helyi** tulajdonság igaz MVC 2, MVC 3, MVC 4 és Silverlight üzleti alkalmazások a szükséges szerelvényeket. Ez a tulajdonság a szerelvények hozzáadása a service-csomagot, amely a központi telepítéshez használt.

   > [!Important]
   > Ha más szerelvényeknek vagy a fájlokat, amelyek szükségesek a webalkalmazáshoz, ezek a fájlok tulajdonságait manuálisan meg kell adni. Ezek a tulajdonságok beállításával kapcsolatos információkért lásd: [fájlok közé tartoznak a szervizcsomagban](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Összes figyelmeztetést és hibát, amelyek az Azure-ba, például a hiányzó szerelvények üzembe helyezése előtt javítsa ki a problémákra utalnak.

Ha építenie az alkalmazást, majd futtassa helyileg a a compute emulator használatával vagy közzéteszi az Azure merülhetnek fel a hiba: "a megadott elérési út, a fájlnév vagy mindkettő túl hosszúak." Ez a hiba azt jelzi, hogy a teljes Azure-projekt neve 146 karakternél. A probléma megoldásához helyezze át a megoldás egy rövidebb elérési úttal rendelkező másik mappát.

Figyelmeztetéseket hibaként kapcsolatos további információkért lásd: [egy Azure Felhőszolgáltatás-projekt konfigurálása a Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Tesztelje az áttelepítést helyileg

1. A Visual Studio **Megoldáskezelőben**, kattintson a jobb gombbal a hozzáadott felhőszolgáltatás-projekt, és válassza ki **beállítás kezdőprojektként**.
1. Válassza ki **Debug > Start Debugging** (F5) az Azure hibakeresési környezetben elindításához. Ebben a környezetben kifejezetten emuláció különböző Azure-szolgáltatáshoz biztosít.

### <a name="use-an-azure-sql-database-for-your-application"></a>Az alkalmazáshoz egy Azure SQL-adatbázis használata

Ha egy kapcsolati karakterláncot a webalkalmazás, amely egy helyi SQL Server-adatbázist használja, meg helyette az Azure SQL Database telepítse át az adatbázist, és frissítse a kapcsolati karakterlánc. Ezt a folyamatot útmutatásért tekintse meg a következő témaköröket:

- [SQL Server adatbázis áttelepítése az SQL-adatbázishoz a felhőben](sql-database/sql-database-cloud-migrate.md)
- [Való kapcsolódás és lekérdezés Visual Studio és az Azure SQL database használata .NET (C#)](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Tegye közzé az alkalmazást az Azure Cloud Service

1. A szükséges felhő és a tárfiók fiókok létrehozása az Azure-előfizetése a leírtak [közzététele, vagy telepítse az Azure-alkalmazások Visual studióból történő előkészítéséhez](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. A Visual Studióban, kattintson a jobb gombbal a projektet, és válassza ki **Microsoft Azure közzététel...**  (ami eltér a "Közzététele..." parancsot.).
1. Az a **Azure-alkalmazás közzététele** , amely jelenik meg, jelentkezzen be az Azure-előfizetéssel rendelkező fiókkal, és válassza ki **következő >**.
1. Az a **beállítások > Általános beállítások** lapra, válassza ki a cél felhőszolgáltatás a a **felhőalapú szolgáltatás** legördülő listából válassza ki, a kiválasztott környezettől és konfigurációktól együtt. 
1. A **beállítások > Speciális beállítások**, válassza ki a tárfiókot használja, majd válassza ki a **következő >**.
1. A **diagnosztika**, hogy küldje el az Application Insights-e.
1. Válassza ki **következő >** összegzését, majd válassza ki, ha **közzététel** központi telepítésének elindítása.
1. A Visual Studio egy adott előrehaladásának tevékenységnapló ablak megnyitása:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Választható) A telepítés megszakításához kattintson a jobb gombbal a műveletnaplóban sortételt, és válassza ki **szakítsa meg, és távolítsa el**. Ez a parancs a telepítési folyamat leáll, és törli a környezet az Azure-ból. Megjegyzés: a telepítési környezet után üzembe helyezéséig eltávolításához kell használnia a [Azure-portálon](https://portal.azure.com).
1. (Választható) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a telepítési környezet a **Server Explorer > Felhőszolgáltatások** csomópont. Itt megtekintheti az egyes szerepkör-példányok állapotát.
1. Telepítés után az alkalmazás eléréséhez, válassza ki a központi telepítés, ha az állapot melletti nyílra **befejezve** jelenik meg a **Azure tevékenységnapló** együtt az URL-címet. Részletekért tekintse meg a következő táblázatot a webes alkalmazás adott típusú indítása az Azure-ból.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>A compute emulator használatával, és az alkalmazás elindítása az Azure-ban

Minden alkalmazástípusok elindítható egy böngészőben a Visual Studio hibakereső csatlakozik kiválasztásával **Debug > Start Debugging** (F5). Egy üres ASP.NET-webalkalmazás projekt esetében, fel kell vennie egy `.aspx` az alkalmazás lapját, és állítsa be a kezdőlapot, a webes projekthez.

A következő táblázat ismerteti, az alkalmazás elindítása az Azure-ban:

   | Webes alkalmazás típusa | Azure-beli |
   | --- | --- | --- |
   | ASP.NET-webalkalmazás<br/>(beleértve az MVC-2, MVC 3, MVC 4) | Válassza ki az URL-címet a **telepítési** lapján a **Azure tevékenységnapló**. |
   | Üres ASP.NET-webalkalmazás | Ha egy alapértelmezett `.aspx` az alkalmazás lapját, válassza ki az URL-címet a **telepítési** lapján a **Azure tevékenységnapló**. Egy másik lapra lép, adja meg a böngészőben egy URL-cím a következő:`<deployment_url>/<page_name>.aspx` |
   | A Silverlight alkalmazás<br/>A Silverlight üzleti alkalmazás<br/>A Silverlight navigációs alkalmazás | Keresse meg az alkalmazás a következő URL-formátum használatával az adott lapon:`<deployment_url>/<page_name>.aspx` |
    WCF-alkalmazás<br/>Munkafolyamat-szolgáltatás WCF-alkalmazás | Állítsa be a `.svc` fájl a kezdőlapot, a WCF-szolgáltatások projekthez. Majd keresse meg a`<deployment_url>/<service_file>.svc` |
   | Az ASP.NET dinamikus entitások<br/>Az ASP.NET dinamikus adatok Linq to SQL | Frissítse a kapcsolati karakterlánc a következő szakaszban leírtak szerint. Majd keresse meg a `<deployment_url>/<page_name>.aspx`. A "LINQ to SQL" egy Azure SQL Database adatbázist kell használnia. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Frissítse a kapcsolati karakterláncot az ASP.NET dinamikus entitások

1. Az ASP.NET dinamikus entitások webalkalmazás SQL Azure adatbázis létrehozása (#use-an-azuresql-database-for-your-application) a korábban ismertetett módon.
1. Vegye fel a táblákat és mezőket, amelyekre szüksége van az adatbázis Azure-portálról.
1. Adjon meg egy kapcsolati karakterláncot a `web.config` a következő formátumú fájlt, és mentse a fájlt:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Frissítés a *connectionString* értéket az SQL Azure database ADO.NET kapcsolati karakterlánccal az alábbiak szerint:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Támogatott Projektsablonjai

Áttelepítve, és felhőbeli szolgáltatásokban közzétett alkalmazások kell sablonok valamelyikét használja az alábbi táblázatban. Az ASP.NET Core nem támogatott.

| Sablon csoport | Projektsablon |
| --- | --- |
| Web | ASP.NET webes alkalmazás (.NET-keretrendszer) |
| Web | 2. az ASP.NET MVC webalkalmazás |
| Web | 3. az ASP.NET MVC webalkalmazás |
| Web | ASP.NET MVC4-webalkalmazás |
| Web | Üres ASP.NET webes alkalmazás (vagy a hely) |
| Web | ASP.NET MVC 2 üres webalkalmazás |
| Web | Az ASP.NET dinamikus adatok entitások webes alkalmazás |
| Web | Az ASP.NET dinamikus adatok Linq to SQL webes alkalmazás |
| A Silverlight | A Silverlight alkalmazás |
| A Silverlight | A Silverlight üzleti alkalmazás |
| A Silverlight | A Silverlight navigációs alkalmazás |
| WCF | WCF-alkalmazás |
| WCF | Munkafolyamat-szolgáltatás WCF-alkalmazás |
| Munkafolyamat | Munkafolyamat-szolgáltatás WCF-alkalmazás |

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az közzététele, vagy az Azure alkalmazás a Visual Studio telepítése](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Hitelesítő adatok beállítása nevű](vs-azure-tools-setting-up-named-authentication-credentials.md).
