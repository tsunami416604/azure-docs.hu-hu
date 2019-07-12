---
title: Csomagok végrehajtása az SSIS integrációs modul az hibaelhárítása |} A Microsoft Docs
description: Ez a cikk nyújt hibaelhárítási útmutatót az SSIS integrációs modult az SSIS-csomag végrehajtása
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621851"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Csomagok végrehajtása az SSIS integrációs modul az hibaelhárítása

Ez a cikk tartalmazza a leggyakoribb hibákat, amelyek, még az SSIS integrációs modul az SQL Server Integration Services (SSIS) csomagjainak végrehajtása közben. A hibák megoldása a lehetséges okait és műveleteket ismerteti.

## <a name="where-to-find-logs-for-troubleshooting"></a>Hol található a naplók hibaelhárítási

Az Azure Data Factory-portál segítségével ellenőrizze az SSIS-csomag végrehajtása tevékenység kimenetét. A kimenet tartalmazza a végrehajtás eredménye, hibaüzeneteket és a művelet azonosítója. További információkért lásd: [a folyamat figyelése](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Az SSIS-katalógus (SSISDB) használatával tekintse meg a részletes naplókat végrehajtására. További információkért lásd: [figyelő futtató csomagok és egyéb műveletek](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Gyakori hibák, okait és megoldásait

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Hibaüzenet jelenik meg: "Kapcsolat időkorlátja lejárt" vagy "a szolgáltatás észlelt hiba történt a kérelem feldolgozása közben. Próbálkozzon újra."

Az alábbiakban a lehetséges okok és a javasolt művelet:
* Az adatok forrása vagy túl van terhelve. Ellenőrizze az adatforrás vagy a cél a terhelés, és rendelkezik-e elegendő a kapacitása. Például ha használta az Azure SQL Database, érdemes felskálázni, ha az adatbázis valószínűleg időtúllépés.
* Az SSIS integrációs modul és az adatforrás vagy a cél között a hálózat nem stabil, különösen akkor, ha a kapcsolat nem régiók közötti vagy a helyszíni és az Azure között. Az újrapróbálkozási minta az SSIS-csomag a alkalmazni az alábbi lépéseket:
  * Ellenőrizze, hogy az SSIS-csomagok hiba esetén (például az adatvesztést vagy adatdeduplikáció) hatásai nélkül futtathatja.
  * Konfigurálása **újra** és **újrapróbálkozási időköz** , **SSIS-csomag végrehajtása** tevékenységet a **általános** lapon. ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Az ADO.NET és OLE DB forrás vagy cél-összetevő beállítása **ConnectRetryCount** és **ConnectRetryInterval** a Csatlakozáskezelő az SSIS-csomag vagy SSIS-tevékenység.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Hibaüzenet jelenik meg: "A"..."beszerezni a kapcsolatot a ADO NET forrás nem sikerült" az "A hálózattal kapcsolatos vagy példányspecifikus hiba történt az SQL Server-kapcsolat létrehozásakor. A kiszolgáló nem található vagy nem volt elérhető."

A probléma általában azt jelenti, hogy az adatforrás, vagy a cél nem érhető el, az SSIS integrációs modul. Az okok eltérőek lehetnek. Próbálja ki ezeket a műveleteket:
* Ellenőrizze, hogy az adatok forrása vagy áthaladva is nevét/IP-cím megfelelően.
* Győződjön meg arról, hogy a tűzfal megfelelően van-e beállítva.
* Győződjön meg arról, hogy a virtuális hálózat megfelelően van konfigurálva, ha az adatok forrása vagy a helyszínen:
  * Ellenőrizheti, hogy a probléma-e a virtuális hálózati konfiguráció szerint az azonos virtuális hálózatban lévő Azure virtuális gép kiépítése. Ezután ellenőrizze, hogy az adatforrás vagy a cél elérhető lesz az Azure virtuális Gépen.
  * Virtuális hálózat egy SSIS integrációs modul az való használatával kapcsolatos további információk [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Hibaüzenet jelenik meg: "A"..."beszerezni a kapcsolatot a ADO NET forrás nem sikerült" a "Nem sikerült létrehozni a felügyelt kapcsolatot kezelő."

A lehetséges oka, hogy a csomagban használt ADO.NET-szolgáltató nincs telepítve az SSIS integrációs modul. A szolgáltató egy egyéni telepítés használatával telepítheti. További információt az egyéni telepítés annak [testreszabása az Azure-SSIS integrációs modul beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Hibaüzenet jelenik meg: "Kapcsolat"..." nem található"

Egy ismert probléma az SQL Server Management Studio (SSMS) régebbi verziójú ezt a hibát okozhat. Ha a csomag tartalmaz egy egyéni összetevő (például SSIS Azure Feature Pack vagy -partnerétől összetevők), amely nincs telepítve a számítógépen, ahol az ssms-ben az üzembe helyezés szolgál, SSMS távolítsa el az összetevőt, és okozza a problémát. Frissítés [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a legújabb verzióra, amely rendelkezik a probléma kijavítva.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Hibaüzenet: "SSIS-végrehajtó kilépési kód: 1073741819 -es."

* Lehetséges ok és a javasolt művelet:
  * Ez a hiba lehet az Excel-forrás és cél korlátozás miatt, ha több Excel források vagy célok állnak végrehajtás alatt párhuzamosan több szálon. Kerülő megoldás ezt a korlátozást úgy módosítsa a sorrendben hajtsa végre, vagy válassza el őket az eseményindító "Feladat végrehajtása a csomag" keresztül és a különböző csomagokban megtalál mindent ExecuteOutOfProcess tulajdonság értéke igaz, az Excel-összetevők is.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Hibaüzenet jelenik meg: "Nem áll elég hely a lemezen"

Ez a hiba azt jelenti, hogy a helyi lemez szerepel az SSIS integrációs modul csomópontját. Ellenőrizze-e a csomag vagy egyéni telepítés fogyassza sok lemezterület:
* A lemez a csomagot használja fel, ha akkor szabadul fel a csomag végrehajtása befejeződése után.
* Ha a lemezt az egyéni telepítés használja fel, fog kell leállítani az SSIS integrációs modul, módosítsa a parancsfájlt, és indítsa újra az integration runtime. A teljes Azure blob-tároló megadott egyéni telepítés lesznek másolva az SSIS integrációs modul csomópontját, így ellenőrzi, hogy van-e a tárolóban található minden felesleges tartalmat.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Hibaüzenet jelenik meg: "Nem sikerült lekérdezni a fő erőforrás. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code:300004. Fájl leírása: betöltése "x" nem sikerült. "

* Lehetséges ok és a javasolt művelet:
  * Az SSIS-tevékenység végrehajtása csomag fájlrendszer (alkalmazáscsomag-fájl vagy soubor projektu), ha ez a hibaüzenet akkor, ha a projektet, a csomagot vagy a konfigurációs fájl nem érhető el az SSIS-tevékenység található a megadott csomag hozzáférési hitelesítő adataival
    * Ha az Azure File használja:
      * A fájl elérési útját a következővel kell kezdődnie \\ \\ \<tárfióknevet\>. file.core.windows.net\\\<fájlmegosztás elérési útja\>
      * A tartományban kell lennie az "Azure"
      * A felhasználónév legyen \<tárfiók neve\>
      * A jelszónak kell lennie \<tárelérési kulcs\>
    * Ha a következők használatával a helyszíni fájlkiszolgálók, ellenőrizze, ha a virtuális hálózat, a csomag eléréséhez szükséges hitelesítő adatokat és az engedély megfelelően van beállítva, hogy az Azure-SSIS integrációs modul elérjék a helyszíni fájlmegosztás

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Hibaüzenet jelenik meg: "A fájl nevét a"..." a megadott a kapcsolat nem volt érvényes "

* Lehetséges ok és a javasolt művelet:
  * Érvénytelen fájlnév van megadva.
  * Ellenőrizze, hogy használ helyett rövid időn (teljes tartománynév) teljes Tartománynevét a Csatlakozáskezelő

### <a name="error-message-cannot-open-file-"></a>Hibaüzenet jelenik meg: "Fájl nem nyitható meg a"...""

Ez a hiba akkor fordul elő, amikor a csomag végrehajtása egy fájlt a helyi lemezen lévő az SSIS integrációs modul nem található. Próbálja ki ezeket a műveleteket:
* Ne használjon abszolút elérési útját a csomagot, amely az SSIS integrációs modul az folyamatban van. Az aktuális végrehajtási munkakönyvtár (.) vagy a temp mappa (% TEMP %) helyette.
* Ha néhány fájlt a SSIS integrációs modul csomópontjait megőrzéséhez van szüksége, készítse elő a fájlok leírtak szerint [testre szabhatja a telepítő](how-to-configure-azure-ssis-ir-custom-setup.md). A munkakönyvtárban lévő fájlok törlődnek, a végrehajtás befejezése után.
* Az Azure Files használata helyett a fájl az SSIS integrációs modul csomópontja tárolja. További információkért lásd: [az Azure-fájlmegosztások](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Hibaüzenet jelenik meg: "A"SSISDB"adatbázis elérte a mérete kvótáját"

Egy lehetséges oka, hogy az SSISDB-adatbázis létrehozásakor egy SSIS integrációs modult az Azure SQL database, vagy egy felügyelt példányra létrehozott elérte a kvótáját. Próbálja ki ezeket a műveleteket:
* Érdemes megfontolni a dtu-k, az adatbázis. Az információk [egy Azure SQL Database-kiszolgálóhoz tartozó SQL Database erőforráskorlátok](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Ellenőrizze, hogy a csomag számos naplókat hoz létre. Ha igen, ezek a naplók karbantartása rugalmas feladat konfigurálhatja. További információkért lásd: [Azure rugalmas adatbázis-feladatok az SSISDB-naplók törlése](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Hibaüzenet jelenik meg: "A kérelem az adatbázis határértéke... és elérte."

Csomagok számát az SSIS integrációs modul párhuzamosan futnak, ha ez a hiba fordulhat elő, mert az SSISDB eléri a kérelmi korlátjának. Érdemes megfontolni az SSISDB DTC-től a probléma megoldásához. Az információk [egy Azure SQL Database-kiszolgálóhoz tartozó SQL Database erőforráskorlátok](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Hibaüzenet jelenik meg: "SSIS-művelet sikertelen volt. váratlan művelet állapota: …"

A hiba többnyire egy átmeneti hiba okozza, így próbálja meg újra futtatni a csomag végrehajtása. Az újrapróbálkozási minta az SSIS-csomag a alkalmazni az alábbi lépéseket:

* Ellenőrizze, hogy az SSIS-csomagok hiba esetén (például az adatvesztést vagy adatdeduplikáció) hatásai nélkül futtathatja.
* Konfigurálása **újra** és **újrapróbálkozási időköz** , **SSIS-csomag végrehajtása** tevékenységet a **általános** lapon. ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Az ADO.NET és OLE DB forrás vagy cél-összetevő beállítása **ConnectRetryCount** és **ConnectRetryInterval** a Csatlakozáskezelő az SSIS-csomag vagy SSIS-tevékenység.

### <a name="error-message-there-is-no-active-worker"></a>Hibaüzenet jelenik meg: "Nincs nincs aktív feldolgozó."

Ez a hiba általában azt jelenti, az SSIS integrációs modul állapota nem kifogástalan. Ellenőrizze az Azure Portalon, az állapot és a részletes hibaüzenetek. További információkért lásd: [Azure-SSIS integrációs modul](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Hibaüzenet jelenik meg: "Az integrációs modul nem lehet frissíteni, és végül fog tovább működni, mivel jelenleg nem érhető el az Azure Blob-tárolóba, a megadott egyéni telepítő."

Ez a hiba akkor fordul elő, ha az SSIS integrációs modul nem tud hozzáférni a tárolási kapacitással, az egyéni telepítés. Ellenőrizze, hogy a közös hozzáférésű jogosultságkód (SAS) URI-t, amely a megadott érvényes, és még nem járt le.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Hibaüzenet jelenik meg: "A Microsoft OLE DB-szolgáltató az Analysis Serviceshez. "Hresult: 0x80004005 leírását: " COM-hiba: COM-hiba: mscorlib; A meghívott objektum kivételt adott vissza"

Egy lehetséges oka, hogy az Azure Analysis Services hitelesítési felhasználónév vagy jelszó az Azure multi-factor Authentication engedélyezve van konfigurálva. A hitelesítés az SSIS integrációs modul nem támogatott. Próbálja meg egy egyszerű szolgáltatást az Azure Analysis Services-hitelesítéshez használandó:
1. Egyszerű szolgáltatás előkészítése leírtak szerint [automatizálása szolgáltatásnevekkel](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. A Csatlakozáskezelő konfigurálása **megadott felhasználónévvel és jelszóval**: állítsa be **AppID** felhasználóneve és **clientSecret** jelszóként.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Hibaüzenet jelenik meg: "ADONET forrás nem sikerült beszerezni a kapcsolat {GUID} a következő hiba miatt: "NT AUTHORITY\NÉVTELEN bejelentkezés" felhasználó bejelentkezése sikertelen volt "egy felügyelt identitás használata

Ellenőrizze, hogy nem konfigurálja a hitelesítési módszer a Csatlakozáskezelő, **Active Directory jelszavas hitelesítést** Ha a paraméter *ConnectUsingManagedIdentity* van **igaz** . Beállíthatja úgy, mint **SQL-hitelesítés** helyette, amely figyelmen kívül hagyja Ha *ConnectUsingManagedIdentity* van beállítva.

### <a name="package-execution-takes-too-long"></a>Csomag végrehajtása túl sokáig tart.

Az alábbiakban a lehetséges okok és a javasolt művelet:
* Túl sok csomagot végrehajtások a az SSIS integrációs modul be van ütemezve. Minden céljából egy üzenetsorban lévő viszont Várakozás lesz.
  * A képlettel határozza meg a maximális: 
    
    Maximális párhuzamos végrehajtások száma kiszolgálónként integrációs modul csomópontok száma = * csomópontonkénti maximális párhuzamos végrehajtása
  * Ismerje meg, hogyan állíthatja be a csomópontok száma és a csomópontonkénti maximális párhuzamos futtatáshoz, lásd: [Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](create-azure-ssis-integration-runtime.md).
* Az SSIS integrációs modul le van állítva, vagy a nem kifogástalan állapotú. Ellenőrizze az SSIS integrációs modul állapotának és a hibák kezelésével kapcsolatos információkért lásd: [Azure-SSIS integrációs modul](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Azt javasoljuk, hogy a beállítása egy időtúllépési a **általános** lapon: ![Az Általános lapon állítsa be a tulajdonságokat](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Gyenge teljesítményt, a csomag végrehajtása

Próbálja ki ezeket a műveleteket:

* Győződjön meg arról, az SSIS integrációs modul adatokat forrás és cél és ugyanabban a régióban.

* A csomag végrehajtása naplózási szintjének beállítása **teljesítmény** az egyes összetevők meg a végrehajtási időtartamának információkat gyűjthet. További információkért lásd: [Integration Services (SSIS) naplózás](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Ellenőrizze az integrációs modul teljesítményének az Azure Portalon:
  * Az SSIS integrációs modul monitorozása kapcsolatos információkért lásd: [Azure-SSIS integrációs modul](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Az adat-előállító metrikáinak megtekintése az Azure Portalon az SSIS integrációs modul CPU/memória előzmények keresheti.
    ![Az SSIS integrációs modul metrikák figyelése](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
