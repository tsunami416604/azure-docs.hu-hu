---
title: Tartományhoz csatlakoztatott Azure HDInsight-architektúra
description: Útmutató a tartományhoz csatlakoztatott HDInsight tervezéséhez.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: d5132cf2414045ca1343354215b2a4564f696190
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044770"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A standard szintű Azure HDInsight-fürtöt, egy egyfelhasználós fürt. Ideális a legtöbb vállalatok számára, hogy kisebb alkalmazásfejlesztő csapatok dolgoznak, nagy mennyiségű adat számítási feladatok létrehozásához. Hozzon létre egy dedikált fürtöt igény szerinti és nincs többé szükség esetén semmisítse meg a minden felhasználónak. 

Sok vállalat áthelyezett olyan modellre, ahol fürtök informatikai csapatok felügyelik, és több alkalmazásfejlesztő csapat ugyanazokon a fürtökön. Ezek a nagyobb cégek minden egyes fürt az Azure HDInsight többfelhasználós hozzá kell férniük.

HDInsight a legnépszerűbb identitásszolgáltatóra, az Active Directory – az olyan felügyelt módon támaszkodik. A HDInsight integrálásával [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), a fürtök a tartományi hitelesítő adataival érheti. 

A virtuális gépek (VM) a HDInsight tartományhoz csatlakoztatva, a megadott tartományban. Tehát a HDInsight futó összes szolgáltatás (Ambari, Hive-kiszolgáló, Ranger, Spark thrift kiszolgáló és mások) zökkenőmentesen használható a hitelesített felhasználó. A rendszergazdák majd erős engedélyezési házirendek használatával létrehozhatja az Apache Ranger szerepköralapú hozzáférés-vezérlést biztosít a fürtben lévő erőforrásokat.


## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

A Kerberos hitelesítési és biztonsági Hadoop nyílt forráskódú támaszkodik. Ezért HDInsight-fürtcsomópontok tartományhoz csatlakoztatva az Azure Active Directory tartományi szolgáltatások által felügyelt tartományhoz. A Kerberos biztonsági konfigurálva van a Hadoop-összetevők a fürtön. 

Az egyes Hadoop-összetevők egy szolgáltatásnév automatikusan létrejön. Az egyes gépek a tartományhoz csatlakozó is létrejön egy egyszerű megfelelő gépre. E szolgáltatás tárolására, és a gép rendszerbiztonsági tagok, meg kell adnia egy szervezeti egységhez (OU) belül a tartományvezérlő (Azure AD DS), ha ezen rendszerbiztonsági tagok kerülnek. 

Összefoglalva, szüksége a környezet beállításához:

- Active Directory-tartomány (az Azure Active Directory tartományi szolgáltatások által kezelt).
- Biztonságos LDAP (LDAPS) engedélyezve van az Azure Active Directory tartományi szolgáltatásokban.
- Megfelelő hálózati kapcsolat a HDInsight virtuális hálózat és az Azure Active Directory tartományi szolgáltatások virtuális hálózat, ha úgy dönt, különálló virtuális hálózatok az őket. A HDInsight virtuális hálózaton belüli virtuális gépek rendelkeznie kell egy virtuális hálózati társviszony-létesítésen keresztül az Azure Active Directory tartományi szolgáltatásokban üzemel. Ha a HDInsight és az Azure Active Directory tartományi szolgáltatások ugyanazon a virtuális hálózaton üzembe helyezett, a rendszer automatikusan létrehozza a kapcsolatot, és további semmit nem kell tennie.
- A szervezeti egység [az Azure Active Directory tartományi Szolgáltatásokban létrehozott](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).
- A szolgáltatás fiók, amely jogosultsággal rendelkezik:
    - A szervezeti Egységben lévő egyszerű szolgáltatások létrehozása.
    - Gépek csatlakoztatása a tartományhoz, és a gép egyszerű létrehozása a szervezeti egységben.

Az alábbi képernyőfelvételen egy szervezeti egység létrehozása a contoso.com. Azt is bemutatja, az egyszerű szolgáltatásnevekről és a gép rendszerbiztonsági tagok közül.

![A tartományhoz csatlakoztatott HDInsight-fürtök szervezeti egység](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

## <a name="set-up-different-domain-controllers"></a>Állítsa be a különféle tartományvezérlők
HDInsight jelenleg csak az Azure AD DS támogatja a fő tartományvezérlő, a fürt által használt a Kerberos-kommunikációhoz. Azonban más összetett az Active Directory-beállításokat is lehetséges, mindaddig, amíg egy ilyen beállítás vezet, a HDInsight hozzáférést az Azure Active Directory tartományi szolgáltatások engedélyezése.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások
[Az Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md) biztosít, amely teljesen kompatibilis a Windows Server Active Directoryval felügyelt tartományhoz. A Microsoft gondoskodik kezelése, javításokat és a tartomány egy magas rendelkezésre állású (HA) beállítás figyelése. Telepítheti a fürt nem kell bajlódnunk a tartományvezérlők karbantartásához. 

Felhasználók, csoportok és jelszavak szinkronizálódnak, az Azure Active Directory (Azure AD). Az Azure Active Directory tartományi szolgáltatások az Azure AD-példányt a egyirányú szinkronizálás lehetővé teszi, hogy a felhasználók által a vállalati hitelesítő adatokkal jelentkezhetnek be a fürt. 

További információkért lásd: [konfigurálása tartományhoz csatlakoztatott HDInsight-fürtök az Azure AD DS segítségével](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>A helyszíni Active Directory vagy az Active Directory IaaS virtuális gépeken

Ha egy helyszíni Active Directory-példányból vagy összetettebb Active Directory-beállításokat a tartomány, ezeket az Azure AD-identitások szinkronizálhatók az Azure AD Connect használatával. Engedélyezheti, hogy az Active Directory-bérlőre az Azure Active Directory tartományi Szolgáltatásokban. 

Mivel Kerberos jelszókivonatokat alapul, kell [Jelszókivonat-szinkronizálás az Azure Active Directory tartományi szolgáltatások engedélyezése](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Ha összevonási az Active Directory összevonási szolgáltatások (AD FS) használ, igény szerint állíthat be Jelszókivonat-szinkronizálás biztonsági abban az esetben az AD FS-infrastruktúra sikertelen lesz. További információkért lásd: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás engedélyezése](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). 

Használatával a helyszíni Active Directory vagy az IaaS virtuális gépek önmagában, az Active Directory nélkül az Azure AD és az Azure AD DS-ben, a beállítás nem támogatott a tartományhoz csatlakoztatott HDInsight-fürtök.

## <a name="next-steps"></a>További lépések
* [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure-using-azure-adds.md)
* [Hive-házirendek a tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-run-hive.md)
* [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](apache-domain-joined-manage.md) 
