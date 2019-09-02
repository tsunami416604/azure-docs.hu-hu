---
title: Az adatok áttelepíthetők a helyszíni Hadoop-fürtről az Azure Storage-ba a Azure Data Factory használatával | Microsoft Docs
description: A helyszíni Hadoop-fürtről az Azure Storage-ba történő Migrálás Azure Data Factory használatával.
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
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211607"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Az adatok áttelepíthetők a helyszíni Hadoop-fürtről az Azure Storage-ba Azure Data Factory használatával 

A Azure Data Factory a helyszíni HDFS az Azure-ba történő áttelepítéséhez, valamint az Azure-Blob Storage vagy a Azure Data Lake Storage Gen2hoz biztosít egy olyan, hatékony és költséghatékony mechanizmust, amellyel a helyszíni adatok áttelepíthetők. A Azure Data Factory alapvetően két módszert tartalmaz az adatok helyszíni HDFS az Azure-ba történő átállítására. Mindegyiket kiválaszthatja a forgatókönyv alapján. 

- ADF DistCp mód. ADF-támogatás a [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) használatával a fájlok másolása az Azure-blobba (beleértve a [szakaszos másolást](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)is) vagy Azure Data Lake Store, ebben az esetben teljes mértékben kihasználhatja a fürt erejét az ADF saját üzemeltetésű integrációs modulján (IR) való futtatás helyett. Jobb másolási sebességet biztosít, különösen akkor, ha a fürt nagyon hatékony. A Azure Data Factory konfigurációjának megfelelően a másolási tevékenység automatikusan létrehoz egy DistCp-parancsot, elküldi a Hadoop-fürtöt, és figyeli a másolási állapotot. A DistCp-mel integrált ADF használatával az ügyfél nem csak a meglévő hatékony fürtöt használja fel a legjobb másolási teljesítmény eléréséhez, hanem a rugalmas ütemezést és az ADF-ből származó egységes figyelési élményt is élvezheti. Alapértelmezés szerint az ADF DistCp mód a helyszíni Hadoop-fürtről az Azure-ba történő adatáttelepítés ajánlott módja.
- Natív IR módú ADF. Bizonyos helyzetekben a DistCp nem tud működni az adott esetekben (például VNET-környezetben, a DistCp eszköz nem támogatja az Express Route Private-peering + Azure Storage VNet-végpontot). Emellett, ha nem szeretné, hogy a meglévő Hadoop-fürtöt a motorként használja az adatok áttelepíteni, mert nagy terhelést eredményez a fürtön, ami hatással lehet a meglévő ETL-feladatok teljesítményére. Ha ez az eset áll fenn, használhatja az ADF Native képességet, ahol az ADF Integration Runtime (IR) lehetővé teszi az adatok másolását a helyszíni HDFS az Azure-ba.

Ez a cikk az alábbi információkat tartalmazza az adatmérnökök és a fejlesztők számára:
> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldás-architektúra 
> * Gyakorlati tanácsok a megvalósításhoz  

## <a name="performance"></a>Teljesítmény

Az ADF-DistCp módban az átviteli sebesség ugyanaz, mint a DistCp eszköz egymástól független használata, ami kihasználja a meglévő Hadoop-fürt kapacitását. A DistCp (Distributed Copy) egy olyan eszköz, amely a fürtök közötti vagy fürtön belüli másoláshoz használatos. A szolgáltatás a MapReduce használja az eloszlás, a hibakezelés és a helyreállítás, valamint a jelentéskészítés hatására. Kibővíti a fájlok és a könyvtárak listáját a feladatok leképezéséhez, amelyek mindegyike a forrás listában megadott fájlok partícióját másolja. A DistCp-mel integrált ADF használatával olyan folyamatokat hozhat létre, amelyekkel teljes mértékben kihasználhatja a hálózati sávszélességet, valamint a tárolási IOPS és sávszélességet az adatátviteli teljesítmény maximalizálása érdekében a környezet számára.  

Az ADF natív IR módban a különböző szinteken is lehetővé teszi a párhuzamosságot, ami teljes mértékben kihasználhatja a hálózati sávszélességet, valamint a tárolási IOPS és a sávszélességet az adatátviteli teljesítmény maximalizálása érdekében a saját üzemeltetésű IR-gép manuális méretezésével vagy horizontális felskálázással saját üzemeltetésű, több gépen futó IR.

- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit. A saját üzemeltetésű integrációs modul segítségével manuálisan is felskálázást hajthat végre a gépen, vagy akár több gépre is kibővíthető ([legfeljebb 4 csomópont](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)), és egyetlen másolási tevékenység fogja particionálni az összes csomóponton beállított fájlját. 
- Egy másolási tevékenység több szál használatával olvas be és ír az adattárba. 
- Az ADF-vezérlési folyamat egyszerre több másolási tevékenységet is elindíthat, például az [egyes hurkok esetében](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

További részletek a [másolási tevékenység teljesítményének útmutatójában](https://docs.microsoft.com/azure/data-factory/copy-activity-performance) olvashatók

## <a name="resilience"></a>Rugalmasság

Az ADF-DistCp módban különböző DistCp parancssori paramétereket alkalmazhat (például:-i, a hibák figyelmen kívül hagyása;-frissítés, adatok írása, ha a forrásfájl és a célfájl mérete eltérő) a rugalmasság különböző szintjeinek eléréséhez.

A natív IR-módban, egy másolási tevékenység futtatásakor az ADF beépített újrapróbálkozási mechanizmussal rendelkezik, így képes az adattárakban vagy a mögöttes hálózaton lévő átmeneti hibák bizonyos szintjének kezelésére. Ha a helyszíni HDFS a Blobba és a helyszíni ADLS Gen2 HDFS-ből történő bináris másolást végez, az ADF automatikusan nagy mértékben végrehajtja az ellenőrzőpontokat. Ha egy másolási tevékenység futása sikertelen vagy időtúllépés miatt megszakadt, egy későbbi újrapróbálkozáskor (ne feledje, hogy az újrapróbálkozások száma > 1), a másolás az elejétől kezdődően folytatódik az utolsó meghibásodási ponttól.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint az ADF a helyszíni HDFS az Azure Blob Storage-be vagy Azure Data Lake Storage Gen2 a HTTPS protokollon keresztüli titkosított kapcsolaton keresztül továbbítja az adatok átvitelét.  A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a belső támadásokat. 

Ha nem szeretné, hogy az adatforgalom a nyilvános interneten keresztül történjen, magasabb szintű biztonságot érhet el, ha az Azure Express Route használatával áthelyezi az adatátvitelt privát kapcsolaton keresztül. Tekintse át az alábbi megoldás-architektúrát, hogy ez hogyan érhető el.

## <a name="solution-architecture"></a>Megoldás architektúrája

Az adatáttelepítés nyilvános interneten keresztül:

![megoldás – architektúra – nyilvános hálózat](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Ebben az architektúrában a rendszer biztonságosan továbbítja az adatátvitelt a HTTPS-en keresztül a nyilvános interneten keresztül. 
- A nyilvános hálózati környezetben javasolt az ADF DistCp mód használata. Így nemcsak a meglévő hatékony fürtöt használhatja a legjobb másolási teljesítmény eléréséhez, hanem a rugalmas ütemezést és az ADF-ből származó egységes figyelési élményt is kihasználhatja.
- Telepítenie kell az ADF saját üzemeltetésű integrációs modult egy Windows rendszerű gépen a vállalati tűzfal mögött ahhoz, hogy elküldje a DistCp-parancsot a Hadoop-fürtre, és figyelje a másolási állapotot. Mivel ez a gép nem lesz az adatok áthelyezésének motorja (csak ellenőrzési célra), a gép kapacitása nem befolyásolja az adatátviteli sebességet.
- A DistCp parancs meglévő paraméterei támogatottak.


Az adatáttelepítés privát kapcsolaton keresztül: 

![megoldás-architektúra-private-Network](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Ebben az architektúrában az adatáttelepítés az Azure Express útvonalon keresztüli privát kapcsolaton keresztül történik, így az adat soha nem halad át a nyilvános interneten.
- A DistCp eszköz nem támogatja az Express Route Private peering és az Azure Storage VNet végpontját, ezért javasoljuk, hogy az integrációs modulon keresztül, az adatáttelepítéshez használja az ADF natív funkciót.
- Az architektúra eléréséhez telepítenie kell az ADF saját üzemeltetésű integrációs modulját egy Windows rendszerű virtuális GÉPRE az Azure Virtual Networkben. Manuálisan is méretezheti a virtuális gépet, vagy akár több virtuális gépre is kibővítheti a hálózat és a tárhely IOPS/sávszélességének teljes kihasználását.
- Az ajánlott konfiguráció, amely az egyes Azure-beli virtuális gépekhez (az ADF saját üzemeltetésű integrációs modul telepítése esetén) való kezdéshez Standard_D32s_v3, 32 vCPU és 128 GB memóriával rendelkezik. Az adatáttelepítés során továbbra is figyelheti a virtuális gép processzor-és memória-kihasználtságát, és megtudhatja, hogy a virtuális gép nagyobb teljesítményre vagy vertikális leskálázásra van-e szükség a költség megtakarítása érdekében.
- Akár 4 virtuálisgép-csomópont társításával is felskálázást hajthat végre, egyetlen saját üzemeltetésű IR-vel. A saját üzemeltetésű integrációs modulon futó egyetlen másolási feladatot a rendszer automatikusan particionálja, és minden virtuálisgép-csomóponton kihasználja a fájlok párhuzamos másolását. A magas rendelkezésre állás érdekében ajánlott két virtuálisgép-csomóponttal kezdeni, hogy elkerülje az adatáttelepítés során előforduló meghibásodási pontokat.
- Ez az architektúra a kezdeti pillanatkép-adatáttelepítés és a Delta-adatáttelepítés során is elérhető.


## <a name="implementation-best-practices"></a>Gyakorlati tanácsok a megvalósításhoz 

### <a name="authentication-and-credential-management"></a>Hitelesítés és hitelesítőadat-kezelés 

- A HDFS való hitelesítéshez használhatja a [Windows (Kerberos) vagy a névtelen](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)hitelesítést. 
- Több hitelesítési típus is támogatott az Azure Blob Storagehoz való kapcsolódáshoz.  A [felügyelt identitások Azure-erőforrásokhoz való](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) használata erősen ajánlott: az Azure ad-ben automatikusan felügyelt ADF azonosítására épülő megoldás lehetővé teszi a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat kellene megadnia a társított szolgáltatás definíciójában.  Azt is megteheti, hogy az Azure-Blob Storage az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), a [közös hozzáférési aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)segítségével hitelesíti. 
- Több hitelesítési típus is támogatott a Azure Data Lake Storage Gen2hoz való kapcsolódáshoz.  Az [Azure-erőforrásokhoz felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) használata kifejezetten ajánlott, bár az [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) vagy a [Storage-fiók kulcsa](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) is használható. 
- Ha nem használ felügyelt identitásokat az Azure-erőforrásokhoz, [a hitelesítő adatok tárolása Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) erősen ajánlott, hogy könnyebb legyen a kulcsok központi kezelése és elforgatása az ADF-hez társított szolgáltatások módosítása nélkül.  Ez a [CI/CD-vel kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)egyike is. 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Az ADF DistCp módban létrehozhat egy másolási tevékenységet, hogy a DistCp-parancsot különböző paraméterekkel küldje el a kezdeti adatáttelepítési viselkedés szabályozása érdekében. 

A natív IR módban az adatpartíció használata ajánlott, különösen a 10 TB-nál több adat áttelepítése esetén. Az adatok particionálásához használja ki a mappák nevét a HDFS, majd az egyes ADF-másolási feladatok egyszerre csak egy mappát másolhatnak. Több ADF-másolási feladatot is futtathat párhuzamosan a jobb teljesítmény érdekében.
Ha a másolási feladatok egyike a hálózat vagy az adattár átmeneti hibája miatt meghiúsul, újrafuttathatja a sikertelen másolási feladatot, hogy újra betöltse az adott partíciót a HDFS-ből. Az egyéb partíciókat betöltő többi másolási feladatot nem érinti a rendszer.

### <a name="delta-data-migration"></a>Különbözeti adatáttelepítés 

Az ADF DistCp módban a különbözeti adatok áttelepítésének érdekében a "-Update, az adatok írása, ha a forrásfájl és a célfájl mérete eltér a" DistCp.

A natív IR-módban az új vagy módosított fájlok HDFS való azonosításának leghatékonyabb módja az idő-particionált elnevezési konvenció használata – Ha a HDFS-ban lévő adatok időszelet-információkkal vannak particionálva a fájlban vagy a mappában (például/yyyy/MM/DD/ file. csv), a folyamat könnyedén azonosíthatja, hogy mely fájlok/mappák legyenek növekményes másolásra.
Ha a HDFS-ben tárolt adatai nem particionálják az időpontot, az ADF az új vagy módosított fájlokat a LastModifiedDate alapján képes azonosítani. A működésének módja az, hogy az ADF megvizsgálja az összes fájlt a HDFS-ből, és csak azt az új és frissített fájlt másolja, amelynek utolsó módosításának időbélyeg-értéke nagyobb, mint egy bizonyos érték. Ügyeljen arra, hogy ha nagy számú fájl található a HDFS-ben, a kezdeti fájl vizsgálata hosszú időt is igénybe vehet, függetlenül attól, hogy hány fájl felel meg a szűrési feltételnek. Ebben az esetben azt javasoljuk, hogy először particionálja az adatpartíciókat ugyanazzal a partícióval a pillanatkép-áttelepítés kezdeti áttelepítéséhez, hogy a fájlok vizsgálata párhuzamosan történjen.

### <a name="estimating-price"></a>Az ár becslése 

Vegye figyelembe a következő, a HDFS-ből az Blob Storage Azure-ba történő áttelepítésre szolgáló folyamatot: 

![díjszabás – folyamat](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Tegyük fel a következőket: 

- Az adatmennyiség összesen 1 PB
- Az adatáttelepítés a második megoldási architektúrával (natív IR mód)
- 1 a PB 1000 partícióra van osztva, és mindegyik másolat egy partíciót helyez át
- Az egyes másolási tevékenységek egy saját üzemeltetésű, 4 géphez társított IR-vel vannak konfigurálva, és 500 MBps átviteli sebességet érnek el.
- A ForEach Egyidejűség értéke 4, az összesített átviteli sebesség pedig 2 GB/s
- Összesen 146 órát vesz igénybe az áttelepítés befejezéséhez.


A fenti feltételezések alapján a becsült ár a következő: 

![díjszabás – tábla](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Ez egy feltételezett árképzési példa.  A tényleges díjszabás a környezet tényleges átviteli sebességével függ.
> Az Azure Windows rendszerű virtuális gép (a saját üzemeltetésű integrációs modul telepített) díját nem tartalmazza.

### <a name="additional-references"></a>További referenciák 
- [HDFS-összekötő](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Az Azure Blob Storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Másolási tevékenység teljesítményének hangolási útmutatója](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Saját üzemeltetésű Integration Runtime létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Saját üzemeltetésű integrációs modul, HA és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [A fájl növekményes másolása az idő particionált fájljának neve alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Új és módosított fájlok másolása a LastModifiedDate alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Az ADF díjszabási lapja](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>További lépések

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)