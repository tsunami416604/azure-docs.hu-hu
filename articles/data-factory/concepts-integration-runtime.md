---
title: Integrációs modul az Azure Data Factoryban | Microsoft Docs
description: Ismerje meg az integrációs modult az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 61dea25b9a52a9acaee029162929e4faeb3a0978
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291857"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integrációs modul az Azure Data Factoryban
Az integrációs modul (Integration Runtime, IR), az Azure Data Factory által használt számítási infrastruktúra a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatok áthelyezése**: Mozgathat adatokat a nyilvános hálózatok adattárai és a magánhálózatok (helyszíni vagy virtuális magánhálózatok) adattárai között. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenység küldése**: Elküldheti és monitorozhatja a különböző számítási szolgáltatásokon (HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server és még sok más) futó átalakítási tevékenységeket.
- **SSIS-csomag végrehajtása**: Natívan végrehajthat SQL Server Integration Services- (SSIS-) csomagokat egy Azure-beli felügyelt számítási környezetben.


> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

A Data Factoryban a végrehajtandó műveletet egy tevékenység határozza meg. A társított szolgáltatások a céladattárat vagy a számítási szolgáltatást határozzák meg. Az integrációs modulok hídként szolgálnak a tevékenység és a társított szolgáltatások között.  A társított szolgáltatás hivatkozik rájuk, és megadja a számítási környezetet, ahol a tevékenység fut, vagy ahonnan a tevékenységet küldik.  Ily módon a tevékenység végrehajtható a céladattárhoz vagy számítási szolgáltatáshoz lehető legközelebb eső régióban, a lehető leghatékonyabban, a biztonsági és megfelelőségi igényeknek is megfelelően.

## <a name="integration-runtime-types"></a>Integrációsmodul-típusok
A Data Factory három típusú integrációs modult ajánl, és ki kell választania azt a típust, amely a leginkább megfelel az adatintegrációsképesség- és hálózatikörnyezet-igényeinek.  A három típus a következő:

- Azure
- Saját üzemeltetésű
- Azure SSIS

Az alábbi táblázat ismerteti az integrációs modulok egyes típusainak képességeit és hálózati támogatását:

Integrációs modul típusa | Nyilvános hálózat | Magánhálózat
------- | -------------- | ---------------
Azure | Adatáthelyezés<br/>Tevékenység küldése | &nbsp;
Saját üzemeltetésű | Adatáthelyezés<br/>Tevékenység küldése | Adatáthelyezés<br/>Tevékenység küldése
Azure SSIS | SSIS-csomag végrehajtása | SSIS-csomag végrehajtása

Az alábbi diagram bemutatja, hogyan használhatók a különböző integrációs modulok kombinálva, hogy változatos adatintegrációs képességeket és hálózati támogatást nyújtsanak:

![Különböző típusú integrációs modulok](media\concepts-integration-runtime\different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul
Egy Azure-beli integrációs modul a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak között
- Az alábbi átalakítási tevékenységek küldése nyilvános hálózatba: HDInsight Hive-tevékenység, HDInsight Pig-tevékenység, HDInsight MapReduce-tevékenység, HDInsight Spark-tevékenység, HDInsight Streaming-tevékenység, Machine Learning Batch Execution-tevékenység, Machine Learning Update Resource-tevékenységek, Stored Procedure-tevékenység, Data Lake Analytics U-SQL-tevékenység, egyéni .NET-tevékenység, webes tevékenység, keresési tevékenység és metaadatok beolvasása tevékenység.

### <a name="azure-ir-network-environment"></a>Azure-beli integrációs modul hálózati környezete
Az Azure-beli integrációs modul támogatja az adattárak és számítási szolgáltatások összekapcsolását a nyilvános hálózatban nyilvánosan hozzáférhető végpontokkal. Használhat saját üzemeltetésű integrációs modult az Azure Virtual Network-környezethez.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure-beli integrációs modul számítási erőforrásai és skálázása
Az Azure-beli integrációs modul teljesen felügyelt, kiszolgáló nélküli számítást biztosít az Azure-ban.  Nem kell foglalkoznia infrastruktúra létesítésével, szoftver telepítésével, frissítéssel vagy a kapacitás méretezésével.  Ráadásul csak a tényleges használat időtartamára fizet.

Az Azure-beli integrációs modul biztosítja a natív számítást az adatok felhőalapú adattárak közötti biztonságos, megbízható és nagy teljesítményű módon való mozgatásához.  Beállíthatja a másolási tevékenységre használni kívánt adatmozgatási egység mennyiségét, és az Azure IR számítási mérete rugalmasan, megfelelően méretezhető anélkül, hogy explicit módon módosítania kellene az Azure-beli integrációs modul méretét.

A tevékenységküldés egy könnyen használható művelet a tevékenységnek a cél számítási szolgáltatásba való irányítására, így ehhez a forgatókönyvhöz nincs szükség a számítási méret vertikális felskálázására.

Az Azure integrációs modulok létrehozásáról és konfigurálásáról az Azure IR létrehozása és konfigurálása útmutatóban találhat információkat. 

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul
Egy saját üzemeltetésű IR a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak és egy magánhálózaton lévő adattár között.
- Az alábbi átalakítási tevékenységek küldése helyszíni vagy Azure virtuális hálózaton lévő számítási erőforrásokba: HDInsight Hive-tevékenység (BYOC), HDInsight Pig-tevékenység (BYOC), HDInsight MapReduce-tevékenység (BYOC), HDInsight Spark-tevékenység (BYOC), HDInsight Streaming-tevékenység (BYOC), Machine Learning Batch Execution-tevékenység, Machine Learning Update Resource-tevékenységek, Stored Procedure-tevékenység, Data Lake Analytics U-SQL-tevékenység, egyéni .NET-tevékenység, keresési tevékenység és metaadatok beolvasása tevékenység.

> [!NOTE] 
> Saját üzemeltetésű integrációs modullal támogathat saját illesztőt igénylő adattárakat (például SAP Hana, MySQL stb.).  További információk: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="self-hosted-ir-network-environment"></a>Saját üzemeltetésű integrációs modul hálózati környezete
Ha biztonságosan szeretne adatintegrációt végezni egy nyilvános felhőkörnyezetből közvetlenül nem látható magánhálózati környezetben, telepíthet egy saját üzemeltetésű integrációs modult helyszíni környezeten a vállalati tűzfal mögé vagy a virtuális magánhálózaton belülre.  A saját üzemeltetésű integrációs modul csak kimenő HTTP-alapú kapcsolatokat hoz létre az internet megnyitásához.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Saját üzemeltetésű integrációs modul erőforrásai és skálázása
A saját üzemeltetésű integrációs modult helyszíni gépre vagy magánhálózaton belüli virtuális gépre kell telepíteni. Jelenleg csak Windows operációs rendszeren támogatjuk a saját üzemeltetésű integrációs modulok futtatását.  

A magas rendelkezésre állás és a méretezhetőség érdekében horizontálisan felskálázhatja saját üzemeltetésű integrációs modulját, ha több helyszíni géppel aktív-aktív módban társít hozzá egy logikai példányt.  További információkért tekintse meg az útmutatók között a saját üzemeltetésű integrációs modul létrehozását és konfigurálását ismertető cikket.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS integrációs modul
A meglévő SSIS számítási feladat átemeléséhez létrehozhat egy Azure-SSIS integrációs modult az SSIS-csomagok natív létrehozásához.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS integrációs modul hálózati környezete
Az Azure-SSIS integrációs modul kiépíthető nyilvános hálózaton vagy magánhálózaton is.  A helyszíni adathozzáférés támogatása az Azure-SSIS integrációs modulnak a helyszíni hálózathoz csatlakoztatott virtuális hálózattal való összekapcsolása révén történik.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS integrációs modul számítási erőforrásai és skálázása
Az Azure-SSIS integrációs modul egy, az SSIS-csomagok futtatására dedikált Azure-beli virtuális gépekből álló teljesen felügyelt fürt. A saját Azure SQL Database- vagy felügyeltpéldány- (előzetes verzió) kiszolgálóját is használhatja az SSIS-projektek/csomagok (SSISDB) csatolandó katalógusának üzemeltetésére. A számítási teljesítmény vertikális felskálázásához adjon meg egy csomópontméretet, és skálázza fel horizontálisan a fürt csomópontszámának megadásával. Az Azure-SSIS integrációs modul futtatási költségének kezeléséhez igény szerint leállíthatja és elindíthatja azt.

További információkért tekintse meg az útmutatók között az Azure-SSIS integrációs modul létrehozását és konfigurálását ismertető cikket.  Létrehozásukat követően kevés vagy szinte semmilyen módosítással, a helyszíni SSIS-csomagokkal megegyező módon üzembe helyezheti és kezelheti meglévő SSIS-csomagjait az olyan jól ismert eszközök használatával, mint az SQL Server Data Tools (SSDT) és az SQL Server Management Studio (SSMS).

Az Azure-SSIS integrációs modullal kapcsolatos további információkért tekintse át a következő cikkeket: 

- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál az Azure SQL felügyelt példányának (előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 

## <a name="integration-runtime-location"></a>Az integrációs modul helye
A Data Factory ott található, ahol az adat-előállító metaadatait tárolja a rendszer, és ahonnan a folyamat aktiválása indul. Az adat-előállító ilyenkor más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti áthelyezése vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. Ez a viselkedés az adatok megfelelősége, a hatékonyság és a hálózati kimeneti forgalmának alacsonyabb költségei érdekében a [globálisan elérhető integrációs modulon](https://azure.microsoft.com/global-infrastructure/services/) keresztül valósul meg.

Az integrációs modul helye meghatározza a háttérszámítások helyét és azt a helyet, ahol az adatok áthelyezése, a tevékenységküldés és az SSIS-csomag végrehajtása történik. Az integrációs modul helye eltérhet annak az adat-előállítónak a helyétől, amelyhez tartozik. 

### <a name="azure-ir-location"></a>Az Azure-beli integrációs modul helye
Beállíthat egy adott helyet az Azure-beli integrációs modul számára. Ebben esetben az adatok áthelyezése vagy a tevékenységküldés abban a régióban történik. 

Ha úgy dönt, hogy az automatikus feloldásra képes Azure-beli integrációs modult használja, amely az alapértelmezett beállítás: 

- A másolási tevékenység során az ADF mindent megtesz, hogy automatikusan észlelje a fogadó- és forrásadattárat, kiválassza ugyanabban a régióban (ha lehetséges) vagy a legközelebbi azonos földrajzi helyen a legjobb helyet, vagy helyettük az adat-előállító régiót használja, ha ezek nem észlelhetőek.
- Az ADF az integrációs modult a keresési/metaadat-beolvasási tevékenység végrehajtásakor és az átalakítási tevékenység elküldésekor az adat-előállító régióban használja.

A folyamattevékenységek monitorozása nézetben a felhasználói felületen vagy a tevékenységfigyelés hasznos adatainál figyelemmel követheti, melyik integrációs modul lép életbe a tevékenység végrehajtása során.

>[!TIP]
>Ha adatmegfelelőségi követelmények szigorúak, és biztosítania kell, hogy az adatok nem hagynak el egy adott földrajzi helyet, az adott régióban létrehozhat egy Azure-beli integrációs modult, és a ConnectVia tulajdonság használatával ehhez irányíthatja a társított szolgáltatást. Ha például adatokat szeretne másolni az Egyesült Királyság déli régióján belüli blobból az Egyesült Királyság déli régióján belüli SQL DW-be, és biztos szeretne lenni abban, hogy az adatok nem hagyják el az Egyesült Királyságot, hozzon létre egy Azure-beli integrációs modult az Egyesült Királyság déli régiójában, és társítson minden társított szolgáltatást ehhez a modulhoz.

### <a name="self-hosted-ir-location"></a>A saját üzemeltetésű integrációs modul helye
A saját üzemeltetésű integrációs modul logikailag van regisztrálva a Data Factoryban, és a funkciók támogatásához használt számítást meg kell adnia. Ezért a saját üzemeltetésű integrációs modulok esetében nincs explicit hely tulajdonság. 

Ha adatmozgás végrehajtásához használja, a saját üzemeltetésű integrációs modul kinyeri az adatokat a forrásból, és a célra írja.

### <a name="azure-ssis-ir-location"></a>Az Azure-SSIS integrációs modul helye
Az Azure-SSIS integrációs modul számára a megfelelő helyet kiválasztása az ETL folyamatokban létfontosságú a magas teljesítmény eléréséhez.

- Az Azure-SSIS integrációs modul helyének nem kell megegyeznie az adat-előállítója helyével, de meg kell egyeznie a saját, az SSISDB üzemeltetési helyéül szolgáló Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálójának helyével. Ily módon az Azure-SSIS integrációs modul könnyen hozzáfér az SSISDB-hez anélkül, hogy jelentős adatforgalom zajlana különböző helyek között.
- Ha nincs meglévő Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálója az SSISDB üzemeltetésére, de rendelkezik helyszíni adatforrásokkal/célokkal, érdemes létrehoznia egy új Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálót egy, a helyszíni hálózatához csatlakoztatott virtuális hálózat helyével megegyező helyen.  Ily módon létrehozhatja az Azure-SSIS integrációs modulját az új Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálóval, és a virtuális hálózathoz csatlakozva hatékonyan minimalizálhatja a különböző helyek közötti adatforgalmat, hiszen minden egy helyen található.
- Ha rendelkezik meglévő Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálóval az SSISDB üzemeltetésére, de nem a helyszíni hálózatához csatlakoztatott virtuális hálózat helyével megegyező helyen, először hozza létre az Azure-SSIS integrációs modult a meglévő Azure SQL Database-/felügyeltpéldány- (előzetes verzió) kiszolgálóval, majd csatlakozzon egy másik virtuális hálózathoz azon a helyen, végül a különböző helyek között konfiguráljon egy virtuális hálózatok közötti kapcsolatot.

Az alábbi ábrán a Data Factory és a hozzá tartozó integrációs modul beállításai találhatók:

![Az integrációs modul helye](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>A használandó integrációs modul meghatározása

### <a name="copy-activity"></a>Másolási tevékenység

A másolási tevékenységhez szükséges egy forrás és fogadó társított szolgáltatás az adatfolyam irányának meghatározására. A rendszer az alábbi logikával határozza meg, melyik integrációsmodell-példányt használja a másolás végrehajtásához: 

- **Másolás két felhőalapú adatforrás között**: ha a forrás és a fogadó társított szolgáltatás is az Azure-beli integrációs modult használja, az ADF a regionális Azure-beli integrációs modult fogja használni, ha meg lett adva, vagy az automatikus feloldású integrációs modul választása esetén (alapértelmezett) automatikusan meghatároz egy helyet [Az integrációs modul helye](#integration-runtime-location) részben foglaltak szerint.
- **Egy felhőalapú adatforrás és egy magánhálózaton lévő adatforrás közötti másolás**: ha a forrás vagy a fogadó társított szolgáltatása egy saját üzemeltetésű integrációs modulra mutat, a rendszer azon a saját üzemeltetésű integrációs modulon hajtja végre a másolási tevékenységet.
- **Másolás két magánhálózaton lévő adatforrás között**: a forrás és a fogadó társított szolgáltatásnak ugyanarra az integrációsmodul-példányra kell mutatnia, és a rendszer azt az integrációs modult használja a másolási tevékenység végrehajtásához.

### <a name="lookup-and-getmetadata-activity"></a>Keresési és metaadat-beolvasási tevékenység

A keresési és metaadat-beolvasási tevékenységet a rendszer az adattár társított szolgáltatásához rendelt integrációs modulon hajtja végre.

### <a name="transformation-activity"></a>Átalakítási tevékenység

Minden átalakítási tevékenységhez tartozik egy cél számítási társított szolgáltatás, amely egy integrációs modulra mutat. Ez az integrációsmodul-példány az, ahonnan a rendszer küldi az átalakítási tevékenységet.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatóul szolgál az Azure SQL felügyelt példányának (előzetes verzió) használatához, illetve az integrációs modul virtuális hálózathoz történő csatlakoztatásához. 
