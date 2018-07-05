---
title: Naplózás az Azure SQL Data Warehouse |} A Microsoft Docs
description: Ismerje meg a naplózást és az Azure SQL Data Warehouse naplózásának beállítása.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 306032ece4feda0e8132db1e95c4a229472e6c04
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2018
ms.locfileid: "34643498"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse naplózása

Ismerje meg a naplózást és az Azure SQL Data Warehouse naplózásának beállítása.

## <a name="what-is-auditing"></a>Mi a naplózás?
Naplózás az SQL Data Warehouse lehetővé teszi az adatbázist a naplózási események jelentkezzen be az Azure Storage-fiók rekord. Naplózás segíthet a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági problémákat.

A naplózási eszközök engedélyezése, és elősegítik a megfelelőségi előírásoknak való megfelelést, de nem garantálják a megfelelőség. Az Azure-ral kapcsolatos további információkat a támogatási szabványoknak való megfelelés programokat, tekintse meg a [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Naplózási alapjai
Az SQL Data Warehouse-adatbázis naplózási szolgáltatásával lehetővé teszi:

* **Megőrzése** auditnaplót kiválasztott események. Megadhatja, hogy az adatbázis-műveleteket naplózni kívánt kategóriáit.
* **A jelentés** az adatbázis-tevékenységeket. Előre konfigurált jelentéseket és a egy irányítópult segítségével gyorsan használatba tevékenység és az események naplózásához.
* **Elemezheti** jelentéseket. Megtalálhatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

Auditnaplók vannak tárolva az Azure storage-fiókjában. Definiálhat egy naplózási naplómegőrzési időtartamot.


## <a id="subheading-4"></a>Adja meg a kiszolgálószintű és adatbázisszintű naplózási házirend

A naplózási házirend egy adott adatbázis vagy a kiszolgáló alapértelmezett szabályzatként adható meg:

* A kiszolgáló házirend **vonatkozik az összes meglévő és újonnan létrehozott adatbázisok** a kiszolgálón.

* Ha *kiszolgálószintű blobnaplózás engedélyezve van*, azt *mindig vonatkozik, az adatbázis*. Az adatbázis naplózva lesz, függetlenül az adatbázis naplózási beállításait.

* Az adatbázis naplózásának engedélyezése, a kiszolgálón, egyrészt does *nem* bírálja felül, vagy módosíthatja a kiszolgálószintű blobnaplózás beállításait. Mindkét naplózások egymás mellett fog létezik. Más szóval az adatbázis kétszer párhuzamosan; rögzítése egyszer, a kiszolgáló házirend és egyszer az adatbázis-házirendet.

> [!NOTE]
> Azt javasoljuk, hogy engedélyezze **csak a kiszolgálószintű blobnaplózás** és hagyja meg az adatbázisszintű naplózást az összes adatbázis le van tiltva.
> Kerülendő, kivéve, ha engedélyezi a kiszolgálónaplózás és -adatbázis naplózási szolgáltatásával együtt:
> * Egy másik használni kívánt *tárfiók* vagy *megőrzési időszak* egy adott adatbázis számára.
> * Eseménytípusok vagy egy adott adatbázis, amely eltér a többi a kiszolgálón lévő adatbázisokhoz kategóriáit naplózni kívánt. Előfordulhat például, hogy tábla Beszúrások, amelyet csak egy adott adatbázishoz kell naplózni kell.
> * A Fenyegetésészlelés, amely jelenleg használni kívánt adatbázis adatbázisszintű naplózása csak támogatott.
>


## <a id="subheading-5"></a>Állítsa be a kiszolgálószintű naplózást az összes adatbázis

A kiszolgáló naplózási házirend vonatkozik **minden meglévő és újonnan létrehozott adatbázisok** a kiszolgálón.

Az alábbi szakasz ismerteti a konfigurációt, a naplózás az Azure portal használatával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Nyissa meg a **az SQL server** naplózni kívánt (fontos, győződjön meg arról, hogy az SQL server, nem egy adott adatbázis/DW jelenik meg). Az a **biztonsági** menüjében válassza **naplózás és Fenyegetésészlelés**.

    ![Navigációs ablaktábla][6]
4. Az a *naplózás és Fenyegetésészlelés* panelen a **naplózási** kiválasztása **ON**. A naplózási házirend érvényes lesz az összes meglévő és újonnan létrehozott adatbázisok ezen a kiszolgálón.

    ![Navigációs ablaktábla][7]
5. Megnyitásához a **naplózási naplók tárolási** panelen válassza ki **tárolási adatok**. Válassza ki, vagy hozzon létre az Azure storage-fiók, ahol naplók menti a rendszer, és válassza a megőrzési időszak (régi naplókat törli). Ezután kattintson az **OK** gombra.

    ![Navigációs ablaktábla][8]

    > [!IMPORTANT]
    > Kiszolgálószintű auditnaplók írt **hozzáfűző Blobok** egy Azure Blob Storage, Azure-előfizetésében.
    >
    > * **A Premium Storage** jelenleg **nem támogatott** a hozzáfűző Blobok.
    > * **A virtuális hálózatok közötti tárolási** jelenleg **nem támogatott**.

8. Kattintson a **Save** (Mentés) gombra.



## <a id="subheading-2"></a>Állítsa be egy önálló adatbázis adatbázisszintű naplózása

Meghatározhatja az adott adatbázishoz vagy a kiszolgáló alapértelmezett szabályzatként a naplózási házirend.

Erősen ajánlott kiszolgálószintű naplózás, és nem adatbázisszintű naplózást, leírtak szerint [kiszolgálószintű vagy adatbázisszintű naplózási házirend definiálása](#subheading-4)

Naplózási ellenőrzés naplózás használata előtt egy ["Alacsonyabb szintű ügyfelek"](sql-data-warehouse-auditing-downlevel-clients.md).


1. Indítsa el a [az Azure portal](https://portal.azure.com).
2. Lépjen a **beállítások** az SQL Data warehouse naplózni kívánt. Válassza ki **naplózás és Fenyegetésészlelés**.

    ![][1]
3. Ezután a kattintva naplózásának engedélyezése a **ON** gombra.

    ![][3]
4. Jelölje ki a naplózási konfiguráció panelen **tárolási adatok** a vizsgálati naplók tárolási panel megnyitásához. Válassza ki az Azure storage-fiókját a naplók és a megőrzési időtartam.
    >[!TIP]
    >Ugyanazt a tárfiókot az összes naplózott adatbázishoz használatával a legtöbbet hozhassa ki az előre konfigurált jelentéseket sablonokat.

    ![][4]

5. Kattintson a **OK** gombra a részletek tárolókonfiguráció mentéséhez.
6. A **esemény NAPLÓZÁSA által**, kattintson a **sikeres** és **hiba** összes esemény naplózása, vagy válassza ki az egyes kategóriák.
7. Ha naplózási adatbázis konfigurál, szükség lehet a kapcsolati karakterláncot, az ügyfél ellenőrizze a megfelelő rögzített adatok naplózását, az megváltoztatására. Ellenőrizze a [módosíthatja a kiszolgáló teljes Tartománynevet a kapcsolati karakterláncban](sql-data-warehouse-auditing-downlevel-clients.md) a témakör korábbi verziójú ügyfél-kapcsolatokhoz.
8. Kattintson az **OK** gombra.

## <a id="subheading-3"></a>Elemezheti a vizsgálati naplók és jelentések

###<a name="server-level-policy-audit-logs"></a>Kiszolgálói szintű csoportházirend-auditnaplók
Kiszolgálószintű auditnaplók írt **hozzáfűző Blobok** egy Azure Blob Storage, Azure-előfizetésében. Mentés nevű tárolóban lévő blob fájlokat gyűjteményeként **sqldbauditlogs**.

A tároló mappa a hierarchiával kapcsolatos további részletekért elnevezési konvenciók és a napló formátuma, tekintse meg a [Blob auditálási napló fájlformátum referenciája](https://go.microsoft.com/fwlink/?linkid=829599).

Többféleképpen naplófájlokat blob megtekintéséhez használhatja:

* Használat **naplózási fájlok egyesítése** az SQL Server Management Studio (SSMS 17-től induló):
    1. Az SSMS menüből válassza ki a **fájl** > **nyílt** > **naplózási fájlok egyesítése**.

    2. A **naplózási fájlok hozzáadása** párbeszédpanel nyílik meg. Válassza ki az egyik a **Hozzáadás** beállítások számára, hogy a helyi lemezen lévő naplófájlokat egyesítéséhez, vagy importálhatja őket az Azure Storage-ból. Adja meg az Azure Storage-adatokat és a fiókkulcs szükségesek.

    3. Egyesítheti az összes fájl hozzáadása után kattintson a **OK** az egyesítési művelet végrehajtása.

    4. Az egyesített fájl megnyílik az ssms-ben, ahol meg is megtekintése és elemezhetők a, valamint exportálja az xel-fájlt vagy CSV-fájl, vagy egy tábla.

* Használja a [alkalmazás szinkronizálása](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) készült. Az Azure-ban fut, és a Log Analytics nyilvános API-kat SQL auditnaplók leküldése a Log Analytics használja. A szinkronizálási kérelem leküldések SQL auditnaplók Log analyticsbe felhasználásra a Log Analytics-irányítópult-n keresztül.

* A Power BI használata. Megtekintheti és elemezheti a naplózási adatokat a Power bi-ban. Tudjon meg többet [Power BI és a egy letölthető sablon hozzáférés](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Letölti a naplófájlokat a portálon keresztül, vagy egy eszköz használatával az Azure Storage blob-tárolóból [Azure Storage Explorer](http://storageexplorer.com/).
    * Miután letöltötte helyileg egy naplófájl, duplán a fájlt, nyissa meg a, megtekintheti és elemezheti a naplókat az ssms-ben.
    * Azure Storage Explorer használatával egyszerre több fájl is letölthető. Kattintson a jobb gombbal egy adott almappát, és válassza ki **Mentés másként** egy helyi mappába menti.

* További módszereket:
   * Több fájlt vagy egy almappát, amely tartalmazza a naplófájlok a letöltés után egyesítheti őket helyileg a korábban ismertetett SSMS egyesítési naplófájlokat utasításokat leírtak szerint.

   * Nézet blobnaplózás programozott módon jelentkezik:

     * Használja a [bővített események olvasó](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# könyvtár.
     * [Bővített események lekérdezésfájlok](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) PowerShell használatával.



<br>
###<a name="database-level-policy-audit-logs"></a>Adatbázisszintű csoportházirend-auditnaplók
A gyűjteményét, Store az adatbázisszintű auditnaplók összesítjük egy **SQLDBAuditLogs** előtag telepítés közben választott Azure storage-fiókban. Megtekintheti a naplófájlok, például egy eszközzel [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com).

Egy előre konfigurált irányítópult jelentéssablon érhető el, mint egy [letölthető Excel-számolótáblában](http://go.microsoft.com/fwlink/?LinkId=403540) segít gyorsan elemezheti a naplófájlok adatait. A naplókat a sablon használatához szükséges Excel 2013-as vagy újabb verzió és a Power Query, amelyet [innen tölthet le](http://www.microsoft.com/download/details.aspx?id=39379).

A sablon képzeletbeli mintaadatokat tartalmaz, és beállíthatja a Power Query naplóban közvetlenül importálása az Azure storage-fiók.

## <a id="subheading-4"></a>Tárolás kulcs újragenerálása
Éles környezetben, valószínűleg a tárelérési kulcsok rendszeresen frissíteni. A kulcsok frissítésekor kell menteni a szabályzatot. A folyamat a következőképpen történik:

1. A naplózás az előző beállítások naplózás szakaszban leírt konfiguráció panelen módosítsa a **Tárelérési kulcs** a *elsődleges* való *másodlagos* és  **MENTÉS**.

   ![][4]
2. Nyissa meg a tárolási konfiguráció panel és **újragenerálása** a *elsődleges elérési kulcs*.
3. Lépjen vissza a naplózási konfiguráció panel
4. Váltson a **Tárelérési kulcs** a *másodlagos* való *elsődleges* nyomja le az ENTER **mentése**.
4. Lépjen vissza a felhasználói felület tárolási és **újragenerálása** a *másodlagos hozzáférési kulcs* (mint a következő előkészítése kulcsok a frissítés.

## <a id="subheading-5"></a>Automation (PowerShell/REST API-t)
Naplózás az Azure SQL Data Warehouse a következő automatizálási eszközökkel is konfigurálhatja:

* **PowerShell-parancsmagok**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Naplózási és dinamikus adatmaszkolási alacsonyabb szintű ügyfelek támogatása
Naplózás együttműködik a TDS-átirányítást támogató SQL-ügyfelek.

Bármely ügyfél megvalósító TDS 7.4 is támogatnia kell a átirányítást. A kivételek közé tartozik a JDBC 4.0, amelyben az átirányító szolgáltatás nem támogatja teljes mértékben, és mely átirányítása a node.js-hez készült Tedious nem lett megvalósítva.

"Régebbi ügyfelek", amely támogatja a TDS 7.3-as verzió és az alábbi módosítsa a kiszolgáló teljes Tartománynevét a kapcsolati karakterláncot a következőképpen:

- A kapcsolati karakterláncban eredeti kiszolgálójának teljes Tartományneve: <*kiszolgálónév*>. database.windows.net
- A kapcsolati karakterláncban a módosított kiszolgálói FQDN: <*kiszolgálónév*> .database. **biztonságos**. windows.net

"Régebbi típusú ügyfelek" részleges listáját tartalmazza:

* .NET 4.0-s vagy régebbi verzió,
* ODBC 10.0-s vagy régebbi verzió.
* JDBC (JDBC támogatja a TDS 7.4, míg a TDS-átirányítási funkció nem teljes mértékben támogatott)
* Fárasztó (a node.js-ben)

**Megjegyzés:** az előző kiszolgáló FQDN módosítása egy SQL Server szint naplózási házirend alkalmazása nélkül szükséges konfiguráció. lépés: az egyes adatbázisokban (ideiglenes megoldás) számára is hasznos lehet.     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
