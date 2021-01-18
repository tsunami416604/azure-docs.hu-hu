---
title: Felügyelt identitások használata Azure SQL Database vagy Azure szinapszis Analytics-Azure Stream Analytics eléréséhez
description: Ez a cikk azt ismerteti, hogyan használhatók a felügyelt identitások a Azure Stream Analytics-feladatok hitelesítéséhez Azure SQL Database vagy az Azure szinapszis Analytics kimenetéhez.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 7d624f2dd2c0c9b4c7e99d5628a1d47e4303da7f
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555593"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Felügyelt identitások használata Azure SQL Database vagy Azure szinapszis Analytics eléréséhez egy Azure Stream Analytics feladatokból (előzetes verzió)

A Azure Stream Analytics támogatja a [felügyelt identitások hitelesítését](../active-directory/managed-identities-azure-resources/overview.md) a Azure SQL Database és az Azure szinapszis Analytics kimeneti nyelők számára. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó-változtatások vagy a felhasználói jogkivonatok lejárata miatti újrahitelesítés szükségességét, amely minden 90 naponként megtörténik. Ha eltávolítja a manuális hitelesítés szükségességét, a Stream Analytics üzemelő példányok teljes mértékben automatizálva lehetnek.

A felügyelt identitás egy Azure Active Directoryban regisztrált felügyelt alkalmazás, amely egy adott Stream Analytics feladatnak felel meg. A felügyelt alkalmazás használatával történik a hitelesítés egy célként megadott erőforráson. Ez a cikk bemutatja, hogyan engedélyezheti a felügyelt identitást egy Azure SQL Database vagy egy Stream Analytics-feladatokhoz tartozó Azure szinapszis analitikai kimenet (ek) számára a Azure Portalon keresztül.

## <a name="prerequisites"></a>Előfeltételek

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

A funkció használatához a következők szükségesek:

- Egy Azure Stream Analytics-feladatot.

- Egy Azure SQL Database erőforrás.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

A funkció használatához a következők szükségesek:

- Egy Azure Stream Analytics-feladatot.

- Egy Azure szinapszis Analytics SQL-készlet.

- Egy Azure Storage-fiók, amely [a stream Analytics feladatokhoz van konfigurálva](azure-synapse-analytics-output.md).

---

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Először létre kell hoznia egy felügyelt identitást a Azure Stream Analytics feladatokhoz.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Azure stream Analytics feladatot.

1. A bal oldali navigációs menüben válassza a **Konfigurálás** területen található **felügyelt identitás** elemet. Ezután jelölje be a **rendszerhez rendelt felügyelt identitás használata** melletti jelölőnégyzetet, majd válassza a **Mentés** lehetőséget.

   ![Rendszer által hozzárendelt felügyelt identitás kiválasztása](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   A Stream Analytics-feladatok identitásához tartozó egyszerű szolgáltatás a Azure Active Directoryban jön létre. Az újonnan létrehozott identitás életciklusát az Azure felügyeli. Ha a Stream Analytics feladatot törli, a társított identitást (azaz az egyszerű szolgáltatásnevet) az Azure automatikusan törli.

1. A konfiguráció mentésekor a szolgáltatásnév objektumazonosító (OID) szerepel a résztvevő AZONOSÍTÓJAként az alábbi ábrán látható módon: 

   ![Elsődleges AZONOSÍTÓként megjelenő objektumazonosító](./media/sql-db-output-managed-identity/principal-id.png)

   Az egyszerű szolgáltatásnév neve megegyezik a Stream Analytics feladatokkal. Ha például a feladatainak neve *MyASAJob*, az egyszerű szolgáltatásnév neve is *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Active Directory-rendszergazda kiválasztása

A felügyelt identitás létrehozása után ki kell választania egy Active Directory rendszergazdát.

1. Navigáljon a Azure SQL Database vagy az Azure szinapszis Analytics-erőforráshoz, és válassza ki azt a SQL Server, amelyen az adatbázis található. Az erőforrás-Áttekintés lapon a *kiszolgáló neve* mellett található SQL Server neve látható.

1. Válassza a **Active Directory rendszergazda** lehetőséget a **Beállítások** területen. Ezután válassza a **rendszergazda beállítása** lehetőséget.

   ![Active Directory felügyeleti oldal](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. A Active Directory felügyeleti lapon keressen rá egy felhasználóra vagy csoportra, hogy a rendszergazda legyen a SQL Server, majd kattintson a **kiválasztás** gombra.

   ![Active Directory adminisztrátor hozzáadása](./media/sql-db-output-managed-identity/add-admin.png)

   A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A nem kiválasztható felhasználók vagy csoportok nem választhatók ki, mert Azure Active Directory rendszergazdaként nem támogatottak. Tekintse meg a támogatott rendszergazdák listáját a **Azure Active Directory szolgáltatások és korlátozások**   című szakaszban [Azure Active Directory hitelesítés használata a SQL Database vagy az Azure szinapszis használatával történő hitelesítéshez](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations). Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) csak a portálra vonatkozik, és nem terjed ki SQL Serverra. Emellett a kiválasztott felhasználó vagy csoport az a felhasználó, aki a következő szakaszban létre tudja hozni a **tárolt adatbázis-felhasználót** .

1. A **Active Directory felügyeleti** lapon válassza a **Mentés** lehetőséget. A rendszergazda módosításának folyamata néhány percet vesz igénybe.

   A Azure Active Directory-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem lehet jelen a virtuális elsődleges adatbázisban SQL Server hitelesítési felhasználóként. Ha van ilyen, a Azure Active Directory rendszergazdai telepítés sikertelen lesz, és visszaállítja a létrehozását, ami azt jelzi, hogy a rendszergazda (név) már létezik. Mivel a SQL Server hitelesítési felhasználó nem része Azure Active Directorynak, a felhasználó nem fog tudni csatlakozni a kiszolgálóhoz Azure Active Directory hitelesítés használatával. 

## <a name="create-a-contained-database-user"></a>Tárolt adatbázis-felhasználó létrehozása

Ezután létre kell hoznia egy tárolt adatbázis-felhasználót az Azure SQL-vagy az Azure szinapszis-adatbázisban, amely a Azure Active Directory identitásra van leképezve. A tárolt adatbázis-felhasználó nem rendelkezik bejelentkezési azonosítóval az elsődleges adatbázishoz, de az adatbázishoz társított címtárban található identitáshoz rendeli. A Azure Active Directory identitás lehet egyéni felhasználói fiók vagy csoport. Ebben az esetben létre kell hoznia egy tárolt adatbázis-felhasználót a Stream Analytics feladatokhoz. 

1. Kapcsolódjon az Azure SQL vagy az Azure szinapszis-adatbázishoz SQL Server Management Studio használatával. A **Felhasználónév** egy Azure Active Directory felhasználó, aki módosítja a **felhasználó** engedélyeit. A SQL Server beállított rendszergazda egy példa. **Azure Active Directory – Universal és MFA** hitelesítés használata. 

   ![Csatlakozás az SQL Serverhez](./media/sql-db-output-managed-identity/connect-sql-server.png)

   A kiszolgáló neve eltérő lehet `<SQL Server name>.database.windows.net` a különböző régiókban. A kínai régiónak például a következőt kell használnia: `<SQL Server name>.database.chinacloudapi.cn` .
 
   Megadhat egy adott Azure SQL-vagy Azure-beli szinapszis-adatbázist, ha a **beállítások > kapcsolati tulajdonságok > kapcsolódás az adatbázishoz lehetőségre** kattint.  

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

1. Ahhoz, hogy a Microsoft Azure Active Directory ellenőrizze, hogy a Stream Analytics-feladatot elérheti-e a SQL Database, Azure Active Directory engedélyt kell adni az adatbázissal való kommunikációra. Ehhez nyissa meg a "tűzfalak és virtuális hálózat" lapot Azure Portal újra, és engedélyezze az "Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálóhoz" lehetőséget. 

   ![Tűzfal és virtuális hálózat](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analyticsi feladatok engedélyeinek megadása

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Miután létrehozott egy tárolt adatbázis-felhasználót, és hozzáférést kapott az Azure-szolgáltatásokhoz a portálon az előző szakaszban leírtak szerint, a Stream Analyticsi feladatnak rendelkeznie kell a felügyelt identitással, hogy felügyelt identitáson keresztül **csatlakozhasson** az Azure SQL Database-erőforráshoz. Javasoljuk, hogy adja meg a SELECT és INSERT engedélyeket a Stream Analytics-feladathoz, mivel a Stream Analytics munkafolyamatban később szükség lesz rájuk. A **Select** engedély lehetővé teszi a feladatoknak az Azure SQL Database-ben lévő táblához való kapcsolódásának tesztelését. Az **Insert** engedély lehetővé teszi a végpontok közötti stream Analytics lekérdezések tesztelését, miután beállította a bemenetet és az Azure SQL Database kimenetét.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Miután létrehozott egy tárolt adatbázis-felhasználót, és hozzáférést kapott az Azure-szolgáltatásokhoz a portálon az előző szakaszban leírtak szerint, a Stream Analyticsi feladatnak rendelkeznie kell engedéllyel a felügyelt identitástól az Azure szinapszis adatbázis-erőforráshoz való **kapcsolódáshoz** a felügyelt identitáson keresztül. Javasoljuk, hogy az adatbázis TÖMEGES MŰVELETEInek kiválasztása, beszúrása és felügyelete engedélyeit a Stream Analytics feladathoz adja meg, mivel a Stream Analytics munkafolyamatban később szükség lesz rájuk. A **Select** engedély lehetővé teszi a feladatoknak, hogy tesztelje a kapcsolatát az Azure szinapszis-adatbázisban lévő táblával. Az **adatbázis TÖMEGES műveleteinek** **beszúrása** és felügyelete lehetővé teszi a végpontok közötti stream Analytics lekérdezések tesztelését, miután konfigurálta a bemenetet és az Azure szinapszis-adatbázis kimenetét.

Ahhoz, hogy a felügyeleti adatbázis TÖMEGES műveletei engedélyt kapjanak, meg kell adnia minden olyan engedélyt, amely a **vezérlés alá tartozó vezérlőelemként** van megjelölve [a stream Analytics](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest&preserve-view=true#remarks) feladatra vonatkozóan. Erre az engedélyre azért van szükség, mert a Stream Analytics feladat végrehajtja a COPY utasítást, amely megköveteli az [adatbázis TÖMEGES műveleteinek és beszúrásának felügyeletét](/sql/t-sql/statements/copy-into-transact-sql).

---

Ezeket az engedélyeket SQL Server Management Studio használatával adhatja meg a Stream Analytics feladatnak. További információért lásd a GRANT (Transact-SQL) referenciát.

Ha csak egy adott táblához vagy objektumhoz kíván engedélyeket adni az adatbázisban, használja a következő T-SQL szintaxist, és futtassa a lekérdezést. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT [PERMISSION NAME] OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

Azt is megteheti, hogy a jobb gombbal az Azure SQL vagy az Azure szinapszis-adatbázisra kattint SQL Server Management Studio és kiválasztja a **tulajdonságok > engedélyeket**. Az engedélyek menüben megtekintheti a korábban hozzáadott Stream Analytics feladatot, és manuálisan is engedélyezheti vagy megtagadhatja az engedélyeket.

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Azure SQL Database vagy Azure szinapszis kimenet létrehozása

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Most, hogy beállította a felügyelt identitását, készen áll Azure SQL Database vagy Azure szinapszis kimenet hozzáadására a Stream Analytics feladatokhoz.

Győződjön meg arról, hogy létrehozott egy táblát a SQL Database a megfelelő kimeneti sémával. A tábla neve azon szükséges tulajdonságok egyike, amelyeket ki kell tölteni, amikor hozzáadja a SQL Database kimenetet a Stream Analytics feladatokhoz. Továbbá győződjön meg arról, hogy a feladatokban **kiválasztott** és **beszúrt** engedélyeket a kapcsolódás teszteléséhez és stream Analytics lekérdezések futtatásához. Ha még nem tette meg, tekintse meg az [engedélyezési stream Analytics feladatok engedélyei](#grant-stream-analytics-job-permissions) szakaszt. 

1. Lépjen vissza a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája** alatt. 

1. Válassza a **hozzáadás > SQL Database** lehetőséget. A SQL Database kimeneti fogadó kimeneti tulajdonságok ablakában válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő menüből.

1. Adja meg a többi tulajdonságot. SQL Database kimenet létrehozásával kapcsolatos további tudnivalókért tekintse meg [a SQL Database kimenet létrehozása stream Analytics](sql-database-output.md)használatával című témakört. Ha elkészült, válassza a **Mentés** lehetőséget.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Most, hogy beállította a felügyelt identitás-és Storage-fiókját, készen áll egy Azure SQL Database vagy Azure szinapszis kimenet hozzáadására a Stream Analytics feladatokhoz.

Győződjön meg arról, hogy létrehozott egy táblát az Azure szinapszis-adatbázisban a megfelelő kimeneti sémával. A tábla neve azon szükséges tulajdonságok egyike, amelyeket ki kell tölteni, amikor az Azure szinapszis kimenetét hozzáadja a Stream Analytics feladatokhoz. Továbbá győződjön meg arról, hogy a feladatokban **kiválasztott** és **beszúrt** engedélyeket a kapcsolódás teszteléséhez és stream Analytics lekérdezések futtatásához. Ha még nem tette meg, tekintse meg az [engedélyezési stream Analytics feladatok engedélyei](#grant-stream-analytics-job-permissions) szakaszt.

1. Lépjen vissza a Stream Analytics feladathoz, és navigáljon a **kimenetek** lapra a **feladatok topológiája** alatt.

1. Válassza a **hozzáadás > Azure szinapszis Analytics** lehetőséget. A SQL Database kimeneti fogadó kimeneti tulajdonságok ablakában válassza a **felügyelt identitás** lehetőséget a hitelesítési mód legördülő menüből.

1. Adja meg a többi tulajdonságot. További információ az Azure szinapszis-kimenet létrehozásáról: [Az Azure szinapszis Analytics kimenete Azure stream Analytics](azure-synapse-analytics-output.md). Ha elkészült, válassza a **Mentés** lehetőséget.

---

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

A Stream Analytics feladatokhoz létrehozott felügyelt identitást csak akkor törli a rendszer, ha a feladatot törlik. A felügyelt identitást nem lehet törölni a feladatok törlése nélkül. Ha már nem szeretné használni a felügyelt identitást, módosíthatja a kimenet hitelesítési módszerét. A felügyelt identitás továbbra is érvényben marad, amíg el nem törli a feladatot, és a rendszer akkor fogja használni, ha úgy dönt, hogy újra felügyelt identitás-hitelesítést használ.

## <a name="next-steps"></a>Következő lépések

* [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)
* [Azure Stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Az Azure szinapszis Analytics kimenete Azure Stream Analytics](azure-synapse-analytics-output.md)
