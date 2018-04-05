---
title: Az Azure Data Factory integrációs futásidejű figyelése |} Microsoft Docs
description: Útmutató az Azure Data Factory integrációs futásidejű különböző típusú.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: douglasl
ms.openlocfilehash: 4c857af02e104940559cb86daa3ccd208d8e35a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Az Azure Data Factoryben az integrációs futásidejű figyelése  
**Integrációs futásidejű** a számítási infrastruktúrától által használt Azure Data Factory adatok integrációs szolgáltatásainak különböző hálózati környezetek között. Integráció futtatókörnyezetek adat-előállító által kínált három típusa van:

- Azure-beli integrációs modul
- Saját üzemeltetésű integrációs modul
- Azure SSIS integrációs modul

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

Ahhoz, hogy az integrációs futásidejű (IR) példányának állapotát, futtassa a következő PowerShell-parancsot: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

A parancsmag a különböző típusú integrációs futásidejű különböző információkat ad vissza. Ez a cikk ismerteti a tulajdonságok és az egyes integrációs futásidejű állapotok.  

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul
Egy Azure-integráció futási időben a számítási erőforrással teljesen rugalmasan kezeli az Azure-ban. Az alábbi táblázat azokhoz a tulajdonságokhoz által visszaadott leírást ad a **Get-AzureRmDataFactoryV2IntegrationRuntime** parancs:

### <a name="properties"></a>Tulajdonságok
A következő táblázat az Azure-integráció futási időben a parancsmag által visszaadott tulajdonságait mutatja:

| Tulajdonság | Leírás |
-------- | ------------- | 
| Name (Név) | Az Azure-integráció futásidejű neve. |  
| Állapot | Az Azure-integráció futásidejű állapotát. | 
| Hely | Az Azure-integráció futtatási helyét. Egy Azure-integráció futtatókörnyezetet helyével kapcsolatos részletekért lásd: [integrációs futásidejű bemutatása](concepts-integration-runtime.md). |
| DataFactoryName | Az Azure-integráció futásidejű tartozik adat-előállító nevét. | 
| ResourceGroupName | Az adat-előállító tartozó erőforráscsoport neve.  |
| Leírás | Az integrációs futásidejű leírása.  |

### <a name="status"></a>status
A következő táblázat a lehetséges állapotok az Azure-integráció futtatókörnyezet:

| status | Megjegyzések/forgatókönyvek | 
| ------ | ------------------ |
| Online | Az Azure-integráció futásidejű áll a használatra kész. | 
| Offline | Az Azure-integráció futásidejű rendszere offline állapotú egy belső hiba miatt. |

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul
Ez a szakasz ismerteti a Get-AzureRmDataFactoryV2IntegrationRuntime parancsmag által visszaadott tulajdonságait. 

> [!NOTE] 
> A visszaadott tulajdonságait és állapotadatait információkat tartalmaznak teljes önálló üzemeltetett integrációs futásidejű és a futtatókörnyezet minden csomópontja.  

### <a name="properties"></a>Tulajdonságok

A következő táblázat ismerteti tulajdonságainak figyelési **minden csomópont**:

| Tulajdonság | Leírás | 
| -------- | ----------- | 
| Name (Név) | Az önálló üzemeltetett integrációs futásidejű és a vele társított csomópontok nevét. Csomópont egy a helyi Windows-számítógépen, amely rendelkezik az önálló üzemeltetett integrációs futásidejű telepítve van-e. |  
| status | A teljes önálló üzemeltetett integrációs futásidejű és minden egyes csomópont állapotát. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért tekintse meg a következő szakaszban. | 
| Verzió | Önálló üzemeltetett integrációs futásidejű és minden csomóponton verziója. Az önálló üzemeltetett integrációs futásidejű verzióját a csoportban lévő csomópontok többsége verziója alapján határozza meg. Csomópontok eltérő verziójú a önálló üzemeltetett integrációs futásidejű beállítása esetén csak a csomópontok a azonos verziószámú, mint a logikai önállóan üzemel integrációs futásidejű függvény megfelelően. Mások korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés sikertelen lesz). | 
| Rendelkezésre álló memória | Rendelkezésre álló memória egy önálló üzemeltetett integrációs futásidejű csomóponton. Ez az érték közel valós idejű pillanatképet. | 
| Processzorkihasználtság | Egy önálló üzemeltetett integrációs futásidejű csomópont CPU-felhasználását. Ez az érték közel valós idejű pillanatképet. |
| Hálózatkezelés (In/Out) | Hálózathasználat egy önálló üzemeltetett integrációs futásidejű csomópont. Ez az érték közel valós idejű pillanatképet. | 
| Egyidejűleg futó feladatainak (futtató / Limit) | Feladatok vagy minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. Korlát azt jelzi, hogy az egyes csomópontok maximális egyidejűleg futó feladatainak. Ez az érték van megadva a mérete alapján. Egyidejű feladatok végrehajtásának speciális forgatókönyvekhez, ahol Processzor/memória/hálózati alatt szükség, de tevékenységek vannak időtúllépés miatt növelheti a korlát növelhető. Ez a funkció egy egy csomópontos önálló üzemeltetett integrációs futásidejű is érhető el. |
| Szerepkör | A kézbesítő és a feldolgozói szerepkörök egy több csomópontos önálló üzemeltetett integrációs futásidejű – két típusa van. Az összes csomópontja a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. A kézbesítő csak egy csomópont, feladatok és feladatok a felhőalapú szolgáltatások lekéréses és mennyi azokat a különböző munkavégző csomópontokhoz használt van. A kézbesítő csomópont egyben a munkavégző csomópont. |

Egyes beállítások tulajdonságok célszerű további Ha az önálló üzemeltetett integrációs futásidejű két vagy több csomópont (forgatókönyv kibővítési). 
  
### <a name="status-per-node"></a>Állapot (csomópontonként)
A következő táblázat a lehetséges állapotok egy önálló üzemeltetett integrációs futásidejű csomópont:

| status | Leírás |
| ------ | ------------------ | 
| Online | Csomópont csatlakozik-e a Data Factory szolgáltatásnak. |
| Offline | Csomópontja offline állapotban. |
| Frissítés | A csomópont automatikus frissítése folyamatban van. |
| Korlátozott | Oka egy hálózati probléma. HTTP-port 8050 probléma, a service bus kapcsolati probléma vagy a hitelesítő adatok szinkronizálási problémája miatt lehet. |
| Inaktív | Csomópont van konfigurálva a konfigurációból egyéb többsége csomópontok különböző. |

A csomópont inaktív lehet, ha más csomópontok nem tud kapcsolódni.

### <a name="status-overall-self-hosted-integration-runtime"></a>Állapot (általános önálló üzemeltetett integrációs futásidejű)
A következő táblázat a lehetséges állapotok az egy önálló üzemeltetett integrációs futásidejű. Ez az állapot attól függ, hogy az összes csomópont, a futtatókörnyezet tartozó állapotok. 

| status | Leírás |
| ------ | ----------- | 
| Regisztrációs kell | Nincs csomópont még az önálló üzemeltetett integrációs futásidejű regisztrálva. |
| Online | Az összes csomópont online állapotban. |
| Offline | Nincs csomópont online állapotban. |
| Korlátozott | Az önálló üzemeltetett integrációs futásidejű nem minden csomópontja egy kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti rá, hogy az egyes csomópontok esetleg nem működik. Ez az állapot okozhatja a hitelesítő adatok szinkronizálási problémája kézbesítő/munkavégző csomóponton. |

Használja a **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** beolvasása a részletes tartalmazó JSON-adattartalmat parancsmag önállóan üzemel integrációs futásidejű tulajdonságok, és a pillanatkép értékek végrehajtásának ideje alatt a parancsmag.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Minta kimenet (azt feltételezi, hogy vannak-e az önálló üzemeltetett integrációs futásidejű társított két csomópont):

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
Azure-SSIS integrációs futásidejű rendszere egy teljes körűen felügyelt fürt Azure virtuális gépek (és csomópontok) átjárófelügyeleti feladatokat lát el, futtassa a SSIS-csomagok. Az Azure Data Factory egyéb tevékenységeket nem futtatható. Kiépítését, követően lekérdezni a tulajdonságait, és az általános/konkrét csomóponthoz tartozó állapotának figyelése.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság/állapota | Leírás |
| --------------- | ----------- |
| CreateTime | Az Azure-SSIS-integrációs futásidejű létrehozásának UTC ideje. |
| Csomópontok | Az Azure-SSIS integrációs futásidejű (indítása/rendelkezésre álló/újrahasznosítása vagy nem érhető el) konkrét csomóponthoz tartozó állapotok és végrehajthatóként hibák lefoglalt/elérhető csomópontok. |
| OtherErrors | A nem csomópont-specifikus végrehajthatóként hibák az Azure-SSIS-integráció Runtime. |
| LastOperation | Az Azure-SSIS-integrációs futásidejű hibával hajtható végre, ha sikertelen volt a legutóbbi indítása/leállítása művelet eredménye. |
| Állapot | Az általános állapotának (kezdeti/indítása/indítása/leállítása/leállítása) az Azure-SSIS-integrációs futásidejű. |
| Hely | Az Azure-SSIS-integrációs futásidejű helyét. |
| NodeSize | Az Azure-SSIS-integrációs futásidejű minden egyes csomópontjára mérete. |
| NodeCount | Az Azure-SSIS-integrációs futásidejű csomópontok száma. |
| MaxParallelExecutionsPerNode | Az Azure-SSIS-integrációs futásidejű csomópontonként párhuzamos végrehajtások száma. |
| CatalogServerEndpoint | A végpont a meglévő Azure SQL adatbázis/felügyelt példány (előzetes verzió) kiszolgáló SSISDB gazdagépre. |
| CatalogAdminUserName | A rendszergazda felhasználóneve a meglévő Azure SQL adatbázis/felügyelt példány (előzetes verzió) kiszolgáló. Data Factory szolgáltatásnak kell előkészíteni és az Ön nevében SSISDB kezelni ezt az információt használja. |
| CatalogAdminPassword | A meglévő Azure SQL adatbázis/felügyelt példány (előzetes verzió) kiszolgáló rendszergazdai jelszavát. |
| CatalogPricingTier | A meglévő Azure SQL Database-kiszolgáló által üzemeltetett SSISDB tarifacsomag.  Azure által felügyelt tartalmazó SQL-példány (előzetes verzió) SSISDB nem alkalmazható. |
| VNetId | A virtuális hálózathoz (VNet) erőforrás-azonosító az Azure-SSIS-integráció futási időben való csatlakozáshoz. |
| Alhálózat | Az Azure-SSIS integrációs futásidejű csatlakozni alhálózat neve. |
| ID (Azonosító) | Az Azure-SSIS-integrációs futásidejű erőforrás-azonosító. |
| Típus | A típusa (felügyelt vagy önálló-Hosted) az Azure-SSIS-integrációs futásidejű. |
| ResourceGroupName | Az Azure-erőforráscsoportot, amelyben a data factory és az Azure-SSIS integrációs futásidejű létrejöttek neve. |
| DataFactoryName | Az Azure data factory neve. |
| Name (Név) | Az Azure-SSIS-integrációs futásidejű neve. |
| Leírás | Az Azure-SSIS-integrációs futásidejű leírása. |

  
### <a name="status-per-node"></a>Állapot (csomópontonként)

| status | Leírás |
| ------ | ----------- | 
| Indítás | Ez a csomópont előkészítésére. |
| Elérhető | Ahhoz, hogy SSIS-csomagok központi telepítése és végrehajtási készen áll az ezen a csomóponton. |
| Újrahasznosítása | Ez a csomópont folyamatban javítani vagy újraindítása. |
| Nem érhető el | Ez a csomópont nem ahhoz, hogy SSIS-csomagok központi telepítése és végrehajtási készen álljon, és végrehajthatóként hibák vagy problémák léptek fel, sikerült feloldani. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Állapot (teljes Azure-SSIS integrációs futásidejű)

| Általános állapota | Leírás | 
| -------------- | ----------- | 
| Kezdeti | Az Azure-SSIS-integrációs futásidejű csomópont még nem lettek lefoglalva/előkészítve. | 
| Indítás | Az Azure-SSIS-integrációs futásidejű csomópontjai folyamatban van a lefoglalt/készített, és számlázási megkezdődött. |
| Elindítva | Az Azure-SSIS-integrációs futásidejű csomópontjai törölték a lefoglalt/készített, és ahhoz, hogy SSIS-csomagok központi telepítése és végrehajtási készen állnak. |
| Leállítás  | Az Azure-SSIS-integrációs futásidejű csomópontjai kiadott. |
| Leállítva | Az Azure-SSIS-integrációs futásidejű csomópontjai kiadott és számlázási leállt. |

További Azure-SSIS integrációs futásidejű információt a következő cikkekben talál:

- [Azure-SSIS integrációs futásidejű](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk tájékoztatást általában többek között az Azure-SSIS infravörös integrációs futtatókörnyezetek 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibővíti az oktatóanyag, és utasításokkal szolgál az Azure SQL felügyelt példány (előzetes verzió) használatával, és az infravörös csatlakoztatása egy virtuális hálózatot. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz (VNethez) való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a VNet oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson a virtuális hálózathoz. 

## <a name="next-steps"></a>További lépések
Tekintse meg a különböző módon folyamatok figyeléséhez a következő cikkeket: 

- [Gyors üzembe helyezés: hozzon létre egy adat-előállító](quickstart-create-data-factory-dot-net.md).
- [Azure figyelheti adat-előállító adatcsatornák figyelése](monitor-using-azure-monitor.md)