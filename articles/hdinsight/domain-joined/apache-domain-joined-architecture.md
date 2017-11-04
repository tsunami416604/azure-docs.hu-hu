---
title: "Tartományhoz csatlakozó Azure HDInsight-architektúra |} Microsoft Docs"
description: "Útmutató a tartományhoz csatlakoztatott HDInsight tervezéséhez."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A hagyományos Hadoop egy egyfelhasználós fürt. Ez megfelel a legtöbb olyan cégnek, amelyekben kisebb alkalmazásfejlesztő csapatok dolgoznak a nagy adatszámítási feladatok kiépítésén. A Hadoop növekvő népszerűségével, sok vállalat vált olyan modellre, ahol a fürtöket az informatikai csapatok felügyelik, és több alkalmazásfejlesztő csapat is dolgozik ugyanazokon a fürtökön. Így a többfelhasználós fürtöket használó funkció az egyik legtöbbet kért funkció a HDInsightban.

Helyett a saját többfelhasználós hitelesítési és engedélyezési készítéséhez, a HDInsight legnépszerűbb identitásszolgáltató – Active Directory (AD) alapul. A hatékony biztonsági funkciókat AD hdinsight többfelhasználós engedélyezési kezelésére használható. Az ad-val a HDInsight integrálásával kommunikálhat a fürtök AD hitelesítő adataival. HDInsight van leképezve egy AD-felhasználó a helyi Hadoop-felhasználó, így a szolgáltatás HDInsight (Ambari, kiszolgáló, idejére Spark thrift Hive server és a mások) munkahelyi zökkenőmentesen a hitelesített felhasználó számára.

## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

Ha integrálja az Active Directory HDInsight, HDInsight fürtcsomópontok esetén, a tartományhoz egy AD-tartományhoz. HDInsight hoz létre a fürtben futó Hadoop szolgáltatások szolgáltatásnevekről és ki vannak téve a belül egy adott szervezeti egység (OU) a tartományban. HDInsight címfeloldási DNS-hozzárendelések is létrehoz a tartomány az IP-címekhez tartozó csomópontot csatlakoznak a tartományhoz.

Az Active Directory két központi telepítési lehetőség áll rendelkezésre:
* **[Az Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md):** ezt a szolgáltatást biztosít a felügyelt Active Directory-tartományhoz, amely teljes mértékben kompatibilis a Windows Server Active Directory. A Microsoft gondoskodik kezelését, javítását és az AD-tartomány ellenőrzése. A fürtök telepítése anélkül, hogy a tartományvezérlők karbantartásához. Felhasználók, csoportok és jelszavak szinkronizálódnak az Azure Active Directoryból, így a felhasználók jelentkezzen be a fürtre, a vállalati hitelesítő adatok használatával.

* **Az Azure IaaS virtuális gépeken futó Windows Server Active Directory-tartomány:** ezt a beállítást, az telepíti és kezeli a saját Windows Server Active Directory-tartomány Azure IaaS virtuális gépeken. 

Ezt a beállítást többféle architektúra használatával érheti el. Az alábbi architektúrák közül választhat.


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>A HDInsight integrálva van az Azure AD tartományi szolgáltatások által felügyelt AD-tartomány
Telepíthet egy [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md) (az Azure AD DS) tartomány felügyelt. Az Azure Active Directory tartományi szolgáltatások biztosít, amely felügyelt, frissítése és a Microsoft által figyelt az Azure-ban kezelt Active Directory-tartománynak. A magas rendelkezésre álláshoz két tartományvezérlő hoz létre, és magában foglalja a DNS-szolgáltatásokra. A HDInsight-fürt majd integrálható a felügyelt tartományra. Ezt a központi telepítési lehetőséget nem kell foglalkoznia kezeléséhez, a javítás, a frissítés és a tartományvezérlő figyelésére.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Integrálása az Azure AD tartományi szolgáltatásokra vonatkozó Előfeltételek:

* [Az Azure AD tartományi szolgáltatások kiépítése felügyelt tartomány](../../active-directory-domain-services/active-directory-ds-getting-started.md).
* Hozzon létre egy [szervezeti egység](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyen belül a HDInsight-fürt virtuális gépek elhelyezésekor és a szolgáltatás rendszerbiztonsági tagok a fürt által használt.
* A telepítő [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), amikor konfigurálja az Azure Active Directory tartományi Szolgáltatásokban. LDAPS beállításához használt tanúsítványt nyilvános hitelesítésszolgáltatótól (ez nem egy önaláírt tanúsítvány) kell kiállítania.
* Hozzon létre névkeresési DNS-zónák az IP-címtartomány a HDInsight-alhálózat (például az előző ábrán 10.2.0.0/24) a felügyelt tartományra.
* Konfigurálása [az NTLM és Kerberos hitelesítéshez szükséges jelszó kivonatokat szinkronizálásának](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) az Azure AD az Azure Active Directory tartományi szolgáltatások felügyelt tartományra.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Azure AD-tartományhoz történő csatlakoztatásához szükséges engedélyek


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>HDInsight a Windows Server AD integrált Azure infrastruktúra-szolgáltatáson futó

Egy (vagy több) virtuális gépek (VM) az Azure-ban a Windows Server Active Directory tartományi szolgáltatások szerepkört telepítheti, és azok tartományvezérlők előléptetése. A tartományvezérlő virtuális gépeket a resource manager üzembe helyezési modellben a HDInsight-fürt azonos virtuális hálózaton történő használatával is telepíthető. Ha a tartományvezérlők másik virtuális hálózatba telepítik, szeretné-e ezek a virtuális hálózatok használatával partnert [VNet – VNet társviszony-létesítés](../../virtual-network/virtual-network-create-peering.md). 

[További információ - üzembe helyezése a Windows Server Active Directory Azure virtuális gépeken](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Ebben az architektúrában az Azure Data Lake Store és a HDInsight-fürt nem használható együtt.


A Windows Server Active Directory Azure virtuális gépeken integrálásához Előfeltételek:

* Létre kell hoznia egy [szervezeti egységet](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* [Lightweight Directory Access protokollok](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAPs) be kell állítani a az ad-vel való kommunikációhoz. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek


## <a name="next-steps"></a>Következő lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A tartományhoz csatlakoztatott HDInsight-fürtök kezeléséhez lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](apache-domain-joined-manage.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Hive-lekérdezések futtatása HDInsight-fürtök tartományhoz az ssh protokoll használatával, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).
