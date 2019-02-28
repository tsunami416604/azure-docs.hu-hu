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
ms.openlocfilehash: 112ff38ad4e35ac284501c5dd3881c4f340b5f9b
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984636"
---
# <a name="azure-data-factory-faq"></a>Az Azure Data Factory – gyakori kérdések
Ez a cikk ismerteti az Azure Data Factory kapcsolatos gyakori kérdésekre adott válaszokat.  

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
Data Factory az egy teljes körűen felügyelt, felhőalapú adatintegrációs szolgáltatás, amely automatizálja az adatáthelyezési és -átalakítási adatok. A gyári, amely átalakítja a nyersanyagokat késztermékekké berendezések, például az Azure Data Factory nyers adatokat gyűjthet, és átalakítja a nyersanyagokat használatra kész adatokat meglévő szolgáltatásokat hangolja össze. 

Azure Data Factory használatával az adatok áthelyezése a helyszíni és a felhő között az adatvezérelt munkafolyamatokat hozhat létre adattárakban. És feldolgozására képes, és átalakítási adatok használatával számítási szolgáltatásokkal, például az Azure HDInsight, az Azure Data Lake Analytics és az SQL Server Integration Services (SSIS) az integration runtime. 

A Data Factory az adatfeldolgozás hajthat végre, az Azure-alapú felhőszolgáltatás vagy a saját helyi számítási környezetben, például az SQL Server, Oracle vagy az SSIS. Miután létrehozott egy folyamatot, amely a szükséges műveletet hajt végre, ütemezheti, rendszeres időközönként (például óránként, naponta vagy hetente) futtatása, idő ablak ütemezés vagy eseményindítót a folyamatban az esemény előfordulása. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

### <a name="control-flows-and-scale"></a>Vezérlő folyamatok és méretezés 
A modern adattárházak a különféle integrációs folyamatainak és mintáinak támogatásához a Data Factory lehetővé teszi, hogy rugalmas folyamat adatmodellezés, amely tartalmazza a teljes vezérlés folyamata paradigmákat feltételes végrehajtása, beleértve programozási adatfolyamatokat állíthat össze, az elágaztatást, és explicit módon továbbíthat paramétereket belül, és ezek a folyamatok között. Átvitelvezérlés is magában foglalja a tevékenységküldés külső végrehajtási motorok és data flow lehetőségekről, köztük az adatátvitel nagy mennyiségű, a másolási tevékenység használatával – adatok átalakítása.

A Data Factory modellezheti az összes folyamat stílus, amely szükséges az adatintegrációt, és, amely elirányíthatók, igény szerint vagy ismétlődő ütemezés szerint szabadon biztosít. Néhány általános folyamat, amely lehetővé teszi, hogy ez a modell a következők:   

- Vezérlő folyamatok:
    - Egy adott folyamaton belüli sorozatban lánc tevékenységek.
    - Ág tevékenységek a folyamaton belül.
    - Paraméterek
        - A folyamat szintjén paraméterek megadása, és argumentumokat adhat meg, amíg a folyamat igény szerint vagy egy eseményindítóból indít el.
        - A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására.
    - Egyéniállapot-átadás
        - A tevékenységkimeneteket (például állapotot) a folyamat soron következő tevékenysége képes feldolgozni.
    - Tárolók hurkolása
        - For-each 
- Eseményindító-alapú folyamatok:
    - A folyamatok aktiválhatók igény szerint vagy időpont alapján indíthatók időpontig.
- Változási folyamatok:
    - Paramétereket használhat és a magas vízjelbe beleszámított a változásadatok másolásához áthelyezés a dimenzió-vagy egy relációs tárolóból, a helyszínen vagy a felhőben, az adatok betöltéséhez az adattárba közben. 

További információkért lásd: [oktatóanyag: Szabályozza a folyamatok](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>A kód ingyenes rendelkező nagy mennyiségű adatok átalakítása
Az új, böngészőalapú fejlesztőeszközeit kódmentes folyamatok készítése és üzembe helyezéséhez használható egy modern, interaktív webes felületet biztosít.

A vizuális adatok fejlesztők és az adattervezők az ADF webes felhasználói felületen a folyamatok létrehozásához használt tervezési kódmentes környezet. Teljesen integrálva van a Visual Studio Online Git és a CI/CD-integráció és iteratív fejlesztés a hibakeresési lehetőségeket biztosít.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Gazdag cross platform SDK-k tapasztalt felhasználók számára
Ha Ön tapasztalt felhasználó, és a egy programozható felületet keres, ADF v2 verzió létrehozásához, kezeléséhez használhatóak SDK-k széles skáláját biztosítja, a kedvenc IDE használatával folyamatok monitorozásához.
1.  Python SDK
2.  PowerShell parancssori felület
3.  C# SDK-t felhasználók is használhatják a dokumentált REST API-kkal kapcsolat az ADF V2 verziója

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iteratív fejlesztés és hibakeresés vizuális eszközökkel
Az Azure Data Factory (ADF) vizuális eszközök teszik lehetővé tegye, iteratív fejlesztés és hibakeresés. A folyamatokat hozhat létre, és tesztelje a hibakeresési funkció használatával a folyamatvásznon egyetlen sor kód megírása nélkül futtatja. A tesztelések eredményét megtekintheti a kimeneti ablakban, a folyamat vászon. Ha a teszt futtatása sikeres, további tevékenységek hozzáadása a folyamatban, és továbbra is, folytatható iteratív hibakeresést. A tesztelések is megszakítja, ha van folyamatban. Nem szükségesek a data factory szolgáltatás hibakeresési gombra kattintás előtt a módosítások közzétételére. Ez hasznos forgatókönyvekben, ahol szeretné győződjön meg arról, hogy az új módosításokat munkahelyi elvárt, mielőtt frissíti a data factory munkafolyamatok a fejlesztési, tesztelheti, vagy éles környezetben. 

### <a name="deploy-ssis-packages-to-azure"></a>SSIS-csomagok üzembe helyezése az Azure-ban 
Ha át szeretné helyezni az SSIS számítási feladatokat, hozzon létre egy adat-előállítót, és az Azure-SSIS integrációs modul üzembe helyezése. Az Azure-SSIS integrációs modul az Azure virtuális gépek (csomópontok), amelyek dedikált az SSIS-csomagok futtatása a felhőben egy teljesen felügyelt fürt. Részletes útmutatásért tekintse meg a [üzembe SSIS-csomagok az Azure-bA](tutorial-create-azure-ssis-runtime-portal.md) oktatóanyag. 
 
### <a name="sdks"></a>SDK-k
Ha tapasztalt felhasználóként programozható felületet keres, az ADF használatával hozhat létre, kezelése vagy folyamatok monitorozásához a kedvenc ide-je használatával SDK-k széles skáláját biztosítja. Nyelvi támogatás magában foglalja a .NET, PowerShell, Python és REST.

### <a name="monitoring"></a>Figyelés
Az adat-előállítók PowerShell, SDK-t vagy a böngésző felhasználói felületéről Visual figyelési eszközök használatával követheti nyomon. Figyelheti, és igény szerint, az eseményindító-alapú és óra alapuló egyéni folyamatok és hatékony módon kezelheti. Mégse meglévő feladatokat, egyetlen pillantással lásd a hibák részletes elemzést részletes hibaüzenet jelenik meg, és a problémák minden tekinthesse egyetlen ablaktábla a hibakeresési környezet közötti váltás vagy oda-vissza képernyők közötti váltás nélkül. 

### <a name="new-features-for-ssis-in-adf"></a>Új funkciók az ADF-ben SSIS-hez
2017-ben kezdeti nyilvános előzetes kiadása óta a Data Factory SSIS-hez hozzáadta a következő funkciókat:

-   Három további konfigurációk/változatok az Azure SQL Database (DB) gazdagép-projektek/csomagok (SSISDB) az SSIS-katalógushoz támogatják:
-   A virtuális hálózati Szolgáltatásvégpontok az Azure SQL DB
-   Felügyelt példány (MI)
-   Rugalmas készlet
-   Az Azure Resource Managerbeli virtuális hálózat (VNet) épülő klasszikus virtuális hálózat, a jövőben – fognak évülni támogatása Ez lehetővé teszi az Azure-SSIS integrációs modul (IR) egy virtuális hálózathoz virtuális hálózatok közötti szolgáltatás végpontok/MI az Azure SQL DB-hez konfigurált beszúrása/join / a helyszíni adatok elérése, lásd: https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Épülő SQL-hitelesítés szeretne csatlakozni az SSISDB - Ez lehetővé teszi, hogy használja az Azure Active Directory (AAD) hitelesítés támogatása az ADF az AAD-hitelesítés felügyelt identitás az Azure-erőforrásokhoz
-   Saját licence a helyszíni SQL Server, az Azure Hybrid Benefit (AHB) beállítás jelentős költségmegtakarítást megszerzésére támogatása
-   Támogatási Enterprise Edition az Azure-SSIS integrációs, amely lehetővé teszi a speciális/Premium szintű funkciók használata, telepítse a további összetevők/fájlnévkiterjesztéseket, valamint a 3. fél ökoszisztéma, lásd: egyéni telepítés: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Lásd az SSIS az ADF, amellyel elsőosztályú SSIS-csomag végrehajtása tevékenységek az ADF-folyamatok meghívásához vagy trigger és ütemezni keresztül ssms-ben, ugyanakkor szorosabb integrációt: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Mi az integrációs modul?
Az Integration runtime különböző hálózati környezetekben a következő adatintegrációs képességeket biztosít az Azure Data Factory által használt számítási infrastruktúra áll:

- **Adatáthelyezés**: Adatáthelyezéskor az integrációs modul helyezi át az adatokat a forrás adattárolóból a cél adattárolóba. A művelet során gondoskodik a beépített összekötőkről, a formátumkonverzióról, az oszlopmegfeleltetésről, valamint a nagy teljesítményű, skálázható adatátvitelről.
- **Tevékenységek szállítást**: Átalakításkor az integrációs modul lehetőséget teremt az SSIS csomagok natív végrehajtására.
- **SSIS-csomagok**: Natív módon végrehajtja az SSIS-csomagok Azure-beli felügyelt számítási környezetben. Az integrációs modul lehetővé teszi a különböző számítási szolgáltatásokon, például az Azure HDInsighton, az Azure Machine Learningen, az Azure SQL Database-en és az SQL Serveren futó átalakítási tevékenységek szervezését és figyelését.

Integrációs modul egy vagy több példány is üzemeltethető, van szükség az adatok áthelyezéséhez és átalakításához. Az Integration runtime futtathat egy Azure-beli nyilvános hálózaton vagy egy magánhálózaton (helyszíni, Azure Virtual Network vagy Amazon Web Services virtuális magánfelhő [VPC]). 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi az az integrációs modulok számának korlátját?
Nincs rögzített korlátozva az integration runtime példányok egy adat-előállítót is lehet. Azonban korlátozva van, amely SSIS-csomag végrehajtása előfizetésenként használhatja az integrációs modul virtuális gép magjainak számát. További információkért lásd: [Data Factory korlátai](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Mik az Azure Data Factory felső szintű alapelvek?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből, amelyek együtt, amelyen is amelyeknek a lépései áthelyezik és átalakítják az adatokat az adatvezérelt munkafolyamatok platform tartalmazza.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. Egy folyamat egy adott munkaegység végrehajtásához tevékenységek logikus csoportosításai. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például a folyamat tartalmazhat egy csoport tevékenységeket, majd futtassa a Hive-lekérdezést futtathat egy HDInsight-fürtön az adatok particionálásához és gyűjthet adatokat egy Azure-blobból. Az az előnye, hogy egy folyamat használatával kezelheti a tevékenységek egy készletben kellene minden egyes tevékenységet külön-külön. Láncolhatja össze a tevékenységek egy folyamatban, hogy azok egymás után, vagy hogy műveleteket lehessen végezni őket egymástól függetlenül, párhuzamosan.

### <a name="activity"></a>Tevékenység
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. Használhat például egy *másolási* tevékenység, amely az adatok másolása az egyik adattárból a másikba. Hasonlóképpen egy Hive-tevékenység Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez is használhatja. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Ezzel a módszerrel tekinthetjük: társított szolgáltatás határozza meg a kapcsolat az adatforráshoz, és a egy adatkészlet jelöli az adatok struktúráját. Például egy Azure Storage társított szolgáltatás megadja a kapcsolati karakterláncot az Azure Storage-fiókhoz való csatlakozáshoz. És a egy Azure Blob-adatkészlet meghatározza a blobtárolót és az adatokat tartalmazó mappát.

Társított szolgáltatások a Data Factory két célja van:

- Képviselő egy *adattár* , amely magában foglalja, de már nem korlátozódik, a helyszíni SQL Server-példány, Oracle database-példány, fájlmegosztás vagy egy Azure Blob storage-fiókot. Támogatott adattárak listáját lásd: [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md).
- Olyan *számítási erőforrás* jelölésére, amelyen végrehajtható a tevékenység. Például a HDInsight Hive-tevékenység fut egy HDInsight Hadoop-fürtön. Átalakítási tevékenységek és a támogatott számítási környezetek listáját lásd: [átalakítása az Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Eseményindítók
Az eseményindítók olyan, amelyek meghatározzák, hogy ha egy folyamat-végrehajtás megkezdődik a feldolgozási egységek. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. 

### <a name="pipeline-runs"></a>Folyamatfuttatások
Folyamat futásának egy folyamat-végrehajtás egy példányát. Folyamat futásának argumentumoknak a folyamatban meghatározott paraméterekhez általában hozza létre. Manuálisan, vagy az eseményindító meghatározásán az argumentumokat adhat át.

### <a name="parameters"></a>Paraméterek
Kulcs-érték párok csak olvasható konfiguráció paraméterei a következők. A folyamat paramétereit, és adja át a paraméterekhez tartozó argumentumok végrehajtása során a futtatási környezetből. A futtatási környezetből létrejön egy eseményindító vagy egy folyamatot, amely manuálisan hajtható végre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Egy adatkészlet, szigorúan típusos paraméterek és a egy entitás, amely akkor újrafelhasználás vagy hivatkozás. Egy tevékenység is referencia-adatkészletek, és lefoglalhatja a az adatkészlet definícióban meghatározott tulajdonságait.

A társított szolgáltatás nem is egy adattárat vagy számítási környezetek kapcsolatadatait tartalmazó, szigorúan típusos paraméterek. Emellett akkor is olyan entitás, amely akkor újrafelhasználás vagy hivatkozás.

### <a name="control-flows"></a>Vezérlő folyamatok
Vezérlő folyamatok szervezése tevékenységlánc egy feladatütemezési, az elágaztatást, a folyamat szintjén meghatározó paraméterek tartalmazó folyamat tevékenységek és argumentumokat, át kell adnia a folyamat igény szerint vagy egy eseményindítóból meghívását. Vezérlő folyamatok tartalmazzák a egyéni-állapot átadása és (vagyis a for-each iterátorok) tárolók hurkolása.


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészlet és a társított szolgáltatások](concepts-datasets-linked-services.md)
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
Az ütemező eseményindító vagy idő ablakos eseményindító segítségével ütemezhet egy folyamatot. Az eseményindító naptár időpontalapú ütemezés szerint használ, és használhatja a folyamatok ütemezése, rendszeres időközönként vagy ismétlődő minták naptár-alapú (például hetente a Este 6 Órára hétfőjén és csütörtökönként este 9-kor) használatával. További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>E paramétereket adhat át egy folyamat futásának?
Igen, a paramétereket egy gyors, elsőosztályú, legfelső szintű fogalom, az ADF-ben is. Argumentumokat adhat, a folyamat futtatása igény szerint vagy egy eseményindítóval végrehajtásakor, és a folyamat szintjén paraméterek megadása.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>A folyamat paramétereihez tartozó alapértelmezett értékeket adhatja meg? 
Igen. Meghatározhatja az alapértelmezett értékeket a paraméterek a folyamatokban. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>A folyamat egyik tevékenységének használhatják egy folyamat számára továbbított argumentumok? 
Igen. A folyamat minden tevékenységét felhasználhat a paraméter értéke, amely a folyamat számára továbbított és futtathat a `@parameter` hozhatnak létre. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Egy tevékenység kimeneti tulajdonság felhasználhatók egy másik tevékenység? 
Igen. Egy tevékenység kimenete a soron következő tevékenysége felhasználhatók a `@activity` hozhatnak létre.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Szabályosan teendőim egy tevékenység kimenete null értéket? 
Használhatja a `@coalesce` hozhatnak létre a kifejezésekben szabályosan kezelni a null értékeket. 

## <a name="mapping-data-flows"></a>Leképezés adatfolyamok

### <a name="which-adf-version-do-i-use-to-create-data-flows"></a>Az ADF verziószámának használható adatfolyamok létrehozására?
Az ADF V2 verzió használatával adatok folyamatok létrehozása
  
### <a name="i-was-a-previous-private-preview-customer-using-data-flows-and-i-used-the-adf-v2-wdata-flows-preview-version"></a>Adatfolyamok használatával előző privát előzetes verziójú ügyfél voltam, és az ADF v2 verzió w/Data flow előzetes verzió használata
Ez a verzió már elavult. Adatfolyam-gyűjteményre ADF v2 verzió használata
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-data-flows"></a>Mi változott a privát előzetes verzió, az adatfolyamok korlátozott nyilvános előzetes verzió?
Ahhoz, hogy a saját Databricks-fürtök már nem lesz. Fürt létrehozása és könnycsepp lefelé ADF kezeli. BLOB-adatkészletek és ADLS-adatkészletek tagolt szöveg és a parquet eszközökben adatkészletek vannak osztva. Továbbra is használhatja az ADLS & Blob Store tárolja ezeket a fájlokat. A megfelelő társított szolgáltatást használja ezeket a storage-motorhoz.

### <a name="can-i-migrate-my-private-preview-factories-to-adf-v2"></a>Áttelepíthetek saját privát előzetes verzió előállítók ADF v2 verzió?

[Igen, az alábbi utasításokat](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration)

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-do-you-need"></a>Hibaelhárítás a saját data flow logikai segítségre van szükségem, mire van szüksége?

Ha a Microsoft Súgó és hibaelhárítás az adatfolyamok biztosít, adja meg a "DSL kód csomag". Ehhez kövesse az alábbi lépéseket:

* Az Flow-tervezőből kattintson a jobb felső sarokban lévő "Code". Ez megjeleníti az adatfolyamhoz szerkeszthető JSON-kód.
* A kód nézetből "Csomag" kattintson a jobb felső sarokban. A terv kapcsoló JSON-ból a formázott DSL parancsfájl-csomaggal.
* Másolja és illessze be ezt a szkriptet, vagy mentse a fájlt.

### <a name="how-do-i-access-data-using-the-other-80-dataset-types-in-adf"></a>Hogyan férhetek hozzá a 80 adatkészlet típusok használata az ADF-ben az adatok?

Az adatfolyam jelenleg lehetővé teszi, hogy Azure SQL Database, az Azure SQL DW, a tagolt szöveges fájlok Blob vagy ADLS és a Parquet-fájlokat a Blob vagy ADLS natív módon a forrás és fogadó. Adatok készíthetők elő az egyéb összekötőkhöz bármelyik a másolási tevékenység használatával, és majd hajtsa végre az adatok átalakításához, miután lesz ütemezve lett egy adatfolyam-tevékenység. Például a folyamat első blobba másolja, majd egy adatfolyam-tevékenység fog egy adatkészlet a forrás, hogy az adatok átalakításához.

## <a name="next-steps"></a>További lépések
Részletes útmutatásért az adat-előállító létrehozása a következő oktatóanyagokban talál:

- [Rövid útmutató: Adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)
