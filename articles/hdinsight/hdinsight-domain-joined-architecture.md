---
title: "A tartományhoz csatlakoztatott Azure HDInsight architektúrája | Microsoft Docs"
description: "Útmutató a tartományhoz csatlakoztatott HDInsight tervezéséhez."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: b5cd321f3cd4c415f5e97ca550b7ab1679324921
ms.openlocfilehash: 94ef8147f1f73b293e818fc508096789373df7fc


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A hagyományos Hadoop egy egyfelhasználós fürt. Ez megfelel a legtöbb olyan cégnek, amelyekben kisebb alkalmazásfejlesztő csapatok dolgoznak a big data számítási feladatok kiépítésén. A Hadoop növekvő népszerűségével, sok vállalat vált olyan modellre, ahol a fürtöket az informatikai csapatok felügyelik, és több alkalmazásfejlesztő csapat is dolgozik ugyanazokon a fürtökön. Így a többfelhasználós fürt az egyik legtöbbet kért funkció a HDInsightban.

Ahelyett, hogy létrehozná a saját többfelhasználós hitelesítési és engedélyezési eszköztárát, a HDInsight a legnépszerűbb identitásszolgáltatóra, az Active Directoryra (AD) támaszkodik. Az Active Directory nagy teljesítményű biztonságicsoport-funkcióival megvalósítható a többfelhasználós hitelesítés a HDInsightban. A HDInsight és az Active Directory integrálásával az Active Directory-felhasználók Active Directorybeli hitelesítő adataikkal kommunikálhatnak a fürtökkel. A HDInsight leképezi az Active Directory-felhasználót egy helyi Hadoop-felhasználóra, így a HDInsighton futó összes szolgáltatás (Ambari, Hive-kiszolgáló, Ranger, Spark Thrift-kiszolgáló stb.) zökkenőmentesen használható a hitelesített felhasználó számára.

## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

A HDInsight és az Active Directory integrálásával a HDInsight-fürtcsomópontok tartományi szinten csatlakoznak az Active Directory-tartományhoz. A HDInsight egyszerű szolgáltatásokat hoz létre a fürtön futó Hadoop-szolgáltatásokhoz, és egy megadott szervezeti egységbe (OU) helyezi őket az Active Directoryban. A HDInsight emellett fordított irányú DNS-leképezéseket is létrehoz az Active Directory-tartományban a tartományhoz csatlakoztatott csomópontok IP-címei számára.

A konfiguráció megvalósításához többféle architektúra is kialakítható. Fontolja meg, hogy melyik architektúra a legmegfelelőbb az Ön számára.

**1. Azure IAAS-on üzemelő, AD-vel integrált HDInsight**

Ez a legegyszerűbb architektúra a HDInsight és az Active Directory integrálásához. Az Active Directory-tartományvezérlő egy (vagy több) virtuális gépen (VM) üzemel az Azure-ban. Ezek a virtuális gépek általában egy virtuális hálózatot alkotnak. Konfigurálni kell egy másik virtuális hálózatot is a HDInsight-fürt számára. Ahhoz, hogy a HDInsight rálásson az Active Directoryra, [virtuális hálózatok közti társviszony-létesítéssel](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md) társítania kell ezeket a virtuális hálózatokat.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Ebben az architektúrában az Azure Data Lake Store és a HDInsight-fürt nem használható együtt.
 

Az Active Directoryra vonatkozó előfeltételek:

* Létre kell hoznia egy [Szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat érdemes elhelyeznie.
* Konfigurálnia kell az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), hogy kommunikáljon az Active Directoryval. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is, amely a HDInsight-fürt létrehozására szolgál. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek;
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek.

**2. Kizárólag felhőalapú Azure AD-vel integrált HDInsight**

A kizárólag felhőalapú Azure Active Directory (Azure AD) esetében konfigurálnia kell egy tartományvezérlőt, hogy a HDInsight integrálható legyen az Azure Active Directoryval. Ez az [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) használatával érhető el. Az Azure AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az Azure AD DS jelenleg kizárólag klasszikus virtuális hálózatokon létezik. Csak a klasszikus Azure Portalon keresztül érhető el. A HDInsight VNet az Azure Portalon létezik, amelyet virtuális hálózatok közti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> Egy klasszikus virtuális hálózat és egy Azure Resource Managerbeli virtuális hálózat társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Active Directoryra vonatkozó előfeltételek:

* Létre kell hoznia egy [Szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat érdemes elhelyeznie. 
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálnia kell az AD DS konfigurálásakor. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDI-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen). 
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is, amely a HDInsight-fürt létrehozására szolgál. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek;
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek.

**3. Helyszíni AD-vel VPN-en keresztül integrált HDInsight**

Ez az architektúra hasonló az 1. számú architektúrához. Az egyetlen különbség, hogy az Active Directory a helyszínen üzemel, és a HDInsight [az Azure és a helyszíni hálózat közti VPN-kapcsolaton keresztül](../expressroute/expressroute-introduction.md) lát rá az Active Directoryra.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Ebben az architektúrában az Azure Data Lake Store és a HDInsight-fürt nem használható együtt.

Az Active Directoryra vonatkozó előfeltételek:

* Létre kell hoznia egy [Szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat érdemes elhelyeznie.
* Konfigurálnia kell az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), hogy kommunikáljon az Active Directoryval. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDI-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is, amely a HDInsight-fürt létrehozására szolgál. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek;
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek.

**4. Azure AD-vel szinkronizált, helyszíni AD-vel integrált HDInsight**

Ez az architektúra hasonló a 2. számú architektúrához. Az egyetlen különbség, hogy a helyszíni Active Directory szinkronizálva van az Azure Active Directoryval. Konfigurálnia kell egy tartományvezérlőt a felhőben, hogy a HDInsight integrálható legyen az Azure Active Directoryval. Ez az [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md) (AD DS) használatával érhető el. Az AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az Azure AD DS jelenleg kizárólag klasszikus virtuális hálózatokon létezik. Csak a klasszikus Azure Portalon keresztül érhető el. A HDInsight VNet az Azure Portalon létezik, amelyet virtuális hálózatok közti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> Egy klasszikus virtuális hálózat és egy Azure Resource Managerbeli virtuális hálózat társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Active Directoryra vonatkozó előfeltételek:

* Létre kell hoznia egy [Szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat érdemes elhelyeznie. 
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálnia kell az AD DS konfigurálásakor. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDI-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen). 
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is, amely a HDInsight-fürt létrehozására szolgál. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek;
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek.

**5. Nem alapértelmezett Azure AD-vel integrált HDInsight (kizárólag teszteléshez és fejlesztéshez ajánlott)**

Ez az architektúra hasonló a 2. számú architektúrához. A legtöbb cég esetében az Active Directory rendszergazdai hozzáférését csak néhány személy kapja meg. Ezért ha egy megvalósíthatósági vizsgálatot szeretne készíteni, vagy csak kipróbálná, hogyan lehet tartományhoz csatlakoztatott fürtöt létrehozni, ahelyett hogy megvárná, amíg a rendszergazda konfigurálja az előfeltételeket az Active Directoryban, hasznosabb lehet, ha egyszerűen létrehoz egy új Azure Active Directoryt az előfizetés alatt. Mivel ezt az Azure AD-t Ön hozta létre, teljes körű jogosultsággal rendelkezik felette az AD DS konfigurálásához.

Az AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az AD DS jelenleg csak a klasszikus virtuális hálózatokon érhető el, így el kell érnie a klasszikus portált, és létre kell hoznia egy klasszikus virtuális hálózatot az AD DS konfigurálásához. A HDInsight VNet az Azure Portalon létezik, amelyet virtuális hálózatok közti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> A klasszikus és az Azure Resource Managerbeli virtuális hálózatok társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Active Directoryra vonatkozó előfeltételek:

* Létre kell hoznia egy [Szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat érdemes elhelyeznie. 
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálnia kell az AD DS konfigurálásakor. Létrehozhat [önaláírt tanúsítványt](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is az LDAPS konfigurálásához. Önaláírt tanúsítvány használata esetén azonban egy kivételt kell igényelnie a <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a> címen.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDI-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen). 
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is, amely a HDInsight-fürt létrehozására szolgál. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek;
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek.

## <a name="next-steps"></a>Következő lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](hdinsight-domain-joined-configure.md).
* A tartományhoz csatlakoztatott HDInsight-fürtök kezeléséhhez lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](hdinsight-domain-joined-manage.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](hdinsight-domain-joined-run-hive.md).
* A Hive-lekérdezések futtatásához az SSH használatával a tartományhoz csatlakoztatott HDInsight-fürtökben lásd: [Az SSH használata a HDInsightra épülő Linuxalapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md).




<!--HONumber=Feb17_HO1-->


