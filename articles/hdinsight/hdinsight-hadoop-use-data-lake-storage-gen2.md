---
title: Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel
description: Ismerje meg, hogyan használhatja a Azure Data Lake Storage Gen2t az Azure HDInsight-fürtökkel.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: hrasheed
ms.openlocfilehash: e06d6473a47dcff3506843150375c70ed2bd8cea
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061838"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel

A Azure Data Lake Storage Gen2 egy Azure Blob Storage-ra épülő, big data elemzésre szánt felhőalapú tárolási szolgáltatás. Data Lake Storage Gen2 ötvözi az Azure Blob Storage és a Azure Data Lake Storage Gen1 képességeit. Az eredményül kapott szolgáltatás a Azure Data Lake Storage Gen1, például a fájlrendszer szemantikai, a címtár-és a fájl szintű biztonságot, valamint a méretezhetőséget, valamint az alacsony költségeket, a többszintű tárolást, a magas rendelkezésre állást és a vész-helyreállítási képességeket kínálja. Az Azure Blob Storage-ból.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állás

A Data Lake Storage Gen2 tárolási lehetőségként érhető el szinte minden Azure HDInsight-fürthöz, alapértelmezettként és egy további Storage-fiókként is. A HBase azonban csak egy Data Lake Storage Gen2 fiókkal rendelkezhet.

A fürt létrehozási lehetőségeinek Data Lake Storage Gen2 használatával történő teljes összehasonlítását lásd: a [tárolási lehetőségek összehasonlítása az Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)-fürtökkel való használathoz.

> [!Note]  
> Miután kiválasztotta a Data Lake Storage Gen2 **elsődleges tárolási típusként**, nem választhat Data Lake Storage Gen1-fiókot további tárolóként.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Hozzon létre egy fürtöt Data Lake Storage Gen2 segítségével a Azure Portal

Data Lake Storage Gen2 for Storage-t használó HDInsight-fürt létrehozásához kövesse az alábbi lépéseket egy Data Lake Storage Gen2-fiók konfigurálásához.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyennel. A Azure Portal használatával megtekintheti a [felhasználó által hozzárendelt felügyelt identitáshoz tartozó szerepkörök létrehozását, listázását, törlését és hozzárendelését](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity). További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](hdinsight-managed-identities.md).

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 fiók létrehozása

Hozzon létre egy Azure Data Lake Storage Gen2 Storage-fiókot. Győződjön meg arról, hogy a **hierarchikus névtér** beállítás engedélyezve van. További információ: gyors útmutató [: Hozzon létre egy Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md)Storage-fiókot.

![A Storage-fiók létrehozását bemutató képernyőkép a Azure Portal](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>A felügyelt identitásra vonatkozó engedélyek beállítása a Data Lake Storage Gen2 fiókban

Rendelje hozzá a felügyelt identitást a Storage- **blob** adattulajdonosi szerepkörhöz a Storage-fiókban. További információkért lásd: [kezelés hozzáférési jogosultságok RBAC (előzetes verzió) az adatok Azure Blob és üzenetsor](../storage/common/storage-auth-aad-rbac.md).

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.
1. Válassza ki a Storage-fiókját, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget a fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör** -hozzárendelések lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
    
    ![A Storage hozzáférés-vezérlési beállításait ábrázoló képernyőfelvétel](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. Új szerepkör hozzáadásához kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage blob-adat tulajdonosi** szerepkörét. Ezután válassza ki azt az előfizetést, amelynek a felügyelt identitás-és Storage-fiókja van. Ezután a Keresés gombra kattintva keresse meg a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást. Végül válassza ki a felügyelt identitást, és a rendszer a **kijelölt tagok**területen jelenik meg.
    
    ![A RBAC-szerepkör hozzárendelését bemutató képernyőkép](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. Kattintson a **Mentés** gombra. A kiválasztott felhasználó által hozzárendelt identitás mostantól a kiválasztott szerepkör alatt szerepel.
1. A kezdeti beállítás befejezése után létrehozhat egy fürtöt a portálon keresztül. A fürtnek ugyanabban az Azure-régióban kell lennie, mint a Storage-fióknak. A fürt létrehozása menü **tárterület** területén válassza a következő beállításokat:
        
    * Az **elsődleges tároló típusa**beállításnál válassza a **Azure Data Lake Storage Gen2**lehetőséget.
    * A **Storage-fiók kiválasztása**területen keresse meg és válassza ki az újonnan létrehozott Data Lake Storage Gen2 Storage-fiókot.
        
        ![A Data Lake Storage Gen2 Azure HDInsight való használatának tárolási beállításai](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * Az **identitás**területen válassza ki a megfelelő előfizetést és az újonnan létrehozott, felhasználó által hozzárendelt felügyelt identitást.
        
        ![Az Data Lake Storage Gen2 Azure HDInsight való használatának identitás-beállításai](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Másodlagos Data Lake Storage Gen2 fiók hozzáadásához a Storage-fiók szintjén egyszerűen rendelje hozzá a korábban létrehozott felügyelt identitást a hozzáadni kívánt új Data Lake Storage Gen2 Storage-fiókhoz. Javasoljuk, hogy a HDInsight nem támogatja a másodlagos Data Lake Storage Gen2 fiók hozzáadását a "további Storage-fiókok" panelen. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Data Lake Storage Gen2-fürt létrehozása az Azure CLI-vel

Letöltheti [a minta sablonfájl fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) , és [letöltheti a minta paramétereinek fájlját](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). A sablon és az Azure CLI-kódrészlet használata előtt cserélje le a következő helyőrzőket a megfelelő értékekre:

| Helyőrző | Leírás |
|---|---|
| `<SUBSCRIPTION_ID>` | Az Azure-előfizetés azonosítója |
| `<RESOURCEGROUPNAME>` | Az az erőforráscsoport, amelyben létre szeretné hozni az új fürtöt és a Storage-fiókot. |
| `<MANAGEDIDENTITYNAME>` | Annak a felügyelt identitásnak a neve, amely engedélyt kap a Azure Data Lake Storage Gen2-fiókra. |
| `<STORAGEACCOUNTNAME>` | A létrehozandó új Azure Data Lake Storage Gen2 fiók. |
| `<CLUSTERNAME>` | A HDInsight-fürt neve. |
| `<PASSWORD>` | Az Ön által választott jelszó az SSH-val és a Ambari irányítópulttal való bejelentkezéshez a fürtbe. |

Az alábbi kódrészlet a következő kezdeti lépéseket hajtja végre:

1. Bejelentkezik az Azure-fiókjába.
1. Beállítja azt az aktív előfizetést, amelyben a létrehozási műveletek lesznek végrehajtva.
1. Létrehoz egy új erőforráscsoportot az új központi telepítési tevékenységekhez. 
1. Felhasználó által hozzárendelt felügyelt identitást hoz létre.
1. Bővítmény hozzáadását az Azure CLI-hez a Data Lake Storage Gen2 szolgáltatásainak használatához.
1. Létrehoz egy új Data Lake Storage Gen2 fiókot a `--hierarchical-namespace true` jelző használatával. 

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

Ezután jelentkezzen be a portálra. Adja hozzá az új felhasználó által hozzárendelt felügyelt identitást a Storage- **blob adatközreműködői** szerepkörhöz a Storage-fiókban a [Azure Portal használata](hdinsight-hadoop-use-data-lake-storage-gen2.md)című témakör 3. lépésében leírtak szerint.

Miután hozzárendelte a szerepkört a felhasználóhoz rendelt felügyelt identitáshoz, a következő kódrészlet használatával telepítse a sablont.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Data Lake Storage Gen2 hozzáférés-vezérlése a HDInsight-ben

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen engedélyeket Data Lake Storage Gen2 támogatni?

Data Lake Storage Gen2 egy hozzáférés-vezérlési modellt használ, amely támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) és a POSIX-hez hasonló hozzáférés-vezérlési listákat (ACL-eket). A Data Lake Storage Gen1 csak az adathozzáférés szabályozásához támogatja a hozzáférés-vezérlési listát.

A RBAC szerepkör-hozzárendeléseket használ az Azure-erőforrásokhoz tartozó felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz tartozó engedélyek hatékony alkalmazására. Ezek az Azure-erőforrások jellemzően legfelső szintű erőforrásokra (például Azure Storage-fiókokra) vannak korlátozva. Az Azure Storage esetében és a Data Lake Storage Gen2 is, ez a mechanizmus a fájlrendszer erőforrására lett kiterjesztve.

 További információ a RBAC-mel rendelkező fájlokra vonatkozó engedélyekről: [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Az ACL-ekkel rendelkező fájlengedélyek részletes ismertetését lásd: [hozzáférés-vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hogyan szabályozhatja az adataik elérését Data Lake Storage Gen2ban?

A HDInsight-fürtnek a Data Lake Storage Gen2 lévő fájlokhoz való hozzáférését felügyelt identitások vezérlik. A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitások esetében nem kell regisztrálnia az Azure AD szolgáltatásban, vagy a hitelesítő adatokat, például a tanúsítványokat kell fenntartania.

Az Azure-szolgáltatások két típusú felügyelt identitással rendelkeznek: rendszerhez rendelt és felhasználó által hozzárendelt. A HDInsight felhasználó által hozzárendelt felügyelt identitásokat használ a Data Lake Storage Gen2 eléréséhez. A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz.

A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. További információ a felügyelt identitásokról: [hogyan működnek az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hogyan az Azure AD-felhasználók számára a kaptár vagy más szolgáltatások használatával történő adatlekérdezésre vonatkozó engedélyeket Data Lake Storage Gen2.

A felhasználók számára az adatlekérdezésre vonatkozó engedélyek megadásához az Azure AD biztonsági csoportokat használja az ACL-ekben a hozzárendelt elsődlegesként. Ne rendeljen hozzá közvetlenül hozzáférési engedélyeket az egyes felhasználókhoz vagy egyszerű szolgáltatásokhoz. Ha Azure AD-alapú biztonsági csoportokat használ az engedélyek áramlásának szabályozására, hozzáadhat és eltávolíthat felhasználókat vagy egyszerű szolgáltatásokat anélkül, hogy az ACL-eket újra kellene alkalmazni egy teljes címtár-struktúrára. A megfelelő Azure AD biztonsági csoportból csak a felhasználókat kell hozzáadnia vagy eltávolítania. Az ACL-ek nem öröklődnek, ezért az ACL-ek újraalkalmazása megköveteli az ACL frissítését minden fájlon és alkönyvtáron.

## <a name="next-steps"></a>További lépések

* [Azure HDInsight-integráció Data Lake Storage Gen2 előzetes verzióval – ACL és biztonsági frissítés](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Bevezetés a Azure Data Lake Storage Gen2ba](../storage/blobs/data-lake-storage-introduction.md)
