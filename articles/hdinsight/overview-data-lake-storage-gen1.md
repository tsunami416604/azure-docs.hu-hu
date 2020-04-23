---
title: Az Azure Data Lake Storage Gen1 áttekintése a HDInsightban
description: A Data Lake Storage Gen1 áttekintése a HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873354"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Az Azure Data Lake Storage Gen1 áttekintése a HDInsightban

Az Azure Data Lake Storage Gen1 egy nagyvállalati szintű, nagyméretű tárház big data-elemzési számítási feladatokhoz. Az Azure Data Lake használatával bármilyen méretű, típusú és betöltési sebességű adatokat rögzíthet. És egy helyen a működési és feltáró elemzés.

A Data Lake Storage Gen1 elérése a Hadoopból (hdinsight-fürtesetén érhető el) a WebHDFS-kompatibilis REST API-k használatával. A Data Lake Storage Gen1 a tárolt adatok elemzésének engedélyezésére szolgál, és az adatelemzési forgatókönyvek ben a teljesítményre van hangolva. A Gen1 tartalmazza a valós vállalati használati esetekhez elengedhetetlen képességeket. Ezek közé tartozik a biztonság, a kezelhetőség, az alkalmazkodóképesség, a megbízhatóság és a rendelkezésre állás.

Az Azure Data Lake Storage Gen1 szolgáltatásról az [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)részletes áttekintése című témakörben olvashat bővebben.

A Data Lake Storage Gen1 legfontosabb képességei a következők.

## <a name="compatibility-with-hadoop"></a>Kompatibilitás a Hadoop-mal

A Data Lake Storage Gen1 egy Apache Hadoop fájlrendszer, amely kompatibilis a HDFS és a Hadoop környezettel.  A WebHDFS API-t használó HDInsight-alkalmazások vagy -szolgáltatások könnyen integrálhatók a Data Lake Storage Gen1 szolgáltatással. A Data Lake Storage Gen1 egy WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazások számára.

A Data Lake Storage Gen1-ben tárolt adatok könnyen elemezhetők a Hadoop analitikus keretrendszerek használatával. Keretrendszerek, például MapReduce vagy Hive. Az Azure HDInsight-fürtök kiépíthetők és konfigurálhatók úgy, hogy közvetlenül hozzáférjenek a Data Lake Storage Gen1-ben tárolt adatokhoz.

## <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

A Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és különböző típusú adatok elemzésre való tárolására alkalmas. Nem korlátozza a fiókméreteket és a fájlméreteket. Vagy az adattótóban tárolható adatok mennyisége. Az egyes fájlok mérete a kilobájttól a petabájtig terjed, így a Data Lake Storage Gen1 nagyszerű választás bármilyen típusú adat tárolására. Az adatok tárolása tartósan történik több példány készítésével. És nincs korlátozva, hogy mennyi ideig tárolhatók az adatok a Data Lake-ben.

## <a name="performance-tuning-for-big-data-analytics"></a>Teljesítményhangolás big data-elemzéshez

A Data Lake Storage Gen1 analitikus rendszerekhez készült. Nagy mennyiségű adat lekérdezéséhez és elemzéséhez nagy átviteli forgalmat igénylő rendszerek. A data lake a fájl egyes részeit több egyedi tárolókiszolgálóra is elosztja. Adatok elemzésekor ez a beállítás javítja az olvasási átviteli szintet, ha a fájlt párhuzamosan olvassa be.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Vállalati használatra való felkészültség: Magas rendelkezésre állás és biztonság

A Data Lake Storage Gen1 iparági szabványoknak megfelelő elérhetőséget és megbízhatóságot biztosít. Az adatelemek tárolása tartósan: a redundáns másolatok nem várt hibák ellen. A vállalatok a Data Lake Storage Gen1-et a meglévő adatplatformjuk fontos részeként használhatják a megoldásaikban.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is nyújt a tárolt adatok számára. További információ: [Adatok védelme az Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1)alkalmazásban.

## <a name="flexible-data-structures"></a>Rugalmas adatstruktúrák

A Data Lake Storage Gen1 bármilyen adatot tárolhat natív formátumban, ahogy van, előzetes átalakítások nélkül. A Data Lake Storage Gen1 nem igényel sémát az adatok betöltése előtt. Az egyes analitikus keretrendszer értelmezi az adatokat, és meghatározza a sémát az elemzés időpontjában. A Data Lake Storage Gen1 képes kezelni a strukturált adatokat. És félszerkezetelt és strukturálatlan adatok.

Data Lake Storage Gen1 tárolók adatok lényegében mappák és fájlok. A tárolt adatok on sdks, az Azure Portal és az Azure PowerShell használatával működik. Az ilyen felületekkel és tárolókkal a tárolóba helyezett adatok bármilyen adattípust tárolhatnak. A Data Lake Storage Gen1 nem végez speciális adatkezelést az adatok típusa alapján.

## <a name="data-security-in-data-lake-storage-gen1"></a>Adatbiztonság a Data Lake Storage Gen1 szolgáltatásban

A Data Lake Storage Gen1 az Azure Active Directoryt használja a hitelesítéshez, és hozzáférés-vezérlési listákat (ACL-eket) használ az adatokhoz való hozzáférés kezeléséhez.

| **Szolgáltatás** | **Leírás** |
| --- | --- |
| Hitelesítés |A Data Lake Storage Gen1 integrálható az Azure Active Directoryval (Azure AD) a Data Lake Storage Gen1-ben tárolt összes adat identitás- és hozzáférés-kezeléséhez. Az integráció miatt a Data Lake Storage Gen1 az Azure AD összes funkciójának előnyeit élvezi. Ezek a szolgáltatások a következők: többtényezős hitelesítés, feltételes hozzáférés és szerepköralapú hozzáférés-vezérlés. Emellett az alkalmazások használatának figyelése, a biztonsági figyelés és riasztás, és így tovább. A Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-csatolón belüli hitelesítéshez. Lásd: [Hitelesítés az Azure Data Lake Storage Gen1 szolgáltatásban az Azure Active Directory használatával](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |A Data Lake Storage Gen1 a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával biztosít hozzáférés-vezérlést. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. Az ACL-ek működéséről a Data Lake Storage Gen1 környezetében további információt a [Hozzáférés-vezérlés a Data Lake Storage Gen1 programban című témakörben talál.](../data-lake-store/data-lake-store-access-control.md) |
| Titkosítás |A Data Lake Storage Gen1 titkosítást is biztosít a fiókban tárolt adatokhoz. A titkosítási beállításokat a Data Lake Storage Gen1 fiók létrehozásakor adja meg. Választhatja az adatok titkosítását, vagy választhatja a nem titkosítást. További információ: [Encryption in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). A titkosítással kapcsolatos konfigurációk biztosításáról az [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával című témakörben](../data-lake-store/data-lake-store-get-started-portal.md)talál. |

Ha többet szeretne tudni az adatok védelméről a Data Lake Storage Gen1 szolgáltatásban, olvassa el [az Azure Data Lake Storage Gen1 szolgáltatásban tárolt adatok védelme.](../data-lake-store/data-lake-store-secure-data.md)

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>A Data Lake Storage Gen1-tel kompatibilis alkalmazások

A Data Lake Storage Gen1 kompatibilis a Hadoop környezetben lévő legtöbb nyílt forráskódú összetevővel. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Az alábbi hivatkozásokra kattintva többet is megtudhat arról, hogyan használható a Data Lake Storage Gen1 a nyílt forráskódú összetevőkkel és más Azure-szolgáltatásokkal.

* Lásd: [Nyílt forráskódú big data-alkalmazások, amelyek együttműködnek az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)használatával.
* Az [Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) a Data Lake Storage Gen1 más Azure-szolgáltatásokkal való használatának megértéséhez a forgatókönyvek szélesebb körének engedélyezéséhez.
* Az [Azure Data Lake Storage Gen1 használata big data-követelményekért olvassa](../data-lake-store/data-lake-store-data-scenarios.md)el.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fájlrendszer (adl://)

Hadoop környezetekben az új fájlrendszeren, az AzureDataLakeFilesystemrendszeren (adl://) keresztül érheti el a Data Lake Storage Gen1-et. A használt `adl://` alkalmazások és szolgáltatások teljesítménye olyan módon optimalizálható, amely jelenleg nem érhető el a WebHDFS fájlrendszerben. Ennek eredményeképpen az ajánlott adl:// használatával rugalmasan élhet a legjobb teljesítménnyel. Vagy a meglévő kód karbantartásával továbbra is használja a WebHDFS API-t közvetlenül. Az Azure HDInsight teljes mértékben kihasználja az AzureDataLakeFilesystem előnyeit, hogy a legjobb teljesítményt nyújtsa a Data Lake Storage Gen1 szolgáltatáson.

A Data Lake Storage Gen1 szolgáltatásban tárolt adatok elérése a következő URI-adatok használatával:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

A Data Lake Storage Gen1 szolgáltatásban található adatok eléréséről [a Tárolt adatokon elérhető műveletek című](../data-lake-store/data-lake-store-get-started-portal.md#properties)témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen2 áttekintése](./overview-data-lake-storage-gen2.md)