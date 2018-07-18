---
title: A tartományhoz csatlakoztatott HDInsight-fürt konfigurálása az Azure AD DS használatával
description: Ismerje meg, hogyan állíthatja be, és a egy tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services használatával
services: hdinsight
author: omidm1
ms.author: omidm
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: d38148181aa18404e45f6efc029117573570e6bc
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115425"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>A tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services használatával

Tartományhoz csatlakoztatott fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. Tartományhoz csatlakoztatott HDInsight-fürtök, hogy a tartományi felhasználók tartományi hitelesítő adataik használatával a fürtökkel hitelesítéséhez és a big data-feladatokat futtatni egy tartományhoz csatlakozik. 

Ebből a cikkből elsajátíthatja egy tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services (Azure AD DS) használatával.

## <a name="enable-azure-ad-ds"></a>Az Azure AD tartományi szolgáltatások engedélyezése

Az Azure Active Directory tartományi szolgáltatások engedélyezése előfeltétele egy tartományhoz csatlakoztatott HDInsight-fürt létrehozása előtt. További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Csak a bérlői rendszergazdák a jogosultság hozzon létre egy Azure AD DS-példányt. Ha HDInsight használata az Azure Data Lake Storage Gen1 az alapértelmezett tárolóként, győződjön meg róla, hogy az alapértelmezett Azure AD-bérlőhöz tartozó Data Lake Storage Gen1 ugyanaz, mint a tartományon a HDInsight-fürt. Hadoop támaszkodik a Kerberos és az alapszintű hitelesítés, mert a multi-factor authentication kell a fürthöz hozzáférő felhasználóknak le kell tiltani.

Miután az Azure AD DS-példány üzembe, hozzon létre egy service fiókot az Azure Active Directoryban (Azure AD) a megfelelő engedélyekkel. Ha ez a szolgáltatás fiók már létezik, megváltoztathatja a jelszavát, és várjon, amíg a szinkronizált az Azure Active Directory tartományi Szolgáltatásokban. Ez alaphelyzetbe állítása a Kerberos-jelszókivonatok létrehozásának eredményez, és a szinkronizálni kívánt Azure Active Directory tartományi szolgáltatások akár 30 percig is eltarthat. 

A szolgáltatás fiók a következő jogosultságokkal kell rendelkeznie:

- Gépek csatlakoztatása a tartományhoz, és helyezze a gép rendszerbiztonsági tagok a szervezeti Egységet a fürt létrehozásakor megadott belül.
- A fürt létrehozásakor megadott szervezeti Egységben lévő egyszerű szolgáltatások létrehozása.

> [!NOTE]
> Mivel az Apache Zeppelin tartománynevet használja: a felügyeleti szolgáltatás fiók, a szolgáltatás fiók hitelesítéséhez *kell* az UPN-utótagként Apache Zeppelin megfelelő működéséhez a tartomány nevétől.

Szervezeti egységek és kezelésük módjával kapcsolatos további információkért lásd: [létrehozása az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokhoz a szervezeti egység](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

A Secure LDAP szól az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokhoz. További információkért lásd: [egy Azure Active Directory tartományi szolgáltatások biztonságos LDAP konfigurálása felügyelt tartományhoz](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>A tartományhoz csatlakoztatott HDInsight-fürt létrehozása

A következő lépés, hogy a HDInsight-fürt létrehozása az Azure Active Directory tartományi szolgáltatások és a szolgáltatásfiókhoz, amelyet az előző szakaszban létrehozott használatával.

Az Azure AD DS-példány és a HDInsight-fürt is helyezni, az azonos Azure virtuális hálózat könnyebbé válik. Helyezi őket a különböző virtuális hálózatokban lévő választja, így a HDInsight virtuális gépnek rendelkeznie a tartományvezérlőhöz való csatlakozáshoz a virtuális gépek egy üzemel társviszonyba állítása kell ezen virtuális hálózatok. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md).

Amikor létrehoz egy tartományhoz csatlakoztatott HDInsight-fürtöt, meg kell adnia a következő paraméterekkel:

- **Tartománynév**: A tartomány nevét, amely az Azure Active Directory tartományi szolgáltatások van társítva. Az alkalmazás például contoso.onmicrosoft.com.
- **Tartományi felhasználónév**: A fiók, amely az előző szakaszban létrehozott a felügyelt tartományban. Például: hdiadmin@contoso.onmicrosoft.com. A tartományi felhasználó a rendszergazda a HDInsight-fürt lesz.
- **Tartományi jelszó**: a szolgáltatásfiók jelszava.
- **Szervezeti egység**: a HDInsight-fürt a használni kívánt szervezeti egység megkülönböztető nevére. Például, szervezeti egység HDInsightOU, DC = contoso, DC = = onmicrosoft, DC = com. Ha a szervezeti egység nem létezik, a HDInsight-fürt próbálja meg a szervezeti egység létrehozása a jogosultságokat, amely a szolgáltatás fiókja rendelkezik használatával. Például ha a fiók az Azure AD DS-rendszergazdák csoportban, rendelkezik a megfelelő engedélyekkel a szervezeti egység létrehozása. Ellenkező esetben előfordulhat, hogy kell először hozza létre a szervezeti Egységet, és a szolgáltatás fiók teljes, amelynek felügyeleti lehetőséget biztosítanak. További információkért lásd: [létrehozása az Azure Active Directory tartományi szolgáltatások által felügyelt tartományokhoz a szervezeti egység](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).

    > [!IMPORTANT]
    > A tartományvezérlők, vesszővel elválasztva, a szervezeti egység után is tartalmaznak (például a szervezeti egység HDInsightOU, DC = contoso, DC = onmicrosoft, DC = = com).

- **LDAPS URL-cím**: Példa ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Adja meg a teljes URL-CÍMÉT, például "ldaps: / /" és a portszám (: 636-os).

- **Hozzáférési felhasználói csoport**: A biztonsági csoportok, amelynek felhasználói szeretné szinkronizálni a fürthöz. Ha például HiveUsers. Ha meg szeretné határozni a több felhasználói csoportot, válassza el őket pontosvesszővel (;).
 
Az alábbi képernyőfelvételen a konfigurációkat az Azure Portalon:

![Az Azure HDInsight-tartományhoz az Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>További lépések
* Hive-házirendek konfigurálása és Hive-lekérdezések futtatása: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtök](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozhat a tartományhoz csatlakoztatott HDInsight-fürtök, lásd: [az SSH használata a Linux-based Hadoop on HDInsight Linux, Unix vagy OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

