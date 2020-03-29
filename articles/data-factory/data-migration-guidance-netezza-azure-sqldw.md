---
title: Adatok áttelepítése a helyszíni Netezza kiszolgálóról az Azure-ba
description: Az Azure Data Factory segítségével adatokat telepítheti át egy helyszíni Netezza kiszolgálóról az Azure-ba.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 80c9929f37b4890387a7625f04db6ce3e37f0cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74922115"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Adatok áttelepítése a helyszíni Netezza kiszolgálóról az Azure-ba az Azure-ba az Azure-ban 

Az Azure Data Factory egy teljesítmény, robusztus és költséghatékony mechanizmust biztosít az adatok nagy méretekben történő áttelepítéséhez egy helyszíni Netezza kiszolgálóról az Azure storage-fiókjába vagy az Azure SQL Data Warehouse adatbázisába. 

Ez a cikk a következő információkat tartalmazza az adatmérnökök és -fejlesztők számára:

> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldásarchitektúra 
> * A végrehajtás sal kapcsolatos bevált gyakorlatai  

## <a name="performance"></a>Teljesítmény

Az Azure Data Factory egy kiszolgáló nélküli architektúrát kínál, amely lehetővé teszi a párhuzamosságkülönböző szinteken. Ha Ön fejlesztő, ez azt jelenti, hogy folyamatokat hozhat létre a hálózati és az adatbázis sávszélességének teljes kihasználásához, hogy maximalizálja a környezet adatmozgatási átviteli sebességet.

![Teljesítménydiagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Az előző ábra a következőképpen értelmezhető:

- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit. Az Azure-integrációs runtime használatakor kiszolgáló nélküli módon [legfeljebb 256 DIUs-t](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) adhat meg minden egyes másolási tevékenységhez. A saját üzemeltetésű integrációs futásidejű (saját üzemeltetésű infravörös) manuálisan skálázhatja fel a gépet, vagy horizontális felskálázás több gépre[(legfeljebb négy csomópontra),](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)és egyetlen másolási tevékenység osztja el a partíciót az összes csomópont között. 

- Egyetlen másolási tevékenység beolvassa az adattárból, és több szál használatával írja az adattárba. 

- Az Azure Data Factory vezérlőfolyamat a párhuzamos tevékenységek et párhuzamosan több másolási tevékenységet is elindíthat. Például, akkor indítsa el őket egy [for each hurok](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

További információt a [Tevékenység teljesítményének és méretezhetőségének másolásához című témakörben talál.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Rugalmasság

Egyetlen példányon belül futó Azure Data Factory egy beépített újrapróbálkozási mechanizmus, amely lehetővé teszi, hogy az adattárakban vagy az alapul szolgáló hálózatban egy bizonyos szintű átmeneti hibák kezelésére.

Az Azure Data Factory másolási tevékenység, amikor adatokmásolása forrás és a fogadó adattárak, két módon kezelheti inkompatibilis sorokat. A nem kompatibilis adatsorok kihagyásával megszakíthatja és meghiúsíthatja a másolási tevékenységet, vagy folytathatja a többi adat másolását. Emellett a hiba okának megismeréséhez naplózhatja az inkompatibilis sorokat az Azure Blob storage-ban vagy az Azure Data Lake Store-ban, rögzítheti az adatokat az adatforráson, és újra próbálkozhat a másolási tevékenységgel.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint az Azure Data Factory adatokat továbbít a helyszíni Netezza kiszolgálóról egy Azure-tárfiókba vagy az Azure SQL Data Warehouse adatbázisába a Hypertext Transfer Protocol Secure (HTTPS) protokollon keresztüli titkosított kapcsolat használatával. A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a köztes támadásokat.

Ha nem szeretné, hogy az adatok átvitele nyilvános interneten keresztül kerülhet, nagyobb biztonságot érhet el, ha az Azure Express Route-on keresztül privát társviszony-létesítési kapcsolaton keresztül továbbítja az adatokat. 

A következő szakasz bemutatja, hogyan érhető el a nagyobb biztonság.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz az adatok áttelepítési módját ismerteti.

### <a name="migrate-data-over-the-public-internet"></a>Adatok áttelepítése a nyilvános interneten keresztül

![Adatok áttelepítése a nyilvános interneten keresztül](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Az előző ábra a következőképpen értelmezhető:

- Ebben az architektúrában biztonságosan továbbíthatja az adatokat https használatával a nyilvános interneten keresztül.

- Ennek az architektúrának az eléréséhez telepítenie kell az Azure Data Factory integrációs futásidejű (saját üzemeltetésű) egy Windows-gépen egy vállalati tűzfal mögött. Győződjön meg arról, hogy ez az integrációs futásidejű közvetlenül hozzáférhet a Netezza kiszolgálóhoz. A hálózat és az adattárolók sávszélességének teljes használatához manuálisan skálázhatja a gépet, vagy több gépre skálázható.

- Ezzel az architektúrával a kezdeti pillanatkép-adatok és a különbözeti adatok áttelepítése is.

### <a name="migrate-data-over-a-private-network"></a>Adatok áttelepítése magánhálózaton keresztül 

![Adatok áttelepítése magánhálózaton keresztül](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Az előző ábra a következőképpen értelmezhető:

- Ebben az architektúrában az adatokat az Azure Express Route-on keresztül privát társviszony-létesítési kapcsolaton keresztül telepíti át, és az adatok soha nem haladnak át a nyilvános interneten. 

- Ennek az architektúrának az eléréséhez telepítenie kell az Azure Data Factory integrációs futásidejű (saját üzemeltetésű) egy Windows virtuális gépen (VM) az Azure virtuális hálózaton belül. A hálózati és adattárolók sávszélességének teljes kihasználásához manuálisan skálázhatja fel a virtuális gépet, vagy több virtuális gépre skálázhat.

- Ezzel az architektúrával a kezdeti pillanatkép-adatok és a különbözeti adatok áttelepítése is.

## <a name="implement-best-practices"></a>Bevált módszerek megvalósítása 

### <a name="manage-authentication-and-credentials"></a>Hitelesítés és hitelesítő adatok kezelése 

- A Netezza hitelesítéséhez használhatja az [ODBC-hitelesítést a kapcsolati karakterláncon keresztül.](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties) 

- Hitelesítés az Azure Blob-tárolóban: 

   - Javasoljuk, hogy [az Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)használatát. Az Azure Active Directoryban (Azure AD) automatikusan felügyelt Azure Data Factory-identitásra épülő felügyelt identitások lehetővé teszik a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat kellene megadnia a csatolt szolgáltatás definíciójában.  

   - Másik lehetőségként hitelesítheti magát az Azure Blob storage-ban [egyszerű szolgáltatás,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication) [egy közös hozzáférésű aláírás](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)vagy egy [tárfiókkulcs](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)használatával. 

- Hitelesítés az Azure Data Lake Storage Gen2 szolgáltatásban: 

   - Javasoljuk, hogy [az Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)használatát.
   
   - Egyszerű szolgáltatás [vagy](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) [tárfiókkulcs](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)is használható. 

- Hitelesítés az Azure SQL Data Warehouse-ban:

   - Javasoljuk, hogy [az Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)használatát.
   
   - Egyszerű [szolgáltatás-](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) vagy [SQL-hitelesítést](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication)is használhat.

- Ha nem használja az Azure-erőforrások felügyelt identitásait, javasoljuk, hogy [tárolja a hitelesítő adatokat az Azure Key Vaultban,](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) hogy megkönnyítse a kulcsok központi kezelését és elforgatását az Azure Data Factory-hoz csatolt szolgáltatások módosítása nélkül. Ez a [CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)egyik legjobb gyakorlata is. 

### <a name="migrate-initial-snapshot-data"></a>Kezdeti pillanatképadatok áttelepítése 

Kis táblák (azaz a 100 GB-nál kisebb kötetű vagy két órán belül az Azure-ba áttelepíthető táblák esetében) minden egyes másolási feladatbetöltési adatot készíthet táblázatonként. A nagyobb átviteli azért több Azure Data Factory másolási feladatok futtatásához külön táblák egyidejű betöltéséhez. 

Az egyes másolási feladatokkal párhuzamos lekérdezések futtatásához és az adatok partíciók szerinti másolásához a [ `parallelCopies` tulajdonságbeállítás](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) használatával is elérheti a párhuzamosság bizonyos szintjét az alábbi adatpartíció-beállításokkal:

- A nagyobb hatékonyság érdekében javasoljuk, hogy egy adatszeletből induljon ki.  Győződjön meg arról, hogy a `parallelCopies` beállítás értéke kisebb, mint a Netezza kiszolgálón lévő táblában lévő adatszelet-partíciók teljes száma.  

- Ha az egyes adatszelet-partíciók kötete még mindig nagy (például 10 GB vagy nagyobb), javasoljuk, hogy váltson dinamikus tartománypartícióra. Ez a beállítás nagyobb rugalmasságot biztosít a partíciók számának és az egyes partíciók mennyiségének meghatározásához partícióoszlop, felső és alsó határ szerint.

Nagyobb táblák (azaz a 100 GB-os vagy nagyobb kötetű, vagy két órán belül *nem* telepíthető táblák) esetén azt javasoljuk, hogy az adatokat egyéni lekérdezéssel particionálja, majd minden egyes másolási feladatpéldányt egyszerre egy partícióval készítsen. A jobb átviteli, futtathat több Azure Data Factory másolási feladatok egyidejűleg futtatható. Minden egyes copy-job cél betöltése egy partíciót egyéni lekérdezéssel, növelheti az átviteli forgalmat azáltal, hogy engedélyezi a párhuzamosság keresztül adatszelet vagy dinamikus tartomány. 

Ha egy másolási feladat egy hálózati vagy adattárbeli átmeneti probléma miatt sikertelen, újrafuttathatja a sikertelen másolási feladatot, hogy újrabetöltse az adott partíciót a táblából. Más más partíciókat betöltő másolási feladatokat nem érint.

Amikor adatokat tölt be egy Azure SQL Data Warehouse-adatbázisba, azt javasoljuk, hogy engedélyezze a PolyBase-t a másolási feladaton belül az Azure Blob storage-szal átmenetiként.

### <a name="migrate-delta-data"></a>Különbözeti adatok áttelepítése 

A tábla új vagy frissített sorainak azonosításához használjon időbélyegoszlopot vagy növekménykulcsot a sémán belül. Ezután a legújabb értéket magas vízjelként tárolhatja egy külső táblában, majd az adatok következő betöltésekkor szűrheti azt a különbözeti adatokszűrésére. 

Minden tábla más vízjeloszlopot használhat az új vagy frissített sorok azonosítására. Javasoljuk, hogy hozzon létre egy külső vezérlőtáblát. A táblában minden sor egy táblát jelöl a Netezza kiszolgálón, amelynek fajlagos vízjeloszlop-neve és magas vízjelértéke van. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs futásidő konfigurálása

Ha adatokat telepít át a Netezza kiszolgálóról az Azure-ba, függetlenül attól, hogy a kiszolgáló a vállalati tűzfal mögött vagy egy virtuális hálózati környezetben található, telepítenie kell egy saját üzemeltetésű infravörös szolgáltatást egy Windows-gépen vagy virtuális gépen, amely a használt motor adatok mozgatása. Az önkiszolgáló infravörös szolgáltatás telepítésekor a következő megközelítést javasoljuk:

- Minden egyes Windows-gép vagy virtuális gép esetén 32 vCPU és 128 GB-os memóriával kezdje. Az adatáttelepítés során folyamatosan figyelheti az infravörös gép processzor- és memóriahasználatát, így megállapíthatja, hogy a jobb teljesítmény érdekében tovább kell-e bővítenie a gépet, vagy csökkentenie kell a gépet a költségek csökkentése érdekében.

- Horizontális felskálázhatja is, ha egyetlen saját üzemeltetésű infravörös kapcsolattal legfeljebb négy csomópontot társít. Egy önkiszolgáló infravörös kapcsolaton futó egyetlen másolási feladat automatikusan alkalmazza az összes virtuálisgép-csomópontot az adatok párhuzamos másolásához. A magas rendelkezésre állás érdekében kezdje négy virtuálisgép-csomóponttal, hogy elkerülje az adatáttelepítés során egyetlen meghibásodási pontot.

### <a name="limit-your-partitions"></a>A partíciók korlátozása

Ajánlott eljárásként végezzen teljesítményigazolást (POC) egy reprezentatív mintaadatkészlettel, hogy minden egyes másolási tevékenységhez meghatározhassa a megfelelő partícióméretet. Azt javasoljuk, hogy töltse be az egyes partíciókat az Azure-ba két órán belül.  

Táblázat másolásához kezdje egyetlen másolási tevékenységgel egyetlen, saját üzemeltetésű infravörös géppel. Fokozatosan `parallelCopies` növelje a beállítást a táblázatban lévő adatszelet-partíciók száma alapján. Tekintse meg, hogy a teljes tábla két órán belül betölthető-e az Azure-ba a másolási feladatból eredő átviteli terhelésnek megfelelően. 

Ha két órán belül nem tölthető be az Azure-ba, és a saját üzemeltetésű infravörös csomópont és az adattár kapacitása nincs teljes mértékben kihasználva, fokozatosan növelje az egyidejű másolási tevékenységek számát, amíg el nem éri a hálózat korlátját vagy az adatok sávszélesség-korlátját. Üzletek. 

Figyelje a cpu- és memóriahasználatot a saját üzemeltetésű infravörös gépen, és készen áll a gép méretezésére vagy több gépre való horizontális felskálázásra, amikor látja, hogy a processzor és a memória teljes mértékben kihasználva van. 

Ha az Azure Data Factory másolási tevékenysége által jelentett szabályozási hibákat `parallelCopies` észlel, vagy csökkentse az egyidejűséget vagy a beállítást az Azure Data Factoryban, vagy fontolja meg a hálózati és adattárak másodpercenkénti sávszélesség- vagy I/O-műveleteinek növelését. 


### <a name="estimate-your-pricing"></a>Becsülje meg az árakat 

Vegye figyelembe a következő folyamatot, amely úgy van kialakítva, hogy adatokat telepítsen át a helyszíni Netezza kiszolgálóról egy Azure SQL Data Warehouse-adatbázisba:

![Az árképzési folyamat](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Tegyük fel, hogy a következő állítások igazak: 

- A teljes adatmennyiség 50 terabájt (TB). 

- Az adatokat az első megoldás architektúrájának használatával telepítjük át (a Netezza kiszolgáló a helyszíni, a tűzfal mögött található).

- Az 50 TB-os kötet 500 partícióra van osztva, és minden másolási tevékenység egy partíciót mozgat.

- Minden másolási tevékenység egy saját üzemeltetésű infravörös kapcsolattal van konfigurálva négy gép ellen, és másodpercenként 20 megabájt (MBps) átviteli sebességet ér el. (A másolási `parallelCopies` tevékenységen belül 4-re van állítva, és a táblából betöltő minden szál 5 Mb/s átviteli sebességet ér el.)

- A ForEach egyidejűség 3- ra van állítva, és az összesített átviteli sebesség 60 Mb/s.

- Az áttelepítés összesen 243 órát vesz igénybe.

Az előző feltételezések alapján, itt a becsült ár: 

![Az árképzési táblázat](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Az előző táblázatban szereplő árképzés hipotetikus. A tényleges díjszabás a környezet tényleges átviteli átmenő eszköztől függ. A Windows-gép ára (a saját üzemeltetésű infravörös telepítéssel) nem tartalmazza. 

### <a name="additional-references"></a>További referenciák

További információt az alábbi cikkekben és útmutatókban talál:

- [Adatok áttelepítése helyszíni relációs adattárház-adatbázisból az Azure-ba az Azure Data Factory használatával](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza csatlakozó](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [ODBC-összekötő](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Azure Blob storage-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Azure SQL Data Warehouse összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Tevékenységteljesítmény-hangolási útmutató másolása](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Helyi integrációs modul létrehozása és konfigurálása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Self-hosted integrációs futásidejű HA és méretezhetőség](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Hitelesítő adatok tárolása az Azure Key Vaultban](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Adatok növekményes másolása egy táblából](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Adatok növekményes másolása több táblából](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Az Azure Data Factory díjszabási lapja](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>További lépések

- [Fájlok másolása több tárolóból az Azure Data Factory használatával](solution-template-copy-files-multiple-containers.md)
