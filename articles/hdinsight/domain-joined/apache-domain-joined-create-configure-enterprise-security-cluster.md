---
title: Hozzon létre, és az Azure HDInsight vállalati biztonsági csomag fürtök konfigurálása
description: Ismerje meg, hogyan hozhat létre, és az Azure HDInsight vállalati biztonsági csomag fürtök konfigurálása
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: e9cb9a902cf60fbd3b297a72a7dfa836ee18c835
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484583"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Hozzon létre, és az Azure HDInsight vállalati biztonsági csomag fürtök konfigurálása

A vállalati biztonsági csomag az Azure HDInsight hozzáférést biztosít az Apache Hadoop-fürtöket az Azure Active Directory-alapú hitelesítés, a több felhasználó támogatása és a szerepköralapú hozzáférés-vezérlés. HDInsight ESP-fürtök teszik lehetővé a szervezetek mely szigorú vállalati biztonsági szabályzatoknak, bizalmas adatok feldolgozását biztonságos formátumhoz.

Ez az útmutató célja megfelelően konfigurálhatja a szükséges erőforrást, úgy, hogy a helyszíni felhasználók jelentkezhetnek be az ESP engedélyezve van a HDInsight-fürt. Ez a cikk végigvezeti a lépéseken, hozzon létre egy Azure HDInsight-fürtön vállalati biztonsági csomag engedélyezve van szükség. A lépések Windows IaaS virtuális gép létrehozása az Active Directory & tartománynév szolgáltatásokat (DNS) engedélyezve van erről. Ez a kiszolgáló helyettesítője fog működni a **tényleges** a helyszíni környezetben, és lehetővé teszi, hogy később a saját környezetében megismételheti a beállítási és konfigurációs lépéseket folytassa. Ez az útmutató segítségével hozhat létre a Jelszókivonat-szinkronizálás használata az Azure Active Directory hibrid identitás környezetben is.

Ez az útmutató hivatott kiegészíteni [használata vállalati biztonsági csomag a HDInsight](apache-domain-joined-architecture.md)

Mielőtt ezt a folyamatot a saját környezetben, a telepítő Active Directory és a tartománynév-szolgáltatásokat (DNS). Azure Active Directory és a szinkronizálás a helyszíni felhasználói fiókok az Azure Active Directoryhoz is, engedélyezze.

![Architektúra ábrája](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>A helyszíni környezet létrehozása

Áttekintés: Ebben a szakaszban, lesz egy Azure gyors üzembe helyezés sablon használatával hozzon létre új virtuális gépet, a tartománynév-szolgáltatásokat (DNS) és a egy új AD-erdő konfigurálása.

1. Lépjen a [egy Azure virtuális gép létrehozása az új AD-erdővel rendelkező](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), a gyors telepítési sablon megtekintéséhez.

1. Kattintson a **helyezze üzembe az Azure**.
1. Jelentkezzen be az Azure-előfizetéshez.
1. Az a **egy Azure virtuális gép létrehozása az új AD-erdővel rendelkező** képernyőn, a következő lépéseket:
    1. Válassza ki az előfizetést, ahol azt szeretné, hogy a telepített erőforrások a **előfizetés** legördülő listából.
    1. Válassza ki **új létrehozása** melletti **erőforráscsoport** adja meg a nevét, és **OnPremADVRG**
    1. Adja meg a többi sablon mezőt a következő adatokat:

        * **Hely**: USA középső régiója
        * **Rendszergazdai felhasználónév**: HDIFabrikamAdmin
        * **Admin Password**: <YOUR_PASSWORD>
        * **Tartomány**: HDIFabrikam.com
        * **DNS-előtag**: hdifabrikam

        ![Sablon létrehozása az Azure virtuális gép és az AD-erdőhöz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Kattintson a **vásárlás**
    1. A központi telepítésének figyelése, és várjon, amíg annak végrehajtása befejeződik.
    1. Ellenőrizze az erőforrások létrejönnek a megfelelő erőforráscsoportba tartozó `OnPremADVRG`.

## <a name="configure-users-and-groups-for-cluster-access"></a>Felhasználók és csoportok fürt hozzáférés konfigurálása

Áttekintés: Ebben a szakaszban létrehozza a felhasználók, a HDInsight-fürthöz, ez az útmutató végén hozzáférhet.

1. A tartományvezérlő, a távoli asztal használatával csatlakozhat.
    1. Ha használta a sablon elején már említettük, a tartományvezérlő nem nevű virtuális gép **adVM** a a `OnPremADVRG` erőforráscsoportot.
    1. Nyissa meg az Azure portal > **erőforráscsoportok** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Kattintson a **RDP** fülre, majd **RDP-fájl letöltése**.
    1. Mentse a fájlt a számítógépre, és nyissa meg.
    1. Amikor a rendszer kéri a hitelesítő adatokat, használja `HDIFabrikam\HDIFabrikamAdmin` a felhasználónevet, majd írja be a jelszót, a rendszergazdai fiók számára is választott.

1. Ha a távoli asztali munkamenetet nyit a tartományvezérlő virtuális gép, indítsa el a **Active Directory – felhasználók és számítógépek** származó a **Kiszolgálókezelő** irányítópult. Kattintson a **eszközök** kattintson a jobb felső sarokban, majd **Active Directory – felhasználók és számítógépek** a legördülő listából.

    ![A Kiszolgálókezelő megnyitása az Active Directory felügyeleti](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Hozzon létre két új felhasználókat, **HDIAdmin**, **HDIUser**. A két felhasználó bejelentkezni a HDInsight-fürtökhöz használható.

    1. Az a **Active Directory – felhasználók és számítógépek** kattintson **művelet** > **új** > **felhasználói**.

        ![Hozzon létre új Active Directory-felhasználó](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Az a **új objektum – felhasználó** képernyőn írja be `HDIUser` , a **felhasználói bejelentkezési név** kattintson **tovább**.

        ![Hozzon létre az első rendszergazdai felhasználót](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. A megjelenő előugró ablakban adja meg a kívánt jelszót az új fiókhoz. Jelölje be a jelölőnégyzetet, arról, hogy a **jelszó sohasem jár le**. HDIClick **OK**.
    1. Kattintson a **Befejezés** az új fiók létrehozásához.
    1. Hozzon létre egy másik felhasználó `HDIAdmin`.

        ![Második rendszergazda felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Az a **Active Directory – felhasználók és számítógépek** kattintson **művelet** > **új** > **csoport**. Hozzon létre `HDIUserGroup` új csoportként.

    ![Új Active Directory-csoport létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Hozzon létre új csoport2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Adja hozzá a **HDIUser** az előző lépésben létrehozott a **HDIUserGroup** tagként.

    1. Kattintson a jobb gombbal a **HDIUserGroup** kattintson **tulajdonságok**.
    1. Lépjen a **tagok** fülre, és **Hozzáadás**.
    1. Adjon meg `HDIUser` feliratú mezőben **írja be a kijelölendő objektumok nevét** kattintson **OK**.
    1. Ismételje az előző lépést a többi fiók `HDIAdmin`

        ![Adja hozzá a csoport tagjai](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

A HDInsight-fürt eléréséhez most már az Active Directory-környezetet, valamint két felhasználót és a egy felhasználói csoportot hozott létre.

Ezeket a felhasználókat szinkronizálja az Azure ad-ben.

### <a name="create-a-new-azure-active-directory"></a>Hozzon létre egy új Azure Active Directory

1. Jelentkezzen be az Azure portálra.
1. Kattintson a **erőforrás létrehozása** , és írja be **directory**. Válassza ki **az Azure Active Directory** > **létrehozása**.
1. Adja meg **HDIFabrikam** alatt **szervezetnevet**.
1. Adja meg **HDIFabrikamoutlook** alatt **kezdeti tartománynév**.
1. Kattintson a **Create** (Létrehozás) gombra.
1. Az Azure Portal bal oldalán kattintson a **Azure Active Directory**.
1. Ha szükséges, kattintson a **címtár váltása** módosíthatja az új címtárra létrehozott **HDIFabrikamoutlook**.
1. A **kezelés** kattintson **egyéni tartománynevek** > **egyéni tartomány hozzáadása**.
1. Adja meg **HDIFabrikam.com** alatt **egyéni tartománynév** kattintson **tartomány hozzáadása**.

![Hozzon létre egy új azure active directory](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Hozzon létre egy új egyéni tartományt](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Áttekintés: Most konfigurálhatja az Azure AD-bérlővel, hogy szinkronizálhatja a felhasználók és csoportok a helyszíni AD a felhőbe.

1. Hozzon létre egy AD-Bérlői rendszergazda.
    1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure AD-bérlő **HDIFabrikam**
    1. Válassza ki **felhasználók** alatt **kezelés** , majd **új felhasználó**.
    1. Adja meg az új felhasználó a következő adatokat:

        * Name: fabrikamazureadmin
        * Felhasználónév: fabrikamazureadmin@hdifabrikam.com
        * Jelszó: egy biztonságos jelszót helyettesítse tetszőleges

    1. Kattintson a a **csoportok** szakaszban, keressen rá a **AAD DC rendszergazdák**, és kattintson a **válassza**.

        ![Csoportok](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Kattintson a **címtárbeli szerepkör** szakaszt, és válassza **globális rendszergazdai** jobb oldalán. Kattintson az **OK** gombra.

        ![Címtárbeli szerepkört](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Adja meg egy jelszót a felhasználó számára. Kattintson a **Create** (Létrehozás) gombra.

1. Ha szeretné módosítani a jelszót az újonnan létrehozott felhasználó <fabrikamazureadmin@hdifabrikam.com>. Jelentkezzen be az Azure Portalhoz az identitást, majd a rendszer kéri, hogy módosítsa a jelszót.

## <a name="sync-on-premises-users-to-azure-ad"></a>Szinkronizálás a helyszíni felhasználók Azure ad-hez

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Töltse le és telepítse a Microsoft Azure Active Directory connect

1. [Töltse le az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Telepítse a Microsoft Azure Active Directory connect a tartományvezérlőn.
    1. Nyissa meg a letöltött végrehajtható fájlt az előző lépésben, és fogadja el a licencfeltételeket. Kattintson a **Folytatás** gombra.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Kattintson a **gyorsbeállítások használata** és a telepítés befejezéséhez.

        ![Gyorsbeállítások használata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Szinkronizálás konfigurálása a helyszíni tartományvezérlővel

1. Az a **az Azure AD Connect** képernyőn, adja meg a felhasználónevet és jelszót a globális rendszergazda az Azure ad-hez. Kattintson a **tovább**. Ez a felhasználónév `fabrikamazureadmin@hdifabrikam.com` az AD-bérlő konfigurálása során létrehozott.
    ![Csatlakozás az Azure AD-hez](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Az a **csatlakozhat az Active Directory Domain Services** képernyőn, adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Kattintson a **tovább**. Ez a felhasználónév `HDIFabrikam\HDIFabrikamAdmin` és a megfelelő jelszót, amelyet korábban hozott létre.

   ![Csatlakozás az Active Directory Domain Servicesben](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Az a **az Azure AD bejelentkezés konfigurálása** kattintson **tovább**.
    ![Az Azure AD bejelentkezés konfigurálása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. A Ready to konfigurálása képernyő, kattintson a **telepítése**.
    ![Telepítése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Ha a **konfiguráció befejezéséhez** képernyő jelenik meg, kattintson a **kilépési**.
    ![Konfigurálás befejezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. A szinkronizálás befejeződése után győződjön meg arról, ha a felhasználók az IAAS Active Directoryban létrehozott Azure Active Directory szinkronizálva lesznek.
    1. Jelentkezzen be az Azure portálra.
    1. Válassza ki **az Azure Active Directory** > **HDIFabrikam** > **felhasználók**.

### <a name="create-an-user-assigned-managed-identity"></a>Hozzon létre egy felügyelt felhasználó által hozzárendelt identitások

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitás konfigurálása Azure Active Directory Domain Services (Azure AD DS) használt. Egy felhasználó által hozzárendelt felügyelt identitás létrehozásával kapcsolatos további információkért lásd: [létrehozása, list, delete vagy egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások szerepkör hozzárendelése](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Jelentkezzen be az Azure portálra.
1. Kattintson a **erőforrás létrehozása** , és írja be **identitás**. Válassza ki **felhasználóhoz felügyelt identitás** > **létrehozása**.
1. Adja meg **HDIFabrikamManagedIdentity** , a **erőforrásnév**.
1. Válassza ki előfizetését.
1. A **erőforráscsoport** kattintson **új létrehozása** , és adja meg **HDIFabrikam-CentralUS**.
1. Válassza ki **USA középső RÉGIÓJA** alatt **hely**.
1. Kattintson a **Create** (Létrehozás) gombra.

![Hozzon létre egy új felhasználóhoz felügyelt identitásnak](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Az Active Directory Domain Services engedélyezése

További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Az Azure AD-DS-gazdagépre a virtuális hálózat létrehozásához. Futtassa a következő powershell-kódot.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Jelentkezzen be az Azure portálra.
1. Kattintson a **erőforrás létrehozása**, adja meg **Domain services** válassza **Azure AD tartományi szolgáltatások**.
1. Az a **alapjai** képernyő a következő lépéseket:
    1. A **könyvtárnév** válassza ki az ebben a cikkben létrehozott Azure Active Directory **HDIFabrikam**.
    1. Adjon meg egy **DNS-tartománynév** , **HDIFabrikam.com**.
    1. Válassza ki előfizetését.
    1. Adja meg az erőforráscsoportot **HDIFabrikam-CentralUS** és a **hely** , **USA középső RÉGIÓJA**.

        ![az Azure ad ds alapvető adatok](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Az a **hálózati** teljes képernyő, válassza ki a hálózatot (**HDIFabrikam virtuális hálózatok közötti**) és az alhálózathoz (**AADDS alhálózattal**), amely az előző powershell-parancsprogram segítségével létrehozott. Vagy használhatja a **új létrehozása** most már létrehozhat egy virtuális hálózatot.

    ![hálózat kiválasztása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Az a **rendszergazdai csoport** képernyőn megjelenik egy értesítés, hogy a csoport neve **AAD DC rendszergazdák** felügyelheti ezt a csoportot már létrejött. Igény szerint módosíthatja a csoport tagságát, de ez nem kötelező ez a cikk lépéseit. Kattintson az **OK** gombra.

    ![rendszergazdai csoport megtekintése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Az a **szinkronizálási** képernyőn, a teljes szinkronizálás engedélyezése kiválasztásával **összes** majd **OK**.

    ![szinkronizálás engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Az a **összefoglalás** képernyőn ellenőrizze a részleteket a az Azure AD-DS-ben, kattintson a **Ok**.

    ![Ellenőrizze a részleteket](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Miután engedélyezte az Azure AD-Tartományi, egy helyi tartomány neve szolgáltatás (DNS) kiszolgáló fut, az AD virtuális gépeken (VM).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Az Azure AD-Tartományi virtuális hálózat konfigurálása

A jelen szakaszban ismertetett lépések segítségével konfigurálhatja az Azure AD-Tartományi virtuális hálózat (**HDIFabrikam-AADDSVNET**) az egyéni DNS-kiszolgálók használatához.

1. Keresse meg az egyéni DNS-kiszolgálók IP-címét. Kattintson a a **HDIFabrikam.com** Számítógépfiók erőforrás, kattintson a **tulajdonságok** alatt **kezelés**   , és tekintse meg az IP-címek alatt felsorolt **IP A virtuális hálózati cím**.

    ![Locate custom DNS IP addresses for Azure AD-DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurálása **HDIFabrikam-AADDSVNET** az egyéni IP-címek `10.0.0.4` és `10.0.0.5`.

    1. Válassza ki **DNS-kiszolgálók** alatt a **beállítások** kategória. Kattintson a Tovább gombra a választógomb **egyéni**, az alábbi mezőben adja meg az első IP-cím (10.0.0.4), és kattintson a **mentése**.
    1. Adjon hozzá további IP-címek (10.0.0.5) használatával ugyanazokat a lépéseket.

1. Ebben az esetben az Azure AD-Tartományi használatára van beállítva IP-címek 10.0.0.4 10.0.0.5, beállítás azonos IP-cím AADDS a virtuális hálózaton, az alábbi képen megjelenítése.

    ![egyéni dns-kiszolgálók megjelenítése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>LDAP-adatforgalom védelme

Az olvasási és írási Active Directory Lightweight Directory Access Protocol (LDAP) segítségével. Teheti forgalom LDAP bizalmas és biztonságos a Secure Sockets Layer (SSL) vagy Transport Layer Security (TLS) technológiát. Engedélyezheti az LDAP SSL feletti (LDAPS) egy megfelelően formázott tanúsítvány telepítésével.

A secure LDAP további információkért lásd: [konfigurálása biztonságos LDAP (LDAPS) számára egy Azure AD Domain Services felügyelt tartomány](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Ebben a szakaszban létrehoz egy önaláírt tanúsítványt, töltse le a tanúsítványt és úgy biztonságos LDAP (LDAPS-t) a a **hdifabrikam** a felügyelt tartomány Azure AD-DS-ben.

A következő szkript létrehoz egy tanúsítványt a hdifabrikam. A tanúsítványt a rendszer menti a "Helyi gépen lévő" elérési úton.

> [!Note] 
> Bármely segédprogramot vagy alkalmazást, amely létrehoz egy érvényes PKCS \#10 kérése az SSL-tanúsítványkérés űrlap segítségével.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Győződjön meg arról, hogy a tanúsítvány telepítve van-e a számítógép\'s személyes tárolójában. Hajtsa végre a következő lépéseket:

1. Indítsa el a Microsoft Management Console (MMC).
1. Adja hozzá a tanúsítványok beépülő modul, amely kezeli a tanúsítványokat a helyi számítógépen.
1. Bontsa ki a **tanúsítványok (helyi számítógép)** , bontsa ki a **személyes**, majd **tanúsítványok**. Egy új tanúsítványt a személyes tárban léteznie kell. A tanúsítványt a teljesen minősített állomásnév.

    ![Ellenőrizze a tanúsítvány létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. A jobb oldali ablaktáblában kattintson a jobb gombbal az előző lépésben létrehozott tanúsítványt mutasson **feladatok**, és kattintson a **exportálása**.

1. Az a **titkos kulcs exportálása** kattintson **Igen, a titkos kulcs exportálását választom,** . A titkos kulcs megadása kötelező a olvasható be a számítógépen, ahová importálni fogja a kulcs titkosított üzeneteket.

    ![titkos kulcs exportálása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Az a **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett beállításokat, és kattintson a **tovább**. 
1. Az a **jelszó** írja be a jelszót a titkos kulcsot, majd válassza az **TripleDES-SHA1** a **titkosítási** kattintson **következő**.
1. Az a **exportálandó fájl** lapon írja be az elérési út és az exportált tanúsítványfájlt, és kattintson a **tovább**.
1. A fájlnév .pfx kiterjesztésű értéke, ez a fájl biztonságos kapcsolatot létesíteni az Azure Portal van konfigurálva.
1. Engedélyezze a biztonságos LDAP (LDAPS-t) számára egy Azure AD tartományi szolgáltatásokkal felügyelt tartományban.
    1. Válassza ki a tartományt **HDIFabrikam.com** az Azure Portalról.
    1. Kattintson a **biztonságos LDAP** alatt **kezelése**.
    1. Az a **Secure LDAP** kattintson **engedélyezése** alatt **Secure LDAP**.
    1. Keresse meg a .pfx tanúsítványfájl exportált a számítógépen.
    1. Adja meg a tanúsítvány jelszavát.

    ![biztonságos ldap engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Most, hogy engedélyezte a biztonságos LDAP, ellenőrizze, hogy legyen elérhető engedélyezésével a 636-os portot.
    1. Kattintson a hálózati biztonsági csoport **AADDS-HDIFabrikam.com-NSG** a a **HDIFabrikam-CentralUS** erőforráscsoportot.
    1. A **beállítások** kattintson **bejövő biztonsági szabályok** > **Hozzáadás**.
    1. Az a **bejövő biztonsági szabály felvétele** képernyőn adja meg a következő tulajdonságait, kattintson a **Hozzáadás**:

        | Tulajdonság | Érték |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Cél | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Prioritás | <Desired Number> |
        | Name | Port_LDAP_636 |

    ![bejövő biztonsági szabály](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` a felhasználó által hozzárendelt felügyelt identitás, a HDInsight Domain Services közreműködői szerepkör engedélyezve van a felügyelt identitás, amely lehetővé teszi, hogy ezt az identitást, olvassa el, létrehozása, módosítása és törlése a tartományi szolgáltatások műveletek.

    ![A felhasználóhoz hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>HDInsight-fürt létrehozása a vállalati biztonsági csomag engedélyezve

Ebben a lépésben a következő előfeltételek teljesülésére van szükség:

1. Hozzon létre egy új erőforráscsoportot `HDIFabrikam-WestUS` helyen `West US`.
1. Hozzon létre egy virtuális hálózat ESP üzemeltető engedélyezve van a HDInsight-fürt.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Társ AADDS üzemeltető virtuális hálózatok közötti kapcsolat létrehozása (`HDIFabrikam-AADDSVNET`) és a virtuális hálózat, amely üzemelteti a ESP engedélyezve van a HDInsight-fürt (`HDIFabrikam-HDIVNet`). A következő powershell-kódot használja a két virtuális hálózat társviszonyba állítása.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Hozzon létre egy új fiókot az Azure Data Lake Storage Gen2 **Hdigen2store**, vagyis a felhasználó által felügyelt identitással konfigurált **HDIFabrikamManagedIdentity**. További információk a Data Lake Storage Gen2 ellátott fiókok a felhasználó létrehozása felügyelt identitások: [használata Azure Data Lake Storage Gen2 Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Az egyéni DNS beállítása a **HDIFabrikam-AADDSVNET** virtuális hálózatot.
    1. Nyissa meg az Azure portal > **erőforráscsoportok** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET**  >   **DNS-kiszolgálók**.
    1. Válassza ki **egyéni** , és adja meg `10.0.0.4` és `10.0.0.5`.
    1. Kattintson a **Save** (Mentés) gombra.

        ![egyéni dns-beállításainak mentése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. ESP-t a HDInsight Spark-fürtöt létrehozni.
    1. Kattintson a **egyéni (méret, beállítások, alkalmazások)** .
    2. Adja meg, hogy kívánt szakasz 1 **alapjai**. Ügyeljen arra, hogy a **fürt típusa** van **Spark 2.3-as (HDI 3.6)** és a **erőforráscsoport** van **HDIFabrikam-CentralUS**

    1. 2\. szakasz alatt **biztonság és a hálózatkezelés**, kövesse az alábbi lépéseket:
        1. Kattintson a **engedélyezve** alatt **vállalati biztonsági csomag**.
        1. Kattintson a **fürt rendszergazdai felhasználói** , és válassza ki a **HDIAdmin** , amely a helyi rendszergazda felhasználó a korábban létrehozott fiókot. Kattintson a **Kiválasztás** gombra.

        1. Kattintson a **hozzáférés fürtcsoport** majd **HDIUserGroup**. Azok a felhasználók, a jövőben hozzáadása ehhez a csoporthoz fog tudni hozzáférni a HDInsight-fürtök.

            ![Válassza ki a fürt hozzáférési csoport](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. A más lépéseket a fürt konfigurációját, és ellenőrizze a részleteket a **fürt összegzése**. Kattintson a **Create** (Létrehozás) gombra.

1. Jelentkezzen be az újonnan létrehozott fürt, az Ambari felhasználói felületén `https://CLUSTERNAME.azurehdinsight.net` használatával a rendszergazda felhasználóneve `hdiadmin@hdifabrikam.com` és a jelszót.

    ![Jelentkezzen be az Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Kattintson a **szerepkörök** a fürt irányítópultján.
1. Az a **szerepkörök** lap, adja meg a csoportot **hdiusergroup** rendeli hozzá a **Fürtfelügyelő** szerepkör alatt **szerepköröket hozzárendelni ezeket**.

    ![fürt rendszergazdai szerepkör hozzárendelése hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Nyissa meg az SSH-ügyfelet, és jelentkezzen be a fürt használatával a **hdiuser** a helyszíni Active Directoryban korábban létrehozott.

    ![Jelentkezzen be a fürthöz az ssh-val](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Ha tudja, hogy ezzel a fiókkal jelentkezzen be, majd már konfigurálta az ESP fürt megfelelően szinkronizálása a helyszíni active Directoryval.

## <a name="next-steps"></a>További lépések

* [Bevezetés az Apache Hadoop-biztonság, a vállalati biztonsági csomag](apache-domain-joined-introduction.md)
