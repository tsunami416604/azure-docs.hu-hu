---
title: 'Azure Data Factory: Gyakori kérdések |} A Microsoft Docs'
description: Válaszok az Azure Data Factory – gyakori kérdések.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048214"
---
# <a name="azure-data-factory-faq"></a>Az Azure Data Factory – gyakori kérdések
Ez a cikk ismerteti az Azure Data Factory kapcsolatos gyakori kérdésekre adott válaszokat.  

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
Data Factory az egy teljes körűen felügyelt, felhőalapú adatintegrációs szolgáltatás, amely automatizálja az adatáthelyezési és -átalakítási adatok. A gyári, amely átalakítja a nyersanyagokat késztermékekké berendezések, például az Azure Data Factory nyers adatokat gyűjthet, és átalakítja a nyersanyagokat használatra kész adatokat meglévő szolgáltatásokat hangolja össze. 

Azure Data Factory használatával az adatok áthelyezése a helyszíni és a felhő között az adatvezérelt munkafolyamatokat hozhat létre adattárakban. És feldolgozására képes, és átalakítási adatok használatával számítási szolgáltatásokkal, például Azure HDInsight, az Azure Data Lake Analytics és az SQL Server Integration Services (SSIS) integrációs modul. 

A Data Factory az adatfeldolgozás hajthat végre, az Azure-alapú felhőszolgáltatás vagy a saját helyi számítási környezetben, például az SQL Server, Oracle vagy az SSIS. Miután létrehozott egy folyamatot, amely a szükséges műveletet hajt végre, ütemezheti rendszeres időközönként (óránkénti, napi vagy heti, a példában), ez alatt az időszak ütemezését, vagy az esemény előfordulása a folyamat aktiválása. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

### <a name="control-flows-and-scale"></a>Vezérlő folyamatok és méretezés 
A különféle integrációs folyamatainak és mintáinak támogatásához a Data Factory lehetővé teszi, hogy a modern data warehouse rugalmas a modellezési folyamat. Ez magában foglalja a teljes vezérlés folyamata paradigmákat, többek között a feltételes végrehajtási programozási, az elágaztatást az adatfolyamatokat állíthat össze, és explicit módon továbbíthat paramétereket belül, és ezek a folyamatok között lehetővé teszi. Átvitelvezérlés is magában foglalja a tevékenységküldés külső végrehajtási motorok és data flow lehetőségekről, köztük az adatátvitel nagy mennyiségű, a másolási tevékenység használatával – adatok átalakítása.

A Data Factory modellezheti az összes folyamat stílus, amely szükséges az adatintegrációt, és, amely elirányíthatók, igény szerint vagy ismétlődő ütemezés szerint szabadon biztosít. Néhány általános folyamat, amely lehetővé teszi, hogy ez a modell a következők:   

- Vezérlő folyamatok:
    - Tevékenységek összefűzhetők sorrendben egy adott folyamaton belül.
    - Tevékenységek elágaztathatók egy adott folyamaton belül.
    - Paraméterek:
        - Paramétereket is definiálva, a folyamat szintjén, és az argumentumok továbbítása történhet, amíg a folyamat igény szerint vagy egy eseményindítóból indít el.
        - A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására.
    - Egyéniállapot:
        - Tevékenység kimeneteiből állapota, beleértve a folyamat soron következő tevékenysége képes használni.
    - Tárolók hurkolása:
        - A foreach tevékenység ismétlődő tevékenységek egy adott gyűjtemény megismétli. 
- Eseményindító-alapú folyamatok:
    - A folyamatok aktiválhatók igény szerint vagy időpont alapján indíthatók időpontig.
- Változási folyamatok:
    - Paraméterek áthelyezés a dimenzió-vagy egy relációs tárolóból, a helyszínen vagy a felhőben, az adatok betöltéséhez az adattárba közben a változásadatok másolásához a magas vízjelbe beleszámított meghatározására használható. 

További információkért lásd: [oktatóanyag: Szabályozza a folyamatok](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Méretek mellett kódmentes folyamatok átalakított adat
Az új, böngészőalapú fejlesztőeszközeit kódmentes folyamatok készítése és üzembe helyezéséhez használható egy modern, interaktív webes felületet biztosít.

A vizuális adatok fejlesztők és az adattervezők az adat-előállító webes felhasználói felületen a folyamatok létrehozásához használt tervezési kódmentes környezet. Teljesen integrálva van a Visual Studio Online Git és a CI/CD-integráció és iteratív fejlesztés a hibakeresési lehetőségeket biztosít.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>A haladó felhasználók gazdag platformfüggetlen SDK-k
A Data Factory V2 egy létrehozásához, kezeléséhez és folyamatok monitorozásához a kedvenc IDE használatával használható SDK-k széles skáláját biztosítja többek között:
* Python SDK
* PowerShell parancssori felület
* C# SDK

Felhasználók is használhatják a dokumentált REST API-k a Data Factory V2 felületre.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteratív fejlesztés és hibakeresés vizuális eszközökkel
Az Azure Data Factory vizuális eszközök lehetővé teszik az iteratív fejlesztés és hibakeresés. A folyamatok hozhat létre, és tesztelje a futtatások használatával a **Debug** egy egyetlen sor kód megírása nélkül folyamatvásznon képesség. Megtekintheti a tesztelések az eredményeit a **kimeneti** a folyamatvásznon ablakában. A teszt futtatása sikeres, miután további tevékenységek hozzáadása a folyamatban, és továbbra is, folytatható iteratív hibakeresést. A tesztelések is megszakítja, miután folyamatban vannak. 

Nem szükségesek a data factory szolgáltatás kiválasztása előtt a módosítások közzétételére **Debug**. Ez hasznos forgatókönyvekben, ahol szeretné győződjön meg arról, hogy a Újdonságok vagy változások várt módon fog működni a data factory munkafolyamatok fejlesztési, tesztelési és éles környezetekben frissítése előtt. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Lehetővé teszi az Azure SSIS-csomagok üzembe helyezése 
Ha át szeretné helyezni az SSIS számítási feladatokat, hozzon létre egy adat-előállítót, és az Azure-SSIS integrációs modul üzembe helyezése. Egy Azure-SSIS integrációs modul az Azure virtuális gépek (csomópontok), amelyek dedikált az SSIS-csomagok futtatása a felhőben egy teljesen felügyelt fürt. Részletes útmutatásért tekintse meg a [üzembe SSIS-csomagok az Azure-bA](tutorial-create-azure-ssis-runtime-portal.md) oktatóanyag. 
 
### <a name="sdks"></a>SDK-k
Ha tapasztalt felhasználóként programozható felületet keres, a Data Factory használatával hozhat létre, kezelése vagy folyamatok monitorozásához a kedvenc ide-je használatával SDK-k széles skáláját biztosítja. Nyelvi támogatás magában foglalja a .NET, PowerShell, Python és REST.

### <a name="monitoring"></a>Figyelés
Az adat-előállítók PowerShell, SDK-t vagy a böngésző felhasználói felületéről Visual figyelési eszközök használatával követheti nyomon. Figyelheti, és igény szerinti, az eseményindító-alapú és óra alapuló egyéni folyamatok kezelése és hatékony módon. A részletes hibaüzenet jelenik meg, és elháríthatja a problémákat, minden tekinthesse környezet közötti váltás vagy oda-vissza képernyők közötti váltás nélkül egyetlen ablaktábla a részletezés Mégse meglévő feladatokat, egyetlen pillantással lásd a hibák. 

### <a name="new-features-for-ssis-in-data-factory"></a>Új szolgáltatások a Data Factory SSIS-hez
A kezdeti nyilvános előzetes verziójának a 2017-ben, mivel a Data Factory SSIS-hez hozzáadta ezt a következő funkciókat:

-   Három további konfigurációk/változatának Azure SQL Database-projektek/csomagok (SSISDB) SSIS adatbázisának üzemeltetéséhez támogatják:
-   Az SQL Database virtuális hálózati Szolgáltatásvégpontok
-   Felügyelt példány
-   Rugalmas készlet
-   Egy Azure Resource Manager virtuális hálózat egy klasszikus virtuális hálózat a jövőben elavulttá válik, amely lehetővé teszi felett támogatása beszúrása/való csatlakozás az Azure-SSIS integrációs modult egy virtuális hálózathoz, virtuális hálózati szolgáltatással konfigurált SQL-adatbázis végpontok/MI/a helyszíni adatokhoz való hozzáférést. További információ: [egy Azure-SSIS integrációs modul csatlakoztatása virtuális hálózat](join-azure-ssis-integration-runtime-virtual-network.md).
-   Azure Active Directory (Azure AD-) hitelesítést és az SSISDB, lehetővé téve az Azure AD-hitelesítés a Data Factory által felügyelt identitás, az Azure-erőforrásokhoz való csatlakozás SQL-hitelesítés támogatása
-   Saját licence a helyszíni SQL Server hogy közül az Azure Hybrid Benefittel jelentős költségmegtakarítást támogatása
-   Enterprise Edition, amely lehetővé teszi az Azure-SSIS integrációs modul támogatása speciális/Premium szintű funkciók használata, egy egyéni telepítés felületet további összetevők/bővítmények és a egy partneri ökoszisztéma telepítése. További információ: [Enterprise Edition, egyéni beállításai, és 3. fél bővíthetőségi ADF az SSIS-hez](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Ugyanakkor szorosabb integrációt az SSIS első osztályú SSIS-csomag végrehajtása tevékenységek a Data Factory-folyamatok meghívásához vagy trigger és ütemezni SSMS használatával teszi lehetővé adat-előállítóban. További információ: [Modernize és az ETL/ELT munkafolyamatok SSIS tevékenységek az ADF folyamatok kiterjesztése](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Mi az integrációs modul?
Az integrációs modul az a számítási infrastruktúra, amellyel Azure Data Factory a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatáthelyezés**: Adatáthelyezéskor az integrációs modul helyezi át az adatokat a forrás- és adattárak között, miközben gondoskodik a beépített összekötők, formátumátalakítás, oszlopmegfeleltetésről, és nagy teljesítményű, skálázható adatátvitelről.
- **Tevékenységek szállítást**: Átalakításkor az integrációs modul lehetőséget biztosít SSIS csomagok natív végrehajtására.
- **SSIS-csomagok**: Az integrációs modul SSIS-csomagok natív módon végrehajtja a felügyelt Azure-beli számítási környezetben. Az integrációs modul lehetővé teszi zahájeno és a különböző számítási szolgáltatások, például az Azure HDInsight, Azure Machine Learning, az SQL Database és SQL Server futó átalakítási tevékenységek figyelése.

Az integrációs modul egy vagy több példány is üzemeltethető, van szükség az adatok áthelyezéséhez és átalakításához. Az integrációs modul futtathat egy Azure-beli nyilvános hálózaton vagy egy magánhálózaton (helyszíni, Azure Virtual Network vagy Amazon Web Services virtuális magánfelhő [VPC]). 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi az az integrációs modulok számának korlátját?
Nincs rögzített korlátozva az integration runtime példányok egy adat-előállítót is lehet. Azonban korlátozva van, amely SSIS-csomag végrehajtása előfizetésenként használhatja az integrációs modul virtuális gép magjainak számát. További információkért lásd: [Data Factory korlátai](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Mik az Azure Data Factory felső szintű alapelvek?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből, amelyek együtt, amelyen is amelyeknek a lépései áthelyezik és átalakítják az adatokat az adatvezérelt munkafolyamatok platform tartalmazza.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. Egy folyamat egy adott munkaegység végrehajtásához tevékenységek logikus csoportosításai. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoport tevékenységeket, majd futtassa a Hive-lekérdezést futtathat egy HDInsight-fürtön az adatok particionálásához és gyűjthet adatokat egy Azure-blobból. Az az előnye, hogy egy folyamat használatával kezelheti a tevékenységek egy készletben kellene minden egyes tevékenységet külön-külön. Láncolhatja össze a tevékenységek egy folyamatban, hogy azok egymás után, vagy hogy műveleteket lehessen végezni őket egymástól függetlenül, párhuzamosan.

### <a name="activities"></a>Tevékenységek
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. A másolási tevékenység használatával például adatok másolása az egyik adattárból a másikba. Hasonlóképpen egy Hive-tevékenység Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez is használhatja. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tekintsen rá úgy ezzel a módszerrel: A társított szolgáltatás határozza meg a kapcsolat az adatforráshoz, és az adatkészlet jelöli az adatok struktúráját. Például egy Azure Storage társított szolgáltatás megadja a kapcsolati karakterláncot az Azure Storage-fiókhoz való csatlakozáshoz. És a egy Azure blob-adatkészlet pedig meghatározza a blobtárolót és az adatokat tartalmazó mappát.

Társított szolgáltatások a Data Factory két célja van:

- Képviselő egy *adattár* , amely magában foglalja, de már nem korlátozódik, a helyszíni SQL Server-példány, Oracle database-példány, fájlmegosztás vagy egy Azure Blob storage-fiókot. Támogatott adattárak listáját lásd: [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md).
- Olyan *számítási erőforrás* jelölésére, amelyen végrehajtható a tevékenység. Például a HDInsight Hive-tevékenység fut egy HDInsight Hadoop-fürtön. Átalakítási tevékenységek és a támogatott számítási környezetek listáját lásd: [átalakítása az Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Eseményindítók
Az eseményindítók olyan, amelyek meghatározzák, hogy ha egy folyamat-végrehajtás megkezdődik a feldolgozási egységek. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. 

### <a name="pipeline-runs"></a>Folyamatfuttatások
Folyamat futásának egy folyamat-végrehajtás egy példányát. Folyamat futásának argumentumoknak a folyamatban meghatározott paraméterekhez általában hozza létre. Manuálisan, vagy az eseményindító meghatározásán az argumentumokat adhat át.

### <a name="parameters"></a>Paraméterek
Kulcs-érték párok csak olvasható konfiguráció paraméterei a következők. A folyamat paramétereit, és adja át a paraméterekhez tartozó argumentumok végrehajtása során a futtatási környezetből. A futtatási környezetből létrejön egy eseményindító vagy egy folyamatot, amely manuálisan hajtható végre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Az adathalmaz olyan, szigorúan típusos paraméterek és a egy entitás, amely akkor újrafelhasználás vagy hivatkozás. Tevékenységek hivatkozhatnak adatkészletekre, és lefoglalhatja a az adatkészlet-definícióban meghatározott tulajdonságait.

A társított szolgáltatás nem is egy adattárat vagy számítási környezetek kapcsolatadatait tartalmazó, szigorúan típusos paraméterek. Emellett akkor is olyan entitás, amely akkor újrafelhasználás vagy hivatkozás.

### <a name="control-flows"></a>Vezérlő folyamatok
Vezérlő folyamatok szervezése tevékenységlánc egy feladatütemezési, az elágaztatást, a folyamat szintjén meghatározó paraméterek tartalmazó folyamat tevékenységek és argumentumokat, át kell adnia a folyamat igény szerint vagy egy eseményindítóból meghívását. Vezérlő folyamatok átadása és (vagyis a foreach iterátorok) tárolók hurkolása egyéni állapota is.


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Mi a Data Factory díjszabási modellje?
Azure Data Factory díjszabása, lásd: [Data Factory díjszabása](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hogyan maradhat naprakészen tartása információ a Data Factory?
A legfrissebb információk az Azure Data Factory szolgáltatásról tanulmányozza a következő helyekre:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentációs kezdőlap](/azure/data-factory)
- [Termék kezdőlapja](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Részletes technikai bemutatása 

### <a name="how-can-i-schedule-a-pipeline"></a>Hogyan ütemezhet egy folyamatot? 
Az ütemező eseményindító vagy idő ablakos eseményindító segítségével ütemezhet egy folyamatot. Az eseményindító egy naptár például az (hétfőjén, 18:00:00) és csütörtökönként este 9:00-kor ütemezheti a rendszeres időközönként vagy a naptár-alapú ismétlődő minták a folyamatokat időpontalapú ütemezés használ. További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>E paramétereket adhat át egy folyamat futásának?
Igen, paraméterei egy gyors, elsőosztályú, legfelső szintű fogalom, adat-előállítóban. Argumentumokat adhat, a folyamat futtatása igény szerint vagy egy eseményindítóval végrehajtásakor, és a folyamat szintjén paraméterek megadása.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>A folyamat paramétereihez tartozó alapértelmezett értékeket adhatja meg? 
Igen. Meghatározhatja az alapértelmezett értékeket a paraméterek a folyamatokban. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>A folyamat egyik tevékenységének használhatják egy folyamat számára továbbított argumentumok? 
Igen. A folyamat minden tevékenységét felhasználhat a paraméter értéke, amely a folyamat számára továbbított és futtathat a `@parameter` hozhatnak létre. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Egy tevékenység kimeneti tulajdonság felhasználhatók egy másik tevékenység? 
Igen. Egy tevékenység kimenete a soron következő tevékenysége felhasználhatók a `@activity` hozhatnak létre.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Szabályosan teendőim egy tevékenység kimenete null értéket? 
Használhatja a `@coalesce` hozhatnak létre a kifejezésekben szabályosan kezelni a null értékeket. 

## <a name="mapping-data-flows"></a>Leképezés adatfolyamok

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>A Data Factory verziószámának használható adatok folyamatokat hozhat létre?
A Data Factory V2 verzióját használja az adatok folyamatokat hozhat létre.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Data flow-felhasználók előző privát előzetes verziójú ügyfél voltam, és az adatfolyam-gyűjteményre használtam a Data Factory V2 előzetes verzióra.
Ez a verzió már elavult. Használja a Data Factory V2 adatfolyam-gyűjteményre.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Mi változott a privát előzetes verzió az adatfolyamok tekintetében korlátozott nyilvános előzetes verziója?
Ahhoz, hogy a saját Azure Databricks-fürtök már nem lesz. Fürt létrehozása és könnycsepp lefelé a Data Factory kezeli. BLOB-adatkészletek és az Azure Data Lake Storage Gen2 adatkészletek elválasztójellel tagolt szöveges és a parquet vagy egyéb adatkészletek vannak osztva. Data Lake Storage Gen2- és Blob storage használatával továbbra is tárolja ezeket a fájlokat. A storage motorok megfelelő társított szolgáltatást használhatja.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Áttelepíthetek saját privát előzetes verzió előállítók a Data Factory V2?

Igen. [Kövesse az utasításokat](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Hibaelhárítás a saját data flow logikai segítségre van szükségem. Milyen adatokhoz férhet szükséges e nyújt segítséget?

Ha a Microsoft biztosít, segítséget vagy hibaelhárítás az adatfolyam-gyűjteményre, adja meg a DSL kód terv. Ehhez kövesse az alábbi lépéseket:

1. Válassza ki a Data Flow Tervező **kód** jobb felső sarokban. Ez megjeleníti az adatfolyamhoz szerkeszthető JSON-kód.
2. Válassza ki a kód nézet **megtervezése** jobb felső sarokban. Ezzel a kapcsolóval a csak olvasható formátumú DSL parancsfájl csomagra váltani fog a JSON.
3. Másolja és illessze be ezt a szkriptet, vagy mentse a fájlt.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Hogyan érhetem el az adatokat a a 80 adatkészlet típusok a Data Factory használatával

Az adatfolyam-leképezés funkció jelenleg az Azure SQL Database, Azure SQL Data Warehouse, tagolt szövegfájlokat az Azure Blob storage vagy az Azure Data Lake Storage Gen2, és a Parquet-fájlokat a Blob storage vagy a Data Lake Storage Gen2 natív módon a forrás- és fogadó teszi lehetővé. 

A másolási tevékenység használatával adatok készíthetők elő az egyéb összekötőkhöz bármelyik és hajthat végre egy adatfolyam tevékenység után lesz ütemezve lett az adatok átalakításához. Például a folyamat első másol a Blob storage-ba, és majd egy adatfolyam-tevékenység használja az adatkészlet forrása módosíthatja az adatokat.

## <a name="next-steps"></a>További lépések
Részletes útmutatásért az adat-előállító létrehozása a következő oktatóanyagokban talál:

- [Rövid útmutató: Adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)
