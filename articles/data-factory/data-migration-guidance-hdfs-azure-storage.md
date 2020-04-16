---
title: Adatok áttelepítése egy helyszíni Hadoop-fürtről az Azure Storage-ba
description: Ismerje meg, hogyan használhatja az Azure Data Factory segítségével az adatok áttelepítését a helyszíni Hadoop-fürtről az Azure Storage-ba.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417136"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Adatok áttelepítése a helyszíni Hadoop-fürtről az Azure Storage-ba az Azure Data Factory használatával 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az Azure Data Factory egy performant, robusztus és költséghatékony mechanizmust biztosít a helyszíni HDFS-ből az Azure Blob storage-ba vagy az Azure Data Lake Storage Gen2-be nagy méretekben történő áttelepítéshez. 

A Data Factory két alapvető módszert kínál az adatok helyszíni HDFS-ből az Azure-ba való áttelepítéséhez. Kiválaszthatja a forgatókönyv alapján a megközelítést. 

- **Data Factory DistCp mód** (ajánlott): A Data Factory, [használhatja DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (elosztott másolat) fájlok másolására, ahogy van az Azure Blob storage (beleértve [a szakaszos másolás)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)vagy az Azure Data Lake Store Gen2. A DistCp-vel integrált Data Factory használatával kihasználhatja a meglévő nagy teljesítményű fürt előnyeit a legjobb másolási átviteli hang eléréséhez. A Data Factory rugalmas ütemezési és egységes figyelési élményt is élvez. A Data Factory konfigurációjától függően a másolási tevékenység automatikusan létrehoz egy DistCp parancsot, elküldi az adatokat a Hadoop-fürtnek, majd figyeli a másolás állapotát. A Data Factory DistCp mód használatát javasoljuk az adatok helyszíni Hadoop-fürtről az Azure-ba való áttelepítéséhez.
- **Data Factory natív integrációs futásidejű mód:** DistCp nem minden esetben lehetőség. Például egy Azure virtuális hálózatok környezetben a DistCp eszköz nem támogatja az Azure ExpressRoute privát társviszony-létesítés egy Azure Storage virtuális hálózati végpont. Emellett bizonyos esetekben nem szeretné használni a meglévő Hadoop-fürtöt az adatok áttelepítésének motorjaként, így nem helyez el nagy terhelést a fürtön, ami hatással lehet a meglévő ETL-feladatok teljesítményére. Ehelyett használhatja a Data Factory integrációs futásidejű natív képességét, mint a motor, amely adatokat másol a helyszíni HDFS-ből az Azure-ba.

Ez a cikk a következő információkat tartalmazza mindkét megközelítésről:
> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldásarchitektúra 
> * A végrehajtás sal kapcsolatos bevált gyakorlatai  

## <a name="performance"></a>Teljesítmény

Data Factory DistCp módban az átviteli hatás ugyanaz, mintha a DistCp eszközt önállóan használná. A Data Factory DistCp mód maximalizálja a meglévő Hadoop-fürt kapacitását. A DistCp nagy fürtközi vagy fürtön belüli másoláshoz használható. 

A DistCp a MapReduce segítségével végzi a terjesztést, a hibakezelést és a helyreállítást, valamint a jelentéseket. Kibővíti a fájlok és könyvtárak listáját a feladatleképezés bemenetére. Minden feladat a forráslistában megadott fájlpartíciót másol. A DistCp-vel integrált Data Factory segítségével folyamatokat hozhat létre a hálózati sávszélesség, a tárolási IOPS és a sávszélesség teljes kihasználásához, hogy maximalizálja a környezetében az adatátviteli átviteli sebességet.  

A Data Factory natív integrációs futásidejű módja lehetővé teszi a párhuzamosságkülönböző szinteken. A párhuzamosság segítségével teljes mértékben kihasználhatja a hálózati sávszélességet, a tárolási IOPS-t és a sávszélességet az adatátviteli átviteli sebesség maximalizálásához:

- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit. A saját üzemeltetésű integrációs futásidejű manuálisan skálázhatja a gépet, vagy horizontális felskálázástöbb gépre[(legfeljebb négy csomópontra).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) Egyetlen másolási tevékenység particionálja a fájlkészletét az összes csomóponton. 
- Egyetlen másolási tevékenység beolvassa az adattárból, és több szál használatával írja az adattárba. 
- A Data Factory vezérlőfolyamat a párhuzamos másolási tevékenységeket párhuzamosan is elindíthatja. Használhatja például [az Egyes ciklushoz .](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity) 

További információt a [másolási tevékenység teljesítményútmutatójában talál.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Rugalmasság

A Data Factory DistCp módban különböző DistCp parancssori paramétereket `-i`használhat (például figyelmen kívül hagyhatja a hibákat, vagy `-update`adatokat írhat, ha a forrásfájl és a célfájl mérete eltér) a különböző rugalmassági szintekhez.

A Data Factory natív integrációs futásidejű módban, egyetlen másolási tevékenység futtatása, Data Factory egy beépített újrapróbálkozási mechanizmussal rendelkezik. Képes kezelni egy bizonyos szintű átmeneti hibák az adattárakban vagy az alapul szolgáló hálózatban. 

A helyszíni HDFS-ből a Blob storage-ba és a helyszíni HDFS-ből a Data Lake Store Gen2-be történő bináris másolás során a Data Factory automatikusan nagymértékben végrehajtja az ellenőrzőpontokat. Ha egy másolási tevékenység futtatása sikertelen vagy idővel később, egy későbbi újrapróbálkozáskor (győződjön meg arról, hogy az újrapróbálkozások száma 1 >), a másolat az utolsó hibapontról folytatódik, nem pedig az elején kezdődik.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint a Data Factory továbbítja az adatokat a helyszíni HDFS blob storage vagy az Azure Data Lake Storage Gen2 használatával titkosított kapcsolat HTTPS protokollon keresztül. A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a köztes támadásokat. 

Másik lehetőségként, ha nem szeretné, hogy az adatok átvitele a nyilvános interneten keresztül, a nagyobb biztonság érdekében, az adatok átvitele egy privát társviszony-létesítési kapcsolaton keresztül ExpressRoute keresztül. 

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a kép az adatok nyilvános interneten történő áttelepítését ábrázolja:

![Az adatok nyilvános hálózaton keresztül történő áttelepítésének megoldásarchitektúráját bemutató diagram](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Ebben az architektúrában az adatok biztonságosan továbbítódnak https használatával a nyilvános interneten keresztül. 
- Nyilvános hálózati környezetben a Data Factory DistCp mód használatát javasoljuk. A legjobb másolási átviteli-átatással kihasználhatja a meglévő hatékony fürt előnyeit. A Data Factory rugalmas ütemezési és egységes figyelési élményt is élvez.
- Ehhez az architektúrához telepítenie kell a Data Factory saját üzemeltetésű integrációs futásidejét egy vállalati tűzfal mögötti Windows-gépen, hogy eltudja küldeni a DistCp parancsot a Hadoop-fürtnek, és figyelhesse a másolásállapotát. Mivel a gép nem az a motor, amely adatokat mozgat (csak vezérlési célból), a gép kapacitása nem befolyásolja az adatátviteli sebesség.
- A DistCp parancs meglévő paraméterei támogatottak.

Ez a kép az adatok privát hivatkozáson keresztül történő áttelepítését ábrázolja: 

![Az adatok magánhálózaton keresztül történő áttelepítésének megoldásarchitektúráját bemutató diagram](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Ebben az architektúrában az adatok áttelepítése az Azure ExpressRoute-on keresztül egy privát társviszony-létesítési kapcsolaton keresztül történik. Az adatok soha nem haladnak át a nyilvános interneten.
- A DistCp eszköz nem támogatja az ExpressRoute privát társviszony-létesítését egy Azure Storage virtuális hálózati végpontjával. Azt javasoljuk, hogy használja a Data Factory natív képességét az integrációs futásidejű az adatok áttelepítéséhez.
- Ehhez az architektúrához telepítenie kell a Data Factory saját üzemeltetésű integrációs futásidejű egy Windows virtuális gép az Azure virtuális hálózatban. Manuálisan skálázhatja a virtuális gépet, vagy több virtuális gépre skálázhat a hálózat és a tárolási IOPS vagy a sávszélesség teljes kihasználásához.
- Az egyes Azure virtuális gépek (a Data Factory saját üzemeltetésű integrációs futásidejű) ajánlott konfigurációja 32 vCPU-val és 128 GB memóriával Standard_D32s_v3. Figyelheti a virtuális gép processzor- és memóriahasználatát az adatáttelepítés során, így ellenőrizheti, hogy a jobb teljesítmény érdekében fel kell-e skáláznia a virtuális gép, vagy a költségek csökkentése érdekében le kell-e skáláznia a virtuális gép.
- Horizontális felskálázhatja akár négy virtuálisgép-csomópontok egyetlen saját üzemeltetésű integrációs futásidejű társításával. Egy önkiszolgáló integrációs futtatóidő-alapú egyetlen másolási feladat automatikusan particionálja a fájlkészletet, és az összes virtuálisgép-csomópontot használja a fájlok párhuzamos másolásához. A magas rendelkezésre állás érdekében azt javasoljuk, hogy kezdje két virtuálisgép-csomópont, hogy elkerüljék az egypontos hiba forgatókönyv adatáttelepítés során.
- Ha ezt az architektúrát használja, a kezdeti pillanatkép-adatok áttelepítése és a különbözeti adatok áttelepítése elérhetők.

## <a name="implementation-best-practices"></a>A végrehajtás sal kapcsolatos bevált gyakorlatai

Azt javasoljuk, hogy kövesse ezeket az ajánlott eljárásokat az adatáttelepítés megvalósításakor.

### <a name="authentication-and-credential-management"></a>Hitelesítés- és hitelesítő adatok kezelése 

- A HDFS-en való hitelesítéshez [használhatja a Windows (Kerberos) vagy az Anonymous rendszert.](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties) 
- Több hitelesítési típusok támogatottak az Azure Blob storage-hoz való csatlakozáshoz.  Javasoljuk, hogy [az Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)használatát. Az Azure Active Directoryban (Azure AD) automatikusan felügyelt Data Factory-identitásra épülő felügyelt identitások lehetővé teszik a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat adnának meg a csatolt szolgáltatásdefinícióban. Másik lehetőségként hitelesítheti magát a Blob storage-ba egyszerű [szolgáltatás,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [közös hozzáférésű aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy [tárfiókkulcs](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)használatával. 
- Több hitelesítési típus is támogatott a Data Lake Storage Gen2-hez való csatlakozáshoz.  Javasoljuk, hogy az [Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)használatát, de egy [egyszerű szolgáltatás](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) vagy egy [tárfiók kulcs](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)is használható. 
- Ha nem használja az Azure-erőforrások felügyelt identitásait, javasoljuk, hogy [tárolja a hitelesítő adatokat az Azure Key Vaultban,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) hogy megkönnyítse a kulcsok központi kezelését és elforgatását a Data Factory csatolt szolgáltatások módosítása nélkül. Ez a [CI/CD esetében](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)is ajánlott eljárás. 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Data Factory DistCp módban létrehozhat egy másolási tevékenységet a DistCp parancs elküldéséhez, és különböző paramétereket használhat a kezdeti adatáttelepítési viselkedés szabályozásához. 

A Data Factory natív integrációs futásidejű módban azt javasoljuk, adatpartíció, különösen akkor, ha több mint 10 TB-os adatok áttelepítése. Az adatok particionálásához használja a HDFS mappaneveit. Ezután minden egyes Data Factory másolási feladat egyszerre egy mappapartíciót másolhat. A jobb átviteli rendszer érdekében egyidejűleg több Data Factory másolási feladatot is futtathat.

Ha a másolási feladatok bármelyike hálózati vagy adattárbeli átmeneti problémák miatt sikertelen, újrafuttathatja a sikertelen másolási feladatot az adott partíció hdfs-ből való újratöltéséhez. A többi partíciót betöltő más másolási feladatokat ez nem érinti.

### <a name="delta-data-migration"></a>Különbözeti adatok áttelepítése 

Data Factory DistCp módban használhatja a DistCp `-update`parancssori paramétert , adatokat írhat, ha a forrásfájl és a célfájl mérete eltér a különbözeti adatáttelepítéshez.

A Data Factory natív integrációs módban a HDFS-ből származó új vagy módosított fájlok azonosításának legeredményesebb módja egy időparticionált elnevezési konvenció használata. Ha a HDFS-ben lévő adatok időszelet-információkkal vannak elosztva a fájl- vagy mappanévben (például */yyyy/mm/dd/file.csv),* a folyamat könnyen képes azonosítani, hogy mely fájlokat és mappákat kell fokozatosan másolni.

Másik lehetőségként, ha az adatok hdfs nem időparticionált, Data Factory képes azonosítani az új vagy módosított fájlokat a **LastModifiedDate** érték használatával. A Data Factory megvizsgálja a HDFS összes fájlját, és csak azokat az új és frissített fájlokat másolja, amelyek utolsó módosított időbélyege nagyobb, mint egy beállított érték. 

Ha nagy számú fájllal rendelkezik a HDFS fájlban, a kezdeti fájlellenőrzés hosszú időt vehet igénybe, függetlenül attól, hogy hány fájl felel meg a szűrőfeltételnek. Ebben az esetben azt javasoljuk, hogy először particionálja az adatokat a kezdeti pillanatkép-áttelepítéshez használt partíció használatával. Ezután a fájlbeolvasás párhuzamosan is előfordulhat.

### <a name="estimate-price"></a>Becsült ár 

Fontolja meg a következő folyamat ot az adatok HDFS-ből az Azure Blob storage-ba való áttelepítéséhez: 

![Az árképzési folyamatot bemutató diagram](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Tegyük fel, hogy a következő információkat: 

- A teljes adatmennyiség 1 PB.
- Az adatok áttelepítése a Data Factory natív integrációs futásidejű mód használatával.
- 1 PB van osztva 1000 partíciók és minden példány mozog egy partíciót.
- Minden másolási tevékenység egy saját üzemeltetésű integrációs futásidővel van konfigurálva, amely négy géphez van társítva, és amely 500 MBps átviteli sebességet ér el.
- A ForEach egyidejűség **4-re** van állítva, az összesített átviteli sebesség pedig 2 GBps.
- Az áttelepítés összesen 146 órát vesz igénybe.

Itt van a becsült ár alapján a feltételezések: 

![Árképzési számításokat tartalmazó táblázat](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Ez egy hipotetikus árképzési példa. A tényleges díjszabás a környezet tényleges átviteli átmenő eszköztől függ.
> Az Azure Windows virtuális gép (saját üzemeltetésű integrációs futásidejű) ára nem szerepel.

### <a name="additional-references"></a>További referenciák

- [HDFS csatlakozó](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Tevékenységteljesítmény-hangolási útmutató másolása](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Helyi integrációs modul létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Saját üzemeltetésű integrációs futásidejű magas rendelkezésre állás és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása az Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Fájl másolása fokozatosan egy időparticionált fájlnév alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Új és módosított fájlok másolása a LastModifiedDate alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Data Factory árképzési oldal](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>További lépések

- [Fájlok másolása több tárolóból az Azure Data Factory használatával](solution-template-copy-files-multiple-containers.md)