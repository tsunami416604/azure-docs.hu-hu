---
title: Mi az Azure Data Lake Storage Gen1? | Microsoft Docs
description: A Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) áttekintése, valamint a más adattárakon biztosított érték áttekintése
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118807"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Mi az Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Az Azure Data Lake Storage Gen1 egy nagyvállalati szintű, nagyméretű tárház big data-elemzési számítási feladatokhoz. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

A Data Lake Storage Gen1 a Hadoopból érhető el (hdinsight-fürttel érhető el) a WebHDFS-kompatibilis REST API-k használatával. Úgy tervezték, hogy lehetővé tegye a tárolt adatok elemzését, és az adatelemzési forgatókönyvek teljesítményére van hangolva. A Data Lake Storage Gen1 minden nagyvállalati szintű képességet tartalmaz: biztonságot, kezelhetőséget, méretezhetőséget, megbízhatóságot és rendelkezésre állást.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Főbb képességek

A Data Lake Storage Gen1 néhány kulcsfontosságú képessége a következők.

### <a name="built-for-hadoop"></a>Hadoop-kompatibilis

A Data Lake Storage Gen1 egy Apache Hadoop fájlrendszer, amely kompatibilis a Hadoop Distributed File System (HDFS) rendszerrel, és együttműködik a Hadoop ökoszisztémával. A WebHDFS API-t használó meglévő HDInsight-alkalmazások vagy -szolgáltatások könnyen integrálhatók a Data Lake Storage Gen1 szolgáltatással. A Data Lake Storage Gen1 egy WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazások számára.

A Data Lake Storage Gen1-ben tárolt adatok könnyen elemezhetők a Hadoop analitikus keretrendszerek, például a MapReduce vagy a Hive használatával. Azure HDInsight-fürtöket hozhat ki, és konfigurálhatja őket a Data Lake Storage Gen1-ben tárolt adatok közvetlen elérésére.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

A Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és számos adatot tárolhat elemzéshez. Nem korlátozza a fiókméretet, a fájlméretet vagy az adattóban tárolható adatok mennyiségét. Az egyes fájlok a kilobájttól a petabájtig terjedhetnek. Az adatok tárolása tartósan történik több példány készítésével. Nincs korlátozva, hogy mennyi ideig tárolhatók az adatok az adattóban.

### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva

A Data Lake Storage Gen1 nagyméretű analitikus rendszerek futtatására készült, amelyek nagy mennyiségű adat lekérdezéséhez és elemzéséhez nagy mennyiségű adat lekérdezéséhez és elemzéséhez szükséges. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

### <a name="enterprise-ready-highly-available-and-secure"></a>Nagyvállalati használatra kész: Magas rendelkezésre állás és biztonság

A Data Lake Storage Gen1 iparági szabványoknak megfelelő elérhetőséget és megbízhatóságot biztosít. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is nyújt a tárolt adatokszámára. További információ: [Adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity)alkalmazásban.

### <a name="all-data"></a>Minden adat

A Data Lake Storage Gen1 bármilyen adatot tárolhat natív formátumban, előzetes átalakítások nélkül. A Data Lake Storage Gen1 nem követeli meg, hogy az adatok betöltése előtt definiáljon sémát, így az adatok értelmezésének és a séma meghatározásának időpontja az egyes analitikus keretrendszerre marad. Tetszőleges méretű és formátumú fájlok tárolása lehetővé teszi a Data Lake Storage Gen1 számára a strukturált, félig strukturált és strukturálatlan adatok kezelését.

Data Lake Storage Gen1 tárolók adatok lényegében mappák és fájlok. A tárolt adatok sdk-k, az Azure portal és az Azure Powershell használatával működik. Ha ezekkel a felületekkel és a megfelelő tárolók használatával helyezi el az adatokat az áruházban, bármilyen típusú adatot tárolhat. A Data Lake Storage Gen1 nem végez különleges adatkezelést az általa tárolt adatok típusa alapján.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Adatok védelme

A Data Lake Storage Gen1 az Azure Active Directoryt (Azure AD) használja a hitelesítéshez, és hozzáférés-vezérlési listákat (ACL-k) az adatokhoz való hozzáférés kezeléséhez.

| Szolgáltatás | Leírás |
| --- | --- |
| Hitelesítés |A Data Lake Storage Gen1 integrálható az Azure AD-vel a Data Lake Storage Gen1-ben tárolt összes adat identitás- és hozzáférés-kezeléséhez. Az integráció miatt a Data Lake Storage Gen1 az Összes Azure AD-szolgáltatás előnyeit élvezi, például a többtényezős hitelesítést, a feltételes hozzáférést, a szerepköralapú hozzáférés-vezérlést, az alkalmazáshasználat figyelését, a biztonsági figyelést és riasztást és így tovább. A Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-csatolón belüli hitelesítéshez. Lásd: [Data Lake Storage Gen1 hitelesítés.](data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |A Data Lake Storage Gen1 a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával biztosít hozzáférés-vezérlést. Engedélyezheti az ACL-eket a gyökérmappában, az almappákban és az egyes fájlokon. Az ACL-ek működéséről a Data Lake Storage Gen1 környezetében további információt a [Hozzáférés-vezérlés a Data Lake Storage Gen1 programban című témakörben talál.](data-lake-store-access-control.md) |
| Titkosítás |A Data Lake Storage Gen1 titkosítást is biztosít a fiókban tárolt adatokhoz. A titkosítási beállításokat a Data Lake Storage Gen1 fiók létrehozásakor adja meg. Választhatja az adatok titkosítását, vagy választhatja a nem titkosítást. További információ: [Encryption in Data Lake Storage Gen1](data-lake-store-encryption.md). A titkosítással kapcsolatos konfigurációk biztosításáról az [Azure Portal használatával a Data Lake Storage Gen1 használatának első lépései című témakörben](data-lake-store-get-started-portal.md)talál. |

Az adatok védelméről a Data Lake Storage Gen1 szolgáltatásban az [Azure Data Lake Storage Gen1 szolgáltatásban található adatok védelme témakörben talál.](data-lake-store-secure-data.md)

## <a name="application-compatibility"></a>Alkalmazáskompatibilitás

A Data Lake Storage Gen1 kompatibilis a Hadoop ökoszisztéma legtöbb nyílt forráskódú összetevőjével. Emellett jól integrálható más Azure-szolgáltatásokkal is. Ha többet szeretne megtudni arról, hogyan használhatja a Data Lake Storage Gen1-et nyílt forráskódú összetevőkkel és más Azure-szolgáltatásokkal, használja az alábbi hivatkozásokat:

- [Az Azure Data Lake Storage Gen1 szolgáltatással kompatibilis alkalmazások és szolgáltatások1](data-lake-store-compatible-oss-other-applications.md) című témakörben a Data Lake Storage Gen1 szolgáltatással interoperábilis nyílt forráskódú alkalmazások listáját tartalmazza.
- [A](data-lake-store-integrate-with-other-services.md) Data Lake Storage Gen1 más Azure-szolgáltatásokkal való használatának a forgatókönyvek szélesebb körének engedélyezéséhez.
- [A Data Lake Storage Gen1 használatával kapcsolatban](data-lake-store-data-scenarios.md) tekintse meg a Data Lake Storage Gen1 használatának forgatókönyveit olyan forgatókönyvekben, mint az adatok betöltése, az adatok feldolgozása, az adatok letöltése és az adatok megjelenítése.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 fájlrendszer

A Data Lake Storage Gen1 az AzureDataLakeFilesystem (adl://) fájlrendszeren keresztül érhető el Hadoop-környezetekben (hdinsight-fürttel érhető el). A adl:// használó alkalmazások és szolgáltatások kihasználhatják a webHDFS-ben jelenleg nem elérhető további teljesítményoptimalizálást. Ennek eredményeképpen a Data Lake Storage Gen1 rugalmasságot biztosít a legjobb teljesítmény kihasználásához a adl:// használatának ajánlott beállításával, vagy a meglévő kód karbantartása a WebHDFS API közvetlen használatával. Az Azure HDInsight teljes mértékben kihasználja az AzureDataLakeFilesystem rendszert, hogy a legjobb teljesítményt nyújtsa a Data Lake Storage Gen1 szolgáltatáson.

A Data Lake Storage Gen1 segítségével `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`érheti el adatait. A Data Lake Storage Gen1 szolgáltatásadatainak eléréséről [a Tárolt adatok tulajdonságainak megtekintése](data-lake-store-get-started-portal.md#properties)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

- [Első lépések a Data Lake Storage Gen1 használatával az Azure Portalon](data-lake-store-get-started-portal.md)
- [A Data Lake Storage Gen1 használatának első lépései a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
- [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)