---
title: Át, és a egy webalkalmazás a Visual Studióból az Azure felhőszolgáltatás közzététele |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthetőek át és a egy Azure felhőszolgáltatásban a webes alkalmazás közzététele a Visual Studio használatával
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: cf1e01d0165d91570c3985850b0a780b61c6b8e5
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055121"
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>How to: telepítse át, és a Visual Studióból az Azure felhőszolgáltatás webalkalmazás közzététele

Figyelembe előnyeit, a üzemeltetési szolgáltatásokat és méretezési képes az Azure, érdemes telepíthetőek át és a egy Azure-felhőszolgáltatásban a webes alkalmazás üzembe helyezése. Csak minimális módosításokra szükség. Ez a cikk ismerteti, hogy csak a cloud Services üzembe helyezése App Service-ben, lásd: [az Azure App Service-webalkalmazás üzembe helyezése](app-service/app-service-deploy-local-git.md).

> [!Important]
> Ez az áttelepítés csak az adott ASP.NET, a Silverlight, a WCF és a WCF-munkafolyamat-projektek esetében támogatott. ASP.NET Core-projektek esetében nem támogatott. Lásd: [támogatott projektsablonok](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>A projekt migrálása a cloud serviceshez

1. Kattintson a jobb gombbal a webes projektre, és válassza ki **konvertálása > a Microsoft Azure Cloud Service-projekt átalakítása**. (Vegye figyelembe, hogy ez a parancs nem látható, ha már rendelkezik egy webes szerepkör projekt a megoldás.)
1. Visual Studio létrehoz egy felhőszolgáltatás-projektet a megoldásban, amely tartalmazza a szükséges webes szerepkör. Ez a projekt nevére ugyanaz, mint az a projektet és az utótag `.Azure`.
1. A Visual Studio beállítja a **másolási helyi** tulajdonság igaz értékre a bármely szerelvényeket, amelyek szükségesek az MVC 2, MVC 3, MVC 4 és a Silverlight üzleti alkalmazások. Ez a tulajdonság hozzáadása a service-csomagot, amely a központi telepítéshez használt ezekkel a szerelvényekkel.

   > [!Important]
   > Ha más szerelvények vagy a webes alkalmazás számára szükséges fájlokat, ezek a fájlok tulajdonságainak manuálisan be kell. Ezek a tulajdonságok beállításával kapcsolatos további információkért lásd: [fájlok közé tartozik a szervizcsomagban](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Összes figyelmeztetést és hibát, amelyek jelzik a problémák megoldásához az Azure-ba, például a hiányzó szerelvények üzembe helyezése előtt.

Ha az alkalmazás létrehozása, a compute emulator használatával helyben futtatható, vagy tegye közzé az Azure-ba, jelenhetnek meg a hiba: "a megadott elérési úthoz, a fájlnév vagy mindkettő túl hosszúak." Ez a hiba azt jelzi, hogy a teljesen minősített Azure projekt neve 146 karakternél. A probléma elhárításához helyezze át a megoldás egy másik mappába, a rövidebb elérési utat.

Bármely figyelmeztetések hibaként kapcsolatos további információkért lásd: [konfigurálása az Azure Felhőszolgáltatás-projekt a Visual Studióval](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Az áttelepítés helyi tesztelése

1. A Visual Studióban **Megoldáskezelőben**, kattintson a jobb gombbal a hozzáadott felhőszolgáltatás-projekt, és válassza ki **Set as Startup Project**.
1. Válassza ki **Debug > Start Debugging** (F5), indítsa el az Azure-hibakeresési környezetben. Ebben a környezetben kifejezetten biztosít különböző Azure-szolgáltatások Emulation technológiával.

### <a name="use-an-azure-sql-database-for-your-application"></a>Az alkalmazás az Azure SQL Database használata

Ha egy kapcsolati karakterláncot a webalkalmazás, amely egy helyszíni SQL Server-adatbázisból, adatbázis migrálása az Azure SQL Database inkább kell és a kapcsolati karakterlánc frissítése. A folyamattal útmutatásért tekintse meg a következő témaköröket:

- [Az SQL Server-adatbázis áttelepítése SQL Database-adatbázishoz a felhőben](sql-database/sql-database-cloud-migrate.md)
- [.NET (C#) használata a Visual Studióval való csatlakozásról és lekérdezésről és Azure SQL database](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Az Azure Cloud Service-alkalmazás közzététele

1. Hozzon létre a szükséges felhőalapú szolgáltatás és a storage-fiókok az Azure-előfizetésében leírt [közzétételekor vagy telepítésekor a Visual Studio Azure-alkalmazások előkészítése](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. A Visual Studióban kattintson a jobb gombbal a projektre, és válassza ki **a Microsoft Azure közzététel...**  (amely nem azonos a "Közzététele..." parancsot.).
1. Az a **Azure-alkalmazások közzététele** , amely megjelenik, jelentkezzen be a fiókjával az Azure-előfizetésében, és válassza ki **Tovább >**.
1. Az a **beállítások > Általános beállítások** lapra, válassza ki a céloldali felhőszolgáltatás az a **Felhőszolgáltatás** legördülő listából válassza ki, a kiválasztott környezet és konfigurációkkal. 
1. A **beállítások > Speciális beállítások**, válassza ki a tárfiókot használja, majd válassza ki a **Tovább >**.
1. A **diagnosztikai**, döntse el, hogy küldje el az Application Insights.
1. Válassza ki **Tovább >** megtekintheti annak összegzését, majd válassza **közzététel** üzembe helyezésének megkezdéséhez.
1. Megnyílik a Visual Studio egy tevékenységnapló ablak, követheti a folyamat állapotát:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Nem kötelező) Megszakítja az üzembe helyezési folyamat, kattintson a jobb gombbal a tevékenységnaplóban a sortételt, és válassza a **megszakítása és törlése**. Ez a parancs leállítja a telepítési folyamatot, és törli az üzembehelyezési környezetet az Azure-ból. Megjegyzés: Miután az üzembe helyezéskor elvégzik a központi telepítési környezet eltávolításához kell használnia a [az Azure portal](https://portal.azure.com).
1. (Nem kötelező) Miután elindította a szerepkörpéldányok, a Visual Studio automatikusan megjeleníti a központi telepítési környezet a **Server Explorer > Cloud Services** csomópont. Itt megtekintheti az egyes szerepkörpéldányok állapotát.
1. Üzembe helyezés után az alkalmazás eléréséhez, válassza ki a központi telepítés, ha az állapot melletti nyílra **befejezve** jelenik meg a **Azure tevékenységnapló** együtt az URL-címet. Tekintse meg az alábbi táblázat a további információk az Azure-ból egy adott típusú webalkalmazás elindítása.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>A compute emulator használatával és az alkalmazás az Azure-ban

Minden alkalmazástípus esetében is indítható el egy böngészőben a Visual Studio hibakereső funkcióját csatlakozik kiválasztásával **Debug > Start Debugging** (F5). Egy ASP.NET-webalkalmazás üres projekt először hozzá kell adnia egy `.aspx` az alkalmazás lapját, és állítsa be a kezdőlapon a webes projekthez.

Az alábbi táblázat ismerteti az Azure-ban az alkalmazás indítása részleteit:

   | Webalkalmazás-típushoz | Azure-ban |
   | --- | --- | --- |
   | ASP.NET-webalkalmazás<br/>(beleértve az MVC 2, MVC 3, MVC 4) | Válassza ki az URL-címet a **üzembe helyezési** lapján a **Azure tevékenységnapló**. |
   | Üres ASP.NET-webalkalmazás | Ha egy alapértelmezett `.aspx` az alkalmazásban lapra, jelölje be az URL-címet a **üzembe helyezési** lapján a **Azure tevékenységnapló**. Nyissa meg egy másik lapot, adja meg egy URL-címet a következő formátumot követi a böngészőben: `<deployment_url>/<page_name>.aspx` |
   | Silverlight-alkalmazás<br/>A Silverlight üzleti alkalmazás<br/>A Silverlight navigációs alkalmazás | Keresse meg az adott oldalon az alkalmazás a következő URL-cím formátumban: `<deployment_url>/<page_name>.aspx` |
    WCF-szolgáltatás alkalmazás<br/>A munkafolyamat-szolgáltatás WCF-alkalmazás | Állítsa be a `.svc` úvodní stránku fájlt a WCF-szolgáltatás projekthez. Majd keresse meg a `<deployment_url>/<service_file>.svc` |
   | Az ASP.NET dinamikus entitások<br/>Az ASP.NET dinamikus adatok Linq to SQL | A következő szakaszban leírtak szerint, frissítette a kapcsolati karakterláncot. Majd keresse meg a `<deployment_url>/<page_name>.aspx`. A "LINQ to SQL" az Azure SQL database kell használnia. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>A kapcsolati karakterlánc frissítése az ASP.NET dinamikus entitások

1. Dinamikus entitások ASP.NET webalkalmazás SQL Azure-adatbázis létrehozása (#use-an-azuresql-database-for-your-application) a korábban ismertetett módon.
1. Adja hozzá a táblákat és mezőket, amelyekre szüksége van ehhez az adatbázishoz az Azure Portalról.
1. Adjon meg egy kapcsolati karakterláncot a `web.config` a következő formátumú fájlt, és mentse a fájlt:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Frissítés a *connectionString* értéket az SQL Azure adatbázishoz tartozó ADO.NET kapcsolati karakterláncot a következőképpen:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Támogatott projektsablonok

Át, és felhőbeli szolgáltatásokban közzétett alkalmazásokhoz kell használnia az alábbi táblázat a sablonok egyikét. ASP.NET Core nem támogatott.

| Sablon csoport | Projektsablon |
| --- | --- |
| Web | ASP.NET-es webalkalmazás (.NET-keretrendszer) |
| Web | 2. az ASP.NET MVC-webalkalmazás |
| Web | 3. az ASP.NET MVC-webalkalmazás |
| Web | ASP.NET MVC4-webalkalmazás |
| Web | Üres ASP.NET-webalkalmazás (vagy a hely) |
| Web | ASP.NET MVC 2 üres webalkalmazás |
| Web | Az ASP.NET dinamikus adatok entitások webes alkalmazás |
| Web | Az ASP.NET dinamikus adatok Linq to SQL webes alkalmazás |
| Silverlight | Silverlight-alkalmazás |
| Silverlight | A Silverlight üzleti alkalmazás |
| Silverlight | A Silverlight navigációs alkalmazás |
| WCF | WCF-szolgáltatás alkalmazás |
| WCF | A munkafolyamat-szolgáltatás WCF-alkalmazás |
| Munkafolyamat | A munkafolyamat-szolgáltatás WCF-alkalmazás |

## <a name="next-steps"></a>További lépések

- [Közzététel vagy az alkalmazás üzembe helyezése az Azure a Visual Studióból előkészítése](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Hitelesítő adatok beállítása nevű](vs-azure-tools-setting-up-named-authentication-credentials.md).
