---
title: Az AAD-DS tartományhoz HDInsight-fürtök konfigurálása
description: Megtudhatja, hogyan telepítheti és konfigurálhatja az Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtök
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 7bde1c834038bdc2a010987b4e32fa49222101ee
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715275"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Tartományhoz csatlakozó HDInsight-fürtök Azure Active Directory tartományi szolgáltatások konfigurálása

Tartományhoz csatlakozó fürtök a többfelhasználós hozzáférést a HDInsight-fürtökön. A HDInsight-fürtök tartományhoz van csatlakoztatva vannak egy tartományhoz, hogy a tartományi felhasználók a tartományi hitelesítő adataik használatával hitelesítik magukat a fürtök és a big Data típusú adatok feladatok futtatásához. 

Ebből a cikkből megismerheti, hogyan konfigurálhatja egy Azure Active Directory tartományi szolgáltatások tartományhoz HDInsight-fürtöt.

> [!NOTE]
> Azure Active Directory tartományi szolgáltatások egy tartományhoz csatlakozó HDInsight-fürt létrehozása szükséges. Létrehozása egy tartományhoz csatlakozó HDInsight fürt Azure IaaS virtuális gépeken futó Active Directory használatával már nem támogatott.

## <a name="create-azure-adds"></a>Az Azure ADDS létrehozása

Azure AD tartományi szolgáltatások (AAD-DS) engedélyezése feltétele egy tartományhoz csatlakozó HDInsight-fürt létrehozása előtt. Az AAD-DS példány engedélyezéséről [engedélyezése AAD Tartományi az Azure portál használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> A bérlői rendszergazdák csak a jogosult hozzon létre egy AAD-DS példányt. Ha az alapértelmezett tároló Azure Data Lake tároló-(ADLS-) használata a HDInsight, majd győződjön meg arról, az alapértelmezett Azure AD bérlője ADLS legyen, mint a tartomány a HDInsight-fürthöz. Sincde Hadoop támaszkodik a Kerberos és az alapszintű hitelesítés, a többtényezős hitelesítést kell olyan felhasználóknál, akik hozzáférhetnek a fürt le kell tiltani.

Az AAD-DS példány van megadva, miután létrehozásához szükséges szolgáltatásfiók az aad-ben (amely lesznek szinkronizálva az AAD-DS-ben) a megfelelő engedélyekkel. Ha ez a szolgáltatás fiók már létezik, szeretné-e a jelszó alaphelyzetbe állítása, és várjon, amíg az AAD-DS-ben a szinkronizált (és szinkronizálása az AAD-DS-ben legfeljebb 30 percet is igénybe vehet az alaphelyzetbe a kerberos Jelszókivonat létrehozását eredményezi). Ennek a szolgáltatásfióknak a következő jogosultságokkal kell rendelkezniük:

- Számítógépek csatlakoztatása a tartományhoz, és helyezze a gép rendszerbiztonsági tagok belül a szervezeti egységet a fürt létrehozása során.
- Hozzon létre szolgáltatásnevekről belül a szervezeti egységet a fürt létrehozása során.

> [!NOTE]
> Apache Zeppelin tartománynevet használja: a felügyeleti szolgáltatás fiók hitelesítéséhez, mert a szolgáltatásfióknak a tartomány neve megegyezik az Apache Zeppelin megfelelő működéséhez az egyszerű Felhasználónévi utótagot kell rendelkeznie.

Szervezeti egységek és kezelésük módjával kapcsolatos további információkért lásd: [hozható létre egy szervezeti egység az AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md). 

Biztonságos LDAP AAD-DS felügyelt tartomány megadása kötelező. Engedélyezheti a biztonságos LDAP [biztonságos LDAP (LDAPS) konfigurálása az AAD-DS felügyelt tartomány](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Tartományhoz csatlakozó HDInsight-fürt létrehozása

A következő lépés, ha az AAD-DS és az előző szakaszban létrehozott szolgáltatásfiók használata a HDInsight-fürthöz.

Célszerűbb a azonos Azure virtuális network(VNet) helyezze el az AAD-DS, mind a HDInsight-fürthöz. Abban az esetben, ha úgy döntött, hogy helyezze őket Vnetekhez különböző, kell partnert ezeket a Vneteket, így HDI virtuális gépek kell egy sor a láthatáron a tartományvezérlőhöz való csatlakozás a virtuális gépek. További információkért lásd: [virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md).

Amikor létrehoz egy tartományhoz csatlakozó HDInsight-fürtöt, meg kell adnia a következő paraméterekkel:

- **Tartománynév**: A tartománynév társított AAD-DS-ben. Például: contoso.onmicrosoft.com
- **Tartományi felhasználónév**: A szolgáltatás fiók a felügyelt tartomány, az előző szakaszban létrehozott. Például: hdiadmin@contoso.onmicrosoft.com. A tartományi felhasználó a rendszergazda a HDInsight-fürt lesz.
- **Tartományi jelszó**: a szolgáltatás fiók jelszavát.
- **Szervezeti egység**: a HDInsight-fürthöz használni kívánt szervezeti egység megkülönböztető nevét. Például: OU HDInsightOU, DC = contoso, DC = = onmicrosohift, DC = com. A szervezeti egység nem létezik, a HDInsight-fürt megpróbálja létrehozni a szervezeti Egységet a jogosultságokkal a szolgáltatás fiókja rendelkezik. (Például ha a fiók az AAD-Tartományi rendszergazdák csoport, hozzon létre egy szervezeti Egységet megfelelő jogosultságokkal rendelkezik, ellenkező esetben előfordulhat, hogy kell először hozza létre a szervezeti egység és a szolgáltatási fiókot teljes hozzáférésük szervezeti egységre először adjon – lásd: [hozzon létre egy szervezeti Egységet a egy AAD-DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)).

    > [!IMPORTANT]
    > Fontos, hogy tartalmazzák az összes, a tartományvezérlők sepearated vesszővel elválasztott után a szervezeti Egységhez (pl. OU HDInsightOU, DC = contoso, DC = = onmicrosohift, DC = com).

- **LDAPS URL-cím**: például ldaps://contoso.onmicrosoft.com:636

    > [!IMPORTANT]
    > Adja meg a teljes URL-címet is a ldaps: / / és a portszám (: 636)

- **Hozzáférés felhasználói csoport**: A biztonsági csoportokat, amelynek a fürthöz szinkronizálni kívánt felhasználókat. Például HiveUsers. Ha meg szeretné határozni több felhasználói csoport, külön azokat vesszővel (,).
 
Az alábbi képernyőfelvételen látható a konfigurációkat az Azure-portálon:

![Azure HDInsight tartományhoz az Active Directory tartományi szolgáltatások konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>További lépések
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozni a tartományhoz a HDInsight-fürtök, lásd: [SSH használata a HDInsight Linux, Unix vagy OS X, Linux-alapú Hadooppal](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

