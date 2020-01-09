---
title: Az ID Broker (előzetes verzió) használata a hitelesítő adatok kezeléséhez – Azure HDInsight
description: Tudnivalók a HDInsight-ügynökről a tartományhoz csatlakoztatott Apache Hadoop fürtök hitelesítésének egyszerűsítése érdekében.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483010"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Az ID Broker (előzetes verzió) használata a hitelesítő adatok kezeléséhez

Ez a cikk bemutatja, hogyan állíthatja be és használhatja az ID Broker szolgáltatást az Azure HDInsight-ben. Ezzel a szolgáltatással bejelentkezhet az Apache Ambari az Azure Multi-Factor Authenticationon keresztül, és lekérheti a szükséges Kerberos-jegyeket anélkül, hogy Azure Active Directory Domain Services (Azure AD DS) jelszó-kivonatokat kellene használnia.

## <a name="overview"></a>Áttekintés

Az ID Broker a következő esetekben egyszerűsíti az összetett hitelesítési beállításokat:

* A szervezet támaszkodik az összevonásra, hogy hitelesítse a felhasználókat a felhőalapú erőforrásokhoz való hozzáféréshez. Korábban a HDInsight Enterprise Security Package (ESP) fürtök használatához engedélyeznie kell a jelszó-kivonatok szinkronizálását a helyszíni környezetből a Azure Active Directoryához. Ez a követelmény bizonyos szervezeteknél nehéz vagy nemkívánatos lehet.

* Olyan megoldásokat fejleszt, amelyek különböző hitelesítési mechanizmusokra támaszkodó technológiákat használnak. Például a Apache Hadoop és az Apache Ranger Kerberos-alapú, míg a Azure Data Lake Storage a OAuth-re támaszkodik.

Az ID Broker egységes hitelesítési infrastruktúrát biztosít, és eltávolítja a jelszó-kivonatok Azure-AD DSba való szinkronizálásának követelményét. Az ID Broker egy Windows Server rendszerű virtuális gépen (ID Broker-csomóponton) futó összetevőkből áll, valamint a fürt átjárójának csomópontjaival. 

Az alábbi ábra az összes felhasználó hitelesítési folyamatát mutatja be, beleértve az összevont felhasználókat is, miután az azonosító-átvitelszervező engedélyezve lett:

![Hitelesítési folyamat azonosítója-átvitelszervezővel](./media/identity-broker/identity-broker-architecture.png)

Az ID Broker lehetővé teszi, hogy Multi-Factor Authentication használatával bejelentkezzen az ESP-fürtökbe anélkül, hogy jelszót kellene biztosítania. Ha már bejelentkezett más Azure-szolgáltatásokba, például a Azure Portalba, bejelentkezhet az HDInsight-fürtbe egyszeri bejelentkezéses (SSO) felhasználói élményben.

## <a name="enable-hdinsight-id-broker"></a>HDInsight-azonosító-átvitelszervező engedélyezése

A következő lépésekkel hozhat létre egy ESP-fürtöt, amelyen engedélyezve van az azonosító-átvitelszervező:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Az ESP-fürt alapvető létrehozási lépéseinek követése. További információ: HDInsight- [fürt létrehozása ESP-vel](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Válassza a **HDInsight-azonosító-átvitelszervező engedélyezése**lehetőséget.

Az ID Broker szolgáltatás egy további virtuális gépet ad hozzá a fürthöz. Ez a virtuális gép az ID Broker csomópontja, és a hitelesítést támogató kiszolgálói összetevőket tartalmaz. Az ID Broker csomópont tartományhoz van csatlakoztatva az Azure AD DS tartományhoz.

![Az ID Broker engedélyezésének lehetősége](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Eszköz-integráció

A HDInsight [IntelliJ beépülő modul](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) a OAuth támogatásához frissül. Ezt a beépülő modult használhatja a fürthöz való kapcsolódáshoz és a feladatok elküldéséhez.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>SSH-hozzáférés jelszó-kivonat nélkül az Azure-ban AD DS

Az azonosító-átvitelszervező engedélyezése után az Azure AD DSban tárolt jelszó-kivonatot kell használnia a tartományi fiókokkal való SSH-forgatókönyvekhez. Ha az SSH-t egy tartományhoz csatlakoztatott virtuális gépre vagy a `kinit` parancs futtatására szeretné futtatni, meg kell adnia egy jelszót. 

Az SSH-hitelesítéshez szükséges, hogy a kivonat elérhető legyen az Azure AD DSban. Ha csak rendszergazdai forgatókönyvekhez kíván SSH-t használni, létrehozhat egy csak felhőalapú fiókot, és az SSH-t a fürthöz is használhatja. Más felhasználók továbbra is használhatják az Ambari-vagy HDInsight-eszközöket (például a IntelliJ beépülő modult) anélkül, hogy az Azure AD DSban elérhetővé tennék a jelszó kivonatát.

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürt konfigurálása Enterprise Security Package használatával Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory-felhasználók szinkronizálása HDInsight-fürttel](../hdinsight-sync-aad-users-to-cluster.md)
* [Fürtteljesítmény monitorozása](../hdinsight-key-scenarios-to-monitor.md)
