---
title: "Azure SQL Database és az ASP.NET alkalmazás létrehozása |} Microsoft Docs"
description: "Ismerje meg, hogyan kérhet egy ASP.NET-alkalmazást az Azure SQL adatbázis-kapcsolat használata."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/09/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: db3be8068ef9e560614daa0e7f0dcf62467fd338
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Azure SQL Database és az ASP.NET alkalmazás létrehozása

Az [Azure Web Apps](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Az oktatóanyag bemutatja, hogyan telepítheti az adatvezérelt ASP.NET webalkalmazás az Azure-ban, és kösse össze [Azure SQL Database](../sql-database/sql-database-technical-overview.md). Amikor végzett, a Azure-beli ASP.NET alkalmazás, és csatlakozik az SQL Database.

![Közzétett ASP.NET-alkalmazást az Azure-webalkalmazásban](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * SQL-adatbázis létrehozása az Azure-ban
> * ASP.NET alkalmazás csatlakoztatása az SQL-adatbázishoz
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Stream naplók az Azure-ból a Terminálszolgáltatások számára
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
  - **ASP.NET és webfejlesztés**
  - **Azure-fejlesztés**

  ![ASP.NET és webfejlesztés és Azure-fejlesztés (Web és felhőszolgáltatások alatt)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

[A minta-projekt letöltése](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Extract (csomagolja ki) a *dotnet-sqldb-oktatóanyag – master.zip* fájlt.

A minta-projekt tartalmazza az alapvető [ASP.NET MVC](https://www.asp.net/mvc) CRUD (hozzon létre-olvasás-Módosítás-Törlés) alkalmazás használatával [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Az alkalmazás futtatása

Nyissa meg a *dotnet-sqldb-oktatóanyag – master/DotNetAppSqlDb.sln* fájlt a Visual Studióban. 

Típus `Ctrl+F5` hibakeresés nélkül az alkalmazás futtatásához. Az alkalmazás az alapértelmezett böngésző jelenik meg. Válassza ki a **hozzon létre új** hivatkozásra, és hozzon létre több *tennivaló* elemeket. 

![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Teszt a **szerkesztése**, **részletek**, és **törlése** hivatkozásokat.

Az alkalmazás egy adatbázis-környezet használatával kapcsolódni az adatbázishoz. Ez a példa az adatbázis-környezet nevű kapcsolati karakterláncot használ `MyDbConnection`. A kapcsolati karakterlánc megadása a *Web.config* fájlt, és hivatkozik a *Models/MyDatabaseContext.cs* fájlt. A kapcsolati karakterlánc nevét az Azure web app egy Azure SQL adatbázishoz való csatlakozáshoz használt az oktatóanyag későbbi részében. 

## <a name="publish-to-azure-with-sql-database"></a>Az Azure SQL Database közzététele

Az a **Megoldáskezelőben**, kattintson a jobb gombbal a **DotNetAppSqlDb** projektre, és válassza ki **közzététel**.

![Közzététel a Megoldáskezelőből](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Győződjön meg arról, hogy a **Microsoft Azure App Service** van kiválasztva, és kattintson a **Publish** (Közzététel) elemre.

![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Közzétételi megnyílik a **létrehozása az App Service** párbeszédpanel, amely segít az összes Azure-erőforrást az Azure-ban az ASP.NET webalkalmazás futtatásához szükséges.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) gombra, majd jelentkezzen be az Azure-előfizetésébe. Ha már be van jelentkezve egy Microsoft-fiókba, győződjön meg arról, hogy abban a fiókban található az előfizetése. Ha nem abban a Microsoft-fiókban van az Azure-előfizetése, amelyikbe be van jelentkezve, kattintással adja hozzá a helyes fiókot.
   
![Bejelentkezés az Azure-ba](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

Ha már bejelentkezett, ezen a panelen már létre is hozhatja az összes, az Azure-webapphoz szükséges erőforrást.

### <a name="configure-the-web-app-name"></a>A webalkalmazás nevének konfigurálása

Megtartja-e a létrehozott webalkalmazás neve, vagy módosítsa azt egy másik egyedi nevét (érvényes karakterek: `a-z`, `0-9`, és `-`). A webalkalmazás-nevet az alkalmazás használatos az alapértelmezett URL-cím (`<app_name>.azurewebsites.net`, ahol `<app_name>` a webes alkalmazás neve). A webalkalmazás nevének egyedinek kell lennie az Azure-ban minden alkalmazások között. 

![Hozzon létre az app service párbeszédpanelen](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

> [!NOTE]
> Ne kattintson **létrehozása**. Először egy későbbi lépésben egy SQL-adatbázis létrehozása.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource-group](../../includes/resource-group.md)]

A **Resource Group** (Erőforráscsoport) mellett kattintson a **New** (Új) elemre.

![Erőforráscsoport mellett az új gombra.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Nevezze el az erőforráscsoportot **myResourceGroup**.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Az **App Service Plan** (App Service-csomag) mellett kattintson a **New** (Új) elemre. 

A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanelen konfigurálja az új App Service-csomagot a következő beállításokkal:

![App Service-csomag létrehozása](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Beállítás  | Ajánlott érték | További információ |
| ----------------- | ------------ | ----|
|**App Service-csomag**| myAppServicePlan | [App Service-csomagok](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |
|**Hely**| Nyugat-Európa | [Azure-régiók](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Méret**| Ingyenes | [Tarifacsomagok](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>SQL Server-példány létrehozása

Adatbázis létrehozása előtt kell egy [Azure SQL Database logikai kiszolgáló](../sql-database/sql-database-features.md). A logikai kiszolgálók adatbázisok egy csoportját tartalmazzák, amelyeket a rendszer egy csoportként kezel.

Válassza ki **további Azure-szolgáltatások felfedezés**.

![A webapp nevének konfigurálása](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

Az a **szolgáltatások** lapra, majd a  **+**  melletti ikon **SQL-adatbázis**. 

![A szolgáltatások lapon kattintson a + SQL-adatbázis melletti ikonra.](media/app-service-web-tutorial-dotnet-sqldatabase/sql.png)

Az a **SQL-adatbázis beállítása** párbeszédpanel, kattintson a **új** melletti **SQL Server**. 

Létrejön egy egyedi kiszolgálónevet. Ez a név részeként az alapértelmezett URL-cím a logikai kiszolgálóhoz használt `<server_name>.database.windows.net`. Egyedinek kell lennie az Azure-ban minden logikai kiszolgáló példányára. Módosítsa a kiszolgáló nevét, de ebben az oktatóanyagban tartsa meg a létrehozott értéket.

Adjon hozzá egy rendszergazdai jogosultságú felhasználónevet és jelszót. Összetettségi követelményeknek, lásd: [jelszóházirend](/sql/relational-databases/security/password-policy).

Ne felejtse el ezt a felhasználónevet és jelszót. Azokat a logikai kiszolgálópéldány később kezelésére van szüksége.

![SQL Server-példány létrehozása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Kattintson az **OK** gombra. Ne zárja be a **SQL-adatbázis beállítása** még párbeszédpanel.

### <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

Az a **SQL-adatbázis beállítása** párbeszédpanel: 

* Tartsa meg az alapértelmezett generált **adatbázisnév**.
* A **kapcsolati karakterlánc nevét**, típus *MyDbConnection*. Ezt a nevet meg kell egyeznie a kapcsolati karakterláncot, amely hivatkozik a *Models/MyDatabaseContext.cs*.
* Kattintson az **OK** gombra.

![SQL-adatbázis konfigurálása](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

A **létrehozása az App Service** párbeszédpanel jeleníti meg az erőforrások létrehozott. Kattintson a **Create** (Létrehozás) gombra. 

![a létrehozott erőforrások](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

Miután a varázsló létrehozta az Azure-erőforrások, az Azure-bA közzéteszi az ASP.NET-alkalmazás. Az alapértelmezett böngésző indították el a telepített alkalmazás URL-címét. 

Adjon hozzá néhány Tennivalólista elemein.

![Közzétett ASP.NET-alkalmazást az Azure-webalkalmazásban](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Gratulálunk! Az adatvezérelt ASP.NET-alkalmazás fut élő az Azure App Service-ben.

## <a name="access-the-sql-database-locally"></a>Hozzáférés az SQL-adatbázis helyi

A Visual Studio lehetővé teszi, hogy vizsgálatát, és az új SQL-adatbázis egyszerűen a kezelése a **SQL Server Object Explorer**.

### <a name="create-a-database-connection"></a>Adatbázis-kapcsolat létrehozása

Az a **nézet** menü **SQL Server Object Explorer**.

Felső részén **SQL Server Object Explorer**, kattintson a **SQL-kiszolgáló hozzáadása** gombra.

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Az a **Connect** párbeszédpanelen bontsa ki a **Azure** csomópont. Az SQL-adatbázis példányainak az Azure-ban az itt felsorolt.

Válassza ki a korábban létrehozott SQL-adatbázis. A korábban létrehozott kapcsolat a program automatikusan kitölti a lap alján.

Írja be a korábban létrehozott adatbázis-rendszergazda jelszavát, és kattintson a **Connect**.

![A Visual Studio adatbázis-kapcsolat konfigurálása](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>A számítógép az ügyfél-kapcsolat engedélyezése

A **hozzon létre egy új tűzfalszabályt** párbeszédpanel már meg van nyitva. Alapértelmezés szerint az SQL Database-példányt csak Azure-szolgáltatások, például az Azure-webalkalmazásban kapcsolatokat engedélyez. Kapcsolódás saját adatbázishoz, hozzon létre egy tűzfalszabályt SQL-adatbázispéldány. A tűzfalszabály lehetővé teszi, hogy a helyi számítógépen a nyilvános IP-címét.

A párbeszédpanel már ki van töltve, a számítógép nyilvános IP-címmel.

Győződjön meg arról, hogy **a ügyfél IP-cím hozzáadása** van kiválasztva, és kattintson a **OK**. 

![SQL Database-példányt a tűzfal beállítása](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio befejezi a tűzfal beállítása a SQL Database-példányt, ha a kapcsolat megjelennek **SQL Server Object Explorer**.

Itt végezheti a leggyakoribb adatbázis műveletek, például a lekérdezések futtatása hozhat létre, nézetek és tárolt eljárások, és több. 

Bontsa ki a kapcsolat > **adatbázisok** > **&lt;az adatbázis >** > **táblák**. Kattintson a jobb gombbal a `Todoes` tábla, és válassza ki **adatok megtekintéséhez**. 

![Fedezze fel az SQL adatbázis-objektumok](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Frissítse app Code First áttelepítést

Az adatbázis és a webes alkalmazást az Azure-ban frissíti használhatja a Visual Studio a jól ismert eszközökkel. Ebben a lépésben segítségével Code First áttelepítést az Entity Framework megváltoztatja az adatbázis-séma, és tegye közzé az Azure-bA.

Entity Framework Code First áttelepítést használatával kapcsolatos további információkért lásd: [Ismerkedés az Entity Framework 6 Code First MVC 5-öt használó](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Az adatmodell frissítése

Nyissa meg _Models\Todo.cs_ kód-szerkesztőben. A következő tulajdonság hozzáadása a `ToDo` osztály:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First áttelepítést helyileg történő futtatása

Futtassa a frissítések a helyi adatbázis néhány parancsokat. 

Az a **eszközök** menüben kattintson a **NuGet-Csomagkezelő** > **Csomagkezelő konzol**.

A Package Manager Console ablakban Code First áttelepítést engedélyezése:

```PowerShell
Enable-Migrations
```

Vegye fel az áttelepítés:

```PowerShell
Add-Migration AddProperty
```

A helyi adatbázis frissítése:

```PowerShell
Update-Database
```

Típus `Ctrl+F5` az alkalmazás futtatásához. A Szerkesztés, részletek tesztelése, és hivatkozások létrehozása.

Ha az alkalmazás betölt nem jelenik meg hibaüzenet, Code First áttelepítést sikeresen befejeződött. Azonban a lap továbbra is ugyanúgy az alkalmazáslogikát még nem használja ezt az új tulajdonságot. 

### <a name="use-the-new-property"></a>Az új tulajdonsággal

Néhány módosítást használata a kódban a `Done` tulajdonság. Az egyszerűség kedvéért ebben az oktatóanyagban, csak fogja módosítani a `Index` és `Create` nézeteket, művelet: a tulajdonság.

Nyissa meg _Controllers\TodosController.cs_.

Keresés a `Create()` metódusa 52. sor, és adja hozzá `Done` a tulajdonságok listájának a `Bind` attribútum. Amikor elkészült, a `Create()` metódus-aláírás néz ki a következő kódot:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Nyissa meg _Views\Todos\Create.cshtml_.

A Razor kódban kell megjelennie a `<div class="form-group">` elem által használt `model.Description`, és majd egy másik `<div class="form-group">` elem által használt `model.CreatedDate`. Ez a két elem követő hozzáadása egy másik `<div class="form-group">` elem által használt `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Nyissa meg _Views\Todos\Index.cshtml_.

Keresse meg az üres `<th></th>` elemet. Ez az elem felett adja hozzá a következő Razor-kódot:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Keresés a `<td>` elem, amely tartalmazza a `Html.ActionLink()` segédmódszereket. _Fent_ ez `<td>`, adja hozzá egy másik `<td>` Razor kódot a következő elem:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Ez minden szeretne látni a változtatásokat a `Index` és `Create` nézetek. 

Típus `Ctrl+F5` az alkalmazás futtatásához.

Ezután egy teendő hozzáadása és ellenőrzése **végzett**. Ezután azt kell jelennek meg az Ön újragondolt befejezett elemként. Ne feledje, hogy a `Edit` nézet nem jeleníti meg a `Done` mezőben, mert nem módosítja a `Edit` nézet.

### <a name="enable-code-first-migrations-in-azure"></a>Engedélyezze a Code First áttelepítést az Azure-ban

Most, hogy a kód módosítása működéséről, beleértve az adatbázis az áttelepítés akkor tegye közzé az Azure-webalkalmazásban, és frissítse az SQL-adatbázis Code First áttelepítést túl.

Fentiekhez hasonló, kattintson jobb gombbal a projektre, és válassza ki **közzététel**.

Kattintson a **beállítások** a Közzététel varázsló megnyitásához.

![Nyissa meg a közzétételi beállítások](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

A varázslóban kattintson **következő**.

Győződjön meg arról, hogy az SQL-adatbázis kapcsolati karakterlánca nem üres **MyDatabaseContext (MyDbConnection)**. Előfordulhat, hogy kell kiválasztania a **myToDoAppDb** adatbázis a legördülő listából. 

Válassza ki **hajtható végre Code First áttelepítést (fut az alkalmazás indítása)**, majd kattintson a **mentése**.

![Az Azure web app alkalmazásban Code First áttelepítést engedélyezése](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>A változtatások közzététele

Most, hogy Code First áttelepítést az Azure web app alkalmazásban engedélyezve van, a kód változtatások közzététele.

A közzétételi oldalon kattintson a **Publish** (Közzététel) elemre.

Próbálja meg újból hozzáadni a Tennivalólista elemein, és válassza ki **végzett**, és Ön újragondolt befejezett elemként jelenniük.

![Azure-webalkalmazásban kód első áttelepítés után](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

A meglévő Tennivalólista elemein továbbra is megjelennek. Ha az ASP.NET-alkalmazás közzétételéhez meglévő az SQL-adatbázis adatai nem elveszett. Code First áttelepítést is, csak a Adatséma változik, és a meglévő adatok sértetlenek.


## <a name="stream-application-logs"></a>Az adatfolyam alkalmazásnaplók

Nyomkövetési üzenet az Azure-webalkalmazásban közvetlenül a Visual Studio adatfolyam.

Nyissa meg _Controllers\TodosController.cs_.

Minden egyes művelethez kezdődik-e a `Trace.WriteLine()` metódust. Ez a kód bemutatják a nyomkövetési üzenetek hozzáadása az Azure-webalkalmazásban kerül.

### <a name="open-server-explorer"></a>Nyissa meg a Server Explorer

Az a **nézet** menü **Server Explorer**. Naplózás konfigurálása az Azure-webalkalmazásban a **Server Explorer**. 

### <a name="enable-log-streaming"></a>Adatfolyamként-napló

A **Server Explorer**, bontsa ki a **Azure** > **App Service**.

Bontsa ki a **myResourceGroup** erőforráscsoport, létrehozta az Azure web app kezdeti létrehozásakor.

Kattintson a jobb gombbal az Azure-webalkalmazásban, és válassza ki **folyamatos átviteli naplók megtekintése**.

![Adatfolyamként-napló](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

A naplók mostantól a folyamatos átviteli a **kimeneti** ablak. 

![A kimeneti ablakban adatfolyam-napló](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Azonban nem jelennek meg a nyomkövetési üzenetek még. Meg mert először kiválasztásakor **folyamatos átviteli naplók megtekintése**, az Azure-webalkalmazásban beállítja a nyomkövetési szint beállítása azokhoz a `Error`, amely csak naplózza hibaesemények (az a `Trace.TraceError()` metódus).

### <a name="change-trace-levels"></a>Nyomkövetési szintek módosítása

Ha módosítani szeretné a nyomkövetési szintek többi nyomkövetési üzenet kimeneti, lépjen vissza **Server Explorer**.

Az Azure-webalkalmazás ismét gombbal és válassza ki **nézetbeállítások**.

Az a **Alkalmazásnaplózást (fájlrendszer)** legördülő menüből válassza **részletes**. Kattintson a **Save** (Mentés) gombra.

![A részletes nyomkövetési szint módosítása](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Kísérletezhet, és hogy milyen típusú üzenetek jelennek meg az egyes különböző nyomkövetési szintek. Például a **információkat** szint továbbít minden által létrehozott naplók `Trace.TraceInformation()`, `Trace.TraceWarning()`, és `Trace.TraceError()`, de nem által létrehozott naplók `Trace.WriteLine()`.
>
>

A böngészőben nyissa meg újra a webes alkalmazás *http://&lt;az alkalmazás neve >. azurewebsites.net*, ismételje meg az Azure-ban a Tennivalók listája alkalmazás körül kattint. A nyomkövetési üzenetek most részére a **kimeneti** ablak a Visual Studióban.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Állítsa le az adatfolyam-napló

A napló-adatfolyam-szolgáltatás leállításához kattintson a **figyelés leállításának** gombra a **kimeneti** ablak.

![Állítsa le az adatfolyam-napló](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Lépjen a [Azure-portálon](https://portal.azure.com) létrehozott webalkalmazás megjelenítéséhez. 



A bal oldali menüben kattintson az **App Service** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

A webalkalmazás lapon rendelkezik ki. 

Alapértelmezés szerint a portál megjeleníti a **áttekintése** lap. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán a lapok megnyithatja a különböző konfigurációs lapok megjelenítése. 

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * SQL-adatbázis létrehozása az Azure-ban
> * ASP.NET alkalmazás csatlakoztatása az SQL-adatbázishoz
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Stream naplók az Azure-ból a Terminálszolgáltatások számára
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
