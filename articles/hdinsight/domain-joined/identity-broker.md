---
title: Az ID Broker használata (előzetes verzió) a hitelesítő adatok kezeléséhez – Azure HDInsight
description: Ismerje meg a HDInsight ID Broker t a tartományhoz csatlakozó Apache Hadoop-fürtök hitelesítésének egyszerűsítése érdekében.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 508eac08284f91821223a78cafdfee7b4c9c540b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410883"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Azonosító-broker használata (előzetes verzió) a hitelesítő adatok kezeléséhez

Ez a cikk ismerteti, hogyan állíthatja be és használhatja az ID Broker funkciót az Azure HDInsightban. Ezzel a funkcióval bejelentkezhet az Apache Ambari-ba az Azure többtényezős hitelesítésén keresztül, és beszerezheti a szükséges Kerberos-jegyeket anélkül, hogy jelszókivonatokat kellene megadnia az Azure Active Directory tartományi szolgáltatásokban (Azure AD DS).

## <a name="overview"></a>Áttekintés

Az ID Broker leegyszerűsíti az összetett hitelesítési beállításokat a következő esetekben:

* A szervezet az összevonási adatokra támaszkodik a felhasználók hitelesítéséhez a felhőbeli erőforrások eléréséhez. Korábban a HDInsight Enterprise Security Package (ESP) fürtök használatához engedélyeznie kellett a jelszókivonat-szinkronizálást a helyszíni környezetből az Azure Active Directoryba. Ez a követelmény egyes szervezetek számára nehéz vagy nemkívánatos lehet.

* Olyan megoldásokat épít, amelyek különböző hitelesítési mechanizmusokra támaszkodó technológiákat használnak. Például az Apache Hadoop és az Apache Ranger a Kerberos-ra támaszkodik, míg az Azure Data Lake Storage az OAuth-ra támaszkodik.

ID Broker egy egységes hitelesítési infrastruktúrát biztosít, és megszünteti a jelszókivonatok azure AD DS-be történő szinkronizálásának követelményét. Az ID Broker windows Server virtuális gépen (ID Broker-csomóponton) futó összetevőkből, valamint fürtátjáró-csomópontokból áll. 

Az alábbi ábra az id broker engedélyezése után az összes felhasználó hitelesítési folyamatát mutatja, beleértve az összevont felhasználókat is:

![Hitelesítési folyamat az ID Brokerrel](./media/identity-broker/identity-broker-architecture.png)

Id Broker lehetővé teszi, hogy jelentkezzen be ESP klaszterek segítségével többtényezős hitelesítés, anélkül, hogy bármilyen jelszót. Ha már bejelentkezett más Azure-szolgáltatásokba, például az Azure Portalra, egyetlen bejelentkezéssel (SSO) bejelentkezhet a HDInsight-fürtbe.

## <a name="enable-hdinsight-id-broker"></a>HDInsight-azonosító-közvetítő engedélyezése

Ha olyan ESP-fürtöt szeretne létrehozni, amelyen engedélyezve van az ID Broker, tegye a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kövesse az ESP-fürt alapvető létrehozási lépéseit. További információt a [HDInsight-fürt létrehozása ESP-vel](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)című témakörben talál.
1. Válassza **a HDInsight ID Broker engedélyezése**lehetőséget.

Az ID Broker szolgáltatás egy további virtuális gép hozzáadása a fürthöz. Ez a virtuális gép az ID Broker csomópont, és tartalmazza a hitelesítést támogató kiszolgáló-összetevőket. Az ID Broker-csomópont tartomány csatlakozott az Azure AD DS-tartományhoz.

![Az azonosító-bróker engedélyezésének lehetősége](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Eszközintegráció

A HDInsight [IntelliJ beépülő modul](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) az OAuth támogatásával frissül. Ezzel a beépülő modulval csatlakozhat a fürthöz, és elküldheti a feladatokat.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-hozzáférés jelszókivonat nélkül az Azure AD DS-ben

Az ID Broker engedélyezése után továbbra is szüksége lesz egy jelszókivonatra az Azure AD DS-ben a tartományi fiókkal rendelkező SSH-forgatókönyvekhez. Az SSH-hoz egy tartományhoz csatlakozó virtuális `kinit` géphez, vagy a parancs futtatásához meg kell adnia egy jelszót. 

SSH-hitelesítés hez a kivonatnak elérhetőnek kell lennie az Azure AD DS-ben. Ha csak felügyeleti forgatókönyvekhez szeretné használni az SSH-t, létrehozhat egy csak felhőalapú fiókot, és használhatja azt az SSH-hoz a fürthöz. Más felhasználók továbbra is használhatják az Ambari vagy HDInsight eszközöket (például az IntelliJ beépülő modult) anélkül, hogy az Azure AD DS-ben elérhető jelszókivonat ot rendelkezne.

## <a name="clinets-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Clinets segítségével OAuth csatlakozni HDInsight átjáró ID Broker beállítás

Az azonosító közvetítő i. telepítése kor az egyéni alkalmazások és az átjáróhoz csatlakozó ügyfelek frissíthetők a szükséges OAuth-token beszerzéséhez. A [jogkivonat](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) beszerzéséhez kövesse a dokumentum lépéseit a következő információkkal:

*   OAuth erőforrás uri:https://hib.azurehdinsight.net 
* Alkalmazási d: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   Engedély: (név: Cluster.ReadWrite, id:8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>További lépések

* [HDInsight-fürt konfigurálása vállalati biztonsági csomaggal az Azure Active Directory tartományi szolgáltatások használatával](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
* [Fürtteljesítmény monitorozása](../hdinsight-key-scenarios-to-monitor.md)
