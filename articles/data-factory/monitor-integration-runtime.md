---
title: Integrációs modul monitorozása Azure Data Factory
description: Megtudhatja, hogyan figyelheti Azure Data Factory Integration Runtime különböző típusait.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 2399849b87e44c5cb70d2db987ae18d8d2d9c552
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261137"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Integrációs modul figyelése Azure Data Factory  
Az **Integration Runtime** a Azure Data Factory által használt számítási infrastruktúra, amely különböző adatintegrációs képességeket biztosít a különböző hálózati környezetekben. A Data Factory három különböző típusú integrációs modult kínál:

- Azure-beli integrációs modul
- Saját üzemeltetésű integrációs modul
- Azure SSIS integrációs modul

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Integration Runtime (IR) egy példánya állapotának lekéréséhez futtassa a következő PowerShell-parancsot: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

A parancsmag különböző adatokat ad vissza az integrációs modul különböző típusaihoz. Ez a cikk az Integration Runtime egyes típusaihoz tartozó tulajdonságokat és állapotokat ismerteti.  

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul
Az Azure Integration Runtime számítási erőforrásait teljes mértékben kezelheti az Azure-ban. Az alábbi táblázat a **Get-AzDataFactoryV2IntegrationRuntime** parancs által visszaadott tulajdonságok leírásait tartalmazza:

### <a name="properties"></a>Tulajdonságok
A következő táblázat az Azure Integration Runtime parancsmag által visszaadott tulajdonságok leírását tartalmazza:

| Tulajdonság | Leírás |
-------- | ------------- | 
| Name (Név) | Az Azure Integration Runtime neve. |  
| Állapot | Az Azure Integration Runtime állapota. | 
| Hely | Az Azure Integration Runtime helye. Az Azure Integration Runtime helyével kapcsolatos részletekért lásd: [az Integration Runtime bemutatása](concepts-integration-runtime.md). |
| DataFactoryName | Annak az adatelőállítónak a neve, amelyhez az Azure Integration Runtime tartozik. | 
| ResourceGroupName | Azon erőforráscsoport neve, amelyhez az adatelőállító tartozik.  |
| Leírás | Az Integration Runtime leírása.  |

### <a name="status"></a>status
Az alábbi táblázat az Azure Integration Runtime lehetséges állapotait tartalmazza:

| status | Megjegyzések/forgatókönyvek | 
| ------ | ------------------ |
| Online | Az Azure Integration Runtime online állapotban van, és készen áll a használatra. | 
| Offline | Az Azure Integration Runtime belső hiba miatt offline állapotban van. |

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul
Ez a szakasz a Get-AzDataFactoryV2IntegrationRuntime parancsmag által visszaadott tulajdonságok leírásait tartalmazza. 

> [!NOTE] 
> A visszaadott tulajdonságok és állapot a teljes saját üzemeltetésű integrációs modulról és a futtatókörnyezet egyes csomópontjairól tartalmaz információkat.  

### <a name="properties"></a>Tulajdonságok

Az alábbi táblázat az **egyes csomópontok**figyelési tulajdonságainak leírását tartalmazza:

| Tulajdonság | Leírás | 
| -------- | ----------- | 
| Name (Név) | A saját üzemeltetésű Integration Runtime és a hozzá társított csomópontok neve. A csomópont egy helyszíni Windows rendszerű számítógép, amelyen telepítve van a saját üzemeltetésű integrációs modul. |  
| status | A saját üzemeltetésű integrációs modul általános és minden csomópontjának állapota. Példa: online/offline/korlátozott/stb. Ezekről az állapotokról további információt a következő szakaszban talál. | 
| Verzió | A saját üzemeltetésű Integration Runtime és az egyes csomópontok verziója. A saját üzemeltetésű integrációs modul verziója a csoportban lévő csomópontok többségének verziója alapján van meghatározva. Ha a saját üzemeltetésű integrációs modul telepítője eltérő verziójú csomópontokkal rendelkezik, akkor csak a logikai saját üzemeltetésű integrációs modulnak megfelelő verziószámmal rendelkező csomópontok működnek megfelelően. Mások korlátozott módban vannak, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés meghiúsul). | 
| Rendelkezésre álló memória | Rendelkezésre álló memória a saját üzemeltetésű integrációs modul csomópontján. Ez az érték a közel valós idejű pillanatkép. | 
| Processzorhasználat | Egy saját üzemeltetésű Integration Runtime-csomópont CPU-kihasználtsága. Ez az érték a közel valós idejű pillanatkép. |
| Hálózatkezelés (be/ki) | Egy saját üzemeltetésű integrációs modul csomópontjának hálózati kihasználtsága. Ez az érték a közel valós idejű pillanatkép. | 
| Egyidejű feladatok (futó/korlát) | **Fut**. Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték a közel valós idejű pillanatkép. <br/><br/>**Korlát**. A korlát az egyes csomópontok maximális egyidejű feladatait jelzi. Ez az érték a gép méretétől függően van meghatározva. Az egyidejű feladatok végrehajtásának korlátját növelheti a speciális forgatókönyvekben, ha a tevékenységek a CPU, a memória vagy a hálózat kihasználtsága alatt állnak. Ez a funkció egy egycsomópontos, saját üzemeltetésű integrációs futtatókörnyezettel is elérhető. |
| Szerepkör | A több csomópontos, saját üzemeltetésű integrációs modulban két típusú szerepkör létezik – diszpécser és feldolgozó. Minden csomópont munkavégző, ami azt jelenti, hogy mind a feladatok végrehajtásához használhatók. Csak egy kiosztó csomópont létezik, amely a feladatok/feladatok a Cloud servicesből való lekérésére és különböző munkavégző csomópontokra történő küldésére szolgál. A diszpécser csomópont is egy feldolgozó csomópont. |

A tulajdonságok egyes beállításai több értelmet mutatnak, ha a saját üzemeltetésű integrációs modulban két vagy több csomópont van (azaz egy kibővíthető forgatókönyvben).

#### <a name="concurrent-jobs-limit"></a>Egyidejű feladatok korlátja

Az egyidejű feladatok korlátjának alapértelmezett értéke a számítógép méretétől függően van beállítva. Az érték kiszámításához használt tényezők a RAM mennyiségétől és a gép CPU-magjainak számától függnek. Tehát minél több magot és annál több memóriát, annál magasabb az egyidejű feladatok alapértelmezett korlátja.

A csomópontok számának növelésével bővítheti a méretezést. A csomópontok számának növelésével az egyidejű feladatok korlátja az összes rendelkezésre álló csomópont egyidejű feladat-határértékének összege.  Ha például egy csomópont legfeljebb tizenkét egyidejű feladatot futtat, akkor három további hasonló csomópont hozzáadásával legfeljebb 48 egyidejű feladat futtatható (azaz 4 x 12). Javasoljuk, hogy csak akkor növelje az egyidejű feladatok korlátját, ha az egyes csomópontok alapértelmezett értékeivel alacsony erőforrás-használatot lát.

A Azure Portalban felülbírálhatja a számított alapértelmezett értéket. Válassza a szerző > Kapcsolatok > Integration Runtimes > Szerkesztés > csomópontok > egyidejű feladatok értékének módosítása csomópontra. Használhatja a PowerShell [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) parancsot is.
  
### <a name="status-per-node"></a>Állapot (/csomópont)
A következő táblázat a saját üzemeltetésű Integration Runtime csomópont lehetséges állapotait tartalmazza:

| status | Leírás |
| ------ | ------------------ | 
| Online | A csomópont a Data Factory szolgáltatáshoz van csatlakoztatva. |
| Offline | A csomópont offline állapotban van. |
| Frissítés | A csomópont automatikus frissítése folyamatban van. |
| Korlátozott | Kapcsolódási probléma miatt. A hiba oka lehet a 8050-es HTTP-port, a Service Bus-kapcsolat problémája vagy a hitelesítő adatok szinkronizálása. |
| Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban található. |

Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz.

### <a name="status-overall-self-hosted-integration-runtime"></a>Állapot (teljes saját üzemeltetésű integrációs modul)
A következő táblázat a saját üzemeltetésű integrációs modul lehetséges állapotait tartalmazza. Ez az állapot a futtatókörnyezethez tartozó összes csomópont állapotától függ. 

| status | Leírás |
| ------ | ----------- | 
| Regisztráció szükséges | Ehhez a saját üzemeltetésű integrációs modulhoz még nincs regisztrálva csomópont. |
| Online | Minden csomópont online állapotú. |
| Offline | Nincs online csomópont. |
| Korlátozott | A saját üzemeltetésű integrációs modulban lévő összes csomópont kifogástalan állapotban van. Ez az állapot azt a figyelmeztetést jeleníti meg, hogy egyes csomópontok leállnak. Ezt az állapotot egy hitelesítő adat szinkronizálási hibája okozhatja a diszpécser/feldolgozó csomóponton. |

A **Get-AzDataFactoryV2IntegrationRuntimeMetric** parancsmag használatával beolvashatja a részletes saját üzemeltetésű integrációs modul TULAJDONSÁGAIT tartalmazó JSON-adattartalmat, valamint a pillanatképek értékeit a parancsmag végrehajtásának időpontjában.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Minta kimenete (feltételezi, hogy két csomópont van társítva ehhez a saját üzemeltetésű integrációs modulhoz):

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
Az Azure-SSIS Integration Runtime egy teljes körűen felügyelt Azure-beli virtuális gép (vagy-csomópont), amely a SSIS-csomagok futtatására van kijelölve. Nem futtatja Azure Data Factory egyéb tevékenységeit. A kiépítés után lekérdezheti a tulajdonságait, és figyelheti a teljes/csomópont-specifikus állapotait.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság/állapot | Leírás |
| --------------- | ----------- |
| CreateTime | Az Azure-SSIS integrációs modul létrehozásának UTC-ideje. |
| Csomópontok | Az Azure-SSIS integrációs modul lefoglalt/elérhető csomópontjai, a csomópont-specifikus állapotokkal (kezdő/elérhető/újrahasznosítási/nem elérhető) és a végrehajtható hibákkal. |
| OtherErrors | Az Azure-SSIS integrációs modulján nem Node-specifikus működés közbeni hibák. |
| LastOperation | Az Azure-SSIS integrációs modul utolsó indítási/leállítási műveletének eredménye, ha a művelet sikertelen. |
| Állapot | Az Azure-SSIS integrációs modul általános állapota (kezdeti/indítás/elindítva/leállítva). |
| Hely | Az Azure-SSIS Integration Runtime helye. |
| NodeSize | Az Azure-SSIS Integration Runtime egyes csomópontjainak mérete. |
| NodeCount | Az Azure-SSIS Integration Runtime csomópontjainak száma. |
| MaxParallelExecutionsPerNode | Az Azure-SSIS integrációs moduljában csomópontok közötti párhuzamos végrehajtások száma. |
| CatalogServerEndpoint | A meglévő Azure SQL Database/felügyelt példány-kiszolgáló végpontja a SSISDB üzemeltetéséhez. |
| CatalogAdminUserName | A meglévő Azure SQL Database/felügyelt példány kiszolgálójának rendszergazdai felhasználóneve. Data Factory szolgáltatás ezeket az információkat használja az SSISDB előkészítéséhez és kezeléséhez az Ön nevében. |
| CatalogAdminPassword | A meglévő Azure SQL Database/felügyelt példány kiszolgálójának rendszergazdai jelszava. |
| CatalogPricingTier | A meglévő Azure SQL Database-kiszolgáló által üzemeltetett SSISDB díjszabási szintje.  Nem alkalmazható Azure SQL Database felügyelt példány-üzemeltetési SSISDB. |
| VNetId | Az Azure-SSIS Integration Runtime virtuális hálózati erőforrás-azonosítója a csatlakozáshoz. |
| Alhálózat | Az Azure-SSIS integrációs modul alhálózatának neve, amelyhez csatlakozni szeretne. |
| ID (Azonosító) | Az Azure-SSIS Integration Runtime erőforrás-azonosítója. |
| Típus | Az Azure-SSIS Integration Runtime típusa (felügyelt/helyi). |
| ResourceGroupName | Az Azure-erőforráscsoport neve, amelyben az adatgyár és az Azure-SSIS Integration Runtime létrejött. |
| DataFactoryName | Az Azure-beli adatgyár neve. |
| Name (Név) | Az Azure-SSIS Integration Runtime neve. |
| Leírás | Az Azure SSIS Integration Runtime leírása. |

  
### <a name="status-per-node"></a>Állapot (/csomópont)

| status | Leírás |
| ------ | ----------- | 
| Indítás | A csomópont előkészítése folyamatban van. |
| Elérhető | Ez a csomópont készen áll a SSIS-csomagok üzembe helyezésére és végrehajtására. |
| Újrahasznosítás | A csomópont javítása/újraindítása folyamatban van. |
| Nem érhető el | Ez a csomópont nem áll készen a SSIS-csomagok üzembe helyezésére és végrehajtására, valamint a feloldható hibákra/problémákra. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Állapot (teljes Azure-SSIS Integration Runtime)

| Általános állapot | Leírás | 
| -------------- | ----------- | 
| Kezdeti | Az Azure-SSIS Integration Runtime csomópontjai nem lettek kiosztva/előkészített állapotban. | 
| Indítás | Megkezdődött az Azure-SSIS integrációs modul csomópontjainak kiosztása/előkészítése és számlázása. |
| Elindítva | Az Azure-SSIS Integration Runtime csomópontjai kiosztottak/felkészültek, és készen állnak a SSIS-csomagok üzembe helyezésére és végrehajtására. |
| Leállítás  | Az Azure-SSIS Integration Runtime csomópontjainak kiadása folyamatban van. |
| Leállítva | Megjelent az Azure SSIS Integration Runtime csomópontjai, és a számlázás leállt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Az Azure-SSIS integrációs modul figyelése a Azure Portal

A következő képernyőképek bemutatják, hogyan lehet kijelölni a figyelni kívánt Azure-SSIS IR, és példát adni a megjelenített információkra.

![Válassza ki a figyelni kívánt Azure-SSIS integrációs modult](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Az Azure SSIS Integration Runtime adatainak megtekintése](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Az Azure SSIS Integration Runtime figyelése a PowerShell-lel

A Azure-SSIS IR állapotának vizsgálatához használja a következő példához hasonló parancsfájlt.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>További információ az Azure-SSIS Integration Runtime-ról

Az Azure-SSIS integrációs modulról további információt az alábbi cikkekben talál:

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az integrációs modulokkal kapcsolatos általános információkat tartalmaz, beleértve a Azure-SSIS IR. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt Azure SQL Database felügyelt példányának használatáról és az IR virtuális hálózathoz való csatlakoztatásáról. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Emellett olyan lépéseket is tartalmaz, amelyekkel a Azure Portal konfigurálhatja a virtuális hálózatot úgy, hogy a Azure-SSIS IR csatlakozni tudjanak a virtuális hálózathoz. 

## <a name="next-steps"></a>Következő lépések
A folyamatok figyelésének különböző módjai a következő cikkekben találhatók: 

- Gyors útmutató [: adatelőállító létrehozása](quickstart-create-data-factory-dot-net.md).
- [Data Factory folyamatok figyelése Azure Monitor használatával](monitor-using-azure-monitor.md)
