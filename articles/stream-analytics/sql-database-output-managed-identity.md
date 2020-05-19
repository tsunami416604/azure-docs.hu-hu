---
title: Felügyelt identitások használata a Azure SQL Database-Azure Stream Analytics eléréséhez
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok Azure SQL DB-kimenetre való hitelesítéséhez.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598240"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Felügyelt identitások használata Azure SQL Database Azure Stream Analytics feladatokból való eléréséhez (előzetes verzió)

Azure Stream Analytics támogatja a [felügyelt identitások hitelesítését](../active-directory/managed-identities-azure-resources/overview.md) Azure SQL Database kimeneti mosogatók esetében. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó-változtatások vagy a felhasználói jogkivonatok lejárata miatti újrahitelesítés szükségességét, amely minden 90 naponként megtörténik. Ha eltávolítja a manuális hitelesítés szükségességét, a Stream Analytics üzemelő példányok teljes mértékben automatizálva lehetnek.

A felügyelt identitás egy Azure Active Directoryban regisztrált felügyelt alkalmazás, amely egy adott Stream Analytics feladatnak felel meg. A felügyelt alkalmazás használatával történik a hitelesítés egy célként megadott erőforráson. Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitást egy Stream Analytics-feladatokhoz tartozó Azure SQL Database kimenet (ek) számára a Azure Portalon keresztül.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a szolgáltatáshoz a következők szükségesek:

- Egy Azure Stream Analytics-feladatot.

- Egy Azure SQL Database erőforrás.

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Először létre kell hoznia egy felügyelt identitást a Azure Stream Analytics feladatokhoz.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Azure stream Analytics feladatot.

1. A bal oldali navigációs menüben válassza a **Konfigurálás**területen található **felügyelt identitás** elemet. Ezután jelölje be a **rendszerhez rendelt felügyelt identitás használata** melletti jelölőnégyzetet, majd válassza a **Mentés**lehetőséget.

   ![Rendszer által hozzárendelt felügyelt identitás kiválasztása](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   A Stream Analytics-feladatok identitásához tartozó egyszerű szolgáltatás a Azure Active Directoryban jön létre. Az újonnan létrehozott identitás életciklusát az Azure felügyeli. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli. 

1. A konfiguráció mentésekor a szolgáltatásnév objektumazonosító (OID) szerepel a résztvevő AZONOSÍTÓJAként az alábbi ábrán látható módon: 

   ![Elsődleges AZONOSÍTÓként megjelenő objektumazonosító](./media/sql-db-output-managed-identity/principal-id.png)

   Az egyszerű szolgáltatásnév neve megegyezik a Stream Analytics feladatokkal. Ha például a feladatainak neve *MyASAJob*, az egyszerű szolgáltatásnév neve is *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Active Directory-rendszergazda kiválasztása

A felügyelt identitás létrehozása után ki kell választania egy Active Directory rendszergazdát.

1. Navigáljon a Azure SQL Database erőforráshoz, és válassza ki azt a SQL Server, amelyen az adatbázis található. Az erőforrás-Áttekintés lapon a *kiszolgáló neve* mellett található SQL Server neve látható. 

1. Válassza a **Active Directory rendszergazda** lehetőséget a **Beállítások**területen. Ezután válassza a **rendszergazda beállítása**lehetőséget. 

   ![Active Directory felügyeleti oldal](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. A Active Directory felügyeleti lapon keressen rá egy felhasználóra vagy csoportra, hogy a rendszergazda legyen a SQL Server, majd kattintson a **kiválasztás**gombra.  

   ![Active Directory adminisztrátor hozzáadása](./media/sql-db-output-managed-identity/add-admin.png)

1. A **Active Directory felügyeleti** lapon válassza a **Mentés** lehetőséget. A rendszergazda módosításának folyamata néhány percet vesz igénybe.  

## <a name="create-a-database-user"></a>Adatbázis-felhasználó létrehozása

Ezután létre kell hoznia egy tárolt adatbázis-felhasználót a SQL Database, amely a Azure Active Directory identitásra van leképezve. A tárolt adatbázis-felhasználó nem rendelkezik bejelentkezési azonosítóval a Master adatbázishoz, de az adatbázishoz társított címtárban található identitáshoz rendeli. A Azure Active Directory identitás lehet egyéni felhasználói fiók vagy csoport. Ebben az esetben létre kell hoznia egy tárolt adatbázis-felhasználót a Stream Analytics feladatokhoz. 

1. Kapcsolódjon az SQL-adatbázishoz SQL Server Management Studio használatával. A **Felhasználónév** egy Azure Active Directory felhasználó, aki módosítja a **felhasználó** engedélyeit. A SQL Server beállított rendszergazda egy példa. **Azure Active Directory – Universal és MFA** hitelesítés használata. 

   ![Csatlakozás az SQL Serverhez](./media/sql-db-output-managed-identity/connect-sql-server.png)

   A kiszolgáló neve eltérő lehet `<SQL Server name>.database.windows.net` a különböző régiókban. A kínai régiónak például a következőt kell használnia: `<SQL Server name>.database.chinacloudapi.cn` .
 
   Megadhat egy adott SQL Database a **beállítások > kapcsolati tulajdonságok > az adatbázishoz való kapcsolódás lehetőséggel**.  

   ![SQL Server a kapcsolatok tulajdonságai](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Amikor első alkalommal kapcsolódik a szolgáltatáshoz, a következő ablak jelenhet meg:

   ![Új tűzfalszabály ablak](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Ha igen, lépjen a SQL Server erőforrásra a Azure Portal. A **Biztonság** szakaszban nyissa meg a **tűzfalak és a virtuális hálózat** lapot. 
   1. Adjon hozzá egy új szabályt a szabály nevével.
   1. Használja a *from* IP címet a *Start IP-* címhez tartozó **Új tűzfalszabály** ablakában.
   1. A *záró IP*-címhez használja az **Új tűzfalszabály** ablakának *IP-* címét. 
   1. Válassza a **Mentés** lehetőséget, majd próbálkozzon újra a SQL Server Management Studio való kapcsolódással. 

1. A csatlakozás után hozza létre a tárolt adatbázis-felhasználót. A következő SQL-parancs egy olyan tárolt adatbázis-felhasználót hoz létre, amelynek a neve megegyezik a Stream Analytics feladatokkal. Ügyeljen rá, hogy a zárójelek szerepeljenek a *ASA_JOB_NAMEban*. Használja a következő T-SQL szintaxist, és futtassa a lekérdezést. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analyticsi feladatok engedélyeinek megadása

A Stream Analyticsi feladatokhoz a felügyelt identitás jogosult a SQL Database-erőforráshoz való **kapcsolódásra** . A legvalószínűbb, hogy a Stream Analytics-feladatok olyan parancsokat futtathatnak, mint például a **Select**. Ezeket az engedélyeket SQL Server Management Studio használatával adhatja meg a Stream Analytics feladatnak. További információért lásd a [Grant (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) referenciát.

Azt is megteheti, hogy a jobb gombbal a SQL Server Management Studio található SQL-adatbázisra kattint, és kiválasztja a **tulajdonságok > engedélyeket**. Az engedélyek menüben megtekintheti a korábban hozzáadott Stream Analytics feladatot, és manuálisan is engedélyezheti vagy megtagadhatja az engedélyeket.

## <a name="create-an-azure-sql-database-output"></a>Azure SQL Database kimenet létrehozása

Most, hogy beállította a felügyelt identitását, készen áll a Azure SQL Database kimenetként való hozzáadására a Stream Analytics feladatokhoz.

1. Lépjen vissza a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája**alatt. 

1. Válassza a **hozzáadás > SQL Database**lehetőséget. A SQL Database kimeneti fogadó kimeneti tulajdonságok ablakában válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő menüből.

1. Adja meg a többi tulajdonságot. SQL Database kimenet létrehozásával kapcsolatos további tudnivalókért tekintse meg [a SQL Database kimenet létrehozása stream Analytics](stream-analytics-define-outputs.md#sql-database)használatával című témakört. Ha elkészült, válassza a **Mentés**lehetőséget. 

## <a name="next-steps"></a>További lépések

* [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)
* [Azure Stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md)
