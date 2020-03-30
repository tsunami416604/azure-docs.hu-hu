---
title: 'Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával'
description: Ebben a rövid útmutatóban apache Hadoop-fürtöt hozhat létre az Azure HDInsightban a Resource Manager-sablon használatával
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: ae0f29b8085bd9637f527f2a58229dd89ce6933b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064678"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager-sablon használatával hozzon létre egy [Apache Hadoop-fürtöt](./apache-hadoop-introduction.md) az Azure HDInsightban. A Hadoop volt az eredeti nyílt forráskódú keretrendszer a nagy adathalmazok fürtözéses feldolgozásához és elemzéséhez. A Hadoop ökoszisztéma kapcsolódó szoftvereket és segédprogramokat tartalmaz, beleértve az Apache Hive-t, az Apache HBase-t, a Sparkot, a Kafkát és még sok mást.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
A HDInsight jelenleg [7 különböző fürttípussal érhető el](../hdinsight-overview.md#cluster-types-in-hdinsight). Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsightban támogatott összetevők listáját lásd: [What's new in the Hadoop cluster versions provided by HDInsight?](../hdinsight-component-versioning.md) (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).  

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password)

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json" range="1-148":::


A sablonban két Azure-erőforrás van definiálva:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): HDInsight-fürt létrehozása.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az **Azure üzembe helyezése** gombot az Azure-ba való bejelentkezéshez és az Erőforrás-kezelő sablon megnyitásához.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés|A legördülő listából válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
    |Hely|Az érték automatikusan kinépesül az erőforráscsoporthoz használt helynel.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt típusa | Válassza **a hadoop**lehetőséget. |
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót. A jelszónak legalább 10 karakter hosszúnak kell lennie, és legalább egy számjegyet, egy nagybetűt és egy kisbetűt, egy nem alfanumerikus karaktert (kivéve a " ' karaktereket ). |
    |Ssh felhasználónév|Adja meg a felhasználónevet, az alapértelmezett **sshuser**|
    |Ssh jelszó|Adja meg a jelszót.|

    Egyes tulajdonságok szoftveresen kötöttek a sablonban.  Ezeket az értéteket a sablonból konfigurálhatja. Ezekről a tulajdonságokról további magyarázat: [Apache Hadoop-fürtök létrehozása a HDInsight ban.](../hdinsight-hadoop-provision-linux-clusters.md)

    > [!NOTE]  
    > A megadott értékeknek egyedinek kell lenniük, és követniük kell az elnevezési irányelveket. A sablon nem végez érvényességi ellenőrzéseket. Ha a megadott értékek már használatban vannak, vagy nem felelnek meg az irányelveknek, a sablon elküldését követően egy hibaüzenetet fog kapni.  

    ![HDInsight Linux elindul Resource Manager sablon portálon](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Hadoop-fürt központi telepítése a HDInsightban az Azure Portal és egy erőforráscsoport-kezelő sablon használatával")

1. Tekintse át a **feltételeket**. Ezután válassza **az Elfogadom a fent meghatározott feltételeket**, majd **a Vásárlás**lehetőséget. Értesítést fog kapni arról, hogy a központi telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás az **erőforrásra** hivatkozással. Az erőforráscsoport lapja felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik egy [Azure Storage-fiókkal](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake Storage-fiók függőség.](../hdinsight-hadoop-use-data-lake-store.md) Ez a továbbiakban az alapértelmezett tárfiók. A HDInsight-fürtés alapértelmezett tárfiókja együtt kell lennie ugyanabban az Azure-régióban. Fürtök törlése nem törli a tárfiókot.

> [!NOTE]  
> A gyorsindításban használt egyéb fürtlétrehozási módszerekről és a [HDInsight-fürtök létrehozása című](../hdinsight-hadoop-provision-linux-clusters.md)témakörben található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

> [!NOTE]  
> Ha *azonnal* továbblép a következő oktatóanyagra, amelyben megismerheti, hogyan futtathat ETL-műveleteket a Hadoop használatával a HDInsighton, érdemes lehet futva hagyni a fürtöt. Ez azért van, mert a bemutató ban újra létre kell hoznia egy Hadoop-fürtöt. Ha azonban nem azonnal folytatja a következő oktatóanyaggal, most törölnie kell a fürtöt.

Az Azure Portalon keresse meg a fürtöt, és válassza a **Törlés**lehetőséget.

![A HDInsight fürt törlése a portálról](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "A HDInsight fürt törlése a portálról")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Hadoop-fürtöt a HDInsightban egy Erőforrás-kezelő sablon használatával. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az Interaktív lekérdezés használatával a HDInsighton](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
