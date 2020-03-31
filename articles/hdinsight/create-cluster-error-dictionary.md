---
title: Azure HDInsight Fürt létrehozása – hibaszótár
description: Az Azure HDInsight-fürtök létrehozásakor előforduló hibák elhárítása
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/19/2019
ms.openlocfilehash: b0dc974185ad616d57327e9cc3743db9ecb20e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302729"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Fürtlétrehozási hibák

Ez a cikk a fürtök létrehozásakor előforduló hibák megoldásait ismerteti.

> [!NOTE]
> A cikkben ismertetett első három hiba érvényesítési hiba. Ezek akkor fordulhatnak elő, ha egy Azure HDInsight-termék a **CsmDocument_2_0** osztályt használja.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: A DeploymentDocument 'CsmDocument_2_0' nem tudta az érvényesítést

### <a name="error"></a>Hiba

"A parancsfájlművelet helye nem\<érhető el\>uri: SCRIPT ACTION URL "

#### <a name="error-message"></a>Hibaüzenet

"A távoli kiszolgáló hibát adott vissza: (404) Nem található."

### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem tudja elérni a fürt létrehozása kérés részeként megadott parancsfájlművelet-URL-címet. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbálja elérni a parancsfájl műveletet.

### <a name="resolution"></a>Megoldás:

- HTTP- vagy HTTPS-URL-cím esetén ellenőrizze az URL-címet egy inkognitóböngésző ablakából való ugrással.
- A WASB URL-címet, győződjön meg arról, hogy a parancsfájl létezik a tárfiókban, hogy adja meg a kérelemben. Győződjön meg arról is, hogy a tárfiók tárolási kulcsa helyes.
- Egy ADLS URL-címet, győződjön meg arról, hogy a parancsfájl létezik a tárfiókban.

---

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: A DeploymentDocument 'CsmDocument_2_0' nem tudta az érvényesítést

### <a name="error"></a>Hiba

"A parancsfájlművelet helye nem \<érhető\>el uri: SCRIPT_ACTION_URL "

#### <a name="error-message"></a>Hibaüzenet

"A megadott \<parancsfájl\> URI SCRIPT_URI az ADLS-ben van, de ez a fürt nem rendelkezik adattótárolási egyszerű"

### <a name="cause"></a>Ok

A HDInsight szolgáltatás nem tudja elérni a fürt létrehozása kérés részeként megadott parancsfájlművelet-URL-címet. A szolgáltatás megkapja az előző hibaüzenetet, amikor megpróbálja elérni a parancsfájl műveletet.

### <a name="resolution"></a>Megoldás:

Adja hozzá a megfelelő Azure Data Lake Storage Gen 1 fiókot a fürthöz. Adja hozzá a data lake storage gen 1 fiókot a fürthöz elérő egyszerű szolgáltatást is.

---

## <a name="error-code-deploymentdocument-csmdocument_2_0-failed-the-validation"></a>Hibakód: A DeploymentDocument 'CsmDocument_2_0' nem tudta az érvényesítést

### <a name="error"></a>Hiba

"Virtuális gép\<mérete\>' CUSTOMER_SPECIFIED_VM_SIZE ' a kérelemben megadott\<érvénytelen, vagy nem támogatott szerepkör " ROLE\>". Érvényes értékek: \<\>VALID_VM_SIZE_FOR_ROLE "

### <a name="cause"></a>Ok

A megadott virtuálisgép-méret nem engedélyezett a szerepkörhöz. Ez a hiba azért fordulhat elő, mert a virtuális gép mérete érték nem működik a várt módon, vagy nem alkalmas a számítógépes szerepkör.

### <a name="resolution"></a>Megoldás:

A hibaüzenet a virtuális gép méretének érvényes értékeit sorolja fel. Válasszon az alábbi értékek közül, majd próbálkozzon újra a Fürt létrehozása kéréssel.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Hibakód: InvalidVirtualNetworkId  

### <a name="error"></a>Hiba

"A VirtualNetworkId érvénytelen. VirtualNetworkId\<'\>USER_VIRTUALNETWORKID '*"

### <a name="cause"></a>Ok

A fürt létrehozása során megadott **VirtualNetworkId** érték nem a megfelelő formátumú.

### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy a **VirtualNetworkId** és az alhálózati értékek a megfelelő formátumban vannak. A **VirtualNetworkId** érték beszerezése:

1. Nyissa meg az Azure Portalt.
1. Válassza ki a virtuális hálózatot.
1. Válassza a **Tulajdonságok** menüelemet. A **ResourceID** tulajdonság értéke a **VirtualNetworkId** érték.

Íme egy példa a virtuális hálózati azonosítóra:

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>Hibakód: CustomizationFailedErrorCode

### <a name="error"></a>Hiba

"A fürt telepítése nem sikerült az egyéni parancsfájlművelet hibája miatt. Sikertelen műveletek: \<\>SCRIPT_NAME , Kérjük, látogasson el az Ambari felhasználói felületre a hiba további hibakereséséhez."

### <a name="cause"></a>Ok

A fürt létrehozása során megadott egyéni parancsfájl a fürt sikeres telepítése után kerül végrehajtásra. Ez a hibakód azt jelzi, hogy hiba történt \<\>a SCRIPT_NAME nevű egyéni parancsfájl végrehajtása során.

### <a name="resolution"></a>Megoldás:

Mivel a parancsfájl az egyéni parancsfájl, javasoljuk, hogy hárítsa el a problémát, és szükség esetén futtassa újra a parancsfájlt. A parancsfájlhiba elhárításához vizsgálja meg a /var/lib/ambari-agent/* mappában található naplókat. Vagy nyissa meg az Ambari felhasználói **felületen** a Műveletek lapot, majd válassza ki a **run_customscriptaction** műveletet a hiba részleteinek megtekintéséhez.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Hibakód: InvalidDocumentErrorCode

### <a name="error"></a>Hiba

"A \<\> META_STORE_TYPE Metastore séma \<verziója\> \<METASTORE_MAJOR_VERSION\> az adatbázisban\>DATABASE_NAME nem kompatibilis a fürt CLUSTER_VERSION " \<

### <a name="cause"></a>Ok

Az egyéni metatároló nem kompatibilis a kiválasztott HDInsight-fürtverzióval. Jelenleg a HDInsight 4.0-s fürtjei csak a Metastore 3.0-s és újabb verzióját támogatják, míg a HDInsight 3.6-os fürtök nem támogatják a Metastore 3.0-s és újabb verzióját.

### <a name="resolution"></a>Megoldás:

Csak olyan Metastore-verziókat használjon, amelyeket a HDInsight-fürtverziója támogat. Ha nem ad meg egyéni metatárolót, a HDInsight belsőleg létrehoz egy metatárolót, majd fürttörléskor törli azt.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Hibakód: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Hiba

"Nem lehet csatlakozni a fürtfelügyeleti végponthoz a skálázási művelet végrehajtásához. Ellenőrizze, hogy a hálózati biztonsági szabályok nem blokkolják-e a fürt höz való külső hozzáférést, és hogy a fürtkezelő (Ambari) felhasználói felülete sikeresen elérhető-e."

### <a name="cause"></a>Ok

A hálózati biztonsági csoport (NSG) tűzfalszabálya blokkolja a fürtkommunikációt a kritikus Azure-állapot- és felügyeleti szolgáltatásokkal.

### <a name="resolution"></a>Megoldás:

Ha a hálózati biztonsági csoportokat kívánja használni a hálózati forgalom szabályozására, a HDInsight telepítése előtt tegye a következő műveleteket:

- Azonosítsa a HDInsighthoz használni kívánt Azure-régiót.
- Azonosítsa a HDInsight által megkövetelt IP-címeket. További információ: [HDInsight kezelése – IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses).
  - Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyekbe a HDInsightot telepíteni kívánja.
  - Hálózati biztonsági csoportok esetén engedélyezze a bejövő forgalmat a 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight felügyeleti szolgáltatások a virtuális hálózaton kívülről is elérhetik a fürtöt.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Hibakód: StoragePermissionsBlockedForMsi  

### <a name="error"></a>Hiba

"A felügyelt identitás nem rendelkezik engedélyekkel a tárfiókhoz. Ellenőrizze, hogy a "Storage Blob Data Owner" szerepkör hozzá van-e rendelve a tárfiók felügyelt identitásához. Tároló: \</subscriptions/ Subscription\> ID\< /resourceGroups/ Resource Group\> Name /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Ok

Nem adta meg az identitás kezeléséhez szükséges engedélyeket. A felhasználó által hozzárendelt felügyelt identitás nem rendelkezik a Blob Storage Közreműködő szerepkör az Azure Data Lake Storage Gen2 tárfiók.

### <a name="resolution"></a>Megoldás:

1. Nyissa meg az Azure Portalt.
1. Nyissa meg a tárfiókot.
1. Keresse meg a **Hozzáférés-vezérlés (IAM) területen.**
1. Győződjön meg arról, hogy a felhasználó rendelkezik a Storage Blob Data Contributor szerepkör vagy a storage blob adattulajdonos szerepkör hozzárendelt.

További információ: [Engedélyek beállítása a Felügyelt identitáshoz a Data Lake Storage Gen2 fiókban.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Hibakód: InvalidNetworkSecurityGroupSecurityRules  

### <a name="error"></a>Hiba

"A hálózati biztonsági csoport /előfizetések/\<\>SubscriptionID /resourceGroups/<\> Resource Group name default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> in alnet /subscriptions/\<SubscriptionID\>\</resourceGroups/ Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network A\>hálózatnév /alhálózatok/\<\> Alhálózat neve nem teszi lehetővé a szükséges bejövő és/vagy kimenő kapcsolatot. További információért látogasson el [az Azure HDInsight virtuális hálózatának megtervezése](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)vagy az ügyfélszolgálat megtervezése című oldalra."

### <a name="cause"></a>Ok

Ha a hálózati biztonsági csoportok vagy a felhasználó által definiált útvonalak (UDRs) szabályozzák a HDInsight-fürt befelé irányuló forgalmat, győződjön meg arról, hogy a fürt képes kommunikálni a kritikus Azure-állapot- és felügyeleti szolgáltatásokkal.

### <a name="resolution"></a>Megoldás:

Ha a hálózati biztonsági csoportokat kívánja használni a hálózati forgalom szabályozására, a HDInsight telepítése előtt tegye a következő műveleteket:

- Azonosítsa a HDInsighthoz használni kívánt Azure-régiót, és hozzon létre egy biztonságos listát a régió IP-címeiről. További információ: [Egészség és kezelési szolgáltatások: Adott régiók.](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses#health-and-management-services-specific-regions)
- Azonosítsa a HDInsight által igényelt IP-címeket. További információ: [HDInsight-felügyeleti IP-címek.](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)
- Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyekbe a HDInsightot telepíteni kívánja. Hálózati biztonsági csoportok esetén engedélyezze a bejövő forgalmat a 443-as porton az IP-címekről. Ez a konfiguráció biztosítja, hogy a HDInsight felügyeleti szolgáltatások a virtuális hálózaton kívülről is elérhetik a fürtöt.
  
---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Hibakód: A fürt telepítője nem tudta telepíteni az összetevőket egy vagy több állomásra

###  <a name="error"></a>Hiba

"A fürt telepítője nem tudta telepíteni az összetevőket egy vagy több állomásra. Próbálkozzon újra a kéréssel."

### <a name="cause"></a>Ok 

Ez a hiba általában átmeneti probléma vagy Azure-kimaradás esetén jön létre.

### <a name="resolution"></a>Megoldás:

Ellenőrizze az [Azure-állapot](https://status.azure.com) lapon az Azure-kimaradások, amelyek hatással lehetnek a fürt központi telepítését. Ha nincs kimaradás, próbálja meg újra a fürt központi telepítését.

## <a name="next-steps"></a>További lépések

A fürtlétrehozássorán előforduló hibák elhárításáról az [Azure HDInsight szolgáltatással kapcsolatos fürtlétrehozási hibák elhárítása című témakörben talál](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-troubleshoot-cluster-creation-fails)további információt.
