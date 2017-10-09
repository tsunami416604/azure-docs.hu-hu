---
title: "Integrációs modul az Azure Data Factoryban | Microsoft Docs"
description: "Ismerje meg az integrációs modult az Azure Data Factoryban."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/15/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf05a28c0fe40839e26cef1af51f3f0317e1e154
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="integration-runtime-in-azure-data-factory"></a>Integrációs modul az Azure Data Factoryban
Az integrációs modul (Integration Runtime, IR), az Azure Data Factory által használt számítási infrastruktúra a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatok áthelyezése**: Mozgathat adatokat a nyilvános hálózatok adattárai és a magánhálózatok (helyszíni vagy virtuális magánhálózatok) adattárai között. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenység küldése**: Elküldheti és monitorozhatja a különböző számítási szolgáltatásokon (HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server és még sok más) futó átalakítási tevékenységeket.
- **SSIS-csomag végrehajtása**: Natívan végrehajthat SQL Server Integration Services (SSIS) csomagokat egy felügyelt Azure számítási környezetben.


> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

A Data Factoryban a végrehajtandó műveletet egy tevékenység határozza meg. A társított szolgáltatások a cél adattárat vagy a számítási szolgáltatást határozzák meg. Az integrációs modulok hídként szolgálnak a tevékenység és a társított szolgáltatások között.  A társított szolgáltatás hivatkozik rájuk, és megadja a számítási környezetet, ahol a tevékenység fut, vagy ahonnan a tevékenységet küldik.  Ily módon a tevékenység végrehajtható a cél adattárhoz vagy számítási szolgáltatáshoz lehető legközelebb eső régióban, a lehető leghatékonyabban, a biztonsági és megfelelőségi igényeknek is megfelelően.

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


## <a name="azure-integration-runtime"></a>Azure integrációs modul
Egy Azure integrációs modul a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak között
- Az alábbi átalakítási tevékenységek küldése nyilvános hálózatba: HDInsight Hive-tevékenység, HDInsight Pig-tevékenység, HDInsight MapReduce-tevékenység, HDInsight Spark-tevékenység, HDInsight Streaming-tevékenység, Machine Learning Batch Execution-tevékenység, Machine Learning Update Resource-tevékenységek, Stored Procedure-tevékenység, Data Lake Analytics U-SQL-tevékenység, .Net egyéni-tevékenység, webes tevékenység, keresési tevékenység és metaadatok beolvasása tevékenység.

### <a name="network-environment"></a>Hálózati környezet
Az Azure integrációs modul támogatja az adattárak és számítási szolgáltatások összekapcsolását a nyilvános hálózatban nyilvánosan hozzáférhető végpontokkal. Használhat saját üzemeltetésű integrációs modult az Azure Virtual Network környezethez.

### <a name="compute-resource-and-scaling"></a>Számítási erőforrások és méretezés
Az Azure integrációs modul teljesen felügyelt, kiszolgáló nélküli számítást biztosít az Azure-ban.  Nem kell foglalkoznia infrastruktúra létesítésével, szoftver telepítésével, frissítéssel vagy a kapacitás méretezésével.  Ráadásul csak a tényleges használat időtartamára fizet.

Az Azure integrációs modul biztosítja a natív számítást az adatok felhőalapú adattárak közötti biztonságos, megbízható és nagy teljesítményű módon való mozgatásához.  Beállíthatja a másolási tevékenységre használni kívánt adatmozgatási egység mennyiségét, és az Azure IR számítási mérete rugalmasan, megfelelően méretezhető anélkül, hogy explicit módon módosítania kellene az Azure integrációs modul méretét.

A tevékenységküldés egy könnyen használható művelet a tevékenységnek a cél számítási szolgáltatásba való irányítására, így ehhez a forgatókönyvhöz nincs szükség a számítási méret vertikális felskálázására.

Az Azure integrációs modulok létrehozásáról és konfigurálásáról az Azure IR létrehozása és konfigurálása útmutatóban találhat információkat. 

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul
Egy saját üzemeltetésű IR a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak és egy magánhálózaton lévő adattár között.
- Az alábbi átalakítási tevékenységek küldése helyszíni vagy Azure virtuális hálózaton lévő számítási erőforrásokba: HDInsight Hive-tevékenység (BYOC), HDInsight Pig-tevékenység (BYOC), HDInsight MapReduce-tevékenység (BYOC), HDInsight Spark-tevékenység (BYOC), HDInsight Streaming-tevékenység (BYOC), Machine Learning Batch Execution-tevékenység, Machine Learning Update Resource-tevékenységek, Stored Procedure-tevékenység, Data Lake Analytics U-SQL-tevékenység, .Net egyéni tevékenység, keresési tevékenység és metaadatok beolvasása tevékenység.

> [!NOTE] 
> Saját üzemeltetésű integrációs modullal támogathat saját illesztőt igénylő adattárakat (például SAP Hana, MySQL stb.).  További információk: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Hálózati környezet
Ha biztonságosan szeretne adatintegrációt végezni egy nyilvános felhőkörnyezetből közvetlenül nem látható magánhálózati környezetben, telepíthet egy saját üzemeltetésű integrációs modult helyszíni környezeten a vállalati tűzfal mögé vagy a virtuális magánhálózaton belülre.  A saját üzemeltetésű integrációs modul csak kimenő HTTP-alapú kapcsolatokat hoz létre az internet megnyitásához.

### <a name="compute-resource-and-scaling"></a>Számítási erőforrások és méretezés
A saját üzemeltetésű integrációs modult helyszíni gépre vagy magánhálózaton belüli virtuális gépre kell telepíteni. Jelenleg csak Windows operációs rendszeren támogatjuk a saját üzemeltetésű integrációs modulok futtatását.  

A magas rendelkezésre állás és a méretezhetőség érdekében horizontálisan felskálázhatja saját üzemeltetésű integrációs modulját, ha több helyszíni géppel aktív-aktív módban társít hozzá egy logikai példányt.  További információkért tekintse meg az útmutatók között a saját üzemeltetésű integrációs modul létrehozása és konfigurálása témakört.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS integrációs modul
A meglévő SSIS számítási feladat átemeléséhez létrehozhat egy Azure-SSIS integrációs modult az SSIS-csomagok natív létrehozásához.

### <a name="network-environment"></a>Hálózati környezet
Az Azure-SSIS integrációs modul kiépíthető nyilvános hálózaton vagy magánhálózaton is.  A helyszíni adathozzáférés támogatása az Azure-SSIS integrációs modulnak a helyszíni hálózathoz csatlakoztatott virtuális hálózattal való összekapcsolása révén történik. Jelenleg csak a klasszikus VNET támogatott. 

### <a name="compute-resource-and-scaling"></a>Számítási erőforrások és méretezés
Az Azure-SSIS integrációs modul egy, az SSIS-csomagok futtatására dedikált Azure-beli virtuális gépekből álló teljesen felügyelt fürt. Használhatja a saját Azure SQL Database vagy Managed Instance (privát előzetes verzió) kiszolgálóját az SSIS-projektek-csomagok (SSISDB) csatolandó katalógusának üzemeltetésére. A számítási teljesítmény vertikális felskálázásához adjon meg egy csomópontméretet, és skálázza fel horizontálisan a fürt csomópontszámának megadásával. Az Azure-SSIS integrációs modul futtatási költségének kezeléséhez igény szerint leállíthatja és elindíthatja azt.

További információkért tekintse meg az útmutatók között az Azure-SSIS integrációs modul létrehozása és konfigurálása témakört.  Amikor létrejött, kevés vagy akár semmilyen módosítással, a helyszíni SSIS-csomagokkal megegyező módon üzembe helyezheti és kezelheti meglévő SSIS-csomagjait, olyan jól ismert eszközök használatával, mint az SQL Server Data Tools (SSDT) és az SQL Server Management Studio (SSMS).

## <a name="determining-which-ir-to-use"></a>A használandó integrációs modul meghatározása
Minden átalakítási tevékenységhez tartozik egy cél számítási társított szolgáltatás, amely egy integrációs modulra mutat. Ez az integrációsmodul-példány az, ahonnan a rendszer küldi az átalakítási tevékenységet.

A másolási tevékenységhez szükséges egy forrás és fogadó társított szolgáltatás az adatfolyam irányának meghatározására. A rendszer az alábbi logikával határozza meg, melyik integrációsmodell-példányt használja a másolás végrehajtásához: 

- **Másolás két felhőalapú adatforrás között**: ha a forrás és a fogadó társított szolgáltatás is az Azure integrációs modult használja, a rendszer a fogadó társított szolgáltatás által használt integrációs modult használja a másolási tevékenység végrehajtásához.
- **Egy felhőalapú adatforrás és egy magánhálózaton lévő adatforrás közötti másolás**: ha a forrás vagy a fogadó társított szolgáltatása egy saját üzemeltetésű integrációs modulra mutat, a rendszer azon a saját üzemeltetésű integrációs modulon hajtja végre a másolási tevékenységet.
- **Másolás két magánhálózaton lévő adatforrás között**: a forrás és a fogadó társított szolgáltatásnak ugyanarra az integrációsmodul-példányra kell mutatnia, és a rendszer azt az integrációs modult használja a másolási tevékenység végrehajtásához.

Az alábbi ábrán két példa látható másolási tevékenységre:

- Az 1. másolási tevékenységnél a forrás egy SQL Server társított szolgáltatás, amely az A jelű saját üzemeltetésű integrációs modulra hivatkozik, a fogadó pedig a B jelű Azure integrációs modulra hivatkozó Azure Storage társított szolgáltatás. A rendszer a másolási tevékenység futtatásakor az A jelű saját üzemeltetésű integrációs modulon hajtja azt végre.
- A 2. másolási tevékenységnél a forrás egy Azure SQL Database társított szolgáltatás, amely a C jelű Azure integrációs modulra hivatkozik, a fogadó pedig a B jelű Azure integrációs modulra hivatkozó Azure Storage társított szolgáltatás. A rendszer a másolási tevékenység futtatásakor a B jelű Azure integrációs modulon hajtja azt végre, mert az a fogadó társított szolgáltatás által használt integrációs modul.

![A használandó integrációs modul](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Az integrációs modul helye
A Data Factory ott található, ahol az adat-előállító metaadatait tárolja a rendszer, és ahonnan a folyamat aktiválása indul. A jelenleg támogatott Data Factory helyek: USA keleti régiója, USA 2. keleti régiója. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. Ez a viselkedés az adatmegfelelőség, a hatékonyság és a csökkentett hálózati adatkimeneti költségek biztosítása érdekében a globálisan több régióban rendelkezésre álló integrációs modulon keresztül valósul meg.

Az integrációs modul helye meghatározza a háttérszámítások helyét, és lényegében a helyet, ahol az adatmozgás, a tevékenységküldés és az SSIS-csomag végrehajtása történik. Az integrációs modul helye eltérhet annak az adat-előállítónak a helyétől, amelyhez tartozik. Az alábbi ábrán a Data Factory és a hozzá tartozó integrációs modul beállításai találhatók:

![Az integrációs modul helye](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure integrációs modul
A Data Factory az adatmozgatáshoz a fogadó földrajzi helyéhez lehető legközelebb eső régióban található Azure integrációs modult használja. A hozzárendeléseket a következő táblázatban tekintheti meg:

A fogadó adattár földrajzi helye | A fogadó adattár helye | Az Azure integrációs modulhoz használt hely
-------------------------------| ----------------| ------------------
Egyesült Államok | USA keleti régiója | USA keleti régiója
&nbsp; | USA 2. keleti régiója | USA 2. keleti régiója
&nbsp; | USA középső régiója | USA középső régiója
&nbsp; | USA északi középső régiója | USA északi középső régiója
&nbsp; | USA déli középső régiója | USA déli középső régiója
&nbsp; | USA nyugati középső régiója | USA nyugati középső régiója
&nbsp; | USA nyugati régiója | USA nyugati régiója
&nbsp; | USA nyugati régiója, 2. | USA nyugati régiója
Kanada | Kelet-Kanada | Közép-Kanada
&nbsp; | Közép-Kanada | Közép-Kanada
Brazília | Dél-Brazília | Dél-Brazília
Európa | Észak-Európa | Észak-Európa
&nbsp; | Nyugat-Európa | Nyugat-Európa
Egyesült Királyság | Az Egyesült Királyság nyugati régiója | Az Egyesült Királyság déli régiója
&nbsp; | Az Egyesült Királyság déli régiója | Az Egyesült Királyság déli régiója
Ázsia és a Csendes-óceáni térség | Délkelet-Ázsia | Délkelet-Ázsia
&nbsp; | Kelet-Ázsia | Délkelet-Ázsia
Ausztrália | Kelet-Ausztrália | Kelet-Ausztrália
&nbsp; | Délkelet-Ausztrália | Délkelet-Ausztrália
Japán | Kelet-Japán | Kelet-Japán
&nbsp; | Nyugat-Japán | Kelet-Japán
Korea | Korea középső régiója | Korea déli régiója
&nbsp; | Korea déli régiója | Korea déli régiója
India | Közép-India | Közép-India
&nbsp; | Nyugat-India | Közép-India
&nbsp; | Dél-India | Közép-India

Egy Azure integrációs modul helyéhez megadhatja az automatikus feloldás beállítást is. Ekkor a Data Factory mindent megtesz, hogy a társított szolgáltatás definíciója alapján automatikusan észlelje a legjobb használható helyet.

> [!NOTE] 
> Ha a cél adattár nem szerepel a listán, vagy nem észlelhető, a tevékenység megfelelőségi okok miatt meghiúsul, és nem lép át egy másik régióba. Ebben az esetben jelezze explicit módon a másolás végrehajtásához használni kívánt alternatív helyet.
 
Az alábbi kép egy példát mutat be a hatékony helyre, ha az Azure integrációs modul helyének beállítása automatikus megoldás. Egy másolási tevékenység végrehajtásakor észleli az adatcél helyét, amely ebben az esetben Nyugat-Japán.  A táblázat alapján az aktuális adatmásolás végrehajtásához a rendszer egy Kelet-Japán régióban található Azure integrációs modult használ. Ha a rendszer ugyanezt az integrációs modult használja a HDInsighthoz való csatlakozásra egy Spark tevékenységhez, a Spark alkalmazás beküldése a Data Factory helyéről történik, amely ebben a példában az USA keleti régiója, és a Spark alkalmazás tényleges végrehajtása a HDInsight kiszolgáló helye. 

![Hatályos hely](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Saját üzemeltetésű integrációs modul
A saját üzemeltetésű integrációs modul logikailag van regisztrálva a Data Factoryban, és a funkciók támogatásához használt számítást meg kell adnia. Ezért a saját üzemeltetésű integrációs modulok esetében nincs explicit hely tulajdonság. 

Ha adatmozgás végrehajtásához használja, a saját üzemeltetésű integrációs modul kinyeri az adatokat a forrásból, és a célra írja.

### <a name="azure-ssis-ir"></a>Azure-SSIS integrációs modul
Az Azure-SSIS integrációs modul számára a megfelelő helyet kiválasztása az ETL folyamatokban létfontosságú a magas teljesítmény eléréséhez.  Az előzetes verzióban kezdetben két hely érhető el (az USA keleti régiója és Észak-Európa).

- Az Azure-SSIS integrációs modul helyének nem kell megegyeznie az adat-előállítója helyével, de meg kell egyeznie a saját, az SSIDB üzemeltetési helyéül szolgáló Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálójának helyével. Ily módon az Azure-SSIS integrációs modul könnyen hozzáfér az SSISDB-hez anélkül, hogy jelentős adatforgalom zajlana különböző helyek között.
- Ha nem rendelkezik meglévő Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálóval az SSISDB üzemeltetésére, de rendelkezik helyszíni adatforrásokkal/célokkal, érdemes létrehoznia egy új Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálót egy, a helyszíni hálózatához csatlakoztatott virtuális hálózat helyével megegyező helyen.  Ily módon létrehozhatja az Azure-SSIS integrációs modulját az új Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálóval, és a virtuális hálózathoz csatlakozva hatékonyan minimalizálhatja a különböző helyek közötti adatmozgást, hiszen minden egy helyen található.
- Ha rendelkezik meglévő Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálóval az SSISDB üzemeltetésére, de nem a helyszíni hálózatához csatlakoztatott virtuális hálózat helyével megegyező helyen, először hozza létre az Azure-SSIS integrációs modult a meglévő Azure SQL Database/Managed Instance (privát előzetes verzió) kiszolgálóval, majd csatlakozzon egy másik virtuális hálózathoz azon a helyen, végül a különböző helyek között konfiguráljon egy virtuális hálózatok közötti kapcsolatot.


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)

