---
title: Ismerkedés az Azure SQL Database naplózási szolgáltatásával
description: Az Azure SQL Database naplózási szolgáltatásával követheti nyomon az adatbázis-eseményeket egy naplóban.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 9a154e677a7308553bbef16837efae39006d3dae
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691194"
---
# <a name="get-started-with-sql-database-auditing"></a>Ismerkedés az SQL-adatbázis naplózási szolgáltatásával

Az Azure [SQL Database](sql-database-technical-overview.md) naplózása és a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban, log Analytics munkaterületen vagy Event Hubsban naplózza a naplóba. Naplózás is:

- Segít megőrizni a jogszabályi megfelelőséget, értelmezni az adatbázis tevékenységeit, és betekintést nyerhet olyan eltérésekkel és rendellenességekkel, amelyek üzleti vagy feltételezett biztonsági szabálysértéseket jelezhetnek.

- Engedélyezi és megkönnyíti a megfelelőségi szabványok betartását, bár nem garantálja a megfelelőséget. A szabványok megfelelőségét támogató Azure-programokkal kapcsolatos további információkért tekintse meg a [Azure biztonsági és adatkezelési központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , ahol megtalálhatja a SQL Database megfelelőségi tanúsítványok legújabb listáját.


> [!NOTE] 
> Ez a témakör az Azure SQL Server-kiszolgálókra, valamint az Azure SQL Serveren létrehozott SQL Database- és SQL Data Warehouse-adatbázisokra vonatkozik. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Az Azure SQL Database naplózásának áttekintése

Az SQL Database naplózási szolgáltatásával a következőket végezheti el:

- A kiválasztott események naplózási nyomvonalának **megőrzése** . Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis-tevékenységről. Az előre konfigurált jelentések és az irányítópultok segítségével gyorsan megkezdheti a tevékenységek és az események jelentéskészítését.
- Jelentések **elemzése** . Megtalálhatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

> [!IMPORTANT]
> A naplófájlok az Azure Blob Storage-ban az Azure-előfizetéshez tartozó Blobok **hozzáfűzésével** íródnak.
>
> - Az összes tárolási típus (v1, v2, blob) támogatott.
> - Minden tárolási replikációs konfiguráció támogatott.
> - A **Premium Storage** jelenleg **nem támogatott**.
> - **A VNet-ben** jelenleg **nem támogatott**a tárterület.
> - **A tűzfal mögötti tárterület** jelenleg **nem támogatott**.
> - **Azure Data Lake Storage Gen2 Storage-fiók** **hierarchikus névtere** jelenleg **nem támogatott**.

## <a id="subheading-8"></a>A kiszolgáló szintű és az adatbázis szintű naplózási szabályzat meghatározása

Egy naplózási szabályzat definiálható egy adott adatbázishoz vagy alapértelmezett kiszolgálói házirendként:

- A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

- Ha a *kiszolgáló blob-naplózása engedélyezve van*, az *mindig az adatbázisra vonatkozik*. A rendszer naplózza az adatbázist, az adatbázis naplózási beállításaitól függetlenül.

- A blob-naplózás engedélyezése az adatbázisban vagy az adatraktárban a kiszolgálón való engedélyezés mellett a *nem bírálja felül, sem módosítja* a kiszolgáló blob-naplózásának beállításait. Mindkét naplózás egymás mellett fog létezni. Ez azt jelenti, hogy az adatbázist párhuzamosan, kétszer kell naplózni. egyszer a kiszolgálói házirend és egyszer az adatbázis-házirend alapján.

   > [!NOTE]
   > Ne engedélyezze egyszerre a kiszolgáló blob-naplózási és az adatbázis-blob naplózását, kivéve, ha:
    > - Egy adott adatbázishoz eltérő *Storage-fiókot* vagy *adatmegőrzési időszakot* szeretne használni.
    > - Egy adott adatbázishoz tartozó eseménytípus vagy kategóriák naplózására van szükség, amely eltér a kiszolgálón lévő többi adatbázistól. Előfordulhat például, hogy olyan táblákat szúr be, amelyeket csak egy adott adatbázisra kell naplózni.
   >
   > Ellenkező esetben javasoljuk, hogy csak a kiszolgálói szintű blob-naplózást engedélyezze, és hagyja letiltani az adatbázis-szintű naplózást minden adatbázis esetében.

## <a id="subheading-2"></a>Az adatbázis naplózásának beállítása

A következő szakasz ismerteti a naplózás konfigurációját a Azure Portal használatával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Az SQL Database/Server (biztonság) fejléc alatt navigáljon a **naplózás** elemre.

    <a id="auditing-screenshot"></a>![navigációs ablaktábla][1]

3. Ha a kiszolgáló naplózási szabályzatát szeretné beállítani, akkor az adatbázis naplózása lapon kiválaszthatja a **kiszolgáló beállításainak megtekintése** hivatkozást. Ezután megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításait. A kiszolgáló naplózási házirendjei a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra érvényesek.

    ![Navigációs ablaktábla][2]

4. Ha azt szeretné, hogy az adatbázis szintjén engedélyezze a naplózást, kapcsolja be a **naplózást** **a**következőre:.

    Ha a kiszolgáló naplózása engedélyezve van, az adatbázis által konfigurált naplózás párhuzamosan, a kiszolgáló auditálásával fog létezni.

    ![Navigációs ablaktábla][3]

5. **Új** – mostantól több beállítással is konfigurálhatja, hogy a rendszer hol írja be a naplókat. Naplókat írhat egy Azure Storage-fiókba, Log Analytics munkaterületre Azure Monitor naplók általi felhasználáshoz, vagy az Event hub használatával történő felhasználásra. Ezen beállítások bármely kombinációját konfigurálhatja, és a rendszer a naplókat is megírja a naplókba.

   > [!WARNING]
   > A Log Analytics naplózásának engedélyezése a betöltési díjak alapján jár. Vegye figyelembe a kapcsolódó költségeket ezzel a [lehetőséggel](https://azure.microsoft.com/pricing/details/monitor/), vagy a naplókat egy Azure Storage-fiókban kell tárolnia.

    ![tárolási beállítások](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. A naplók a Storage-fiókba való írásának konfigurálásához válassza a **tárterület** lehetőséget, és nyissa meg a **tároló adatait**. Válassza ki azt az Azure Storage-fiókot, ahol a rendszer menti a naplókat, majd válassza ki a megőrzési időtartamot. A rendszer törli a régi naplókat. Ezután kattintson az **OK** gombra.

   > [!IMPORTANT]
   > - A megőrzési időtartam alapértelmezett értéke 0 (korlátlan megőrzés). Ezt az értéket módosíthatja úgy, hogy a Storage-fiók konfigurálásakor áthelyezi a tárolási **Beállítások** **megőrzés (nap)** csúszkáját a naplózáshoz.
   > - Ha a megőrzési időszakot 0 (korlátlan megőrzés) értékre módosítja bármely más értékre, vegye figyelembe, hogy az adatmegőrzés csak a megőrzési érték módosítását követően írt naplókra vonatkozik az adatmegőrzés engedélyezve van)

    ![tárfiók](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. A naplók Log Analytics munkaterületre való írásának konfigurálásához válassza a **log Analytics (előzetes verzió)** lehetőséget, és nyissa meg **log Analytics részleteit**. Válassza ki vagy hozza létre a Log Analytics munkaterületet, ahol a naplók meg lesznek írva, majd kattintson **az OK**gombra.

    ![Log Analytics munkaterület](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Ha konfigurálni szeretné a naplók írását az Event hubhoz, válassza az **Event hub (előzetes verzió)** lehetőséget, és nyissa meg az **Event hub részleteit**. Válassza ki az Event hub-t, ahol a naplók meg lesznek írva, majd kattintson **az OK**gombra. Ügyeljen arra, hogy az Event hub ugyanabban a régióban legyen, mint az adatbázis és a kiszolgáló.

    ![Eseményközpont](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Kattintson a **Save** (Mentés) gombra.
10. Ha testre szeretné szabni a naplózott eseményeket, ezt [PowerShell-parancsmagok](#subheading-7) vagy a [REST API](#subheading-9)használatával végezheti el.
11. A naplózási beállítások konfigurálása után bekapcsolhatja az új veszélyforrások észlelése funkciót, és konfigurálhatja az e-maileket a biztonsági riasztások fogadására. A veszélyforrások észlelése esetén a rendellenes adatbázis-tevékenységekkel kapcsolatos proaktív riasztásokat kap, amelyek potenciális biztonsági fenyegetéseket jelezhetnek. További információkért lásd: [a fenyegetések észlelésének első lépései](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Szüneteltetett Azure SQL Data Warehouse naplózásának engedélyezése nem lehetséges. A szolgáltatás engedélyezéséhez szüntesse meg az adatraktár szüneteltetését.

> [!WARNING]
> Ha olyan kiszolgálón engedélyezi a naplózást, amelyen Azure SQL Data Warehouse van, akkor **az adattárház folytatódni fog, és újra szünetelteti** , ami felmerülhet a számlázási költségek miatt.

## <a id="subheading-3"></a>Naplózási naplók és jelentések elemzése

Ha úgy döntött, hogy naplókat ír Azure Monitor naplókba:

- Használja a [Azure Portal](https://portal.azure.com).  Nyissa meg a megfelelő adatbázist. Az adatbázis **naplózási** lapjának felső részén kattintson a **naplók megtekintése**elemre.

    ![naplók megtekintése](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Ezt követően két módon megtekintheti a naplókat:
    
    Ha a **naplózási rekordok** lap tetején lévő **log Analytics** gombra kattint, megnyílik a naplók nézet a log Analytics munkaterületen, ahol testreszabhatja az időtartományt és a keresési lekérdezést.
    
    ![Megnyitás Log Analytics munkaterületen](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Ha a **naplózási rekordok** oldal tetején található **irányítópult megtekintése** elemre kattint, megnyílik egy irányítópult, amely a naplók információit jeleníti meg, ahol a biztonsági elemzéseket, a bizalmas adatokhoz való hozzáférést és más adatokat is megtekintheti. Ez az irányítópult úgy lett kialakítva, hogy segítséget nyújtson az adataihoz szükséges biztonsági információk megszerzésében.
    Testreszabhatja az időtartományt és a keresési lekérdezést is. 
    ![nézet Log Analytics irányítópulton](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics irányítópult](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics biztonsági ismeretek](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Azt is megteheti, hogy a naplókat a Log Analytics panelről is elérheti. Nyissa meg Log Analytics munkaterületét, és az **általános** szakaszban kattintson a **naplók**elemre. Egy egyszerű lekérdezéssel kezdheti meg a naplókat, például a *következőt: Search "SQLSecurityAuditEvents"* .
    Itt is használhatja [Azure monitor naplókat](../log-analytics/log-analytics-log-search.md) a speciális keresések futtatásához a naplózási napló adatain. A Azure Monitor naplók valós idejű üzemeltetési elemzéseket biztosítanak az integrált keresés és az egyéni irányítópultok használatával, amelyekkel a munkaterhelések és a kiszolgálók több millió rekordját is könnyedén elemezheti. További hasznos információk Azure Monitor naplók keresési nyelvéről és parancsairól: [Azure monitor naplók keresési referenciája](../log-analytics/log-analytics-log-search.md).

Ha úgy döntött, hogy naplókat ír az Event hub-ba:

- Az Event hub naplózási adatainak felhasználása érdekében be kell állítania egy streamet az események felhasználásához, és egy célhoz kell írnia azokat. További információ: [Azure Event Hubs dokumentáció](https://docs.microsoft.com/azure/event-hubs/).
- Az Event hub naplófájljai az [Apache Avro](https://avro.apache.org/) -események törzsében vannak rögzítve, és az UTF-8 kódolást használó JSON-formázással vannak tárolva. A naplók olvasásához használhat [Avro-eszközöket](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) vagy hasonló eszközöket, amelyek ezt a formátumot dolgozzák fel.

Ha úgy döntött, hogy naplózza a naplókat egy Azure Storage-fiókba, a naplók megtekintéséhez több módszer is használható:

- A rendszer összesíti a naplókat a telepítés során kiválasztott fiókban. A naplókat a [Azure Storage Explorer](https://storageexplorer.com/)eszközzel is megismerheti. Az Azure Storage-ban a naplózási naplók a **sqldbauditlogs**nevű tárolóban lévő blob-fájlok gyűjteményében lesznek mentve. A tárolási mappa hierarchiájának, az elnevezési konvencióknak és a napló formátumának további részleteiért tekintse meg a [SQL Database a naplózási napló formátumát](https://go.microsoft.com/fwlink/?linkid=829599).

- Használja a [Azure Portal](https://portal.azure.com).  Nyissa meg a megfelelő adatbázist. Az adatbázis **naplózási** lapjának felső részén kattintson a **naplók megtekintése**elemre.

    ![Navigációs ablaktábla][7]

    Megnyílik a **naplózási rekordok** , amelyekről megtekintheti a naplókat.

  - Az egyes dátumokat a **naplózási rekordok** lap tetején található **szűrő** gombra kattintva tekintheti meg.
  - A naplózási **forrás**váltásával válthat a *kiszolgálói naplózási házirend* és az adatbázis- *naplózási házirend* által létrehozott naplózási rekordok között.
  - Csak az SQL-injektálással kapcsolatos naplózási rekordokat tekintheti meg, ha az **SQL-injektálások csak naplózási rekordok megjelenítése** jelölőnégyzetét ellenőrzi.

       ![Navigációs ablaktábla][8]

- A System Function **sys. fn_get_audit_file** (T-SQL) használatával táblázatos formátumban visszaküldheti a naplózási adatokat. További információ a függvény használatáról: [sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- A **naplófájlok egyesítése** SQL Server Management Studio (a SSMS 17-től kezdődően):
    1. A SSMS menüben válassza a **fájl** ** > a** naplófájlok **egyesítése** > a naplófájlok egyesítése lehetőséget.

        ![Navigációs ablaktábla][9]
    2. Megnyílik a naplófájlok **hozzáadása** párbeszédpanel. Válassza ki a **hozzáadási** lehetőségek egyikét, és válassza ki, hogy szeretné-e egyesíteni a naplófájlokat egy helyi lemezről, vagy importálni őket az Azure Storage-ból. Meg kell adnia az Azure Storage adatait és a fiók kulcsát.

    3. Miután az összes egyesíteni kívánt fájl hozzá lett adva, kattintson az **OK** gombra az egyesítési művelet befejezéséhez.

    4. Az egyesített fájl megnyílik a SSMS-ben, ahol megtekintheti és elemezheti, valamint exportálhatja egy XEL vagy CSV-fájlba vagy egy táblába.

- Power BI használata. Megtekintheti és elemezheti a naplózási naplóban tárolt adatPower BI. További információért és a letölthető sablonok eléréséhez tekintse meg a [naplózási napló adatainak elemzése Power BIban](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)című témakört.
- Töltse le a naplófájlokat az Azure Storage blob-tárolóból a portálon keresztül, vagy egy eszköz, például [Azure Storage Explorer](https://storageexplorer.com/)használatával.
  - A naplófájl helyi letöltése után kattintson duplán a fájlra a SSMS található naplók megnyitásához, megtekintéséhez és elemzéséhez.
  - Azure Storage Explorer használatával egyszerre több fájlt is letölthet. Ehhez kattintson a jobb gombbal egy adott almappára, és válassza a **Mentés másként** lehetőséget a helyi mappában való mentéshez.

- További metódusok:

  - Több fájl vagy a naplófájlokat tartalmazó almappa letöltése után helyileg egyesítheti azokat a korábban ismertetett SSMS-egyesítési naplófájlok című részben leírtak szerint.
  - A blob-naplózási naplók programozott megjelenítése:

    - [Kiterjesztett események fájljainak lekérdezése](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) a PowerShell használatával.

## <a id="subheading-5"></a>Üzemi eljárások

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Földrajzilag replikált adatbázisok naplózása</a>

A földrajzilag replikált adatbázisok esetében, ha engedélyezi a naplózást az elsődleges adatbázison, a másodlagos adatbázis azonos naplózási házirenddel fog rendelkezni. A naplózás a másodlagos adatbázison is beállítható úgy, hogy az elsődleges adatbázistól függetlenül engedélyezi a naplózást a **másodlagos kiszolgálón**.

- Kiszolgáló szintű (**ajánlott**): a naplózás bekapcsolása mind az **elsődleges** , mind a **másodlagos kiszolgálón** – az elsődleges és másodlagos adatbázisok egymástól függetlenül lesznek naplózva a megfelelő kiszolgálói szintű házirend alapján.
- Adatbázis szintje: a másodlagos adatbázisok adatbázis-szintű naplózása csak az elsődleges adatbázis naplózási beállításaiból állítható be.
  - A naplózást engedélyezni kell az *elsődleges adatbázison*, nem pedig a kiszolgálón.
  - Miután az elsődleges adatbázison engedélyezte a naplózást, a másodlagos adatbázison is engedélyezve lesz.

    >[!IMPORTANT]
    >Az adatbázis szintű naplózással a másodlagos adatbázis tárolási beállításai azonosak lesznek az elsődleges adatbázissal, ami régiók közötti forgalmat okoz. Javasoljuk, hogy csak a kiszolgálói szintű naplózást engedélyezze, és hagyja letiltani az adatbázis-szintű naplózást minden adatbázis esetében.

### <a id="subheading-6">Tárolási kulcs újragenerálása</a>

Éles környezetben valószínűleg rendszeresen frissíti a tárolási kulcsokat. A naplók Azure Storage-ba írásakor újra kell mentenie a naplózási szabályzatot a kulcsok frissítésekor. A folyamat a következő:

1. Nyissa meg a **tároló adatait**. A **tárolási hozzáférési kulcs** mezőben válassza a **másodlagos**lehetőséget, majd kattintson **az OK**gombra. Ezután kattintson a **Mentés** gombra a naplózási konfiguráció lap tetején.

    ![Navigációs ablaktábla][5]
2. Nyissa meg a tárolási konfiguráció lapot, és újból létrehozza az elsődleges hozzáférési kulcsot.

    ![Navigációs ablaktábla][6]
3. Lépjen vissza a naplózási konfiguráció lapra, váltson másodlagosról elsődlegesre a Storage-hozzáférési kulcsra, majd kattintson **az OK**gombra. Ezután kattintson a **Mentés** gombra a naplózási konfiguráció lap tetején.
4. Lépjen vissza a tárolási konfiguráció lapra, és generálja újra a másodlagos elérési kulcsot (felkészülés a következő kulcs frissítési ciklusára).

## <a name="additional-information"></a>További információ

- A naplózási formátumra, a tárolási mappa hierarchiájának és az elnevezési konvenciók részleteiért tekintse meg a [blob naplózási napló formátumának referenciáját](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL Database naplózási szolgáltatás 4000 karakterből álló karaktert tárol egy naplózási rekordban. Ha egy auditálható művelet által visszaadott **utasítás** vagy **data_sensitivity_information** -érték több mint 4000 karaktert tartalmaz, a rendszer az első 4000 karakternél nagyobb értéket **csonkít, és nem naplózza**.

- A naplók az Azure-előfizetéshez tartozó Azure Blob-tárolóban lévő **Blobok hozzáfűzésére** vannak írva:
  - A hozzáfűzési Blobok jelenleg **nem támogatják** a **Premium Storage** .
  - **A VNet-ben** jelenleg **nem támogatott**a tárterület.

- Az alapértelmezett naplózási házirend magában foglalja az összes műveletet és a következő műveleti csoportokat, amelyek az adatbázison végrehajtott összes lekérdezést és tárolt eljárást naplózzák, valamint sikeres és sikertelen bejelentkezéseket is végeznek:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Különböző típusú műveletek és műveleti csoportok naplózását a PowerShell használatával konfigurálhatja az [SQL-adatbázis naplózásának kezelése Azure PowerShell használatával](#subheading-7) című szakaszban leírtak szerint.

- HRE-hitelesítés használata esetén a sikertelen bejelentkezések rekordjai *nem* jelennek meg az SQL-naplóban. A sikertelen bejelentkezési naplózási rekordok megtekintéséhez látogasson el a [Azure Active Directory portálra]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), amely az események adatait naplózza.


## <a id="subheading-7"></a>SQL Database-naplózás kezelése Azure PowerShell használatával

**PowerShell-parancsmagok (beleértve a további szűrések where záradékának támogatását)** :

- [Adatbázis-naplózási szabályzat létrehozása vagy frissítése (set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Kiszolgáló naplózási szabályzatának létrehozása vagy frissítése (set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Adatbázis naplózási szabályzatának beolvasása (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Kiszolgáló naplózási szabályzatának beolvasása (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Adatbázis-naplózási házirend eltávolítása (remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Kiszolgáló naplózási házirendjének eltávolítása (remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

A parancsfájlokra példa: a [naplózás és a veszélyforrások észlelésének konfigurálása a PowerShell használatával](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>SQL Database-naplózás kezelése REST API használatával

**REST API**:

- [Adatbázis-naplózási házirend létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Kiszolgáló naplózási szabályzatának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis naplózási szabályzatának beolvasása](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Kiszolgáló naplózási szabályzatának beolvasása](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Kiterjesztett szabályzat a WHERE záradék támogatásával további szűréshez:

- [Adatbázis *kiterjesztett* naplózási szabályzatának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Kiszolgáló *kiterjesztett* naplózási szabályzatának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis *kiterjesztett* naplózási szabályzatának beolvasása](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Kiszolgáló *kiterjesztett* naplózási szabályzatának beolvasása](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Az SQL Database naplózásának kezelése Azure Resource Manager sablonokkal

Az Azure SQL Database naplózását [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) sablonok használatával felügyelheti, ahogy az alábbi példákban is látható:

- [Azure-SQL Server üzembe helyezése, amely lehetővé teszi a naplózási naplók írását az Azure Blob Storage-fiókba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Olyan Azure-SQL Server üzembe helyezése, amely lehetővé teszi a naplózást a naplók írásához Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Olyan Azure-SQL Server üzembe helyezése, amely lehetővé teszi a naplózást a naplók írásához Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> A csatolt minták egy külső nyilvános tárházban találhatók, és az "adott állapotban", szavatosság nélkül, és semmilyen Microsoft támogatási program vagy szolgáltatás nem támogatott.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading – 8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

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
