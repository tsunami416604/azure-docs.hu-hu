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
ms.date: 12/14/2017
ms.author: saurinsh
ms.openlocfilehash: eca019fa5e7866ed6281e8cfee105ba1d99249bc
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A szabványos HDInsight-fürt egyfelhasználós fürttagként. Ez megfelel a legtöbb olyan cégnek, amelyekben kisebb alkalmazásfejlesztő csapatok dolgoznak a nagy adatszámítási feladatok kiépítésén. Hadoop szerzett időben népszerűvé vált, mivel számos vállalat lépések áthelyezése egy modelltől, amelyben fürtök informatikai csapatoknak kezeli, és több alkalmazás megosztás fürtök csoportok felé. Így a többfelhasználós fürtöket használó funkció az egyik legtöbbet kért funkció a HDInsightban.

Helyett a saját többfelhasználós hitelesítési és engedélyezési készítéséhez, a HDInsight legnépszerűbb identitásszolgáltató – Active Directory (AD) alapul. A hatékony biztonsági funkció, az Active Directory segítségével hdinsight többfelhasználós hitelesítés kezelésére szolgál. Az ad-val a HDInsight integrálásával kommunikálhat a fürtök AD hitelesítő adataival. A virtuális gépek hdinsight tartományhoz az ad, és ez hogyan HDInsight van leképezve egy AD-felhasználó a helyi Hadoop-felhasználó, így a szolgáltatás HDInsight (Ambari, kiszolgáló, idejére Spark thrift Hive server és a mások) munkahelyi zökkenőmentesen a hitelesített felhasználó számára. A rendszergazdák Apache Pletyka használatát a szerepköralapú hozzáférés-vezérlés a Hdinsightban erőforrások erős engedélyezési házirendeket is létrehozhat.


## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

HDInsight integrációja az Active Directoryval, a HDInsight-fürtcsomóponton, a tartományhoz egy AD-tartományhoz. A Hadoop-összetevők a fürt a Kerberos biztonsági van konfigurálva. Az egyes a Hadoop-összetevők egy egyszerű szolgáltatásnév az Active Directory jön létre. Egy egyszerű megfelelő gépek is létrejön, minden gép, amely csatlakozik a tartományhoz. Ezek szolgáltatásnevekről és a gép rendszerbiztonsági tagoknak is megzavarhatják az Active Directory szolgáltatásba. Ennek eredményeképpen szükség van rá adjon meg egy szervezeti egység (OU) az Active Directoryban, ha ezen rendszerbiztonsági tagok vannak elhelyezve. 

Összefoglalva, a környezet kell beállításával:

- Az Active Directory tartományvezérlő LDAPS konfigurálva.
- Az Active Directory tartományvezérlőjéhez HDInsight tartozó virtuális hálózati kapcsolat.
- Az Active Directory létrehozott szervezeti egység.
- A szolgáltatás fiók, amely jogosult:

    - A szervezeti egység létrehozása szolgáltatásnevekről.
    - Számítógépek csatlakoztatása a tartományhoz, és hozzon létre számítógép rendszerbiztonsági tagok a szervezeti Egységhez.

Az alábbi képernyőfelvételen látható egy szervezeti Egységet létrehozni a contoso.com webhelyen. Egyes szolgáltatásnevekről és gép rendszerbiztonsági tagok a képernyőkép is mutatja be.

![Tartományhoz csatlakoztatott HDInsight-fürtök szervezeti egység](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="three-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Három módon, hogy a saját Active Directory-tartományvezérlők

Három módon helyezheti az Active Directory-tartományvezérlők tartományhoz a HDInsight-fürtök létrehozása. 

- **Az Azure Active Directory tartományi szolgáltatások**: Ez a szolgáltatás egy felügyelt Active Directory-tartományhoz, amely teljes mértékben kompatibilis a Windows Server Active Directory biztosítja. A Microsoft gondoskodik kezelését, javítását és az AD-tartomány ellenőrzése. A fürtök telepítése anélkül, hogy a tartományvezérlők karbantartásához. Felhasználók, csoportok, és jelszavak szinkronizálódnak az Azure Active Directoryból, így a felhasználók jelentkezzen be a fürtre, a vállalati hitelesítő adatok használatával. További információkért lásd: [Azure Active Directory tartományi szolgáltatások konfigurálása tartományhoz csatlakoztatott HDInsight-fürtök](./apache-domain-joined-configure-using-azure-adds.md).

- **Az Azure IaaS virtuális gépeken futó Active Directory**: ezt a beállítást, az telepíti és kezeli a saját Windows Server Active Directory-tartomány Azure IaaS virtuális gépeken. További információkért lásd: [konfigurálása tartományhoz csatlakoztatott védőfal mögötti környezet](./apache-domain-joined-configure.md).

- **A helyszíni Active Directory**: ezt a beállítást, a HDInsight integrálása a helyszíni Active Directory-tartományvezérlőhöz.


## <a name="next-steps"></a>További lépések
* A tartományhoz csatlakoztatott HDInsight-fürtök konfigurálásához lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](apache-domain-joined-configure.md).
* A tartományhoz csatlakoztatott HDInsight-fürtök kezeléséhez lásd: [Tartományhoz csatlakoztatott HDInsight-fürtök kezelése](apache-domain-joined-manage.md).
* A Hive-házirendek konfigurálásához és a Hive-lekérdezések futtatásához lásd: [Hive-házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight-fürtökben](apache-domain-joined-run-hive.md).
* Hive-lekérdezések futtatása HDInsight-fürtök tartományhoz az ssh protokoll használatával, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).
