---
title: Vállalati biztonsági csomag egy HDInsight-fürt konfigurálása az Azure AD-DS-sel
description: Megtudhatja, hogyan állíthatja be, és a HDInsight vállalati biztonsági csomag fürt konfigurálása Azure Active Directory Domain Services használatával.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eb24aa0471604696de99f4878baef764cfef0a8b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408354"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>A HDInsight-fürt konfigurálása a vállalati biztonsági csomaggal az Azure Active Directory Domain Services használatával

Vállalati biztonsági csomag (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. ESP HDInsight-fürtöket, hogy a tartományi felhasználók tartományi hitelesítő adataik használatával a fürtökkel hitelesítéséhez és a big data-feladatokat futtatni egy tartományhoz csatlakozik. 

Ebből a cikkből elsajátíthatja egy HDInsight-fürt konfigurálása ESP az Azure Active Directory Domain Services (Azure AD DS) használatával.

>[!NOTE]
>ESP általánosan elérhető a HDI 3.6 a Spark, interaktív és Hadoop. A HBase és a Kafka fürttípusok ESP az előzetes verzióban.

## <a name="enable-azure-ad-ds"></a>Engedélyezze az Azure AD-DS-ben

Az Azure Active-Directory tartományi szolgáltatások engedélyezése előfeltétele az ESP használata egy HDInsight-fürt létrehozása előtt. További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Az Azure AD-Tartományi engedélyezve van, minden felhasználó és objektumok indítsa el az alapértelmezés szerint az Azure AD-Tartományi szinkronizálása az Azure Active Directory (AAD). A szinkronizálási művelet függ az aad-beli objektumok száma. A szinkronizálás eltarthat pár nappal a több száz, több ezer objektumot tartalmaz. 

Ügyfelek kiválaszthatják a szinkronizálás csak a csoportokat, amelyek a HDInsight-fürtökbe való hozzáférésre van szükségük. Ez a beállítás csak bizonyos csoportokat a szinkronizálás nevezzük *szinkronizálás hatóköre*. Lásd: [konfigurálása hatókörrel rendelkező Azure AD-ből a felügyelt tartományhoz való szinkronizálás](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) útmutatást.

> [!NOTE]
> Csak a bérlői rendszergazdák a jogosultság hozzon létre egy Azure AD-DS-példányt. A multi-factor authentication csak a fürthöz hozzáférő felhasználók számára le kell tiltani kell.

Secure LDAP engedélyezése, ha a tanúsítvány helyezze el a tartománynév a tulajdonos neve vagy a tulajdonos alternatív neveként. Például, ha a tartománynév *contoso.com*, ellenőrizze, hogy pontos neve létezik-e a tanúsítvány tulajdonos neve vagy a tulajdonos alternatív neve. További információkért lásd: [a felügyelt tartomány secure LDAP konfigurálása az Azure AD-DS a](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-aad-ds-health-status"></a>AAD-DS állapot ellenőrzése

Megtekintheti az Azure Active Directory Domain Services állapotának **állapotfigyelő** alatt a **kezelés** kategória. Ellenőrizze, hogy AAD-DS állapota zöld (fut) és a szinkronizálás befejeződött.

![Az Azure Active Directory Domain Services állapota](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Adja hozzá a felügyelt identitás

Miután engedélyezte az Azure AD-Tartományi, felhasználó által hozzárendelt felügyelt identitás létrehozása, majd rendelje hozzá a **HDInsight Domain Services közreműködői** szerepkört az Azure AD-Tartományi hozzáférés-vezérlés.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Egy felügyelt identitás hozzárendelése a **HDInsight Domain Services közreműködői** szerepkör biztosítja, hogy az identitás megfelelő hozzáféréssel az AAD-DS-tartományról bizonyos domain services műveletek végrehajtásához. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP-HDInsight-fürt létrehozása

A következő lépés, hogy a HDInsight-fürt létrehozása a engedélyezve van, az Azure AD-DS-sel ESP.

Az Azure AD-DS-példánya és a HDInsight-fürt is helyezni, az azonos Azure virtuális hálózat könnyebbé válik. Helyezi őket a különböző virtuális hálózatokban lévő választja, így a HDInsight virtuális gépnek rendelkeznie a tartományvezérlőhöz való csatlakozáshoz a virtuális gépek egy üzemel társviszonyba állítása kell ezen virtuális hálózatok. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md). Annak tesztelése, ha a társviszony-létesítés helyesen végezték el, csatlakoztatni egy virtuális Gépet a HDInsight virtuális hálózat/alhálózat, és pingelni a tartománynév, vagy futtassa **ldp.exe** hozzáférés AAD-DS-tartományhoz.

Amikor létrehoz egy HDInsight-fürtöt, hogy egyéni lapján engedélyezheti a vállalati biztonsági csomag. 

![Az Azure HDInsight biztonsági és hálózatkezelési](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Miután engedélyezte a ESP, az Azure AD-Tartományi kapcsolatos gyakori konfigurációs hibáinak lehet automatikusan észlelte és érvényesítve.

![Az Azure HDInsight vállalati biztonsági csomag tartomány ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Korai észlelését időt takaríthat meg azáltal, hogy a fürt létrehozása előtt javítsa a hibákat.

![Az Azure HDInsight vállalati biztonsági csomag nem sikerült ellenőrizni a tartományt](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP egy HDInsight-fürtöt hoz létre, amikor meg kell adnia a következő paraméterekkel:

- **Fürt rendszergazdai felhasználói**: válassza ki a fürt rendszergazdája, a szinkronizált Azure AD-DS-ben. Ez a fiók már kell lennie a szinkronizált és az AAD-DS-ben elérhető.

- **A fürt hozzáféréscsoportokat**: A biztonsági csoportok segít a felhasználóknak a fürthöz szinkronizálni szeretné az Azure AD-Tartományi elérhetőnek kell lennie. Ha például HiveUsers csoport. További információkért lásd: [hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL-cím**: Példa ldaps://contoso.com:636.

Az alábbi képernyőfelvételen egy sikeres konfigurációk az Azure Portalon:

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A létrehozott felügyelt identitás lehet kiválasztani az a felhasználó által hozzárendelt felügyelt identitás legördülő listából új fürt létrehozása során.

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>További lépések
* Hive-házirendek konfigurálása és Hive-lekérdezések futtatása: [Hive-házirendek konfigurálása a HDInsight-fürtök ESP](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozhat a HDInsight-fürtök ESP, lásd: [az SSH használata a Linux-based Hadoop on HDInsight Linux, Unix vagy OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).