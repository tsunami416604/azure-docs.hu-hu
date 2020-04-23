---
title: Azure SQL-naplózás
description: Az Azure SQL-adatbázis naplózása segítségével nyomon követheti az adatbázis-eseményeket egy naplóban.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 48cdbc8188604ce1992a1cb15289576ba92902a3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086147"
---
# <a name="azure-sql-auditing"></a>Azure SQL-naplózás

Az Azure [SQL Database](sql-database-technical-overview.md) és az [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) naplózása nyomon követi az adatbázis-eseményeket, és naplóba írja őket az Azure storage-fiókjában, a Log Analytics-munkaterületben vagy az Event Hubs-ban. 

A naplózás további előnyei:

- Segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.

- Lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, bár nem mindig garantálja a megfelelőséget. A szabványoknak való megfelelést támogató Azure-programokról az [Azure Adatvédelmi központban](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) talál, ahol az SQL Database megfelelőségi tanúsítványainak legfrissebb listáját találja.

> [!NOTE] 
> Ez a témakör az Azure SQL Database és az Azure Synapse Analytics-adatbázisokra egyaránt vonatkozik. Az egyszerűség kedvéért az SQL Database az Azure SQL Database és az Azure Synapse Analytics hivatkozva használatos.

## <a name="overview"></a><a id="overview"></a>Áttekintés

Az SQL-adatbázis naplózásával az alábbiakat végezheti el:

- **A** kiválasztott események naplózási nyomvonalának megtartása. Megadhatja a naplózni kívánt adatbázis-műveletek kategóriáit.
- **Jelentés** az adatbázis tevékenységéről. Az előre konfigurált jelentések és irányítópultok segítségével gyorsan megkezdheti a tevékenységekkel és az eseményekkel kapcsolatos jelentéskészítést.
- **Jelentések elemzése.** Azonosíthatja a gyanús eseményeket, a szokatlan tevékenységeket és a trendeket.

> [!IMPORTANT]
> - Az Azure SQL Database naplózása a rendelkezésre állási & teljesítményre van optimalizálva. Nagyon nagy aktivitású Azure SQL Database lehetővé teszi a műveletek folytatását, és előfordulhat, hogy nem rögzít néhány naplózott eseményeket.

#### <a name="auditing-limitations"></a>Naplózási korlátozások

- **A prémium szintű tárhely** jelenleg **nem támogatott.**
- Az Azure Data **Lake Storage Gen2 tárfiók** **hierarchikus névtere** jelenleg **nem támogatott.**
- A szüneteltetett **Azure SQL Data Warehouse** naplózása nem támogatott. A naplózás engedélyezéséhez folytassa az adattárházat.

## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Kiszolgálószintű és adatbázisszintű naplózási házirend meghatározása

Naplózási házirend definiálható egy adott adatbázishoz vagy alapértelmezett kiszolgálóházirendként:

- A kiszolgálóházirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

- Ha *a kiszolgálóblob-naplózás engedélyezve van,* *az mindig az adatbázisra vonatkozik.* Az adatbázis naplózása az adatbázis naplózási beállításaitól függetlenül történik.

- Blob naplózásának engedélyezése az adatbázisban vagy az adatraktárban, amellett, hogy engedélyezi azt a kiszolgálón, *nem* bírálja felül vagy módosítja a kiszolgálóblob-naplózás beállításait. Mindkét ellenőrzés egymás mellett fog létezni. Más szóval az adatbázist párhuzamosan kétszer naplózják; egyszer a kiszolgálóházirend, egyszer pedig az adatbázis-házirend.

   > [!NOTE]
   > Ne engedélyezze a kiszolgálóblob-naplózást és az adatbázisblob-naplózást együtt, kivéve, ha:
    > - Egy másik *tárfiókot*, *megőrzési időszakot* vagy *Log Analytics-munkaterületet* szeretne használni egy adott adatbázishoz.
    > - Egy adott adatbázis eseménytípusait vagy kategóriáit szeretné naplózni, amelyek eltérnek a kiszolgáló többi adatbázisától. Előfordulhat például, hogy olyan táblabeszúrások vannak, amelyeket csak egy adott adatbázishoz kell naplózni.
   >
   > Ellenkező esetben azt javasoljuk, hogy csak kiszolgálószintű blob naplózása engedélyezze, és hagyja az adatbázis-szintű naplózás letiltva az összes adatbázis.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Naplózás beállítása a kiszolgálóhoz

Az alapértelmezett naplózási házirend tartalmazza az összes műveletet és a következő műveletcsoportokat, amelyek naplózzák az adatbázissal végrehajtott összes lekérdezést és tárolt eljárást, valamint a sikeres és sikertelen bejelentkezéseket:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
A PowerShell használatával konfigurálhatja a különböző típusú műveletek és műveletcsoportok naplózását, ahogy azt az [Azure PowerShell használatával történő SQL-adatbázis-naplózás kezelése](#manage-auditing) című szakasz ismerteti.

Az Azure SQL Database Audit 4000 karakternyi adatot tárol egy naplórekordban a karaktermezőkszámára. Ha a **naplózható** műveletből visszaadott utasítás vagy **data_sensitivity_information** értékek több mint 4000 karaktert tartalmaznak, az első 4000 karakteren túli adatok csonkolva lesznek, **és nem lesznek naplózva.**
A következő szakasz ismerteti a naplózás az Azure Portal használatával.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Nyissa meg a **Naplózás elemet** az SQL adatbázis/kiszolgáló ablaktábla Biztonság fejlécében.
3. Ha kiszolgálónaplózási házirendet szeretne beállítani, az adatbázis-naplózási lapon kiválaszthatja a **Kiszolgáló beállításainak megtekintése** hivatkozást. Ezután megtekintheti vagy módosíthatja a kiszolgáló naplózási beállításait. A kiszolgáló naplózási házirendjei a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkoznak.

    ![Navigációs ablaktábla](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. Ha az adatbázis szintjén szeretné engedélyezni a naplózást, kapcsolja be **a** **Naplózás** t. Ha a kiszolgáló naplózása engedélyezve van, az adatbázis által konfigurált naplózás a kiszolgáló naplózásával együtt fog létezni.

5. Több lehetősége is van a naplózási naplók írási helyének konfigurálására. Naplókat írhat egy Azure-tárfiókba, egy Log Analytics-munkaterületre az Azure Monitor naplói (előzetes verzió) általi felhasználáshoz, vagy az eseményközpontba az eseményközpont használatával (előzetes verzió) való használatra. Ezek a beállítások tetszőleges kombinációját konfigurálhatja, és mindegyikbe naplót írnak.
  
   ![tárolási lehetőségek](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Naplózás a tárolási célig

Ha a naplóírást tárfiókba szeretné beállítani, válassza a **Storage (Tárolás)** lehetőséget, és nyissa meg **a Táradatokat.** Válassza ki azt az Azure storage-fiókot, ahol a naplókat menti, majd válassza ki a megőrzési időszakot. Ezt követően kattintson az **OK** gombra. A megőrzési időszaknál régebbi naplók törlődnek.

- A megőrzési időszak alapértelmezett értéke 0 (korlátlan megőrzés). Ezt az értéket úgy módosíthatja, hogy a **Storage-beállítások** **megőrzése (Napok)** csúszkáját áthelyezi a tárfiók naplózáshoz való konfigurálásakor.
  - Ha a megőrzési időszakot 0-ról (korlátlan megőrzés) bármely más értékre módosítja, vegye figyelembe, hogy a megőrzéscsak a megőrzési érték módosítása után írt naplókra vonatkozik (a megőrzéskorlátlanra állított időszakban írt naplók megmaradnak, még akkor is, ha a megőrzés engedélyezve van).

  ![tárfiók](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>Megjegyzések

- A naplónaplók az Azure-előfizetésben lévő Azure Blob-tárolóban lévő **Blobok hozzáfűzése** szolgáltatásba kerülnek
- Ha egy nem módosítható naplótárolót szeretne beállítani a kiszolgáló- vagy adatbázis-szintű naplózási eseményekhez, kövesse az [Azure Storage utasításait](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) (győződjön meg arról, hogy a nem módosítható blobtár konfigurálásakor **a további hozzáfűzések engedélyezése** lehetőséget választotta).
- Naplónaplókat írhat egy Azure Storage-fiók egy virtuális hálózat vagy tűzfal mögött. Konkrét utasításokat a [Naplózás írása a virtuális hálózat és a tűzfal mögötti tárfiókba](create-auditing-storage-account-vnet-firewall.md)talál.
- A naplózási beállítások konfigurálása után bekapcsolhatja az új fenyegetésészlelési funkciót, és beállíthatja az e-maileket a biztonsági riasztások fogadására. Fenyegetésészlelés használatakor proaktív riasztásokat kap a rendellenes adatbázis-tevékenységekről, amelyek potenciális biztonsági fenyegetéseket jelezhetnek. További információ: [Első lépések a fenyegetésészleléssel.](sql-database-threat-detection-get-started.md)
- A naplóformátumról, a tárolómappa hierarchiájáról és az elnevezési konvenciókról a [Blob naplónapló-formátumának hivatkozási száma című témakörben talál.](https://go.microsoft.com/fwlink/?linkid=829599)
- AAD-hitelesítés használata esetén a sikertelen bejelentkezési rekordok *nem* jelennek meg az SQL naplójában. A sikertelen bejelentkezési naplózási rekordok megtekintéséhez meg kell látogatnia az [Azure Active Directory portálon,]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)amely naplózza az események részleteit.
- Az [írásvédett replikák naplózása](sql-database-read-scale-out.md) automatikusan engedélyezve van. A tárolómappák hierarchiájáról, az elnevezési konvenciókról és a naplóformátumról az SQL Database Audit Log Format című témakörben talál további [részleteket.](sql-database-audit-log-format.md) 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Naplózás a Log Analytics célhelyre
  
Ha naplónaplókírását szeretné naplózni egy Log Analytics-munkaterületre, válassza a **Log Analytics (előzetes verzió)** lehetőséget, és nyissa meg a **Log Analytics adatait.** Jelölje ki vagy hozza létre a Naplóelemzés idotörzsét, ahol naplókat írnak, majd kattintson az **OK**gombra.
   
   ![LogAnalytics munkaterület](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Naplózás az Eseményközpont célpontjára

> [!WARNING]
> Ha engedélyezi a naplózást egy olyan kiszolgálón, amelyen SQL-készlet található, **az SQL-készlet újraindítása és újra szüneteltetése** esetén, amely számlázási díjakat vonhat maga után.
> A szüneteltetett SQL-készlet naplózása nem engedélyezés. Az engedélyezéshez állítsa le az SQL-készlet szüneteltetésének szüneteltetését.

Ha naplóírást szeretne beállítani egy eseményközpontba, válassza **az Event Hub (előzetes verzió)** lehetőséget, és nyissa meg **az Event Hub adatait.** Jelölje ki azt az eseményközpontot, ahol a naplókat írni fogja, majd kattintson az **OK**gombra. Győződjön meg arról, hogy az eseményközpont ugyanabban a régióban van, mint az adatbázis és a kiszolgáló.

   ![Eventhub (Rendezvényközpont)](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Naplónaplók és jelentések elemzése

Ha úgy dönt, hogy naplónaplókat ír az Azure Monitor naplóiba:

- Használja az [Azure Portalon.](https://portal.azure.com)  Nyissa meg a megfelelő adatbázist. Az adatbázis **Naplózáslapjának** tetején kattintson a **Naplónaplók megtekintése**elemre.

    ![naplómegtekintése](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Ezután kétféleképpen tekintheti meg a naplókat:
    
    A **Naplózási adatok** lap tetején a **Log Analytics** elemre kattintva megnyílik a Log Analytics munkaterület naplók nézete, ahol testreszabhatja az időtartományt és a keresési lekérdezést.
    
    ![megnyitás a Log Analytics-munkaterületen](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    A **Rekordok naplózása** lap tetején a **Nézet irányítópultra** kattintva megnyílik a naplóadatokat megjelenítő irányítópult, ahol leáshat a Security Insights, a Bizalmas adatokhoz való hozzáférés és egyebek beszüntetésébe. Ez az irányítópult arra szolgál, hogy segítséget nyújtson az adatok biztonsági elemzési adataihoz.
    Az időtartományt és a keresési lekérdezést is testreszabhatja. 
    ![A Log Analytics irányítópultjának megtekintése](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics irányítópult](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics biztonsági elemzések](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Azt is megteheti, hogy a naplónaplók a Log Analytics panelről is hozzáférhet. Nyissa meg a Log Analytics-munkaterületet, és az **Általános** csoportban kattintson a **Naplók gombra.** Kezdheti egy egyszerű lekérdezéssel, például: *keresés "SQLSecurityAuditEvents"* a naplónaplók megtekintéséhez.
    Itt is [használhatja](../log-analytics/log-analytics-log-search.md) az Azure Monitor naplók speciális keresések futtatásához a naplóadatok. Az Azure Monitor naplói valós idejű operatív elemzéseket nyújtanak az integrált keresési és egyéni irányítópultok használatával, amelyek segítségével több millió rekordot elemezhet az összes számítási feladatában és kiszolgálóján. Az Azure Monitor naplóinak keresési nyelvéről és parancsairól az [Azure Monitor naplóinak keresési hivatkozásában talál](../log-analytics/log-analytics-log-search.md)további hasznos információt.

Ha úgy döntött, hogy naplót ír az Event Hubba:

- A naplózási naplók adatainak felhasználásához az Event Hub, be kell állítania egy adatfolyamot az események felhasználásához, és írja azokat a cél. További információ: [Azure Event Hubs documentation](../event-hubs/index.yml).
- Az Event Hub naplói az Apache [Avro-események](https://avro.apache.org/) törzsében kerülnek rögzítésre, és A JSON-formázás használatával, UTF-8 kódolással tárolhatók. A naplónaplók olvasásához használhatja az [Avro Tools-t](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) vagy hasonló eszközöket, amelyek ezt a formátumot dolgozzák fel.

Ha úgy döntött, hogy naplót ír egy Azure-tárfiókba, számos módszer rel rendelkezik a naplók megtekintéséhez:

- A naplónaplók összesítése a telepítés során kiválasztott fiókban lesz. A naplónaplókat egy olyan eszköz, például az [Azure Storage Explorer](https://storageexplorer.com/)segítségével fedezheti fel. Az Azure storage-ban a naplózási naplók egy **sqldbauditlogs**nevű tárolóban blobfájlok gyűjteményeként kerülnek mentésre. A tárolómappák hierarchiájáról, az elnevezési konvenciókról és a naplóformátumról az SQL Database Audit Log Format című témakörben talál további [részleteket.](https://go.microsoft.com/fwlink/?linkid=829599)

- Használja az [Azure Portalon.](https://portal.azure.com)  Nyissa meg a megfelelő adatbázist. Az adatbázis **Naplózáslapjának** tetején kattintson a **Naplónaplók megtekintése**elemre.

    ![Navigációs ablaktábla](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    **Megnyílik a naplózási rekordok,** amelyekből megtekintheti a naplókat.

  - A rekordok naplózása lap tetején található **Szűrő** gombra kattintva tekinthet meg adott **dátumokat.**
  - A naplózási forrás váltásával válthat a *kiszolgáló naplózási házirendje* által létrehozott naplóbejegyzések és az *adatbázis-ellenőrzési házirend* **között.**
  - Csak az SQL-injektálással kapcsolatos naplózási rekordok megtekintéséhez jelölje be **a Csak az SQL-injekciók naplózási rekordjainak megjelenítése jelölőnégyzetet.**

       ![Navigációs ablaktábla]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- A **sys.fn_get_audit_file (T-SQL)** rendszerfüggő funkcióval táblázatos formátumban adja vissza a naplóadatokat. A funkció használatáról további információt a [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Az **Egyesítési naplózási fájlok** használata az SQL Server Management Studio rendszerben (az SSMS 17-től kezdődően):
    1. Az SSMS menüben válassza a > **Fájlegyesítési folyamat naplózási fájljainak****megnyitása** > parancsot. **File**

        ![Navigációs ablaktábla](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. Megnyílik a **Naplófájlok hozzáadása** párbeszédpanel. Válassza ki a **Hozzáadás** i beállítások egyikét, és válassza ki, hogy a naplófájlokat egy helyi lemezről egyesíti-e, vagy importálja-e őket az Azure Storage-ból. Meg kell adnia az Azure Storage adatait és a fiókkulcsot.

    3. Az összes egyesítésre bekövetkező fájl hozzáadása után kattintson az **OK** gombra az egyesítési művelet befejezéséhez.

    4. Az egyesített fájl megnyílik az SSMS-ben, ahol megtekintheti és elemezheti, valamint xel- vagy CSV-fájlba vagy táblába exportálhatja.

- Használja a Power BI-t. A Power BI-ban megtekintheti és elemezheti a naplóadatokat. További információt és letölthető sablon elérését a Naplóadatok elemzése a [Power BI-ban című témakörben talál.](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)
- Töltse le a naplófájlokat az Azure Storage blobtárolóból a portálon keresztül vagy egy olyan eszköz használatával, mint például [az Azure Storage Explorer.](https://storageexplorer.com/)
  - Miután helyileg letöltötte a naplófájlt, kattintson duplán a fájlra az SSMS-ben lévő naplók megnyitásához, megtekintéséhez és elemzéséhez.
  - Az Azure Storage Explorer segítségével egyszerre több fájlt is letölthet. Ehhez kattintson a jobb gombbal egy adott almappára, és válassza **a Mentés másként parancsot** a helyi mappába való mentéshez.

- További módszerek:

  - Több fájl vagy naplófájlokat tartalmazó almappa letöltése után helyileg egyesítheti őket az SSMS-egyesítési naplófájlokról szóló, korábban ismertetett utasításokban leírtak szerint.
  - Blob naplózási naplóinak megtekintése programozott módon:

    - [Kiterjesztett eseményfájlok lekérdezése](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) a PowerShell használatával.

## <a name="production-practices"></a><a id="production-practices"></a>Termelési gyakorlatok

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>Georeplikált adatbázisok naplózása

Georeplikált adatbázisok esetén, ha engedélyezi a naplózást az elsődleges adatbázisban, a másodlagos adatbázis azonos naplózási házirenddel fog rendelkezni. A másodlagos adatbázis naplózása is beállítható a **másodlagos kiszolgálón**történő naplózás engedélyezésével, az elsődleges adatbázistól függetlenül.

- Kiszolgálószintű **(ajánlott):** Kapcsolja be a naplózást mind az **elsődleges kiszolgálón,** mind a **másodlagos kiszolgálón** – az elsődleges és másodlagos adatbázisok naplózása a megfelelő kiszolgálószintű házirend alapján függetlenül történik.
- Adatbázisszintű: A másodlagos adatbázisok adatbázisszintű naplózása csak az elsődleges adatbázis naplózási beállításaiból konfigurálható.
  - A naplózást magának az *elsődleges adatbázisnak*kell engedélyeznie, nem a kiszolgálón.
  - Miután a naplózás engedélyezve van az elsődleges adatbázisban, a másodlagos adatbázisban is engedélyezve lesz.

    >[!IMPORTANT]
    >Az adatbázis-szintű naplózás, a tárolási beállítások a másodlagos adatbázis azonos lesz az elsődleges adatbázis, ami régiók közötti forgalmat. Azt javasoljuk, hogy csak kiszolgálószintű naplózást engedélyezze, és hagyja az adatbázisszintű naplózást letiltva az összes adatbázisra.

### <a name="storage-key-regeneration"></a>Tárolási kulcs regenerálása

Éles környezetben valószínűleg rendszeresen frissíti a tárolókulcsokat. Ha naplót ír az Azure Storage-ba, a kulcsok frissítése kor újra el kell mentenie a naplózási szabályzatot. A folyamat a következő:

1. **A Tárrészletek megnyitása**. A **Tárolóelérési kulcs** mezőben válassza a **Másodlagos**lehetőséget, majd kattintson az **OK**gombra. Ezután kattintson a **Mentés** gombra a naplózási konfigurációs lap tetején.

    ![Navigációs ablaktábla](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. Nyissa meg a tárolási konfigurációs lapot, és hozza létre újra az elsődleges hozzáférési kulcsot.

    ![Navigációs ablaktábla](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. Lépjen vissza a naplózási konfigurációs lapra, kapcsolja át a tárolóelérési kulcsot másodlagosról elsődlegesre, majd kattintson az **OK**gombra. Ezután kattintson a **Mentés** gombra a naplózási konfigurációs lap tetején.
4. Lépjen vissza a tárolási konfigurációs lapra, és hozza létre újra a másodlagos hozzáférési kulcsot (a következő kulcs frissítési ciklusának előkészítéseként).

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>Az Azure SQL Server és adatbázis-naplózásának kezelése

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

**PowerShell-parancsmagok (beleértve a WHERE záradék támogatását a további szűréshez):**

- [Adatbázis-naplózási házirend létrehozása vagy frissítése (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Kiszolgálónaplózási házirend létrehozása vagy frissítése (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Adatbázis-naplózási házirend beszereznie (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Kiszolgálónaplózási házirend bekérdezése (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Adatbázis-naplózási házirend eltávolítása (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Kiszolgálónaplózási házirend eltávolítása (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Például parancsfájlt a [Naplózás és a fenyegetésészlelés konfigurálása a PowerShell használatával című témakörben.](scripts/sql-database-auditing-and-threat-detection-powershell.md)

### <a name="using-rest-api"></a>A REST API használata

**REST API**:

- [Adatbázis-naplózási házirend létrehozása vagy frissítése](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Kiszolgálónaplózási házirend létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Adatbázis-naplózási házirend beszerezni](/rest/api/sql/database%20auditing%20settings/get)
- [Kiszolgálónaplózási házirend beszerezni](/rest/api/sql/server%20auditing%20settings/get)

Kiterjesztett házirend a WHERE záradékkal további szűréshez:

- [Adatbázis *–bővített* naplózási házirend létrehozása vagy frissítése](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Kiszolgáló – *bővített* naplózási házirend létrehozása vagy frissítése](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [*Adatbázis-kiterjesztett* naplózási házirend beszerezni](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Kiszolgáló *kiterjesztett* naplózási házirendjének beszereznie](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

Az Azure SQL-adatbázis-naplózást [az Azure Resource Manager-sablonok](../azure-resource-manager/management/overview.md) használatával kezelheti, ahogy az alábbi példákban látható:

- [Azure SQL Server üzembe helyezése auditálás engedélyezve az Azure Blob storage-fiók naplóinak írásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Azure SQL Server üzembe helyezése auditálás engedélyezve a naplónaplók naplózása a Log Analytics szolgáltatásba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Azure SQL Server üzembe helyezése naplózásengedélyezve naplónaplók írásához az Event Hubs-ba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> A csatolt minták egy külső nyilvános tárházban találhatók, és "ahogy van", garancia nélkül vannak megadva, és nem támogatottak a Microsoft támogatási programja/szolgáltatása.
