---
title: Nagyvállalati biztonság az Azure AD DS-ban – Azure HDInsight
description: Ismerje meg, hogyan állíthat be és konfigurálhat egy HDInsight Enterprise Security Package-fürtöt a Azure Active Directory Domain Services használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2b4756990162817087b0904a764b97526c3545d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186651"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfigurációk Enterprise Security Package Azure Active Directory Domain Services a HDInsight-ben

A Enterprise Security Package (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. Az ESP-vel rendelkező HDInsight-fürtök tartományhoz csatlakoznak. Ez a szolgáltatás lehetővé teszi, hogy a tartományi felhasználók tartományi hitelesítő adataikat használják a fürtök hitelesítéséhez és big data feladatok futtatásához.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy HDInsight-fürtöt ESP használatával Azure Active Directory Domain Services (Azure AD DS) használatával.

> [!NOTE]  
> Az ESP általánosan elérhető a HDInsight 3,6-es és 4,0-as verziójában a következő típusú fürtökhöz: Apache Spark, Interactive, Hadoop és HBase. A Apache Kafka-fürthöz tartozó ESP csak előzetes verzióban érhető el, csak a legjobb támogatással. Az ESP GA dátum előtt létrehozott ESP-fürtök (2018. október 1.) nem támogatottak.

## <a name="enable-azure-ad-ds"></a>Az Azure AD tartományi szolgáltatások engedélyezése

> [!NOTE]  
> Csak a bérlői rendszergazdák rendelkeznek az Azure AD DS engedélyezéséhez szükséges jogosultságokkal. Ha a fürt tárterülete Azure Data Lake Storage Gen1 vagy Gen2, le kell tiltania az Azure Multi-Factor Authentication csak azokra a felhasználókra, akiknek egyszerű Kerberos-hitelesítéssel kell elérniük a fürtöt.
>
> A [megbízható IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) -címek vagy a [feltételes hozzáférés](../../active-directory/conditional-access/overview.md) használatával letilthatja az egyes felhasználók multi-Factor Authenticationét *csak* akkor, ha a HDInsight-fürt virtuális hálózatának IP-tartományát érik el. Ha feltételes hozzáférést használ, győződjön meg arról, hogy a Active Directory szolgáltatás végpontja engedélyezve van a HDInsight virtuális hálózaton.
>
> Ha a fürt tárterülete Azure Blob Storage, ne tiltsa le a Multi-Factor Authentication.

Az Azure AD DS előfeltétele, hogy HDInsight-fürtöt hozzon létre az ESP használatával. További információ: [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md).

Ha az Azure AD DS engedélyezve van, az összes felhasználó és objektum alapértelmezés szerint megkezdi a Azure Active Directory (Azure AD) és az Azure AD DS közötti szinkronizálást. A szinkronizálási művelet hossza az Azure AD-ban található objektumok számától függ. A szinkronizálás eltarthat néhány napig több százezer objektum esetében is.

Az Azure AD DS használt tartománynévnek 39 karakter vagy kevesebbnek kell lennie, hogy működjön a HDInsight.

Dönthet úgy is, hogy csak azokat a csoportokat szinkronizálja, amelyeknek hozzáférésre van szüksége a HDInsight-fürtökhöz. Ezzel a beállítással csak bizonyos csoportok szinkronizálhatók, *hatókörön belüli szinkronizálásnak*nevezzük. Útmutatásért lásd: [hatókörön belüli szinkronizálás konfigurálása az Azure ad-ből a felügyelt tartományba](../../active-directory-domain-services/scoped-synchronization.md).

A biztonságos LDAP engedélyezésekor a tartománynevet a tulajdonos nevében helyezze el. És a tulajdonos alternatív neve a tanúsítványban. Ha a tartományneve *contoso100.onmicrosoft.com*, győződjön meg arról, hogy a pontos név létezik a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve mezőben. További információ: [biztonságos LDAP konfigurálása Azure AD DS felügyelt tartományhoz](../../active-directory-domain-services/tutorial-configure-ldaps.md).

A következő példa létrehoz egy önaláírt tanúsítványt. A *contoso100.onmicrosoft.com* tartománynév a `Subject` következő: (tulajdonos neve) és `DnsName` (a tulajdonos alternatív neve).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Az Azure AD DS állapotának keresése

Azure Active Directory Domain Services állapotának megtekintése a **kezelés** kategória **állapot** parancsával. Győződjön meg arról, hogy az Azure AD DS állapota zöld (fut), és a szinkronizálás befejeződött.

![Azure AD DS állapot](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Felügyelt identitás létrehozása és engedélyezése

A biztonságos tartományi szolgáltatások műveleteinek egyszerűbbé tételéhez használjon *felhasználó által hozzárendelt felügyelt identitást* . Ha a **HDInsight tartományi szolgáltatások közreműködői** szerepkört rendeli hozzá a felügyelt identitáshoz, akkor az képes a tartományi szolgáltatások műveleteinek olvasására, létrehozására, módosítására és törlésére.

Bizonyos tartományi szolgáltatási műveletek, például szervezeti egységek és egyszerű szolgáltatások létrehozása szükséges a HDInsight Enterprise Security Packageához. Bármely előfizetésben létrehozhat felügyelt identitásokat. Az általános felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md). További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](../hdinsight-managed-identities.md).

Az ESP-fürtök beállításához hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyennel. Lásd [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md):.

Ezután rendelje hozzá a **HDInsight tartományi szolgáltatások közreműködői** szerepkört a felügyelt identitáshoz az Azure AD DS **hozzáférés-vezérlésében** . A szerepkör-hozzárendelés végrehajtásához Azure AD DS rendszergazdai jogosultságok szükségesek.

![Azure Active Directory Domain Services hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A **HDInsight tartományi szolgáltatások közreműködői** szerepkörének hozzárendelésével biztosíthatja, hogy ez az identitás`on behalf of`megfelelő () hozzáférést biztosítson az Azure AD DS tartományban található tartományi szolgáltatások műveleteihez. Ezek a műveletek magukban foglalják a szervezeti egységek létrehozását és törlését.

Miután a felügyelt identitás megkapta a szerepkört, az Azure AD DS rendszergazdája felügyeli, hogy ki használja. Először a rendszergazda kiválasztja a felügyelt identitást a portálon. Ezután kiválasztja a **Access Control (iam)** lehetőséget az **Áttekintés**területen. A rendszergazda hozzárendeli a **felügyelt identitás operátori** szerepkört azokhoz a felhasználókhoz vagy csoportokhoz, amelyek ESP-fürtöket kívánnak létrehozni.

Az Azure AD DS rendszergazdája például hozzárendelheti ezt a szerepkört a **sjmsi** által felügyelt identitás **MarketingTeam** csoportjához. Az alábbi képen egy példa látható. Ez a hozzárendelés biztosítja, hogy a szervezet megfelelő tagjai a felügyelt identitás használatával ESP-fürtöket hozzanak létre.

![HDInsight felügyelt identitás-kezelő szerepkör-hozzárendelés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Hálózati szempontok

> [!NOTE]  
> Az Azure AD DS Azure Resource Manager-alapú virtuális hálózaton kell telepíteni. A klasszikus virtuális hálózatok használata az Azure AD DS esetében nem támogatott. További információ: [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Az Azure AD DS engedélyezése. Ezután egy helyi tartománynévrendszer-(DNS-) kiszolgáló fut a Active Directory virtuális gépeken (VM). Konfigurálja az Azure AD DS Virtual Network szolgáltatást ezeknek az egyéni DNS-kiszolgálóknak a használatára. A megfelelő IP-címek megkereséséhez válassza a **kezelés** kategória **Tulajdonságok** elemét, és keresse meg **a virtuális hálózat IP-címe**elemét.

![Helyi DNS-kiszolgálók IP-címeinek megkeresése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Módosítsa a DNS-kiszolgálók konfigurációját az Azure AD DS Virtual Network szolgáltatásban. Ha ezeket az egyéni IP-címeket szeretné használni, válassza a **DNS-kiszolgálók** lehetőséget a **Beállítások** kategóriában. Ezután válassza az **Egyéni** lehetőséget, adja meg az első IP-címet a szövegmezőben, majd kattintson a **Mentés**gombra. Adja hozzá a további IP-címeket ugyanezen lépések végrehajtásával.

![A virtuális hálózat DNS-konfigurációjának frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Az Azure AD DS-példány és a HDInsight-fürt is egyszerűbben helyezhető el ugyanabban az Azure-beli virtuális hálózaton. Ha különböző virtuális hálózatokat kíván használni, ezeket a virtuális hálózatokat egyenrangúként kell megadnia, hogy a tartományvezérlő látható legyen a HDInsight virtuális gépek számára. További információ: [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md).

A virtuális hálózatok társítása után konfigurálja a HDInsight virtuális hálózatot egyéni DNS-kiszolgáló használatára. Adja meg az Azure AD DS magánhálózati IP-címeket a DNS-kiszolgáló címeiként. Ha mindkét virtuális hálózat ugyanazokat a DNS-kiszolgálókat használja, az egyéni tartománynevet a megfelelő IP-címhez fogja feloldani, és elérhető lesz a HDInsight. Ha például a tartománynév `contoso.com`, akkor ezt a lépést követően a megfelelő Azure AD DS `ping contoso.com` IP-címen kell feloldania.

![Egyéni DNS-kiszolgálók konfigurálása egy egyenrangú virtuális hálózathoz](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Ha hálózati biztonsági csoport (NSG) szabályokat használ a HDInsight-alhálózatban, engedélyezze a [szükséges IP-címeket](../hdinsight-management-ip-addresses.md) a bejövő és a kimenő forgalomhoz is.

A hálózat beállításának teszteléséhez csatlakoztassa a Windows rendszerű virtuális GÉPET a HDInsight virtuális hálózathoz/alhálózathoz, és Pingelje a tartománynevet. (Fel kell oldania egy IP-címet.) Futtassa az **Ldp. exe fájlt** az Azure AD DS tartomány eléréséhez. Ezt követően csatlakoztassa a Windows rendszerű virtuális gépet a tartományhoz, és győződjön meg arról, hogy az összes szükséges RPC-hívás sikeres volt az ügyfél és a kiszolgáló között.

Az **nslookup** használatával erősítse meg a Storage-fiókhoz való hálózati hozzáférést. Vagy bármely külső adatbázis, amelyet használhat (például külső Hive-metaadattár vagy Ranger DB). Gondoskodjon arról, hogy a [szükséges portok](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) engedélyezettek legyenek az Azure AD DS alhálózat NSG-szabályaiban, ha egy NSG az Azure AD DS védelmét biztosítja. Ha a Windows rendszerű virtuális gép tartományhoz való csatlakozása sikeres, folytassa a következő lépéssel, és hozzon létre ESP-fürtöket.

## <a name="create-an-hdinsight-cluster-with-esp"></a>ESP-vel rendelkező HDInsight-fürt létrehozása

Az előző lépések helyes beállítása után a következő lépés a HDInsight-fürt létrehozása az ESP-vel engedélyezve. HDInsight-fürt létrehozásakor engedélyezheti Enterprise Security Package a **Biztonság és hálózatkezelés** lapon. Azure Resource Manager-sablon üzembe helyezéséhez használja egyszer a portál élményét. Ezután töltse le az előre kitöltött sablont a **felülvizsgálat + létrehozás** oldalon a későbbi újrafelhasználáshoz.

A HDInsight-azonosító- [átvitelszervező](identity-broker.md) szolgáltatás a fürt létrehozása során is engedélyezhető. Az ID Broker funkció lehetővé teszi, hogy Multi-Factor Authentication használatával bejelentkezzen a Ambari-be, és a szükséges Kerberos-jegyek beszerzése nélkül, jelszó-kivonatok szükségesek az Azure AD DS.

> [!NOTE]  
> Az ESP-fürtök nevének első hat karakterének egyedinek kell lennie a környezetben. Ha például több ESP-fürttel rendelkezik különböző virtuális hálózatokban, válasszon egy elnevezési konvenciót, amely biztosítja, hogy a fürt neveinek első hat karaktere egyedi legyen.

![Tartomány-érvényesítés az Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Az ESP engedélyezése után a rendszer automatikusan észleli és ellenőrzi az Azure AD DShoz kapcsolódó gyakori helytelen konfigurációkat. A hibák elhárítása után folytassa a következő lépéssel.

![Az Azure HDInsight Enterprise Security Package sikertelen volt a tartomány ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Ha ESP-vel rendelkező HDInsight-fürtöt hoz létre, a következő paramétereket kell megadnia:

* **Fürt rendszergazdai felhasználója**: válasszon egy rendszergazdát a fürt számára a szinkronizált Azure AD DS-példányból. A tartományi fióknak már szinkronizálva kell lennie, és elérhetőnek kell lennie az Azure AD DSban.

* **Fürthöz való hozzáférési csoportok**: azok a biztonsági csoportok, amelyekhez szinkronizálni szeretné a felhasználókat, és amelyek hozzáférhetnek a fürthöz, elérhetőnek kell lenniük az Azure ad DSban. Ilyen például a HiveUsers csoport. További információ: [csoport létrehozása és Tagok hozzáadása Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **LDAPS URL-cím**: példa `ldaps://contoso.com:636`.

A létrehozott felügyelt identitás kiválasztható a **felhasználó által hozzárendelt felügyelt identitás** legördülő listából, ha új fürtöt hoz létre.

![Azure HDInsight ESP Active Directory tartományi szolgáltatások felügyelt identitás](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>További lépések

* A kaptár-házirendek konfigurálásához és a kaptár-lekérdezések futtatásához lásd: [Apache Hive házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md).
* Az SSH-val az ESP-vel való HDInsight-fürtökhöz való kapcsolódáshoz lásd: az [SSH használata Linux-alapú Apache Hadoop Linux, UNIX vagy OS X rendszerű HDInsight használatával](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
