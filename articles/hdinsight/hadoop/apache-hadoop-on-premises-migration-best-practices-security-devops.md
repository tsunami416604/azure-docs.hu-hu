---
title: 'Biztonság: Telepítse a helyszíni Apache Hadoop-ot az Azure HDInsightba'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítésével kapcsolatos biztonsági és DevOps-gyakorlati tanácsokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974410"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Telepítse át a helyszíni Apache Hadoop-fürtöket az Azure HDInsightba – biztonsági és DevOps-gyakorlati tanácsok

Ez a cikk javaslatokat ad a biztonság és a DevOps az Azure HDInsight-rendszerekben. Ez egy olyan sorozat része, amely gyakorlati tanácsokkal segíti a helyszíni Apache Hadoop-rendszerek Azure HDInsightba való áttelepítését.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Fürt biztonságossá tétele és szabályozása a vállalati biztonsági csomaggal

A vállalati biztonsági csomag (ESP) támogatja az Active Directory-alapú hitelesítést, a többfelhasználós támogatást és a szerepköralapú hozzáférés-vezérlést. Az ESP beállítás kiválasztásával a HDInsight-fürt csatlakozik az Active Directory-tartományhoz, és a vállalati rendszergazda az Apache Ranger használatával konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC) az Apache Hive-biztonsághoz. A rendszergazda az alkalmazottak adathozzáférését és a hozzáférés-vezérlési házirendek módosításait is naplózhatja.

Az ESP a következő fürttípusokon érhető el: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka és Interactive Query (Hive LLAP).

A tartományhoz csatlakozott HDInsight-fürt központi telepítéséhez kövesse az alábbi lépéseket:

- Telepítse az Azure Active Directoryt (AAD) a tartománynév átadásával.
- Telepítse az Azure Active Directory tartományi szolgáltatásokat (AAD DS).
- Hozza létre a szükséges virtuális hálózatot és alhálózatot.
- Telepítsen egy virtuális gépet a virtuális hálózatban az AAD DS kezeléséhez.
- Csatlakozzon a virtuális géphez a tartományhoz.
- Telepítse az AD és a DNS-eszközöket.
- Az AAD DS-rendszergazda hozzon létre egy szervezeti egységet.
- LDAPS engedélyezése AAD DS-hez.
- Hozzon létre egy szolgáltatásfiókot az Azure Active Directoryban delegált olvasási & írási rendszergazdai engedéllyel a szervezeti egységnek, hogy azok lehessék. Ez a szolgáltatásfiók ezután csatlakoztathatja a gépeket a tartományhoz, és a szervezeti egységen belül elhelyezheti a gépnévtagokat. A szervezeti egységen belül is létrehozhat szolgáltatásnévi egységeket, amelyeket a fürt létrehozása során megadott.

    > [!Note]
    > A szolgáltatásfióknak nem kell AD tartományfelügyeleti fióknak lennie.

- A HDInsight ESP-fürt telepítése a következő paraméterek beállításával:

    |Paraméter |Leírás |
    |---|---|
    |Tartománynév|Az Azure AD DS-hez társított tartománynév.|
    |Tartomány felhasználóneve|Az előző szakaszban létrehozott Azure AD DS DC által kezelt tartományban `hdiadmin@contoso.onmicrosoft.com`lévő szolgáltatásfiók: . Ez a tartományi felhasználó lesz a HDInsight-fürt rendszergazdája.|
    |Tartományi jelszó|A szolgáltatásfiók jelszava.|
    |Szervezeti egység|A HDInsight-fürthöz használni kívánt szervezeti egység megkülönböztető neve, `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`például: . Ha ez a szervezeti egység nem létezik, a HDInsight-fürt a szolgáltatásfiók jogosultságai alapján próbálja meg létrehozni a szervezeti egységet.|
    |LDAPS URL-cím|például `ldaps://contoso.onmicrosoft.com:636`.|
    |Access felhasználói csoport|Azok a biztonsági csoportok, amelyek felhasználóit szinkronizálni szeretné a fürttel, például: `HiveUsers`. . Ha több felhasználói csoportot szeretne megadni, válassza el őket pontosvesszővel:'. A csoport(ok) létezniük kell a címtárban az ESP-fürt létrehozása előtt.|

További információkért tekintse át a következő cikkeket:

- [Bevezetés az Apache Hadoop biztonságába a tartományhoz csatlakozó HDInsight-fürtökkel](../domain-joined/hdinsight-security-overview.md)
- [Az Azure-tartományhoz csatlakozó Apache Hadoop-fürtök tervezése a HDInsightban](../domain-joined/apache-domain-joined-architecture.md)
- [Tartományhoz csatlakozott HDInsight-fürt konfigurálása az Azure Active Directory tartományi szolgáltatások használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Azure Active Directory-felhasználók HDInsight-fürttel való szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
- [Apache Hive-házirendek konfigurálása a tartományhoz csatlakozó HDInsightban](../domain-joined/apache-domain-joined-run-hive.md)
- [Az Apache Oozie futtatása tartományhoz csatlakozó HDInsight Hadoop-fürtökben](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>A vállalat végső vállalati biztonságának megvalósítása

A teljes körű vállalati biztonság a következő vezérlőkkel érhető el:

**Magán- és védett adatfolyamat (peremszintű biztonság)**
    - Kerületi szintű biztonság érhető el az Azure virtuális hálózatok, hálózati biztonsági csoportok és átjáró szolgáltatás.

**Az adatok hozadékának hitelesítése és engedélyezése**
    - Tartományhoz csatlakozott HDInsight-fürt létrehozása az Azure Active Directory tartományi szolgáltatások használatával. (Vállalati biztonsági csomag).
    - Az Ambari használatával szerepköralapú hozzáférést biztosíthatok az AD-felhasználók fürterőforrásaihoz.
    - Az Apache Ranger használatával az asztal / oszlop / sor szintjén állíthatja be a Hive hozzáférés-vezérlési házirendjeit.
    - A fürthöz való SSH-hozzáférés csak a rendszergazdára korlátozható.

**Naplózás**
    - A HDInsight-fürt erőforrásaihoz és adataihoz való összes hozzáférés megtekintése és jelentése.
    - A hozzáférés-vezérlési házirendek összes módosításának megtekintése és jelentése.

**Titkosítás**
    - Átlátszó kiszolgálóoldali titkosítás Microsoft által kezelt vagy ügyfél által kezelt kulccsal.
    - A tranzit titkosítás tügyféloldali titkosítás, https és TLS használatával.

További információkért tekintse át a következő cikkeket:

- [Az Azure virtuális hálózatok – áttekintés](../../virtual-network/virtual-networks-overview.md)
- [Az Azure hálózati biztonsági csoportok áttekintése](../../virtual-network/security-overview.md)
- [Az Azure virtuális hálózat társviszony-létesítése](../../virtual-network/virtual-network-peering-overview.md)
- [Az Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md)
- [Az Azure Storage Service titkosítása inaktív](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Figyelés & riasztás használata

További információt a következő cikkben talál:

[Azure Monitor – áttekintés](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Fürtök frissítése

Rendszeresen frissítsen a legújabb HDInsight-verzióra, hogy kihasználja a legújabb funkciókat. A következő lépésekkel frissítheti a fürtöt a legújabb verzióra:

1. Hozzon létre egy új TEST HDInsight-fürtöt a legújabb elérhető HDInsight-verzióval.
1. Tesztelje az új fürtön, hogy megbizonyosodjon arról, hogy a feladatok és a munkaterhelések a várt módon működnek.
1. Szükség szerint módosítsa a feladatokat, alkalmazásokat vagy munkaterheléseket.
1. A fürtcsomópontokon helyileg tárolt átmeneti adatok biztonsági és biztonsági másolatot.
1. Törölje a meglévő fürtöt.
1. Hozzon létre egy fürtöt a legújabb HDInsight-verzióból ugyanabban a virtuális hálózati alhálózatban, ugyanazokat az alapértelmezett adatokat és metatárolót használva, mint az előző fürt.
1. Importálja a biztonsági másolatot tartalmazó átmeneti adatokat.
1. Indítsa el a feladatokat/folytassa a feldolgozást az új fürt használatával.

További információt a KÖVETKEZŐ cikkben talál: [A HDInsight-fürt frissítése új verzióra.](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Patch fürt operációs rendszerek

Felügyelt Hadoop-szolgáltatásként a HDInsight gondoskodik a HDInsight-fürtök által használt virtuális gépek operációs rendszerének javításáról.

További információt a [HDInsight operációs rendszer javításáról szóló cikkben talál.](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Áttelepítés utáni

1. **Alkalmazások kiújítása** – Iteratív módon hajtsa végre a szükséges módosításokat a feladatok, folyamatok és parancsfájlok.
2. **Tesztek végrehajtása** – Iteratív módon futtasson funkcionális és teljesítményteszteket.
3. **Optimalizálás** – A fenti teszteredmények alapján orvosolja a teljesítményproblémákat, majd tesztelje újra a teljesítmény javulásának megerősítését.

## <a name="next-steps"></a>További lépések

További információ a [HDInsight 4.0-ról.](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)
