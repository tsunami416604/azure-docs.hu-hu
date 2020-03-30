---
title: Csomagvégrehajtás – problémamegoldás az SSIS-integrációs futásidőben
description: Ez a cikk hibaelhárítási útmutatást nyújt az SSIS-csomagok ssis-integrációs futásidejű
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187478"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Csomagvégrehajtás – problémamegoldás az SSIS-integrációs futásidőben

Ez a cikk az OKAT a leggyakoribb hibákat tartalmazza, amelyek az SQL Server Integration Services (SSIS) csomagok SSIS-integrációs futásidejű végrehajtásakor találhatók. Leírja a hibák megoldásának lehetséges okait és lépéseit.

## <a name="where-to-find-logs-for-troubleshooting"></a>Hol találhatók hibaelhárítási naplók?

Az Azure Data Factory portálon ellenőrizheti az SSIS-csomag végrehajtási tevékenység kimenetét. A kimenet tartalmazza a végrehajtási eredményt, a hibaüzeneteket és a műveletazonosítót. További információt [a Folyamat figyelése](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)című témakörben talál.

Az SSIS-katalógus (SSISDB) segítségével ellenőrizze a végrehajtás részletes naplóit. További információt a [Futó csomagok és egyéb műveletek figyelése (Monitor Running Packages) és egyéb műveletek között talál.](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solutions"></a>Gyakori hibák, okok és megoldások

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Hibaüzenet: "A kapcsolat időtúllépése lejárt" vagy "A szolgáltatás hibát észlelt a kérés feldolgozása kor. Próbálkozzon újra.”

Az alábbiakban a lehetséges okokat és az ajánlott műveleteket olvashatja:
* Az adatforrás vagy a cél túlterhelt. Ellenőrizze az adatforrás vagy a célterhelés terhelését, és ellenőrizze, hogy elegendő kapacitással rendelkezik-e. Ha például az Azure SQL Database-t használta, fontolja meg a felskálázást, ha az adatbázis valószínűleg időbeli meghosszabbítása.
* Az SSIS-integrációs futásidő és az adatforrás vagy a cél közötti hálózat instabil, különösen akkor, ha a kapcsolat régiók közötti vagy a helyszíni és az Azure között van. Alkalmazza az újrapróbálkozási mintát az SSIS-csomagban az alábbi lépésekkel:
  * Győződjön meg arról, hogy az SSIS-csomagok mellékhatások (például adatvesztés vagy adatmásolás) nélkül újra futtathatók a hiba esetén.
  * Az **SSIS-csomag tevékenységének végrehajtása** **újra-** és **újrapróbálkozási időközének** konfigurálása az **Általános** lapon. ![](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Egy ADO.NET és OLE DB forrás- vagy célösszetevő esetén állítsa be a **ConnectRetryCount** és **a ConnectRetryInterval elemet** a Csatlakozáskezelőben az SSIS-csomagban vagy az SSIS-tevékenységben.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>A következő hibaüzenet jelenik meg: "Az ADO NET-forrás nem tudta megszerezni a kapcsolatot:...'" "Hálózattal vagy példányspecifikus hibával történt az SQL Server kiszolgálóval való kapcsolat létrehozása közben. A kiszolgáló nem található, vagy nem érhető el."

Ez a probléma általában azt jelenti, hogy az adatforrás vagy a cél nem érhető el az SSIS integrációs modulból. Ennek számos oka lehet. Próbálkozzon a következő műveletekkel:
* Ellenőrizze, hogy megfelelően adja-e meg az adatforrás vagy a célnevét/IP-címét.
* Ellenőrizze, hogy a tűzfal megfelelően van-e beállítva.
* Győződjön meg arról, hogy a virtuális hálózat megfelelően van konfigurálva, ha az adatforrás vagy a cél helyben van:
  * Ellenőrizheti, hogy a probléma a virtuális hálózati konfiguráció kiépítése egy Azure virtuális gép ugyanabban a virtuális hálózatban. Ezután ellenőrizze, hogy az adatforrás vagy a cél érhető el az Azure virtuális gép.
  * A virtuális hálózat SSIS-integrációs futásidejű használatával kapcsolatos további részleteket az [Azure-SSIS-integrációs futásidő](join-azure-ssis-integration-runtime-virtual-network.md)virtuális hálózathoz való csatlakozása.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>A következő hibaüzenet jelenik meg: "Az ADO NET-forrás nem tudta megszerezni a kapcsolatot:...'" a "Nem sikerült létrehozni egy felügyelt kapcsolatkezelőt."

Ennek az az oka, hogy a csomagban használt ADO.NET szolgáltató nincs telepítve az SSIS-integrációs futásórában. A szolgáltatót egyéni beállítással telepítheti. Az egyéni beállítással kapcsolatos további részleteket [az Azure-SSIS-integrációs futásidejű beállítás testreszabása című részben](how-to-configure-azure-ssis-ir-custom-setup.md)talál.

### <a name="error-message-the-connection--is-not-found"></a>Hibaüzenet: "A kapcsolat '...' nem található"

Az SQL Server Management Studio (SSMS) régebbi verzióinak egy ismert hibája okozhatja ezt a hibát. Ha a csomag olyan egyéni összetevőt tartalmaz (például SSIS Azure Feature Pack vagy partnerösszetevők), amely nincs telepítve azon a gépen, amelyen az SSMS-t az üzembe helyezéshez használják, az SSMS eltávolítja az összetevőt, és hibát okoz. Frissítse az [SSMS-t](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a probléma megoldásához utolsó verzióra.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Hibaüzenet:"SSIS Végrehajtó kilépési kód: -1073741819."

* Lehetséges ok és javasolt művelet:
  * Ez a hiba az Excel-forrás és -cél korlátozása miatt fordulhat elő, ha több Excel-forrás vagy cél párhuzamosan, többszálas állapotban hajt végre. Ezt a korlátozást úgy oldhatja meg, hogy módosítja az Excel-összetevőket, hogy egymás után hajtsa végre őket, vagy különböző csomagokra bontsa őket, és a "Package Task végrehajtása" művelettel indítsa el az ExecuteOutOfProcess tulajdonságot True értékkel.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Hibaüzenet: "Nincs elég hely a lemezen"

Ez a hiba azt jelenti, hogy a helyi lemez használatban van az SSIS-integrációs futásidejű csomópontban. Ellenőrizze, hogy a csomag vagy az egyéni beállítás sok lemezterületet foglal-e el:
* Ha a csomag felhasználja a lemezt, a csomag végrehajtása után felszabadul.
* Ha a lemezt az egyéni beállítás használja fel, le kell állítania az SSIS-integráció futásidejét, módosítania kell a parancsfájlt, és újra el kell indítania az integrációs futási időt. Az egyéni telepítéshez megadott teljes Azure blobtároló tmásolja az SSIS-integrációs futásidejű csomópontra, ezért ellenőrizze, hogy van-e felesleges tartalom a tároló alatt.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>A következő hibaüzenet jelenik meg: "Nem sikerült beolvasni az erőforrást a főkiszolgálóról. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Leírás:A fájl betöltése (***) nem sikerült."

* Lehetséges ok és javasolt művelet:
  * Ha az SSIS-tevékenység fájlrendszerből (csomagfájlból vagy projektfájlból) futtat csomagot, ez a hiba akkor fordul elő, ha a projekt, a csomag vagy a konfigurációs fájl nem érhető el az SSIS-tevékenységben megadott csomag-hozzáférési hitelesítő adatokkal
    * Ha Azure File-t használ:
      * A fájl elérési \\ \\ \<útjának\>a\\\<tárfiók nevével kell kezdődnie .file.core.windows.net fájlmegosztási elérési úttal\>
      * A tartománynak "Azure"-nak kell lennie
      * A felhasználónévnek \<tárfiók névnek kell lennie.\>
      * A jelszó \<nak tárolóhozzáférési kulcsnak kell lennie\>
    * Ha a helyszíni fájlt használja, ellenőrizze, hogy a virtuális hálózat, a csomaghozzáférési hitelesítő adatok és az engedélyek megfelelően vannak-e konfigurálva, hogy az Azure-SSIS-integrációs futásidejű hozzáférhessen a helyszíni fájlmegosztáshoz

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Hibaüzenet: "A fájlnév'...' a kapcsolatban megadott beállítás érvénytelen"

* Lehetséges ok és javasolt művelet:
  * Érvénytelen fájlnév van megadva
  * Győződjön meg arról, hogy a kapcsolatkezelőben nem rövid idő alatt fqdn (teljesen minősített tartománynév) nevet használ

### <a name="error-message-cannot-open-file-"></a>A következő hibaüzenet jelenik meg: "A fájl nem nyitható meg...'"

Ez a hiba akkor fordul elő, ha a csomag végrehajtása nem talál egy fájlt a helyi lemezen az SSIS-integrációs futásidőben. Próbálkozzon a következő műveletekkel:
* Ne használja az abszolút elérési utat a csomagban, amely végrehajtása az SSIS-integrációs futásidőben. Az aktuális végrehajtási munkakönyvtár (.) vagy az ideiglenes mappa (%TEMP%) használata Helyett.
* Ha meg kell persistninéhány fájlt az SSIS-integrációs futásidejű csomópontokon, készítse elő a fájlokat a [Telepítés testreszabása című](how-to-configure-azure-ssis-ir-custom-setup.md)részben leírtak szerint. A munkakönyvtárban lévő összes fájl törlődik a végrehajtás befejezése után.
* Azure Files használata a fájl tárolása helyett az SSIS-integrációs futásidejű csomópont. További információt az [Azure-fájlmegosztások használata (Use Azure file shares) (Az Azure-fájlmegosztások használata) (Azure-fájlmegosztás](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Hibaüzenet: "Az adatbázis 'SSISDB' elérte a méretkvótáját"

Ennek lehetséges oka, hogy az Azure SQL-adatbázisban létrehozott SSISDB adatbázis vagy egy felügyelt példány az SSIS integrációs modul létrehozásakor elérte a kvótáját. Próbálkozzon a következő műveletekkel:
* Fontolja meg az adatbázis DTU-jának növelését. Ezzel kapcsolatban az [Azure SQL Database-kiszolgálóra vonatkozó SQL Database-erőforráskorlátokat ismertető](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) cikkben talál további információt.
* Ellenőrizze, hogy a csomag sok naplót fog-e létrehozni. Ha igen, egy rugalmas feladatot is beállíthat ezen naplók törléséhez. További részletekért tekintse meg az [SSISDB-naplók törlése Azure-beli rugalmas adatbázis-feladatokkal](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) című cikket.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Hibaüzenet: "Az adatbázis kéréskorra vonatkozó korlátja ... és elérték."

Ha az SSIS-integrációs futtatóórában számos csomag fut párhuzamosan, ez a hiba azért fordulhat elő, mert az SSISDB elérte a kérelemkorlátot. A probléma megoldásához fontolja meg az SSISDB DTC-jének növelését. Ezzel kapcsolatban az [Azure SQL Database-kiszolgálóra vonatkozó SQL Database-erőforráskorlátokat ismertető](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server) cikkben talál további információt.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>A következő hibaüzenet jelenik meg: "Az SSIS-művelet nem várt műveletállapottal nem sikerült: ..."

A hibát többnyire átmeneti probléma okozza, ezért próbálja meg újrafuttatni a csomag végrehajtását. Alkalmazza az újrapróbálkozási mintát az SSIS-csomagban az alábbi lépésekkel:

* Győződjön meg arról, hogy az SSIS-csomagok mellékhatások (például adatvesztés vagy adatmásolás) nélkül újra futtathatók a hiba esetén.
* Az **SSIS-csomag tevékenységének végrehajtása** **újra-** és **újrapróbálkozási időközének** konfigurálása az **Általános** lapon. ![](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Egy ADO.NET és OLE DB forrás- vagy célösszetevő esetén állítsa be a **ConnectRetryCount** és **a ConnectRetryInterval elemet** a Csatlakozáskezelőben az SSIS-csomagban vagy az SSIS-tevékenységben.

### <a name="error-message-there-is-no-active-worker"></a>Hibaüzenet: "Nincs aktív dolgozó."

Ez a hiba általában azt jelenti, hogy az SSIS-integrációs futásidejű állapota nem megfelelő. Ellenőrizze az Azure Portalon az állapot és a részletes hibák. További információ: [Azure-SSIS integrációs futásidő.](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Hibaüzenet jelenik meg: "Az integrációs futásidejű nem frissíthető, és végül leáll, mivel nem tudjuk elérni az egyéni telepítéshez megadott Azure Blob-tárolót."

Ez a hiba akkor fordul elő, ha az SSIS-integrációs futásidő nem fér hozzá az egyéni telepítéshez konfigurált tárolóhoz. Ellenőrizze, hogy a megadott megosztott hozzáférésű aláírás (SAS) URI-je érvényes-e, és nem járt-e le.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Hibaüzenet: "Microsoft OLE DB Provider for Analysis Services. "Hresult: 0x80004005 Leírás:" COM hiba: COM hiba: mscorlib; Kivételt tett a felkérés célpontja"

Az egyik lehetséges oka az, hogy a felhasználónév vagy jelszó az Azure Multi-Factor Authentication engedélyezve van konfigurálva az Azure Analysis Services hitelesítés. Ez a hitelesítés nem támogatott az SSIS-integrációs futásidő. Próbáljon meg egy egyszerű szolgáltatás az Azure Analysis Services hitelesítéséhez:

1. Az Automation with service principals című részben leírtak szerint készítse el a [szolgáltatásnévszolgáltatást.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. A Csatlakozáskezelőben konfigurálja **a Megadott felhasználónév és jelszó használata**beállítást: az **AppID** beállítása felhasználónévként, **az ügyféltitkos kulcsot** pedig jelszóként.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>A következő hibaüzenet: "Az ADONET-forrás nem tudta megszerezni a(z) {GUID} kapcsolatot a következő hibaüzenettel: Felügyelt identitás használata esetén nem sikerült bejelentkezni az "NT AUTHORITY\ANONYMOUS LOGON" felhasználónál.

Győződjön meg arról, hogy nem konfigurálja a Csatlakozáskezelő hitelesítési módszerét **Active Directory jelszó-hitelesítésként,** ha a *ConnectUsingManagedIdentity* paraméter **igaz**. A *connectUsingManagedIdentity* beállítása esetén **sql-hitelesítésként** konfigurálható, amelyet a rendszer figyelmen kívül hagy.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Hibaüzenet: "0xC020801F at ..., OData Source [...]: Nem lehet felügyelt kapcsolatot létesíteni a futásidejű kapcsolatkezelőtől"

Ennek egyik lehetséges oka az, hogy a Transport Layer Security (TLS) nem engedélyezi az SSIS-integrációs futásidejű, amely az OData-forrás által megkövetelt. A TLS-t az SSIS-integrációs futásidőben a Testreszabás beállítással engedélyezheti. További részletek a Nem lehet csatlakozni a [Project Online Odata-hoz az SSIS-ből](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) és [a Testreszabás beállítás az Azure-SSIS integrációs futásidejéhez.](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Hibaüzenet: "Átmeneti feladat kérése guid művelettel ... hiba miatt: Nem sikerült elküldeni az átmeneti műveletet a következő hibaüzenettel: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Nem sikerült betölteni az adatproxyt."

Győződjön meg arról, hogy az Azure-SSIS-integrációs futásidejű konfigurálva van a saját üzemeltetésű integrációs futásidejű. További részletek a [Saját üzemeltetésű infravörös kapcsolat konfigurálása az Azure-SSIS IR proxyjaként az ADF-ben](self-hosted-integration-runtime-proxy-ssis.md)című részben találhatók.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Hibaüzenet: "Átmeneti feladat állapota: Nem sikerült. Átmeneti feladat hiba: ErrorCode: 2010, ErrorMessage: A saját üzemeltetett integrációs runtime ... offline állapotban van"

Győződjön meg arról, hogy a saját üzemeltetésű integrációs futásidejű telepítve van, és elindult. További részletek találhatók [létrehozása és konfigurálása saját üzemeltetésű integrációs runtime](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Hibaüzenet: "Átmeneti feladat hiba: ErrorCode: 2906, ErrorMessage: A csomag végrehajtása nem sikerült., Kimenet: {"OperationErrorMessages": "Hiba: A kért OLE DB-szolgáltató ... nincs regisztrálva. Ha a 64 bites illesztőprogram nincs telepítve, futtassa a csomagot 32 bites módban..."

Győződjön meg arról, hogy a csomagban lévő OLE DB-összekötők által használt megfelelő szolgáltató megfelelően van telepítve a saját üzemeltetésű integrációs futásidejű gépen. További részletek a [Saját üzemeltetésű infravörös kapcsolat konfigurálása az Azure-SSIS IR proxyjaként az ADF-ben](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Hiba: "Átmeneti feladat hiba: Hibakód: 2906, ErrorMessage: A csomag végrehajtása nem sikerült., Kimenet: {"OperationErrorMessages": "Hiba: System.IO.FileLoadException: Nem tölthető be a fájl vagy a következő szerelvény::Microsoft.WindowsAzure.Storage, Version=..., Kultúra=semleges, PublicKeyToken=31bf3856ad364e35' vagy annak egyik függősége. A található szerelvény jegyzékfájljának definíciója nem egyezik meg a kódösszeállítás-hivatkozással." ..."

Az egyik lehetséges ok a saját üzemeltetésű integrációs futásidejű nincs megfelelően telepítve vagy frissítve. Javasoljuk, hogy töltse le és telepítse újra a legújabb self-hosted integrációs runtime. További részletek találhatók [létrehozása és konfigurálása saját üzemeltetésű integrációs runtime](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>A következő hibaüzenet jelenik meg: "A metaadatok kérésekor kapcsolat szükséges. Ha kapcsolat nélküli módban dolgozik, törölje a jelet a Kapcsolat nélküli munka menü kapcsolat nélküli munka menüjében a kapcsolat engedélyezéséhez"

* Lehetséges ok és javasolt művelet:
  * Ha a végrehajtási naplóban a "Az összetevő nem támogatja a csatlakozáskezelő true" beállítással történő csatlakozáskezelő használatát, ez azt jelenti, hogy a kapcsolatkezelőt olyan összetevőn használják, amely még nem támogatja a "ConnectByProxy" értéket. A támogatott összetevők az [Azure-SSIS IR konfigurálása az ADF-ben az Azure-SSIS IR proxyjaként](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) című helyen találhatók.
  * A végrehajtási napló megtalálható az [SSMS-jelentésben](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) vagy az SSIS csomagvégrehajtási tevékenységben megadott naplómappában.
  * a virtuális hálózat alternatív a helyszíni adatok elérésére is használható. További részletek a [Join an Azure-SSIS integrációs futásidejű virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Hibaüzenet: "Átmeneti feladat állapota: Nem sikerült. Átmeneti feladathiba: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "... \\SSISTelemettry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Győződjön meg arról, hogy a Visual C++ futásidejű telepítve van a saját üzemeltetésű integrációs üzemidejű gépen. További részletek a [Saját üzemeltetésű infravörös kapcsolat konfigurálása az Azure-SSIS IR proxyjaként az ADF-ben](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>A csomag több végrehajtása váratlanul aktiválódik

* Lehetséges ok és javasolt művelet:
  * Az ADF tárolt eljárási tevékenysége vagy a lookup tevékenység az SSIS-csomag végrehajtásának elindításához használatos. A t-sql parancs átmeneti problémát okozhat, és aktiválhatja az ismétlést, amely több csomagvégrehajtást okozna.
  * Használja ExecuteSSISPackage tevékenység helyett, amely biztosítja a csomag végrehajtása nem fut újra, kivéve, ha a felhasználói készlet újrapróbálkozások száma a tevékenységben. A részletek a[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * A t-sql parancs finomítása, hogy újra lehessen futni, ha ellenőrzi, hogy egy végrehajtás már megtörtént-e.

### <a name="package-execution-takes-too-long"></a>A csomag végrehajtása túl sokáig tart

Az alábbiakban a lehetséges okokat és az ajánlott műveleteket olvashatja:

* Túl sok csomagvégrehajtás van ütemezve az SSIS-integrációs futásidőben. Ezek a kivégzések sorban fognak állni, hogy sorba állnak.
  * A maximális értéket a következő képlettel határozzuk meg:

    Maximális párhuzamos végrehajtási szám infravörös / csomópontszámonként * Maximális párhuzamos végrehajtás csomópontonként
  * A csomópontszám és a csomópontonkénti maximális párhuzamos végrehajtás beállításáról az [Azure-SSIS-integrációs futásidő létrehozása az Azure Data Factoryban című témakörben](create-azure-ssis-integration-runtime.md)olvashat.
* Az SSIS-integrációs futásidejű leáll, vagy sérült állapotú. Az SSIS-integrációs futásidejű állapot és hibák ellenőrzéséről az [Azure-SSIS-integrációs futásidejű](monitor-integration-runtime.md#azure-ssis-integration-runtime)című témakörben olvashat.

Azt is javasoljuk, hogy állítson be ![időhosszabbítást az](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **Általános** lapon: Tulajdonságok beállítása az Általános lapon .

### <a name="poor-performance-in-package-execution"></a>Gyenge teljesítmény a csomag végrehajtása során

Próbálkozzon a következő műveletekkel:

* Győződjön meg arról, hogy az SSIS-integrációs futásidejű ugyanabban a régióban van, mint az adatforrás és a cél.

* Állítsa a csomag végrehajtás naplózási szintjét **Teljesítmény** beállításra, hogy a végrehajtás minden egyes összetevőjéhez adatokat gyűjtsön. További információt az [Integrációs szolgáltatások (SSIS) naplózása című témakörben talál.](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Ellenőrizze az infravörös csomópontok teljesítményét az Azure Portalon:
  * Az SSIS-integrációs futásidő figyeléséről az [Azure-SSIS-integrációs futásidejű](monitor-integration-runtime.md#azure-ssis-integration-runtime)című témakörben talál további információt.
  * Az SSIS-integrációs futásidejű CPU-/memóriaelőzményeket az Azure Portalon az adatgyár metrikáinak megtekintésével találhatja meg.
    ![Az SSIS-integrációs futásidejű metrikák figyelése](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
