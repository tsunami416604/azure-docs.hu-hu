---
title: Vállalati biztonsági csomag egy HDInsight-fürt konfigurálása az Azure AD-DS-sel
description: Megtudhatja, hogyan állíthatja be, és a HDInsight vállalati biztonsági csomag fürt konfigurálása Azure Active Directory Domain Services használatával.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 10/3/2018
ms.openlocfilehash: 84ee24b9002237d0993a30190944dbd6dd190ac8
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48784939"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>A HDInsight-fürt konfigurálása a vállalati biztonsági csomaggal az Azure Active Directory Domain Services használatával

Vállalati biztonsági csomag (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. ESP HDInsight-fürtöket, hogy a tartományi felhasználók tartományi hitelesítő adataik használatával a fürtökkel hitelesítéséhez és a big data-feladatokat futtatni egy tartományhoz csatlakozik. 

Ebből a cikkből elsajátíthatja egy HDInsight-fürt konfigurálása ESP az Azure Active Directory Domain Services (Azure AD DS) használatával.

>[!NOTE]
>ESP általánosan elérhető a HDI 3.6 a Spark, interaktív és Hadoop. A HBase és a Kafka fürttípusok ESP az előzetes verzióban.

## <a name="enable-azure-ad-ds"></a>Engedélyezze az Azure AD-DS-ben

Azure ad-Tartományi engedélyezése előfeltétele az ESP használata egy HDInsight-fürt létrehozása előtt. További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Ha engedélyezve van az Azure AD-Tartományi, minden felhasználó és objektumok indítása a szinkronizálása az Azure Active Directoryból az Azure AD-DS alapértelmezés szerint. Az objektumok száma az Azure ad-ben a szinkronizálási műveletet hossza függ. A szinkronizálás eltarthat pár nappal a több száz, több ezer objektumot tartalmaz. 

Ügyfelek kiválaszthatják a szinkronizálás csak a csoportokat, amelyek a HDInsight-fürtökbe való hozzáférésre van szükségük. Ez a beállítás csak bizonyos csoportokat a szinkronizálás nevezzük *szinkronizálás hatóköre*. Lásd: [konfigurálása hatókörrel rendelkező Azure AD-ből a felügyelt tartományhoz való szinkronizálás](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-scoped-synchronization) útmutatást.

Miután engedélyezte az Azure AD-Tartományi, egy helyi tartomány neve szolgáltatás (DNS) kiszolgáló fut, az AD virtuális gépeken (VM). Az Azure AD-Tartományi Virtual Network (VNET) ezen egyéni DNS-kiszolgálók használatára konfigurálja. Keresse meg a megfelelő IP-címeket, válasszon **tulajdonságok** alatt a **kezelés** alatt felsorolt kategória és tekintse meg az IP-címek **IP-cím virtuális hálózaton**.

![Keresse meg az IP-címek a helyi DNS-kiszolgálók](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Kiválasztásával ezeket egyéni IP-címek használata az Azure AD-Tartományi virtuális hálózat DNS-kiszolgálók konfigurációjának módosítása **DNS-kiszolgálók** alatt a **beállítások** kategória. Kattintson a Tovább gombra a választógomb **egyéni**, az alábbi mezőben adja meg az első IP-címet, és kattintson a **mentése**. Adjon hozzá további IP-címeket használja ugyanazokat a lépéseket.

![A virtuális hálózat DNS-konfiguráció frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

> [!NOTE]
> Csak a bérlői rendszergazdák a jogosultság hozzon létre egy Azure AD-DS-példányt. A multi-factor authentication csak a fürthöz hozzáférő felhasználók számára le kell tiltani kell.

Secure LDAP engedélyezése, ha a tanúsítvány helyezze el a tartománynév a tulajdonos neve vagy a tulajdonos alternatív neveként. Például, ha a tartománynév *contoso.com*, ellenőrizze, hogy pontos neve létezik-e a tanúsítvány tulajdonos neve vagy a tulajdonos alternatív neve. További információkért lásd: [a felügyelt tartomány secure LDAP konfigurálása az Azure AD-DS a](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="check-azure-ad-ds-health-status"></a>Az Azure AD-DS állapot ellenőrzése

Megtekintheti az Azure Active Directory Domain Services állapotának **állapotfigyelő** alatt a **kezelés** kategória. Győződjön meg arról, az Azure AD-Tartományi zöld (fut) és a szinkronizálás befejeződött.

![Az Azure Active Directory Domain Services állapota](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="add-managed-identity"></a>Adja hozzá a felügyelt identitás

Ha még nincs ilyen, hozzon létre egy felhasználó által hozzárendelt felügyelt identitás. Lásd: [létrehozás, list, delete vagy egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások szerepkör hozzárendelése](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) útmutatást. 

A felügyelt identitás megkönnyítik a tartományi szolgáltatások műveleteket szolgál. Ez az identitás olvasása, létrehozása, módosítása és törlése a tartományi szolgáltatások műveletek, például a szervezeti egységek és a szolgáltatás elvek hozhat létre a HDInsight vállalati biztonsági csomag szükséges hozzáféréssel rendelkezik.

Miután engedélyezte az Azure AD-Tartományi, felhasználó által hozzárendelt felügyelt identitás létrehozása, majd rendelje hozzá a **HDInsight Domain Services közreműködői** szerepkört az Azure AD-Tartományi hozzáférés-vezérlés.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Egy felügyelt identitás hozzárendelése a **HDInsight Domain Services közreműködői** szerepkör biztosítja, hogy az identitás megfelelő hozzáféréssel az Azure Active Directory-tartományi bizonyos domain services műveletek végrehajtásához. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP-HDInsight-fürt létrehozása

A következő lépés, hogy a HDInsight-fürt létrehozása a engedélyezve van, az Azure AD-DS-sel ESP.

Az Azure AD-DS-példánya és a HDInsight-fürt is helyezni, az azonos Azure virtuális hálózat könnyebbé válik. Ha azok különböző virtuális hálózatokban lévő, virtuális hálózatok, hogy a HDInsight-beli virtuális gépek láthatók a tartományvezérlőre, és felveheti a tartományhoz kell társviszonyt. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md). Annak tesztelése, ha a társviszony-létesítés helyesen végezték el, csatlakoztatni egy virtuális Gépet a HDInsight virtuális hálózat/alhálózat, és pingelni a tartománynév, vagy futtassa **ldp.exe** Azure Active Directory-tartományi szolgáltatások eléréséhez.

Miután a virtuális hálózatok társviszonyban állnak, konfigurálása a HDInsight VNET az egyéni DNS-kiszolgálót használ, és adjon meg az Azure AD-Tartományi magánhálózati IP-címek, a DNS-kiszolgáló címei. Ha mindkét virtuális hálózaton az azonos DNS-kiszolgálók, az egyéni tartomány nevét feloldja a megfelelő IP-címhez, és HDInsight elérhető lesz. Például ha a tartomány nevét a "contoso.com" majd elvégezte a lépést, pingelése "contoso.com" Azure AD-Tartományi IP jobb kell feloldhatónak lennie. Ez a tartomány majd csatlakoztatni egy virtuális Gépet.

![Egyéni DNS-kiszolgálók beállítása a társított virtuális hálózaton](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Amikor létrehoz egy HDInsight-fürtöt, vállalati biztonsági csomag egyéni lapján engedélyezheti.

![Az Azure HDInsight biztonsági és hálózatkezelési](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-security-networking.png)

Miután engedélyezte a ESP, az Azure AD-Tartományi kapcsolatos gyakori konfigurációs hibáinak lehet automatikusan észlelte és érvényesítve.

![Az Azure HDInsight vállalati biztonsági csomag tartomány ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Korai észlelését időt takaríthat meg azáltal, hogy a fürt létrehozása előtt javítsa a hibákat.

![Az Azure HDInsight vállalati biztonsági csomag nem sikerült ellenőrizni a tartományt](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP egy HDInsight-fürtöt hoz létre, amikor meg kell adnia a következő paraméterekkel:

- **Fürt rendszergazdai felhasználói**: válassza ki a fürt rendszergazdája, a szinkronizált Azure AD-DS-ben. Ez a fiók a szinkronizált és elérhető az Azure AD-Tartományi már kell lennie.

- **A fürt hozzáféréscsoportokat**: A biztonsági csoportok segít a felhasználóknak a fürthöz szinkronizálni szeretné az Azure AD-Tartományi elérhetőnek kell lennie. Ha például HiveUsers csoport. További információkért lásd: [hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL-cím**: Példa ldaps://contoso.com:636.

Az alábbi képernyőfelvételen egy sikeres konfigurálása az Azure Portalon:

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A létrehozott felügyelt identitás lehet kiválasztani az a felhasználó által hozzárendelt felügyelt identitás legördülő listából új fürt létrehozása során.

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).


## <a name="next-steps"></a>További lépések
* Hive-házirendek konfigurálása és Hive-lekérdezések futtatása: [Hive-házirendek konfigurálása a HDInsight-fürtök ESP](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozhat a HDInsight-fürtök ESP, lásd: [az SSH használata a Linux-based Hadoop on HDInsight Linux, Unix vagy OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
