---
title: Enterprise Security Package Azure Active Directory a HDInsight
description: Ismerje meg, hogyan állíthat be és konfigurálhat egy HDInsight Enterprise Security Package-fürtöt a Azure Active Directory Domain Services használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 5989aca2b577621c31fe486877ea006cb25d47b5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030352"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfigurációk Enterprise Security Package Azure Active Directory Domain Services a HDInsight-ben

A Enterprise Security Package (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. Az ESP-vel rendelkező HDInsight-fürtök egy tartományhoz csatlakoznak, így a tartományi felhasználók a fürthöz való hitelesítéshez és big data feladatok futtatásához használhatják a tartományi hitelesítő adataikat.

Ebből a cikkből megtudhatja, hogyan konfigurálhat HDInsight-fürtöt ESP-vel Azure Active Directory Domain Services (Azure AD-DS) használatával.

> [!NOTE]  
> Az ESP általánosan elérhető a HDInsight 3,6-es és a 4,0-es verzióban a fürtök típusaihoz: Apache Spark, interaktív, Hadoop és HBase. Az Apache Kafka-fürthöz tartozó ESP előzetes verzióban érhető el, és csak a lehető legjobb támogatást nyújtja. Az ESP GA dátum előtt létrehozott ESP-fürtök (2018. október 1.) nem támogatottak.

## <a name="enable-azure-ad-ds"></a>Az Azure AD-DS engedélyezése

> [!NOTE]  
> Csak a bérlői rendszergazdák rendelkeznek az Azure AD-DS engedélyezéséhez szükséges jogosultságokkal. Ha a fürt tárterülete Azure Data Lake Storage (ADLS) Gen1 vagy Gen2, le kell tiltania a Multi-Factor Authentication (MFA) csak azokra a felhasználókra, akiknek alapvető Kerberos-hitelesítéssel kell hozzáférnie a fürthöz. A [megbízható IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) -címek vagy a [feltételes hozzáférés](../../active-directory/conditional-access/overview.md) használatával letilthatja az MFA-t adott felhasználók számára, ha a HDInsight-fürt VNET IP-tartományhoz férnek hozzá. Ha feltételes hozzáférést használ, győződjön meg arról, hogy az AD Service-végpont engedélyezve van a HDInsight VNET.
>
> Ha a fürt tárterülete Azure Blob Storage (WASB), ne tiltsa le az MFA-t.

A AzureAD engedélyezése – a DS előfeltétele, mielőtt HDInsight-fürtöt hozna létre az ESP használatával. További információ: [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md). 

Ha az Azure AD-DS engedélyezve van, az összes felhasználó és objektum alapértelmezés szerint megkezdi a Azure Active Directory (HRE) és az Azure AD-DS közötti szinkronizálást. A szinkronizálási művelet hossza az Azure AD-ban található objektumok számától függ. A szinkronizálás eltarthat néhány napig több százezer objektum számára. 

Az Azure AD-DS szolgáltatásban használt tartománynévnek 39 karakter vagy kevesebbnek kell lennie, hogy működjön a HDInsight.

Dönthet úgy is, hogy csak azokat a csoportokat szinkronizálja, amelyeknek hozzáférésre van szüksége a HDInsight-fürtökhöz. Ezzel a beállítással csak bizonyos csoportok szinkronizálhatók, *hatókörön belüli szinkronizálásnak*nevezzük. Útmutatásért lásd: [hatókörön belüli szinkronizálás konfigurálása az Azure ad-ből a felügyelt tartományba](../../active-directory-domain-services/scoped-synchronization.md) .

A biztonságos LDAP engedélyezésekor a tulajdonos neve és a tulajdonos alternatív neve mezőbe helyezze a tartománynevet a tanúsítványban. Ha például a tartománynév *contoso100.onmicrosoft.com*, győződjön meg arról, hogy a pontos név létezik a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve mezőben. További információ: [Secure LDAP konfigurálása Azure AD-DS felügyelt tartományhoz](../../active-directory-domain-services/tutorial-configure-ldaps.md). Az alábbi példa egy önaláírt tanúsítványt hoz létre, és a tulajdonos neve és a DnsName (a tulajdonos alternatív neve) tartománynévvel (*contoso100.onmicrosoft.com*) rendelkezik:

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Az Azure AD-DS állapotának keresése
A Azure Active Directory Domain Services állapotának megtekintéséhez válassza az **állapot** lehetőséget a **kezelés** kategóriában. Győződjön meg arról, hogy az Azure AD-DS állapota zöld (fut), és a szinkronizálás befejeződött.

![Azure Active Directory Domain Services állapota](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Felügyelt identitás létrehozása és engedélyezése

Egy **felhasználó által hozzárendelt felügyelt identitás** használatával egyszerűsítheti és biztonságossá teheti a tartományi szolgáltatások műveleteit. Ha a HDInsight tartományi szolgáltatások közreműködői szerepkört rendeli hozzá a felügyelt identitáshoz, akkor az képes a tartományi szolgáltatások műveleteinek olvasására, létrehozására, módosítására és törlésére. Bizonyos tartományi szolgáltatási műveletek, például szervezeti egységek és egyszerű szolgáltatások létrehozása szükséges a HDInsight Enterprise Security Package. A felügyelt identitások bármelyik előfizetésben létrehozhatók. Az általános felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md). További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](../hdinsight-managed-identities.md).

Az ESP-fürtök beállításához hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyennel. További útmutatásért tekintse meg a [felhasználó által hozzárendelt felügyelt Azure Portal identitáshoz tartozó szerepkör létrehozása, listázása, törlése vagy hozzárendelése](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) című témakört. Ezután rendelje hozzá a **HDInsight tartományi szolgáltatások közreműködői** szerepkört a felügyelt identitáshoz az Azure AD-DS hozzáférés-vezérlés (HRE-DS rendszergazdai jogosultságok szükségesek ennek a szerepkör-hozzárendelésnek a végrehajtásához).

![Azure Active Directory Domain Services hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A **HDInsight tartományi szolgáltatások közreműködői** szerepkörének hozzárendelésével biztosíthatja, hogy az identitás megfelelő (nevében) hozzáférést biztosítson a tartományi szolgáltatások műveleteinek elvégzéséhez, például szervezeti egységek létrehozásához, szervezeti egységek törléséhez, stb. a HRE-DS tartományon.

Miután létrehozta a felügyelt identitást, és a megfelelő szerepkört adta, a HRE-DS-rendszergazda beállíthatja, hogy ki használhatja ezt a felügyelt identitást. A felügyelt identitáshoz tartozó felhasználók beállításához a rendszergazdának ki kell választania a felügyelt identitást a portálon, majd az **Áttekintés**területen kattintson az **Access Control (iam)** elemre. Ezután a jobb oldalon rendelje hozzá a **felügyelt identitás operátori** szerepkört azokhoz a felhasználókhoz vagy csoportokhoz, amelyek HDInsight ESP-fürtöket kíván létrehozni. Például a HRE-DS rendszergazda hozzárendelheti ezt a szerepkört a **sjmsi** által felügyelt identitás **MarketingTeam** csoportjához az alábbi képen látható módon. Ezzel biztosíthatja, hogy a szervezet megfelelő tagjai hozzáférhessenek a felügyelt identitáshoz az ESP-fürtök létrehozása céljából.

![HDInsight felügyelt identitás-kezelő szerepkör-hozzárendelés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Hálózati megfontolások

> [!NOTE]  
> Az Azure AD-DS-t egy Azure Resource Manager-alapú vNET kell telepíteni. A klasszikus virtuális hálózatok nem támogatottak az Azure AD-DS-ben. További információ: [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md#create-and-configure-the-virtual-network).

Az Azure AD-DS engedélyezése után a helyi tartománynév-szolgáltatási (DNS) kiszolgáló fut az AD Virtual Machineson (VM). Konfigurálja az Azure AD-DS Virtual Network (VNET) az egyéni DNS-kiszolgálók használatára. A megfelelő IP-címek megkereséséhez válassza a **kezelés** kategória **Tulajdonságok** elemét, és tekintse meg a **Virtual Network IP-címe**alatt felsorolt IP-címeket.

![Helyi DNS-kiszolgálók IP-címeinek megkeresése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Módosítsa az Azure AD-DS VNET található DNS-kiszolgálók konfigurációját úgy, hogy ezeket az egyéni IP-címeket használják a **Beállítások** kategóriába tartozó **DNS-kiszolgálók** kiválasztásával. Ezután kattintson az **Egyéni**elem melletti választógombra, adja meg az első IP-címet az alábbi szövegmezőben, majd kattintson a **Save (Mentés**) gombra. További IP-címeket is hozzáadhat ugyanezekkel a lépésekkel.

![A VNET DNS-konfigurációjának frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Az Azure AD-DS-példány és a HDInsight-fürt is egyszerűbben helyezhető el ugyanabban az Azure-beli virtuális hálózaton. Ha különböző virtuális hálózatok-ket kíván használni, ezeket a virtuális hálózatokat egyenrangúként kell megadnia, hogy a tartományvezérlőt a HDI virtuális gépek láthassák. További információ: [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md). 

A virtuális hálózatok társítása után konfigurálja a HDInsight VNET egyéni DNS-kiszolgáló használatára, és adja meg az Azure AD-DS privát IP-címeket DNS-kiszolgálóként. Ha mindkét virtuális hálózatok ugyanazokat a DNS-kiszolgálókat használja, az egyéni tartománynevet a megfelelő IP-címhez fogja feloldani, és elérhető lesz a HDInsight. Ha például a tartománynév `contoso.com`, akkor a lépés után a `ping contoso.com` megoldásnak a megfelelő Azure AD-DS IP-címen kell feloldania.

![Egyéni DNS-kiszolgálók konfigurálása a társ VNET](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Ha hálózati biztonsági csoportokat (NSG) használ a HDInsight-alhálózatban, engedélyeznie kell a [szükséges IP-címeket](../hdinsight-management-ip-addresses.md) a bejövő és a kimenő forgalom számára is.

Ha **szeretné ellenőrizni, hogy** a hálózat megfelelően van-e beállítva, csatlakoztasson egy Windows rendszerű virtuális gépet a HDINSIGHT-VNET/alhálózathoz, és Pingelje a tartománynevet (az IP-címen kell megoldania), majd futtassa az **Ldp. exe fájlt** az Azure AD-DS tartomány eléréséhez. **Ezt követően csatlakoztassa a Windows rendszerű virtuális gépet a tartományhoz, és győződjön meg** arról, hogy az összes szükséges RPC-hívás sikeres volt az ügyfél és a kiszolgáló között. Az **nslookup** használatával megerősítheti a hálózati hozzáférést a Storage-fiókhoz vagy az esetlegesen használt külső adatbázisokhoz (például külső Hive-metaadattár vagy a Ranger db).
Győződjön meg arról, hogy az összes [szükséges port](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) engedélyezett a HRE-DS alhálózati biztonsági csoport szabályaiban, ha az HRE-DS-t egy NSG védi. Ha a Windows rendszerű virtuális gép tartományhoz való csatlakozása sikeres, folytassa a következő lépéssel, és hozzon létre ESP-fürtöket.

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP-vel rendelkező HDInsight-fürt létrehozása

Az előző lépések helyes beállítása után a következő lépés a HDInsight-fürt létrehozása az ESP-vel engedélyezve. HDInsight-fürt létrehozásakor engedélyezheti a Enterprise Security Package a **Biztonság és hálózat** lapon. Ha az üzembe helyezéshez inkább Azure Resource Manager sablont szeretne használni, használja a portált egyszer, és töltse le az előre kitöltött sablont a **felülvizsgálat + létrehozás** oldalon a későbbi újrafelhasználáshoz.

> [!NOTE]  
> Az ESP-fürtök nevének első hat karakterének egyedinek kell lennie a környezetben. Ha például több ESP-fürttel rendelkezik különböző virtuális hálózatok, akkor olyan elnevezési convension kell választania, amely biztosítja, hogy a fürt neveinek első hat karaktere egyedi legyen.

![Azure HDInsight Enterprise biztonsági csomag tartományának ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Miután engedélyezte az ESP-t, a rendszer automatikusan észleli és ellenőrzi az Azure AD-DS-vel kapcsolatos gyakori konfigurációs beállításokat. A hibák kijavítása után folytassa a következő lépéssel:

![Az Azure HDInsight Enterprise biztonsági csomagja nem sikerült a tartomány érvényesítése](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Ha ESP-vel rendelkező HDInsight-fürtöt hoz létre, a következő paramétereket kell megadnia:

- **Fürt rendszergazdai felhasználója**: A szinkronizált Azure AD-DS-ből válasszon ki egy rendszergazdát a fürthöz. A tartományi fióknak már szinkronizálva kell lennie, és elérhetőnek kell lennie az Azure AD-DS-ben.

- **Fürthöz való hozzáférési csoportok**: Azok a biztonsági csoportok, amelyekhez szinkronizálni szeretné a felhasználókat, és amelyek hozzáférhetnek a fürthöz, elérhetőnek kell lenniük az Azure AD-DS-ben. Például HiveUsers csoport. További információ: [csoport létrehozása és Tagok hozzáadása Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL-CÍM**: Például: `ldaps://contoso.com:636`.

A létrehozott felügyelt identitás az új fürt létrehozásakor a felhasználó által hozzárendelt felügyelt identitás legördülő menüjéből választható ki.

![Azure HDInsight ESP Active Directory tartományi szolgáltatások felügyelt identitás](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>További lépések

* A kaptár-házirendek konfigurálásához és a kaptár-lekérdezések futtatásához lásd: [Apache Hive házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md).
* Az SSH-val az ESP-vel való HDInsight-fürtökhöz való kapcsolódáshoz lásd: az [SSH használata Linux-alapú Apache Hadoop Linux, UNIX vagy OS X rendszerű HDInsight használatával](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
