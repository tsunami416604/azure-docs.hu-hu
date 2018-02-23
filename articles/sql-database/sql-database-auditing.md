---
title: "Ismerkedés az Azure SQL database naplózási |} Microsoft Docs"
description: "Azure SQL database naplózási segítségével nyomon követheti az adatbázissal kapcsolatos eseményeket a naplók."
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.openlocfilehash: 0994bdc7e6c86e10f7002649830fc59ad3d1a045
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-sql-database-auditing"></a>Ismerkedés az SQL-adatbázis naplózási szolgáltatásával
Az Azure SQL database naplózási nyomon követi az adatbázis-események, mind az írás őket naplózási jelentkezzen be az Azure storage-fiók. A naplózás is:

* Segít törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére betekintést.

* Lehetővé teszi, és elősegíti a megfelelést a megfelelőségi követelményeket, de azt nem garantálja a megfelelőség. További információ az Azure programokat, hogy támogatási szabványoknak való megfelelés, a következő témakörben: a [Azure biztonsági és adatkezelési központ](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Az Azure SQL adatbázis naplózásának áttekintése
SQL-adatbázis a naplózás is használhatja:


* **Tartsa meg** kijelölt események egy naplóban. Adatbázis-műveleteket kell naplózni kategóriáinak adhat meg.
* **A jelentés** adatbázis-tevékenység. Előre konfigurált jelentések és egy irányítópult segítségével gyorsan Ismerkedés a tevékenységet és az események naplózásához.
* **Elemezze** jelentéseket. Gyanús események, a szokatlan tevékenység és a trendeket találja.

Konfigurálhatja a kategóriák, különböző típusú naplózás leírtak szerint a [az adatbázis naplózásának beállítása](#subheading-2) szakasz.

> [!IMPORTANT]
> Naplók írt **hozzáfűző Blobok** egy Azure Blob Storage az Azure-előfizetése.
>
> * **Prémium szintű Storage** jelenleg **nem támogatott** által hozzáfűző blobokat.
> * **VNet a tárolási** jelenleg **nem támogatott**.

## <a id="subheading-8"></a>Adja meg a kiszolgálói szintű és adatbázis-szintű naplózási házirend

A naplózási házirend meghatározása egy adott adatbázis vagy az alapértelmezett házirend-kiszolgáló:

* A kiszolgáló házirend a kiszolgálón az összes meglévő és az újonnan létrehozott adatbázisra vonatkozik.

* Ha *engedélyezve van a kiszolgáló blob naplózás*, azt *mindig vonatkozik az adatbázis*. Az adatbázis naplózza, függetlenül az adatbázis naplózási beállításait.

* Engedélyezte a blob az adatbázis naplózását, lehetővé téve a kiszolgálón kívül does *nem* bírálja felül, vagy a kiszolgáló blobnaplózási funkció beállításainak módosításához. Mindkét naplózás jelen egymás mellett. Ez azt jelenti az adatbázis legyen naplózva, kétszer párhuzamosan; a kiszolgáló házirend és egyszer az adatbázis házirend többször.

   > [!NOTE]
   > Kerülje engedélyezésével server blob naplózási, mind az adatbázis blobnaplózási funkció együtt, kivéve, ha:
    > * Egy másik használni kívánt *tárfiók* vagy *megőrzési időszak* adott adatbázishoz.
    > * Naplózandó esemény kategóriákban adott adatbázishoz, amelyek eltérnek a kiszolgálón lévő adatbázis többi részétől. Például lehetséges, hogy tábla Beszúrások, csak egy adott adatbázis kell naplózni kell.
   >
   > Ellenkező esetben azt javasoljuk, hogy csak a kiszolgálói szintű blob-naplózást, és hagyja meg az adatbázis-szintű naplózás le van tiltva az összes olyan adatbázis.


## <a id="subheading-2"></a>Az adatbázis naplózásának beállítása
Az alábbi szakasz ismerteti az Azure portál használatával naplózási beállításait.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Lépjen a **beállítások** naplózandó SQL adatbázis vagy SQL Server panelen. Az a **beállítások** panelen válassza **naplózási & Threat detection**.

    <a id="auditing-screenshot"></a>![Navigációs ablaktábla][1]
3. Ha a kiszolgáló naplózási házirend beállítása szeretne használni, válassza a **beállításainak megtekintéséhez** hivatkozás az adatbázis naplózási paneljén. Ezt követően megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításainak. Kiszolgáló naplózási házirendek ezen a kiszolgálón az összes meglévő és az újonnan létrehozott adatbázisokra érvényesek.

    ![Navigációs ablaktábla][2]
4. Ha inkább az adatbázis szintjén naplózásának blob engedélyezésére **naplózási**, jelölje be **ON**, és a **típus naplózás**, jelölje be **Blob**.

    Ha a kiszolgáló blobnaplózási funkció engedélyezve van, az adatbázis-konfigurálva naplózási és a kiszolgáló blob naplózási jelen.

    ![Navigációs ablaktábla][3]
5. Lehetőségre a **naplózási naplók tárolási** panelen válassza **tárolási részletek**. Válassza ki az Azure storage-fiók, ahol naplókat a rendszer menti, majd válassza ki a megőrzési időn. Törli a régi naplókat. Ezután kattintson az **OK** gombra.
   >[!TIP]
   >A legtöbbet hozhatja ki a naplózási jelentések sablonok, ugyanazt a tárfiókot használja az összes naplózott adatbázisok.

    <a id="storage-screenshot"></a>![Navigációs ablaktábla][4]
6. Ha szeretné testre szabni a naplózott eseményeket, ehhez PowerShell vagy a REST API használatával.
7. A naplózási beállítások konfigurálása után az új fenyegetés észlelési szolgáltatás, és konfigurálja a biztonsági riasztások e-maileket. Fenyegetésészlelés használatakor kapni proaktív riasztások a adatbázist érintő rendellenes tevékenységeket, amely azt jelzi, hogy a esetleges biztonsági fenyegetéseket jelezhetnek. További információkért lásd: [Ismerkedés a fenyegetésészlelés](sql-database-threat-detection-get-started.md).
8. Kattintson a **Save** (Mentés) gombra.





## <a id="subheading-3"></a>Elemezze a vizsgálati naplók és jelentések
Naplók a telepítés során választott Azure storage-fiók összesítése. Eszköz használatával felfedezheti a vizsgálati naplók [Azure Tártallózó](http://storageexplorer.com/).

Menti a BLOB naplóinak blob fájlok belül nevű tárolót **sqldbauditlogs**.

További információt a hierarchiában, a tároló mappa rendszerben elnevezési konvenciókat és időformátumát, tekintse meg a [Blob naplózási napló fájlformátum referenciájában](https://go.microsoft.com/fwlink/?linkid=829599).

Többféleképpen segítségével blob naplófájlok megtekintése:

* Használja a [Azure-portálon](https://portal.azure.com).  Nyissa meg a megfelelő adatbázishoz. Az adatbázis felső **naplózási & Threat detection** panelen kattintson a **nézet naplók**.

    ![Navigációs ablaktábla][7]

    Egy **rekordok naplózása** panel megnyitása, amelyen után képes lesz a naplók megtekintéséhez.

    - Gombra kattintva megtekintheti az adott dátumok **szűrő** tetején a **rekordok naplózása** panelen.
    - Naplózási azt jelzi, hogy a kiszolgáló házirend vagy az adatbázis házirend naplózási hozott létre válthat.

       ![Navigációs ablaktábla][8]

* A rendszer funkcióval **sys.fn_get_audit_file** (T-SQL) vissza a napló adatairól táblázatos formátumban. Ez a funkció használatáról további információkért lásd: a [sys.fn_get_audit_file dokumentáció](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Használjon **naplózási fájlok egyesítése** az SQL Server Management Studio (SSMS 17-től induló):
    1. A szolgáltatáshoz az SSMS menüben válassza ki a **fájl** > **nyitott** > **naplózási fájlok egyesítése**.

        ![Navigációs ablaktábla][9]
    2. A **naplózási fájlok hozzáadása** párbeszédpanel. Válasszon egyet a a **Hozzáadás** beállításokat, hogy a helyi lemezen lévő naplózási fájlok egyesítése vagy importálja azokat az Azure Storage-e. Adja meg az Azure Storage-adatokat és a fiókkulcsot szükségesek.

    3. Egyesíteni az összes fájl hozzáadása után kattintson **OK** az egyesítési művelet végrehajtása.

    4. Az egyesített fájl megnyitása SSMS, ahol Ön is megtekintése és elemezze, valamint exportálni kell egy xel-fájlt vagy a CSV-fájl vagy egy táblához.

* Használja a [alkalmazás szinkronizálása](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) , hogy létrehoztunk. Az Azure-ban fut, és használja az Operations Management Suite (OMS) Naplóelemzési nyilvános API felületeket leküldéses SQL naplók az OMS Szolgáltatáshoz. A szinkronizálási kérelem leküldéses értesítések SQL naplók OMS Log Analyticshez való használathoz az OMS szolgáltatáshoz irányítópulton keresztül.

* Használja a Power bi-ban. Megtekintheti és elemezheti a napló adatairól a Power bi-ban. További információ [Power bi-ban, és hozzáférést egy letölthető sablon](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Töltse le a naplófájlok az Azure Storage-blob tároló a portálon, vagy egy olyan eszközzel, mint a [Azure Tártallózó](http://storageexplorer.com/).
    * Miután letöltötte a helyi naplófájlt, duplán kattintva nyissa meg a, megtekintése és elemzése a naplófájlok az SSMS fájlt.
    * Azure Tártallózó keresztül egyszerre több fájl is letölthető. Kattintson a jobb gombbal egy adott almappát, és válassza ki **Mentés másként** egy helyi mappába menti.

* További módszereket:
   * Több fájl vagy egy almappára naplófájlokat tartalmazó a letöltés után egyesítheti helyileg az SSMS egyesítési naplófájlokat utasításokat a korábban ismertetett leírtak szerint.

   * Nézet blobnaplózási funkció szoftveres naplói:

     * Használja a [kiterjesztett események olvasó](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# könyvtár.
     * [Kiterjesztett események fájljainak lekérdezése](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) PowerShell használatával.




## <a id="subheading-5"></a>Éles eljárások
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Georeplikált adatbázisok naplózás</a>
Georeplikált adatbázisok Ha az elsődleges adatbázis naplózásának engedélyezése a másodlagos adatbázis lesz azonos naplózási házirend. Akkor is a naplózás engedélyezésével a másodlagos adatbázis naplózásának beállítása a **másodlagos kiszolgáló**, függetlenül az elsődleges adatbázisból.

* Kiszolgálószintű (**ajánlott**): mindkét naplózás bekapcsolása a **elsődleges kiszolgáló** , valamint a **másodlagos kiszolgáló** – az elsődleges és másodlagos adatbázisok egyes naplózza függetlenül a megfelelő kiszolgálószintű házirend alapján.

* Adatbázis-szintű: A másodlagos adatbázisok naplózásának adatbázis szintje csak az elsődleges adatbázis naplózási beállításainak lehet megadni.
   * BLOB naplózását engedélyezni kell a *maga elsődleges adatbázis*, nem a kiszolgáló.
   * Blob naplózás engedélyezése után az elsődleges adatbázist, akkor is válik elérhetővé, másodlagos adatbázison.

     >[!IMPORTANT]
     >Adatbázis-szintű naplózás esetén a másodlagos adatbázis-tárolási beállításai lesz azonos az elsődleges adatbázis, a kereszt-területi forgalmat, amely. Azt javasoljuk, hogy csak a kiszolgálószintű naplózást, és hagyja meg az adatbázis-szintű naplózás le van tiltva az összes olyan adatbázis.
<br>

### <a id="subheading-6">Tárolási kulcs újragenerálása</a>
Éles akkor valószínűleg a kulcsok rendszeresen frissíteni. Ha a kulcsok frissítése, kell mentenie kell a naplózási házirend. A folyamat a következőképpen történik:

1. Nyissa meg a **tárolási részletek** panelen. Az a **Tárelérési kulcs** mezőben válassza **másodlagos**, és kattintson a **OK**. Kattintson a **mentése** a naplózási konfiguráció panel tetején.

    ![Navigációs ablaktábla][5]
2. Nyissa meg a tárolási konfiguráció paneljét, és az elsődleges elérési kulcs újragenerálása.

    ![Navigációs ablaktábla][6]
3. Lépjen vissza a naplózási konfiguráció panelt, váltson a tárelérési kulcs az elsődleges a másodlagos, és kattintson **OK**. Kattintson a **mentése** a naplózási konfiguráció panel tetején.
4. Lépjen vissza a tárolási konfiguráció panelt, és generálni a másodlagos elérési kulcsot (a frissítés során a következő kulcs előkészítése).

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>SQL adatbázis-naplózás az Azure PowerShell kezelése


* **PowerShell-parancsmagok**:

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Set-AzureRMSqlDatabaseAuditing][105]
   * [Set-AzureRMSqlServerAuditing][106]

   Tekintse meg a parancsfájl például [konfigurálhatja a naplózás és a fenyegetések észlelésére, a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-sql-database-auditing-using-rest-api"></a>SQL adatbázis naplózás REST API használatával kezelése

* **REST API - Blobnaplózási funkció**:

   * [Hozzon létre vagy frissítés adatbázis Blob naplózási házirend](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Hozzon létre vagy frissítési kiszolgáló Blob naplózási házirend](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Adatbázis-Blob naplórendet beolvasása](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Kiszolgáló Blob naplórendet beolvasása](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Műveleti eredmény naplózás Server Blob beolvasása](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
