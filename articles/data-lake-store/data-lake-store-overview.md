---
title: Mi az Azure Data Lake Storage Gen1? | Microsoft Docs
description: Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store), és milyen értékeket nyújt az egyéb adattárakhoz képest – áttekintés
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681828"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Mi az Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Az Azure Data Lake Storage Gen1 egy vállalati szintű kapacitású adattár a big Data típusú adatok adatelemzési számítási feladatokhoz. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

Data Lake Storage Gen1 (HDInsight-fürttel érhető el) hadoopból érhető el a WebHDFS-kompatibilis REST API-k használatával. Van kialakítva, hogy engedélyezze a tárolt adatok elemzése és a rendszer nagy teljesítményt nyújtson az adatelemzési forgatókönyvekben. Data Lake Storage Gen1 minden vállalati szintű képességet tartalmaz: biztonsági, kezelhetőség, méretezhetőség, megbízhatóság és rendelkezésre állási.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Főbb képességek

Data Lake Storage Gen1 főbb képességei közé az alábbi.

### <a name="built-for-hadoop"></a>Hadoop-kompatibilis

Data Lake Storage Gen1 egy Apache Hadoop-fájlrendszer, amely a Hadoop elosztott fájlrendszer (HDFS) kompatibilis és együttműködik a Hadoop-ökoszisztéma. A meglévő HDInsight-alkalmazások vagy szolgáltatások, amelyek a WebHDFS API-val könnyen integrálhatók a Data Lake Storage Gen1. Data Lake Storage Gen1 továbbá elérhetővé tesz egy WebHDFS-kompatibilis REST-felület alkalmazásokhoz.

A Data Lake Storage Gen1 tárolt adatok könnyen elemezheti a Hadoop elemzési keretrendszerek, például a MapReduce vagy struktúrát. Az Azure HDInsight-fürtök kiépítése, és konfigurálja őket közvetlenül a Data Lake Storage Gen1 tárolt adatok eléréséhez.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

Data Lake Storage Gen1 korlátlan tárterületet biztosít, és különféle elemzési adatokat tárolhat. Ez nem megkötések a fiókok méretének, a fájlok méretét vagy a data lake-ben tárolt adatok mennyisége. Egyes fájlok mérete a kilobájtoktól a petabájtokig terjedhet. Adatokat azáltal, hogy több példányt tartósan tárolja. Nincs megszabva, az időtartam, amelyre az adatok tárolhatók a data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva

Data Lake Storage Gen1 lekérdezéséhez és kielemzéséhez nagy mennyiségű adat teljesítményt igénylő, nagy méretű elemzési rendszerek futtatására lett tervezve. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

### <a name="enterprise-ready-highly-available-and-secure"></a>A vállalati használatra: Magas rendelkezésre állású és biztonságos

Data Lake Storage Gen1 biztosít az iparági szabványnak megfelelő rendelkezésre állás és megbízhatóság. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén.

Data Lake Storage Gen1 is biztosít a nagyvállalati szintű biztonsággal, a tárolt adatok számára. További információkért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Az összes adat

Data Lake Storage Gen1 bármilyen adatot tárolhat natív formátumában, módosítás vagy előzetes átalakítás nélkül. Data Lake Storage Gen1 igényli egy séma előtt az adatok betöltése, így az egyéni elemzési keretrendszer maga értelmezheti az adatokat, és definiálhat egy sémát az elemzés idején. Tetszőleges méretű és formátumú fájlok tárolásának képessége lehetővé teszi a Data Lake Storage Gen1 strukturált, félig strukturált és strukturálatlan adatok kezelésére.

Data Lake Storage Gen1 tárolókból adatok olyan lényegében mappák és fájlok. A tárolt adatok SDK-k, az Azure portal és az Azure Powershell használatával kezelheti. Ha az adatok a tárolóba a felületek és a megfelelő tárolók használatával helyezi, bármilyen típusú adatot is tárolhatja. Data Lake Storage Gen1 nem kezeli különleges módon az adatokat a tárolt adatok típusa alapján hajt végre.

## <a name="DataLakeStoreSecurity"></a>Az adatok védelme

Data Lake Storage Gen1 használja az Azure Active Directory (Azure AD-) hitelesítés és hozzáférés-listákat (ACL) való hozzáférés kezelése szabályozhatja az adatokhoz.

| Szolgáltatás | Leírás |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integrálható az identitás és hozzáférés-kezelés az Azure AD-hez a Data Lake Storage Gen1 tárolt összes adatot. Az integráció miatt minden Azure AD-ből a Data Lake Storage Gen1 előnyei például a többtényezős hitelesítés, a feltételes hozzáférés, szerepköralapú hozzáférés-vezérlés, Alkalmazáshasználat monitorozását, biztonsági monitoringgal és riasztások szolgáltatás, és így tovább. Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [hitelesítés a Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával. Hozzáférés-vezérlési listák a gyökérkönyvtáron, az almappák és az egyes fájlokon is engedélyezheti. További információ a hozzáférés-vezérlési listák Data Lake Storage Gen1 kontextusában működése: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Titkosítás |Data Lake Storage Gen1 is biztosít a fiókban tárolt adatok titkosítását. A Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Ha szeretné, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélkül. További információkért lásd: [titkosítást a Data Lake Storage Gen1](data-lake-store-encryption.md). Útmutatás a titkosítással kapcsolatos konfigurációk megadására: [Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](data-lake-store-get-started-portal.md). |

A Data Lake Storage Gen1 biztonságos adatainak kapcsolatos utasításokért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Alkalmazáskompatibilitás

Data Lake Storage Gen1 kompatibilis a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőkkel. Azt is jól integrálható más Azure-szolgáltatásokkal. Ismerje meg, hogyan használja a Data Lake Storage Gen1 nyílt forráskódú összetevőkkel több és más Azure-szolgáltatások, használja az alábbi hivatkozásokat:

- Lásd: [alkalmazások és szolgáltatások kompatibilis az Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) Data Lake Storage Gen1 együttműködésre képes nyílt forráskódú alkalmazások listáját.
- Lásd: [integrálása más Azure szolgáltatásokkal](data-lake-store-integrate-with-other-services.md) megérteni a Data Lake Storage Gen1 használata más Azure-szolgáltatások széles körének forgatókönyvek engedélyezéséhez.
- Lásd: [Data Lake Storage Gen1 használatára vonatkozó forgatókönyvek](data-lake-store-data-scenarios.md) megtudhatja, hogyan használható a Data Lake Storage Gen1 tölt be adatot, adatok feldolgozása, letöltése és vizualizációja – egyéb felhasználási helyzetek.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 fájlrendszer

Data Lake Storage Gen1 elérhető keresztül a fájlrendszer AzureDataLakeFilesystem (adl: / /) a (HDInsight-fürttel érhető el) Hadoop-környezetekben. Alkalmazások és szolgáltatások, amelyek használják az adl: / / kihasználhatja további teljesítményoptimalizálás segíti, amelyek nem érhető el a WebHDFS-ben. Ennek eredményeképpen Data Lake Storage Gen1 biztosít, a rugalmasságot, vagy győződjön meg arról, használata, a legjobb teljesítmény elérése érdekében ajánlott beállítással, adl: / / vagy fenntarthatja a meglévő kód továbbra is a WebHDFS API közvetlen használja. Az Azure HDInsight teljes mértékben kihasználja az a legjobb teljesítményt biztosítja a Data Lake Storage Gen1 AzureDataLakeFilesystem.

Férhet hozzá az adataihoz, a Data Lake Storage Gen1 használatával `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Data Lake Storage Gen1 lévő adatok elérésével kapcsolatos további információkért lásd: [a tárolt adatok tulajdonságainak megtekintése](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>További lépések

- [Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](data-lake-store-get-started-portal.md)
- [Ismerkedés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
- [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)