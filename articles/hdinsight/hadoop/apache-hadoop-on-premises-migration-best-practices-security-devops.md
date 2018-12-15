---
title: Azure HDInsight – biztonság és a fejlesztési és üzemeltetési eljárások helyszíni Apache Hadoop-fürtök áttelepítése
description: Ismerje meg, biztonsági és a fejlesztési és üzemeltetési áttelepítése a helyszíni Hadoop-fürtöket az Azure HDInsight ajánlott eljárásait.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 7cfb327a3eb6cbf2ae90c9d258a470797732acaa
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437503"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Azure HDInsight – biztonság és a fejlesztési és üzemeltetési eljárások helyszíni Apache Hadoop-fürtök áttelepítése

Ez a cikk az Azure HDInsight rendszerben javaslatok a biztonsági és a fejlesztési és üzemeltetési biztosít. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Biztonságos, és szabályozhatja a fürtön vállalati biztonsági csomaggal

A vállalati biztonsági csomag (ESP) támogatja az Active Directory-alapú hitelesítés, a több felhasználó támogatása és a szerepköralapú hozzáférés-vezérlés. A választott ESP beállítással a HDInsight-fürt az Active Directory-tartományhoz csatlakozik, és a vállalati rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlés (RBAC) az Apache Hive-biztonsághoz az Apache Ranger segítségével. A rendszergazda is naplózhatja az alkalmazottak és bármely változtatást a hozzáférés-vezérlési házirendeket adatelérési.

A következő fürttípusokat ESP érhető el: Az Apache Hadoop, az Apache Spark, az Apache HBase, Apache Kafka és az interaktív lekérdezések (Hive LLAP). 

Használja az alábbi lépéseket a tartományhoz csatlakoztatott HDInsight-fürt üzembe helyezéséhez:

- A tartománynév átadásával, üzembe helyezése az Azure Active Directory (AAD).
- Az Azure Active Directory Domain Services (AAD-DS) telepítése.
- Hozzon létre a szükséges virtuális hálózatot és alhálózatot.
- AAD DS kezelheti a virtuális hálózat egy virtuális gép üzembe helyezése.
- A virtuális gép csatlakoztatása a tartományhoz.
- Telepítse az AD és a DNS-eszközök.
- Kérje meg az AAD Tartományi rendszergazdát, hozzon létre egy szervezeti egység (OU).
- LDAPS-t engedélyezése AAD Tartományi.
- Hozzon létre egy szolgáltatásfiókot az Azure Active Directoryban a delegált olvasási és írási rendszergazdai engedéllyel a szervezeti Egységhez való, hogy képes kezelni. Ez a szolgáltatás fiók ezután gépek csatlakoztatása a tartományhoz, és helyezze el a gép rendszerbiztonsági tagok belül a szervezeti Egységhez. Azt is létrehozhat egyszerű szolgáltatást lekéri a szervezeti Egységet a fürt létrehozásakor megadott.


    > [!Note]
    > A szolgáltatásfiók nem kell az AD tartományi rendszergazdai fiókot.


- Az alábbi paraméterek beállításával a HDInsight ESP-fürt üzembe helyezése:
    - **Tartománynév**: A tartománynév, a társított Azure Active Directory tartományi Szolgáltatásokban.
    - **Tartományi felhasználónév**: A fiók, amelyet az előző szakaszban, például az Azure AD DS-tartományvezérlő által felügyelt tartományban: `hdiadmin@contoso.onmicrosoft.com`. A tartományi felhasználó a rendszergazda a HDInsight-fürt lesz.
    - **Tartományi jelszó**: A szolgáltatásfiók jelszava.
    - **Szervezeti egység**: A például a HDInsight-fürt a használni kívánt szervezeti egység megkülönböztető nevét: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Ha a szervezeti egység nem létezik, a HDInsight-fürt próbál létrehozni a szervezeti Egységet a jogosultságokat a szolgáltatási fiók használatával.
    - **LDAPS URL-cím**: például `ldaps://contoso.onmicrosoft.com:636`.
    - **Hozzáférési felhasználói csoport**: A biztonsági csoportok, amelynek felhasználói szeretné szinkronizálni a fürthöz, például: `HiveUsers`. Ha meg szeretné határozni a több felhasználói csoportot, válassza el őket pontosvesszővel (;). A csoport a címtárban az ESP-fürt létrehozása előtt léteznie kell.

További információkért tekintse át a következő cikkeket:

- [Apache Hadoop-biztonság használatába a tartományhoz csatlakoztatott HDInsight-fürtök bemutatása](../domain-joined/apache-domain-joined-introduction.md)

- [Az Azure-tartományhoz az Apache Hadoop-fürtök tervezése a HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [A tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Egy HDInsight-fürtön az Azure Active Directory-felhasználók szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
- [Az Apache Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight](../domain-joined/apache-domain-joined-run-hive.md)
- [Az Apache Oozie futtathat a tartományhoz csatlakoztatott HDInsight Hadoop-fürtöket](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Teljes körű vállalati biztonság

A végpontok közötti vállalati biztonsági hajthatók végre a következő vezérlőknek a használatát:

- **A magán- és védett adatfolyamat (szegélyhálózat-alapú biztonság)**
    - Szegélyhálózat-alapú szintű biztonság Azure-beli virtuális hálózatok, hálózati biztonsági csoportok és az átjárószolgáltatás keresztül érhető el.

- **Hitelesítés és engedélyezés az adatok eléréséhez**
    - Hozzon létre az Azure Active Directory Domain Services tartományhoz csatlakoztatott HDInsight-fürtön. (Vállalati biztonsági csomag).
    - Adja meg a fürt erőforrásai szerepkörön alapuló hozzáférés AD-felhasználók az Ambari használatával.
    - Az Apache Ranger segítségével értékre beállított hozzáférési Alkalmazásvezérlési házirendeket a Hive a tábla vagy oszlop / sorszinthez.
    - A fürthöz SSH-hozzáférés csak a rendszergazda korlátozható.

- **Naplózás**
    - Megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásainak és az adatokat.
    - Megtekintheti és jelentheti a hozzáférés-vezérlési házirendeket összes módosítást.

- **Titkosítás**
    - Transzparens kiszolgálóoldali titkosítás, a Microsoft által kezelt kulcsok vagy felhasználó által kezelt kulcsok használata.
    - Az átvitel során titkosítás ügyféloldali titkosítás, a https és a TLS használatával.

További információkért tekintse át a következő cikkeket:

- [Az Azure virtuális hálózatok áttekintése](../../virtual-network/virtual-networks-overview.md)
- [Az Azure hálózati biztonsági csoportok áttekintése](../../virtual-network/security-overview.md)
- [Az Azure virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md)
- [Az Azure storage biztonsági útmutatóját](../../storage/common/storage-security-guide.md)
- [Az Azure Storage Service Encryption inaktív](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Használja a monitorozás és riasztások

További információkért tekintse meg a cikket:

[Azure Monitor – áttekintés](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Fürtök frissítése

A HDInsight legújabb a legújabb funkciók előnyeinek kihasználása érdekében rendszeresen frissíteni. A következő lépések segítségével a fürt frissítése a legújabb verzióra:

1. Hozzon létre egy új tesztelési HDInsight-fürtöt, a legújabb elérhető HDInsight-verzió használatával.
1. Tesztelje az új fürtön, győződjön meg arról, hogy a feladatok és a számítási feladatok a várt módon működik.
1. Feladatok vagy alkalmazások vagy munkaterhelések szükség szerint módosítsa.
1. Készítsen biztonsági másolatot a fürtcsomópontokon helyileg tárolt átmeneti adatok.
1. Törölje a meglévő fürtből.
1. A legújabb verziójú HDInsight-fürt létrehozása a ugyanazon virtuális hálózat alhálózat, mint az előző fürtben alapértelmezett adat- és metaadat-tároló használatával.
1. Átmeneti az adatok, amelyek készült biztonsági másolat importálása.
1. Indítási feladatok/folytatni a használatával az új fürtön.

További információkért tekintse meg a cikket: [Új verzió frissítési HDInsight-fürt](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Javítás az operációs rendszerek fürtbe

Felügyelt Hadoop-szolgáltatás HDInsight gondoskodik az operációs rendszer, a HDInsight-fürtök által használt virtuális gépek javítása.

További információkért tekintse meg a cikket: [Az operációs rendszer javításai a HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Áttelepítés utáni

1. **Javíthatja az alkalmazások** – iteratív végezze el a szükséges módosításokat a feladatok, folyamatok és parancsprogramok.
2. **Tesztek végrehajtása** – iteratív működési Futtatás és a teljesítmény teszteket.
3. **Optimalizálja** - teljesítmény problémák elhárítása a fenti teszt eredményei alapján, és erősítse meg a teljesítménnyel kapcsolatos fejlesztések, majd tesztelje újra.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
