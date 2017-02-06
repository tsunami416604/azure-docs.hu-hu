---
title: "Az Azure Data Lake Store áttekintése | Microsoft Docs"
description: "Megtudhatja, mi az Azure Data Lake Store, és milyen értékeket nyújt az egyéb adattárakhoz képest"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b3475057-9427-4492-a3af-25a802a23a79
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a1d30c77dd29a134017be7deec6bb50991e6a698


---
# <a name="overview-of-azure-data-lake-store"></a>Az Azure Data Lake Store áttekintése
Az Azure Data Lake Store egy vállalati szintű, nagy kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

> [!TIP]
> Fedezze fel az Azure Data Lake Store szolgáltatást a [Data Lake Store képzési terv](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) segítségével.
> 
> 

Az Azure Data Lake Store a (HDInsight-fürttel hozzáférhető) Hadoopból érhető el a WebHDFS-kompatibilis REST API-k használatával. Kifejezetten arra tervezték, hogy lehetővé tegye a tárolt adatok elemzését, továbbá nagy teljesítményt nyújtson az adatelemzési forgatókönyvekben. Minden olyan vállalati szintű képességet tartalmaz – biztonság, kezelhetőség, méretezhetőség, megbízhatóság és rendelkezésre állás –, amelyek elengedhetetlenek a valós vállalati alkalmazások esetében.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Az Azure Data Lake főbb képességei közé az alábbiak tartoznak.

### <a name="built-for-hadoop"></a>Hadoop-kompatibilis
Az Azure Data Lake-adattár egy Hadoop elosztott fájlrendszerrel (HDFS) kompatibilis Apache Hadoop-fájlrendszer, amely együttműködik a Hadoop-ökoszisztémával.  A WebHDFS API-t használó meglévő HDInsight-alkalmazásai vagy szolgáltatásai könnyen integrálhatók a Data Lake Store-ral. A Data Lake Store továbbá elérhetővé tesz egy WebHDFS-kompatibilis REST-felületet is az alkalmazásokhoz

A Data Lake Store-ban tárolt adatok könnyen elemezhetők a Hadoop elemzési keretrendszerek, például a MapReduce vagy a Hive segítségével. A Microsoft Azure HDInsight-fürtök úgy is üzembe helyezhetők és konfigurálhatók, hogy közvetlenül elérjék a Data Lake Store-ban tárolt adatokat.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok
Az Azure Data Lake Store korlátlan tárterületet biztosít, és különböző, elemzési célú adatok tárolására alkalmas. Nincsenek megkötések a fiókok méretének, a fájlméret, vagy a data lake-ben tárolt adatok mennyiségének tekintetében. Az egyéni fájlok mérete a kilobájtoktól a petabájtokig terjedhet, így bármilyen típusú adat tárolásához kiváló választás. A számos másolatnak köszönhetően az adatok megbízhatóan, valamint korlátlan ideig tárolhatók a data lake-ben.

### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva
Az Azure Data Lake Store nagy méretű elemzési rendszerek futtatására lett tervezve, amelyek jelentős átviteli sebességet igényelnek nagy mennyiségű adatok lekérdezéséhez és elemzéséhez. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

### <a name="enterprise-ready-highly-available-and-secure"></a>Felkészült a nagyvállalatok igényeire: Magas rendelkezésre állású és biztonságos
Az Azure Data Lake Store az iparági szabványnak megfelelő rendelkezésre állást és megbízhatóságot biztosít. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén. A vállalatok meglévő adatplatformjuk fontos részeként alkalmazhatják az Azure Data Lake-et megoldásaikban.

A Data Lake Store továbbá nagyvállalati szintű védelmet biztosít a tárolt adatok számára. További információ: [Az adatok védelme az Azure Data Lake Store-ban](#DataLakeStoreSecurity).

### <a name="all-data"></a>Minden adat
Az Azure Data Lake Store bármilyen adatot képes natív formátumában, módosítás vagy előzetes átalakítás nélkül tárolni. A Data Lake Store nem igényli egy séma definiálását az adatok betöltése előtt, így az egyéni elemzési keretrendszer maga értelmezheti az adatokat és definiálhat egy sémát az elemzés során. A tetszőleges méretű és formátumú fájlok tárolásának képessége lehetővé teszi, hogy a Data Lake Store strukturált, félig strukturált és strukturálatlan adatokat is kezelhessen.

Az Azure Data Lake Store adattárolói lényegében mappák és fájlok. A tárolt adatokat az SDK-k, az Azure Portal és az Azure PowerShell használatával kezelheti. Mindaddig, amíg a fenti felületeken keresztül és a megfelelő tárolók használatával helyezi el adatait a tárolóban, bármilyen típusú adatot tárolhat. A Data Lake Store nem kezeli különleges módon az adatokat a tárolt adatok típusa alapján.

## <a name="a-namedatalakestoresecurityasecuring-data-in-azure-data-lake-store"></a><a name="DataLakeStoreSecurity"></a>Az adatok védelme az Azure Data Lake Store-ban
Az Azure Data Lake Store az Azure Active Directory a hitelesítésével és hozzáférés-vezérlési listáival (ACL-ek) felügyeli az adatok hozzáférését.

| Szolgáltatás | Leírás |
| --- | --- |
| Authentication |Az Azure Data Lake Store integrálható az Azure Active Directory-val (AAD) az Azure Data Lake Store-ban tárolt összes adat identitás- és hozzáférés-kezelés érdekében. Az integrációnak köszönhetően az Azure Data Lake az AAD összes funkcióját használni tudja, többek között a többtényezős hitelesítést, a feltételes hozzáférést, a szerepköralapú hozzáférés-vezérlést, az alkalmazás-használat figyelését, a biztonsági figyelést és riasztást stb. Az Azure Data Lake Store támogatja az OAuth 2.0 protokollt a REST-felületen belüli hitelesítéshez. |
| Hozzáférés-vezérlés |Az Azure Data Lake Store a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával biztosítja a hozzáférés-vezérlést. A Data Lake Store nyilvános előzetes verziójában (a jelenlegi kiadásban) a hozzáférés-vezérlési listákat a legfelső szintű mappához, az almappákhoz vagy egyes fájlokhoz lehet engedélyezni. További információkat a hozzáférés-vezérlési listák Data Lake Store-környezetben való működéséről a következő témakörben talál: [Hozzáférés-vezérlés a Data Lake Store-ban](data-lake-store-access-control.md). |
| Titkosítás |A Data Lake Store biztosítja a fiókban tárolt adatok titkosítását. A Data Lake Store-fiók létrehozásakor megadhatja a titkosítási beállításokat. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. További információkat a titkosítással kapcsolatos konfigurációról a következő témakörben talál: [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md). |

Többet szeretne megtudni az adatok védelméről a Data Lake Store-ban? Kövesse az alábbi hivatkozásokat.

* Az adatok a Data Lake Store-ban való védelmére vonatkozó utasításokért lásd: [Az adatok védelme az Azure Data Lake Store-ban](data-lake-store-secure-data.md).
* Szeretne inkább videókat megtekinteni? [Tekintse meg ezt a videót](https://mix.office.com/watch/1q2mgzh9nn5lx) az adatok a védelméről a Data Lake Store-ban.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Az Azure Data Lake Store-ral kompatibilis alkalmazások
Az Azure Data Lake Store a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőjével kompatibilis. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható. Mindez azt jelenti, hogy a Data Lake Store tökéletes megoldás az adattárolási igények kielégítésére. Kövesse az alábbi hivatkozásokat, ha többet szeretne megtudni arról, hogyan használható a Data Lake Store a nyílt forráskódú összetevőkkel és az egyéb Azure-szolgáltatásokkal.

* A Data Lake Store-ral kompatibilis nyílt forráskódú alkalmazások listáját: [Az Azure Data Lake Store-ral kompatibilis alkalmazások és szolgáltatások](data-lake-store-compatible-oss-other-applications.md).
* Ha többet szeretne megtudni arról, hogyan használható a Data Lake Store más Azure-szolgáltatásokkal a lehetséges forgatókönyvek bővítése érdekében, lásd: [Integráció más Azure-szolgáltatásokkal](data-lake-store-integrate-with-other-services.md).
* Ha többet szeretne megtudni arról, hogyan használható a Data Lake Store az olyan forgatókönyvek esetében, mint az adatok bevitele, feldolgozása, letöltése és megjelenítése, lásd: [A Data Lake Store használatára vonatkozó forgatókönyvek](data-lake-store-data-scenarios.md).

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Mi az Azure Data Lake Store-fájlrendszer (adl://)?
A Data Lake Store-hoz az új fájlrendszer, (a HDInsight-fürttel hozzáférhető) AzureDataLakeFilesystem (adl://) használatával lehet hozzáférni a Hadoop-környezetekben. Az adl:// használatával az alkalmazások és szolgáltatások további teljesítmény-optimalizálásokat hasznosíthatnak, amelyek jelenleg nem érhetők el a WebHDFS-ben. Ennek eredményeképpen a Data Lake Store biztosítja azt a rugalmasságot, amellyel a legjobb teljesítményt érheti el az adl:// ajánlott beállításainak használatával, vagy fenntarthatja a meglévő kódot a WebHDFS API közvetlen használatával. Az Azure HDInsight teljes mértékben kihasználja az AzureDataLakeFilesystemet, hogy a lehető legjobb teljesítményt biztosítsa a Data Lake Store-on.

A Data Lake Store-ban lévő adatait az `adl://<data_lake_store_name>.azuredatalakestore.net` használatával érheti el. A Data Lake Store-ban lévő adatok elérésével kapcsolatos további információkért lásd: [A tárolt adatok tulajdonságainak megtekintése](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Hogyan kezdhetem meg az Azure Data Lake Store használatát?
Data Lake Store kiépítése az Azure Portal használatával: [A Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md). Az Azure Data Lake kiépítése után megtudhatja, hogyan használja az Azure Data Lake Analytics vagy az Azure HDInsight big data ajánlatokat a Data Lake Store-ral. .NET-alkalmazásokat készíthet is egy Azure Data Lake Store-fiók létrehozásához, illetve műveletek, például adatok letöltése, feltöltése stb. végrehajtásához.

* [Ismerkedés az Azure Data Lake Analytics szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure Data Lake Store használatának első lépései a .NET SDK-val](data-lake-store-get-started-net-sdk.md)

## <a name="data-lake-store-videos"></a>Data Lake Store-videók
Ha könnyebben tanul videók megtekintésével, a Data Lake Store számos szolgáltatáshoz biztosít videókat.

* [Azure Data Lake Store-fiók létrehozása](https://mix.office.com/watch/1k1cycy4l4gen)
* [Adatkezelés az Azure Data Lake Store-ban az Adatkezelő használatával](https://mix.office.com/watch/icletrxrh6pc)
* [Az Azure Data Lake Analytics és az Azure Data Lake Store összekapcsolása](https://mix.office.com/watch/qwji0dc9rx9k)
* [Az Azure Data Lake Store elérése a Data Lake Analytics használatával](https://mix.office.com/watch/1n0s45up381a8)
* [Az Azure HDInsight és az Azure Data Lake Store összekapcsolása](https://mix.office.com/watch/l93xri2yhtp2)
* [Az Azure Data Lake Store elérése a Hive és a Pig használatával](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Adatok másolása az Azure Data Lake Store-ba és az Azure Data Lake Store-ból a DistCP (Hadoop Distributed Copy) használatával](https://mix.office.com/watch/1liuojvdx6sie)
* [Adatok áthelyezése relációs források és az Azure Data Lake Store között az Apache Sqoop használatával](https://mix.office.com/watch/1butcdjxmu114)
* [Adatok előkészítése az Azure Data Lake Store-hoz készült Azure Data Factory használatával](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Az adatok védelme az Azure Data Lake Store-ban](https://mix.office.com/watch/1q2mgzh9nn5lx)




<!--HONumber=Dec16_HO1-->


