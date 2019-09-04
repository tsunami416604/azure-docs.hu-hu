---
title: A helyszíni Netezza-kiszolgálóról az Azure-ba történő Migrálás Azure Data Factory használata | Microsoft Docs
description: A helyszíni Netezza-kiszolgálóról az Azure-ba történő Migrálás Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259560"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Az adatok áttelepíthetők a helyszíni Netezza-kiszolgálóról az Azure-ba a Azure Data Factory használatával 

A Azure Data Factory a helyszíni Netezza-kiszolgálóról az Azure Storage-ba vagy az Azure SQL Data Warehouse-be történő áttelepítéséhez szükséges, teljesítményű, robusztus és költséghatékony mechanizmust biztosít. Ez a cikk a következő információkat tartalmazza az adatmérnökök és a fejlesztők számára:

> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldás-architektúra 
> * Gyakorlati tanácsok a megvalósításhoz  

## <a name="performance"></a>Teljesítmény

Azure Data Factory olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot, ami lehetővé teszi a fejlesztők számára, hogy teljes mértékben kihasználják a hálózati sávszélességet és az adatbázis-sávszélességet az adatátviteli teljesítmény maximalizálása érdekében környezet.

![teljesítmény](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Egy másolási tevékenység kihasználhatja a skálázható számítási erőforrások előnyeit: Azure Integration Runtime használata esetén [akár 256 DIUs-t](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) is megadhat az egyes másolási tevékenységekhez kiszolgáló nélküli módon. saját üzemeltetésű Integration Runtime használatakor manuálisan méretezheti a gépet, vagy akár több gépre is kiterjesztheti ([legfeljebb 4 csomóponttal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), és egyetlen másolási tevékenység fogja terjeszteni a partíciót az összes csomóponton. 
- Egy másolási tevékenység több szál használatával olvas be és ír az adattárba. 
- Azure Data Factory a vezérlési folyamat egyszerre több másolási tevékenységet is indíthat, például az [egyes hurkok esetében](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

További részletek a [másolási tevékenység teljesítményének útmutatójában](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) olvashatók

## <a name="resilience"></a>Rugalmasság

Egy másolási tevékenység futtatásakor Azure Data Factory beépített újrapróbálkozási mechanizmussal rendelkezik, így képes az adattárakban vagy a mögöttes hálózaton lévő átmeneti hibák bizonyos szintjének kezelésére.

Azure Data Factory másolási tevékenység két módszert is kínál a nem kompatibilis sorok kezelésére, amikor Adatmásolást végez a forrás-és fogadó adattárolók között. Megszakíthatja és elvégezheti a másolási tevékenységet, ha nem kompatibilis adatok észlelhetők, vagy folytatják a REST-adatok másolását a nem kompatibilis adatsorok kihagyásával. Emellett naplózhatja a nem kompatibilis sorokat az Azure Blob Storage-ban vagy Azure Data Lake Storeban, hogy megismerje a hiba okát, javítsa ki az adatforráson lévő információkat, majd próbálja megismételni a másolási tevékenységet.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint a Azure Data Factory a helyszíni Netezza-kiszolgálóról az Azure Storage-ba, vagy a HTTPS protokollon keresztül titkosított kapcsolaton keresztül Azure SQL Data Warehouse az adatok átvitelét. Adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a belső támadásokat.

Ha nem szeretné, hogy az adatforgalom a nyilvános interneten keresztül történjen, magasabb szintű biztonságot érhet el, ha az Azure Express Route használatával áthelyezi az adatátvitelt privát kapcsolaton keresztül. Tekintse át az alábbi megoldás-architektúrát, hogy ez hogyan érhető el.

## <a name="solution-architecture"></a>Megoldás architektúrája

Az adatáttelepítés nyilvános interneten keresztül:

![megoldás – architektúra – nyilvános hálózat](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- Ebben az architektúrában a rendszer biztonságosan továbbítja az adatátvitelt a HTTPS-en keresztül a nyilvános interneten keresztül.
- Az architektúra eléréséhez telepítenie kell Azure Data Factory saját üzemeltetésű integrációs modult egy Windows rendszerű gépen a vállalati tűzfal mögött. Győződjön meg arról, hogy a Azure Data Factory saját üzemeltetésű integrációs modulja egy Windows rendszerű gépen közvetlenül hozzáférhet a Netezza-kiszolgálóhoz. Manuálisan is felskálázást hajthat végre a gépen, vagy akár több gépre is kibővítheti a hálózat és az adattárolók sávszélességének teljes kihasználását az adatmásoláshoz.
- Ez az architektúra a kezdeti pillanatkép-adatáttelepítés és a Delta-adatáttelepítés során is elérhető.

Az adatáttelepítés privát kapcsolaton keresztül: 

![megoldás-architektúra-private-Network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- Ebben az architektúrában az adatáttelepítés az Azure Express útvonalon keresztüli privát kapcsolaton keresztül történik, így az adat soha nem halad át a nyilvános interneten. 
- Az architektúra eléréséhez telepítenie kell Azure Data Factory saját üzemeltetésű integrációs modult egy Windows rendszerű virtuális GÉPEN az Azure Virtual Network szolgáltatásban. A virtuális gépeket manuálisan, vagy akár több virtuális gépre is felhasználhatja, így teljes mértékben kihasználhatja a hálózat és az adattárolók sávszélességét az adatmásoláshoz.
- Ez az architektúra a kezdeti pillanatkép-adatáttelepítés és a Delta-adatáttelepítés során is elérhető.

## <a name="implementation-best-practices"></a>Gyakorlati tanácsok a megvalósításhoz 

### <a name="authentication-and-credential-management"></a>Hitelesítés és hitelesítőadat-kezelés 

- A Netezza való hitelesítéshez használhatja az ODBC- [hitelesítést a kapcsolódási karakterláncon keresztül](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Több hitelesítési típus is támogatott az Azure Blob Storagehoz való kapcsolódáshoz.  A [felügyelt identitások Azure-erőforrásokhoz való](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) használata erősen ajánlott: az Azure ad-ben az automatikusan felügyelt Azure Data Factoryra épülő konfiguráció alapján a folyamatokat a társított szolgáltatás definíciójában a hitelesítő adatok megadása nélkül állíthatja be.  Azt is megteheti, hogy az Azure-Blob Storage az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [közös hozzáférési aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)segítségével hitelesíti. 
- Több hitelesítési típus is támogatott a Azure Data Lake Storage Gen2hoz való kapcsolódáshoz.  Az [Azure-erőforrásokhoz felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) használata kifejezetten ajánlott, bár az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) is használható. 
- Több hitelesítési típus is támogatott a Azure SQL Data Warehousehoz való kapcsolódáshoz. Az [Azure-erőforrásokhoz felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) használata kifejezetten ajánlott, bár az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) vagy az [SQL-hitelesítés](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) is használható.
- Ha nem használ felügyelt identitásokat az Azure-erőforrásokhoz, [a hitelesítő adatok Azure Key Vault való tárolása](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) erősen ajánlott, hogy könnyebb legyen a kulcsok központi kezelése és elforgatása az Azure Data Factory társított szolgáltatások módosítása nélkül.  Ez a [CI/CD-vel kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)egyike is. 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Kis méretű táblák esetén, ha a kötet mérete kisebb, mint 100 GB, vagy 2 órán belül áttelepíthető az Azure-ba, minden egyes másolási feladatot tábla szerint lehet betölteni. Több Azure Data Factory másolási feladatot is futtathat, így a jobb átviteli sebesség érdekében egyidejűleg különböző táblákat tölthet be. 

Az egyes másolási feladatokon belül a [parallelCopies-beállítás](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) és az adatpartíció lehetőség használatával is elérheti a párhuzamos lekérdezéseket, és partíciók másolásával is elvégezheti az adatok másolását. Az alábbi adatokkal két adatpartíciós lehetőség közül választhat.
- Javasoljuk, hogy az adatszeletből induljon el, mivel ez hatékonyabb.  Győződjön meg arról, hogy a parallelCopies-beállításban a párhuzamosságok száma a Netezza-kiszolgálón lévő táblában lévő adatszelet-partíciók teljes száma alatt van.  
- Ha az egyes adatszelet-partíciók kötetének mérete továbbra is nagy (például 10 GB-nál nagyobb), javasoljuk, hogy váltson a dinamikus tartomány partícióra, ahol nagyobb a rugalmassága, hogy meghatározza a partíciók számát és az egyes partíciók kötetének méretét. partíciós oszlop, felső határ és alsó határ alapján.

A nagyméretű táblák esetében, ha a kötet mérete nagyobb, mint 100 GB, vagy 2 órán belül nem telepíthető át az Azure-ba, javasoljuk, hogy az adatok egyéni lekérdezéssel legyenek particionálva, majd az egyes másolási feladatok egyszerre egy partíciót másoljanak. Több Azure Data Factory másolási feladatot is futtathat párhuzamosan a jobb teljesítmény érdekében. Ügyeljen arra, hogy az egyes másolási feladatokhoz tartozó cél egy partíció egyéni lekérdezéssel való betöltéséhez továbbra is engedélyezheti a párhuzamosságot adatszelet vagy dinamikus tartomány használatával az átviteli sebesség növelése érdekében. 

Ha a másolási feladatok egyike a hálózat vagy az adattár átmeneti hibája miatt meghiúsul, újrafuttathatja a sikertelen másolási feladatot, ha újra szeretné tölteni az adott partíciót a táblából. Az egyéb partíciókat betöltő többi másolási feladatot nem érinti a rendszer.

Az adatok Azure SQL Data Warehouseba való betöltésekor a rendszer azt javasolja, hogy a program a másolási feladaton belül engedélyezze az Azure Blob Storage-ban történő átmeneti tárolást.

### <a name="delta-data-migration"></a>Különbözeti adatáttelepítés 

A táblából származó új vagy frissített sorok azonosításának módja egy időbélyeg-oszlop vagy a sémán belüli növekményes kulcs használata, majd a legutolsó érték magas vízjelekként való tárolása egy külső táblában, amely a következő adatok betöltésére használható a különbözeti adatok szűrésére. 

A különböző táblák különböző vízjel oszlopot használhatnak az új vagy frissített sorok azonosítására. Javasoljuk, hogy hozzon létre egy külső vezérlőelem-táblázatot, ahol az egyes sorok a Netezza-kiszolgálón egy táblát jelölnek az adott vízjel-oszlop nevével és magas küszöbértékkel. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Saját üzemeltetésű Integration Runtime-konfiguráció Azure-beli virtuális gépen vagy gépen

A Netezza-kiszolgálóról az Azure-ba történő Migrálás során, függetlenül attól, hogy a Netezza-kiszolgáló a vállalati tűzfal mögött vagy egy VNET-környezetben van-e telepítve, telepítenie kell a saját üzemeltetésű integrációs modult Windows rendszerű gépre vagy virtuális gépre, amely az áthelyezni kívánt motor. adatok.

- Az ajánlott konfiguráció az egyes gépekhez vagy virtuális gépekhez 32 vCPU és 128 GB memóriával rendelkezik. Az adatáttelepítés során továbbra is megtarthatja a processzor és a memória kihasználtságának figyelését, és megtekintheti, hogy szükség van-e a gép jobb teljesítményének növelésére vagy a számítógép méretezésére a költség megtakarítása érdekében.
- Azt is megteheti, hogy legfeljebb 4 csomópontot társít egyetlen saját üzemeltetésű IR-vel. A saját üzemeltetésű integrációs modulon futó egyetlen másolási művelet automatikusan kihasználja az összes virtuálisgép-csomópontot az adatok párhuzamos másolásához. A magas rendelkezésre állás érdekében ajánlott 2 virtuálisgép-csomóponttal kezdeni, hogy elkerülje az adatáttelepítés során előforduló meghibásodási pontokat.

### <a name="rate-limiting"></a>Díjszabás korlátozása

Ajánlott eljárásként a teljesítmény POC-t egy reprezentatív minta adatkészlettel kell elvégeznie, hogy minden egyes másolási tevékenységhez megfelelő partíciós méretet lehessen meghatározni. Javasoljuk, hogy az egyes partíciókat 2 órán belül be lehessen tölteni az Azure-ba.  

Egy tábla másolásához egyetlen, saját üzemeltetésű IR-géppel rendelkező másolási tevékenységet kezdjen. Fokozatosan növelje a parallelCopies beállítását a tábla adatszelet-partícióinak száma alapján, és ellenőrizze, hogy a teljes tábla betölthető-e az Azure-ba a másolási feladatokban látható átviteli sebességnek megfelelően 2 órán belül. 

Ha nem érhető el, és ugyanakkor a saját üzemeltetésű IR-csomópont és az adattár kapacitása nem teljes mértékben kihasználható, fokozatosan növelje az egyidejű másolási tevékenységek számát, amíg meg nem éri az adattárakhoz tartozó hálózati vagy sávszélesség-korlátot. 

Tartsa figyelemmel a CPU/memória kihasználtságát a saját üzemeltetésű IR-gépen, és készen áll a gép vertikális felskálázására vagy több gépre való méretezésre, amikor megjelenik a CPU/memória teljes kihasználtsága. 

Ha Azure Data Factory másolási tevékenység által jelentett szabályozási hibákat tapasztal, csökkentse a párhuzamosságot vagy a parallelCopies beállítást a Azure Data Factoryban, vagy vegye fontolóra a hálózat és az adattárak sávszélesség-/IOPS-korlátjának növelését. 


### <a name="estimating-price"></a>Az ár becslése 

Vegye figyelembe a következő, a helyszíni Netezza-kiszolgálóról az Azure SQL-adattárházba történő áttelepítéshez kialakított folyamatot:

![díjszabás – folyamat](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Tegyük fel a következőket: 

- Az összes adat mennyisége 50 TB. 
- Az adatáttelepítés az első megoldás-architektúra használatával (a Netezza-kiszolgáló a tűzfal mögött található)
- 50 TB 500 partícióra van osztva, és minden másolási tevékenység egy partíciót helyez át.
- Minden másolási tevékenység egy saját üzemeltetésű, 4 gépen üzemelő IR-vel van konfigurálva, és 20 MBps-os átviteli sebességet érhet el. (Másolási tevékenységen belül a parallelCopies 4 értékre van állítva, az adatoknak a táblából való betöltéséhez pedig minden szál 5 MB/s sebességű átviteli sebességet érhet el)
- A ForEach Egyidejűség értéke 3, az összesített átviteli sebesség pedig 60 MBps.
- Összesen 243 órát vesz igénybe az áttelepítés befejezéséhez.

A fenti feltételezések alapján a becsült ár a következő: 

![díjszabás – tábla](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Ez egy feltételezett árképzési példa. A tényleges díjszabás a környezet tényleges átviteli sebességével függ. A Windows rendszerű gép (a saját üzemeltetésű integrációs modul telepített) díját nem tartalmazza. 

### <a name="additional-references"></a>További referenciák 
- [Adatok migrálása a helyszíni adattárházból az Azure-ba a Azure Data Factory használatával](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Netezza-összekötő](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-összekötő](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Az Azure Blob Storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Másolási tevékenység teljesítményének hangolási útmutatója](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Saját üzemeltetésű Integration Runtime létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Saját üzemeltetésű integrációs modul, HA és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Adatok növekményes másolása egy táblából](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Adatok növekményes másolása több táblából](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Azure Data Factory díjszabási oldala](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>További lépések

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)