---
title: "Azure Active Directory tartományszolgáltatásokban - Azure tartományhoz HDInsight-fürtök konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtök"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: saurinsh
ms.openlocfilehash: c5ab129e87c8c7903972bd94286ba0368037a738
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Tartományhoz csatlakozó HDInsight-fürtök Azure Active Directory tartományi szolgáltatások konfigurálása

Tartományhoz csatlakoztatott fürtök biztonsági képességeket biztosítanak a többfelhasználós vállalati a HDInsight. A HDInsight-fürtök tartományhoz az active directory-tartományok, csatlakozik, hogy a tartományi felhasználók a tartományi hitelesítő adataik használatával hitelesítik magukat a fürtök és a big Data típusú adatok feladatok futtatása. 

Három módon, hogy egy tartományhoz csatlakozó HDInsight-fürt csatlakozni tud-e a tartományvezérlő beállítása:

- Az Azure Active Directory tartományi szolgáltatások (az Azure AD DS)
- Helyszíni Active Directory
- Active Directory-tartományvezérlő Azure IaaS virtuális gépeken

Ebből a cikkből megismerheti, hogyan konfigurálhatja egy Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtöt.

## <a name="create-azure-adds"></a>Az Azure ADDS létrehozása

Hozzon létre egy Azure Active Directory tartományi szolgáltatások HDInsight-fürtök létrehozása előtt kell. Az Azure ad létrehozásához lásd: [engedélyezése Azure Active Directory tartományi szolgáltatások az Azure portál használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> A bérlői rendszergazdák csak a jogosult tartományi szolgáltatások létrehozására. 

Után van megadva a tartományi szolgáltatások, a szolgáltatás-fiók létrehozásához szüksége a **az Azure AD-tartományvezérlő rendszergazdák** csoport hozhat létre a HDInsight-fürthöz. A fiók globális rendszergazdának kell lennie az Azure ad-val.

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
 
Az alábbi képernyőfelvételen látható a konfigurációkat az Azure-portálon:

![Azure HDInsight tartományhoz az Active Directory tartományi szolgáltatások konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>További lépések
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [SSH használata a HDInsight Linux, Unix vagy OS X, Linux-alapú Hadooppal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

