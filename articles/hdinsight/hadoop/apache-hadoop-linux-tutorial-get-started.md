---
title: 'Gyors útmutató: Használatával a Resource Manager – Azure HDInsight az Apache Hadoop-fürtök létrehozása'
description: Ebben a rövid útmutatóban az Apache Hadoop-fürtöt az Azure HDInsight használata a Resource Manager-sablon létrehozása
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: 8eb297c555d2d7f95419b2a9aa11a26cf5c7230f
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137470"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Gyors útmutató: Az Apache Hadoop-fürt létrehozása az Azure HDInsight használata a Resource Manager-sablon

Ebben a rövid útmutatóban elsajátíthatja az Apache Hadoop-fürt létrehozása az Azure HDInsight egy Resource Manager-sablon használatával.

Hasonló sablonok tekinthetők [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). A sablon leírása található [Itt](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).  A fürt használatával is létrehozhat a [az Azure portal](apache-hadoop-linux-create-cluster-get-started-portal.md).  

A HDInsight jelenleg [7 különböző fürttípussal érhető el](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsightban támogatott összetevők listáját lásd: [What's new in the Hadoop cluster versions provided by HDInsight?](../hdinsight-component-versioning.md) (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Hadoop-fürt létrehozása

1. Válassza ki a **üzembe helyezés az Azure** jelentkezzen be az Azure-ba, és az Azure Portalon nyissa meg a Resource Manager-sablon az alábbi gombra.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Előfizetés**     |  Válassza ki az Azure-előfizetését. |
    |**Erőforráscsoport**     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |**Hely**     | Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |
    |**Fürt neve**     | Adja meg a Hadoop-fürt nevét. Mivel a HDInsightban az összes fürt ugyanazt a DNS-névteret használja, a névnek egyedinek kell lennie. A név csak kisbetűket, számokat és kötőjeleket tartalmazhat, és betűvel kell kezdődnie.  A kötőjelek előtt és után csak nem kötőjel karakter állhat.  A név is 3 és 59 karakter között kell lennie. |
    |**Fürt típusa**     | Válassza a **Hadoop** lehetőséget. |
    |**A fürt bejelentkezési neve és jelszava**     | Az alapértelmezett bejelentkezési név az **admin**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs, illetve egy nem alfanumerikus karaktert (ami nem lehet ' " ` \)). Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |**SSH-felhasználónév és jelszó**     | Az alapértelmezett felhasználónév az **sshuser**.  Az SSH-felhasználónevet át lehet nevezni.  Az SSH-felhasználói jelszóra ugyanazon követelmények vonatkoznak, mint a fürt bejelentkezési jelszavára.|

    Egyes tulajdonságok szoftveresen kötöttek a sablonban.  Ezeket az értéteket a sablonból konfigurálhatja. További magyarázat ezeket a tulajdonságokat, lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > A megadott értékeknek egyedinek kell lenniük, és követniük kell az elnevezési irányelveket. A sablon nem végez érvényességi ellenőrzéseket. Ha a megadott értékek már használatban vannak, vagy nem felelnek meg az irányelveknek, a sablon elküldését követően egy hibaüzenetet fog kapni.  

    ![HDInsight Linux lekérdezi elindított Resource Manager-sablon a portálon](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "üzembe helyezése Hadoop-fürtöt a HDInsight az Azure portal és a egy resource manager-sablon használatával")

3. Válassza ki **elfogadom a feltételeket és a fenti feltételeket**, majd válassza ki **beszerzési**. Kapni fog egy értesítést, amely az üzembe helyezés folyamatban van.  Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

4. A fürt létrehozása után kapni fog egy **üzembe helyezés sikeres** értesítés egy **erőforráscsoport megnyitása** hivatkozásra.  A **erőforráscsoport** lapon megjelennek az új HDInsight-fürt és a fürthöz társított alapértelmezett tároló. Minden egyes fürt egy [Azure Storage-fiók](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-tárfiókra](../hdinsight-hadoop-use-data-lake-store.md) függőség. Ez az alapértelmezett tárfiók. A HDInsight-fürt és az alapértelmezett tárfióknak ugyanazon Azure-régióban kell felhőátjárónak. A fürtök törlésével a tárfiók nem törlődik.

> [!NOTE]  
> Egyéb Fürtlétrehozási módszerekhez és ebben a rövid útmutatóban használt tulajdonságok megértéséhez, lásd: [létre HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

> [!NOTE]  
> Ha *azonnal* továbblép a következő oktatóanyagra, amelyben megismerheti, hogyan futtathat ETL-műveleteket a Hadoop használatával a HDInsighton, érdemes lehet futva hagyni a fürtöt. Ennek az oka az oktatóanyagban kell újra létrehozni egy Hadoop-fürtöt. Ha azonban nem azonnal folytatja a következő oktatóanyaggal, most törölnie kell a fürtöt.

**A fürt és/vagy az alapértelmezett tárfiók törlése**

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![HDInsight – fürt törlése](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan Apache Hadoop-fürt létrehozása a Resource Manager-sablon használatával HDInsight. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
>[A kinyerési, átalakítási és az Apache Hive használata a HDInsight adatok betöltése](../hdinsight-analyze-flight-delay-data-linux.md)