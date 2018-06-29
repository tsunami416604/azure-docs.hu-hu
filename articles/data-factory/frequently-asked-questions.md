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
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045949"
---
# <a name="azure-data-factory-faq"></a>Az Azure Data Factory – gyakori kérdések
Ez a cikk az Azure Data Factory kapcsolatban gyakran feltett kérdésekre adott válaszokat tartalmazza.  

## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory? 
Adat-előállító egy teljes körűen felügyelt, felhőalapú, adat-integrációs szolgáltatás, amely automatizálja a mozgás és az adatok átalakítása. Például olyan adat-előállítóval, amely a nyers átalakítása termékek berendezések fut az Azure Data Factory koordinálja a nyers adatokat gyűjthet, és irányítópulttá, készen áll a használatra információ meglévő szolgáltatások. 

Azure Data Factory használatával áthelyezni az adatokat a helyszíni és a felhő között az adatvezérelt munkafolyamatokat hozhat létre adattárolókhoz. Tud feldolgozni, és átalakítási adatok segítségével számítási szolgáltatások, például az Azure HDInsight, az Azure Data Lake Analytics és az SQL Server Integration Services (SSIS) integrációs futásidejű. 

A Data Factory az adatok feldolgozása hajthat végre, vagy egy Azure-alapú felhőalapú szolgáltatás, vagy saját önálló üzemeltetett számítási környezetben, például SSIS, SQL Server vagy Oracle. Miután létrehozott egy folyamatot, amely hajt végre a műveletet, amelyekre szüksége van, is az ütemezés futtatása rendszeres időközönként (például óránkénti, napi vagy heti), a idő ablak ütemezése vagy az eseményindító a feldolgozási sor az esemény előfordulása. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

### <a name="control-flows-and-scale"></a>Vezérlő adatfolyamok és méretezhetőség 
Különböző integrációs adatfolyamok és minták támogatásához a modern adatraktárban, adat-előállító lehetővé teszi, hogy rugalmas adatok csővezeték modellezési, amely tartalmazza többek között a feltételes végrehajtási mintáknak programozási teljes folyamata adatokat kimenetátirányítási ugorhat és explicit módon paraméterekkel belül, és ezek a folyamatok között. Folyamatábrán is magában foglalja a tevékenység indítási külső végrehajtási motorok és adatok folyamata lehetőségekről, beleértve a adatátvitelt jelölik a másolási tevékenység keresztül léptékű keresztül adatok átalakítása.

Adat-előállító időtartamot is egyetlen folyamat stílus, amely szükséges az adatok integráció és az igény szerinti vagy ismételten ütemezés, amely képes továbbítani a modell tartalmaz. Ez a modell lehetővé teszi, hogy néhány gyakori adatfolyamok a következők:   

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

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>A kód szabad kimenetátirányítási mechanizmusát használó műveletekről léptékű adatok átalakítása
Az új tooling webböngésző-alapú felületet biztosít a kódmentes folyamat létrehozása és telepítése során az a modern, interaktív webes révén.

A látványelemek adatainak fejlesztők és az adatok mérnökök az ADF webes felhasználói felületén a folyamatok létrehozásához használandó kódmentes tervezési környezetben. Teljesen integrálva van a Visual Studio Online Git, és a CI/CD-integrációval és iteratív fejlesztése a hibakeresési beállításokat biztosít.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Gazdag cross platform SDK-k tapasztalt felhasználók számára
Ha tapasztalt felhasználók és programozási felület keres, ADF V2, amely pótolhatja hozhatnak létre, kezelheti az SDK-k széles skáláját biztosítja, a kedvenc IDE használatával folyamatok figyelése
1.  Python SDK
2.  PowerShell parancssori felület
3.  C# SDK felhasználók is kihasználhatja a dokumentált REST API-k felületre az ADF 2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Ismétlődő fejlesztési és eszközökkel visual-hibakeresés
Az Azure Data Factory (ADF) visual eszközök lehetővé teszik a iteratív fejlesztési és hibakeresés folyamatát. A folyamatok hozhat létre, és tesztelje a hibakeresési funkció használatát a feldolgozási sor vászonra egysoros kód írása nélkül futtatja. A teszt futtatása eredményét megtekintheti a kimeneti ablakában a feldolgozási sor vászonra. A teszt futtatása sikeres, ha további tevékenységek hozzáadása a folyamat, és folytatni a hibakeresés ismétlődő módon. A teszt futtatása is megszakítja, ha folyamatban. Nem szükségesek a változások közzétételére a data factory szolgáltatásnak hibakeresési való kattintás előtt. Ez akkor hasznos, ahol győződjön meg arról, hogy az új módosításokat munkahelyi fejlesztői, gyári adatok munkafolyamatait frissítése előtt várt módon tesztelése, vagy környezetek termék kívánt forgatókönyvek. 

### <a name="deploy-ssis-packages-to-azure"></a>SSIS-csomagok üzembe helyezése az Azure-ban 
Ha szeretné a SSIS-munkaterhelések, adat-előállító létrehozása, és egy Azure-SSIS-integrációs futásidejű kiépítéséhez. Az Azure-SSIS integrációs futásidejű rendszere Azure virtuális gépeken (csomópontok) felhőben való futtatásra a SSIS-csomagok a dedikált teljes körűen felügyelt fürtben. Részletes útmutatásért lásd: a [Azure telepítése SSIS-csomagok](tutorial-create-azure-ssis-runtime-portal.md) oktatóanyag. 
 
### <a name="sdks"></a>SDK-k
Ha a tapasztalt felhasználók és az ADF biztosít programozási felület keres, szerzői, kezelése vagy a kedvenc IDE figyelje az adatcsatornák használható SDK-k széles skáláját. Nyelvi támogatás tartalmazza a .NET, a PowerShell, a többi pedig a Python.

### <a name="monitoring"></a>Figyelés
Az adat-előállítók PowerShell, SDK vagy a Visual figyelőeszközök a böngésző felhasználói felületen keresztül figyelheti. Figyelheti és igény szerint, eseményindító-alapú és egyéni adatfolyamok vezérelt és hatékony módon óra kezelése. Mégse meglévő feladatokat, lásd: hibák egy pillanat alatt is részletekbe menően tárhatják részletes hibaüzenet jelenik meg, és a problémák minden egytáblás, üveghatású a hibakeresési váltás, illetve a képernyők oda-vissza léptetése nélkül. 

### <a name="new-features-for-ssis-in-adf"></a>Az ADF SSIS új funkciói
2017 a kezdeti nyilvános előzetes kiadása óta Data Factory az SSIS hozzáadta a következő szolgáltatásokat:

-   Támogatja az három további konfigurációk/Variant típusú adatok az Azure SQL adatbázis (adatbázis) állomás SSIS-katalógus projektek/csomagok (SSISDB):
-   A virtuális hálózat végpontok Azure SQL-adatbázis
-   Felügyelt példány (MI)
-   Rugalmas készlet
-   Az Azure Resource Manager Virtual Network (VNet) klasszikus virtuális hálózatot, amely a későbbiekben – elavult funkciók mellett kártevőészlelést támogatása Ez lehetővé teszi az Azure-SSIS integrációs futásidejű (IR) egy virtuális hálózat szolgáltatás végpontok/MI az Azure SQL Database beállított virtuális hálózatba szúrjon/join / a helyszíni adatok elérése, lásd: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Támogatás az Azure Active Directory (AAD) hitelesítéshez felett az SSISDB - kapcsolódni az SQL-hitelesítés használatát teszik lehetővé az AAD-hitelesítés a az ADF felügyelt szolgáltatás identitásának (MSI)
-   Hozása a saját helyszíni SQL Server licenccel vett szolgáltatás érvényessége alatt jelentős költségmegtakarítást Azure hibrid juttatás (AHB) beállítás támogatása
-   Enterprise Edition az Azure-SSIS IR, amely lehetővé teszi támogatása speciális/prémium szolgáltatások, telepítse a további összetevők/fájlnévkiterjesztéseket, valamint a 3. fél ökoszisztéma, lásd: egyéni telepítés használja: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Lásd az ADF, amely lehetővé teszi az első osztályú SSIS-csomag végrehajtása tevékenységek ADF kimenetátirányítási meghívni, eseményindító és keresztül SSMS, ütemezni a SSIS szorosabb integrációt: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Mi az az integrációs futásidejű?
Integrációs futásidejű a számítási infrastruktúrától, így különböző hálózati környezetben a következő adatok integrációs képességeket biztosít Azure Data Factory használatával:

- **Adatátvitel**: az adatátvitel, integrációs futásidejű helyezi át az adatok között a forrás és cél adattárolókhoz, beépített összekötők, konverzióval, oszlopleképezés, és performant és méretezhető adatátvitel során.
- **Tevékenységek mennyi**: az átalakításhoz, integrációs futásidejű lehetőségét nyújtani az natív módon hajtható végre SSIS-csomagok.
- **SSIS-csomagok végrehajtása**: natív módon végrehajtja a felügyelt Azure számítási környezetben SSIS-csomagok. Az integrációs modul lehetővé teszi a különböző számítási szolgáltatásokon, például az Azure HDInsighton, az Azure Machine Learningen, az Azure SQL Database-en és az SQL Serveren futó átalakítási tevékenységek szervezését és figyelését.

Helyezze át, és az adatok átalakítása szükséges integrációs futásidejű egy vagy több példányát telepítheti. Integrációs futásidejű futtathatja az Azure nyilvános hálózaton vagy a privát hálózaton (a helyszínen, Azure virtuális hálózat vagy Amazon Web Services virtuális magánfelhő [VPC]). 

További információkért lásd: [Integrációs modul az Azure Data Factoryban](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Mi az az integráció futtatókörnyezetek egyike számára vonatkozó korlátozást?
Nincs adat-előállítóban lehet integrációs futásidejű példányainak száma nem rögzített korlátját. Van, azonban a virtuális gép mag, az integrációs futásidejű SSIS-csomag végrehajtását előfizetésenként is használhat a számára vonatkozó korlátozást. További információkért lásd: [korlátozza a Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Mik az Azure Data Factory legfelső szintű elveit?
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory működjön együtt, a platform, amelyen létrehozhatja azt az adatvezérelt munkafolyamatok lépésekről, amelyekkel áthelyezése és az adatok átalakítása négy fő összetevőből tartalmazza.

### <a name="pipelines"></a>Folyamatok
A data factory egy vagy több folyamattal rendelkezhet. Egy folyamat tevékenységek végrehajtására munkaegység logikai csoportosítása. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. Például egy folyamatot, amely egy Azure blob származó adatok, és futtassa a Hive-lekérdezések HDInsight-fürtök az adatok particionálása tevékenységcsoport tartalmazhat. Az előnye, hogy egy folyamat használatával készletként, az egyes tevékenységek kezelése helyett a tevékenységek kezelése. Akkor is tanúsítványlánc-együtt a folyamat egymás után fog működni a tevékenységek, vagy akkor tudja azokat egymástól függetlenül működnek, párhuzamosan.

### <a name="activity"></a>Tevékenység
Egy folyamatban a tevékenységek a feldolgozási lépéseket jelentik. Használhat például egy *másolási* tevékenység egy adattárolóban tárolnak adatokat másolni egy másik adattárolóhoz. Hasonlóképpen a Hive tevékenység Hive-lekérdezések futó átalakító, vagy az adatok elemzése Azure HDInsight-fürtök is használhatja. A Data Factory három típusú tevékenységet támogat: az adattovábbítási tevékenységeket, az adat-átalakítási tevékenységeket és a vezérlési tevékenységeket.

### <a name="datasets"></a>Adathalmazok
Az adatkészletek adatstruktúrákat jelölnek az adattárakon belül, amelyek egyszerűen rámutatnak vagy meghivatkozzák az adatokat, amelyeket a tevékenységekben be- vagy kimenetként használni szeretne. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Azt gondolja, hogy így: összekapcsolt szolgáltatás létesítendő kapcsolatot az adatforrás és adatkészlet jelenti. az adatok szerkezete. Például az Azure tárolás társított szolgáltatásának adja meg a kapcsolati karakterlánc csatlakozni az Azure Storage-fiók. És egy Azure-Blob adatkészlet határozza meg, a blob-tároló és az adatokat tartalmazó mappát.

Társított szolgáltatások adat-előállítóban két célja van:

- Képviselő egy *adattár* , amely magában foglalja, de nincs korlátozva, a helyszíni SQL Server-példány, Oracle adatbázis-példány, egy fájlmegosztást vagy egy Azure Blob storage-fiókot. Támogatott adattárolókhoz listájáért lásd: [másolási tevékenység során az Azure Data Factory](copy-activity-overview.md).
- Olyan *számítási erőforrás* jelölésére, amelyen végrehajtható a tevékenység. Például a HDInsight Hive fut egy HDInsight Hadoop-fürt. Átalakítás tevékenységek és a támogatott számítási környezetek listájáért lásd: [az Azure Data Factoryben az adatok átalakítása](transform-data.md).

### <a name="triggers"></a>Eseményindítók
Eseményindítók képviselik, amelyek meghatározzák, ha az a feldolgozási sor végrehajtása kezdődött el feldolgozási egység. A különböző típusú eseményekhez eltérő típusú eseményindítók tartoznak. 

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

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hogyan maradhat naprakész információkkal, adat-előállító?
Azure Data Factory kapcsolatos legfrissebb információkért nyissa meg a következő helyekre:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentációjának kezdőlapján](/azure/data-factory)
- [A termék kezdőlap](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Műszaki részletes bemutatója 

### <a name="how-can-i-schedule-a-pipeline"></a>Egy folyamat ütemezése? 
Használhatja a Feladatütemező eseményindító vagy idő ablak indítási folyamat ütemezéséhez. Az eseményindító üzenőfal-órája naptár ütemezés használ, és használhatja folyamatok ütemezése, rendszeres időközönként vagy ismétlődő minták naptár-alapú (például hetente a következő 18: 00 hétfőjén és csütörtöki napokon 9 du.) használatával. További információ: [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Is paraméter is át egy folyamat elindul?
Igen, első osztályú, legfelső szintű fogalma az ADF támogatottak. Az adatcsatorna szintjén paraméterek megadása, és adja át argumentumok futtatása a feldolgozási sor az igény szerinti vagy eseményindító használatával futtassa.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Adhatja meg az adatcsatorna paraméterek alapértelmezett értéke? 
Igen. A paraméterek alapértelmezett értéke a folyamatok határozhatja meg. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Egy folyamat egy tevékenységének felhasználhat egy folyamat futtatása átadott argumentumok? 
Igen. A folyamat minden tevékenységét felhasználhat a paraméter értéke, amelynek a feldolgozási sor átadott, és futtassa a `@parameter` összeállításához. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Egy tevékenység kimeneti tulajdonság fogyasztják egy másik tevékenység? 
Igen. Egy tevékenység kimeneti képes használni a következő tevékenységet a `@activity` összeállításához.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hogyan do I problémamentes kezelésére egy tevékenység kimeneti null értéket? 
Használhatja a `@coalesce` szabályosan kezelni a null érték kifejezésében összeállításához. 

## <a name="next-steps"></a>További lépések
Egy adat-előállító létrehozása lépésenkénti útmutatásért tekintse meg az alábbi oktatóanyagok:

- [Gyors üzembe helyezés: Tartalmazó data factory létrehozása](quickstart-create-data-factory-dot-net.md)
- [Oktatóanyag: Adatok másolása a felhőben](tutorial-copy-data-dot-net.md)
