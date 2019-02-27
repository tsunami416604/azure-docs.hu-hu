---
title: Azure Data Lake Storage Gen2 érintő forgatókönyvek |} A Microsoft Docs
description: Megismerheti a különböző forgatókönyveket és eszközök használatával, mely adatokat is betöltött, a feldolgozás, a letöltött és a webalkalmazásban jelennek meg a Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: ec97cf903a37465f98007750fc41edc0952ab3a6
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889596"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen2 használata big data-követelményekhez

Big data-feldolgozáshoz négy fő szakaszból áll:

> [!div class="checklist"]
> * Nagy mennyiségű adatot tölt be egy adattár, valós idejű, illetve kötegek formájában
> * Az adatok feldolgozása
> * Az adatok letöltése
> * Az adatok megjelenítése

Először hozzon létre egy tárfiókot és a egy fájlrendszer. Ezután adja meg az adatokhoz való hozzáférés. Ez a cikk első néhány szakaszát e feladatok elvégzéséhez nyújt segítséget. Fennmaradó azt fogja jelölje ki a beállításokat és az eszközök az egyes feldolgozási fázisokban.

## <a name="create-a-data-lake-storage-gen2-account"></a>A Data Lake Storage Gen2-fiók létrehozása

Egy Data Lake Storage Gen2 fiók egy tárfiókot, amelyet a hierarchikus névtérrel rendelkezik. 

Hozzon létre egyet, tekintse meg [a rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Hozzon létre egy fájlrendszer

A *fájlrendszer* fájlok és mappák tárolója. Ezek a tárfiókban lévő adatok bevitele a kezdéshez legalább egy van szüksége.  Ez egy lista eszközöket tartalmazza, amelyek segítségével hozza létre őket.

|Eszköz | Útmutatás |
|---|--|
|Azure Storage Explorer | [Hozzon létre egy fájlrendszer a Storage Explorer használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [A Blob-tárolóba vagy a fájlmegosztás létrehozása AzCopyV10 használatával](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Hadoop-fájl (HDFS) rendszer parancssori felület (CLI) a HDInsight |[Hozzon létre egy fájlrendszer HDFS a HDInsight használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Az Azure Databricks-jegyzetfüzet-kódban|[Hozzon létre egy storage-fiók fájlrendszer (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Hozzon létre egy fájlrendszert és csatlakoztathatom azokat (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

A legegyszerűbb fájlrendszerek létrehozása a Storage Explorerben vagy az AzCopy használatával. Egy kicsit több munkát fájlrendszerek létrehozása a HDInsight és a Databricks használatával vesz igénybe. Azonban ha szeretné használni a HDInsight vagy a Databricks-fürtök ennek ellenére dolgozhatja fel az adatokat, majd is először hozza létre a fürtöket, és a HDFS parancssori felület használatával a létrehozás fájlrendszerek.  

## <a name="grant-access-to-the-data"></a>Az adatokhoz való hozzáférés engedélyezése

Mielőtt elkezdené az adatok bevitele, állítsa be a fiókját, és a fiókban lévő adatok megfelelő hozzáférési engedélyekkel.

Hozzáférést három módja van:

* Egy felhasználó, csoport, felhasználó által felügyelt identitás vagy egyszerű szolgáltatás hozzárendelése egy ilyen szerepkörbe:

  [Storage-Blobadatok olvasója](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Storage-Blobadatok Közreműködője](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Tárolási Blob adatok tulajdonosa](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Használjon egy közös hozzáférésű Jogosultságkód (SAS) tokent.

* Használja a tárfiók kulcsára.

Ez a táblázat bemutatja, hogyan minden egyes Azure-szolgáltatás vagy eszköz számára.

|Eszköz | Való hozzáférés biztosítása | Útmutatás |
|---|--|---|
|Storage Explorer| Szerepkör hozzárendelése a felhasználók és csoportok | [A felhasználók számára az Azure Active Directory rendszergazda és a nem rendszergazdai szerepkörök hozzárendelése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Szerepkör hozzárendelése a felhasználók és csoportok <br>**vagy**<br> SAS-tokennel| [A felhasználók számára az Azure Active Directory rendszergazda és a nem rendszergazdai szerepkörök hozzárendelése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Könnyedén létrehozhat egy SAS-fájl letöltésére az Azure Storage – az Azure Storage Explorerben](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Szerepkör hozzárendelése felhasználóhoz felügyelt identitásnak | [Egy HDInsight-fürt létrehozása a Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Szerepkör hozzárendelése egy felhasználó által hozzárendelt – felügyelt identitás<br>**vagy**<br> Egy egyszerű szolgáltatás szerepkör hozzárendelése<br>**vagy**<br> Használja a tárfiók kulcsa | [Társított szolgáltatás tulajdonságai](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Szerepkör hozzárendelése felhasználóhoz felügyelt identitásnak | [Egy HDInsight-fürt létrehozása a Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Szerepkör hozzárendelése a szolgáltatásnév | [Útmutató: Az Azure AD-alkalmazás és -erőforrások elérésére képes egyszerű szolgáltatás létrehozása a portál használatával](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Hozzáférést biztosít az adott fájlok és mappák, tanulmányozza a következő cikkeket.

* [Azure Storage Explorer használata az Azure Data Lake Storage Gen2 fájl- és oszlopszintű engedélyek beállítása](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Hozzáférés-vezérlési listák a fájlok és könyvtárak](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Egyéb vonatkozásai beállításával kapcsolatos további információkért lásd: [biztonsági útmutató az Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Az adatok betöltése

Ez a szakasz kiemeli a különböző adatforrás és a különböző módokon, amelyben az adatok olvasódnak be egy Data Lake Storage Gen2-fiókot.

![Betölteni az adatokat az Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "betölteni az adatokat az Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc adatok

Ez jelöli, amelyek kisebb adatkészletek használt prototípus-készítés big data-alkalmazásokat. Különböző módon ad-hoc adatok feldolgozására függően az adatok forrását. 

Itt az eszközöket, amelyek segítségével adatokat ad-hoc listája.

| Adatforrás | Kigyűjtés használatával |
| --- | --- |
| Helyi számítógép |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight-fürtön futó DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamelési adatok hangulatelemzéséhez

Vásárlói adatok hozhat létre különböző forrásokból, például az alkalmazások, eszközök, szenzorok, stb. Ezeket az adatokat az eszközök széles által olvasódnak be a Data Lake Sorage Gen2. Ezek az eszközök általában fog rögzítése és feldolgozni az adatokat egy eseményt az esemény által történik, a valós idejű, majd írja be az események kötegekben Data Lake Storage Gen2 be, hogy ezek tovább dolgozhatók fel.

Itt az eszközöket, amelyek segítségével adatfolyamként továbbított adatok listája.

|Eszköz | Útmutatás |
|---|--|
|Azure HDInsight Storm | [A HDInsight Apache Storm írhat az Apache Hadoop HDFS-be](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relációs adatok

A forrásadatok relációs adatbázisból. Egy időszakon belül a relációs adatbázisok gyűjtése hatalmas mennyiségű adatokat, ami a legfontosabb elemzéseket keresztül big Data jellegű adatok feldolgozása. A következő eszközök segítségével a Data Lake Storage Gen2 helyezhetik át az adatokat.

Íme, relációs adatokat használó eszközök listáját.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Az Azure Data Factory másolási tevékenysége](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server naplóadatok (feltöltés használó egyéni alkalmazások)

Ez a fajta adatkészlet azért hívjuk fel, mert a web server naplóadatok elemzése egy gyakori alkalmazási helyzet a big data-alkalmazások és a Data Lake Storage Gen2-re kell feltölteni a naplófájlokat nagy mennyiségű kell. A következő eszközök bármelyikét használhatja saját parancsfájlok vagy alkalmazások töltse fel az adatokat írni.

Itt az eszközöket, amelyek használatával Web server naplózási adatok listája.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Az Azure Data Factory másolási tevékenysége](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

A web server naplózási adatok feltöltését, valamint is fel más típusú adatok (például közösségi hangulati adatok) egy jó módszer a saját egyéni parancsfájlok vagy alkalmazások írni, mert azt a rugalmasságot biztosít az adatok feltöltése összetevő részeként a nagyobb big data-alkalmazásokat. Bizonyos esetekben ez a kód is igénybe vehet egy parancsfájl vagy egyszerű parancssori segédprogrammal formájában. Más esetekben a kódot egy üzleti alkalmazás vagy megoldás big data-feldolgozáshoz integrálhatók használhatók.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök kapcsolódó adatok

A legtöbb HDInsight-fürttípusok (Hadoop, HBase, Storm-) tároló adattár Data Lake Storage Gen2 támogatja. HDInsight-fürtök az Azure Storage Blobs (WASB) adatok eléréséhez. A jobb teljesítmény érdekében a Data Lake Storage Gen2-fiókra a fürthöz társított másolhatja az adatokat a WASB. A következő eszközök használatával másolja az adatokat.

Ez egy lista eszközöket tartalmazza, amelyek segítségével a HDInsight-fürtökkel tartozó adatok betöltését.

|Eszköz | Útmutatás |
|---|--|
|Apache DistCp | [Adatok másolása az Azure Storage-blobokat és az Azure Data Lake Storage Gen2 között a DistCp használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Az AzCopy-eszköz | [Adatok áthelyezése az Azcopyval a](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Adatok másolása, vagy az Azure Data Lake Storage Gen1 Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Adatok tárolása a helyszíni vagy IaaS Hadoop fürtök

Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtök helyi HDFS használó gépeken. A Hadoop-fürtök lehet egy helyszíni környezetben vagy egy IaaS-fürtöt az Azure-on belül. Az ilyen adatok másolása az Azure Data Lake Storage Gen2-re megközelítésre egyszeri vagy ismétlődő módon követelményeket is lehet. Különféle módon használhatja, ennek érdekében. Az alábbi, az alternatív megoldások és a kapcsolódó feláldozását listáját.

| A módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Az Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtök adatok másolása az Azure Data Lake Storage Gen2-re |[Az ADF támogatja a HDFS adatforrásként](../../data-factory/connector-hdfs.md) |Az ADF HDFS és elsőrangú – teljes körű felügyeleti és figyelési-a-beépített támogatást nyújt a |Az adatkezelési átjáró üzembe helyezhető a helyszínen vagy az iaas-fürt szükséges |
| A Distcp használatával adatokat másol a Hadoop az Azure Storage. Ezután másolja az adatokat az Azure Storage Data Lake Storage Gen2-re megfelelő mechanizmussal. |A Data Lake Storage Gen2 használatával másolhat adatokat az Azure Storage-ból: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Nyílt forráskódú eszközöket is használhat. |Többlépéses folyamat, amely magában foglalja a több technológiák |

### <a name="really-large-datasets"></a>Nagyon nagy méretű adatkészletek

Több terabájt tartománya az adatkészletek fel, a fenti módszerek használatával néha lassú és költséges lehet. Ezekben az esetekben az Azure ExpressRoute is használhatja.  

Az Azure ExpressRoute használatával magánkapcsolatok hozhatók létre az Azure adatközpontok és olyan infrastruktúrák között a helyszínen. Ez nagy mennyiségű adat átvitelére megbízható lehetőséget kínál. További tudnivalókért lásd: [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Az adatok feldolgozása

Amint az adatok Data Lake Storage Gen2 elérhető analysis futtathatja az adatok a támogatott big data-alkalmazások használatával. 

![Adatok elemzése az Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "a Data Lake Storage Gen2 adatok elemzése")

Itt az eszközöket, amelyek segítségével adatokat elemzési feladatok futtatása a Data Lake Storage Gen2-ban tárolt adatok listája.

|Eszköz | Útmutatás |
|---|--|
|Azure HDInsight | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rövid útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 Azure Databricks használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Oktatóanyag: A kinyerési, átalakítási és az adatok betöltése az Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Az adatok vizualizációja

Szolgáltatások kombinációját használhatja létrehozása a Data Lake Storage Gen2-ban tárolt adatok vizuális ábrázolásai.

![Adatok megjelenítése a Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "adatok megjelenítése a Data Lake Storage Gen2")

* Első lépésként használatával [Azure Data Factoryben az adatok áthelyezése az Azure SQL Data Warehouse Data Lake Storage Gen2](../../data-factory/copy-activity-overview.md)
* Ezt követően is [Power BI integrálható az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) hozhat létre az adatok vizuális megjelenítése.

## <a name="download-the-data"></a>Az adatok letöltése

Érdemes azt is, töltse le vagy adatok áthelyezése az Azure Data Lake Storage Gen2 forgatókönyvek, például:

* Adatok áthelyezése más tárházakban való kapcsolat a meglévő adatfeldolgozási folyamatok. Például érdemes Data Lake Storage Gen2 adatok áthelyezése az Azure SQL Database vagy a helyszíni SQL Server.

* Adatok letöltése a helyi számítógép prototípusok application készítése során IDE-környezetek feldolgozásra.

![Kimenő forgalom a Data Lake Storage Gen2 adatait](./media/data-lake-storage-data-scenarios/egress-data.png "kimenő forgalom a Data Lake Storage Gen2 adatait")

Itt az eszközöket, amelyek segítségével adatokat letölteni a Data Lake Storage Gen2 listája.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Az Azure Data Factory másolási tevékenysége](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Adatok másolása az Azure Storage-blobokat és az Azure Data Lake Storage Gen2 között a DistCp használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
