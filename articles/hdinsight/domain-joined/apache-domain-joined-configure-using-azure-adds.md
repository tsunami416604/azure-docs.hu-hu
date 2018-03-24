---
title: Azure Active Directory tartományszolgáltatásokban - Azure tartományhoz HDInsight-fürtök konfigurálása |} Microsoft Docs
description: Megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtök
services: hdinsight
documentationcenter: ''
author: bprakash
manager: jhubbard
editor: cgronlun
tags: ''
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/20/2018
ms.author: bhanupr
ms.openlocfilehash: ae7ccaf3d167176a1fc6015e84b0eb023da945d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Tartományhoz csatlakozó HDInsight-fürtök Azure Active Directory tartományi szolgáltatások konfigurálása

Tartományhoz csatlakoztatott fürtök biztonsági képességeket biztosítanak a többfelhasználós vállalati a HDInsight. A HDInsight-fürtök tartományhoz az active directory-tartományok, csatlakozik, hogy a tartományi felhasználók a tartományi hitelesítő adataik használatával hitelesítik magukat a fürtök és a big Data típusú adatok feladatok futtatása. 

Ebből a cikkből megismerheti, hogyan konfigurálhatja egy Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtöt.

> [!NOTE]
> Active Directory Azure IaaS virtuális gépeken már nem támogatott.

## <a name="create-azure-adds"></a>Az Azure ADDS létrehozása

Hozzon létre egy Azure Active Directory tartományi szolgáltatások HDInsight-fürtök létrehozása előtt kell. Az Azure ad létrehozásához lásd: [engedélyezése Azure Active Directory tartományi szolgáltatások az Azure portál használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> A bérlői rendszergazdák csak a jogosult tartományi szolgáltatások létrehozására. Ha az alapértelmezett tároló Azure Data Lake tároló-(ADLS-) használata a HDInsight, majd győződjön meg arról, az alapértelmezett Azure AD bérlője ADLS legyen, mint a tartomány a HDInsight-fürthöz. Ehhez állítsa be az Azure Data Lake Store működéséhez a multi-factor authentication kell olyan felhasználóknál, akik hozzáférhetnek a fürt le kell tiltani.

Után van megadva a tartományi szolgáltatások, a szolgáltatás-fiók létrehozásához szüksége a **az Azure AD-tartományvezérlő rendszergazdák** csoport hozhat létre a HDInsight-fürthöz. A fiók globális rendszergazdának kell lennie az Azure ad-val.

Biztonságos LDAP Azure AD tartományi szolgáltatások felügyelt tartomány számára engedélyezni kell. Engedélyezheti a biztonságos LDAP [konfigurálása biztonságos LDAP (LDAPS) egy Azure AD tartományi szolgáltatások által felügyelt tartomány](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Tartományhoz csatlakozó HDInsight-fürt létrehozása

A következő lépés, ha az AAD Tartományi és az előző szakaszban létrehozott szolgáltatásfiók használata a HDInsight-fürthöz.

Célszerűbb a azonos Azure virtuális network(VNet) helyezze el az Azure AD tartományi szolgáltatások és a HDInsight-fürthöz egyaránt. Abban az esetben, ha más Vnetekről vannak, akkor mindkét Vnetek kell partnert. További információkért lásd: [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md).

Amikor létrehoz egy tartományhoz csatlakozó HDInsight-fürtöt, meg kell adnia a következő paraméterekkel:

- **Tartománynév**: A tartománynév társított Azure Active Directory tartományi Szolgáltatásokban. Például: contoso.onmicrosoft.com
- **Tartományi felhasználónév**: A szolgáltatás fiók az Azure AD-tartományvezérlő rendszergazdák csoportban az előző szakaszban létrehozott. Például: hdiadmin@contoso.onmicrosoft.com. A tartományi felhasználó a tartományhoz csatlakoztatott HDInsight fürt rendszergazdája.
- **Tartományi jelszó**: a szolgáltatás fiók jelszavát.
- **Szervezeti egység**: a HDInsight-fürthöz használni kívánt szervezeti egység megkülönböztető nevét. Például: OU HDInsightOU, DC = contoso, DC = = onmicrosohift, DC = com. Ha a szervezeti egység nem létezik, a HDInsight-fürt megkísérli a szervezeti egység létrehozása. 
- **LDAPS URL-cím**: például ldaps://contoso.onmicrosoft.com:636
- **Hozzáférés felhasználói csoport**: A biztonsági csoportokat, amelynek a fürthöz szinkronizálni kívánt felhasználókat. Például HiveUsers. Ha meg szeretné határozni több felhasználói csoport, külön azokat vesszővel (,).
 
> [!NOTE]
> Apache Zeppelin tartománynevet használja: a felügyeleti szolgáltatás fiók hitelesítéséhez, mert a szolgáltatásfióknak a tartomány neve megegyezik az Apache Zeppelin megfelelő működéséhez az egyszerű Felhasználónévi utótagot kell rendelkeznie.
 
Az alábbi képernyőfelvételen látható a konfigurációkat az Azure-portálon:

![Azure HDInsight tartományhoz az Active Directory tartományi szolgáltatások konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>További lépések
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [SSH használata a HDInsight Linux, Unix vagy OS X, Linux-alapú Hadooppal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

