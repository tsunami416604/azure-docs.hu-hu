---
title: Nagyvállalati biztonság az Azure AD DS-szel – Azure HDInsight
description: Megtudhatja, hogy miként állíthat be és konfigurálhat HDInsight Enterprise Security Package fürtöt az Azure Active Directory tartományi szolgáltatások használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272837"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Vállalati biztonsági csomag konfigurációi az Azure Active Directory tartományi szolgáltatásokkal a HDInsightban

Az Enterprise Security Package (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökhöz. Az ESP-vel rendelkező HDInsight-fürtök egy tartományhoz csatlakoznak, így a tartományi felhasználók a tartományi hitelesítő adataikkal hitelesíthetik magukat a fürtökkel, és big data-feladatokat futtathatnak.

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

Ha engedélyezi a biztonságos LDAP-t, tegye a tartománynevet a tulajdonos nevébe, és a tulajdonos alternatív nevét a tanúsítványba. Ha például a tartománynév *contoso100.onmicrosoft.com,* győződjön meg arról, hogy a tanúsítvány tulajdonosának neve és a tulajdonos alternatív neve szerepel. További információ: [Biztonságos LDAP konfigurálása Azure AD DS felügyelt tartományhoz.](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

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

A tartományszolgáltatások műveleteinek egyszerűsítéséhez és védelméhez használhatja a *felhasználó által hozzárendelt felügyelt identitást.* Amikor hozzárendeli a **HDInsight tartományi szolgáltatások közreműködői** szerepkört a felügyelt identitáshoz, képes olvasni, létrehozni, módosítani és törölni a tartományi szolgáltatások műveleteit. 

Bizonyos tartományi szolgáltatási műveletek, például a korvízek és a szolgáltatásegyszerű szolgáltatások létrehozása szükséges a HDInsight Enterprise Security Package számára. Felügyelt identitások bármely előfizetésben létrehozhat. A felügyelt identitások általánosságban további információkért lásd: [Felügyelt identitások az Azure-erőforrásokhoz.](../../active-directory/managed-identities-azure-resources/overview.md) A felügyelt identitások Azure HDInsightban való működéséről további információt az [Azure HDInsight felügyelt identitásai című témakörben talál.](../hdinsight-managed-identities.md)

Esp-fürtök beállításához hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyentel. További információt a [Szerepkör létrehozása, listázása, törlése vagy hozzárendelése a felhasználó által hozzárendelt felügyelt identitáshoz az Azure Portal használatával című témakörben talál.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

Ezután rendelje hozzá a **HDInsight tartományi szolgáltatások közreműködői** szerepkört az Azure AD DS **hozzáférés-vezérlésének** felügyelt identitásához. Ehhez a szerepkör-hozzárendeléshez szüksége van az Azure AD DS felügyeleti jogosultságaira.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

A **HDInsight tartományi szolgáltatások közreműködői** szerepkör hozzárendelése biztosítja, hogy ez az identitás megfelelő (nevében) hozzáféréssel rendelkezik a tartományi szolgáltatások műveleteinek az Azure AD DS-tartományban. Ezek a műveletek közé tartozik a megfelelőségi műveletek létrehozása és törlése.

A felügyelt identitás létrehozása után, és a megfelelő szerepkört, az Azure AD DS-rendszergazda beállíthatja, hogy ki használhatja ezt a felügyelt identitást. Először a rendszergazda kiválasztja a felügyelt identitást a portálon, majd kiválasztja a **hozzáférés-vezérlést (IAM)** **az Áttekintés csoportban.** Ezután a jobb oldalon a rendszergazda hozzárendeli a **felügyelt identitásoperátor** szerepkört azokhoz a felhasználókhoz vagy csoportokhoz, amelyek HDInsight ESP-fürtöket szeretnének létrehozni. 

Például az Azure AD DS-rendszergazda hozzárendelheti ezt a szerepkört a **MarketingTeam** csoporthoz az **sjmsi** felügyelt identitáshoz, ahogy az az alábbi képen látható. Ez a hozzárendelés biztosítja, hogy a szervezet megfelelő emberei a felügyelt identitás segítségével esp-fürtöket hozhassanak létre.

![HDInsight felügyelt identitáskezelő szerepkör-hozzárendelése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Hálózati szempontok

> [!NOTE]  
> Az Azure AD DS-t telepíteni kell egy Azure Resource Manager-alapú virtuális hálózatban. Klasszikus virtuális hálózatok nem támogatottak az Azure AD DS. További információ: [Az Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)című témakörben talál.

Miután engedélyezte az Azure AD DS-t, a helyi tartománynév-rendszer (DNS) kiszolgáló fut az Active Directory virtuális gépeken (VM). Konfigurálja az Azure AD DS virtuális hálózatot az egyéni DNS-kiszolgálók használatára. A megfelelő IP-címek megkereséséhez válassza a **Tulajdonságok lehetőséget** a **Kezelés** kategóriában, és tekintse meg **az IP-cím a virtuális hálózaton**területen területen.

![Helyi DNS-kiszolgálók IP-címeinek megkeresése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Módosítsa a DNS-kiszolgálók konfigurációját az Azure AD DS virtuális hálózatban, hogy ezeket az egyéni IP-ket használja a **Beállítások** kategóriában lévő **DNS-kiszolgálók** kiválasztásával. Ezután válassza az **Egyéni** lehetőséget, írja be az első IP-címet a szövegmezőbe, és válassza a **Mentés gombot.** Adjon hozzá további IP-címeket ugyanezen lépésekkel.

![A virtuális hálózati DNS-konfiguráció frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Az Azure AD DS-példányt és a HDInsight-fürtöt is könnyebben helyezheti el ugyanabban az Azure virtuális hálózatban. Ha különböző virtuális hálózatokat kíván használni, a virtuális hálózatokat társítania kell, hogy a tartományvezérlő látható legyen a HDInsight virtuális gépek számára. További információt a [Virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md)című témakörben talál. 

A virtuális hálózatok társviszonyba kerülése után konfigurálja úgy a HDInsight virtuális hálózatot, hogy egyéni DNS-kiszolgálót használjon, és adja meg az Azure AD DS privát IP-címét DNS-kiszolgálócímként. Ha mindkét virtuális hálózat ugyanazt a DNS-kiszolgálót használja, az egyéni tartománynév a megfelelő IP-címre oldódik fel, és a HDInsight ból érhető el. Ha például a tartománynév `contoso.com`, majd `ping contoso.com` ezt a lépést követően fel kell oldania a megfelelő Azure AD DS IP-cím.

![Egyéni DNS-kiszolgálók konfigurálása társviszony-létesített virtuális hálózathoz](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Ha a HDInsight-alhálózatban hálózati biztonsági csoport (NSG) szabályokat használ, engedélyeznie kell a [szükséges IP-ket](../hdinsight-management-ip-addresses.md) mind a bejövő, mind a kimenő forgalomhoz.

Annak ellenőrzéséhez, hogy a hálózat megfelelően van-e beállítva, csatlakozzon egy Windows virtuális géphez a HDInsight virtuális hálózathoz/alhálózathoz, és pingelje a tartománynevet. (Meg kell oldani, hogy egy IP.) Az **ldp.exe futtatásával** elérheti az Azure AD DS-tartományt. Ezután csatlakoztassa ezt a Windows virtuális gép a tartományhoz annak megerősítéséhez, hogy az összes szükséges RPC-hívások sikeresek az ügyfél és a kiszolgáló között. 

Az **nslookup** segítségével is megerősítheti a hálózati hozzáférést a tárfiókhoz vagy bármely külső adatbázishoz, amelyet használhat (például külső Hive metastore vagy Ranger DB). Győződjön meg arról, hogy az összes [szükséges port oka](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) az Azure AD DS-alhálózat NSG-szabályai, ha egy NSG segít az Azure AD DS biztonságos. Ha a Windows virtuális géphez való tartományhoz való csatlakozás sikeres, akkor folytathatja a következő lépéssel, és létrejöhet ESP-fürtöket.

## <a name="create-an-hdinsight-cluster-with-esp"></a>HDInsight-fürt létrehozása ESP-vel

Miután beállította az előző lépéseket helyesen, a következő lépés a HDInsight-fürt létrehozása esp engedélyezve van. HDInsight-fürt létrehozásakor a **Biztonság + hálózat** lapon engedélyezheti a vállalati biztonsági csomagot. Ha inkább egy Azure Resource Manager-sablont szeretne használni a központi telepítéshez, használja a portálélményt egyszer, és töltse le az előre kitöltött sablont a **Véleményezés + létrehozás** lapon későbbi újrafelhasználáshoz. 

A [HDInsight ID Broker](identity-broker.md) szolgáltatást is engedélyezheti a fürt létrehozása során. Az ID Broker funkció lehetővé teszi, hogy jelentkezzen be Ambari segítségével többtényezős hitelesítés és a szükséges Kerberos-jegyek beszerezni anélkül, hogy jelszó kibontások az Azure AD DS-ben.

> [!NOTE]  
> Az ESP-fürtnevek első hat karakterének egyedinek kell lennie a környezetben. Ha például több ESP-fürtje van különböző virtuális hálózatokban, válasszon egy elnevezési konvenciót, amely biztosítja, hogy a fürtnevek első hat karaktere egyedi legyen.

![Tartományérvényesítés az Azure HDInsight enterprise biztonsági csomaghoz](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Az ESP engedélyezése után az Azure AD DS-hez kapcsolódó gyakori helytelen konfigurációkat a rendszer automatikusan észleli és érvényesíti. A hibák kijavítása után folytathatja a következő lépéssel.

![Az Azure HDInsight enterprise biztonsági csomag nem sikerült a tartomány érvényesítésén](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Amikor ESP-vel hoz létre egy HDInsight-fürtöt, a következő paramétereket kell megadnia:

- **Fürtfelügyeleti felhasználó:** Válasszon egy rendszergazdát a fürthöz a szinkronizált Azure AD DS-példányból. Ezt a tartományi fiókot már szinkronizálni kell, és elérhetőnek kell lennie az Azure AD DS-ben.

- **Fürthozzáférési csoportok:** Azoknak a biztonsági csoportoknak, amelyek felhasználóit szinkronizálni szeretné, és hozzáférhetnek a fürthöz, elérhetőnek kell lenniük az Azure AD DS-ben. Egy példa a HiveUsers csoport. További információ: [Csoport létrehozása és tagok hozzáadása az Azure Active Directoryban című témakörben.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

- **LDAPS URL**: `ldaps://contoso.com:636`Erre példa a .

A létrehozott felügyelt identitás új fürt létrehozásakor a **Felhasználó által hozzárendelt felügyelt identitás** legördülő listából választható ki.

![Az Azure HDInsight ESP Active Directory tartományi szolgáltatások felügyelt identitása](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>További lépések

* A Hive-házirendek konfigurálása és a Hive-lekérdezések futtatása az [Apache Hive-házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md)című témakörben.
* Ha az SSH-t az ESP-vel rendelkező HDInsight-fürtökhöz való csatlakozáshoz használja, olvassa el az [SSH használata Linux alapú Apache Hadoop használatával HDInsight-on Linux, Unix vagy OS X rendszeren.](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)
