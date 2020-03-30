---
title: Adatbázis-tisztítási feladat végrehajtása az Azure Functions használatával
description: Az Azure Functions használatával ütemezhet egy feladatot, amely az Azure SQL Database-hez csatlakozik a sorok rendszeres karbantartásához.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 2e3f53943d45e90b8aff8e386ce8d0e28670673f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366809"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions használata azure SQL-adatbázishoz való csatlakozáshoz

Ez a cikk bemutatja, hogyan azure functions használatával hozzon létre egy ütemezett feladat, amely csatlakozik egy Azure SQL-adatbázis vagy az Azure SQL felügyelt példány. A függvénykód megtisztítja az adatbázis tábláinak sorait. Az új C# függvény egy előre definiált időzítő-eseményindító sablon alapján jön létre a Visual Studio 2019-ben. A forgatókönyv támogatásához be kell állítania egy adatbázis-kapcsolati karakterláncot alkalmazásbeállításként a függvényalkalmazásban. Az Azure SQL felügyelt példány a nyilvános végpont ot kell [engedélyeznie](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) ahhoz, hogy az Azure Functions-ből kapcsolódjon. Ebben a forgatókönyvben egy tömeges műveletet használ az adatbázis ellen. 

Ha ez az első tapasztalat a C# függvényekkel való munka során, olvassa el az [Azure Functions C# fejlesztői hivatkozást.](functions-dotnet-class-library.md)

## <a name="prerequisites"></a>Előfeltételek

+ Hajtsa végre az [első függvény létrehozása a Visual Studio használatával](functions-create-your-first-function-visual-studio.md) az első függvény létrehozása a 2.x-es vagy a futásidejű verzió újabb verzióját megcélozó helyi függvényalkalmazás létrehozásához című cikklépéseit. A projektet is közzé kell tennie egy azure-beli függvényalkalmazásban.

+ Ez a cikk egy Transact-SQL parancsot mutat be, amely tömeges tisztítási műveletet hajt végre az AdventureWorksLT mintaadatbázis **SalesOrderHeader** táblájában. Az AdventureWorksLT mintaadatbázis létrehozásához hajtsa végre az [Azure SQL-adatbázis létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md)című cikkben ismertetett lépéseket.

+ A rövid útmutatóhoz használt számítógép nyilvános IP-címéhez [kiszolgálószintű tűzfalszabályt](../sql-database/sql-database-get-started-portal-firewall.md) kell hozzáadnia. Erre a szabályra azért van szükség, hogy a helyi számítógépről hozzá lehessen férni az SQL-adatbázispéldányhoz.  

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Be kell szereznie a létrehozott adatbázis kapcsolati karakterláncát, amikor befejezte [az Azure SQL-adatbázis létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md)című befejeződést.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza a bal oldali menü **SQL-adatbázisok parancsát,** majd az adatbázist az **SQL-adatbázisok** lapon.

1. Válassza **a Kapcsolati karakterláncok** lehetőséget a **Beállítások csoportban,** és másolja a teljes **ADO.NET** kapcsolati karakterláncot. Az Azure SQL felügyelt példány másolása kapcsolati karakterlánc nyilvános végponthoz.

    ![Másolja az ADO.NET kapcsolati sztringet.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati sztring beállítása

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. A legjobb biztonsági gyakorlat, kapcsolati karakterláncok és egyéb titkos kulcsok a függvényalkalmazás beállításait tárolja. Az alkalmazásbeállítások használata megakadályozza a kapcsolati karakterlánc véletlen felfedését a kóddal. Közvetlenül a Visual Studióból érheti el a függvényalkalmazás alkalmazásbeállításait.

Az alkalmazást korábban már közzétette az Azure-ban. Ha még nem tette meg, [tegye közzé a függvényalkalmazást az Azure-ban.](functions-develop-vs.md#publish-to-azure)

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás-projektre, és válassza**az Azure App Service-beállítások** **közzététele** > parancsot. Válassza **a Hozzáadás lehetőséget,** az Új **alkalmazásbeállítás neve**mezőben írja be `sqldb_connection`a parancsot, és válassza az **OK**gombot.

    ![A függvényalkalmazás alkalmazásbeállításai.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Az új **sqldb_connection** beállításban illessze be az előző szakaszban másolt `{your_password}` kapcsolati karakterláncot a **Helyi** mezőbe, és cserélje le és cserélje le `{your_username}` a helyőrzőket valós értékekre. Válassza **az Érték beszúrása helyiből** lehetőséget a frissített érték **Távoli** mezőbe másolásához, majd kattintson az **OK gombra.**

    ![SQL-kapcsolati karakterlánc-beállítás hozzáadása.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    A kapcsolati karakterláncok az Azure-ban **(Remote)** titkosítottan tárolódnak. A titkos kulcsok kiszivárgásának elkerülése érdekében a local.settings.json projektfájlt (**helyi**) ki kell zárni a forrásellenőrzésből, például .gitignore fájl használatával.

## <a name="add-the-sqlclient-package-to-the-project"></a>SqlClient-csomag hozzáadása a projekthez

Hozzá kell adnia az SqlClient-könyvtárat tartalmazó NuGet csomagot. Ez az adatelérési kódtár egy SQL-adatbázishoz való csatlakozáshoz szükséges.

1. Nyissa meg a helyi függvényalkalmazás-projektet a Visual Studio 2019-ben.

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás-projektre, és válassza **a NuGet-csomagok kezelése parancsot.**

1. A **Tallózás** fülön keresse meg a(z) ```System.Data.SqlClient``` elemet, majd válassza ki.

1. A **System.Data.SqlClient** lapon válassza `4.5.1` a verzió elemet, majd kattintson a **Telepítés gombra.**

1. A telepítés befejezése után tekintse át a módosításokat, majd kattintson az **OK** gombra az **Előnézet** ablak bezárásához.

1. Ha megjelenik egy **License Acceptance** (Licenc elfogadása) ablak, kattintson az **I Accept** (Elfogadom) gombra.

Most hozzáadhatja a C# függvénykódot, amely csatlakozik az SQL-adatbázishoz.

## <a name="add-a-timer-triggered-function"></a>Időzítő által aktivált hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás-projektre, és válassza az**Új Azure-függvény** **hozzáadása** > parancsot.

1. Ha az **Azure Functions** sablon ki van `DatabaseCleanup.cs` jelölve, nevezze el az új elemet valami hasonlónak, és válassza a **Hozzáadás**lehetőséget.

1. Az **Új Azure-függvény** párbeszédpanelen válassza az **Időzítő eseményindító** lehetőséget, majd **az OK gombot.** Ez a párbeszédpanel kódfájlt hoz létre az időzítő által aktivált funkcióhoz.

1. Nyissa meg az új kódfájlt, és adja hozzá a következőket a fájl tetején lévő utasítások használatával:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Cserélje le `Run` a meglévő függvényt a következő kódra:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Ez a függvény 15 másodpercenként `Status` fut, hogy frissítse az oszlopot a szállítási dátum alapján. Ha többet szeretne megtudni az időzítő eseményindítóról, olvassa el [az Időzítő eseményindító az Azure Functionshez című témakört.](functions-bindings-timer.md)

1. A függvényalkalmazás elindításához nyomja le az **F5** billentyűt. Az [Azure Functions Core Tools](functions-develop-local.md) végrehajtási ablak a Visual Studio mögött nyílik meg.

1. Az indítás után 15 másodperccel a függvény fut. Nézze meg a kimenetet, és jegyezze fel a **SalesOrderHeader** táblában frissített sorok számát.

    ![Tekintse meg a függvénynaplókat.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Az első végrehajtáskor 32 adatsort kell frissítenie. A futtatások után nincs adatsor frissítése, hacsak nem módosítja a SalesOrderHeader tábla adatait, hogy a `UPDATE` kimutatás további sorokat jelöljön ki.

Ha azt tervezi, hogy közzéteszi ezt `TimerTrigger` a [funkciót,](functions-develop-vs.md#publish-to-azure)ne feledje, hogy módosítsa az attribútumot egy ésszerű cron [ütemezés,](functions-bindings-timer.md#ncrontab-expressions) mint minden 15 másodpercben.

## <a name="next-steps"></a>További lépések

Ezután megtanulják, hogyan kell használni. A Logic Apps alkalmazásokkal integrálható más szolgáltatásokkal.

> [!div class="nextstepaction"]
> [Logikai alkalmazásokkal integrálható függvény létrehozása](functions-twitter-email.md)

A Függvényekről további információt az alábbi cikkekben talál:

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
