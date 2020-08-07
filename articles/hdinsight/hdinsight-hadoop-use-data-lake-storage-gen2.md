---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel
description: Ismerje meg, hogyan használhatja a Azure Data Lake Storage Gen2t az Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 21b09e6b7a2be6b87288d973b40c566fb6217841
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849981"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel

A Azure Data Lake Storage Gen2 egy Azure Blob Storage-ra épülő, big data elemzésre szánt felhőalapú tárolási szolgáltatás. Data Lake Storage Gen2 ötvözi az Azure Blob Storage és a Azure Data Lake Storage Gen1 képességeit. Az eredményül kapott szolgáltatás a Azure Data Lake Storage Gen1 funkcióit kínálja. Ezek a funkciók a következők: fájlrendszer-szemantika, könyvtár szintű és fájl szintű biztonság, valamint alkalmazkodóképesség. Az Azure Blob Storage alacsony díjas, többrétegű tárolási, magas rendelkezésre állású és vész-helyreállítási képességeivel együtt.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állás

A Data Lake Storage Gen2 tárolási lehetőségként érhető el szinte minden Azure HDInsight-fürthöz, alapértelmezettként és egy további Storage-fiókként is. A HBase azonban csak egy Data Lake Storage Gen2 fiókkal rendelkezhet.

A fürt létrehozási lehetőségeinek Data Lake Storage Gen2 használatával történő teljes összehasonlítását lásd: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Miután kiválasztotta a Data Lake Storage Gen2 **elsődleges tárolási típusként**, nem választhat Data Lake Storage Gen1-fiókot további tárolóként.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Hozzon létre egy fürtöt Data Lake Storage Gen2 segítségével a Azure Portal

Data Lake Storage Gen2 for Storage-t használó HDInsight-fürt létrehozásához kövesse az alábbi lépéseket egy Data Lake Storage Gen2-fiók konfigurálásához.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyennel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal felső sarokban kattintson az **erőforrás létrehozása**elemre.
1. A keresőmezőbe írja be a **felhasználó által hozzárendelt** értéket, és kattintson a **felhasználóhoz rendelt felügyelt identitás**elemre.
1. Kattintson a **Létrehozás** lehetőségre.
1. Adja meg a felügyelt identitás nevét, válassza ki a megfelelő előfizetést, erőforráscsoportot és helyet.
1. Kattintson a **Létrehozás** lehetőségre.

További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](hdinsight-managed-identities.md).

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2-fiók létrehozása

Hozzon létre egy Azure Data Lake Storage Gen2 Storage-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal felső sarokban kattintson az **erőforrás létrehozása**elemre.
1. A keresőmezőbe írja be a **Storage** kifejezést, majd kattintson a **Storage-fiók**elemre.
1. Kattintson a **Létrehozás** lehetőségre.
1. A **Storage-fiók létrehozása** képernyőn:
    1. Válassza ki a megfelelő előfizetést és erőforráscsoportot.
    1. Adja meg a Data Lake Storage Gen2-fiók nevét.
    1. Kattintson a **speciális** fülre.
    1. Kattintson a **Data Lake Storage Gen2**alatt található **hierarchikus névtér** melletti **engedélyezve** lehetőségre.
    1. Kattintson a **Felülvizsgálat + létrehozás** elemre.
    1. Kattintson a **Létrehozás** gombra

További információ a Storage-fiók létrehozása során felmerülő egyéb lehetőségekről: gyors útmutató [: Azure Data Lake Storage Gen2 Storage-fiók létrehozása](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![A Storage-fiók létrehozását bemutató képernyőkép a Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>A felügyelt identitásra vonatkozó engedélyek beállítása a Data Lake Storage Gen2 fiókban

Rendelje hozzá a felügyelt identitást a Storage- **blob adattulajdonosi** szerepkörhöz a Storage-fiókban.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.
1. Válassza ki a Storage-fiókját, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget a fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.

    ![A Storage hozzáférés-vezérlési beállításait ábrázoló képernyőfelvétel](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Új szerepkör hozzáadásához kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage blob-adat tulajdonosi** szerepkörét. Ezután válassza ki azt az előfizetést, amelynek a felügyelt identitás-és Storage-fiókja van. Ezután a Keresés gombra kattintva keresse meg a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást. Végül válassza ki a felügyelt identitást, és a rendszer a **kijelölt tagok**területen jelenik meg.

    ![Az Azure-szerepkörök hozzárendelését bemutató képernyőkép](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Kattintson a **Mentés** gombra. A kiválasztott felhasználó által hozzárendelt identitás mostantól a kiválasztott szerepkör alatt szerepel.
1. A kezdeti beállítás befejezése után létrehozhat egy fürtöt a portálon keresztül. A fürtnek ugyanabban az Azure-régióban kell lennie, mint a Storage-fióknak. A fürt létrehozási menüjének **tárterület** lapján válassza a következő beállításokat:

    * Az **elsődleges tároló típusa**beállításnál válassza a **Azure Data Lake Storage Gen2**lehetőséget.
    * Az **elsődleges Storage-fiók**területen keresse meg és válassza ki az újonnan létrehozott Data Lake Storage Gen2 Storage-fiókot.

    * Az **identitás**területen válassza ki az újonnan létrehozott, felhasználó által hozzárendelt felügyelt identitást.

        ![A Data Lake Storage Gen2 Azure HDInsight való használatának tárolási beállításai](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Másodlagos Data Lake Storage Gen2 fiók hozzáadásához a Storage-fiók szintjén egyszerűen rendelje hozzá a korábban létrehozott felügyelt identitást a hozzáadni kívánt új Data Lake Storage Gen2 Storage-fiókhoz. Javasoljuk, hogy adjon hozzá egy másodlagos Data Lake Storage Gen2 fiókot a HDInsight "további Storage-fiókok" paneljén nem támogatott.
    > * Engedélyezheti az RA-GRS vagy az RA-ZRS az Azure Storage-fiókban, amelyet a HDInsight használ. A fürt az RA-GRS vagy az RA-ZRS másodlagos végponttal való létrehozása azonban nem támogatott.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Data Lake Storage Gen2-fürt létrehozása az Azure CLI-vel

[Letöltheti a minta sablonfájl fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) , és [letöltheti a minta paramétereinek fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). A sablon és az Azure CLI-kódrészlet használata előtt cserélje le a következő helyőrzőket a megfelelő értékekre:

| Helyőrző | Leírás |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCEGROUPNAME>` | Az az erőforráscsoport, amelyben létre szeretné hozni az új fürtöt és a Storage-fiókot. |
| `<MANAGEDIDENTITYNAME>` | Annak a felügyelt identitásnak a neve, amely engedélyt kap a Azure Data Lake Storage Gen2-fiókra. |
| `<STORAGEACCOUNTNAME>` | A létrehozandó új Azure Data Lake Storage Gen2 fiók. |
| `<FILESYSTEMNAME>`  | Annak a fájlrendszernek a neve, amelyet a fürtnek használnia kell a Storage-fiókban. |
| `<CLUSTERNAME>` | A HDInsight-fürt neve. |
| `<PASSWORD>` | Az SSH-val és a Ambari-irányítópulttal a fürtbe való bejelentkezéshez választott jelszava. |

Az alábbi kódrészlet a következő kezdeti lépéseket hajtja végre:

1. Bejelentkezik az Azure-fiókjába.
1. Beállítja azt az aktív előfizetést, amelyben a létrehozási műveletek lesznek végrehajtva.
1. Létrehoz egy új erőforráscsoportot az új központi telepítési tevékenységekhez.
1. Felhasználó által hozzárendelt felügyelt identitást hoz létre.
1. Bővítmény hozzáadását az Azure CLI-hez a Data Lake Storage Gen2 szolgáltatásainak használatához.
1. Létrehoz egy új Data Lake Storage Gen2 fiókot a jelző használatával `--hierarchical-namespace true` .

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Ezután jelentkezzen be a portálra. Adja hozzá az új felhasználó által hozzárendelt felügyelt identitást a Storage-beli **blob adatközreműködői** szerepkörhöz a Storage-fiókban. Ez a lépés a [Azure Portal használata](hdinsight-hadoop-use-data-lake-storage-gen2.md)című témakör 3. lépésében található.

 > [!IMPORTANT]
 > Győződjön meg arról, hogy a Storage-fiókja rendelkezik a felhasználó által hozzárendelt identitással a **Storage blob adatközreműködői** szerepkörének engedélyeivel, ellenkező esetben a fürt létrehozása sikertelen lesz.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Fürt létrehozása Data Lake Storage Gen2 segítségével Azure PowerShell

A PowerShell használatával olyan HDInsight-fürtöt hozhat létre, amely Azure Data Lake Storage Gen2 jelenleg nem támogatott.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Data Lake Storage Gen2 hozzáférés-vezérlése a HDInsight-ben

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen engedélyeket Data Lake Storage Gen2 támogatni?

Data Lake Storage Gen2 egy hozzáférés-vezérlési modellt használ, amely támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) és a POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket). A Data Lake Storage Gen1 csak az adathozzáférés szabályozásához támogatja a hozzáférés-vezérlési listát.

A RBAC szerepkör-hozzárendeléseket használ az Azure-erőforrásokhoz tartozó felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz tartozó engedélyek hatékony alkalmazására. Ezek az Azure-erőforrások jellemzően legfelső szintű erőforrásokra (például Azure Storage-fiókokra) vannak korlátozva. Az Azure Storage esetében és a Data Lake Storage Gen2 is, ez a mechanizmus a fájlrendszer erőforrására lett kiterjesztve.

 További információ a RBAC-mel rendelkező fájlokra vonatkozó engedélyekről: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Az ACL-ekkel rendelkező fájlengedélyek részletes ismertetését lásd: [hozzáférés-vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan szabályozhatja az adataik elérését Data Lake Storage Gen2ban?

A HDInsight-fürtnek a Data Lake Storage Gen2 lévő fájlokhoz való hozzáférését felügyelt identitások vezérlik. A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitásokkal nem kell regisztrálnia az egyszerű szolgáltatásokat az Azure AD-ben. Vagy őrizze meg a hitelesítő adatokat, például a tanúsítványokat.

Az Azure-szolgáltatások két típusú felügyelt identitással rendelkeznek: rendszerhez rendelt és felhasználó által hozzárendelt. A HDInsight felhasználó által hozzárendelt felügyelt identitásokat használ a Data Lake Storage Gen2 eléréséhez. Az a létrehozása `user-assigned managed identity` önálló Azure-erőforrásként történik. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan az Azure AD-felhasználók számára a kaptár vagy más szolgáltatások használatával történő adatlekérdezésre vonatkozó engedélyeket Data Lake Storage Gen2.

A felhasználók számára az adatlekérdezésre vonatkozó engedélyek megadásához az Azure AD biztonsági csoportokat használja az ACL-ekben a hozzárendelt elsődlegesként. Ne rendeljen hozzá közvetlenül hozzáférési engedélyeket az egyes felhasználókhoz vagy egyszerű szolgáltatásokhoz. Az Azure AD biztonsági csoportjaival szabályozhatja az engedélyek áramlását, hozzáadhat és eltávolíthat felhasználókat vagy egyszerű szolgáltatásokat anélkül, hogy az ACL-eket újra kellene alkalmazni egy teljes címtár-struktúrára. A megfelelő Azure AD biztonsági csoportból csak a felhasználókat kell hozzáadnia vagy eltávolítania. Az ACL-ek nem öröklődnek, ezért az ACL-ek újraalkalmazása megköveteli az ACL frissítését minden fájlon és alkönyvtáron.

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

Több módon is hozzáférhet a Data Lake Storage Gen2 lévő fájlokhoz egy HDInsight-fürtről.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel az elérési utat a fürt gyökerére cseréli le a következővel:

    ```
    abfs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt fő csomópontjának [SSH-kapcsolatain](./hdinsight-hadoop-linux-use-ssh-unix.md) alapulnak. A példák mindhárom URI-sémát használják. Cserélje `CONTAINERNAME` le `STORAGEACCOUNT` a és a értéket a megfelelő értékekre.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancs

1. Hozzon létre egy fájlt a helyi tárolóban.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürt tárterületén.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürt tárolójába.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. A fürt tárterületén lévő könyvtár tartalmának listázása.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Struktúra-tábla létrehozása

A szemléltető célokra három fájl helye látható. A tényleges végrehajtáshoz csak az egyik bejegyzést használja `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight-integráció Data Lake Storage Gen2 előzetes verzióval – ACL és biztonsági frissítés](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Bevezetés a Azure Data Lake Storage Gen2ba](../storage/blobs/data-lake-storage-introduction.md)
* [Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezés használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
