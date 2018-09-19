---
title: Az Azure Data Lake Storage Gen1 áttekintése |} A Microsoft Docs
description: Ismerje meg, milyen Data Lake Storage Gen1 van (korábbi nevén Azure Data Lake Store), és milyen értékeket nyújt az egyéb adattárakhoz képest
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b733a0bec20c7abae5df41acd74284bdf75bca2c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124133"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 áttekintése

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Az Azure Data Lake Storage Gen1 egy vállalati szintű kapacitású adattár a big Data típusú adatok adatelemzési számítási feladatokhoz. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

> [!TIP]
> Használja a [Data Lake Storage Gen1 képzési](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) megismerheti a Data Lake Storage Gen1 szolgáltatást elindítani.
> 
> 

Data Lake Storage Gen1 (HDInsight-fürttel érhető el) hadoopból érhető el a WebHDFS-kompatibilis REST API-k használatával. Kifejezetten arra tervezték, hogy lehetővé tegye a tárolt adatok elemzését, továbbá nagy teljesítményt nyújtson az adatelemzési forgatókönyvekben. Minden olyan vállalati szintű képességet tartalmaz – biztonság, kezelhetőség, méretezhetőség, megbízhatóság és rendelkezésre állás –, amelyek elengedhetetlenek a valós vállalati alkalmazások esetében.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Data Lake Storage Gen1 főbb képességei közé az alábbi.

### <a name="built-for-hadoop"></a>Hadoop-kompatibilis
Data Lake Storage Gen1 egy Apache Hadoop-fájlrendszer kompatibilis a Hadoop elosztott fájlrendszer (HDFS) és a Hadoop környezetben működik.  A meglévő HDInsight-alkalmazások vagy szolgáltatások, amelyek a WebHDFS API-val könnyen integrálhatók a Data Lake Storage Gen1. Data Lake Storage Gen1 továbbá elérhetővé tesz egy WebHDFS-kompatibilis REST-felület alkalmazásokhoz

A Data Lake Storage Gen1 tárolt adatok könnyen elemezhetők a Hadoop elemzési keretrendszerek, például a MapReduce vagy struktúrát. A Microsoft Azure HDInsight-fürtök kell létrehozni és konfigurálni a Data Lake Storage Gen1 tárolt adatok közvetlen elérésére.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok
Data Lake Storage Gen1 korlátlan tárterületet biztosít, és különféle elemzési adatok tárolására alkalmas. Nincsenek megkötések a fiókok méretének, a fájlméret, vagy a data lake-ben tárolt adatok mennyiségének tekintetében. Az egyéni fájlok mérete a kilobájtoktól a petabájtokig terjedhet, így bármilyen típusú adat tárolásához kiváló választás. A számos másolatnak köszönhetően az adatok megbízhatóan, valamint korlátlan ideig tárolhatók a data lake-ben.

### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva
Data Lake Storage Gen1 az nagy méretű lekérdezéséhez és kielemzéséhez nagy mennyiségű adat teljesítményt igénylő elemző rendszerek futtatására lett tervezve. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

### <a name="enterprise-ready-highly-available-and-secure"></a>Felkészült a nagyvállalatok igényeire: Magas rendelkezésre állású és biztonságos
Data Lake Storage Gen1 biztosít az iparági szabványnak megfelelő rendelkezésre állás és megbízhatóság. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén. Vállalatok számára fontos része annak a létező adatplatform, használhatja saját megoldások Data Lake Storage Gen1.

Data Lake Storage Gen1 is biztosít a nagyvállalati szintű biztonsággal, a tárolt adatok számára. További információkért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Minden adat
Data Lake Storage Gen1 is tárol adatokat adatát natív formátumban, ez, módosítás vagy előzetes átalakítás nélkül. Data Lake Storage Gen1 igényli egy séma előtt az adatok betöltése, így az egyéni elemzési keretrendszer maga értelmezheti az adatokat, és definiálhat egy sémát az elemzés idején. Tetszőleges méretű és formátumú fájlok tárolásának lehetővé teszi a Data Lake Storage Gen1 strukturált, félig strukturált és strukturálatlan adatok kezelésére.

Data Lake Storage Gen1 tárolókból adatok olyan lényegében mappák és fájlok. A tárolt adatokat az SDK-k, az Azure Portal és az Azure PowerShell használatával kezelheti. Mindaddig, amíg a fenti felületeken keresztül és a megfelelő tárolók használatával helyezi el adatait a tárolóban, bármilyen típusú adatot tárolhat. Data Lake Storage Gen1 nem kezeli különleges módon az adatokat a tárolt adatok típusa alapján hajt végre.

## <a name="DataLakeStoreSecurity"></a>Az adatok védelme az Data Lake Storage Gen1
Data Lake Storage Gen1 használja az Azure Active Directory a hitelesítéshez és hozzáférés-listákat (ACL) való hozzáférés kezelése szabályozhatja az adatokhoz.

| Szolgáltatás | Leírás |
| --- | --- |
| Hitelesítés |Data Lake Storage Gen1 együttműködik a Data Lake Storage Gen1 tárolt összes adat identitás- és hozzáférés-kezelés az Azure Active Directory (AAD). Eredményeként az integrációt, a Data Lake Storage Gen1 előnyöket AAD összes funkcióját, többek között a multi-factor authentication szolgáltatás, a feltételes hozzáférés, a szerepköralapú hozzáférés-vezérlés, a Alkalmazáshasználat monitorozását, biztonsági figyelést és riasztást stb. Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [hitelesítés a Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. A hozzáférés-vezérlési listák Data Lake Storage Gen1 összefüggésben működéséről további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Titkosítás |Data Lake Storage Gen1 is biztosít a fiókban tárolt adatok titkosítását. A Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. További információkért lásd: [titkosítást a Data Lake Storage Gen1](data-lake-store-encryption.md). Útmutatás a titkosítással kapcsolatos konfigurációk megadására: [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md). |

További információ az adatok védelme az Data Lake Storage Gen1 szeretne? Kövesse az alábbi hivatkozásokat.

* A Data Lake Storage Gen1 biztonságos adatainak kapcsolatos utasításokért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).
* Szeretne inkább videókat megtekinteni? [Ebben a videóban](https://mix.office.com/watch/1q2mgzh9nn5lx) a Data Lake Storage Gen1 tárolt adatok megóvására.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 kompatibilis alkalmazások
Data Lake Storage Gen1 a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható. Ez lehetővé teszi Data Lake Storage Gen1 tökéletes megoldás az adatok tárolási igényeinek. Ha többet szeretne megtudni, hogyan használható Data Lake Storage Gen1, mind a nyílt forráskódú összetevőit, valamint a más Azure-szolgáltatások az alábbi hivatkozásokat követve.

* Lásd: [alkalmazások és szolgáltatások kompatibilis az Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) Data Lake Storage Gen1 együttműködésre képes nyílt forráskódú alkalmazások listáját.
* Lásd: [integrálása más Azure szolgáltatásokkal](data-lake-store-integrate-with-other-services.md) megérteni, hogyan Data Lake Storage Gen1 használható más Azure-szolgáltatások széles körének forgatókönyvek engedélyezéséhez.
* Lásd: [Data Lake Storage Gen1 használatára vonatkozó forgatókönyvek](data-lake-store-data-scenarios.md) megtudhatja, hogyan használható a Data Lake Storage Gen1 tölt be adatot, adatok feldolgozása, letöltése és vizualizációja – egyéb felhasználási helyzetek.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Mi a Data Lake Storage Gen1 fájlrendszer (adl: / /)?
Data Lake Storage Gen1 elérhető keresztül az új fájlrendszer, a AzureDataLakeFilesystem (adl: / /), a Hadoop-környezetekben (a HDInsight-fürt érhető el). Az adl:// használatával az alkalmazások és szolgáltatások további teljesítmény-optimalizálásokat hasznosíthatnak, amelyek jelenleg nem érhetők el a WebHDFS-ben. Eredményeképpen Data Lake Storage Gen1 rugalmasságot biztosít, amellyel a legjobb teljesítmény érdekében az ajánlott beállítással, adl: / / vagy fenntarthatja a meglévő kód továbbra is a WebHDFS API közvetlen használja. Az Azure HDInsight teljes mértékben kihasználja az a legjobb teljesítményt biztosítja a Data Lake Storage Gen1 AzureDataLakeFilesystem.

Férhet hozzá az adataihoz, a Data Lake Storage Gen1 használatával `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. A Data Lake Storage Gen1 az adatokkal való további információkért lásd: [a tárolt adatok tulajdonságainak megtekintése](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>További lépések

* [Get Started with Data Lake Storage Gen1 az Azure Portal használatával](data-lake-store-get-started-portal.md)
* [Az Azure Data Lake Storage Gen1 használatának első lépései a .NET SDK használata](data-lake-store-get-started-net-sdk.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)