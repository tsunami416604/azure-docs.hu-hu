---
title: Csomagok végrehajtása az SSIS integrációs modul hibaelhárítás |} A Microsoft Docs
description: Ez a cikk a elhárításához nyújt útmutatást az SSIS-csomagok végrehajtása az SSIS integrációs modul
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
ms.openlocfilehash: f17c364d258ef356a98180c9903603d92a6a9245
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078520"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Hibaelhárítási-csomagok végrehajtása az SSIS integrációs modul

Ez a cikk a leggyakoribb hibákat, amelyek akkor ütközhet, amikor végrehajtása SSIS-csomagok az SSIS integrációs modul, a lehetséges okok és a műveletek a hibák megoldása tartalmazza.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Hol található hibaelhárítás a naplók

* Az ADF-portál segítségével a SSIS-csomag végrehajtása tevékenység többek között a végrehajtás eredménye, hibaüzeneteket és a művelet azonosítója. a kimenet ellenőrzése Részletek található [a folyamat figyelése](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* Az SSIS katalógus (SSISDB) tekintse meg a részletes naplókat végrehajtására használható. Részletes található [figyelő futtató csomagok és egyéb műveletek](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Gyakori hibák, okok és megoldás

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Chybová zpráva: `"Connection Timeout Expired."` vagy `"The service has encountered an error processing your request. Please try again."`

* Lehetséges ok és a javasolt művelet:
  * Az adatok forrás és a cél túl van terhelve. Ellenőrizze a terhelés, az adatokat forrás/cél, és rendelkezik-e elegendő a kapacitása. Például Azure SQL használata esetén ajánlott kell figyelembe venni méretezési fel, ha az adatbázis valószínűleg időtúllépés.
  * SSIS integrációs modul és az adatokat forrás és a cél között a hálózat nem stabil, különösen akkor, ha a kapcsolat nem régiók közötti vagy a helyszíni és az azure között. Javasolt újrapróbálkozási minta az SSIS-csomag alkalmazása a következő lépésekben:
    * Győződjön meg arról, hogy az SSIS-csomagok újrafuttathatja nélkül mellékhatása hiba esetén (például. az adatvesztést, adatok dup...)
    * Konfigurálja a **újra** és **újrapróbálkozási időköz** SSIS csomag tevékenység hajtsa végre az Általános lapon ![tulajdonságainak Általános lapján](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Az ADO.NET és OLEDB forrás és a cél-összetevő ConnectRetryCount és ConnectRetryInterval állíthatók be a kezelő az SSIS-csomag vagy SSIS-tevékenység

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Hibaüzenet jelenik meg: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Lehetséges ok és a javasolt művelet:
  * A probléma általában azt jelenti, hogy az adatokat forrás és a cél nem érhető el az SSIS integrációs modult, amely különböző okok miatt okozhatja:
    * Ellenőrizze, hogy megfelelően van átadja az adatokat forrás és a cél nevét/IP-cím
    * Győződjön meg arról, hogy a tűzfal megfelelően van-e beállítva
    * Győződjön meg arról, hogy a virtuális hálózat megfelelően van konfigurálva, ha az adatokat forrás és a cél helyi.
      * A probléma-e a vNet konfigurációját egy Azure virtuális gép ugyanazon a vneten kiépítésével ellenőrizheti. Majd ellenőrizze, hogy az adatokat forrás és a cél elérhető lesz az Azure virtuális gép
      * SSIS integrációs modul, a virtuális hálózat használatával kapcsolatos további részleteket talál [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Hibaüzenet: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* Lehetséges ok és a javasolt művelet:
  * A csomagban használt ADO.NET-szolgáltató nincs telepítve az SSIS integrációs modul. A szolgáltató az egyéni beállításai használatával is telepítheti. Egyéni telepítés részleteinek található [testreszabása az Azure-SSIS integrációs modul beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Hibaüzenet: "`The connection '...' is not found`"

* Lehetséges ok és a javasolt művelet:
  * Ez a hiba azért lehet, mert egy ismert probléma az SSMS régi verzióját. A csomag tartalmaz egy egyéni összetevőt (például SSIS Azure Feature Pack vagy 3. fél összetevők), amely nincs telepítve a számítógépen, ahol az ssms-ben az üzembe helyezés szolgál, ha az összetevő által SSMS eltávolításra kerül, és a hibát. Frissítés [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a legújabb verzióra, amely rendelkezik a probléma kijavítva.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Hibaüzenet jelenik meg: "Nem áll elég hely a lemezen"

* Lehetséges ok és a javasolt művelet:
  * Ez a hiba azt jelenti, hogy a helyi lemezt használja SSIS integrációs modul csomópontjának. Ellenőrizze-e a csomag vagy egyéni telepítés lenne felhasználása sok lemez szóközöket.
    * A lemez a csomagot használja fel, ha akkor szabadul fel a csomag végrehajtása befejeződése után.
    * Ha a lemezt az egyéni telepítés által felhasznált, szüksége lesz a SSIS integrációs modul leállításához, módosítsa a parancsfájlt, és indítsa újra az SSIS integrációs modul. A teljes Azure Blob-tároló megadott egyéni telepítés SSIS integrációs modul csomópont feletti lesznek másolva, ezért ellenőrizze, hogy van-e a tárolóban található minden felesleges tartalmat.

### <a name="error-message-cannot-open-file-"></a>Hibaüzenet jelenik meg: "Fájl nem nyitható meg a"...""

* Lehetséges ok és a javasolt művelet:
  * Ez a hiba akkor fordul elő, amikor a csomag végrehajtása a fájl nem található az SSIS integrációs modul helyi lemez.
    * Csomag, az SSIS integrációs modul végrehajtása abszolút elérési út használata nem ajánlott. Aktuális végrehajtási munkakönyvtár (.) vagy a temp mappa (% TEMP %) helyette.
    * Ha egyes fájlok SSIS integrációs modul csomópontján megőrzéséhez szükséges, ajánlott útján a fájlok előkészítéséhez [testre szabhatja a telepítő](how-to-configure-azure-ssis-ir-custom-setup.md). A végrehajtási munkakönyvtárban lévő fájlok törlődnek, a végrehajtás befejezése után.
    * Egy másik lehetőség, hogy az Azure File használata helyett az SSIS integrációs modul csomópontja tárolja. További részleteket tekinthet meg [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Hibaüzenet jelenik meg: "A"SSISDB"adatbázis elérte a mérete kvótáját"

* Lehetséges ok és a javasolt művelet:
  * Az Azure SQL vagy a felügyelt példányon létrehozott SSIS integrációs modul létrehozása során az SSISDB elérte a kvótáját.
    * Érdemes megfontolni a probléma megoldásához az adatbázis dtu-k. Részleteket tekinthet meg [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Ellenőrizze, hogy a csomag számos naplókat hoz létre. Ha igen, rugalmas feladat beállítható úgy, hogy ezek a naplók törlése. Tekintse meg [Azure Elastic Database-feladatok az SSISDB-naplók törlése](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) részletek.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Hibaüzenet jelenik meg: "A kérelem az adatbázis határértéke... és elérte."

* Lehetséges ok és a javasolt művelet:
  * Ha a csomagok számát az SSIS integrációs modul párhuzamosan futtatja, a hiba akkor jelentkezik, mert elérte a kérelem korlátozás az SSISDB. Érdemes megfontolni a DTC-t, az SSISDB a probléma megoldásához. Részleteket tekinthet meg [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Hibaüzenet jelenik meg: "SSIS-művelet sikertelen volt. váratlan művelet állapota: …"

* Lehetséges ok és a javasolt művelet:
  * A hiba többnyire egy átmeneti hiba okozza, így próbálja meg újra futtatni a csomag végrehajtása. Javasolt újrapróbálkozási minta az SSIS-csomag alkalmazása a következő lépésekben:
    * Ellenőrizze, hogy az SSIS-csomagok újra futtathatja a hiba nélkül mellékhatása (például az adatvesztést, adatok dup...)
    * Konfigurálja a **újra** és **újrapróbálkozási időköz** SSIS csomag tevékenység hajtsa végre az Általános lapon ![tulajdonságainak Általános lapján](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * Az ADO.NET és OLEDB forrás és a cél-összetevő ConnectRetryCount és ConnectRetryInterval állíthatók be a kezelő az SSIS-csomag vagy SSIS-tevékenység

### <a name="error-message-there-is-no-active-worker"></a>Hibaüzenet jelenik meg: "Nincs nincs aktív feldolgozó."

* Lehetséges ok és a javasolt művelet:
  * Ez a hiba általában azt jelenti, az SSIS integrációs modul egy nem megfelelő állapotban van. Ellenőrizze az állapotot és a részletek hibák az Azure Portalon: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Hibaüzenet jelenik meg: "Az integrációs modul nem lehet frissíteni, és végül fog tovább működni, mivel jelenleg nem érhető el az Azure Blob-tárolóba, a megadott egyéni telepítő."

* Ez a hiba akkor fordul elő, amikor SSIS integrációs modul nem tud hozzáférni a tárolási kapacitással, az egyéni telepítés. Ellenőrizze, hogy a megadott SAS Uri érvényes, és még nem járt le.

### <a name="package-takes-unexpected-long-time-to-execute"></a>Csomag végrehajtása váratlan hosszú időt vesz igénybe.

* Lehetséges ok és a javasolt művelet:
  * Túl sok csomagot végrehajtások a az SSIS integrációs modul be van ütemezve. Ebben az esetben egy üzenetsorba tartozó végrehajtásához viszont minden céljából vár.
    * Maximális párhuzamos végrehajtások száma kiszolgálónként integrációs modul csomópontok száma = * csomópontonkénti maximális párhuzamos végrehajtása
    * Tekintse meg [Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](create-azure-ssis-integration-runtime.md) , hogy hogyan állítsa be a csomópontok száma és a maximális párhuzamos végrehajtás csomópontonkénti.
  * SSIS integrációs modul le van állítva, vagy a nem kifogástalan állapotú. Ellenőrizze [Azure-SSIS integrációs modul](monitor-integration-runtime.md#azure-ssis-integration-runtime) segítségével ellenőrizheti az SSIS integrációs modul állapota és hibái.
  * Javasolt beállítani az időkorlátot, ha biztos benne, hogy a csomag végrehajtása kell befejeződött, egy bizonyos idő alatt: ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Gyenge teljesítményt, a csomag végrehajtása

* Lehetséges ok és a javasolt művelet:

  * Ellenőrizze, hogy van-e az SSIS integrációs modul az adatforrás és a cél ugyanabban a régióban.

  * Engedélyezze a "Teljesítmény" naplózási szint

      Beállíthatja, hogy a "Teljesítmény" gyűjtött részletes időtartama információk az egyes összetevők a végrehajtás a csomag végrehajtása naplózási szintjét. Részleteket tekinthet meg: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Ellenőrizze az integrációs modul teljesítményének az integrációs modul figyelő lap az Azure Portalon.
    * Hogyan SSIS integrációs modul monitorozása: [Az Azure-SSIS integrációs modul](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Az SSIS integrációs modul CPU/memória használati előzmények érhető el: a Data Factory a metrikák az Azure Portalon ![metrikák SSIS integrációs modul monitorozása](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
