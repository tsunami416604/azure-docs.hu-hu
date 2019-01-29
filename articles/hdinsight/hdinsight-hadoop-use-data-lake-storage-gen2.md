---
title: Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök
description: Ismerje meg, hogyan hozhat létre Azure Data Lake Storage Gen2 Azure HDInsight-fürtök használatát.
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: 9a1d0775c12d424c35e9e9d366f69e07ec9b1468
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096976"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök

Az Azure Data Lake Storage (a Data Lake Storage) Gen2 olyan big data-analitika, Azure Blob storage épülő dedikált képességeit. Data Lake Storage Gen2 az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeinek egyesítésével. Egy szolgáltatás, amely az Azure Data Lake Storage Gen1, például a fájlrendszer szemantikáját, könyvtárat, és a fájl biztonsági és méretezési csoport alacsony költségű, többrétegű tárolást, valamint magas rendelkezésre állás/vész-helyreállítási lehetőségei az Azure-Blobból kínálja eredménye Storage.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 rendelkezésre állása

Az Azure Data Lake Storage Gen2 alapértelmezett és a egy tárfiókot, szinte az összes Azure HDInsight-fürttípusok a tárolási beállításként érhető el. A HBase, azonban legfeljebb egy Data Lake Storage Gen2-fiókot.

> [!Note] 
> Miután kiválasztotta a Data Lake Storage Gen2, mint a **elsődleges tárolási típusok**, nem választhatja ki a Data Lake Storage Gen1 fiók kiegészítő tárolóként.

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Egy HDInsight-fürt létrehozása a Data Lake Storage Gen2

Hozzon létre egy HDInsight-fürtöt, Data Lake Storage Gen2 használ a tároláshoz, amely a következő lépések segítségével hozzon létre egy Data Lake Storage Gen2-fiókot, hogy megfelelően van konfigurálva.

1. Felhasználó által hozzárendelt felügyelt identitás, létrehozása, ha még nem rendelkezik. Lásd: [létrehozása, list, delete vagy egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások szerepkör hozzárendelése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

    ![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot. Ügyeljen arra, hogy a **hierarchikus fájlrendszer** beállítás engedélyezve van. Lásd: [a rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot](../storage/blobs/data-lake-storage-quickstart-create-account.md) további részletekért.

    ![Képernyőfelvétel: a tárfiók létrehozása az Azure Portalon](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. A felügyelt identitás hozzárendelése a **Storage-Blobadatok Közreműködője (előzetes verzió)** szerepkör a tárfiókban. Lásd: [kezelése hozzáférési jogosultsággal az adatok Azure Blob és üzenetsor RBAC (előzetes verzió)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.
    1. Válassza ki a tárfiókot, majd válassza ki **hozzáférés-vezérlés (IAM)** a fiókhoz tartozó hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.
    
        ![Képernyőfelvétel: a tárolási hozzáférés-vezérlési beállításokkal](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
    1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage-Blobadatok Közreműködője (előzetes verzió)** szerepkör. Ezután válassza ki a felügyelt identitás- és storage-fiókot tartalmazó előfizetést. Ezután keresse meg a felhasználó által hozzárendelt felügyelt identitás, amelyet korábban hozott létre. Végül válassza ki a felügyelt identitást, és alatt helyezkednek el **kiválasztott tagok**.
    
        ![Képernyőfelvétel: hogyan kell egy RBAC szerepkör hozzárendelése](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. Kattintson a **Save** (Mentés) gombra. A kiválasztott felhasználó által hozzárendelt identitás alatt most már szerepel a **közreműködői** szerepkör.

    1. A kezdeti telepítés befejezése után létrehozhat egy fürtöt a portálon keresztül. A fürt a tárfiók Azure ugyanabban a régióban kell lennie. Az a **tárolási** szakasz a fürt létrehozási menü, válassza ki a következő beállításokat:
        
        * A **elsődleges tárolási típusok**, kattintson a **Azure Data Lake Storage Gen2**.
        * A **válassza ki a Tárfiókot**, keresse meg és válassza ki a Data Lake Storage Gen2 újonnan létrehozott tárfiókot.
        
            ![Az Azure HDInsight használata a Data Lake Storage Gen2-tárolási beállításai](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * A **identitás** válassza ki a megfelelő előfizetést, és az újonnan létrehozott felhasználó által hozzárendelt felügyelt identitás.
        
            ![A Data Lake Storage Gen2 használata Azure HDInsight identitás](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Hozzáférés-vezérlés a Data Lake Storage Gen2 a HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Milyen típusú engedélyeket támogatja a Data Lake Storage Gen2?

Az Azure Data Lake Storage Gen2-hozzáférés-vezérlési modellt, amely támogatja az Azure szerepkör alapú hozzáférés-vezérlés (RBAC) és a POSIX-hoz hasonló hozzáférés-vezérlési listák (ACL) valósítja meg. A Data Lake Storage Gen1 csak a támogatott hozzáférés-vezérlési listák adatokhoz való hozzáférés szabályozása.

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör-hozzárendelések használja a alkalmazni az engedélyek beállítása a felhasználók, csoportok és az egyszerű szolgáltatások Azure-erőforrások. Általában ezek az Azure előrébb a legfelső szintű erőforrásokhoz (például Azure Storage-fiókok). Az Azure Storage, és emellett az Azure Data Lake Storage Gen2 Ez a mechanizmus most már elérhető a fájl rendszererőforrás.

 A fájl engedélyeit az RBAC további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

A hozzáférés-vezérlési fájlengedélyek további információkért lásd: [hozzáférés-vezérlési listák a fájlok és könyvtárak](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>Hogyan vezérlőelem hozzáférés Gen2 az adataimat?

Felügyelt identitások arra, hogy a HDInsight-fürt a Data Lake Storage Gen2 lévő fájlok eléréséhez az szabályozza. Egy felügyelt identitás az identitás, amelynek hitelesítő adatait az Azure által felügyelt Azure AD-ben regisztrált. Nem kell regisztrálni az egyszerű szolgáltatások Azure AD-ben és a hitelesítő adatok, például a tanúsítványok kezelése.

Felügyelt identitások Azure-szolgáltatások két típusa van: rendszer-, felhasználó hozzárendelve. Az Azure HDInsight a felügyelt identitásokból felhasználó által hozzárendelt hozzáférés az Azure Data Lake Storage Gen2 használja. Egy felhasználó által hozzárendelt felügyelt identitás egy önálló Azure-erőforrás jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználó által hozzárendelt identitások életciklusa külön van kezelve azon Azure-beli szolgáltatáspéldányokétól, amelyekhez hozzá lettek rendelve. A felügyelt identitásokból további információkért lásd: [hogyan történik az Azure-erőforrások munka a felügyelt identitásokból](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>Hogyan állíthatok be az Azure AD-felhasználók engedélyek használatával adatokat lekérdezni a Data Lake Storage Gen2 használatával Hive- vagy más szolgáltatások?

A hozzárendelt egyszerű hozzáférés-vezérlési listák az Azure AD biztonsági csoportjait használja. Közvetlenül ne rendeljen egyéni felhasználók vagy egyszerű szolgáltatások a fájlhozzáférési engedélyeket. Ha AD biztonsági csoportok segítségével engedélyeket szabályozásához, hozzáadhat és távolítsa el a felhasználók vagy egyszerű szolgáltatások újbóli ACL-eket egy teljes könyvtárstruktúrát nélkül. Csak akkor kell hozzáadni vagy eltávolítani a felhasználókat a megfelelő Azure AD biztonsági csoportot. Hozzáférés-vezérlési listák nem öröklődnek, és ezért az ACL-ek újbóli igényel, az ACL-t minden fájl és alkönyvtárat frissítése.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Lake Storage Gen2 előzetes verzió használata Azure HDInsight-fürtök](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Az Azure HDInsight-integrációja az Data Lake Storage Gen2 előzetes verzió – hozzáférés-szabályozási és biztonsági frissítése](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Bevezetés az Azure Data Lake Storage Gen2 előzetes verzió](../storage/blobs/data-lake-storage-introduction.md)
