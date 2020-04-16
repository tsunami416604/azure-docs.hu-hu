---
title: Integrációs modul
description: Ismerje meg az integrációs modult az Azure Data Factoryban.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: ffa348c796a4d9d4e3bdb8e7ce18ba0eb82e17ad
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418382"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integrációs modul az Azure Data Factoryban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az integrációs modul (Integration Runtime, IR), az Azure Data Factory által használt számítási infrastruktúra a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatfolyam:** [Adatfolyam](concepts-data-flow-overview.md) végrehajtása felügyelt Azure számítási környezetben.  
- **Adatmozgás**: Adatok másolása a nyilvános hálózat adattáraiba és a magánhálózat (helyszíni vagy virtuális magánhálózat) adattáraiba. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenységfeladása:** A különböző számítási szolgáltatásokon, például az Azure Databricksen, az Azure HDInsighton, az Azure Machine Learningen, az Azure SQL Database-en, az SQL Serveren futó átalakítási tevékenységek feladása és figyelése.
- **SSIS-csomag végrehajtása**: Natívan végrehajthat SQL Server Integration Services- (SSIS-) csomagokat egy Azure-beli felügyelt számítási környezetben.

A Data Factoryban a végrehajtandó műveletet egy tevékenység határozza meg. A társított szolgáltatások a céladattárat vagy a számítási szolgáltatást határozzák meg. Az integrációs modulok hídként szolgálnak a tevékenység és a társított szolgáltatások között.  A csatolt szolgáltatás vagy tevékenység hivatkozik rá, és biztosítja a számítási környezetet, ahol a tevékenység fut, vagy küldi el. Ily módon a tevékenység végrehajtható a céladattárhoz vagy számítási szolgáltatáshoz lehető legközelebb eső régióban, a lehető leghatékonyabban, a biztonsági és megfelelőségi igényeknek is megfelelően.

## <a name="integration-runtime-types"></a>Integrációsmodul-típusok

A Data Factory háromféle integrációs futásidejű (IR) típust kínál, és ki kell választania azt a típust, amely a legjobban szolgálja a keresett adatintegrációs képességeket és hálózati környezetigényeket.  A három típus a következő:

- Azure
- Saját üzemeltetésű
- Azure SSIS

Az alábbi táblázat ismerteti az integrációs modulok egyes típusainak képességeit és hálózati támogatását:

Integrációs modul típusa | Nyilvános hálózat | Magánhálózat
------- | -------------- | ---------------
Azure | Adatfolyam<br/>Adatáthelyezés<br/>Tevékenység küldése | &nbsp;
Saját üzemeltetésű | Adatáthelyezés<br/>Tevékenység küldése | Adatáthelyezés<br/>Tevékenység küldése
Azure SSIS | SSIS-csomag végrehajtása | SSIS-csomag végrehajtása

Az alábbi diagram bemutatja, hogyan használhatók a különböző integrációs modulok kombinálva, hogy változatos adatintegrációs képességeket és hálózati támogatást nyújtsanak:

![Különböző típusú integrációs modulok](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul

Az Azure-integrációs futásidejű ek a következőket tehetik:

- Adatfolyamok futtatása az Azure-ban 
- Másolási tevékenység futtatása felhőalapú adattárak között
- A következő átalakítási tevékenységek feladása nyilvános hálózatban: Databricks Notebook/ Jar/ Python tevékenység, HDInsight Hive tevékenység, HDInsight Pig tevékenység, HDInsight MapReduce tevékenység, HDInsight Spark-tevékenység, HDInsight streamelési tevékenység, gépi tanulási kötegelt végrehajtási tevékenység, gépi tanulási frissítési erőforrás-tevékenységek, tárolt eljárás tevékenység, Data Lake Analytics U-SQL tevékenység, .NET egyéni tevékenység, webes tevékenység, keresése és metaadat-tevékenység beküldése.

### <a name="azure-ir-network-environment"></a>Azure-beli integrációs modul hálózati környezete

Az Azure Integration Runtime támogatja az adattárakhoz és számítási szolgáltatásokhoz való csatlakozást nyilvánosan elérhető végpontokkal. Használhat saját üzemeltetésű integrációs modult az Azure Virtual Network-környezethez.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure-beli integrációs modul számítási erőforrásai és skálázása
Az Azure-beli integrációs modul teljesen felügyelt, kiszolgáló nélküli számítást biztosít az Azure-ban.  Nem kell aggódnia az infrastruktúra kiépítése, a szoftvertelepítése, a javítás vagy a kapacitásméretezés miatt.  Ráadásul csak a tényleges használat időtartamára fizet.

Az Azure-beli integrációs modul biztosítja a natív számítást az adatok felhőalapú adattárak közötti biztonságos, megbízható és nagy teljesítményű módon való mozgatásához.  Beállíthatja a másolási tevékenységhez használni kívánt adatintegrálási egységek mennyiségét, és az Azure IR számítási mérete rugalmasan felskálázható vertikálisan ennek megfelelően anélkül, hogy explicit módon módosítania kellene az Azure-beli integrációs modul méretét. 

A tevékenység feladása egy könnyű művelet a tevékenység a cél számítási szolgáltatáshoz való irányításához, így ebben a forgatókönyvben nem kell bővíteni a számítási méretet.

Az Azure IR létrehozásáról és konfigurálásáról az Azure IR-t útmutatók alatt való létrehozása és konfigurálása című témakörben talál. 

> [!NOTE] 
> Az Azure Integration futásidejű az adatfolyam-futásidejű tulajdonságokkal rendelkezik, amely meghatározza az alapul szolgáló számítási infrastruktúra, amely az adatfolyamok futtatásához használható. 

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul

Egy saját üzemeltetésű IR a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak és egy magánhálózaton lévő adattár között.
- A következő átalakítási tevékenységek feladása a helyszíni vagy azure-beli virtuális hálózat számítási erőforrásaiellen: HDInsight Hive tevékenység (BYOC-Hozd a saját fürtje), HDInsight Pig tevékenység (BYOC), HDInsight MapReduce tevékenység (BYOC), HDInsight Spark tevékenység (BYOC), HDInsight streamelési tevékenység (BYOC), Gépi tanulási kötegelt végrehajtás tevékenység, gépi tanulási frissítési erőforrás-tevékenységek, tárolt eljárástevékenység, Data Lake Analytics U-SQL tevékenység, Egyéni tevékenység (Azure Batch) futtatása , A hirdetési tevékenység és a Metaadat-tevékenység becsatornázása.

> [!NOTE] 
> Használja a saját üzemeltetésű integrációs futásidejű, hogy támogassa az adattárak, amelyek megkövetelik bring-your-own driver, például az SAP Hana, MySQL, stb.  További információt a [támogatott adattárak című témakörben talál.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!NOTE] 
> A Java runtime-környezet (JRE) a self hosted IR függősége. Ellenőrizze, hogy a JRE telepítve van-e ugyanarra a gazdagépre.

### <a name="self-hosted-ir-network-environment"></a>Saját üzemeltetésű integrációs modul hálózati környezete

Ha az adatintegrációt biztonságosan szeretné végrehajtani egy magánhálózati környezetben, amely nem rendelkezik közvetlen rálátással a nyilvános felhőkörnyezetből, telepíthet egy saját üzemeltetésű infravörös integrációt a vállalati tűzfal mögötti helyszíni környezetben vagy egy virtuális magánhálózaton belül.  A saját üzemeltetésű integrációs modul csak kimenő HTTP-alapú kapcsolatokat hoz létre az internet megnyitásához.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Saját üzemeltetésű integrációs modul erőforrásai és skálázása

Telepítse a saját üzemeltetésű infravörös telepítését egy helyszíni gépen vagy egy magánhálózaton belüli virtuális gépen. Jelenleg csak Windows operációs rendszeren támogatjuk a saját üzemeltetésű integrációs modulok futtatását.  

A magas rendelkezésre állás és a méretezhetőség érdekében horizontálisan felskálázhatja saját üzemeltetésű integrációs modulját, ha több helyszíni géppel aktív-aktív módban társít hozzá egy logikai példányt.  További információkért tekintse meg, hogyan [hozhat létre és konfigurálhat saját üzemeltetésű infravörös](create-self-hosted-integration-runtime.md) cikket a részletekre vonatkozó útmutatók alatt.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS integrációs modul

A meglévő SSIS számítási feladat átemeléséhez létrehozhat egy Azure-SSIS integrációs modult az SSIS-csomagok natív létrehozásához.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS integrációs modul hálózati környezete

Az Azure-SSIS integrációs modul kiépíthető nyilvános hálózaton vagy magánhálózaton is.  A helyszíni adathozzáférés támogatása az Azure-SSIS integrációs modulnak a helyszíni hálózathoz csatlakoztatott virtuális hálózattal való összekapcsolása révén történik.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS integrációs modul számítási erőforrásai és skálázása

Az Azure-SSIS integrációs modul egy, az SSIS-csomagok futtatására dedikált Azure-beli virtuális gépekből álló teljesen felügyelt fürt. Saját Azure SQL-adatbázist vagy felügyelt példánykiszolgálót hozhat létre az SSIS-projektek/csomagok (SSISDB) katalógusának üzemeltetéséhez, amely hozzá lesz csatolva. A számítási teljesítmény vertikális felskálázásához adjon meg egy csomópontméretet, és skálázza fel horizontálisan a fürt csomópontszámának megadásával. Az Azure-SSIS integrációs modul futtatási költségének kezeléséhez igény szerint leállíthatja és elindíthatja azt.

További információkért tekintse meg az útmutatók között az Azure-SSIS integrációs modul létrehozását és konfigurálását ismertető cikket.  Létrehozásukat követően kevés vagy szinte semmilyen módosítással, a helyszíni SSIS-csomagokkal megegyező módon üzembe helyezheti és kezelheti meglévő SSIS-csomagjait az olyan jól ismert eszközök használatával, mint az SQL Server Data Tools (SSDT) és az SQL Server Management Studio (SSMS).

Az Azure-SSIS integrációs modullal kapcsolatos további információkért tekintse át a következő cikkeket: 

- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenkénti útmutatást nyújt az Azure-SSIS ir létrehozásához, és egy Azure SQL-adatbázist használ az SSIS-katalógus üzemeltetéséhez. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk kibontja az oktatóanyagot, és útmutatást nyújt az Azure SQL Database felügyelt példány használatával kapcsolatban, és csatlakozik az infravörös egy virtuális hálózathoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 

## <a name="integration-runtime-location"></a>Az integrációs modul helye

A Data Factory ott található, ahol az adat-előállító metaadatait tárolja a rendszer, és ahonnan a folyamat aktiválása indul. Az adat-előállító ilyenkor más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti áthelyezése vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. Ez a viselkedés az adatok megfelelősége, a hatékonyság és a hálózati kimeneti forgalmának alacsonyabb költségei érdekében a [globálisan elérhető integrációs modulon](https://azure.microsoft.com/global-infrastructure/services/) keresztül valósul meg.

Az integrációs modul helye meghatározza a háttérszámítások helyét és azt a helyet, ahol az adatok áthelyezése, a tevékenységküldés és az SSIS-csomag végrehajtása történik. Az integrációs modul helye eltérhet annak az adat-előállítónak a helyétől, amelyhez tartozik. 

### <a name="azure-ir-location"></a>Az Azure-beli integrációs modul helye

- Másolási tevékenység esetén az ADF mindent megtesz annak érdekében, hogy automatikusan észlelje a fogadó adattárának helyét, majd használja az infravörös modult ugyanabban a régióban, ha rendelkezésre áll, vagy a legközelebbit ugyanabban a földrajzi helyen; ha a fogadó adattár régiója nem észlelhető, az infravörös kapcsolat az adatgyári régióban alternatívként használatos.

  Például, ha a gyár létre az USA keleti részén, 
  
  - Amikor adatokat másol az Azure Blobba az USA nyugati részén, ha az ADF sikeresen észlelte, hogy a Blob az USA nyugati részén található, a másolási tevékenység végrehajtása az USA nyugati részén történik; ha a régió észlelése sikertelen, a másolási tevékenység végrehajtása az USA keleti régiójában történik.
  - Ha olyan Salesforce-ba másolja az adatokat, amelyek régiója nem észlelhető, a másolási tevékenység az USA keleti részén történik az infravörös kapcsolaton.

- Másolási tevékenység esetén az ADF mindent megtesz annak érdekében, hogy automatikusan észlelje a fogadó és a forrásadattár számára a legjobb hely kiválasztását, akár ugyanabban a régióban (ha elérhető), akár az azonos földrajzi területen lévő legközelebbi helyen, vagy ha nem észlelhető az adat-előállító régió alternatívaként való használatához.

- A keresés/GetMetadata/Tevékenység törlése (más néven csővezeték-tevékenységek), átalakítási tevékenység feladása (más néven külső tevékenységek) és a szerzői műveletek (tesztkapcsolat, tallózás mappalista és táblalista, előnézeti adatok) esetén az ADF az infravörös kapcsolatot használja az adatgyári régióban.

- Az adatfolyam esetében az ADF az infravörös megoldást használja az adatgyári régióban. 

  > [!TIP] 
  > Egy jó gyakorlat az lenne, hogy az adatfolyam fut ugyanabban a régióban, mint a megfelelő adattárak (ha lehetséges). Ezt az Azure IR automatikus feloldásával (ha az adattár helye megegyezik a Data Factory helyével), vagy hozzon létre egy új Azure IR-példányt ugyanabban a régióban, mint az adattárak, majd hajtsa végre az adatfolyamok. 

A folyamattevékenységek monitorozása nézetben a felhasználói felületen vagy a tevékenységfigyelés hasznos adatainál figyelemmel követheti, melyik integrációs modul lép életbe a tevékenység végrehajtása során.

### <a name="self-hosted-ir-location"></a>A saját üzemeltetésű integrációs modul helye

A saját üzemeltetésű integrációs modul logikailag van regisztrálva a Data Factoryban, és a funkciók támogatásához használt számítást meg kell adnia. Ezért a saját üzemeltetésű integrációs modulok esetében nincs explicit hely tulajdonság. 

Ha adatmozgás végrehajtásához használja, a saját üzemeltetésű integrációs modul kinyeri az adatokat a forrásból, és a célra írja.

### <a name="azure-ssis-ir-location"></a>Az Azure-SSIS integrációs modul helye

Az Azure-SSIS integrációs modul számára a megfelelő helyet kiválasztása az ETL folyamatokban létfontosságú a magas teljesítmény eléréséhez.

- Az Azure-SSIS IR helye nem kell megegyeznie az adat-előállító helyével, de meg kell egyeznie a saját Azure SQL Database vagy felügyelt példány kiszolgálójának helyével, ahol az SSISDB-t üzemeltetni kell. Ily módon az Azure-SSIS integrációs modul könnyen hozzáfér az SSISDB-hez anélkül, hogy jelentős adatforgalom zajlana különböző helyek között.
- Ha nem rendelkezik egy meglévő Azure SQL Database vagy felügyelt példány kiszolgáló ssisdb üzemeltetéséhez, de rendelkezik a helyszíni adatforrások/célállomások, hozzon létre egy új Azure SQL-adatbázis vagy felügyelt példány kiszolgáló ugyanazon a helyen egy virtuális hálózat csatlakozik a helyszíni hálózathoz.  Ily módon létrehozhatja az Azure-SSIS IR-t az új Azure SQL Database vagy felügyelt példánykiszolgáló használatával, és csatlakozhat a virtuális hálózathoz , mindezt ugyanazon a helyen, hatékonyan minimalizálva a különböző helyek közötti adatmozgásokat.
- Ha a meglévő Azure SQL-adatbázis vagy felügyelt példány kiszolgáló, ahol SSISDB üzemelteti nem ugyanaz, mint a helyét a virtuális hálózat csatlakozik a helyszíni hálózathoz, először hozza létre az Azure-SSIS IR egy meglévő Azure SQL-adatbázis vagy felügyelt példány kiszolgáló és csatlakozott egy másik virtuális hálózat ugyanazon a helyen, majd konfigurálja a virtuális hálózat virtuális hálózati kapcsolat a különböző helyek között.

Az alábbi ábrán a Data Factory és a hozzá tartozó integrációs modul beállításai találhatók:

![Az integrációs modul helye](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>A használandó integrációs modul meghatározása

### <a name="copy-activity"></a>Másolási tevékenység

A másolási tevékenységhez szükséges egy forrás és fogadó társított szolgáltatás az adatfolyam irányának meghatározására. A rendszer az alábbi logikával határozza meg, melyik integrációsmodell-példányt használja a másolás végrehajtásához: 

- **Két felhőalapú adatforrás okai közötti másolás:** ha a forrás- és a fogadóhoz kapcsolódó szolgáltatások is Azure IR-t használnak, az ADF a regionális Azure IR-t használja, ha megadott, vagy automatikusan meghatározza az Azure IR helyét, ha az automatikus feloldási infravörös (alapértelmezett) helyet választja az [Integrációs futásidejű hely](#integration-runtime-location) szakaszban leírtak szerint.
- **Egy felhőalapú adatforrás és egy magánhálózaton lévő adatforrás közötti másolás**: ha a forrás vagy a fogadó társított szolgáltatása egy saját üzemeltetésű integrációs modulra mutat, a rendszer azon a saját üzemeltetésű integrációs modulon hajtja végre a másolási tevékenységet.
- **Másolás két adatforrás között a magánhálózaton:** mind a forrás, mind a fogadó csatolt szolgáltatásnak ugyanarra az integrációs futásidőre kell mutatnia, és az integrációs futásidejűnek a másolási tevékenység végrehajtásához kell használnia.

### <a name="lookup-and-getmetadata-activity"></a>Keresési és metaadat-beolvasási tevékenység

A keresési és metaadat-beolvasási tevékenységet a rendszer az adattár társított szolgáltatásához rendelt integrációs modulon hajtja végre.

### <a name="external-transformation-activity"></a>Külső átalakítási tevékenység

Minden külső átalakítási tevékenység, amely egy külső számítási motor thasználja a cél számítási csatolt szolgáltatás, amely egy integrációs futásidejű. Ez az integrációs futásidejű példány határozza meg azt a helyet, ahonnan a külső, kézzel kódolt átalakítási tevékenység et feladják.

### <a name="data-flow-activity"></a>Adatfolyam-tevékenység

Az adatfolyam-tevékenységek a hozzá társított Azure-integrációs futásidőben kerülnek végrehajtásra. Az adatfolyamok által használt Spark-számítási adatokat az Azure-integrációs futásidejű adatfolyam-tulajdonságok határozzák meg, és az ADF teljes körűen kezeli.

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Azure-integrációs futásidejű létrehozása](create-azure-integration-runtime.md)
- [Saját üzemeltetésű integrációs futásidejű létrehozása](create-self-hosted-integration-runtime.md)
- [Hozzon létre egy Azure-SSIS-integrációs futásidejűt.](create-azure-ssis-integration-runtime.md) Ez a cikk kibontja az oktatóanyagot, és útmutatást nyújt az Azure SQL Database felügyelt példány használatával kapcsolatban, és csatlakozik az infravörös egy virtuális hálózathoz. 
