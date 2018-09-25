---
title: Vállalati biztonsági csomag egy HDInsight-fürt konfigurálása az Azure AD-DS-sel
description: Ismerje meg, hogyan állíthatja be, és a HDInsight vállalati biztonsági csomag fürt konfigurálása Azure Active Directory Domain Services használatával
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a5b377381fd540c2a9f1d85e0cb7edce32c2dae8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968373"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>A HDInsight-fürt konfigurálása a vállalati biztonsági csomaggal az Azure Active Directory Domain Services használatával

Vállalati biztonsági csomag (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. ESP HDInsight-fürtöket, hogy a tartományi felhasználók tartományi hitelesítő adataik használatával a fürtökkel hitelesítéséhez és a big data-feladatokat futtatni egy tartományhoz csatlakozik. 

Ebből a cikkből elsajátíthatja egy HDInsight-fürt konfigurálása ESP az Azure Active Directory Domain Services (Azure AD DS) használatával.

>[!NOTE]
>ESP HDI 3.6-os + a Spark, interaktív és Hadoop érhető el. ESP HBase-fürt esetében az előzetes verzióban.


## <a name="enable-azure-ad-ds"></a>Engedélyezze az Azure AD-DS-ben

Az Azure Active-Directory tartományi szolgáltatások engedélyezése előfeltétele az ESP használata egy HDInsight-fürt létrehozása előtt. További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Csak a bérlői rendszergazdák a jogosultság hozzon létre egy Azure AD-DS-példányt. Ha HDInsight használata az Azure Data Lake Storage Gen1 az alapértelmezett tárolóként, győződjön meg róla, hogy az alapértelmezett Azure AD-bérlőhöz tartozó Data Lake Storage Gen1 ugyanaz, mint a tartományon a HDInsight-fürt. Hadoop támaszkodik a Kerberos és az alapszintű hitelesítés, mert a multi-factor authentication kell a fürthöz hozzáférő felhasználóknak le kell tiltani.

A Secure LDAP szól az Azure Active-Directory tartományi szolgáltatások által felügyelt tartományokhoz. Amikor engedélyezi az LDAPS-t, a tanúsítvány helyezze el a tartománynév a tulajdonos neveként vagy alternatív neveként. További információkért lásd: [a felügyelt tartomány secure LDAP konfigurálása az Azure AD-DS a](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="add-managed-identity"></a>Adja hozzá a felügyelt identitás

Miután engedélyezte az Azure AD-Tartományi, hozzon létre egy felügyelt identitás, és rendelje hozzá a **HDInsight Domain Services közreműködői** szerepkört az Azure AD-Tartományi hozzáférés-vezérlés.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP-HDInsight-fürt létrehozása

A következő lépés, hogy a HDInsight-fürt létrehozása a engedélyezve van, az Azure AD-DS-sel ESP.

Az Azure AD-DS-példánya és a HDInsight-fürt is helyezni, az azonos Azure virtuális hálózat könnyebbé válik. Helyezi őket a különböző virtuális hálózatokban lévő választja, így a HDInsight virtuális gépnek rendelkeznie a tartományvezérlőhöz való csatlakozáshoz a virtuális gépek egy üzemel társviszonyba állítása kell ezen virtuális hálózatok. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md).

Amikor létrehoz egy HDInsight-fürtöt, engedélyezheti a vállalati biztonsági csomag, csatlakoztathatja a fürtjét az Azure AD-Tartományi rendelkezik. 

![Az Azure HDInsight biztonsági és hálózatkezelési](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Miután engedélyezte a ESP, az Azure AD-Tartományi kapcsolatos gyakori konfigurációs hibáinak lehet automatikusan észlelte és érvényesítve.

![Az Azure HDInsight vállalati biztonsági csomag tartomány ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Korai észlelését időt takaríthat meg azáltal, hogy a fürt létrehozása előtt javítsa a hibákat.

![Az Azure HDInsight vállalati biztonsági csomag nem sikerült ellenőrizni a tartományt](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP egy HDInsight-fürtöt hoz létre, amikor meg kell adnia a következő paraméterekkel:

- **Fürt rendszergazdai felhasználói**: válassza ki a fürt rendszergazdája, a szinkronizált Azure AD-DS-ben.

- **A fürt hozzáféréscsoportokat**: segít a felhasználóknak a fürthöz szinkronizálni kívánt legyen szinkronizált és elérhető az Azure AD-Tartományi biztonsági csoportok. Ha például HiveUsers. Ha meg szeretné határozni a több felhasználói csoportot, válassza el őket pontosvesszővel (;). A csoport(ok) léteznie kell a címtár kiépítése előtt. További információkért lásd: [hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). A csoport nem létezik, ha hiba lép fel: "Csoport HiveUsers nem található az Active Directoryban."

- **LDAPS URL-cím**: Példa ldaps://contoso.onmicrosoft.com:636.

    > [!IMPORTANT]
    > Adja meg a teljes URL-CÍMÉT, például "ldaps: / /" és a portszám (: 636-os).

Az alábbi képernyőfelvételen a konfigurációkat az Azure Portalon:

   ![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>További lépések
* Hive-házirendek konfigurálása és Hive-lekérdezések futtatása: [Hive-házirendek konfigurálása a HDInsight-fürtök ESP](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozhat a HDInsight-fürtök ESP, lásd: [az SSH használata a Linux-based Hadoop on HDInsight Linux, Unix vagy OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

