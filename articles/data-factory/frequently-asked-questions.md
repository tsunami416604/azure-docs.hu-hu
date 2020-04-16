---
title: 'Azure Data Factory: gyakori kérdések '
description: Válaszok az Azure Data Factoryval kapcsolatos gyakori kérdésekre.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412942"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory – gyakori kérdések

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk választ ad az Azure Data Factoryval kapcsolatos gyakori kérdésekre.  

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
A Data Factory egy teljes körűen felügyelt, felhőalapú, adatintegrációs ETL szolgáltatás, amely automatizálja az adatok mozgását és átalakítását. Az Azure Data Factory a nyersanyagok késztermékekké alakításához szükséges berendezéseket működtető gyárhoz hasonlóan olyan meglévő szolgáltatásokat vezényli, amelyek nyers adatokat gyűjtenek, és használatra kész információkká alakítják át azokat. 

Az Azure Data Factory használatával adatalapú munkafolyamatokat hozhat létre az adatok helyszíni és felhőbeli adattárak közötti áthelyezéséhez. Az adatokat pedig feldolgozhatja és átalakíthatja az adatfolyamokkal. Az ADF támogatja a kézzel kódolt átalakítások külső számítási motorjait is olyan számítási szolgáltatások használatával, mint az Azure HDInsight, az Azure Databricks és az SQL Server Integration Services (SSIS) integrációs futásidejű. 

A Data Factory segítségével az adatfeldolgozást azure-alapú felhőszolgáltatáson vagy saját saját üzemeltetésű számítási környezetben, például SSIS, SQL Server vagy Oracle környezetben hajthatja végre. Miután létrehozott egy folyamatot, amely végrehajtja a szükséges műveletet, ütemezheti, hogy rendszeresen (óránkénti, napi vagy heti, például), időablak-ütemezés, vagy indítsa el a folyamatot egy esemény előfordulása. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

### <a name="control-flows-and-scale"></a>Ellenőrzési folyamatok és lépték 
A modern adatraktárban a különböző integrációs folyamatok és minták támogatása érdekében a Data Factory rugalmas adatfolyamat-modellezést tesz lehetővé. Ez magában foglalja a teljes vezérlési folyamat programozási paradigmák, amelyek magukban foglalják a feltételes végrehajtás, elágazás az adatfolyamatokban, és a képesség, hogy explicit módon továbbítja a paramétereket belül és azok között a folyamatok. A vezérlési folyamat magában foglalja az adatok átalakítását a tevékenység feladásával a külső végrehajtási motorokra és az adatáramlási képességekkel, beleértve a nagy méretű adatmozgást is a Másolás tevékenységen keresztül.

A Data Factory lehetővé teszi az adatintegrációhoz szükséges és igény szerint vagy többször ütemezés szerint elküldhető folyamatstílus modellezését. Néhány gyakori folyamatok, amelyek ezt a modellt lehetővé teszi a következők:   

- Ellenőrzési folyamatok:
    - A tevékenységek egy folyamaton belüli sorrendben láncolhatók össze.
    - A tevékenységek elágazásúak egy folyamaton belül.
    - Paraméterek:
        - A paraméterek a folyamat szintjén definiálhatók, és argumentumok adhatók meg a folyamat igény szerinti vagy eseményindítóból történő meghívása közben.
        - A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására.
    - Egyéni állapot átadása:
        - A tevékenységkimeneteket, beleértve az állapotot is, a folyamat egy későbbi tevékenysége is felhasználhatja.
    - Hurkolt konténerek:
        - A foreach tevékenység egy adott tevékenységgyűjteményt egy adott ciklusban fog megismétlődni. 
- Eseményindító-alapú folyamatok:
    - A folyamatok igény szerint vagy faliidő-idő szerint is aktiválhatók.
- Delta-áramlások:
    - A paraméterek segítségével meghatározhatja a különbözeti másolás magas vízjelét, miközben dimenzió- vagy referenciatáblákat mozgat egy relációs tárolóból, akár a helyszínen, akár a felhőben, az adatok tóba való betöltéséhez. 

További információt az [Oktatóanyag: Folyamatok vezérlése című témakörben talál.](tutorial-control-flow.md)

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Nagy méretekben átalakított adatok kódmentes folyamatokkal
Az új böngészőalapú eszközkészítési élmény korszerű, interaktív webalapú élményt nyújt a kódmentes folyamatkészítéshez és üzembe helyezéshez.

A vizuális adatfejlesztők és adatmérnökök számára a Data Factory webes felhasználói felülete a folyamatok létrehozásához használt kódmentes tervezési környezet. Teljesen integrálva van a Visual Studio Online Git-tel, és integrációt biztosít a CI/CD és az iteratív fejlesztéshez hibakeresési lehetőségekkel.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Gazdag platformfüggetlen SDK-k haladó felhasználók számára
A Data Factory V2 sdk-k gazdag készletét biztosítja, amelyek a folyamatok szerkesztésére, kezelésére és figyelésére használhatók a kedvenc IDE használatával, beleértve a következőket:
* Python SDK
* PowerShell CLI
* C# SDK

A felhasználók a dokumentált REST API-k segítségével is érintkezhetnek a Data Factory V2-vel.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteratív fejlesztés és hibakeresés vizuális eszközök használatával
Az Azure Data Factory vizuális eszközei iteratív fejlesztést és hibakeresést tesznek lehetővé. Létrehozhatja a folyamatokat, és tesztfuttatásokat tehet a **folyamatvásznon** a Debug funkció használatával, egyetlen kódsor írása nélkül. A tesztfuttatások eredményeit a folyamatvászon **Kimenet ablakában** tekintheti meg. Miután a tesztfuttatás sikeres, további tevékenységeket adhat hozzá a folyamathoz, és iteratív módon folytathatja a hibakeresést. A tesztelési futtatásokat a folyamat után is megszakíthatja. 

A Hibakeresés lehetőség kiválasztása előtt nem kell közzétennie a módosításokat az adatgyári **szolgáltatásban.** Ez olyan esetekben hasznos, amikor meg szeretne győződni arról, hogy az új kiegészítések vagy módosítások a várt módon fognak működni, mielőtt az adat-előállító munkafolyamatokat fejlesztési, tesztelési vagy éles környezetekben frissítené. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>SSIS-csomagok azure-beli üzembe helyezésének lehetősége 
Ha át szeretné helyezni az SSIS-számítási feladatokat, létrehozhat egy Data Factory-t, és létrehozhat egy Azure-SSIS-integrációs futásidőt. Az Azure-SSIS-integrációs futásidejű az Azure virtuális gépek (csomópontok) teljes körűen felügyelt fürtje, amelyek az SSIS-csomagok felhőbeli futtatására szolgálnak. Részletes útmutatást az [SSIS-csomagok azure-beli üzembe helyezése oktatóanyagcímű](tutorial-create-azure-ssis-runtime-portal.md) témakörben talál. 
 
### <a name="sdks"></a>SDK-k
Ha Ön haladó felhasználó, és programozott felületet keres, a Data Factory sdk-k gazdag készletét biztosítja, amelyek segítségével a folyamatokat a kedvenc IDE használatával hozhat létre, kezelheti vagy figyelheti. Nyelvi támogatás: .NET, PowerShell, Python és REST.

### <a name="monitoring"></a>Figyelés
Az adatgyárak figyelheti a PowerShell, SDK, vagy a vizuális figyelési eszközök a böngésző felhasználói felületén. Az igény szerinti, eseményindító- és óravezérelt egyéni folyamatokat hatékonyan és eredményesen figyelheti és kezelheti. Szakítsa meg a meglévő feladatokat, tekintse meg a hibákat egy pillantással, részletezze a részletes hibaüzeneteket, és hibakeresést okoza a problémáknak egyetlen üvegtábláról anélkül, hogy környezetváltást vagy navigálást végezne a képernyők között. 

### <a name="new-features-for-ssis-in-data-factory"></a>Új funkciók az SSIS-hez a Data Factory-ban
A 2017-es nyilvános előzetes verzió megjelenése óta a Data Factory a következő funkciókkal egészült ki az SSIS-hez:

-    Az Azure SQL Database további három konfigurációjának/változatának támogatása a projektek/csomagok SSIS-adatbázisának (SSISDB) üzemeltetéséhez:
-    SQL-adatbázis virtuális hálózati szolgáltatás végpontokkal
-    Felügyelt példány
-    Rugalmas készlet
-    Az Azure Resource Manager virtuális hálózat támogatása egy klasszikus virtuális hálózat on-t a jövőben, amely lehetővé teszi, hogy adja be/csatlakozzon az Azure-SSIS integrációs futásidejű egy virtuális hálózat konfigurálva az SQL Database virtuális hálózati szolgáltatás végpontok/MI/helyszíni adathozzáférés. További információt a [Join an Azure-SSIS integrációs futásidő virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md)című témakörben talál.
-    Az Azure Active Directory (Azure AD) hitelesítésének és az SQL-hitelesítésnek az SSISDB-hez való csatlakozáshoz való támogatása, amely lehetővé teszi az Azure AD-hitelesítést az Azure-erőforrások Data Factory felügyelt identitásával
-    Támogatás saját helyszíni SQL Server-licencének az Azure Hybrid Benefit opcióval való jelentős költségmegtakarítás hozásához
-    Az Azure-SSIS integrációs futásidejű Enterprise Edition támogatása, amely lehetővé teszi a speciális/prémium funkciók használatát, egy egyéni beállítási felületet további összetevők/bővítmények telepítéséhez és egy partneri ökoszisztémához. További információ: [Enterprise Edition, Custom Setup, and 3rd Party Extensibility for SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Az SSIS mélyebb integrációja a Data Factory-ban, amely lehetővé teszi az első osztályú SSIS-csomag műveletek meghívását/aktiválását a Data Factory-folyamatokban, és ütemezését ssms-en keresztül. További információ: [Az ETL/ELT-munkafolyamatok modernizálása és bővítése SSIS-tevékenységekkel az ADF-folyamatokban.](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)


## <a name="what-is-the-integration-runtime"></a>Mi az integrációs futásidejű?
Az integrációs futásidejű az a számítási infrastruktúra, amelyet az Azure Data Factory a következő adatintegrációs képességek biztosításához használ különböző hálózati környezetekben:

- **Adatmozgás**: Az adatok mozgatásához az integrációs futásidejű a forrás- és céladattárak között mozgatja az adatokat, miközben támogatja a beépített összekötőket, a formátumkonverziót, az oszlopleképezést, valamint a teljesítmény- és méretezhető adatátvitelt.
- **Feladási tevékenységek**: Az átalakításhoz az integrációs futásidejű lehetővé teszi az SSIS-csomagok natív végrehajtását.
- **SSIS-csomagok végrehajtása:** Az integrációs futásidejű natív módon hajtja végre az SSIS-csomagokat felügyelt Azure-számítási környezetben. Az integrációs futtatókörnyezet támogatja a különböző számítási szolgáltatásokon, például az Azure HDInsighton, az Azure Machine Learningen, az SQL Database-en és az SQL Serveren futó átalakítási tevékenységek feladása és figyelése.

Az adatok áthelyezéséhez és átalakításához szükség szerint üzembe helyezheti az integrációs futásidejű egy vagy több példányt. Az integrációs futásidő futtatható egy Azure nyilvános hálózaton vagy egy magánhálózaton (helyszíni, Azure Virtual Network vagy Amazon Web Services virtuális magánfelhő [VPC]). 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi az integrációs runtimes-ok számának korlátozása?
Nincs szigorú korlát az integrációs futásidejű példányok száma lehet egy adat-előállítóban. Van azonban egy korlát a virtuális gép magok száma, amely az integrációs futásidejű használhatja előfizetésenként SSIS-csomag végrehajtása. További információ: [Data Factory limits](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Melyek az Azure Data Factory felső szintű fogalmai?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy kulcsfontosságú összetevőt tartalmaz, amelyek platformként működnek együtt, ahol adatvezérelt munkafolyamatokat hozhat össze az adatok áthelyezésének és átalakításának lépéseivel.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. A folyamat a munka egységének elvégzéséhez szükséges tevékenységek logikai csoportosítása. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például egy folyamat tartalmazhat egy tevékenységcsoportot, amely adatokat betöltése egy Azure blobból, majd futtatja a Hive-lekérdezést egy HDInsight-fürtön az adatok particionálásához. A haszon az, hogy egy folyamat segítségével kezelheti a tevékenységeket, mint egy készlet, ahelyett, hogy az egyes tevékenységek kezelése külön-külön. A folyamatok tevékenységeit egymás után, vagy egymástól függetlenül, párhuzamosan is használhatja.

### <a name="data-flows"></a>Adatfolyamok
Az adatfolyamok olyan objektumok, amelyeket vizuálisan hoz létre a Data Factoryban, amelyek a háttérSpark-szolgáltatások on-t skálázása korra alakítják át az adatokat. Nem kell megérteni e programozást vagy a Spark belső ügyeit. Csak tervezze meg az adatátalakítási szándékot grafikonok (leképezés) vagy számolótáblák (Wrangling) használatával.

### <a name="activities"></a>Tevékenységek
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. A Másolás tevékenységgel például adatokat másolhat az egyik adattárból egy másik adattárba. Hasonlóképpen használhatja a Hive-tevékenység, amely egy Hive-lekérdezést futtat egy Azure HDInsight-fürtön az adatok átalakításához vagy elemzéséhez. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondoljon rá így: Egy csatolt szolgáltatás határozza meg az adatforrással való kapcsolatot, és egy adatkészlet az adatok szerkezetét jelöli. Például egy Azure Storage-kapcsolt szolgáltatás határozza meg a kapcsolati karakterlánc ot az Azure Storage-fiókhoz való csatlakozáshoz. És egy Azure blob adatkészlet határozza meg a blob tároló és az adatokat tartalmazó mappa.

A csatolt szolgáltatásoknak két célja van a Data Factoryban:

- Egy *olyan adattár,* amely többek között egy helyszíni SQL Server-példányt, egy Oracle-adatbázis-példányt, egy fájlmegosztást vagy egy Azure Blob-tárfiókot tartalmaz. A támogatott adattárak listáját az Azure Data Factory tevékenység másolása című [témakörben tetszetős ekben.](copy-activity-overview.md)
- Egy *olyan számítási erőforrás* t, amely egy tevékenység végrehajtását képes üzemeltetni. A HDInsight-struktúra tevékenység például egy HDInsight-hadoop-fürtön fut. Az átalakítási tevékenységek és a támogatott számítási környezetek listáját az Adatok átalakítása az Azure Data Factoryban című [témakörben található.](transform-data.md)

### <a name="triggers"></a>Eseményindítók
Az eseményindítók olyan feldolgozási egységeket jelölnek, amelyek meghatározzák, hogy mikor indul el egy folyamat végrehajtása. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. 

### <a name="pipeline-runs"></a>Folyamatfuttatások
A folyamatfuttatás egy folyamat-végrehajtás példánya. A folyamat futtatása általában úgy történik, hogy argumentumokat ad át a folyamatban definiált paramétereknek. Az argumentumokat manuálisan vagy az eseményindító-definíción belül adhatja át.

### <a name="parameters"></a>Paraméterek
A paraméterek kulcs-érték párok írásvédett konfigurációban.Paramétereket definiál egy folyamatban, és átadja a megadott paraméterek argumentumait a futtatási környezetből történő végrehajtás során. A futtatási környezetet egy eseményindító vagy egy manuálisan végrehajtott folyamat hozta létre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Az adatkészlet egy erősen beírt paraméter, és egy entitás, amely újra felhasználhatja vagy hivatkozhat. Egy tevékenység hivatkozhat adatkészletek, és felhasználhatja az adatkészlet-definícióban definiált tulajdonságokat.

A csatolt szolgáltatás is egy erősen beírt paraméter, amely kapcsolati adatokat tartalmaz akár egy adattárban, vagy egy számítási környezetben. Ez is egy entitás, amely újra felhasználhatja, vagy referencia.

### <a name="control-flows"></a>Ellenőrzési folyamatok
Szabályozza a folyamatokat, amelyek olyan folyamattevékenységeket tartalmaznak, amelyek láncolási tevékenységeket tartalmaznak egy sorozatban, elágazásban, a folyamat szintjén meghatározott paramétereket, valamint azokat az argumentumokat, amelyeket a folyamat igény szerinti vagy eseményindítója meghívásakor ad át. A vezérlőfolyamatok közé tartozik az egyéni állapotátadási és -hurkolési tárolók (azaz a foreach iterátorok).


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Mi a Data Factory díjszabási modellje?
Az Azure Data Factory díjszabási részleteiről a [Data Factory díjszabási részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/data-factory/)

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hogyan maradhatok naprakész a Data Factory-val kapcsolatos információkkal?
Az Azure Data Factoryval kapcsolatos legfrissebb információkért látogasson el a következő webhelyekre:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentáció kezdőlapja](/azure/data-factory)
- [A termék kezdőlapja](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Műszaki mélymerülés 

### <a name="how-can-i-schedule-a-pipeline"></a>Hogyan ütemezhetek egy folyamatot? 
Az ütemező eseményindítójával vagy az időablak-eseményindítóval ütemezhet egy folyamatot. Az eseményindító falióra-naptár ütemezést használ, amely rendszeres időközönként vagy naptáralapú ismétlődő mintákba ütemezheti a folyamatokat (például hétfőnként 18:00-kor és csütörtökönként 21:00-kor). További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Továbbíthatok paramétereket egy folyamatfuttatáshoz?
Igen, a paraméterek egy első osztályú, legfelső szintű koncepció a Data Factoryban. Paramétereket definiálhat a folyamat szintjén, és argumentumokat adhat át a folyamat igény szerinti futtatásakor vagy egy eseményindító használatával.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Megadhatom a folyamatparamétereinek alapértelmezett értékeit? 
Igen. A folyamatok paramétereinek alapértelmezett értékeit megadhatja. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>A folyamat ban lévő tevékenységek felszámíthatják a folyamatnak átadott argumentumokat? 
Igen. A folyamaton belüli minden tevékenység felhasználhatja a folyamatnak átadott paraméterértéket, és futtathatja a `@parameter` konstrukcióval. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Egy tevékenység kimeneti tulajdonság a másik tevékenységben használható fel? 
Igen. Egy tevékenységkimenet felhasználható egy későbbi tevékenységben `@activity` a konstruktussal.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hogyan kezelhetem szabályosan a null értékeket egy tevékenységkimenetben? 
A kifejezésekben `@coalesce` a konstruktus segítségével szabályosan kezelheti a null értékeket. 

## <a name="mapping-data-flows"></a>Adatfolyamok leképezése

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Segítségre van szükségem az adatfolyam-logika hibaelhárításához. Milyen információkat kell megadnom a segítséghez?

Amikor a Microsoft segítséget vagy hibaelhárítást biztosít az adatfolyamokkal kapcsolatban, adja meg az adatfolyam-parancsfájlt. Ez a kód-mögötti parancsfájl az adatfolyam-diagramból. Az ADF felhasználói felületén nyissa meg az adatfolyamot, majd kattintson a jobb felső sarokban található "Parancsfájl" gombra. Másolja és illessze be a parancsfájlt, vagy mentse szövegfájlba.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Hogyan érhetem el az adatokat a Data Factory további 90 adatkészlettípusával?

A leképezési adatfolyam funkció jelenleg lehetővé teszi az Azure SQL Database, az Azure SQL Data Warehouse, az Azure Blob storage vagy az Azure Data Lake Storage Gen2 tagolt szövegfájljait, valamint a Blob storage-ból vagy a Data Lake Storage Gen2-ből származó parkettafájlokat natív módon a forrás és a fogadó számára. 

A Másolás tevékenység segítségével a többi összekötő adatait, majd hajtsa végre az adatfolyam-tevékenység et az adatok átalakítása után van előkészítve. Például a folyamat először másolja a Blob storage, majd egy adatfolyam-tevékenység egy adatkészletet a forrásban az adatok átalakításához.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Az önkiszolgáló integrációs futásidő elérhető az adatfolyamokhoz?

A saját üzemeltetésű infravörös kapcsolat egy ADF-folyamatszerkezet, amely segítségével a Másolási tevékenységgel adatokat szerezhet be vagy helyezhet át a helyszíni vagy virtuális gép alapú adatforrásokba és fogadókba. Az adatokat először egy másolattal, majd az adatfolyamot az átalakításhoz, majd egy későbbi másolattal helyezze át, ha az átalakított adatokat vissza kell helyeznie az on-prem tárolóba.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Az adatfolyam-számítási motor több bérlőt is kiszolgál?
A fürtök soha nem lesznek megosztva. Garantáljuk az elkülönítést a termelési futtatásokban futó minden egyes feladathoz. Hibakeresési forgatókönyv esetén egy személy kap egy fürtöt, és minden debugs megy, hogy a fürt, amely által kezdeményezett, hogy a felhasználó.

## <a name="wrangling-data-flows"></a>Vonagló adatfolyamok

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Melyek a támogatott régiók az adatfolyam okozásához?

A wrangling adatfolyam jelenleg a következő régiókban létrehozott adatgyárakban támogatott:

* Kelet-Ausztrália
* Közép-Kanada
* Közép-India
* USA keleti régiója
* USA 2. keleti régiója
* Kelet-Japán
* Észak-Európa
* Délkelet-Ázsia
* USA déli középső régiója
* Az Egyesült Királyság déli régiója
* USA nyugati középső régiója
* Nyugat-Európa
* USA nyugati régiója
* USA nyugati régiója, 2.

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Mik a korlátozások és korlátok a huzavona adatfolyam?

Az adatkészletnevek csak alfanumerikus karaktereket tartalmazhatnak. A következő adattárak támogatottak:

* DelimitedText adatkészlet az Azure Blob Storage-ban a fiókkulcs-hitelesítés használatával
* DelimitedText adatkészlet az Azure Data Lake Storage gen2 szolgáltatásában fiókkulcs vagy egyszerű szolgáltatáshitelesítés használatával
* Dekadkáltszöveges adatkészlet az Azure Data Lake Storage gen1 szolgáltatásegyszerű hitelesítéshasználatával
* Az Azure SQL Database és a Data Warehouse sql hitelesítéshasználatával. Lásd alább a támogatott SQL-típusokat. Nincs PolyBase vagy átmeneti támogatás az adattárházhoz.

Jelenleg a csatolt szolgáltatás Key Vault integrációja nem támogatott a cigi adatfolyamok.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Mi a különbség a leképezés és a huzavona adatfolyamok között?

Az adatfolyamatok leképezése leképezése leképezése leképezése leképezése leképezése az adatok nagy méretekben történő átalakítását biztosítja kódolás nélkül. Az adatátalakítási feladatot az adatfolyam-vásznon átalakítások sorozatának összeállításával tervezheti meg. Kezdje tetszőleges számú forrásátalakítással, majd adatátalakítási lépésekkel. Töltse ki az adatfolyamot egy fogadóval, hogy az eredményeket egy célhelyre hozhatja. Az adatfolyam leképezése kiválóan alkalmas az adatok leképezésére és átalakítására, mind az ismert, mind az ismeretlen sémákkal a fogadókban és a forrásokban.

A cigivel való kapcsolatfelvétel lehetővé teszi, hogy agilis adatelőkészítést és feltárást végezhessen a Power Query Online mashup szerkesztőjével, amely a spark-végrehajtáson keresztül nagy méretekben működik. Az adattavak számának növekedésével néha csak meg kell vizsgálnia egy adatkészletet, vagy hozzon létre egy adatkészletet a tóban. Nem egy ismert célponthoz mész. A wrangling adatfolyamok kevésbé formális és modellalapú elemzési forgatókönyvekhez használatosak.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Mi a különbség a Power Platform adatfolyamai és a huzavona adatfolyamok között?

A Power Platform-adatfolyamok lehetővé teszik a felhasználók számára, hogy adatokat importáljanak és alakítsanak át az adatforrások széles köréből a Common Data Service és az Azure Data Lake szolgáltatásba PowerApps-alkalmazások, Power BI-jelentések vagy flow-automatizálások létrehozásához. A Power Platform-adatfolyamok a Power BI-hoz és az Excelhez hasonlóan a Power Query meglévő adat-előkészítési élményét használják. A Power Platform-adatfolyamok lehetővé teszik a szervezeten belüli egyszerű újrafelhasználást, és automatikusan kezelik a vezénylést (például automatikusan frissítik azokat az adatfolyamokat, amelyek az előbbi frissítésekor egy másik adatfolyamtól függenek).

Az Azure Data Factory (ADF) egy felügyelt adatintegrációs szolgáltatás, amely lehetővé teszi az adatmérnökök és a polgári adatintegrátor számára, hogy összetett hibrid kinyerés-átalakítási-betöltési (ETL) és extract-load-transform (ELT) munkafolyamatokat hozzanak létre. Az ADF-ben az adatfolyam oka egy kódnélküli, kiszolgáló nélküli környezet lehetővé teszi a felhasználók számára, hogy leegyszerűsítse az adatok előkészítését a felhőben, és bármilyen adatméretre méretezhető, infrastruktúra-felügyelet nélkül. A Power Query adat-előkészítési technológiáját használja (a Power Platform-adatfolyamokban, excelben, Power BI-ban is használják) az adatok előkészítéséhez és alakításához. A big data-integráció összes bonyolultságának és méretezési kihívásainak kezelésére készült, az adatfolyamok összevonása lehetővé teszi a felhasználók számára, hogy gyorsan előkészítsék az adatokat a spark-végrehajtás révén. A felhasználók rugalmas adatfolyamatokat hozhatnak létre elérhető vizuális környezetben a böngészőalapú felületünkkel, és lehetővé tehetik az ADF számára a Spark-végrehajtás összetettségének kezelését. Ütemezéseket hozhat létre a folyamatokhoz, és figyelheti az adatfolyam-végrehajtásokat az ADF figyelési portálról. Az Adatok rendelkezésre állási sla-i könnyedén kezelhetők az ADF gazdag rendelkezésre állásának figyelésével és riasztásaival, és kiaknázhatják a beépített folyamatos integrációs és üzembe helyezési lehetőségeket, hogy menthessék és kezelhesd a folyamatokat felügyelt környezetben. Riasztások létrehozása és végrehajtási tervek megtekintéséhez annak ellenőrzéséhez, hogy a logika a tervek szerint teljesít-e az adatfolyamok finomhangolása során.

### <a name="supported-sql-types"></a>Támogatott SQL-típusok

A wrangling adatfolyam a következő adattípusokat támogatja az SQL-ben. Egy nem támogatott adattípus használatával kapcsolatban érvényesítési hiba jelenik meg.

* Rövid
* double
* real
* lebegőpontos
* Char
* nchar
* varchar
* nvarchar
* egész szám
* int
* bit
* logikai
* smallint
* tinyint
* bigint
* long
* szöveg
* dátum
* dátum/idő
* datetime2
* smalldatetime
* időbélyeg
* uniqueidentifier
* xml

A jövőben más adattípusok is támogatottak lesznek.

## <a name="next-steps"></a>További lépések
Az adat-előállító létrehozásának lépésenkénti útmutatóit az alábbi oktatóanyagokban találja:

- [Rövid útmutató: Adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)
