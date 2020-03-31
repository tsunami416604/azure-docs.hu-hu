---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel
description: Ismerje meg, hogyan használhatja az Azure Data Lake Storage Gen2-t az Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272291"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel

Az Azure Data Lake Storage Gen2 egy felhőalapú tárolási szolgáltatás, amely az Azure Blob storage-ra épül a big data-elemzéshez. A Data Lake Storage Gen2 egyesíti az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeit. Az eredményül kapott szolgáltatás az Azure Data Lake Storage Gen1 szolgáltatásait kínálja, például a fájlrendszer szemantikáját, a címtár- és fájlszintű biztonságot és a méretezhetőséget, valamint az alacsony költségű, többszintű tárolást, a magas rendelkezésre állást és a vész-helyreállítási képességeket az Azure Blob storage-ból.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 elérhetősége

A Data Lake Storage Gen2 szinte az összes Azure HDInsight-fürttípushoz elérhető, alapértelmezett és további tárfiókként. A HBase azonban csak egy Data Lake Storage Gen2 fiókkal rendelkezhet.

A Fürtlétrehozási beállítások teljes összehasonlítását a Data Lake Storage Gen2 használatával az [Azure HDInsight-fürtökkel használható tárolási lehetőségek összehasonlítása(compare storage options for use) (Tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel) (A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel) menüben](hdinsight-hadoop-compare-storage-options.md)található.

> [!Note]  
> Miután a Data Lake Storage Gen2-t választja **elsődleges tárolótípusként,** nem választhat további tárként a Data Lake Storage Gen1 fiókot.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Fürt létrehozása a Data Lake Storage Gen2 szolgáltatással az Azure Portalon keresztül

Hozzon létre egy HDInsight-fürt, amely a Data Lake Storage Gen2 tárolására, kövesse az alábbi lépéseket a Data Lake Storage Gen2 fiók konfigurálásához.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyen.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal felső sarokban kattintson az **Erőforrás létrehozása**elemre.
1. A keresőmezőbe írja be a **hozzárendelt felhasználót,** és kattintson **a Felügyelt felhasználó által hozzárendelt felhasználó elemre.**
1. Kattintson **a Létrehozás gombra.**
1. Adja meg a felügyelt identitás nevét, válassza ki a megfelelő előfizetést, erőforráscsoportot és helyet.
1. Kattintson **a Létrehozás gombra.**

A felügyelt identitások Azure HDInsightban való működéséről további információt az [Azure HDInsight felügyelt identitásai című témakörben talál.](hdinsight-managed-identities.md)

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2-fiók létrehozása

Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal felső sarokban kattintson az **Erőforrás létrehozása**elemre.
1. A keresőmezőbe írja be a **Storage (Tárterület)** kifejezést, és kattintson **a Tárfiók elemre.**
1. Kattintson **a Létrehozás gombra.**
1. A **Tárfiók létrehozása** képernyőn:
    1. Válassza ki a megfelelő előfizetési és erőforráscsoportot.
    1. Adja meg a Data Lake Storage Gen2 fiók nevét. A tárfiók elnevezési konvencióiról az [Azure-erőforrások elnevezési konvenciói című](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)témakörben talál további információt.
    1. Kattintson a **Speciális** fülre.
    1. Kattintson **az Engedélyezés** elemre a **Hierarchikus névtér** mellett a **Data Lake Storage Gen2 csoportban.**
    1. Kattintson az **Áttekintés + létrehozás** elemre.
    1. Kattintson a **Létrehozás gombra.**

A tárfiók létrehozása során rendelkezésre álló egyéb lehetőségekről a [Rövid útmutató: Azure Data Lake Storage Gen2 tárfiók létrehozása](../storage/blobs/data-lake-storage-quickstart-create-account.md)című témakörben talál további információt.

![A tárfiók létrehozásáról az Azure Portalon látható képernyőkép](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>A felügyelt identitás engedélyeinek beállítása a Data Lake Storage Gen2 fiókban

Rendelje hozzá a felügyelt identitást a **storage blob adattulajdonosi** szerepkörhöz a tárfiókban.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot.
1. Válassza ki a tárfiókot, majd válassza **a Hozzáférés-vezérlés (IAM)** lehetőséget a fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.

    ![A tárolási hozzáférés-vezérlési beállításokat bemutató képernyőkép](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Új szerepkör hozzáadásához válassza a **+ Szerepkör-hozzárendelés hozzáadása** gombot.
1. A **Szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage Blob Data Owner** szerepkört. Ezután válassza ki a felügyelt identitás- és tárfiókkal rendelkező előfizetést. Ezután keresse meg a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást. Végül válassza ki a felügyelt identitást, és az megjelenik a **Kiválasztott tagok**csoportban.

    ![RBAC szerepkör hozzárendelését bemutató képernyőkép](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Kattintson a **Mentés** gombra. A kiválasztott felhasználó által hozzárendelt identitás most antól a kijelölt szerepkör alatt jelenik meg.
1. A kezdeti telepítés befejezése után fürtöt hozhat létre a portálon keresztül. A fürtnek ugyanabban az Azure-régióban kell lennie, mint a tárfióknak. A fürt létrehozása menü **Tároló** lapján adja meg a következő beállításokat:

    * **Az elsődleges tárolótípus**esetén válassza az **Azure Data Lake Storage Gen2**lehetőséget.
    * Az **Elsődleges tárfiók csoportban**keresse meg és válassza ki az újonnan létrehozott Data Lake Storage Gen2 tárfiókot.

    * Az **Identitás csoportban**válassza ki az újonnan létrehozott, felhasználó által hozzárendelt felügyelt identitást.

        ![Tárolási beállítások a Data Lake Storage Gen2 és az Azure HDInsight használatával](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Másodlagos Data Lake Storage Gen2-fiók hozzáadásához a tárfiók szintjén egyszerűen rendelje hozzá a korábban létrehozott felügyelt identitást az új Data Lake Storage Gen2 tárfiókhoz, amelyet hozzá szeretne adni. Kérjük, vegye figyelembe, hogy egy másodlagos Data Lake Storage Gen2-fiók hozzáadása a HDInsight "További tárfiókok" panelen keresztül nem támogatott.
    > * A HDInsight által használt Azure storage-fiókon engedélyezheti a RA-GRS vagy a RA-ZRS szolgáltatást. Azonban a fürt létrehozása az RA-GRS vagy RA-ZRS másodlagos végpont ellen nem támogatott.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Fürt létrehozása a Data Lake Storage Gen2 segítségével az Azure CLI-n keresztül

[Letöltheti a mintasablonfájlt,](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) és [letöltheti a mintaparaméterek et](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). A sablon és az alábbi Azure CLI-kódrészlet használata előtt cserélje le a következő helyőrzőket a megfelelő értékekre:

| Helyőrző | Leírás |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCEGROUPNAME>` | Az az erőforráscsoport, amelyhez az új fürt- és tárfiókot létre szeretné hozni. |
| `<MANAGEDIDENTITYNAME>` | A felügyelt identitás neve, amely engedélyt kap az Azure Data Lake Storage Gen2 fiókjához. |
| `<STORAGEACCOUNTNAME>` | Az új Azure Data Lake Storage Gen2 fiók, amely létrehozva lesz. |
| `<CLUSTERNAME>` | A HDInsight-fürt neve. |
| `<PASSWORD>` | A kiválasztott jelszó a fürtbe való bejelentkezéshez az SSH használatával, valamint az Ambari irányítópulton. |

Az alábbi kódrészlet a következő kezdeti lépéseket teszi:

1. Bejelentkezik az Azure-fiókjába.
1. Beállítja az aktív előfizetést, ahol a létrehozási műveleteket el kell végezni.
1. Új erőforráscsoportot hoz létre az új telepítési tevékenységekhez.
1. Létrehoz egy felhasználó által hozzárendelt felügyelt identitást.
1. Hozzáad egy bővítményt az Azure CLI-hez a Data Lake Storage Gen2 szolgáltatásainak használatához.
1. Új Data Lake Storage Gen2-fiókot hoz létre a `--hierarchical-namespace true` jelző használatával.

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

Ezután jelentkezzen be a portálra. Adja hozzá az új, felhasználó által hozzárendelt felügyelt identitást a **storage Blob Data Contributor** szerepkörhöz a tárfiókban, a 3. [Using the Azure portal](hdinsight-hadoop-use-data-lake-storage-gen2.md)

Miután hozzárendelte a szerepkört a felhasználó által hozzárendelt felügyelt identitáshoz, telepítse a sablont a következő kódrészlet használatával.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Fürt létrehozása a Data Lake Storage Gen2 használatával az Azure PowerShellen keresztül

A PowerShell használatával hdinsight-fürt létrehozása az Azure Data Lake Storage Gen2 használatával jelenleg nem támogatott.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Hozzáférés-vezérlés a Data Lake Storage Gen2 szolgáltatáshoz a HDInsightban

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen típusú engedélyeket támogat a Data Lake Storage Gen2?

A Data Lake Storage Gen2 olyan hozzáférés-vezérlési modellt használ, amely támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) és a POSIX-szerű hozzáférés-vezérlési listákat (ACL). A Data Lake Storage Gen1 csak az adatokhoz való hozzáférés szabályozásához támogatja a hozzáférés-vezérlési listákat.

Az RBAC szerepkör-hozzárendeléseket használ az Azure-erőforrások felhasználóira, csoportjaira és egyszerű szolgáltatásaira vonatkozó engedélyek hatékony alkalmazására. Ezek az Azure-erőforrások általában a legfelső szintű erőforrásokra vannak korlátozva (például Azure Storage-fiókok). Az Azure Storage és a Data Lake Storage Gen2 esetében ez a mechanizmus ki lett terjesztve a fájlrendszer-erőforrásra.

 Az RBAC fájlengedélyeiről az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)című témakörben talál további információt.

Az ACL-ekkel kapcsolatos fájlengedélyekről a [fájlok és könyvtárak hozzáférés-vezérlési listái](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)című témakörben talál további információt.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan szabályozhatom az adataimhoz való hozzáférést a Data Lake Storage Gen2 szolgáltatásban?

A HDInsight-fürt a Data Lake Storage Gen2 fájljaihoz való hozzáférésre felügyelt identitásokon keresztül szabályozható. A felügyelt identitás az Azure Active Directoryban (Azure AD) regisztrált identitás, amelynek hitelesítő adatait az Azure kezeli. Felügyelt identitások használatával nem kell regisztrálnia a szolgáltatásnévtagok at Azure AD vagy a hitelesítő adatok, például a tanúsítványok karbantartása.

Az Azure-szolgáltatások kétféle felügyelt identitással rendelkeznek: rendszerhez rendelt és felhasználó által hozzárendelt. A HDInsight a felhasználó által hozzárendelt felügyelt identitások segítségével éri el a Data Lake Storage Gen2 azonosítóját. A felhasználó által hozzárendelt felügyelt identitás különálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. A felügyelt identitásokról további információt a [Hogyan működnek az Azure-erőforrások felügyelt identitásai.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan állíthatok be engedélyeket az Azure AD-felhasználók számára az adatok lekérdezéséhez a Data Lake Storage Gen2-ben a Hive vagy más szolgáltatások használatával?

Adatok lekérdezésére vonatkozó engedélyek beállításához használja az Azure AD biztonsági csoportjait az ACL-ek hozzárendelt egyszerű elemként. Ne rendeljen közvetlenül fájlhozzáférési engedélyeket az egyes felhasználókhoz vagy egyszerű szolgáltatástagokhoz. Ha az Azure AD biztonsági csoportok használatával szabályozza az engedélyek áramlását, felhasználókat vagy egyszerű felhasználókat adhat hozzá és távolíthat el anélkül, hogy a teljes címtárstruktúrára újra a c-címeket alkalmazná. Csak hozzá kell adnia vagy el kell távolítania a felhasználókat a megfelelő Azure AD biztonsági csoportból. Az ACL-ek nem öröklődnek, ezért az ACL-ek újraalkalmazása minden fájlés alkönyvtár rendszerének frissítését igényli.

## <a name="access-files-from-the-cluster"></a>Fájlok elérése a fürtből

A Data Lake Storage Gen2-ben található fájlok hdinsight-fürtről többféleképpen érhetők el.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökérhez vezető elérési utat a következőre cserélheti:

    ```
    abfs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Példák adatelérési példák

Példák alapulnak [ssh kapcsolatot](./hdinsight-hadoop-linux-use-ssh-unix.md) a fürt fő csomópontja. A példák mindhárom URI-sémát használják. Cserélje `CONTAINERNAME` `STORAGEACCOUNT` ki és a vonatkozó értékeket

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancsok

1. Hozzon létre egy fájlt a helyi tárolón.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürttárolón.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürttárolóba.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. A fürttároló könyvtártartalmának listázása.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive-tábla létrehozása

Három fájlhely jelenik meg szemléltető célokra. Tényleges végrehajtáshoz csak az `LOCATION` egyik tételt használja.

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

## <a name="next-steps"></a>További lépések

* [Az Azure HDInsight integrációja a Data Lake Storage Gen2 előzetes verziójával – ACL és biztonsági frissítés](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezésével](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
