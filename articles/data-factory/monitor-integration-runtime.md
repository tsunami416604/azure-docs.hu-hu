---
title: Integrációs futásidő figyelése az Azure Data Factoryban
description: Ismerje meg, hogyan figyelheti a különböző típusú integrációs futásidejű azure Data Factory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261137"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Integrációs modul monitorozása az Azure Data Factoryben  
**Az integrációs futásidejű** az Azure Data Factory által a különböző hálózati környezetekben különböző adatintegrációs képességek biztosításához használt számítási infrastruktúra. A Data Factory háromféle integrációs futtatási lehetőséget kínál:

- Azure integrációs modul
- Saját üzemeltetésű integrációs modul
- Azure SSIS integrációs modul

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az integrációs futásidejű (IR) példány állapotának lefelvételéhez futtassa a következő PowerShell-parancsot: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

A parancsmag különböző információkat ad vissza a különböző típusú integrációs futásidejű. Ez a cikk ismerteti az egyes integrációs futásidejű típusok tulajdonságait és állapotát.  

## <a name="azure-integration-runtime"></a>Azure integrációs modul
Az Azure-integrációs futásidejű számítási erőforrás teljes körűen felügyelt az Azure-ban. Az alábbi táblázat a **Get-AzDataFactoryV2IntegrationRuntime** parancs által visszaadott tulajdonságok leírását tartalmazza:

### <a name="properties"></a>Tulajdonságok
Az alábbi táblázat a parancsmag által egy Azure-integrációs futásidejű rekedő tulajdonságok leírását tartalmazza:

| Tulajdonság | Leírás |
-------- | ------------- | 
| Név | Az Azure-integrációs futásidejű neve. |  
| Állapot | Az Azure-integrációs futásidejű állapot. | 
| Hely | Az Azure-integrációs futásidejű ek helye. Az Azure-integrációs futásidejűek helyéről a [Bevezetés az integrációs futásidejű című témakörben](concepts-integration-runtime.md)talál további információt. |
| DataFactoryName | Annak az adatgyárnak a neve, amelyhez az Azure-integrációs futásidejű tartozik. | 
| ResourceGroupName | Annak az erőforráscsoportnak a neve, amelyhez az adat-előállító tartozik.  |
| Leírás | Az integrációs futásidő leírása.  |

### <a name="status"></a>status
Az alábbi táblázat az Azure-integrációs futásidejűek lehetséges állapotát tartalmazza:

| status | Megjegyzések/forgatókönyvek | 
| ------ | ------------------ |
| Online | Az Azure-integrációs futásidő online állapotban van, és használatra kész. | 
| Offline | Az Azure-integrációs futásidő belső hiba miatt offline állapotban van. |

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul
Ez a szakasz a Get-AzDataFactoryV2IntegrationRuntime parancsmag által visszaadott tulajdonságok leírását tartalmazza. 

> [!NOTE] 
> A visszaadott tulajdonságok és állapot információkat tartalmaznak a teljes saját üzemeltetésű integrációs futásidejű és a futásidejű egyes csomópontok.  

### <a name="properties"></a>Tulajdonságok

Az alábbi táblázat az egyes csomópontok figyelési tulajdonságainak **leírását tartalmazza:**

| Tulajdonság | Leírás | 
| -------- | ----------- | 
| Név | Az önkiszolgáló integrációs futásidejű és a hozzá társított csomópontok neve. A Csomópont egy helyszíni Windows-gép, amelyen telepítve van az önkiszolgáló integrációs futásidő. |  
| status | A teljes saját üzemeltetésű integrációs futásidő és az egyes csomópontok állapota. Példa: Online/Offline/Limited/etc. Ezekről az állapotokról a következő szakaszban talál további információt. | 
| Verzió | A saját üzemeltetésű integrációs futásidejű és az egyes csomópontok verziója. A saját üzemeltetésű integrációs futásidejű verziója a csoport ban lévő csomópontok többségének verziója alapján kerül meghatározásra. Ha vannak olyan csomópontok, amelyek különböző verziójú a saját üzemeltetésű integrációs futásidejű telepítő, csak a csomópontok azonos verziószámmal, mint a logikai saját üzemeltetésű integrációs futásidejű függvény megfelelően. Mások a korlátozott módban, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés sikertelen). | 
| Igénybe vehető memória | Saját üzemeltetésű integrációs futásidejű csomóponton rendelkezésre álló memória. Ez az érték egy közel valós idejű pillanatkép. | 
| Processzorhasználat | Saját üzemeltetésű integrációs futásidejű csomópont processzorkihasználtsága. Ez az érték egy közel valós idejű pillanatkép. |
| Hálózatkezelés (be/ki) | Saját üzemeltetésű integrációs futásidejű csomópont hálózati kihasználtsága. Ez az érték egy közel valós idejű pillanatkép. | 
| Egyidejű feladatok (futó/korlát) | **Futás**. Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték egy közel valós idejű pillanatkép. <br/><br/>**Korlát**. A korlát az egyes csomópontok maximális egyidejű feladatait jelenti. Ez az érték a gép mérete alapján van meghatározva. Növelheti a korlátot az egyidejű feladat-végrehajtás ütemezéséhez speciális forgatókönyvekben, amikor a tevékenységek időtúllépés akkor is kivannak, ha a PROCESSZOR, a memória vagy a hálózat kihasználatlan. Ez a funkció egyegycsomópontos, saját üzemeltetésű integrációs futásidejűfunkcióval is elérhető. |
| Szerepkör | A többcsomópontos saját üzemeltetésű integrációs futásidejűek kétféle szerepkört használnak – diszpécser és feldolgozó. Minden csomópont dolgozó, ami azt jelenti, hogy mind használható feladatok végrehajtásához. Csak egy diszpécser csomópont van, amely feladatok/feladatok lekérése a felhőszolgáltatásokból, és különböző munkavégző csomópontokra történő elküldésére szolgál. A diszpécser csomópont egyben munkavégző csomópont is. |

A tulajdonságok egyes beállításainak több értelme van, ha két vagy több csomópont van az önkiszolgáló integrációs futásidőben (azaz egy horizontális felskálázási forgatókönyvben).

#### <a name="concurrent-jobs-limit"></a>Egyidejű feladatok korlátja

Az egyidejű feladatok korlátjának alapértelmezett értéke a számítógép mérete alapján van beállítva. Az érték kiszámításához használt tényezők a RAM mennyiségétől és a gép CPU-magjainak számától függenek. Tehát minél több mag és minél több memória, annál magasabb az egyidejű feladatok alapértelmezett korlátja.

A csomópontok számának növelésével horizontálisfelskálázhatja. A csomópontok számának növelésekor az egyidejű feladatkorlát az összes elérhető csomópont egyidejű feladatkorlátjának összege.  Ha például egy csomópont lehetővé teszi, hogy legfeljebb tizenkét egyidejű feladatot futtasson, akkor további három hasonló csomópont hozzáadása lehetővé teszi, hogy legfeljebb 48 egyidejű feladatot (azaz 4 x 12-t) futtasson. Azt javasoljuk, hogy növelje az egyidejű feladatok korlátját csak akkor, ha alacsony erőforrás-használat ot lát az egyes csomópontok alapértelmezett értékeivel.

Felülírhatja a számított alapértelmezett értéket az Azure Portalon. Válassza a Author > Connections > Integration Runtimes > Edit > csomópontok lehetőséget, > az egyidejű feladat értékének módosítása csomópontonként lehetőséget. Használhatja a PowerShell [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) parancsot is.
  
### <a name="status-per-node"></a>Állapot (csomópontonként)
Az alábbi táblázat egy saját üzemeltetésű integrációs futásidejű csomópont lehetséges állapotát tartalmazza:

| status | Leírás |
| ------ | ------------------ | 
| Online | A csomópont csatlakozik a Data Factory szolgáltatáshoz. |
| Offline | A csomópont offline állapotban van. |
| Frissítés | A csomópont automatikus frissítése folyamatban van. |
| Korlátozott | Csatlakozási probléma miatt. A HTTP-port 8050-es problémája, a szolgáltatásbusz kapcsolódási problémája vagy a hitelesítő adatok szinkronizálása miatt fordulhat elő. |
| Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban van. |

Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz.

### <a name="status-overall-self-hosted-integration-runtime"></a>Állapot (teljes saját üzemeltetésű integrációs futásidő)
Az alábbi táblázat egy saját üzemeltetésű integrációs futásidejű lehetséges állapotát tartalmazza. Ez az állapot a futásidejűhez tartozó összes csomópont állapotától függ. 

| status | Leírás |
| ------ | ----------- | 
| Regisztrációra van szükség | Egyetlen csomópont sincs regisztrálva ehhez az önkiszolgáló integrációs futásidőhöz. |
| Online | Minden csomópont online állapotban van. |
| Offline | Nincs online csomópont. |
| Korlátozott | Ebben a saját üzemeltetésű integrációs futásidejű nem minden csomópont kifogástalan állapotban van. Ez az állapot egy figyelmeztetés, hogy egyes csomópontok lehet, hogy nem. Ennek az állapotnak az lehet az oka, hogy hitelesítő adatok szinkronizálásával kapcsolatos probléma történt a diszpécser/feldolgozó csomóponton. |

A **Get-AzDataFactoryV2IntegrationRuntimeMetric** parancsmag használatával lekéri a JSON-tartalom, amely tartalmazza a részletes önkiszolgáló integrációs futásidejű tulajdonságokat, és a pillanatkép értékeit a parancsmag végrehajtása során.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Mintakimenet (feltételezi, hogy két csomópont van társítva ehhez a saját üzemeltetésű integrációs futásidőhöz):

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
Az Azure-SSIS-integrációs futásidejű az SSIS-csomagok futtatására kijelölt Azure virtuális gépek (vagy csomópontok) teljes körűen felügyelt fürtje. Nem futtat semmilyen más azure data factory tevékenységeket. Miután kivan építve, lekérdezheti a tulajdonságait, és figyelheti a teljes/csomópont-specifikus állapotok.

### <a name="properties"></a>Tulajdonságok

| Tulajdonság/állapot | Leírás |
| --------------- | ----------- |
| CreateTime (Létrehozási idő) | Az UtC-idő, amikor az Azure-SSIS-integrációs futásidejű jött létre. |
| Csomópontok | Az Azure-SSIS-integrációs futásidejű szolgáltatások lerendelt/elérhető csomópontjai csomópontspecifikus állapotokkal (kezdő/elérhető/újrahasznosítható/nem érhető el) és végrehajtható hibákkal. |
| Egyéb hibák | Az Azure-SSIS-integrációs futásidejű nem csomópont-specifikus végrehajtható hibák. |
| Utolsó művelet | Az Azure-SSIS-integrációs futásidejű utolsó indítási/leállítási művelet eredménye, ha nem sikerült. |
| Állapot | Az Azure-SSIS-integrációs futásidejű általános állapota (kezdeti/indítási/indítási/leállítási/leállítási). |
| Hely | Az Azure-SSIS-integrációs futásidejű ek helye. |
| NodeSize (NodeMéret) | Az Azure-SSIS-integrációs futásidejű ek egyes csomópontjainak mérete. |
| NodeCount (NodeCount) | Az Azure-SSIS-integrációs futásidejű csomópontok száma. |
| MaxParallelExecutionsPerNode | Az Azure-SSIS-integrációs futásidejű csomópontonkénti párhuzamos végrehajtások száma. |
| CatalogServerEndpoint | A meglévő Azure SQL Database/Managed Instance kiszolgáló ssisdb üzemeltetéséhez a végpont. |
| CatalogAdminUserName | A meglévő Azure SQL Database/Managed Instance kiszolgáló rendszergazdai felhasználóneve. A Data Factory szolgáltatás ezeket az információkat az Ön nevében történő SSISDB előkészítésére és kezelésére használja. |
| CatalogAdminPassword | A meglévő Azure SQL Database/Felügyelt példány kiszolgáló rendszergazdai jelszava. |
| KatalógusPricingTier | A meglévő Azure SQL Database-kiszolgáló által üzemeltetett SSISDB tarifacsomagja.  Nem alkalmazható az Azure SQL Database felügyelt példány üzemelteti SSISDB. |
| Virtuális hálózat azonosítója | Az Azure-SSIS-integrációs futásidejű virtuális hálózati erőforrás-azonosító. |
| Alhálózat | Az Azure-SSIS-integrációs futásidejű csatlakozás alhálózati neve. |
| ID (Azonosító) | Az Azure-SSIS-integrációs futásidejű erőforrás-azonosítója. |
| Típus | Az Azure-SSIS-integrációs futásidejű típusa (felügyelt/saját üzemeltetett). |
| ResourceGroupName | Az Azure Resource Group neve, amelyben az adatgyár és az Azure-SSIS-integrációs futásidő jött létre. |
| DataFactoryName | Az Azure-adat-előállító neve. |
| Név | Az Azure-SSIS-integrációs futásidejű neve. |
| Leírás | Az Azure-SSIS-integrációs futásidejű leírása. |

  
### <a name="status-per-node"></a>Állapot (csomópontonként)

| status | Leírás |
| ------ | ----------- | 
| Indítás | Ez a csomópont előkészítése folyamatban van. |
| Elérhető | Ez a csomópont készen áll az SSIS-csomagok üzembe helyezésére/végrehajtására. |
| Újrahasznosítás | Ez a csomópont javítása/újraindítása folyamatban van. |
| Nem érhető el | Ez a csomópont nem áll készen az SSIS-csomagok üzembe helyezésére/végrehajtására, és végrehajtható hibákkal/problémákkal rendelkezik, amelyeket megoldhat. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Állapot (teljes Azure-SSIS-integrációs futásidő)

| Általános állapot | Leírás | 
| -------------- | ----------- | 
| Kezdeti | Az Azure-SSIS-integrációs futásidejű csomópontok nincsenek lefoglalva/előkészítve. | 
| Indítás | Az Azure-SSIS-integrációs futásidejű csomópontok levannak foglalva/előkészítve, és a számlázás megkezdődött. |
| Első lépések | Az Azure-SSIS-integrációs futásidejű csomópontok le vannak foglalva/előkészítettek, és készen állnak az SSIS-csomagok üzembe helyezésére/végrehajtására. |
| Leállítás  | Az Azure-SSIS-integrációs futásidejű csomópontok kiadása folyamatban van. |
| Leállítva | Az Azure-SSIS-integrációs futásidejű csomópontok megjelentek, és a számlázás leállt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Az Azure-SSIS-integrációs futásidő figyelése az Azure Portalon

A következő képernyőképek bemutatják, hogyan választhatja ki az Azure-SSIS ir-t a figyelendő, és példát mutatnak a megjelenített információkra.

![Válassza ki az Azure-SSIS-integrációs futásidőt a monitorozáshoz](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Az Azure-SSIS-integrációs futásidejű adatok megtekintése](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Az Azure-SSIS-integrációs futásidő figyelése a PowerShell segítségével

Az Azure-SSIS IR állapotának ellenőrzéséhez használjon parancsfájlt, mint például az alábbi példában.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>További információ az Azure-SSIS integrációs futásidejéről

Az Azure-SSIS-integrációs futásidejűről az alábbi cikkekben olvashat bővebben:

- [Az Azure-SSIS integrációs futásideje](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk általános tájékoztatást nyújt az integrációs futásidőkről általában, beleértve az Azure-SSIS IR-t is. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibontja az oktatóanyagot, és útmutatást nyújt az Azure SQL Database felügyelt példány használatával kapcsolatban, és csatlakozik az infravörös egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Emellett az Azure Portal használatával konfigurálhatja a virtuális hálózatot, hogy az Azure-SSIS ir csatlakozhatanak a virtuális hálózathoz. 

## <a name="next-steps"></a>További lépések
A folyamatok különböző módokon történő figyeléséhez az alábbi cikkekben található: 

- [Rövid útmutató: adatgyár létrehozása.](quickstart-create-data-factory-dot-net.md)
- [Adatfeldolgozó folyamatok figyeléséhez használja az Azure Monitort](monitor-using-azure-monitor.md)
