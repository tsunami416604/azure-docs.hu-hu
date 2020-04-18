---
title: 'Rövid útmutató: Interaktív lekérdezési fürt létrehozása sablon használatával – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy interaktív lekérdezési fürtöt az Azure HDInsightban az Erőforrás-kezelő sablon használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 74b1c25c8bab11c0b2a72510fd419df239e2c23a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603580"
---
# <a name="quickstart-create-interactive-query-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rövid útmutató: Interaktív lekérdezési fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager-sablon használatával hozzon létre egy [interaktív lekérdezési](./apache-interactive-query-get-started.md) fürtöt az Azure HDInsightban. Az interaktív lekérdezés (más néven Apache Hive LLAP vagy [Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP)egy Azure HDInsight-fürttípus. [cluster type](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-interactive-query-cluster"></a>Interaktív lekérdezési fürt létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-interactive-hive)

:::code language="json" source="~/quickstart-templates/101-hdinsight-interactive-hive/azuredeploy.json" range="1-158":::


A sablonban két Azure-erőforrás van definiálva:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): HDInsight-fürt létrehozása.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az **Azure üzembe helyezése** gombot az Azure-ba való bejelentkezéshez és az Erőforrás-kezelő sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json)

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listából válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
    |Hely|Az érték automatikusan kinépesül az erőforráscsoporthoz használt helynel.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót. A jelszónak legalább 10 karakter hosszúnak kell lennie, és legalább egy számjegyet, egy nagybetűt és egy kisbetűt, egy nem alfanumerikus karaktert (kivéve a " ' karaktereket ). |
    |Ssh felhasználónév|Adja meg a felhasználónevet, az alapértelmezett sshuser|
    |Ssh jelszó|Adja meg a jelszót.|

    ![Erőforrás-kezelő sablon HBase](./media/quickstart-resource-manager-template/resource-manager-template-hive.png)

1. Tekintse át a **feltételeket**. Ezután válassza **az Elfogadom a fent meghatározott feltételeket**, majd **a Vásárlás**lehetőséget. Értesítést fog kapni arról, hogy a központi telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás az **erőforrásra** hivatkozással. Az erőforráscsoport lapja felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik egy [Azure Storage-fiókkal](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake Storage-fiók függőség.](../hdinsight-hadoop-use-data-lake-store.md) Ez a továbbiakban az alapértelmezett tárfiók. A HDInsight-fürtés alapértelmezett tárfiókja együtt kell lennie ugyanabban az Azure-régióban. Fürtök törlése nem törli a tárfiókot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Az Azure Portalon keresse meg a fürtöt, és válassza a **Törlés**lehetőséget.

![Erőforrás-kezelő sablon HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hive.png)

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre interaktív lekérdezési fürtöt a HDInsightban egy Erőforrás-kezelő sablon használatával. A következő cikkben megtudhatja, hogyan használhatja az Apache Zeppelint az Apache Hive-lekérdezések futtatásához.

> [!div class="nextstepaction"]
> [Apache Hive-lekérdezések végrehajtása az Azure HDInsightban az Apache Zeppelin segítségével](./hdinsight-connect-hive-zeppelin.md)
