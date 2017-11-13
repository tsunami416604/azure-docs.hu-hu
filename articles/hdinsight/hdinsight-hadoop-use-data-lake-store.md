---
title: "A Data Lake Store és a Hadoop együttes használata az Azure HDInsightban | Microsoft Docs"
description: "Megtudhatja, hogyan kérdezhet le adatokat az Azure Data Lake Store-ból, és hogyan tárolhatja az elemzésének eredményeit."
keywords: "blob storage,hdfs,strukturált adatok,strukturálatlan adatok, data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: jgao
ms.openlocfilehash: e139db13cf943723a498158870627fd66cdd6e0f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>A Data Lake Store és az Azure HDInsight-fürtök együttes használata

A HDInsight-fürtben lévő adatok elemzéséhez az adatokat tárolhatja az [Azure Storage](../storage/common/storage-introduction.md)-ban, az [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)-ban, vagy mindkettőben. Mindkét tárolási lehetőség lehetővé teszi a számításhoz használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

Ebből a cikkből megtudhatja, hogyan használható a Data Lake Store a HDInsight-fürtökkel. Az Azure Storage és a HDInsight-fürtök együttes használatával kapcsolatos tudnivalókért lásd: [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](hdinsight-hadoop-use-blob-storage.md). További információ a HDInsight-fürtök létrehozásáról: [Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> A Data Lake Store elérése mindig biztonságos csatornán keresztül történik, így nincs `adls` fájlrendszer-sémanév. Mindig `adl` használandó.
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>Lehetőségek HDInsight-fürtök esetén

A Hadoop támogatja az alapértelmezett fájlrendszert. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozása során egy Azure Storage-blobtároló adható meg az alapértelmezett fájlrendszerként, illetve a 3.5-ös vagy újabb verziójú HDInsight esetében néhány kivételtől eltekintve választhat, hogy az Azure Storage vagy az Azure Data Lake Store legyen az alapértelmezett fájlrendszer. 

A HDInsight-fürtök kétféleképpen használhatják a Data Lake Store-t:

* Az alapértelmezett tárolóként
* Kiegészítő tárolóként, ahol az Azure Storage Blob az alapértelmezett tároló.

Jelen pillanatban csak bizonyos típusú, illetve verziójú HDInsight-fürtök támogatják a Data Lake Store alapértelmezett vagy kiegészítő tárfiókként való használatát:

| A HDInsight-fürt típusa | A Data Lake Store az alapértelmezett tároló | A Data Lake Store kiegészítő tároló| Megjegyzések |
|------------------------|------------------------------------|---------------------------------------|------|
| A HDInsight 3.6-os verziója | Igen | Igen | |
| A HDInsight 3.5-ös verziója | Igen | Igen | A HBase kivételével|
| A HDInsight 3.4-es verziója | Nem | Igen | |
| A HDInsight 3.3-as verziója | Nem | Nem | |
| A HDInsight 3.2-es verziója | Nem | Igen | |
| Prémium (szintű) HDInsight| Nem | Nem | |
| Storm | | |A Data Lake Store használható Storm-topológiából származó adatok írására. A Data Lake Store-ban tárolhatók referenciaadatok is, amelyek olvashatók lesznek egy Storm-topológiából.|

Ha a Data Lake Store-t kiegészítő tárfiókként használja, az nem befolyásolja a fürtből az Azure-tárba történő írás, illetve olvasás lehetőségét vagy hatékonyságát.


## <a name="use-data-lake-store-as-default-storage"></a>A Data Lake Store használata az alapértelmezett tárolóként

Ha a HDInsight alapértelmezett tárolójaként a Data Lake Store van beállítva, a fürttel kapcsolatos fájlok a Data Lake Store-ban a következő helyen találhatók:

    adl://mydatalakestore/<cluster_root_path>/

ahol a `<cluster_root_path>` a Data Lake Store-ban létrehozott mappa neve. Ha mindegyik fürthöz megadja a gyökér elérési útját, ugyanazt a Data Lake Store-fiókot több fürthöz is használhatja. Így olyan beállítással rendelkezhet, ahol:

* Az 1. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A 2. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Vegye figyelembe, hogy mindét fürt ugyanazt a **mydatalakestore** nevű Data Lake Store-fiókot használja. Mindegyik fürt hozzáféréssel rendelkezik a saját gyökér fájlrendszeréhez a Data Lake Store-ban. Az Azure Portalon végzett üzembe helyezésekor a **/fürtök/\<fürtnév>** mappanevet kell használnia a gyökér elérési úthoz.

Ahhoz, hogy a Data Lake Store-t alapértelmezett tárolóként használhassa, hozzáférést kell biztosítania a szolgáltatásnév számára a következő elérési utakhoz:

- A Data Lake Store-fiók gyökere.  Példa: adl://mydatalakestore/.
- Az összes fürt mappája.  Példa: adl://mydatalakestore/clusters.
- Egy adott fürt mappája.  Példa: adl://mydatalakestore/clusters/cluster1storage.

A szolgáltatásnevek létrehozásával és a hozzáférés biztosításával kapcsolatos további információkért lásd: [A Data Lake Store-hoz történő hozzáférés konfigurálása](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>A Data Lake Store használata kiegészítő tárolóként

A Data Lake Store a fürt kiegészítő tárolójaként is használható. Ilyen esetekben a fürt alapértelmezett tárolója az Azure Storage Blob vagy egy Data Lake Store-fiók lehet. Ha HDInsight-feladatokat futtat a kiegészítő tárolóként használt Data Lake Store-ban tárolt adatokon, a fájlok teljes elérési útját kell használnia. Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Vegye figyelembe, hogy most nincs **cluster_root_path** az URL-címben. Ez azért van, mert a Data Lake Store ebben az esetben nem alapértelmezett tároló, így csak meg kell adnia a fájok elérési útját.

Ahhoz, hogy a Data Lake Store-t kiegészítő tárolóként használhassa, hozzáférést kell biztosítania a szolgáltatásnév számára a tárolt fájlok elérési útjaihoz.  Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

A szolgáltatásnevek létrehozásával és a hozzáférés biztosításával kapcsolatos további információkért lásd: [A Data Lake Store-hoz történő hozzáférés konfigurálása](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Több Data Lake Store-fiók használata

Egy Data Lake Store-fiók kiegészítő fiókként történő hozzáadásához vagy több Data Lake Store-fiók hozzáadásához hozzáférést kell biztosítani a HDInsight-fürt számára egy vagy több Data Lake Store-fiók adataihoz. Lásd: [A Data Lake Store-hoz történő hozzáférés konfigurálása](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>A Data Lake Store-hoz történő hozzáférés konfigurálása

A Data Lake Store-hoz a HDInsight-fürtből történő hozzáférés konfigurálásához rendelkeznie kell egy Azure Active Directory- (Azure AD-) szolgáltatásnévvel. Kizárólag Azure AD-rendszergazdák hozhatnak létre szolgáltatásnevet. A szolgáltatásnevet egy tanúsítvánnyal kell létrehozni. További információkért lásd: [A Data Lake Store-hoz történő hozzáférés konfigurálása](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access), és [Szolgáltatásnév létrehozása önaláírt tanúsítvánnyal](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Ha a Data Lake Store-t a HDInsight-fürt kiegészítő tárolójaként tervezi használni, akkor kifejezetten ezt a megoldást javasoljuk, amikor létrehozza a fürtöt az ebben a cikkben leírtaknak megfelelően. Az Azure Data Lake Store-t kiegészítő tárolóként hozzáadni egy meglévő HDInsight-fürthöz bonyolult folyamat, és sok hibalehetőséggel jár.
>

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Többféleképpen is elérheti egy HDInsight-fürtből a Data Lake Store-ban tárolt fájlokat.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökér elérési útját az adl:/// karakterláncra cseréli le. Így a fenti példában lecserélheti az `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` helyett az `adl:///` értéket használhatja.

        adl:///<file path>

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg. Ha például a fájl teljes elérési útja a következő:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Ehelyett a következő relatív elérési úttal is elérheti ugyanazon sample.log fájlt.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Data Lake Store-hozzáféréssel rendelkező HDInsight-fürtök létrehozása

Az alábbi hivatkozások a Data Lake Store-hozzáféréssel rendelkező HDInsight-fürtök létrehozásának részletes útmutatóira mutatnak.

* [A Portal használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [A PowerShell használata (alapértelmezett tárolóként beállított Data Lake Store-ral)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [A PowerShell használata (kiegészítő tárolóként beállított Data Lake Store-ral)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtanulta, hogyan használhatja HDFS-kompatibilis Azure Data Lake Store-tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Az Azure Data Lake Store használatának első lépései](../data-lake-store/data-lake-store-get-started-portal.md)
* [Data Lake Store-t használó HDInsight-fürt létrehozása az Azure Portal használatával](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store-t használó HDInsight-fürt létrehozása az Azure PowerShell használatával](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
