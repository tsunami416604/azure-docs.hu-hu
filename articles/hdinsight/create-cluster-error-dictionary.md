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
ms.openlocfilehash: ee9ed5374b12c3130d952770a4be6a759e37339a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156862"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure-HDInsight: fürtök létrehozásával kapcsolatos hibák

Ez a cikk a fürtök létrehozásakor esetlegesen előforduló hibák megoldásait ismerteti.

> [!NOTE]
> A cikkben ismertetett első három hiba érvényesítési hiba. Akkor fordulhatnak elő, amikor egy Azure HDInsight-termék a **CsmDocument_2_0** osztályt használja.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error"></a>Hiba

"A parancsfájl-művelet helye nem érhető el URI:\<parancsfájl műveleti URL-címe\>"

#### <a name="error-message"></a>Hibaüzenet

"A távoli kiszolgáló a következő hibát adta vissza: (404) nem található."

### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem fér hozzá a fürt létrehozása kérelem részeként megadott parancsfájl-művelet URL-címéhez. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbál hozzáférni a parancsfájl művelethez.

### <a name="resolution"></a>Felbontás

- HTTP-vagy HTTPS-URL-cím esetén ellenőrizze, hogy az URL-cím egy inkognitóban böngészőablakban található-e.
- WASB URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a kérelemben megadott Storage-fiókban. Győződjön meg arról is, hogy a Storage-fiókhoz tartozó tárolási kulcs helyes.
- ADLS URL-cím esetén győződjön meg arról, hogy a parancsfájl létezik a Storage-fiókban.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error"></a>Hiba

"A parancsfájl-művelet helye nem érhető el URI: \<SCRIPT_ACTION_URL\>"

#### <a name="error-message"></a>Hibaüzenet

"Az adott parancsfájl URI-ja \<SCRIPT_URI\> a ADLS, de ez a fürt nem rendelkezik a (z)"

### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem fér hozzá a fürt létrehozása kérelem részeként megadott parancsfájl-művelet URL-címéhez. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbál hozzáférni a parancsfájl művelethez.

### <a name="resolution"></a>Felbontás

Adja hozzá a megfelelő Azure Data Lake Storage 1. generációs fiókot a fürthöz. Adja hozzá azt a szolgáltatásnevet is, amely hozzáfér a Data Lake Storage Gen 1 fiókhoz a fürthöz.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: a (z) "CsmDocument_2_0" DeploymentDocument nem tudta végrehajtani az ellenőrzést.

### <a name="error"></a>Hiba

A kérelemben megadott "virtuálisgép-méret"\<CUSTOMER_SPECIFIED_VM_SIZE\>érvénytelen vagy nem támogatott a (z) "\<SZEREPKÖR\>" szerepkörben. Az érvényes értékek a következők: \<VALID_VM_SIZE_FOR_ROLE\>. "

### <a name="cause"></a>Ok

A virtuális gép megadott mérete nem engedélyezett a szerepkörhöz. Ez a hiba akkor fordulhat elő, ha a virtuális gép mérete nem a várt módon működik, vagy nem megfelelő a számítógép szerepkörhöz.

### <a name="resolution"></a>Felbontás

A hibaüzenet felsorolja a virtuális gép méretének érvényes értékeit. Válassza ki az alábbi értékek egyikét, majd próbálja megismételni a fürt létrehozása kérelmet.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Hibakód: InvalidVirtualNetworkId  

### <a name="error"></a>Hiba

"A VirtualNetworkId érvénytelen. VirtualNetworkId "\<USER_VIRTUALNETWORKID\>" * "

### <a name="cause"></a>Ok

A fürt létrehozása során megadott **VirtualNetworkId** érték formátuma nem megfelelő.

### <a name="resolution"></a>Felbontás

Győződjön meg arról, hogy a **VirtualNetworkId** és az alhálózati értékek a megfelelő formátumban vannak. A **VirtualNetworkId** értékének lekérése:

1. Nyissa meg az Azure Portalt.
1. Válassza ki a virtuális hálózatot.
1. Válassza a **Tulajdonságok** menüpontot. A **ResourceId** tulajdonság értéke a **VirtualNetworkId** érték.

Íme egy példa a virtuális hálózati AZONOSÍTÓra:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Hibakód: CustomizationFailedErrorCode

### <a name="error"></a>Hiba

"A fürt központi telepítése nem sikerült, mert hiba történt az egyéni parancsfájl műveletében. Sikertelen műveletek: \<SCRIPT_NAME\>, lépjen a Ambari felhasználói felületén a hiba további hibakereséséhez. "

### <a name="cause"></a>Ok

A fürt létrehozásakor megadott egyéni parancsfájl végrehajtása a fürt sikeres telepítése után történik. Ez a hibakód azt jelzi, hogy hiba történt a \<SCRIPT_NAME\>nevű egyéni parancsfájl végrehajtása során.

### <a name="resolution"></a>Felbontás

Mivel a szkript az egyéni szkript, javasoljuk, hogy hárítsa el a problémát, és szükség esetén futtassa újra a parancsfájlt. A parancsfájl hibáinak megoldásához vizsgálja meg a/var/lib/ambari-Agent/* mappában található naplókat. Vagy nyissa meg a Ambari felhasználói felületén az **Operations (műveletek** ) lapot, majd a hiba részleteinek megtekintéséhez válassza ki a **run_customscriptaction** műveletet.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Hibakód: InvalidDocumentErrorCode

### <a name="error"></a>Hiba

"A \<META_STORE_TYPE\> Metaadattár-sémájának verziója \<METASTORE_MAJOR_VERSION\> az adatbázisban \<DATABASE_NAME\> nem kompatibilis a fürt verziójával \<CLUSTER_VERSION\>"

### <a name="cause"></a>Ok

Az egyéni metaadattár nem kompatibilis a kiválasztott HDInsight-fürt verziójával. A HDInsight 4,0-fürtök jelenleg csak a 3,0-es és újabb Metaadattár-verziót támogatják, míg a HDInsight 3,6-fürtök nem támogatják a Metaadattár 3,0-es és újabb verzióit.

### <a name="resolution"></a>Felbontás

Csak azokat a Metaadattár-verziókat használja, amelyeket a HDInsight-fürt verziója támogat. Ha nem ad meg egyéni metaadattár, a HDInsight belsőleg létrehoz egy metaadattár, majd törli azt a fürt törlése után.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Hibakód: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Hiba

"Nem lehet csatlakozni a fürt felügyeleti végponthoz a skálázási művelet végrehajtásához. Ellenőrizze, hogy a hálózati biztonsági szabályok nem blokkolja-e a fürt külső hozzáférését, és hogy a Fürtfelügyelő (Ambari) felhasználói felülete sikeresen elérhető-e. "

### <a name="cause"></a>Ok

A hálózati biztonsági csoporton (NSG) található tűzfalszabály blokkolja a kritikus Azure állapot-és felügyeleti szolgáltatásokkal folytatott kommunikációt.

### <a name="resolution"></a>Felbontás

Ha hálózati biztonsági csoportokat kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket:

- Azonosítsa az HDInsight használni kívánt Azure-régiót.
- Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight kezelése – IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et.
  - Hálózati biztonsági csoportok esetében engedélyezze a bejövő forgalmat az 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Hibakód: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Hiba

"A felügyelt identitásnak nincs engedélye a Storage-fiókra. Ellenőrizze, hogy a "Storage blob-adatok tulajdonosa" szerepkör hozzá van-e rendelve a Storage-fiók felügyelt identitásához. Storage:/Subscriptions/\<előfizetés-azonosító\>/resourceGroups/\< erőforráscsoport neve\>/providers/Microsoft.Storage/storageAccounts/\<Storage-fiók neve\>, felügyelt identitás:/Subscriptions/\<előfizetés azonosítója\>/resourceGroups//\< erőforráscsoport neve\>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/\<felhasználó által felügyelt identitás neve\>"

### <a name="cause"></a>Ok

Nem adta meg az identitás kezeléséhez szükséges engedélyeket. A felhasználó által hozzárendelt felügyelt identitás nem rendelkezik Blob Storage közreműködő szerepkörrel a Azure Data Lake Storage Gen2 Storage-fiókban.

### <a name="resolution"></a>Felbontás

1. Nyissa meg az Azure Portalt.
1. Nyissa meg a Storage-fiókját.
1. Keresse meg a **Access Control (iam)** alatt.
1. Győződjön meg arról, hogy a Storage blob adatközreműködői szerepkör vagy a Storage blob-adat tulajdonosi szerepköre "hozzárendelt" hozzáféréssel rendelkezik az előfizetés felhasználó által hozzárendelt felügyelt identitásához.

További információ: az [Data Lake Storage Gen2 fiókban található felügyelt identitás engedélyeinek beállítása](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Hibakód: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Hiba

"A biztonsági szabályok a hálózati biztonsági csoport/Subscriptions/\<SubscriptionID\>/resourceGroups/< erőforráscsoport neve\> default/Providers/Microsoft. Network/networkSecurityGroups/\<hálózati biztonsági csoport neve\> subnet/Subscriptions/\<SubscriptionID\>/resourceGroups/\<erőforráscsoport neve\> RG-westeurope-vnet-tomtom-default/Providers/Microsoft. Network/virtualNetworks/\<Virtual A hálózati név\>/Subnets/\<alhálózat neve\> nem engedélyezi a szükséges bejövő és/vagy kimenő kapcsolatokat. További információért látogasson el [a virtuális hálózat megtervezése az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment), vagy forduljon az ügyfélszolgálathoz. "

### <a name="cause"></a>Ok

Ha a hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalak (UDR-EK) vezérlik a HDInsight-fürt felé irányuló bejövő forgalmat, akkor győződjön meg arról, hogy a fürt képes kommunikálni a kritikus Azure állapot-és felügyeleti szolgáltatásokkal.

### <a name="resolution"></a>Felbontás

Ha hálózati biztonsági csoportokat kíván használni a hálózati forgalom szabályozásához, a HDInsight telepítése előtt végezze el a következő műveleteket:

- Azonosítsa a HDInsight használni kívánt Azure-régiót, és hozzon létre egy biztonságos listát a régiója IP-címeiről. További információ [: állapot-és kezelési szolgáltatások: adott régiók](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions).
- Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight-felügyeleti IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
- Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et. Hálózati biztonsági csoportok esetében engedélyezze a bejövő forgalmat az 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Hibakód: a fürt telepítése nem tudta telepíteni az összetevőket egy vagy több gazdagépre

###  <a name="error"></a>Hiba

"A fürt beállítása nem tudta telepíteni az összetevőket egy vagy több gazdagépre. Próbálkozzon újra a kéréssel. "

### <a name="cause"></a>Ok 

Ez a hiba általában akkor jön létre, ha átmeneti probléma merül fel, vagy ha egy Azure-leállás történik.

### <a name="resolution"></a>Felbontás

Győződjön meg arról, hogy minden olyan Azure- [leállást](https://status.azure.com/status) tartalmaz, amely hatással lehet a fürt üzembe helyezésére. Ha nincsenek kimaradások, próbálja megismételni a fürt üzembe helyezését.

## <a name="next-steps"></a>Következő lépések

További információ a fürtök létrehozásával kapcsolatos hibák elhárításáról: [fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails).
