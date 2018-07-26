---
title: Az Azure Data Factory integrációs modul monitorozása |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti a különböző típusú integrációs modult az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: douglasl
ms.openlocfilehash: 9c45b428a6d2060243f1eba9a284c7eb1b1b21c0
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259102"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Az Azure Data Factory integrációs modul monitorozása  
**Az Integration runtime** különböző adatintegrációs képességeket biztosít a különböző hálózati környezetekben az Azure Data Factory által használt számítási infrastruktúra áll. Integrációs modulok a Data Factory által kínált három típusa van:

- Azure-beli integrációs modul
- Helyi integrációs modul
- Azure SSIS integrációs modul

Az integrációs modul (IR) példányát állapotának lekéréséhez futtassa a következő PowerShell-parancsot: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

A parancsmag visszaadja a különböző adatokat a különböző típusú integrációs modult. Ez a cikk ismerteti a tulajdonságok és az állapotok az egyes integrációs modul.  

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul
A számítási erőforrás egy Azure-beli integrációs modul teljes körűen felügyelt rugalmasan az Azure-ban. Az alábbi táblázat ismerteti által visszaadott tulajdonságokat a **Get-AzureRmDataFactoryV2IntegrationRuntime** parancsot:

### <a name="properties"></a>Tulajdonságok
Az alábbi táblázat ismerteti az Azure-beli integrációs modul a parancsmag által visszaadott tulajdonságait:

| Tulajdonság | Leírás |
-------- | ------------- | 
| Name (Név) | Az Azure-beli integrációs modul neve. |  
| Állapot | Az Azure integrációs modul állapotának. | 
| Földrajzi egység | Az Azure-beli integrációs modul helye. További információk az Azure integrációs modul helye: [Bevezetés a saját üzemeltetésű integrációs](concepts-integration-runtime.md). |
| DataFactoryName | Az adat-előállítóhoz tartozó az Azure-beli integrációs modul neve. | 
| ResourceGroupName | Az erőforráscsoporthoz tartozik, az adat-előállító neve.  |
| Leírás | Az integrációs modul leírása.  |

### <a name="status"></a>Állapot
A következő táblázat tartalmazza az Azure-beli integrációs modul lehetséges állapotok:

| Állapot | Megjegyzések és forgatókönyvek | 
| ------ | ------------------ |
| Online | Az Azure-beli integrációs modul áll a használatra kész. | 
| Offline | Az Azure-beli integrációs modul az offline állapotú egy belső hiba miatt. |

## <a name="self-hosted-integration-runtime"></a>Helyi integrációs modul
Ez a szakasz ismerteti a Get-AzureRmDataFactoryV2IntegrationRuntime parancsmag által visszaadott tulajdonságokat. 

> [!NOTE] 
> A visszaadott tulajdonságait és általános saját üzemeltetésű integrációs modult, és a futtatókörnyezet lévő mindegyik csomópont adatait tartalmazzák.  

### <a name="properties"></a>Tulajdonságok

Az alábbi táblázat ismerteti tulajdonságainak figyelési **minden csomópont**:

| Tulajdonság | Leírás | 
| -------- | ----------- | 
| Name (Név) | A saját üzemeltetésű integrációs modul és a hozzá társított csomópontok neve. Csomópont egy helyszíni Windows-gépen, amelyen a saját üzemeltetésű integrációs modul telepítve. |  
| Állapot | Az általános saját üzemeltetésű integrációs modult és minden egyes csomópont állapotát. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért tekintse meg a következő szakaszban. | 
| Verzió | A saját üzemeltetésű integrációs modult és minden egyes csomópont verziója. A saját üzemeltetésű integrációs modul verzióját határozza meg a csoport csomópontjának többsége verzióján alapul. Ha a saját üzemeltetésű integrációs modul telepítése különböző verziójú csomópontok, csak a csomópontok, a logikai verziószámmal saját üzemeltetésű integrációs modul függvény megfelelően. Mások a korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés nem működik). | 
| Elérhető memória | Rendelkezésre álló memória egy saját üzemeltetésű integrációs modul csomópontján. Ez az érték közel valós idejű pillanatképet. | 
| Processzorkihasználtság | Egy saját üzemeltetésű integration runtime csomópontja CPU-felhasználását. Ez az érték közel valós idejű pillanatképet. |
| Hálózatkezelés (In/Out) | Egy saját üzemeltetésű integration runtime csomópontja hálózatfelhasználásáról. Ez az érték közel valós idejű pillanatképet. | 
| Egyidejű feladatok (futó / Limit) | **Futó**. Feladatok és minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. <br/><br/>**Korlát**. Korlát azt jelzi, hogy az egyidejű feladatok maximális száma minden egyes csomópont esetében. Ez az érték van megadva a mérete alapján. Vertikális felskálázás akár egyidejű feladat végrehajtása a speciális esetben, amikor a tevékenység időkorlátja még ha CPU, memória, vagy hálózati a kevésbé használt a korlát növeléséhez. Ez a funkció egy egycsomópontos saját üzemeltetésű integrációs modult a is érhető el. |
| Szerepkör | Dispatcher és feldolgozói szerepkörök a több csomópontos saját üzemeltetésű integrációs modul – a két típusa van. Minden csomópont a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. Nincs dispatcher csak egy csomópont, amelyet lekéréses feladatok/feladatok a cloud servicesből, és melyik másik feldolgozó csomópontokat. A kezelő csomópont egyben munkavégző csomópont. |

Egyes beállítások tulajdonságok további érthető legyen, ha két vagy több csomópont található a saját üzemeltetésű integrációs modul (azaz egy horizontális felskálázási forgatókönyv).

#### <a name="concurrent-jobs-limit"></a>Egyidejű feladatok korlát

Az alapértelmezett érték az egyidejű feladatok határérték van beállítva a gép mérete alapján. Ez az érték kiszámításához használt tényezők attól függ, a RAM mennyisége és a gép CPU-magok számát. Tehát a további Processzormagok száma és a memória, a magasabb az alapértelmezett korlátozza egyidejűleg futó feladatok száma.

A horizontális felskálázáshoz csomópontok számának növelése. Csomópontok számának növelése, az egyidejű feladatok határérték van-e az összes elérhető csomópont egyidejű feladat korlát értékeinek összegét.  Például ha egy csomópont engedélyezi, hogy legfeljebb 12 egyidejű feladatok futtatásához, majd a három több hasonló csomópontok hozzáadása lehetővé teszi, hogy legfeljebb 48 egyidejű feladatok (4 x 12) futtatása. Azt javasoljuk, hogy egyidejű feladatok korlátjának növelését, csak akkor, ha kevés az erőforrás-használat az alapértelmezett értékekre láthatja minden egyes csomóponton.

A számított alapértelmezett érték az Azure Portalon felül lehet bírálni. Válassza ki a szerző > kapcsolatok > integrációs modulok > Edi > csomópont > / csomópont egyidejű feladat érték módosítása. A PowerShell is használható [update-azurermdatafactoryv2integrationruntimenode](https://docs.microsoft.com/en-us/powershell/module/azurerm.datafactoryv2/update-azurermdatafactoryv2integrationruntimenode?view=azurermps-6.4.0#examples) parancsot.
  
### <a name="status-per-node"></a>Állapot (csomópontonkénti)
Az alábbi táblázatban egy saját üzemeltetésű integration runtime csomópontját a lehetséges állapotok:

| Állapot | Leírás |
| ------ | ------------------ | 
| Online | Csomópont csatlakoztatva van a Data Factory szolgáltatásban. |
| Offline | Csomópont offline állapotban. |
| Frissítés | A csomópont automatikus frissítése folyamatban van. |
| Korlátozott | Kapcsolódási probléma miatt. HTTP-port 8050 probléma, service bus kapcsolódási probléma vagy a hitelesítő adatok szinkronizálása kapcsolatos hiba miatt lehet. |
| Inaktív | Csomópontnak számít eltér a többi legtöbb csomópont konfigurációjának konfigurációban. |

Egy csomópont inaktív lehet, ha a többi csomópont nem tud kapcsolódni.

### <a name="status-overall-self-hosted-integration-runtime"></a>Állapot (általános saját üzemeltetésű integrációs modul)
A következő táblázat a lehetséges állapotok egy saját üzemeltetésű integrációs modul. Ez az állapot attól függ, hogy az összes olyan csomópontot, a futásidejű tartozó állapotok. 

| Állapot | Leírás |
| ------ | ----------- | 
| Regisztráció szükséges | Nem csomópont még a saját üzemeltetésű integrációs modul regisztrálva. |
| Online | Összes csomópontja online állapotban. |
| Offline | Nem csomópont online állapotú. |
| Korlátozott | A saját üzemeltetésű integrációs modul csomópontja nem minden kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti, hogy egyes csomópontok esetleg nem működik. Ez az állapot okozhatja egy credential szinkronizálási problémát a kezelő/munkavégző csomópont. |

Használja a **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** parancsmag használatával beolvassa a részletes tartalmazó JSON-adattartalom saját üzemeltetésű integrációs modul tulajdonságait, és a pillanatkép értékeket végrehajtásának ideje alatt a a parancsmag.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Példa a kimenetre (azt feltételezi, hogy nincsenek-e a saját üzemeltetésű integrációs modul társított két csomópont):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul
Az Azure-SSIS integrációs modul, az Azure virtuális gépeken (vagy csomópontok) dedikált az SSIS-csomagok futtatása egy teljes körűen felügyelt fürt. Nem futtatható az Azure Data Factory egyéb tevékenységeket. Kiosztása után már lekérdezése a tulajdonságait, és annak teljes/node-specifikus állapotok nyomon.

### <a name="properties"></a>Tulajdonságok

| A tulajdonság/állapot | Leírás |
| --------------- | ----------- |
| CreateTime | Az Azure-SSIS integrációs modul létrehozásának UTC ideje. |
| Csomópontok | A lefoglalt/elérhető csomópontok az Azure-SSIS integrációs modul konkrét csomóponthoz tartozó állapotok (elindítása/elérhető/újrahasznosítás vagy nem érhető el) és műveletet igénylő hibák. |
| OtherErrors | A nem csomópont-specifikus műveletet igénylő hibák az Azure-SSIS integrációs modulban. |
| LastOperation | Az Azure-SSIS integrációs modul sem gyakorlatban hasznosítható hibákkal utolsó indítása és leállítása műveletet eredménye. |
| Állapot | Az általános állapota (kezdeti/indítása/indítása/leállítása/leállítva) az Azure-SSIS integrációs modult. |
| Földrajzi egység | Az Azure-SSIS integrációs modul helye. |
| NodeSize | Az egyes csomópontok az Azure-SSIS integrációs modul méretét. |
| NodeCount | Az Azure-SSIS integrációs modult a csomópontok számát. |
| MaxParallelExecutionsPerNode | Az Azure-SSIS integrációs modult a csomópontonkénti párhuzamos végrehajtások száma. |
| CatalogServerEndpoint | A végpont a meglévő Azure SQL Database/Managed Instance (előzetes verzió) kiszolgáló SSISDB-gazdagépre. |
| CatalogAdminUserName | A meglévő Azure SQL Database/Managed Instance (előzetes verzió) kiszolgáló rendszergazdai felhasználóneve. A Data Factory szolgáltatás ezen információk segítségével készítheti elő és SSISDB kezelheti az Ön nevében. |
| CatalogAdminPassword | A meglévő Azure SQL Database/Managed Instance (előzetes verzió) kiszolgáló rendszergazdai jelszavát. |
| CatalogPricingTier | A meglévő Azure SQL Database-kiszolgáló által üzemeltetett SSISDB tarifacsomagja.  Az Azure SQL felügyelt példány (előzetes verzió) SSISDB üzemeltetési nem alkalmazható. |
| VNetId | A virtuális hálózati erőforrás-azonosító az Azure-SSIS integrációs modullal való csatlakozásra. |
| Alhálózat | Az alhálózat neve az Azure-SSIS integrációs modul csatlakozni. |
| Azonosító | Erőforrás-Azonosítóját az Azure-SSIS integrációs modult. |
| Type (Típus) | A típusa (felügyelt/önkiszolgáló-Hosted) az Azure-SSIS integrációs modult. |
| ResourceGroupName | Az Azure-erőforráscsoport, amelyben az adat-előállító és az Azure-SSIS integrációs modul létrehozott neve. |
| DataFactoryName | Az Azure-beli adat-előállító neve. |
| Name (Név) | Az Azure-SSIS integrációs modul neve. |
| Leírás | Az Azure-SSIS integrációs modul leírása. |

  
### <a name="status-per-node"></a>Állapot (csomópontonkénti)

| Állapot | Leírás |
| ------ | ----------- | 
| Indulás alatt | Ez a csomópont előkészítésére. |
| Elérhető | Ez a csomópont készen áll a SSIS-csomagok üzembe helyezése/végrehajtását. |
| Újrahasznosítása | Ez a csomópont folyamatban javítani vagy újraindítása. |
| Nem érhető el | Ez a csomópont nem áll készen az SSIS-csomagok üzembe helyezése/futtathat, és gyakorlatban hasznosítható hibák vagy problémák feloldására. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Állapot (teljes Azure-SSIS integrációs modul)

| Teljes állapot | Leírás | 
| -------------- | ----------- | 
| Kezdeti | Az Azure-SSIS integrációs modul csomópontján nem törölték a lefoglalt/előkészítve. | 
| Indulás alatt | Folyamatban van az Azure-SSIS integrációs modul csomópontján, felosztott/előkészített és számlázási megkezdődött. |
| Elindítva | Az Azure-SSIS integrációs modul csomópontján lett lefoglalva vagy előkészített és számára, hogy az SSIS-csomagok üzembe helyezése és végrehajtási. |
| Leállítás  | Az Azure-SSIS integrációs modul csomópontján jelennek. |
| Leállítva | Az Azure-SSIS integrációs modul csomópontján kiadott és számlázási leállt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Az Azure Portalon az Azure-SSIS integrációs modul monitorozása

A következő képernyőfelvételek bemutatják, hogyan válassza ki az Azure-SSIS integrációs modul figyelheti, és adjon meg egy példát a megjelenített információk.

![Válassza ki az Azure-SSIS integrációs modul monitorozásához](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Az Azure-SSIS integrációs modul adatainak megtekintése](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>A PowerShell-lel az Azure-SSIS integrációs modul monitorozása

Az alábbi példához hasonlóan parancsfájl segítségével ellenőrizze az állapotát az Azure-SSIS integrációs modult.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>További információ az Azure-SSIS integrációs modul

További információ az Azure-SSIS integrációs modul a következő cikkekben talál:

- [Az Azure-SSIS integrációs modul](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást integrációs modulok általában többek között az Azure-SSIS integrációs modult. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál az Azure SQL felügyelt példányának (előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti a virtuális hálózat konfigurálása, hogy az Azure-SSIS integrációs modul csatlakozhat a virtuális hálózat az Azure portal használatával. 

## <a name="next-steps"></a>További lépések
Különböző módokon folyamatok figyeléséhez a következő cikkekben talál: 

- [Gyors útmutató: adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md).
- [Data Factory-folyamatok figyelése az Azure Monitor használatával](monitor-using-azure-monitor.md)
