---
title: Nagyvállalati biztonság az Azure AD DS-szel – Azure HDInsight
description: Megtudhatja, hogy miként állíthat be és konfigurálhat HDInsight Enterprise Security Package fürtöt az Azure Active Directory tartományi szolgáltatások használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/17/2020
ms.openlocfilehash: c045378b6e69a9bb1b696d3390dadf84a50bd3b7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687273"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Vállalati biztonsági csomag konfigurációi az Azure Active Directory tartományi szolgáltatásokkal a HDInsightban

Az Enterprise Security Package (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökhöz. Az ESP-vel rendelkező HDInsight-fürtök tartományhoz csatlakoznak. Ez a kapcsolat lehetővé teszi a tartományi felhasználók számára, hogy a tartományi hitelesítő adataikkal hitelesítsék magukat a fürtökkel, és big data-feladatokat futtassanak.

Ebben a cikkben megtudhatja, hogyan konfigurálhat egy HDInsight-fürt ESP-vel az Azure Active Directory tartományi szolgáltatások (Azure AD DS) használatával.

> [!NOTE]  
> Az ESP általában a HDInsight 3.6-os és 4.0-s verzióiban érhető el a következő fürttípusokhoz: Apache Spark, Interactive, Hadoop és HBase. Az Apache Kafka fürttípus ESP-verziója előzetes verzióban érhető el, csak a legjobb támogatást nyújtva. Az ESP ga dátuma (2018. október 1.) előtt létrehozott ESP-fürtök nem támogatottak.

## <a name="enable-azure-ad-ds"></a>Az Azure AD tartományi szolgáltatások engedélyezése

> [!NOTE]  
> Csak a bérlői rendszergazdák rendelkeznek az Azure AD DS engedélyezéséhez. Ha a fürttároló Az Azure Data Lake Storage Gen1 vagy Gen2, csak az on-t ido szintű Kerberos-hitelesítéssel kell letiltania az Azure többtényezős hitelesítést.
>
> [A megbízható IP-címekkel](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) vagy [a feltételes hozzáféréssel](../../active-directory/conditional-access/overview.md) *csak* akkor tilthatja le a többtényezős hitelesítést bizonyos felhasználók számára, ha a HDInsight-fürt virtuális hálózatának IP-tartományához férnek hozzá. Feltételes hozzáférés használata esetén győződjön meg arról, hogy az Active Directory szolgáltatás végpontja engedélyezve van a HDInsight virtuális hálózaton.
>
> Ha a fürttároló Az Azure Blob storage, ne tiltsa le a többtényezős hitelesítést.

Az Azure AD DS engedélyezése előfeltétele, mielőtt létrehozhat egy HDInsight-fürtet ESP-vel. További információ: [Az Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md)című témakörben talál.

Ha az Azure AD DS engedélyezve van, az összes felhasználó és objektum alapértelmezés szerint szinkronizálni kezd az Azure Active Directoryból (Azure AD) az Azure AD DS szolgáltatásba. A szinkronizálási művelet időtartama az Azure AD-ben lévő objektumok számától függ. A szinkronizálás több százezer objektum esetében néhány napig is eltarthat.

Az Azure AD DS-hez használt tartománynévnek legkevesebb karakterből kell lennie a HDInsight használatához.

Választhat, hogy csak azokat a csoportokat szinkronizálja, amelyeknek szükségük van a HDInsight-fürtökhöz. Ezt a lehetőséget, hogy csak bizonyos csoportokat szinkronizáljon, *hatókörrel szinkronizált szinkronizálásnak nevezzük.* További információt a [Hatókörrel kapcsolatos szinkronizálás konfigurálása az Azure AD-ről a felügyelt tartományba című témakörben talál.](../../active-directory-domain-services/scoped-synchronization.md)

Ha engedélyezi a biztonságos LDAP-t, tegye a tartománynevet a tulajdonos nevébe. És a tulajdonos alternatív neve a tanúsítványban. Ha a tartománynév *contoso100.onmicrosoft.com*, győződjön meg arról, hogy a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve megtalálható. További információ: [Biztonságos LDAP konfigurálása Azure AD DS felügyelt tartományhoz.](../../active-directory-domain-services/tutorial-configure-ldaps.md)

A következő példa önaláírt tanúsítványt hoz létre. A contoso100.onmicrosoft.com *tartománynév* `Subject` (tulajdonosnév) `DnsName` és (tulajdonos alternatív neve) is szerepel.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Az Azure AD DS állapotának ellenőrzése

Tekintse meg az Azure Active Directory tartományi szolgáltatások állapotát a Kezelés kategória **Állapot** **parancsára** kattintva. Győződjön meg arról, hogy az Azure AD DS állapota zöld (futó) és a szinkronizálás befejeződött.

![Az Azure AD DS állapota](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Felügyelt identitás létrehozása és engedélyezése

A biztonságos tartományi szolgáltatások műveleteinek egyszerűsítéséhez használjon *felhasználó által hozzárendelt felügyelt identitást.* Amikor hozzárendeli a **HDInsight tartományi szolgáltatások közreműködői** szerepkört a felügyelt identitáshoz, képes olvasni, létrehozni, módosítani és törölni a tartományi szolgáltatások műveleteit.

Bizonyos tartományi szolgáltatási műveletek, például a korvízek és a szolgáltatásegyszerű szolgáltatások létrehozása szükséges a HDInsight Enterprise Security Package számára. Felügyelt identitások bármely előfizetésben létrehozhat. A felügyelt identitások általánosságban további információkért lásd: [Felügyelt identitások az Azure-erőforrásokhoz.](../../active-directory/managed-identities-azure-resources/overview.md) A felügyelt identitások Azure HDInsightban való működéséről további információt az [Azure HDInsight felügyelt identitásai című témakörben talál.](../hdinsight-managed-identities.md)

Esp-fürtök beállításához hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyentel. Lásd: [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Ezután rendelje hozzá a **HDInsight tartományi szolgáltatások közreműködői** szerepkört az Azure AD DS **hozzáférés-vezérlésének** felügyelt identitásához. Ehhez a szerepkör-hozzárendeléshez szüksége van az Azure AD DS felügyeleti jogosultságaira.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A **HDInsight tartományi szolgáltatások közreműködői** szerepkör hozzárendelése`on behalf of`biztosítja, hogy ez az identitás megfelelő ( ) hozzáféréssel rendelkezik az Azure AD DS-tartomány tartományi szolgáltatások műveleteihez. Ezek a műveletek közé tartozik a megfelelőségi műveletek létrehozása és törlése.

Miután a felügyelt identitás megkapta a szerepkört, az Azure AD DS-rendszergazda kezeli, aki használja. Először a rendszergazda kiválasztja a felügyelt identitást a portálon. Ezután az **Áttekintés**csoportban válassza a **hozzáférés-vezérlést (IAM)** . A rendszergazda hozzárendeli a **felügyelt identitásoperátor** szerepkört azokhoz a felhasználókhoz vagy csoportokhoz, amelyek ESP-fürtöket szeretnének létrehozni.

Például az Azure AD DS-rendszergazda hozzárendelheti ezt a szerepkört a **MarketingTeam** csoporthoz az **sjmsi** felügyelt identitáshoz. Egy példa látható az alábbi képen. Ez a hozzárendelés biztosítja, hogy a szervezet megfelelő felhasználói a felügyelt identitás használatával esp-fürtöket hozzanak létre.

![HDInsight felügyelt identitáskezelő szerepkör-hozzárendelése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Hálózati szempontok

> [!NOTE]  
> Az Azure AD DS-t telepíteni kell egy Azure Resource Manager-alapú virtuális hálózatban. Klasszikus virtuális hálózatok nem támogatottak az Azure AD DS. További információ: [Az Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)című témakörben talál.

Engedélyezze az Azure AD DS szolgáltatást. Ezután egy helyi DNS-kiszolgáló fut az Active Directory virtuális gépeken. Konfigurálja az Azure AD DS virtuális hálózatot az egyéni DNS-kiszolgálók használatára. A megfelelő IP-címek megkereséséhez válassza a **Tulajdonságok lehetőséget** a **Kezelés** kategóriában, és tekintse meg **az IP-cím a virtuális hálózaton**területen területen.

![Helyi DNS-kiszolgálók IP-címeinek megkeresése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Módosítsa a DNS-kiszolgálók konfigurációját az Azure AD DS virtuális hálózatában. Az egyéni IP-k használatához válassza a **DNS-kiszolgálókat** a **Beállítások** kategóriában. Ezután válassza az **Egyéni** lehetőséget, írja be az első IP-címet a szövegmezőbe, és válassza a **Mentés gombot.** Adjon hozzá további IP-címeket ugyanezen lépésekkel.

![A virtuális hálózati DNS-konfiguráció frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Az Azure AD DS-példányt és a HDInsight-fürtöt is könnyebben helyezheti el ugyanabban az Azure virtuális hálózatban. Ha különböző virtuális hálózatokat kíván használni, a virtuális hálózatokat társítania kell, hogy a tartományvezérlő látható legyen a HDInsight virtuális gépek számára. További információt a [Virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md)című témakörben talál.

A virtuális hálózatok társviszonyba kötése után konfigurálja úgy a HDInsight virtuális hálózatot, hogy egyéni DNS-kiszolgálót használjon. És adja meg az Azure AD DS privát IP-címek, mint a DNS-kiszolgáló címét. Ha mindkét virtuális hálózat ugyanazt a DNS-kiszolgálót használja, az egyéni tartománynév a megfelelő IP-címre oldódik fel, és a HDInsight ból érhető el. Ha például a tartománynév `contoso.com`, majd `ping contoso.com` ezt a lépést követően fel kell oldania a megfelelő Azure AD DS IP-cím.

![Egyéni DNS-kiszolgálók konfigurálása társviszony-létesített virtuális hálózathoz](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Ha a HDInsight-alhálózatban hálózati biztonsági csoport (NSG) szabályokat használ, engedélyeznie kell a [szükséges IP-ket](../hdinsight-management-ip-addresses.md) mind a bejövő, mind a kimenő forgalomhoz.

A hálózati telepítés teszteléséhez csatlakozzon egy Windows virtuális géphez a HDInsight virtuális hálózathoz/alhálózathoz, és pingelje a tartománynevet. (Meg kell oldani, hogy egy IP.) Az **ldp.exe futtatásával** elérheti az Azure AD DS-tartományt. Ezután csatlakoztassa ezt a Windows virtuális gép a tartományhoz annak megerősítéséhez, hogy az összes szükséges RPC-hívások sikeresek az ügyfél és a kiszolgáló között.

Az **nslookup** használatával erősítse meg a tárfiókhoz való hálózati hozzáférést. Vagy bármely külső adatbázis, amely lehet használni (például külső Hive metastore vagy Ranger DB). Győződjön meg [arról,](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) hogy a szükséges portok engedélyezettek az Azure AD DS-alhálózat NSG-szabályaiban, ha egy NSG biztonságossá teszi az Azure AD DS-t. Ha a Windows virtuális géphez való tartományhoz való csatlakozás sikeres, akkor folytathatja a következő lépéssel, és létrejöhet ESP-fürtöket.

## <a name="create-an-hdinsight-cluster-with-esp"></a>HDInsight-fürt létrehozása ESP-vel

Miután beállította az előző lépéseket helyesen, a következő lépés a HDInsight-fürt létrehozása esp engedélyezve van. HDInsight-fürt létrehozásakor a **Biztonság + hálózat** lapon engedélyezheti a vállalati biztonsági csomagot. Az Azure Resource Manager-sablon üzembe helyezéshez, használja a portál felületét egyszer. Ezután töltse le az előre kitöltött sablont a **Véleményezés + létrehozás** oldalon a későbbi újrafelhasználáshoz.

A [HDInsight ID Broker](identity-broker.md) szolgáltatást is engedélyezheti a fürt létrehozása során. Az ID Broker funkció lehetővé teszi, hogy jelentkezzen be Ambari segítségével többtényezős hitelesítés és a szükséges Kerberos-jegyek beszerezni anélkül, hogy jelszó kibontások az Azure AD DS-ben.

> [!NOTE]  
> Az ESP-fürtnevek első hat karakterének egyedinek kell lennie a környezetben. Ha például több ESP-fürtje van különböző virtuális hálózatokban, válasszon egy elnevezési konvenciót, amely biztosítja, hogy a fürtnevek első hat karaktere egyedi legyen.

![Tartományérvényesítés az Azure HDInsight enterprise biztonsági csomaghoz](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Az ESP engedélyezése után az Azure AD DS-hez kapcsolódó gyakori helytelen konfigurációkat a rendszer automatikusan észleli és érvényesíti. A hibák kijavítása után folytathatja a következő lépéssel.

![Az Azure HDInsight enterprise biztonsági csomag nem sikerült a tartomány érvényesítésén](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Amikor ESP-vel hoz létre egy HDInsight-fürtöt, a következő paramétereket kell megadnia:

* **Fürtfelügyeleti felhasználó:** Válasszon egy rendszergazdát a fürthöz a szinkronizált Azure AD DS-példányból. Ezt a tartományi fiókot már szinkronizálni kell, és elérhetőnek kell lennie az Azure AD DS-ben.

* **Fürthozzáférési csoportok:** Azoknak a biztonsági csoportoknak, amelyek felhasználóit szinkronizálni szeretné, és hozzáférhetnek a fürthöz, elérhetőnek kell lenniük az Azure AD DS-ben. Egy példa a HiveUsers csoport. További információ: [Csoport létrehozása és tagok hozzáadása az Azure Active Directoryban című témakörben.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

* **LDAPS URL**: `ldaps://contoso.com:636`Erre példa a .

A létrehozott felügyelt identitás új fürt létrehozásakor a **Felhasználó által hozzárendelt felügyelt identitás** legördülő listából választható ki.

![Az Azure HDInsight ESP Active Directory tartományi szolgáltatások felügyelt identitása](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>További lépések

* A Hive-házirendek konfigurálása és a Hive-lekérdezések futtatása az [Apache Hive-házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md)című témakörben.
* Ha az SSH-t az ESP-vel rendelkező HDInsight-fürtökhöz való csatlakozáshoz használja, olvassa el az [SSH használata Linux alapú Apache Hadoop használatával HDInsight-on Linux, Unix vagy OS X rendszeren.](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)
