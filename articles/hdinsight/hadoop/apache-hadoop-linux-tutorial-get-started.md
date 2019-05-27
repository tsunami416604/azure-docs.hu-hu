---
title: 'Gyors útmutató: Használatával a Resource Manager – Azure HDInsight az Apache Hadoop-fürtök létrehozása'
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtök.
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 9084e8e0fe7707b5f115df80792cea102772b8e9
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861649"
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

## <a name="troubleshoot"></a>Az eszköz nem tudta a várt módon befejezni a szinkronizálást. A probléma megoldásának módjáról erre az üzenetre kattintva tájékozódhat.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

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

Ha készen áll dolgozni a saját adataival, és szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következő cikkeket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](../hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* Egy HDInsight-fürt létrehozása a Data Lake Storage módjáról további információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md).
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* További információ a Hive a HDInsight, beleértve a Visual Studióban a Hive-lekérdezések végrehajtása: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).
* További információ az adatok átalakítására szolgáló Pig: [Apache Pig használata a HDInsight](hdinsight-use-pig.md).
* További információ a Hadoopon adatokat feldolgozó programok írására szolgáló MapReduce módszerről: [A MapReduce használata a HDInsighttal](hdinsight-use-mapreduce.md).
* A HDInsight-adatok elemzésére szolgáló HDInsight Tools for Visual Studio szolgáltatással kapcsolatos további információkért lásd: [Get started using Visual Studio Hadoop tools for HDInsight](apache-hadoop-visual-studio-tools-get-started.md) (A HDInsight Visual Studio Hadoop-eszközeinek használatára vonatkozó első lépések).
* A HDInsight-adatok elemzésére szolgáló HDInsight Tools for VSCode szolgáltatással kapcsolatos további információkat [az Azure HDInsight Tools for Visual Studio Code használatát ismertető cikkben találja](../hdinsight-for-vscode.md).


A HDInsight-fürtök létréhozásával vagy kezelésével kapcsolatos további információkért lásd a következő cikkeket:

* A Linux-alapú HDInsight-fürt kezelésével kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari](../hdinsight-hadoop-manage-ambari.md).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](../hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).

HDInsight-fürt használata az Azure Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd:

* [Az Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular).
* [Az Azure-sablon referencia](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md