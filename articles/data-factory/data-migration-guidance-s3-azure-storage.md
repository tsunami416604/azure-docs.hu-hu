---
title: Adatok migrálása az Amazon S3-ból az Azure Storage-ba
description: Az Azure Data Factory segítségével áttelepítheti az adatokat az Amazon S3-ról az Azure Storage-ba.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122644"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Adatok áttelepítése az Amazon S3-ról az Azure Storage-ba az Azure Data Factory használatával 

Az Azure Data Factory egy teljesítményalapú, robusztus és költséghatékony mechanizmust biztosít az Amazon S3-ról az Azure Blob Storage-ra vagy az Azure Data Lake Storage Gen2-re nagy méretekben történő áttelepítéséhez.  Ez a cikk a következő információkat tartalmazza az adatmérnökök és -fejlesztők számára: 

> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldásarchitektúra 
> * A végrehajtás sal kapcsolatos bevált gyakorlatai  

## <a name="performance"></a>Teljesítmény

Az ADF kiszolgáló nélküli architektúrát kínál, amely lehetővé teszi a párhuzamosságokat különböző szinteken, ami lehetővé teszi a fejlesztők számára, hogy csővezetékeket építsenek a hálózati sávszélesség, valamint a tárolási IOPS és sávszélesség teljes kihasználásához a környezet ben lévő adatátviteli sebesség maximalizálása érdekében. 

Az ügyfelek sikeresen áttelepítettek több száz millió fájlt tartalmazó petabájtnyi adatot az Amazon S3-ról az Azure Blob Storage-ba, amelynek folyamatos átviteli sebessége 2 GBps és újabb. 

![teljesítmény](media/data-migration-guidance-s3-to-azure-storage/performance.png)

A fenti képen látható, hogyan lehet elérni a nagy adatmozgási sebességet a különböző szintű párhuzamosság:
 
- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit: az Azure-integrációs futásidő használatakor kiszolgáló nélküli módon [legfeljebb 256 DNS-t](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) adhat meg minden egyes másolási tevékenységhez; saját üzemeltetésű integrációs futásidő használataesetén manuálisan skálázhatja a gépet, vagy horizontális felskálázhatja több gépre[(legfeljebb 4 csomópontra),](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)és egyetlen másolási tevékenység particionálja a fájlkészletét az összes csomóponton. 
- Egyetlen másolási tevékenység beolvasása az adattárból, és több szál használatával ír az adattárba. 
- Az ADF vezérlőfolyamat több másolási tevékenységet is indíthat párhuzamosan, például a [Minden ciklushoz parancsot](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)használva. 

## <a name="resilience"></a>Rugalmasság

Egyetlen másolási tevékenység futtatása, ADF beépített újrapróbálkozási mechanizmus, így képes kezelni egy bizonyos szintű átmeneti hibák az adattárakban vagy az alapul szolgáló hálózatban. 

Az S3-ról a Blobba és az S3-ról az ADLS Gen2-re történő bináris másolás során az ADF automatikusan ellenőrzőpontokat hajt végre.  Ha egy másolási tevékenység futtatása sikertelen vagy időlett el, egy későbbi újrapróbálkozáskor a másolat az utolsó hibapontról folytatódik, nem pedig az elejétől kezdve. 

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint az ADF az Amazon S3-ról az Azure Blob Storage-ba vagy az Azure Data Lake Storage Gen2-be továbbítja az adatokat a HTTPS protokollon keresztüli titkosított kapcsolat használatával.  A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a köztes támadásokat. 

Ha nem szeretné, hogy az adatok átvitele nyilvános interneten keresztül kerülhessen továbbításra, nagyobb biztonságot érhet el, ha az AWS Direct Connect és az Azure Express Route közötti privát társviszony-létesítési kapcsolaton keresztül továbbítja az adatokat.  Tekintse meg az alábbi megoldásarchitektúrát arról, hogyan érhető el ez. 

## <a name="solution-architecture"></a>Megoldásarchitektúra

Adatok áttelepítése nyilvános interneten keresztül:

![megoldás-architektúra-nyilvános hálózat](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- Ebben az architektúrában az adatok biztonságosátvitele https-en keresztül nyilvános interneten keresztül történik. 
- Mind a forrás Amazon S3, valamint a cél Az Azure Blob Storage vagy az Azure Data Lake Storage Gen2 vannak konfigurálva, hogy a forgalom az összes hálózati IP-címek.  Tekintse meg az alábbi második architektúrát arról, hogyan korlátozhatja a hálózati hozzáférést egy adott IP-tartományra. 
- Könnyedén felskálázhatja a lóerő takarásban, hogy teljes mértékben kihasználja a hálózati és tárolási sávszélességet, így a lehető legjobb teljesítményt érheti el a környezetében. 
- Ezzel az architektúrával mind a kezdeti pillanatkép-áttelepítés, mind a különbözeti adatok áttelepítése elérhető. 

Adatok áttelepítése privát hivatkozáson keresztül: 

![megoldás-architektúra-magán-hálózat](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- Ebben az architektúrában az adatok áttelepítése az AWS Direct Connect és az Azure Express Route közötti privát társviszony-létesítési kapcsolaton keresztül történik, így az adatok soha nem haladnak át nyilvános interneten.  Az AWS VPC és az Azure Virtual network használatát igényli. 
- Az architektúra eléréséhez telepítenie kell az ADF saját üzemeltetésű integrációs futásidejét egy Windows virtuális gépen az Azure virtuális hálózaton belül.  Manuálisan skálázhatja fel a saját üzemeltetésű rendszerépítő virtuális gépeket, vagy horizontális felskálázhatja több virtuális gépre (legfeljebb 4 csomópontra) a hálózat és a tárolási IOPS/sávszélesség teljes kihasználásához. 
- Ha elfogadható az adatátvitel HTTPS-en keresztül, de le szeretné zárni az S3 forráshoz való hálózati hozzáférést egy adott IP-tartományba, az AWS VPC eltávolításával és a privát kapcsolat HTTPS-re való lecserélésével elfogadhatja ennek az architektúrának egy változatát.  Érdemes megtartani az Azure Virtuális és saját üzemeltetésű ir az Azure virtuális gépen, így egy statikus nyilvánosan irányítható IP engedélyezési célra. 
- Ezzel az architektúrával mind a kezdeti pillanatkép-adatok áttelepítése, mind a különbözeti adatok áttelepítése elérhető. 

## <a name="implementation-best-practices"></a>A végrehajtás sal kapcsolatos bevált gyakorlatai 

### <a name="authentication-and-credential-management"></a>Hitelesítés- és hitelesítő adatok kezelése 

- Az Amazon S3-fiókhitelesítéshez [az IAM-fiók hozzáférési kulcsát kell használnia.](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties) 
- Az Azure Blob Storage-hoz való csatlakozáshoz több hitelesítési típus is támogatott.  Felügyelt [identitások használata az Azure-erőforrások](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) erősen ajánlott: épül egy automatikusan felügyelt ADF-azonosítás az Azure AD, lehetővé teszi, hogy konfigurálja a folyamatok hitelesítő adatok megadása nélkül a csatolt szolgáltatás definíciójában.  Másik lehetőségként hitelesítheti magát az Azure Blob Storage [szolgáltatásegyszerű,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [a megosztott hozzáférés-aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy [a tárfiók kulcshasználatával.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Több hitelesítési típus is támogatott az Azure Data Lake Storage Gen2-hez való csatlakozáshoz.  Felügyelt [identitások használata az Azure-erőforrások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) használata erősen ajánlott, bár [egyszerű szolgáltatás vagy](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) [tárfiók kulcs](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) is használható. 
- Ha nem használja a felügyelt identitások az Azure-erőforrások, [a hitelesítő adatok tárolása az Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) erősen ajánlott, hogy megkönnyítse a kulcsok központi kezelése és elforgatása nélkül ADF-csatolt szolgáltatások módosítása nélkül.  Ez a [CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)egyik legjobb gyakorlata is. 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Az adatpartíció különösen 100 TB-nál több adat áttelepítésekor ajánlott.  Az adatok particionálásához használja ki az "előtag" beállítást az Amazon S3 mappáinak és fájljainak név szerinti szűréséhez, majd minden ADF másolási feladat egyszerre egy partíciót másolhat.  A jobb átviteli rendszer érdekében egyidejűleg több ADF-másolási feladatot is futtathat. 

Ha a másolási feladatok bármelyike hálózati vagy adattár-átmeneti probléma miatt sikertelen, újrafuttathatja a sikertelen másolási feladatot, hogy újra betöltse az adott partíciót az AWS S3-ból.  Az összes többi más partíciót betöltő másolási feladatoknem érinti. 

### <a name="delta-data-migration"></a>Különbözeti adatok áttelepítése 

Az Új vagy módosított fájlok Azonosításának legeredményesebb módja az AWS S3-ból az időparticionált elnevezési konvenció használata – amikor az AWS S3-ban lévő adatokat időszelet-információkkal particionálták a fájl vagy mappa nevében (például /yyyy/mm/dd/file.csv), akkor a folyamat könnyen azonosíthatja, hogy mely fájlokat/mappákat kell fokozatosan másolni. 

Másik lehetőségként, Ha az adatok AWS S3 nem idő particionált, ADF képes azonosítani az új vagy módosított fájlokat a LastModifiedDate.   Úgy működik, hogy az ADF átkutatja az összes fájlt az AWS S3-ból, és csak az új és frissített fájlt másolja, amelynek utolsó módosított időbélyege nagyobb, mint egy bizonyos érték.  Ne feledje, hogy ha nagy számú fájl van az S3-ban, a kezdeti fájlellenőrzés hosszú időt vehet igénybe, függetlenül attól, hogy hány fájl felel meg a szűrőfeltételnek.  Ebben az esetben javasoljuk, hogy először particionálja az adatokat, ugyanazt az "előtagot" a kezdeti pillanatkép-áttelepítéshez, hogy a fájlvizsgálat párhuzamosan történjen.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Az Azure VM-en saját üzemeltetésű integrációs futásidejű forgatókönyvek esetén 

Akár privát kapcsolaton keresztül telepíti át az adatokat, akár az Amazon S3 tűzfalon szeretne engedélyezni egy adott IP-tartományt, telepítenie kell az Azure Windows VM-re üzemeltetett integrációs futásidőt. 

- Az egyes Azure virtuális gépekhez javasolt konfigurációt 32 vCPU és 128 GB memóriával Standard_D32s_v3.  Az adatáttelepítés során folyamatosan figyelheti a PROCESSZOR- és memóriakihasználtságot, és ellenőrizheti, hogy a jobb teljesítmény érdekében tovább kell-e bővítenie a virtuális gép méretét, vagy a költségek csökkentése érdekében csökkentenie kell a virtuális gép méretét. 
- Horizontális felskálázhatja akár 4 virtuálisgép-csomópontok egyetlen saját üzemeltetésű infravörös társításával.  Egy önkiszolgáló infravörös rendszeren futó egyetlen másolási feladat automatikusan particionálja a fájlkészletet, és az összes virtuálisgép-csomópontot használja a fájlok párhuzamos másolásához.  A magas rendelkezésre állás érdekében ajánlott 2 virtuális gép csomópontokkal kezdeni, hogy elkerülje az egyetlen meghibásodási pontot az adatáttelepítés során. 

### <a name="rate-limiting"></a>Sebességkorlátozás 

Ajánlott eljárásként hajtson végre egy teljesítmény POC egy reprezentatív minta adatkészlettel, így meghatározhatja a megfelelő partícióméretet. 

Kezdje egyetlen partícióval és egyetlen másolási tevékenységgel az alapértelmezett DIU-beállítással.  Fokozatosan növelje a DIU-beállítást, amíg el nem éri az adattárak sávszélesség-korlátját vagy az IOPS/sávszélesség-korlátot, vagy el nem éri az egyetlen másolási tevékenységen engedélyezett maximális 256 DIU értéket. 

Ezután fokozatosan növelje az egyidejű másolási tevékenységek számát, amíg el nem éri a környezet korlátait. 

Ha az ADF másolási tevékenység által jelentett szabályozási hibákat észlel, csökkentse az ADF egyidejűségi vagy DIU-beállítását, vagy fontolja meg a hálózati és adattárak sávszélesség-/IOPS-korlátjának növelését.  

### <a name="estimating-price"></a>Az ár becslése 

> [!NOTE]
> Ez egy hipotetikus árképzési példa.  A tényleges díjszabás a környezet tényleges átviteli átmenő eszköztől függ.

Vegye figyelembe a következő, az S3-ból az Azure Blob Storage-ba történő áttelepítéshez kialakított folyamatot: 

![árképzési folyamat](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Tegyük fel, hogy a következőket tegyük fel: 

- A teljes adatmennyiség 2 PB 
- Adatok áttelepítése HTTPS-en keresztül az első megoldásarchitektúrával 
- 2 PB van osztva 1 K partíciók és minden példány mozog egy partíciót 
- Minden példány DIU=256 értékkel van konfigurálva, és 1 GBps átviteli sebességet ér el 
- ForEach egyidejűség van beállítva 2 és összesített átviteli sebesség 2 GBps 
- Összesen 292 órát vesz igénybe az áttelepítés 

Itt van a becsült ár alapján a fenti feltételezések: 

![árképzési tábla](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>További referenciák 
- [Amazon Simple Storage Service csatlakozó](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Tevékenységteljesítmény-hangolási útmutató másolása](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Saját üzemeltetésű integrációs futásidő létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integrációs futásidejű HA és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása az Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Fájl másolása növekményesen az idő particionált fájlneve alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Új és módosított fájlok másolása a LastModifiedDate alapján](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF árképzési lap](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Sablon

Itt van a [sablon](solution-template-migration-s3-azure.md) kezdeni, hogy migrálja petabájtnyi adatok, amelyek több száz millió fájlt amazon S3 az Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>További lépések

- [Fájlok másolása több tárolóból az Azure Data Factory segítségével](solution-template-copy-files-multiple-containers.md)
