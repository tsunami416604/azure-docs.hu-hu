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
ms.sourcegitcommit: 4ba44128ec19d3937643ac934ca3e787cb9819a3
ms.openlocfilehash: 690ba97d2b0634548a0ec424d441ad34d70667b9
ms.lasthandoff: 02/27/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A hagyományos Hadoop egy egyfelhasználós fürt. Ez megfelel a legtöbb olyan cégnek, amelyekben kisebb alkalmazásfejlesztő csapatok dolgoznak a nagy adatszámítási feladatok kiépítésén. A Hadoop növekvő népszerűségével, sok vállalat vált olyan modellre, ahol a fürtöket az informatikai csapatok felügyelik, és több alkalmazásfejlesztő csapat is dolgozik ugyanazokon a fürtökön. Így a többfelhasználós fürtöket használó funkció az egyik legtöbbet kért funkció a HDInsightban.

Ahelyett, hogy létrehozná a saját többfelhasználós hitelesítési és engedélyezési eszköztárát, a HDInsight a legnépszerűbb identitásszolgáltatóra, az Azure Active Directoryra (Azure AD) támaszkodik. Az Azure AD nagy teljesítményű biztonsági funkcióival megvalósítható a többfelhasználós hitelesítés a HDInsightban. A HDInsight és az Azure AD integrálásával kommunikálhat az Azure AD hitelesítő adatok használatával a fürtökkel. A HDInsight leképezi az Azure AD-felhasználót egy helyi Hadoop-felhasználóra, így a HDInsighton futó összes szolgáltatás (Ambari, Hive-kiszolgáló, Ranger, Spark Thrift-kiszolgáló és mások) zökkenőmentesen használható a hitelesített felhasználó számára.

## <a name="integrate-hdinsight-with-azure-ad"></a>A HDInsight és az Azure AD integrálása

A HDInsight és az Azure AD integrálásával a HDInsight-fürtcsomópontok tartományi szinten csatlakoznak az Azure AD-tartományhoz. A HDInsight egyszerű szolgáltatásokat hoz létre a fürtön futó Hadoop-szolgáltatásokhoz, és egy megadott szervezeti egységbe (OU) helyezi őket az Azure AD-ben. A HDInsight emellett fordított irányú DNS-leképezéseket is létrehoz az Azure AD-tartományban a tartományhoz csatlakoztatott csomópontok IP-címei számára.

Ezt a beállítást többféle architektúra használatával érheti el. Az alábbi architektúrák közül választhat.

**Azure IaaS-on üzemelő, Azure AD-vel integrált HDInsight**

Ez a legegyszerűbb architektúra a HDInsight és az Azure AD integrálásához. Az Azure AD-tartományvezérlő egy (vagy több) virtuális gépen (VM) üzemel az Azure-ban. Ezek a virtuális gépek általában egy virtuális hálózatot alkotnak. Konfigurálni kell egy másik virtuális hálózatot is a HDInsight-fürt számára. Ahhoz, hogy a HDInsight rálásson az Azure AD-re, [virtuális hálózatok közötti társviszony-létesítéssel](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md) társítania kell ezeket a virtuális hálózatokat.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> Ebben az architektúrában az Azure Data Lake Store és a HDInsight-fürt nem használható együtt.


Az Azure AD előfeltételei:

* Létre kell hoznia egy [szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* A [Lightweight Directory Access Protocol](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) protokollt be kell állítani az Azure AD-vel való kommunikációra. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek

**Kizárólag felhőalapú Azure AD-vel integrált HDInsight**

A kizárólag felhőalapú Azure AD esetében konfiguráljon egy tartományvezérlőt, hogy a HDInsight integrálható legyen az Azure AD-vel. Ez az [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) használatával érhető el. Az Azure AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az Azure AD DS jelenleg kizárólag klasszikus virtuális hálózatokon létezik. Csak a klasszikus Azure-portálon keresztül érhető el. A HDInsight virtuális hálózat az Azure Portalon létezik, amelyet a virtuális hálózatok közötti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> Egy klasszikus virtuális hálózat és egy Azure Resource Managerbeli virtuális hálózat társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Azure AD előfeltételei:

* Létre kell hoznia egy [szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálni kell az Azure AD DS konfigurálásakor. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az Azure AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Azure AD-tartományhoz történő csatlakoztatásához szükséges engedélyek

**Helyszíni Active Directory-vel VPN-en keresztül integrált HDInsight**

Ez architektúra hasonló az Azure IaaS-on üzemelő, Azure AD-vel integrált HDInsight architektúrához. Az egyetlen különbség, hogy az Azure AD a helyszínen üzemel, és a HDInsight [az Azure és a helyszíni hálózat közti VPN-kapcsolaton keresztül](../expressroute/expressroute-introduction.md) lát rá az Azure AD-re.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> Ebben az architektúrában az Azure Data Lake Store és a HDInsight-fürt nem használható együtt.

Az Azure AD előfeltételei:

* Létre kell hoznia egy [szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* Konfigurálnia kell az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md), hogy kommunikáljon Azure AD-vel. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Azure AD-tartományhoz történő csatlakoztatásához szükséges engedélyek

**Azure AD-vel szinkronizált, helyszíni Active Directory-val integrált HDInsight**

Ez az architektúra hasonló a kizárólag felhőalapú Azure AD-vel integrált HDInsight architektúrához. Az egyetlen különbség, hogy a helyszíni Active Directory szinkronizálva van az Azure AD-vel. Konfiguráljon egy tartományvezérlőt a felhőben, hogy a HDInsight integrálható legyen az Azure AD-vel. Ez az [Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md) használatával érhető el. Az Azure AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az Azure AD DS jelenleg kizárólag klasszikus virtuális hálózatokon létezik. Csak a klasszikus Azure-portálon keresztül érhető el. A HDInsight virtuális hálózat az Azure Portalon létezik, amelyet a virtuális hálózatok közötti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> Egy klasszikus virtuális hálózat és egy Azure Resource Managerbeli virtuális hálózat társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Azure AD előfeltételei:

* Létre kell hoznia egy [szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálni kell az Azure AD DS konfigurálásakor. Az LDAPS konfigurálásához használt tanúsítványnak valódi (nem önaláírt) tanúsítványnak kell lennie.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az Azure AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Active Directory-tartományhoz történő csatlakoztatásához szükséges engedélyek

**Nem alapértelmezett Azure AD-vel integrált HDInsight (kizárólag teszteléshez és fejlesztéshez ajánlott)**

Ez az architektúra hasonló a kizárólag felhőalapú Azure AD-vel integrált HDInsight architektúrához. A legtöbb cég esetében az Azure AD rendszergazdai hozzáférését néhány személy kapja meg. Ezért ha egy megvalósíthatósági vizsgálatot szeretne készíteni, vagy csak kipróbálná, hogyan lehet tartományhoz csatlakoztatott fürtöt létrehozni, ahelyett hogy megvárná, amíg a rendszergazda konfigurálja az előfeltételeket az Azure AD-ben, hasznosabb lehet, ha egyszerűen létrehoz egy Azure AD-példányt az előfizetés alatt. Mivel ezt az Azure AD-példányt Ön hozta létre, teljes körű jogosultsággal rendelkezik felette az Azure AD DS konfigurálásához.

Az Azure AD DS tartományvezérlő gépeket hoz létre a felhőben, és megadja az IP-címeiket. Két tartományvezérlőt hoz létre a magas rendelkezésre állás érdekében.

Az Azure AD csak a klasszikus virtuális hálózatokban létezik, ezért hozzáférésre van szüksége a klasszikus Azure portálhoz, és létre kell hoznia egy klasszikus virtuális hálózatot az Azure AD DS konfigurálásához. A HDInsight virtuális hálózat az Azure Portalon létezik, amelyet a virtuális hálózatok közötti társviszony-létesítéssel társítani kell a klasszikus virtuális hálózattal.

> [!NOTE]
> A klasszikus és az Azure Resource Managerbeli virtuális hálózat társításához a két virtuális hálózatnak ugyanabban a régióban kell lennie, és ugyanazon Azure-előfizetés alá kell tartoznia.

![Tartományhoz csatlakoztatott HDInsight-fürtök topológiája](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Az Azure AD előfeltételei:

* Létre kell hoznia egy [szervezeti egységet](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), amelyben a HDInsight-fürt virtuális gépeit és a fürt által használt egyszerű szolgáltatásokat helyezi el.
* Az [LDAPS-t](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is konfigurálni kell az Azure AD DS konfigurálásakor. Létrehozhat [önaláírt tanúsítványt](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) is az LDAPS konfigurálásához. Önaláírt tanúsítvány használata esetén azonban egy kivételt kell igényelnie a <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a> címen.
* Fordított irányú DNS-zónákat kell létrehoznia a tartományon a HDInsight-alhálózat IP-címtartománya számára (például 10.2.0.0/24 az előző képen).
* A [jelszókivonatokat](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) szinkronizálnia kell az Azure AD-ről az Azure AD DS-re.
* Szükséges egy szolgáltatásfiók vagy egy felhasználói fiók is. Ezt a fiókot a HDInsight-fürt létrehozására használhatja. A fióknak az alábbi engedélyekkel kell rendelkeznie:

    - A szervezeti egységben lévő egyszerű szolgáltatásobjektumok és gépobjektumok létrehozásához szükséges engedélyek
    - A fordított irányú DNS-proxyszabályok létrehozásához szükséges engedélyek;
    - A gépeknek az Azure AD-tartományhoz történő csatlakoztatásához szükséges engedélyek

## <a name="next-steps"></a>Következő lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](hdinsight-domain-joined-configure.md).
* A tartományhoz csatlakoztatott HDInsight-fürtök kezeléséhez lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](hdinsight-domain-joined-manage.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](hdinsight-domain-joined-run-hive.md).
* A Hive-lekérdezések futtatásához az SSH használatával a tartományhoz csatlakoztatott HDInsight-fürtökben lásd: [Az SSH használata a HDInsightra épülő Linuxalapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md).

