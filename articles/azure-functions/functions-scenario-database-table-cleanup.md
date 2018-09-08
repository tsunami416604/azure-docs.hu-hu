---
title: Hajtsa végre a karbantartási feladat egy adatbázist az Azure Functions használatával |} A Microsoft Docs
description: Az Azure Functions használatával, amely csatlakozik az Azure SQL Database rendszeres időközönként sorait Tisztítja a feladat ütemezését.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: a257948c97437d6045f705acb02054928d22ff89
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092869"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Csatlakozás az Azure SQL Database az Azure Functions használatával
Ez a témakör bemutatja, hogyan hozzon létre egy ütemezett feladatot, amely törli a sorokat egy Azure SQL Database egyik táblájában az Azure Functions használatával. Az új C#-szkriptfüggvény jön létre az Azure Portalon előre meghatározott időzítő eseményindító sablon alapján. Ennek támogatásához is meg kell adni egy adatbázis-kapcsolati karakterlánc, a függvényalkalmazáshoz egy alkalmazásbeállításhoz. Ebben a forgatókönyvben az adatbázison tömeges műveletet használja. 

A függvény folyamat saját kell létrehozni, olvasási, frissítési és törlési (CRUD) műveleteket egy Mobile Apps-tábla, Ehelyett használjon [Mobile Apps-kötések](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Előfeltételek

+ Ebben a témakörben egy időzítővel aktivált függvényt használ. A témakörben található lépésekkel [az Azure-ban egy időzítő által aktivált függvény létrehozása](functions-create-scheduled-function.md) egy C# verzióhoz, a függvény létrehozásához.   

+ Ez a témakör bemutatja a Transact-SQL parancsot, amely végrehajtja a műveletcsoport törlése a **SalesOrderHeader** az AdventureWorksLT mintaadatbázis táblájában. Az AdventureWorksLT mintaadatbázis létrehozásához hajtsa végre a lépéseket a témakör [egy Azure SQL database létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

A kapcsolati sztring lekérése befejezésekor létrehozott adatbázis kell [egy Azure SQL database létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
 
3. Válassza ki **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lapot.

4. Válassza ki **adatbázis kapcsolati karakterláncainak megjelenítése** , és másolja a teljes **ADO.NET** kapcsolati karakterláncot. 

    ![Másolja az ADO.NET kapcsolati karakterláncot.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati sztring beállítása 

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Akkor célszerű kapcsolati karakterláncok és egyéb titkos információinak tárolása a függvényalkalmazás-beállításokat. Alkalmazásbeállítások használatával megakadályozza a véletlen közzétételét a kód a kapcsolati karakterláncot. 

1. Keresse meg a létrehozott függvényalkalmazás [az Azure-ban egy időzítő által aktivált függvény létrehozása](functions-create-scheduled-function.md).

2. Válassza ki **platformfunkciók** > **Alkalmazásbeállítások**.
   
    ![Alkalmazásbeállítások a függvényalkalmazáshoz.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Görgessen le a **kapcsolati karakterláncok** , és adjon hozzá egy kapcsolati karakterláncot a táblázatban megadott beállításokkal.
   
    ![Adja hozzá a kapcsolati karakterláncot a függvényalkalmazás-beállításokat.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Beállítás       | Ajánlott érték | Leírás             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name (Név)**  |  sqldb_connection  | A függvény kódját a tárolt kapcsolati karakterlánc eléréséhez használt.    |
    | **Érték** | Másolt karakterláncot  | Illessze be az előző szakaszban kimásolt kapcsolati karakterláncot, és cserélje le `{your_username}` és `{your_password}` valódi értékek helyőrzőt. |
    | **Típus** | SQL Database | Használja az alapértelmezett SQL-adatbázis-kapcsolat. |   

3. Kattintson a **Save** (Mentés) gombra.

Most a C#-függvénykódot, amely csatlakozik az SQL-adatbázis is hozzáadhat.

## <a name="update-your-function-code"></a>A függvénykód módosítása

1. A portálon a függvényalkalmazásban jelölje ki az időzítő által aktivált függvény.
 
3. A meglévő C# szkriptet függvénykódot tetején adja hozzá a következő szerelvény hivatkozásokat:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >A kód ezekben a példákban olyan C#-szkript a portálról. Helyette előre lefordított C#-függvény helyi fejlesztésekor adja hozzá ezeket a hivatkozásokat a helyi projekt tartalomkiszolgálójáról.  

3. Adja hozzá a következő `using` utasítások a függvény használatával:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Cserélje le a meglévő `Run` függvény a következő kóddal:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    A mintául szolgáló parancs frissíti a `Status` oszlop a szállítási dátum alapján. Azt frissítenie kell a 32 sornyi adatot.

5. Kattintson a **mentése**, tekintse meg a **naplók** windows esetében a következő végrehajtási függvényt, és jegyezze fel a frissített sorok száma a **SalesOrderHeader** tábla.

    ![A függvény naplóinak megtekintése.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan használhatja a Functions a Logic Apps szolgáltatással való integráció más szolgáltatásokkal.

> [!div class="nextstepaction"] 
> [A Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md)

Függvények kapcsolatos további információkért lásd a következő témaköröket:

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
