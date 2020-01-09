---
title: Fürthöz tartozó hiba szótárának létrehozása
description: Megtudhatja, hogyan hozhat létre fürtbeli hibákat a szótárban.
ms.reviewer: hrasheed
author: karkrish
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: troubleshooting
ms.date: 11/19/2019
ms.author: v-todmc
ms.openlocfilehash: e537014f741196024c24dd6ee98af0d8af2e1b31
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483062"
---
# <a name="hdinsight-cluster-creation-errors"></a>HDInsight: fürtök létrehozásával kapcsolatos hibák

Ez a cikk a fürtök létrehozásakor előforduló hibák megoldásait ismerteti. 

> [!NOTE]
> Az alábbi lista első három hibája az érvényesítési hibák miatt következik be, ha a CsmDocument_2_0 osztályt egy HDInsight-termék használja.



## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error-script-action-location-cannot-be-accessed-uriscript-action-url"></a>**Hiba**: a parancsfájl-művelet helye nem érhető el URI:\<parancsfájl műveleti URL-címe\>

**Hibaüzenet: "a távoli kiszolgáló a következő hibát adta vissza: (404) nem található."**

### <a name="cause"></a>Ok
A fürt létrehozása kérelem részeként megadott parancsfájl-műveleti URL-cím nem érhető el a HDInsight szolgáltatásból. A "ErrorMessage" hibaüzenet jelenik meg, amikor megpróbáljuk elérni a parancsfájl-műveletet.

### <a name="resolution"></a>Felbontás 
  - HTTP/HTTPS URL esetén ellenőrizheti, hogy az URL-címet egy inkognitóban-böngészőablakból próbálja megnyitni. 
  - WASB URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a kérelemben megadott Storage-fiókban. Győződjön meg arról, hogy a Storage-fiókhoz tartozó tárolási kulcs pontos. 
  - ADLS URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a Storage-fiókban.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error-script-action-location-cannot-be-accessed-uri-script-action-url"></a>**Hiba**: a parancsfájl-művelet helye nem érhető el URI: \<parancsfájl műveleti URL-címe\>

**Hibaüzenet: az adott parancsfájl URI-ja \<a parancsfájl URI-\> a ADLS-ben, de a fürthöz nem tartozik a (z) adat-Lake Storage rendszerbiztonsági tag**

### <a name="cause"></a>Ok
A fürt létrehozása kérelem részeként megadott parancsfájl-műveleti URL-cím nem érhető el a HDInsight szolgáltatásból. A "ErrorMessage" hibaüzenet jelenik meg, amikor megpróbáljuk elérni a parancsfájl-műveletet. 

### <a name="resolution"></a>Felbontás

Győződjön meg arról, hogy a megfelelő Azure Data Lake Store 1. generációs fiók hozzá van adva a fürthöz. A Azure Data Lake Store Gen 1 fiók eléréséhez használt egyszerű szolgáltatásnév a fürthöz is hozzá lesz adva. 

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error-vm-size-customer-specified-vm-size-provided-in-the-request-is-invalid-or-not-supported-for-role-role-valid-values-are-valid-vm-size-for-role"></a>**Hiba**: a virtuális gép mérete "\<ügyfél által megadott VIRTUÁLISGÉP-méret\>" a kérelemben megadott érték érvénytelen vagy nem támogatott a (z) "\<szerepkör\>" szerepkörben. AZ érvényes értékek: \<a SZEREPKÖR-\>érvényes virtuálisgép-MÉRETet.

### <a name="cause"></a>Ok
Az ügyfél által megadott virtuálisgép-méretek nem engedélyezettek a szerepkörhöz. Ez igaz lehet, mert a virtuális gép mérete nem a várt módon működik, vagy nem megfelelő a számítógép szerepkörhöz. 

### <a name="resolution"></a>Felbontás
A hibaüzenet felsorolja a virtuális gép méretének érvényes értékeit. Válasszon egyet ezek közül az érvényes értékek közül, majd próbálja megismételni a fürt létrehozása kérelmet. 

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Hibakód: InvalidVirtualNetworkId  

### <a name="error-the-virtualnetworkid-is-not-valid-virtualnetworkid-user_virtualnetworkid"></a>**Hiba**: a VirtualNetworkId érvénytelen. VirtualNetworkId "\<USER_VIRTUALNETWORKID\>" *

### <a name="cause"></a>Ok
A fürt létrehozása során megadott **VirtualNetworkId** érték formátuma nem megfelelő. 

### <a name="resolution"></a>Felbontás
Győződjön meg arról, hogy a **VirtualNetworkId** és az alhálózat formátuma megfelelő. A **VirtualnetworkId** érték beszerzéséhez nyissa meg a Azure Portal, válassza ki a virtuális hálózatot, majd válassza a menü **Tulajdonságok** elemét. A **ResourceId** tulajdonság a **VirtualNetworkId** érték. 

Példa virtuális hálózati AZONOSÍTÓra: 

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" 

---

## <a name="error-code-customizationfailederrorcode"></a>Hibakód: CustomizationFailedErrorCode

### <a name="error-cluster-deployment-failed-due-to-an-error-in-the-custom-script-action-failed-actions-script_name-please-go-to-ambari-ui-to-further-debug-the-failure"></a>**Hiba**: a fürt központi telepítése nem sikerült, mert hiba történt az egyéni parancsfájl műveletében. Sikertelen műveletek: \<SCRIPT_NAME\>, lépjen a Ambari felhasználói felületén a hiba további hibakereséséhez.

### <a name="cause"></a>Ok
A fürt létrehozásakor megadott egyéni parancsfájl végrehajtása a fürt sikeres telepítése után történik. Ez a hibakód azt jelzi, hogy hiba történt a \<SCRIPT_NAME\>nevű egyéni parancsfájl végrehajtása során.   

### <a name="resolution"></a>Felbontás
Mivel ez a felhasználó egyéni parancsfájlja, a felhasználóknak el kell hárítaniuk a problémát, és szükség esetén újra kell futtatni a parancsfájlt. A parancsfájl hibáinak megoldásához vizsgálja meg a/var/lib/ambari-Agent/* mappában található naplókat. Vagy nyissa meg a Ambari felhasználói felületén az Operations (műveletek) lapot, majd a hiba részleteinek megtekintéséhez válassza ki a **run_customscriptionaction** műveletet. 

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Hibakód: InvalidDocumentErrorCode

### <a name="error-the-meta_store_type-metastore-schema-version-metastore_major_version-in-database-database_name-is-incompatible-with-cluster-version-cluster_version"></a>**Hiba**: a \<META_STORE_TYPE\> metaadattár-séma verziója \<METASTORE_MAJOR_VERSION\> az adatbázisban \<DATABASE_NAME\> nem kompatibilis a fürt verziójával \<CLUSTER_VERSION\>

### <a name="cause"></a>Ok
Az egyéni metaadattár nem kompatibilis a kiválasztott HDInsight-fürt verziójával. A HDInsight 4,0-fürt jelenleg csak a 3. Metaadattár-verziót támogatja. az *x*és a HDInsight 3,6 nem támogatja a metaadattár 3-as verzióját. *x* vagy újabb. 

### <a name="resolution"></a>Felbontás
Ügyeljen arra, hogy csak az egyes HDInsight-fürtök által támogatott Metaadattár-verziókat használja. Figyelje meg, hogy ha nincs megadva egyéni metaadattár, a HDInsight belsőleg létrehoz egy metaadattár. Ez a metaadattár azonban automatikusan törlődik a fürt törlése során. 

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Hibakód: FailedToConnectWithClusterErrorCode 

### <a name="error-unable-to-connect-to-cluster-management-endpoint-to-perform-scaling-operation-verify-that-network-security-rules-are-not-blocking-external-access-to-the-cluster-and-that-the-cluster-manager-ambari-ui-can-be-successfully-accessed"></a>**Hiba**: nem sikerült kapcsolódni a fürt felügyeleti végponthoz a skálázási művelet végrehajtásához. Ellenőrizze, hogy a hálózati biztonsági szabályok nem blokkolja-e a fürt külső elérését, és hogy a Fürtfelügyelő (Ambari) felhasználói felülete sikeresen elérhető-e.

### <a name="cause"></a>Ok
Van egy tűzfalszabály a hálózati biztonsági csoportban (NSG), amely blokkolja a kritikus Azure állapot-és felügyeleti szolgáltatásokkal folytatott kommunikációt. 

### <a name="resolution"></a>Felbontás
Ha **hálózati biztonsági csoportokat** kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket: 
  - Azonosítsa az HDInsight használni kívánt Azure-régiót. 
  - Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight kezelése – IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
    - Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et. 
    - **Hálózati biztonsági csoportok:** A **bejövő** forgalom engedélyezése az IP-címekről a **443** -as porton . Ez biztosítja, hogy a HDInsight-felügyeleti szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt. 

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Hibakód: StoragePermissionsBlockedForMsi  

### <a name="error-the-managed-identity-does-not-have-permissions-on-the-storage-account-please-verify-that-storage-blob-data-owner-role-is-assigned-to-the-managed-identity-for-the-storage-account-storage-subscriptions-subscription-id-resourcegroups-resource-group-name-providersmicrosoftstoragestorageaccounts-storage-account-name-managed-identity-subscriptions-subscription-id-resourcegroups--resource-group-name-providersmicrosoftmanagedidentityuserassignedidentities-user-managed-identity-name"></a>**Hiba**: a felügyelt identitás nem rendelkezik engedélyekkel a Storage-fiókhoz. Ellenőrizze, hogy a "Storage blob-adatok tulajdonosa" szerepkör hozzá van-e rendelve a Storage-fiók felügyelt identitásához. Storage:/Subscriptions/\<előfizetés-azonosító\>/resourceGroups/\< erőforráscsoport neve\>/providers/Microsoft.Storage/storageAccounts/\<Storage-fiók neve\>, felügyelt identitás:/Subscriptions/\<előfizetés azonosítója\>/resourceGroups//\< erőforráscsoport neve\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<felhasználó által felügyelt identitás neve\>

### <a name="cause"></a>Ok
A szükséges engedélyek nem voltak megadva az **identitás kezeléséhez.** A **felhasználó által hozzárendelt felügyelt identitás** nem rendelkezett blob Storage közreműködő szerepkörrel ADLS Gen2 Storage-fiókban. 

### <a name="resolution"></a>Felbontás

Nyissa meg a Azure Portal, lépjen a Storage-fiókjába, keresse meg a **Access Control (iam)** lehetőséget, és győződjön meg arról, hogy a Storage blob-adatközreműködői vagy a Storage blob-adatok tulajdonosi szerepköre "" hozzárendeléssel rendelkezik a **felhasználó által hozzárendelt felügyelt identitáshoz** az előfizetéshez. További információ: az [Data Lake Storage Gen2 fiókban található felügyelt identitás engedélyeinek beállítása](hdinsight-hadoop-use-data-lake-storage-gen2.md). 

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Hibakód: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error-the-security-rules-in-the-network-security-group-subscriptionssubscriptionidresourcegroupsresource-group-name-defaultprovidersmicrosoftnetworknetworksecuritygroupsnetwork-security-group-name-configured-with-subnet-subscriptionssubscriptionidresourcegroupsresource-group-name-rg-westeurope-vnet-tomtom-defaultprovidersmicrosoftnetworkvirtualnetworksvirtual-network-namesubnetssubnet-name"></a>**Hiba**: a (z)/Subscriptions/hálózati biztonsági csoport biztonsági szabályai\<SubscriptionID >\/resourceGroups/<Resource Group name> default/Providers/Microsoft. Network/networkSecurityGroups/\<hálózati biztonsági csoport neve\> konfigurálva az alhálózat/Subscriptions/\<SubscriptionID >\/resourceGroups/\<erőforráscsoport neve > RG-westeurope-vnet-TomTom-default\/Providers/Microsoft. Network/virtualNetworks/\<Virtual Hálózati név >\/alhálózatok/\<alhálózat neve\> 
a nem engedélyezi a szükséges bejövő és/vagy kimenő kapcsolatokat. További információért látogasson el [a virtuális hálózat megtervezése az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment), vagy forduljon az ügyfélszolgálathoz. * *

### <a name="cause"></a>Ok
Ha hálózati biztonsági csoportokat (NSG) vagy felhasználó által definiált útvonalakat (UDR) használ a HDInsight-fürt bejövő forgalmának vezérléséhez, meg kell győződnie arról, hogy a fürt képes kommunikálni a kritikus Azure állapot-és felügyeleti szolgáltatásokkal.

### <a name="resolution"></a>Felbontás
Ha **hálózati biztonsági csoportokat** kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket: 
  - Azonosítsa a HDInsight használni kívánt Azure-régiót, és hozzon létre egy biztonságos listát a régió IP-címeiről: [Health and Management Services: egyes régiók](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
  - Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight-felügyeleti IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses). 
  - Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et. **Hálózati biztonsági csoportok**: engedélyezze a bejövő adatforgalmat az **443** -es porton az IP-címekről . Ezzel biztosíthatja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Hibakód: a fürt telepítése nem tudta telepíteni az összetevőket egy vagy több gazdagépre

###  <a name="errorcluster-setup-failed-to-install-components-on-one-or-more-hosts-please-retry-your-request"></a>**Hiba**: a fürt telepítése nem tudta telepíteni az összetevőket egy vagy több gazdagépre. Próbálkozzon újra a kéréssel. 

### <a name="cause"></a>Ok 
Ez a hiba általában akkor jön létre, ha átmeneti probléma merül fel, vagy egy Azure-leállás van 

### <a name="resolution"></a>Felbontás

Az [Azure állapot](https://status.azure.com/status) lapján keresse meg az esetleges Azure-kimaradásokat, amelyek hatással lehetnek a fürt üzembe helyezésére. Ha nincsenek kimaradások, próbálja megismételni a fürt üzembe helyezését. 

## <a name="next-steps"></a>Következő lépések

A fürtök létrehozásával kapcsolatos hibák elhárításával kapcsolatos további információk: [fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
