---
title: 'Rövid útmutató: Apache HBase fürt létrehozása sablonnal – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Apache HBase-fürtöt az Azure HDInsightban a Resource Manager sablon használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 609580fb57e5c11741332c437172532853e73917
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79485775"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rövid útmutató: Apache HBase-fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager-sablon használatával hozzon létre egy [Apache HBase-fürtöt](./apache-hbase-overview.md) az Azure HDInsightban. HBase egy nyílt forráskódú, NoSQL adatbázis épül Apache Hadoop és modellezve a [Google BigTable](https://cloud.google.com/bigtable/).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-apache-hbase-cluster"></a>Apache HBase-fürt létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-hbase-linux)

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json" range="1-145":::


A sablonban két Azure-erőforrás van definiálva:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): HDInsight-fürt létrehozása.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az **Azure üzembe helyezése** gombot az Azure-ba való bejelentkezéshez és az Erőforrás-kezelő sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

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

    ![Erőforrás-kezelő sablon HBase](./media/quickstart-resource-manager-template/resource-manager-template-hbase.png)

1. Tekintse át a **feltételeket**. Ezután válassza **az Elfogadom a fent meghatározott feltételeket**, majd **a Vásárlás**lehetőséget. Értesítést fog kapni arról, hogy a központi telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás az **erőforrásra** hivatkozással. Az erőforráscsoport lapja felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik egy [Azure Storage-fiókkal](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake Storage-fiók függőség.](../hdinsight-hadoop-use-data-lake-store.md) Ez a továbbiakban az alapértelmezett tárfiók. A HDInsight-fürtés alapértelmezett tárfiókja együtt kell lennie ugyanabban az Azure-régióban. Fürtök törlése nem törli a tárfiókot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Az Azure Portalon keresse meg a fürtöt, és válassza a **Törlés**lehetőséget.

![Erőforrás-kezelő sablon HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache HBase-fürtöt a HDInsightban egy Erőforrás-kezelő sablon használatával. A következő cikkben megtudhatja, hogyan lehet lekérdezni a HBase hdinsight-ban a HBase Shell használatával.

> [!div class="nextstepaction"]
> [Az Apache HBase lekérdezése az Azure HDInsightban a HBase Rendszerhéjsegítségével](./query-hbase-with-hbase-shell.md)
