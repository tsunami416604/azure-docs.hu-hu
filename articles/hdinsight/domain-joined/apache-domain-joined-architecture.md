---
title: Tartományhoz csatlakozó Azure HDInsight-architektúra |} Microsoft Docs
description: Útmutató a tartományhoz csatlakoztatott HDInsight tervezéséhez.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8503534031dc5774e64c58edd3e158162a5a6aee
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110454"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Azure-tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsightban

A szabványos HDInsight-fürt egyfelhasználós fürttagként. Ez megfelel a legtöbb olyan cégnek, amelyekben kisebb alkalmazásfejlesztő csapatok dolgoznak a nagy adatszámítási feladatok kiépítésén. Minden felhasználó egy másik fürthöz dedikált rá az igény szerinti rendelkezik, és nincs többé szükség esetén semmisítse meg. Azonban számos vállalat lépések áthelyezése egy modelltől, amelyben fürtök informatikai csapatoknak kezeli, és több alkalmazás megosztás fürtök csoportok felé. Ebből kifolyólag többfelhasználós hozzáférés a fürthöz szükséges az Azure HDInsight a nagyobb vállalatok számára.

A HDInsight legnépszerűbb identitásszolgáltató – aktív a Directory (AD) a felügyelt módon támaszkodik. A HDInsight integrálásával [Azure Active Directory tartományi szolgáltatások (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md), a fürtök a tartományi hitelesítő adataival való hozzáféréshez. A virtuális gépek hdinsight olyan tartományhoz a megadott tartományhoz, így a szolgáltatások futnak, a HDInsight (Ambari, kiszolgáló, idejére Spark thrift Hive server és a mások) munkahelyi zökkenőmentesen a hitelesített felhasználó számára. A rendszergazdák Apache Pletyka használatát a szerepköralapú hozzáférés-vezérlés a fürterőforrások erős engedélyezési házirendeket is létrehozhat.


## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

A Kerberos hitelesítés és a biztonsági nyílt forráskódú Hadoop támaszkodik. Emiatt a HDInsight-fürtcsomóponton lehet tartományi tartományhoz csatlakozik az AAD-DS kezeli. A Hadoop-összetevők a fürt a Kerberos biztonsági van konfigurálva. Az egyes a Hadoop-összetevők egy egyszerű szolgáltatásnév automatikusan létrejön. Egy egyszerű megfelelő gépek is létrejön, minden gép, amely csatlakozik a tartományhoz. A szolgáltatás és a gépi rendszerbiztonsági tagok tárolásához, szükség van rá adjon meg egy szervezeti egység (OU) belül a tartományvezérlő (AAD-DS), ha ezen rendszerbiztonsági tagok vannak elhelyezve. 

Összefoglalva, akkor be kell állítania tartalmazó környezetben:

- Az Active Directory-tartomány (AAD-Directory tartományi szolgáltatások által kezelt)
- Biztonságos LDAP (LDAPS) engedélyezve van az AAD-DS-ben.
- Megfelelő hálózati kapcsolat a HDInsight által az AAD-DS VNET VNET abban az esetben, ha úgy dönt, külön Vnetek számukra. A virtuális gépek a HDI virtuális hálózaton belül rendelkeznie kell egy sor a láthatáron AAD-DS használatával a VNETBEN társviszony-létesítés. Ha ugyanazon virtuális HDI, mind az AAD-DS van telepítve, a kapcsolat automatikusan biztosítja, és nincs további teendő.
- Egy szervezeti egység (OU) [AAD-DS létre](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- A szolgáltatás fiók, amely jogosult:
    - A szervezeti egység létrehozása szolgáltatásnevekről.
    - Számítógépek csatlakoztatása a tartományhoz, és hozzon létre számítógép rendszerbiztonsági tagok a szervezeti Egységhez.

Az alábbi képernyőfelvételen látható egy szervezeti Egységet létrehozni a contoso.com webhelyen. Egyes szolgáltatásnevekről és gép rendszerbiztonsági tagok a képernyőkép is mutatja be.

![Tartományhoz csatlakoztatott HDInsight-fürtök szervezeti egység](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Különböző tartományi vezérlők beállítása:
A HDInsight jelenleg csak támogatja az AAD-DS a fő tartományvezérlőnek, hogy a fürt kommunikál a Kerberise a fürt. Azonban más összetett AD beállításokat is is előfordulhatnak, mindaddig, amíg az AAD-DS HDI-hozzáférés engedélyezéséhez vezet.

- **[Az Azure Active Directory tartományi szolgáltatások (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: Ez a szolgáltatás egy felügyelt tartomány, amely teljes mértékben kompatibilis a Windows Server Active Directory biztosítja. A Microsoft gondoskodik, javítását és a figyelés a tartomány egy magas Available(HA) beállítása. A fürtök telepítése anélkül, hogy a tartományvezérlők karbantartásához. Felhasználók, csoportok és jelszavak szinkronizálódnak a a Azure Active Directory(AAD) [egyirányú szinkronizálás aad AAD-DS], amely lehetővé teszi felhasználók és jelentkezzen be a fürt vállalati hitelesítő adatokkal. További információkért lásd: [hogyan konfigurálhatja a tartományhoz csatlakoztatott HDInsight-fürtök AAD-DS használatával](./apache-domain-joined-configure-using-azure-adds.md).
- **A helyszíni AD vagy ad szolgáltatásokba, az infrastruktúra-szolgáltatási virtuális gépeken futó**: Ha egy helyszíni AD-re vagy más összetett AD beállításokat a tartomány, szinkronizálhatja az aad-be használatával AD Connect majd engedélyezése az AAD-DS-t, hogy AD-bérlői megszakítása. Mivel Kerberos jelszókivonatait támaszkodik, szüksége lesz a [az AAD-DS Jelszókivonat-szinkronizálás engedélyezése](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Ha összevonási használja az AD összevonási szolgáltatások (ADFS), opcionálisan beállíthatja Jelszókivonat-szinkronizálás biztonsági abban az esetben, ha az AD FS infrastruktúra sikertelen lesz. További információk: [Jelszókivonat-szinkronizálás és az AAD Connect-szinkronizálás engedélyezése](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). A helyszíni AD vagy AD az infrastruktúra-szolgáltatási virtuális gépeken futó önmagában, aad-ben és az AAD-DS nélkül használata nem támogatott konfiguráció HDI fürt tartományhoz való csatlakozásra.

## <a name="next-steps"></a>További lépések
* [Konfigurálja a HDInsight-fürtök tartományhoz](apache-domain-joined-configure-using-azure-adds.md).
* [A HDInsight-fürtök tartományhoz Hive-szabályzatok konfigurálása](apache-domain-joined-run-hive.md).
* [Tartományhoz csatlakozó HDInsight-fürtök kezelése](apache-domain-joined-manage.md). 
