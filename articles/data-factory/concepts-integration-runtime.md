---
title: Integrációs modul
description: Ismerje meg az integrációs modult az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 194bc7983019a616d534a4146f86fff59f9719dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990521"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integrációs modul az Azure Data Factoryban
Az integrációs modul (Integration Runtime, IR), az Azure Data Factory által használt számítási infrastruktúra a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatfolyam**: [adatfolyamatok](concepts-data-flow-overview.md) végrehajtása felügyelt Azure számítási környezetben.  
- **Adatáthelyezés**: a nyilvános hálózatban lévő adattárakban és a magánhálózaton (helyszíni vagy virtuális magánhálózat) lévő adattárakban tárolt Adatmásolás. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenység elküldése**: különböző számítási szolgáltatásokon, például az Azure Databrickson, az Azure HDInsight, a Azure Machine Learningon, a Azure SQL Databaseon, a SQL Serveron és egyebeken futó átalakítási tevékenységek elküldése és monitorozása.
- **SSIS-csomag végrehajtása**: Natívan végrehajthat SQL Server Integration Services- (SSIS-) csomagokat egy Azure-beli felügyelt számítási környezetben.

A Data Factoryban a végrehajtandó műveletet egy tevékenység határozza meg. A társított szolgáltatások a céladattárat vagy a számítási szolgáltatást határozzák meg. Az integrációs modulok hídként szolgálnak a tevékenység és a társított szolgáltatások között.  A társított szolgáltatás vagy tevékenység hivatkozik rá, és megadja azt a számítási környezetet, ahol a tevékenység vagy a szolgáltatás fut, vagy amelyről elküldi a szolgáltatást. Ily módon a tevékenység végrehajtható a céladattárhoz vagy számítási szolgáltatáshoz lehető legközelebb eső régióban, a lehető leghatékonyabban, a biztonsági és megfelelőségi igényeknek is megfelelően.

## <a name="integration-runtime-types"></a>Integrációsmodul-típusok
A Data Factory három típusú integrációs modult ajánl, és ki kell választania azt a típust, amely a leginkább megfelel az adatintegrációsképesség- és hálózatikörnyezet-igényeinek.  A három típus a következő:

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

## <a name="azure-integration-runtime"></a>Azure integrációs modul
Egy Azure-beli integrációs modul a következőkre képes:

- Adatfolyamatok futtatása az Azure-ban 
- Másolási tevékenység futtatása felhőalapú adattárak között
- A következő átalakítási tevékenységek elküldése a nyilvános hálózatban: Databricks notebook/jar/Python tevékenység, HDInsight struktúra tevékenység, HDInsight Pig tevékenység, HDInsight MapReduce tevékenység, HDInsight Spark-tevékenység, HDInsight streaming Activity, Machine Learning kötegelt végrehajtási tevékenység, Machine Learning frissítési erőforrás tevékenység, tárolt eljárás tevékenység, Data Lake Analytics U-SQL tevékenység, .NET egyéni tevékenység, webes tevékenység, keresési tevékenység és metaadatok beolvasása tevékenység.

### <a name="azure-ir-network-environment"></a>Azure-beli integrációs modul hálózati környezete
Azure Integration Runtime támogatja az adattárakhoz és a számítási szolgáltatásokhoz való csatlakozást nyilvános elérhető végpontokkal. Használhat saját üzemeltetésű integrációs modult az Azure Virtual Network-környezethez.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure-beli integrációs modul számítási erőforrásai és skálázása
Az Azure-beli integrációs modul teljesen felügyelt, kiszolgáló nélküli számítást biztosít az Azure-ban.  Nem kell foglalkoznia infrastruktúra létesítésével, szoftver telepítésével, frissítéssel vagy a kapacitás méretezésével.  Ráadásul csak a tényleges használat időtartamára fizet.

Az Azure-beli integrációs modul biztosítja a natív számítást az adatok felhőalapú adattárak közötti biztonságos, megbízható és nagy teljesítményű módon való mozgatásához.  Beállíthatja a másolási tevékenységhez használni kívánt adatintegrálási egységek mennyiségét, és az Azure IR számítási mérete rugalmasan felskálázható vertikálisan ennek megfelelően anélkül, hogy explicit módon módosítania kellene az Azure-beli integrációs modul méretét. 

A tevékenységküldés egy könnyen használható művelet a tevékenységnek a cél számítási szolgáltatásba való irányítására, így ehhez a forgatókönyvhöz nincs szükség a számítási méret vertikális felskálázására.

Az Azure integrációs modulok létrehozásáról és konfigurálásáról az Azure IR létrehozása és konfigurálása útmutatóban találhat információkat. 

> [!NOTE] 
> Az Azure Integration Runtime az adatfolyam-futtatókörnyezethez kapcsolódó tulajdonságokkal rendelkezik, amely meghatározza a mögöttes számítási infrastruktúrát, amely az adatfolyamatok futtatására szolgál. 

## <a name="self-hosted-integration-runtime"></a>Helyi integrációs modul
Egy saját üzemeltetésű IR a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak és egy magánhálózaton lévő adattár között.
- A következő átalakítási tevékenységek elküldése a helyszíni vagy az Azure-beli számítási erőforrásokkal szemben Virtual Network: HDInsight-struktúra tevékenysége (BYOC – saját fürt használata), HDInsight Pig-tevékenység (BYOC), HDInsight MapReduce tevékenység (BYOC), HDInsight Spark tevékenység (BYOC), HDInsight streaming Activity (BYOC), Machine Learning kötegelt végrehajtási tevékenység, Machine Learning erőforrás-tevékenységek frissítése, tárolt eljárási tevékenység, Data Lake Analytics U-SQL tevékenység, egyéni tevékenység (Azure Batch), keresés tevékenység és metaadatok beolvasása tevékenység.

> [!NOTE] 
> A saját üzemeltetésű integrációs modult használva olyan adattárakat támogathat, amelyek a saját illesztőprogramot, például az SAP Hana-t vagy a MySQL-t igénylik.  További információ: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> A Java Runtime Environment (JRE) a saját üzemeltetésű IR függősége. Győződjön meg arról, hogy a JRE ugyanarra a gazdagépre van telepítve.

### <a name="self-hosted-ir-network-environment"></a>Saját üzemeltetésű integrációs modul hálózati környezete
Ha biztonságosan szeretne adatintegrációt végezni egy nyilvános felhőkörnyezetből közvetlenül nem látható magánhálózati környezetben, telepíthet egy saját üzemeltetésű integrációs modult helyszíni környezeten a vállalati tűzfal mögé vagy a virtuális magánhálózaton belülre.  A saját üzemeltetésű integrációs modul csak kimenő HTTP-alapú kapcsolatokat hoz létre az internet megnyitásához.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Saját üzemeltetésű integrációs modul erőforrásai és skálázása
A saját üzemeltetésű integrációs modult helyszíni gépre vagy magánhálózaton belüli virtuális gépre kell telepíteni. Jelenleg csak Windows operációs rendszeren támogatjuk a saját üzemeltetésű integrációs modulok futtatását.  

A magas rendelkezésre állás és a méretezhetőség érdekében horizontálisan felskálázhatja saját üzemeltetésű integrációs modulját, ha több helyszíni géppel aktív-aktív módban társít hozzá egy logikai példányt.  További információ: a saját üzemeltetésű integrációs modul [létrehozása és konfigurálása](create-self-hosted-integration-runtime.md) című cikk, útmutató a részletekhez.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS integrációs modul
A meglévő SSIS számítási feladat átemeléséhez létrehozhat egy Azure-SSIS integrációs modult az SSIS-csomagok natív létrehozásához.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS integrációs modul hálózati környezete
Az Azure-SSIS integrációs modul kiépíthető nyilvános hálózaton vagy magánhálózaton is.  A helyszíni adathozzáférés támogatása az Azure-SSIS integrációs modulnak a helyszíni hálózathoz csatlakoztatott virtuális hálózattal való összekapcsolása révén történik.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS integrációs modul számítási erőforrásai és skálázása
Az Azure-SSIS integrációs modul egy, az SSIS-csomagok futtatására dedikált Azure-beli virtuális gépekből álló teljesen felügyelt fürt. Saját Azure SQL Database vagy felügyelt példány-kiszolgálót is használhat, amely a hozzá tartozó SSIS-projektek/csomagok (SSISDB) katalógusát futtatja. A számítási teljesítmény vertikális felskálázásához adjon meg egy csomópontméretet, és skálázza fel horizontálisan a fürt csomópontszámának megadásával. Az Azure-SSIS integrációs modul futtatási költségének kezeléséhez igény szerint leállíthatja és elindíthatja azt.

További információkért tekintse meg az útmutatók között az Azure-SSIS integrációs modul létrehozását és konfigurálását ismertető cikket.  Létrehozásukat követően kevés vagy szinte semmilyen módosítással, a helyszíni SSIS-csomagokkal megegyező módon üzembe helyezheti és kezelheti meglévő SSIS-csomagjait az olyan jól ismert eszközök használatával, mint az SQL Server Data Tools (SSDT) és az SQL Server Management Studio (SSMS).

Az Azure-SSIS integrációs modullal kapcsolatos további információkért tekintse át a következő cikkeket: 

- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](tutorial-create-azure-ssis-runtime-portal.md). Ez a cikk lépésenként mutatja be egy Azure-SSIS integrációs modul létrehozását, és egy Azure SQL-adatbázist használ az SSIS-katalógus futtatására. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt Azure SQL Database felügyelt példányának használatáról és az IR virtuális hálózathoz való csatlakoztatásáról. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 

## <a name="integration-runtime-location"></a>Az integrációs modul helye
A Data Factory ott található, ahol az adat-előállító metaadatait tárolja a rendszer, és ahonnan a folyamat aktiválása indul. Az adat-előállító ilyenkor más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti áthelyezése vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. Ez a viselkedés az adatok megfelelősége, a hatékonyság és a hálózati kimeneti forgalmának alacsonyabb költségei érdekében a [globálisan elérhető integrációs modulon](https://azure.microsoft.com/global-infrastructure/services/) keresztül valósul meg.

Az integrációs modul helye meghatározza a háttérszámítások helyét és azt a helyet, ahol az adatok áthelyezése, a tevékenységküldés és az SSIS-csomag végrehajtása történik. Az integrációs modul helye eltérhet annak az adat-előállítónak a helyétől, amelyhez tartozik. 

### <a name="azure-ir-location"></a>Az Azure-beli integrációs modul helye
Beállíthat egy adott helyet az Azure-beli integrációs modul számára. Ebben esetben az adatok áthelyezése vagy a tevékenységküldés abban a régióban történik. 

Ha úgy dönt, hogy az **automatikus feloldási Azure IR** használja, amely az alapértelmezett, 

- A másolási tevékenység során az ADF mindent megtesz, hogy automatikusan észlelje a fogadó- és forrásadattárat, kiválassza ugyanabban a régióban (ha lehetséges) vagy a legközelebbi azonos földrajzi helyen a legjobb helyet, vagy helyettük az adat-előállító régiót használja, ha ezek nem észlelhetőek.

- A keresési/GetMetadata/törlési tevékenységek végrehajtásához (más néven folyamathoz), az átalakítási tevékenység elküldéséhez (más néven külső tevékenységekhez) és a szerzői műveletekhez (a kapcsolat tesztelése, a mappák listájának és a táblázatok listájának megtekintése, az előzetes verzióra vonatkozó adatok), az ADF az IR-t fogja használni az adatfeldolgozó régióban.

- Adatforgalom esetén az ADF az IR-t fogja használni az adatfeldolgozó régióban. 

  > [!TIP] 
  > Jó gyakorlat lenne annak biztosítása, hogy az adatforgalom ugyanabban a régióban fusson, mint a megfelelő adattárak (ha lehetséges). Ezt megteheti a Azure IR automatikus feloldásával (ha az adattár helye megegyezik a Data Factory helyével), vagy egy új Azure IR példány létrehozásával ugyanabban a régióban, ahol az adattár található, majd végrehajtja az adatfolyamot. 

A folyamattevékenységek monitorozása nézetben a felhasználói felületen vagy a tevékenységfigyelés hasznos adatainál figyelemmel követheti, melyik integrációs modul lép életbe a tevékenység végrehajtása során.

>[!TIP]
>Ha adatmegfelelőségi követelmények szigorúak, és biztosítania kell, hogy az adatok nem hagynak el egy adott földrajzi helyet, az adott régióban létrehozhat egy Azure-beli integrációs modult, és a ConnectVia tulajdonság használatával ehhez irányíthatja a társított szolgáltatást. Ha például adatokat szeretne másolni az Egyesült Királyság déli régióján belüli blobból az Egyesült Királyság déli régióján belüli SQL DW-be, és biztos szeretne lenni abban, hogy az adatok nem hagyják el az Egyesült Királyságot, hozzon létre egy Azure-beli integrációs modult az Egyesült Királyság déli régiójában, és társítson minden társított szolgáltatást ehhez a modulhoz.

### <a name="self-hosted-ir-location"></a>A saját üzemeltetésű integrációs modul helye
A saját üzemeltetésű integrációs modul logikailag van regisztrálva a Data Factoryban, és a funkciók támogatásához használt számítást meg kell adnia. Ezért a saját üzemeltetésű integrációs modulok esetében nincs explicit hely tulajdonság. 

Ha adatmozgás végrehajtásához használja, a saját üzemeltetésű integrációs modul kinyeri az adatokat a forrásból, és a célra írja.

### <a name="azure-ssis-ir-location"></a>Az Azure-SSIS integrációs modul helye
Az Azure-SSIS integrációs modul számára a megfelelő helyet kiválasztása az ETL folyamatokban létfontosságú a magas teljesítmény eléréséhez.

- A Azure-SSIS IR helyének nem kell megegyeznie a saját adatgyárának helyével, de meg kell egyeznie a saját Azure SQL Database vagy a felügyelt példány-kiszolgáló helyével, ahol a SSISDB üzemeltetve van. Ily módon az Azure-SSIS integrációs modul könnyen hozzáfér az SSISDB-hez anélkül, hogy jelentős adatforgalom zajlana különböző helyek között.
- Ha nem rendelkezik meglévő Azure SQL Database vagy felügyelt példány-kiszolgálóval a SSISDB üzemeltetéséhez, de helyszíni adatforrásokkal/célhelyekkel rendelkezik, hozzon létre egy új Azure SQL Database vagy felügyelt kiszolgálópéldány-kiszolgálót a helyszíni hálózathoz csatlakoztatott virtuális hálózattal azonos helyen.  Így a Azure-SSIS IR az új Azure SQL Database vagy a felügyelt példány-kiszolgáló használatával hozhatja létre, és az ugyanazon a helyen található virtuális hálózathoz csatlakozik, ami gyakorlatilag minimalizálja a különböző helyek közötti adatáthelyezést.
- Ha a meglévő Azure SQL Database vagy felügyelt példány-kiszolgáló helye, ahol a SSISDB fut, nem egyezik meg a helyszíni hálózathoz csatlakoztatott virtuális hálózat helyével, először hozza létre a Azure-SSIS IR egy meglévő Azure SQL Database használatával, vagy Felügyelt példány-kiszolgáló és egy másik virtuális hálózat összekapcsolása ugyanazon a helyen, majd a virtuális hálózat beállítása a különböző helyek közötti virtuális hálózati kapcsolatra.

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

### <a name="external-transformation-activity"></a>Külső átalakítási tevékenység

A külső számítási motort használó minden külső átalakítási tevékenységhez tartozik egy cél számítási társított szolgáltatás, amely egy integrációs modulra mutat. Ez az Integration Runtime-példány meghatározza azt a helyet, ahol a külső, kézzel kódolt átalakítási tevékenység elküldése megtörténik.

### <a name="data-flow-activity"></a>Adatfolyam-tevékenység

Az adatfolyam-tevékenységek végrehajtása a hozzá társított Azure Integration Runtime-on történik. Az adatfolyamatok által használt Spark-számításokat a Azure Integration Runtime adatáramlási tulajdonságai határozzák meg, és teljes mértékben az ADF kezelik.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Azure Integration Runtime létrehozása](create-azure-integration-runtime.md)
- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt Azure SQL Database felügyelt példányának használatáról és az IR virtuális hálózathoz való csatlakoztatásáról. 
