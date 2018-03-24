---
title: 'Az Azure Data Factory: Gyakori kérdések |} Microsoft Docs'
description: Azure Data Factory gyakran feltett kérdésekre adott válaszok.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: 8c240e1a654c80c34f6b612d9126058e5d67c4c2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory-faq"></a>Az Azure Data Factory – gyakori kérdések
Ez a cikk az Azure Data Factory szolgáltatásnak 2 verziójára vonatkozik. Adat-előállító gyakran feltett kérdésekre adott válaszok biztosít.  

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [gyakori kérdések a Data Factory 1-es verziójú](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
Adat-előállító egy teljes körűen felügyelt, felhőalapú, adat-integrációs szolgáltatás, amely automatizálja a mozgás és az adatok átalakítása. Például olyan adat-előállítóval, amely a nyers átalakítása termékek berendezések fut az Azure Data Factory koordinálja a nyers adatokat gyűjthet, és irányítópulttá, készen áll a használatra információ meglévő szolgáltatások. 

Azure Data Factory használatával áthelyezni az adatokat a helyszíni és a felhő között az adatvezérelt munkafolyamatokat hozhat létre adattárolókhoz. Tud feldolgozni, és átalakítási adatok segítségével számítási szolgáltatások, például az Azure HDInsight, az Azure Data Lake Analytics és az SQL Server Integration Services (SSIS) integrációs futásidejű. 

A Data Factory az adatok feldolgozása hajthat végre, vagy egy Azure-alapú felhőalapú szolgáltatás, vagy saját önálló üzemeltetett számítási környezetben, például SSIS, SQL Server vagy Oracle. Miután létrehozott egy folyamatot, amely hajt végre a műveletet, amelyekre szüksége van, úgy, hogy rendszeres időközönként (például óránkénti, napi vagy heti) futtatása, vagy a csővezeték az esemény előfordulása eseményindítót ütemezhető. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

## <a name="whats-different-in-version-2"></a>Melyek a 2. verzió újdonságai?
Az Azure Data Factory 2. verziója az eredeti Azure Data Factory adatáthelyezési és -átalakítási szolgáltatására épül, de a felhőt előnyben részesítő adatintegrációs forgatókönyvek szélesebb készletét használja. Az Azure Data Factory 2-es verzióját a következő szolgáltatásokat biztosítja:

- Vezérlő adatfolyamok és méretezhetőség
- SSIS-csomagok üzembe helyezése és futtatása az Azure-ban

A 1-es verzió, a következő azt felismeri, hogy az ügyfelek kell tervezési összetett, hibrid adatok-integrációs feladatokhoz szükséges adatmozgatást és a feldolgozás a felhőben, a helyszíni és a felhő virtuális gépek (VM). Ezek a követelmények állapotba helyezi át, és feldolgozni az adatokat a védett virtuális hálózati környezetben, és az igény szerinti számítási kapacitásért bővíteni kell.

Adatok folyamatok egyre analytics üzleti stratégia kulcsfontosságú része, mivel azt megfigyelhető volt, hogy ezek a tevékenységek esetében támogatja a növekményes adatok és az esemény által indított végrehajtások rugalmas ütemezés. Összetettségét növekszik, így túl does az a követelmény a szolgáltatás, amely tartalmazza az ugorhat, ismétlési és feltételes feldolgozási közös munkafolyamat-mintáknak támogatásához.

A 2. verzióban át is telepíthet meglévő SSIS-csomagokat a felhőbe. Ez a művelet liftek, és átvált a SSIS-szolgáltatásként az Azure Data Factory, amely használja az egyik új funkciója integrációs futásidejű belül felügyelt. Forgó fel egy SSIS integrációs futásidejű a 2-es verzióját, amelyet hajtható végre, kezelésére, monitorozására és a felhőben SSIS-csomagok.

### <a name="control-flows-and-scale"></a>Vezérlő adatfolyamok és méretezhetőség 
Támogatásához a különböző integrációs adatfolyamok és minták a modern adatraktárban, a Data Factory engedélyezte egy új, rugalmas, csővezeték adatmodell, amely már nem kötődik-idősoros adatok. Ezzel a kiadással conditionals, az adatok adatcsatorna, vezérlési folyamatában fiókirodai modell, és kifejezetten kéri a paraméterek belül, és ezek a folyamatok között.

Most már rendelkezik a szabadságot, hogy a modell összes folyamat stílus, amely szükséges az adatok integráció és az igény szerinti vagy ismételten ütemezés, amely képes továbbítani. Néhány példa a mostantól elérhető vált folyamatokra:   

- Vezérlő adatfolyamok:
    - Lánc a tevékenységcsoportok szekvenciális adatcsatorna belül.
    - Egy folyamat fiókirodai tevékenységét.
    - Paraméterek
        - A feldolgozási sor szinten paramétereit, és adja át argumentumok, amíg a folyamat az igény szerinti vagy egy indít.
        - A tevékenységek képesek a folyamat számára továbbított argumentumok feldolgozására.
    - Egyéniállapot-átadás
        - A tevékenységkimeneteket (például állapotot) a folyamat soron következő tevékenysége képes feldolgozni.
    - Tárolók hurkolása
        - For-each 
- Eseményindító-alapú forgalom:
    - Az igény szerinti vagy valós idő szerint is elindítható a folyamatok.
- Különbözeti adatfolyamok:
    - A paraméterekkel, és adja meg a magas vízjel különbözeti másolására dimenzió vagy hivatkozás táblák áthelyezése relációs áruházbeli, vagy a helyszínen vagy a felhőben, az adatokat tölthet be a lake közben. 

További információkért lásd: [oktatóanyag: szabályozza a forgalom](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>SSIS-csomagok üzembe helyezése az Azure-ban 
Ha szeretné a SSIS-munkaterhelések, hozzon létre egy adat-előállító 2-es verzióját, és az Azure-SSIS-integrációs futásidejű kiépítéséhez. Az Azure-SSIS integrációs futásidejű rendszere Azure virtuális gépeken (csomópontok) felhőben való futtatásra a SSIS-csomagok a dedikált teljes körűen felügyelt fürtben. Részletes útmutatásért lásd: a [Azure telepítése SSIS-csomagok](tutorial-create-azure-ssis-runtime-portal.md) oktatóanyag. 
 

### <a name="sdks"></a>SDK-k
Ha a tapasztalt felhasználók és programozási felület keres, 2-es biztosít, amelyekkel szerzői, kezelése vagy a kedvenc IDE figyelje az adatcsatornák SDK-k széles skáláját.

- **.NET SDK**: A .NET SDK frissült a 2. verzióban. 
- **PowerShell**: A PowerShell-parancsmagok frissültek a 2. verzióban. A 2. verzió parancsmagjainak nevében szerepel a *DataFactoryV2* tag. For example, *Get-AzureRmDataFactoryV2*. 
- **Python SDK**: Ez az SDK a 2. verzióban lett bevezetve.
- **REST API**: A REST API frissült a 2. verzióban.  

A 2. verzióban frissített SDK-k visszamenőlegesen nem kompatibilisek az 1. verzió ügyfeleivel. 

### <a name="monitoring"></a>Figyelés
Jelenleg a 2. verzió kizárólag SDK-k használatával támogatja az adat-előállítók monitorozását. A portál még nem támogatja a 2. verziójú adat-előállítók monitorozását. 

## <a name="what-is-integration-runtime"></a>Mi az az integrációs futásidejű?
Integrációs futásidejű a számítási infrastruktúrától, így különböző hálózati környezetben a következő adatok integrációs képességeket biztosít Azure Data Factory használatával:

- **Adatátvitel**: adatok áthelyezése adatok-tárolók egy nyilvános hálózat és az adatok tárolók egy privát hálózaton (helyszíni vagy virtuális magánhálózati) között. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenységek mennyi**: a küldő és a figyelő átalakítása tevékenységek a különböző futó szolgáltatások, mint az Azure HDInsight, Azure Machine Learning, az Azure SQL Database, SQL Server, számítási.
- **SSIS-csomagok végrehajtása**: natív módon végrehajtja a felügyelt Azure számítási környezetben SSIS-csomagok.

Helyezze át, és az adatok átalakítása szükséges integrációs futásidejű egy vagy több példányát telepítheti. Integrációs futásidejű futtathatja az Azure nyilvános hálózaton vagy a privát hálózaton (a helyszínen, Azure virtuális hálózat vagy Amazon Web Services virtuális magánfelhő [VPC]). 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi az az integráció futtatókörnyezetek egyike számára vonatkozó korlátozást?
Nincs adat-előállítóban lehet integrációs futásidejű példányainak száma nem rögzített korlátját. Van, azonban a virtuális gép mag, az integrációs futásidejű SSIS-csomag végrehajtását előfizetésenként is használhat a számára vonatkozó korlátozást. További információkért lásd: [korlátozza a Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Mikor kell használni a 1-es verziójú helyett 2-es verzióját? 
Ha most ismerkedik az Azure Data Factory, kezdje közvetlenül 2-es verzióját. Ha már használja az 1-es, építse újra az adat-előállítók a 2-es verzióját.

> [!WARNING]
> Adat-előállító 2-es verziója előzetes verzió van, és nincs általános elérhetőségével (GA). Ezért az nem tartozik az Azure-szolgáltatás szolgáltatásiszint-szerződés (SLA) vállalt adat-előállítót, amely GA 1 változata 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Mik azok a 2-es verziójú legfelső szintű elveit?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory működjön együtt, a platform, amelyen létrehozhatja azt az adatvezérelt munkafolyamatok lépésekről, amelyekkel áthelyezése és az adatok átalakítása négy fő összetevőből tartalmazza.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. Egy folyamat tevékenységek végrehajtására munkaegység logikai csoportosítása. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például egy folyamatot, amely egy Azure blob származó adatok, és futtassa a Hive-lekérdezések HDInsight-fürtök az adatok particionálása tevékenységcsoport tartalmazhat. Az előnye, hogy egy folyamat használatával készletként, az egyes tevékenységek kezelése helyett a tevékenységek kezelése. Akkor is tanúsítványlánc-együtt a folyamat egymás után fog működni a tevékenységek, vagy akkor tudja azokat egymástól függetlenül működnek, párhuzamosan.

### <a name="activity"></a>Tevékenység
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. Használhat például egy *másolási* tevékenység egy adattárolóban tárolnak adatokat másolni egy másik adattárolóhoz. Hasonlóképpen a Hive tevékenység Hive-lekérdezések futó átalakító, vagy az adatok elemzése Azure HDInsight-fürtök is használhatja. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="data-sets"></a>Adatkészletek
Adatkészletek képviselő adatstruktúrák belül az adattároló, amely egyszerűen mutasson, vagy a használni kívánt a tevékenység bemeneti vagy kimeneti, adatokra hivatkoztak. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati karakterláncokhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Azt gondolja, hogy így: összekapcsolt szolgáltatás létesítendő kapcsolatot az adatforrás és adatkészlet jelenti. az adatok szerkezete. Például az Azure tárolás társított szolgáltatásának adja meg a kapcsolati karakterlánc csatlakozni az Azure Storage-fiók. És egy Azure-Blob adatkészlet határozza meg, a blob-tároló és az adatokat tartalmazó mappát.

Társított szolgáltatások adat-előállítóban két célja van:

- Képviselő egy *adattár* , amely magában foglalja, de nincs korlátozva, a helyszíni SQL Server-példány, Oracle adatbázis-példány, egy fájlmegosztást vagy egy Azure Blob storage-fiókot. Támogatott adattárolókhoz listájáért lásd: [másolási tevékenység során az Azure Data Factory](copy-activity-overview.md).
- Olyan *számítási erőforrás* jelölésére, amelyen végrehajtható a tevékenység. Például a HDInsight Hive fut egy HDInsight Hadoop-fürt. Átalakítás tevékenységek és a támogatott számítási környezetek listájáért lásd: [az Azure Data Factoryben az adatok átalakítása](transform-data.md).

### <a name="triggers"></a>Eseményindítók
Eseményindítók képviselik, amelyek meghatározzák, ha az a feldolgozási sor végrehajtása kezdődött el feldolgozási egység. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. Előzetes támogatott üzenőfal-órája Feladatütemező eseményindítót. 


### <a name="pipeline-runs"></a>Folyamatfuttatások
Egy folyamatot, futtassa a feldolgozási sor végrehajtása példánya. Általában hozható létre egy folyamatot, úgy, hogy a paraméterek az adatcsatorna definiált argumentumok futtatásához. Az argumentumok átadhatók kézzel vagy az eseményindító definícióját.

### <a name="parameters"></a>Paraméterek
Kulcs-érték párok írásvédett konfigurációban támogatottak. Egy folyamat paramétereit, és adja meg a definiált paraméterek argumentumai végrehajtása során futtatási környezetből. A futtatási környezet létrehozása egy eseményindítót, vagy az egy folyamatot, amely manuálisan hajtható végre. A folyamatok tevékenységei a paraméterértékeket dolgozzák fel.

Adatkészlet egy szigorú típusmegadású paraméter, és használja fel, vagy hivatkozzon entitás. Egy tevékenység hivatkozhat adatkészletek és lefoglalhatja a tulajdonságokat, amelyeket az adatkészlet definícióban meghatározott.

A csatolt "szolgáltatás" is egy szigorú típusmegadású paraméter, amely tartalmazza a kapcsolódási adatokat, hogy a tárolóban vagy számítási környezetekben. Akkor is felhasználhatja, vagy hivatkozzon entitás.

### <a name="control-flows"></a>Vezérlő adatfolyamok
Vezérlő adatfolyamok levezényelni a feldolgozási sor tevékenységek, amelyek tartalmaznak egy sorozatban lévő tevékenységcsoport, ugorhat, paraméterek, a folyamat szinten meghatározó lánckezelő, és adja meg, ha Ön argumentumokat meghívja a feldolgozási sor az igény szerinti vagy egy. Vezérlő adatfolyamok magába foglalja egyéni-állapot átadásakor, valamint a tárolók (Ez azt jelenti, hogy az egyes Iterátornak) hurkolást.


A Data Factory alapelveivel kapcsolatban további információkat a következő cikkekben talál:

- [Adatkészlet és összekapcsolt szolgáltatások](concepts-datasets-linked-services.md)
- [Folyamatok és tevékenységek](concepts-pipelines-activities.md)
- [Integrációs modul](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Mi az a Data Factory árképzési modellt?
Az Azure Data Factory díjszabása, lásd: [díjszabása adat-előállító](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="what-regions-support-azure-data-factory-version-2"></a>Mely régiókat támogatják az Azure Data Factory 2-es verzióját?
2-es verzióját az adat-előállítók jelenleg, az USA keleti régiója, USA keleti régiója 2 és Nyugat-Európában régiókban hozhat létre. Azonban egy adat-előállító segítségével integrációs futásidejű egy másik régióban adatok áthelyezése adattárolókhoz, küldése tevékenységeket célozzon számítási szolgáltatások vagy a feladó SSIS-csomagok között. További információkért lásd: [adat-előállító helyek](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hogyan maradhat naprakész információkkal, adat-előállító?
Azure Data Factory kapcsolatos legfrissebb információkért nyissa meg a következő helyekre:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentációjának kezdőlapján](/azure/data-factory)
- [A termék kezdőlap](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Műszaki részletes bemutatója 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Az 1-es és 2-es folyamatok egymás mellett futtassa is?
Nem. 2-es és az 1-es adat-előállítók verziót entitások (például a társított szolgáltatások, adatkészletek vagy adatcsatornák) nem tartalmazhat.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>Meg kell-e adatkészletek definiálhatja a 2-es verzióját?
Adatkészlet most már egy kötelező entitás a legtöbb tevékenység esetében. Az másolása, gépi tanulás, keresési, érvényesítése és egyéni tevékenységeket, hogy a séma- és egyéb metaadat-információkat a következő adatkészletben használjon az átalakításhoz szükség. A többi tevékenység már nem szükséges adatkészletek.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Tanúsítványlánc két tevékenység 2-es verzióját az adatkészlet nélkül is?
Igen. Tevékenységek a 2-es verzióját is láncolt anélkül, hogy az adatkészletek. Tevékenységek segítségével láncában találhatók a **dependsOn** tulajdonság az adatcsatorna JSON definíciójában. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Verziójának pedig 1, 2-es verzióját a támogatott tevékenységek? 
Igen, az összes 1-es verziójú tevékenység támogatottak a 2-es verzióját.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>2. verziójú adatcsatorna ütemezése? 
Az ütemező eseményindító használhatja egy 2. verziójú folyamat ütemezéséhez. Az eseményindító üzenőfal-órája naptár ütemezés használ, és használhatja folyamatok ütemezése, rendszeres időközönként vagy ismétlődő minták naptár-alapú (például hetente a következő 18: 00 hétfőjén és csütörtöki napokon 9 du.) használatával. További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Is paraméter is át egy adatcsatornának, futtassa a 2-es verzióját?
Igen, a 2-es verzió kiváló, legfelső szintű elvét támogatottak. Az adatcsatorna szintjén paraméterek megadása, és adja át argumentumok futtatása a feldolgozási sor az igény szerinti vagy eseményindító használatával futtassa.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Adhatja meg az adatcsatorna paraméterek alapértelmezett értéke? 
Igen. A paraméterek alapértelmezett értéke a folyamatok határozhatja meg. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Egy folyamat egy tevékenységének felhasználhat egy folyamat futtatása átadott argumentumok? 
Igen. A folyamat minden tevékenységét felhasználhat a paraméter értéke, amelynek a feldolgozási sor átadott, és futtassa a `@parameter` összeállításához. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Egy tevékenység kimeneti tulajdonság fogyasztják egy másik tevékenység? 
Igen. Egy tevékenység kimeneti képes használni a következő tevékenységet a `@activity` összeállításához.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hogyan do I problémamentes kezelésére egy tevékenység kimeneti null értéket? 
Használhatja a `@coalesce` szabályosan kezelni a null érték kifejezésében összeállításához. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Használhatok újrapróbálkozási és időtúllépés 2-es verzióját tevékenység szinten?
Igen. Annak a szabályozására tevékenységek a 2-es verziójú, mint 1-es verziójával, és is konfigurálhatja újra időtúllépés tevékenység szinten. 

## <a name="next-steps"></a>További lépések
A 2-es verzióját tartalmazó data factory létrehozása lépésenkénti útmutatójáért tekintse meg az alábbi oktatóanyagok:

- [Gyors üzembe helyezés: Tartalmazó data factory létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)

