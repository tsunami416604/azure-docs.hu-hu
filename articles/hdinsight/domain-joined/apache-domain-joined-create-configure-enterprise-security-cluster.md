---
title: Vállalati biztonsági csomagfürtök létrehozása, konfigurálása – Azure
description: A vállalati biztonsági csomagfürtök létrehozása és konfigurálása az Azure HDInsightban
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436040"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Vállalati biztonsági csomagfürtök létrehozása és konfigurálása az Azure HDInsightban

Az Azure HDInsight vállalati biztonsági csomagja (ESP) hozzáférést biztosít az Active Directory-alapú hitelesítéshez, a többfelhasználós támogatáshoz és az Azure-beli Apache Hadoop-fürtök szerepköralapú hozzáférés-vezérléséhez. A HDInsight ESP-fürtök lehetővé teszik a szigorú vállalati biztonsági házirendeknek megfelelő szervezetek számára a bizalmas adatok biztonságos feldolgozását.

Ez az útmutató bemutatja, hogyan hozhat létre egy ESP-kompatibilis Azure HDInsight-fürt. Azt is bemutatja, hogyan hozhat létre olyan Windows IaaS virtuális gép, amelyen engedélyezve van az Active Directory és a DNS .It also shows how to create a Windows IaaS VM on which Active Directory and Domain Name System (DNS) are enabled. Ez az útmutató a szükséges erőforrások konfigurálásához lehetővé teszi a helyszíni felhasználók számára, hogy jelentkezzen be egy ESP-kompatibilis HDInsight-fürtbe.

A létrehozott kiszolgáló a *tényleges* helyszíni környezet helyettesítéseként fog működni. Ezt fogja használni a beállítási és konfigurációs lépésekhez. Később ismételje meg a lépéseket a saját környezetében.

Ez az útmutató is segít létrehozni egy hibrid identitáskörnyezet segítségével jelszókivonat-szinkronizálás az Azure Active Directory (Azure AD). Az útmutató kiegészíti [az ESP használatát a HDInsight ban.](apache-domain-joined-architecture.md)

Mielőtt ezt a folyamatot a saját környezetében használná:

* Állítsa be az Active Directoryt és a DNS-t.
* Engedélyezze az Azure AD.Enable Azure AD.
* Szinkronizálja a helyszíni felhasználói fiókokat az Azure AD-vel.

![Az Azure AD architektúradiagramja](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Helyszíni környezet létrehozása

Ebben a szakaszban egy Azure gyorsindítási telepítési sablont fog használni új virtuális gépek létrehozásához, a DNS konfigurálásához és egy új Active Directory-erdő hozzáadásához.

1. Nyissa meg a rövid útmutató központi telepítési [sablont, és hozzon létre egy Azure-beli virtuális gép egy új Active Directory erdővel.](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)

1. Válassza **a Telepítés az Azure-ba**lehetőséget.
1. Jelentkezzen be Azure-előfizetésbe.
1. Az **Azure-beli virtuális gép létrehozása egy új AD erdővel** lapon adja meg a következő információkat:

    |Tulajdonság | Érték |
    |---|---|
    |Előfizetés|Válassza ki azt az előfizetést, amelyhez telepíteni szeretné az erőforrásokat.|
    |Erőforráscsoport|Válassza **az Új létrehozása**lehetőséget, és írja be a nevet.`OnPremADVRG`|
    |Hely|Válasszon ki egy helyet.|
    |Rendszergazdai felhasználónév|`HDIFabrikamAdmin`|
    |Rendszergazdai jelszó|Adja meg a jelszót.|
    |Tartománynév|`HDIFabrikam.com`|
    |Dns-előtag|`hdifabrikam`|

    Hagyja meg a fennmaradó alapértelmezett értékeket.

    ![Sablon azure-beli virtuális gép létrehozásához egy új Azure AD-erdővel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Tekintse át az **Általános Szerződési Feltételeket**, majd válassza **az Elfogadom a fent meghatározott feltételeket**.
1. Válassza **a Vásárlás**lehetőséget, és figyelje a központi telepítést, és várja meg, amíg befejeződik. Az üzembe helyezés körülbelül 30 percet vesz igénybe.

## <a name="configure-users-and-groups-for-cluster-access"></a>Felhasználók és csoportok konfigurálása fürthozzáféréshez

Ebben a szakaszban hozza létre azokkal a felhasználókkal, akik az útmutató végére hozzáférhetnek a HDInsight-fürthöz.

1. Csatlakozzon a tartományvezérlőhöz a Távoli asztal segítségével.
    1. Az Azure Portalon keresse meg az**OnPremADVRG** > **adVM** > Connect **erőforráscsoportokat.** > **Connect**
    1. Az **IP-cím** legördülő listából válassza ki a nyilvános IP-címet.
    1. Válassza **az RDP-fájl letöltése**lehetőséget, majd nyissa meg a fájlt.
    1. Használja `HDIFabrikam\HDIFabrikamAdmin` felhasználónévként.
    1. Adja meg a rendszergazdai fiókhoz választott jelszót.
    1. Válassza **az OK gombot.**

1. A tartományvezérlő **Kiszolgálókezelő** irányítópultjáról keresse meg az Eszközök Az Active Directory – felhasználók és számítógépek **Tools** > **eszközeit.**

    ![A Kiszolgálókezelő irányítópultján nyissa meg az Active Directory-kezelés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Hozzon létre két új felhasználót: **HDIAdmin** és **HDIUser**. Ez a két felhasználó bejelentkezik a HDInsight-fürtökbe.

    1. Az **Active Directory – felhasználók és** `HDIFabrikam.com`számítógépek lapon kattintson a jobb gombbal a gombbal, majd válassza az **Új** > **felhasználó lehetőséget.**

        ![Új Active Directory-felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Az **Új objektum -** Felhasználó `HDIUser` lapon adja meg az **Utónév** és **a Felhasználó bejelentkezési nevét.** A többi mező automatikusan kinépesíti. Ezután válassza a **Tovább**gombot.

        ![Az első rendszergazdai felhasználói objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. A megjelenő előugró ablakban adja meg az új fiók jelszavát. Válassza **a Jelszó soha nem jár le**, majd az OK **gombot** az előugró üzenetnél.
    1. Az új fiók létrehozásához válassza a **Tovább**lehetőséget, majd a **Befejezés** gombot.
    1. Ismételje meg a fenti `HDIAdmin`lépéseket a felhasználó létrehozásához.

        ![Második rendszergazdai felhasználói objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Hozzon létre egy globális biztonsági csoportot.

    1. Az **Active Directory – felhasználók és számítógépek beépülő modulból**kattintson a jobb gombbal `HDIFabrikam.com`a elemre, majd keresse meg az **Új** > **csoport lehetőséget.**

    1. Írja `HDIUserGroup` be a **Csoport név** mezőbe.

    1. Válassza **az OK gombot.**

    ![Új Active Directory-csoport létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Új objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Tagok hozzáadása a **HDIUserGroup csoporthoz.**

    1. Kattintson a jobb gombbal a **HDIUser** elemre, és válassza **a Hozzáadás egy csoporthoz parancsot...**.
    1. A **Szövegkijelölendő objektumok nevének** megadása `HDIUserGroup`mezőbe írja be a mezőbe. Ezután kattintson **az OK**gombra, majd ismét az **OK gombra** az előugró ablakban.
    1. Ismételje meg az előző lépéseket a **HDIAdmin** fiókhoz.

        ![A HDIUser tag hozzáadása a HDIUser csoporthoz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Most létrehozta az Active Directory-környezetet. Két felhasználót és egy felhasználói csoportot adott hozzá, amelyek hozzáférhetnek a HDInsight-fürthöz.

A felhasználók szinkronizálva lesznek az Azure AD-vel.

### <a name="create-an-azure-ad-directory"></a>Azure AD-könyvtár létrehozása

1. Jelentkezzen be az Azure portálra.
1. Válassza **az Erőforrás** `directory`létrehozása és típus lehetőséget. Válassza az **Azure Active Directory** > **létrehozása lehetőséget.**
1. A Szervezet neve `HDIFabrikam` **mezőbe**írja be a mezőbe a lehetőséget.
1. A **Kezdeti tartománynév mezőbe**írja be a mezőbe `HDIFabrikamoutlook`a t.
1. Kattintson a **Létrehozás** gombra.

    ![Azure AD-könyvtár létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Egyéni tartomány létrehozása

1. Az új **Azure Active Directory**kezelése **csoportban**válassza az **Egyéni tartománynevek lehetőséget.**
1. Válassza **a + Egyéni tartomány hozzáadása**lehetőséget.
1. Az **Egyéni tartománynév csoportban**írja be a be `HDIFabrikam.com`adását, és válassza a Tartomány **hozzáadása**lehetőséget.
1. Ezután töltse [ki a DNS-adatok at a tartományregisztrálóhoz.](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)

![Egyéni tartomány létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Csoport létrehozása

1. Az új **Azure Active Directory**ban válassza a **Csoportok**lehetőséget. **Groups**
1. Válassza **a + Új csoport lehetőséget.**
1. A **csoportnév** mezőbe írja `AAD DC Administrators`be a mezőbe.
1. Kattintson a **Létrehozás** gombra.

## <a name="configure-your-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Most konfigurálja az Azure AD-bérlőt, hogy szinkronizálhassa a felhasználókat és csoportokat a helyszíni Active Directory-példányból a felhőbe.

Hozzon létre egy Active Directory-bérlői rendszergazdát.

1. Jelentkezzen be az Azure Portalon, és válassza ki az Azure AD-bérlőt, **a HDIFabrikam-ot.**

1. Keresse meg a**Felhasználók** >  **kezelése** > Az**új felhasználó**lehetőséget.

1. Adja meg az új felhasználó következő adatait:

    **Identitás**

    |Tulajdonság |Leírás |
    |---|---|
    |Felhasználónév|Írja `fabrikamazureadmin` be a szövegmezőbe. A tartománynév legördülő listából válassza a`hdifabrikam.com`|
    |Név| Írja be a `fabrikamazureadmin` (igen) kifejezést.|

    **Jelszó**
    1. Válassza **a Jelszó létrehozása lehetőséget.**
    1. Adjon meg egy biztonságos jelszót, amelyet választott.

    **Csoportok és szerepkörök**
    1. Jelölje ki **a kijelölt 0 csoportot.**
    1. Válassza **az AAD tartományvezérlő-rendszergazdák**lehetőséget, majd **a Lehetőséget.**

    ![Az Azure AD-csoportok párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Válassza a **Felhasználó**lehetőséget.
    1. Válassza **a Globális rendszergazda**lehetőséget, majd a **Lehetőséget.**

    ![Az Azure AD szerepkör párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Kattintson a **Létrehozás** gombra.

1. Ezután az új felhasználó jelentkezzen be az Azure Portalon, ahol a rendszer kéri a jelszó módosítását. Ezt a Microsoft Azure Active Directory Connect konfigurálása előtt kell megtennie.

## <a name="sync-on-premises-users-to-azure-ad"></a>Helyszíni felhasználók szinkronizálása az Azure AD-vel

### <a name="configure-microsoft-azure-active-directory-connect"></a>A Microsoft Azure Active Directory Connect konfigurálása

1. A tartományvezérlőről töltse le a [Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)szolgáltatást.

1. Nyissa meg a letöltött végrehajtható fájlt, és fogadja el a licencfeltételeket. Válassza a **Folytatás** elemet.

1. Válassza **a Gyorsbeállítások használata lehetőséget.**

1. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure AD globális rendszergazdájának felhasználónevét és jelszavát. Használja az `fabrikamazureadmin@hdifabrikam.com` Active Directory-bérlő konfigurálásakor létrehozott felhasználónevet. Ezután válassza a **Tovább**gombot.

    ![A "Csatlakozás az Azure AD-hez" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. A Csatlakozás az **Active Directory tartományi szolgáltatásokhoz** lapon adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Használja a `HDIFabrikam\HDIFabrikamAdmin` korábban létrehozott felhasználónevet és jelszavát. Ezután válassza a **Tovább**gombot.

   ![A "Csatlakozás az Azure AD-hez" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Az **Azure AD bejelentkezési konfigurációs** lapján válassza a **Tovább lehetőséget.**
   ![Az "Azure AD bejelentkezési konfiguráció" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. A **Konfigurálásra kész** lapon válassza a **Telepítés**lehetőséget.

   ![A "Konfigurálásra kész" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. A **Konfiguráció befejeződése** lapon válassza a **Kilépés**lehetőséget.
   ![A "Konfiguráció kész" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. A szinkronizálás befejezése után győződjön meg arról, hogy az IaaS-címtárban létrehozott felhasználók szinkronizálva vannak-e az Azure AD-vel.
   1. Jelentkezzen be az Azure portálra.
   1. Válassza az **Azure Active Directory** > **HDIFabrikam-felhasználók** > **lehetőséget.**

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, amely segítségével konfigurálhatja az Azure AD tartományi szolgáltatások (Azure AD DS). További információ: [Szerepkör létrehozása, listázása, törlése vagy hozzárendelése egy felhasználó által hozzárendelt felügyelt identitáshoz az Azure Portal használatával.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

1. Jelentkezzen be az Azure portálra.
1. Válassza **az Erőforrás** `managed identity`létrehozása és típus lehetőséget. Válassza **a Felügyelt felhasználó hozadéklehetőséget.** > **Create**
1. Az **erőforrás neve** `HDIFabrikamManagedIdentity`mezőbe írja be a mezőbe a t.
1. Válassza ki előfizetését.
1. Az **Erőforráscsoport**csoportban válassza `HDIFabrikam-CentralUS`az Új **létrehozása** lehetőséget, és írja be a parancsot.
1. A **Hely csoportban**válassza az **USA középső részén**lehetőséget.
1. Kattintson a **Létrehozás** gombra.

![Új, felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Az Azure AD tartományi szolgáltatások engedélyezése

Kövesse az alábbi lépéseket az Azure AD DS engedélyezéséhez. További információ: [Az Azure AD DS engedélyezése az Azure Portalhasználatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)című témakörben talál.

1. Hozzon létre egy virtuális hálózatot az Azure AD DS üzemeltetéséhez. Futtassa a következő PowerShell-kódot.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Jelentkezzen be az Azure portálra.
1. Válassza az Erőforrás `Domain services` **létrehozása**lehetőséget, írja be a , majd az Azure **AD tartományi szolgáltatások** > **létrehozása lehetőséget.**
1. Az **Alapok** lapon:
    1. A **Címtár név csoportban**válassza ki a létrehozott Azure AD könyvtárat: **HDIFabrikam**.
    1. **A DNS-tartománynév**hez adja meg *HDIFabrikam.com.*
    1. Válassza ki előfizetését.
    1. Adja meg a **HDIFabrikam-CentralUS**erőforráscsoportot. A **Hely lehetőséget**az USA középső **részén**válassza.

        ![Az Azure AD DS alapvető részletei](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. A **Hálózat** lapon válassza ki a PowerShell-parancsfájl használatával létrehozott hálózatot (**HDIFabrikam-VNET**) és az alhálózatot (**AADDS-alhálózat).** Vagy válassza **az Új létrehozása** lehetőséget a virtuális hálózat létrehozásához.

    ![A "Virtuális hálózat létrehozása" lépés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. A **Rendszergazda csoport** lapon meg kell jelennie egy értesítésnek arról, hogy a csoport felügyeletére már létrehozták **az AAD dc-rendszergazdák** nevű csoportot. Módosíthatja a csoport tagságát, ha szeretné, de ebben az esetben nem kell módosítania. Válassza **az OK gombot.**

    ![Az Azure AD rendszergazdai csoport megtekintése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. A **Szinkronizálás** lapon engedélyezze a teljes szinkronizálást az **Összes** > **OK**gombra kattintva.

    ![Az Azure AD DS-szinkronizálás engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Az **Összefoglaló** lapon ellenőrizze az Azure AD DS adatait, és válassza az **OK gombot.**

    ![Az "Azure AD tartományi szolgáltatások engedélyezése" összegzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Miután engedélyezte az Azure AD DS-t, egy helyi DNS-kiszolgáló fut az Azure AD virtuális gépeken.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Az Azure AD DS virtuális hálózatkonfigurálása

Az alábbi lépésekkel konfigurálja az Azure AD DS virtuális hálózat **(HDIFabrikam-AADDSVNET)** az egyéni DNS-kiszolgálók használatára.

1. Keresse meg az egyéni DNS-kiszolgálók IP-címét.
    1. Válassza `HDIFabrikam.com` ki az Azure AD DS-erőforrást.
    1. A **Kezelés csoportban**válassza a **Tulajdonságok lehetőséget.**
    1. Keresse meg az IP-címeket **a virtuális hálózaton lévő IP-cím**alatt.

    ![Az Azure AD DS egyéni DNS-IP-címeinek megkeresése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Konfigurálja a **HDIFabrikam-AADDSVNET protokollt** a 10.0.0.4 és a 10.0.0.5 egyéni IP-címek használatára.

    1. A **Beállítások csoportban**válassza a **DNS-kiszolgálók**lehetőséget.
    1. Válassza **az Egyéni**lehetőséget.
    1. A szövegmezőbe írja be az első IP-címet (*10.0.0.4*).
    1. Kattintson a **Mentés** gombra.
    1. Ismételje meg a lépéseket a másik IP-cím (*10.0.0.5*) hozzáadásához.

A mi forgatókönyvünkben úgy állítottuk be az Azure AD DS-t, hogy a 10.0.0.4 és a 10.0.0.5 IP-címeket használja, és ugyanazt az IP-címet állítja be az Azure AD DS virtuális hálózaton:

![Az egyéni DNS-kiszolgálók lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP-forgalom biztonságossá tétele

A Lightweight Directory Access Protocol (LDAP) az Azure Active Directoryból való olvasásra és írásra szolgál. Az LDAP-forgalmat bizalmassá és biztonságossá teheti a Secure Sockets Layer (SSL) vagy a Transport Layer Security (TLS) technológia használatával. Az LDAP SSL-en (LDAPS) keresztül imitálható, megfelelően formázott tanúsítvány telepítésével engedélyezhető.

A biztonságos LDAP-ról további információt az [LDAPS konfigurálása Azure AD DS felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)című témakörben talál.

Ebben a szakaszban önaláírt tanúsítványt hoz létre, letölti a tanúsítványt, és konfigurálja az LDAPS-t a **HDIFabrikam** Azure AD DS felügyelt tartományához.

A következő parancsfájl létrehoz egy tanúsítványt **hdiFabrikam**. A tanúsítvány a *LocalMachine* elérési útján kerül mentésre.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Minden olyan segédprogram vagy alkalmazás, amely érvényes nyilvános kulcsú \#titkosítási szabványt (PKCS) 10 kérelmet hoz létre, használható az SSL tanúsítványkérelem létrehozásához.

Ellenőrizze, hogy a tanúsítvány telepítve van-e a számítógép **személyes** tárolójában:

1. Indítsa el a Microsoft Management Console (MMC) konzolt.
1. Adja hozzá a **tanúsítványokat** kezelő Tanúsítványok beépülő modult a helyi számítógépen.
1. Bontsa ki **a Tanúsítványok (helyi számítógép)** > **személyes** > **tanúsítványainak kibontását.** A **személyes** tárolóban új tanúsítványnak kell léteznie. Ezt a tanúsítványt a teljesen minősített állomásnévadja ki.

    ![A helyi tanúsítvány létrehozásának ellenőrzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. A jobb oldali ablaktáblában kattintson a jobb gombbal a létrehozott tanúsítványra. Mutasson a **Minden tevékenység pontra,** és válassza **az Exportálás**lehetőséget.

1. A **Személyes kulcs exportálása** lapon válassza az **Igen lehetőséget, és exportálja a személyes kulcsot**. A számítógépnek, amelyen a kulcsot importálni fogja, a titkosított kulcsnak szüksége van a titkosított üzenetek olvasására.

    ![A Tanúsítványexportáló varázsló Személyes kulcs exportálása lapja](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. A **Fájlformátum exportálása** lapon hagyja meg az alapértelmezett beállításokat, majd kattintson a **Tovább gombra.**
1. A **Jelszó** lapon írja be a személyes kulcs jelszavát. Titkosítás **esetén**válassza a **TripleDES-SHA1 lehetőséget.** Ezután válassza a **Tovább**gombot.
1. A **Fájl exportálandó** lapon írja be az exportált tanúsítványfájl elérési útját és nevét, majd kattintson a **Tovább gombra.** A fájlnévnek .pfx kiterjesztéssel kell rendelkeznie. Ez a fájl az Azure Portalon biztonságos kapcsolat létrehozásához van konfigurálva.
1. LDAPS engedélyezése egy Azure AD DS felügyelt tartományban.
    1. Az Azure Portalon válassza `HDIFabrikam.com`ki a tartományt.
    1. A **Kezelés csoportban**válassza **a Biztonságos LDAP**lehetőséget.
    1. A **Biztonságos LDAP** lap **Biztonságos LDAP**területén válassza az **Engedélyezés**lehetőséget.
    1. Keresse meg a számítógépen exportált .pfx tanúsítványfájlt.
    1. Adja meg a tanúsítvány jelszavát.

    ![Biztonságos LDAP engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Most, hogy engedélyezte az LDAPS-t, győződjön meg róla, hogy elérhető a 636-os port engedélyezésével.
    1. A **HDIFabrikam-CentralUS** erőforráscsoportban válassza ki az **AADDS-HDIFabrikam.com-NSG**hálózati biztonsági csoportot.
    1. A **Beállítások csoportban**válassza a Bejövő biztonsági **szabályok** > hozzáadása**lehetőséget.**
    1. A **Bejövő biztonsági szabály hozzáadása** lapon adja meg a következő tulajdonságokat, és válassza a **Hozzáadás**lehetőséget:

        | Tulajdonság | Érték |
        |---|---|
        | Forrás | Bármelyik |
        | Forrásporttartományok | * |
        | Cél | Bármelyik |
        | Célporttartomány | 636 |
        | Protocol (Protokoll) | Bármelyik |
        | Műveletek | Engedélyezés |
        | Prioritás | \<A kívánt szám> |
        | Név | Port_LDAP_636 |

    ![A "Bejövő biztonsági szabály hozzáadása" párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**A HDIFabrikamManagedIdentity** a felhasználó által hozzárendelt felügyelt identitás. A HDInsight tartományi szolgáltatások közreműködői szerepkör engedélyezve van a felügyelt identitás, amely lehetővé teszi, hogy ez az identitás olvasni, létrehozni, módosítani és törölni a tartományi szolgáltatások műveleteket.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP-kompatibilis HDInsight-fürt létrehozása

Ez a lépés a következő előfeltételeket igényli:

1. Hozzon létre egy új erőforráscsoportot *HDIFabrikam-WestUS* a hely **nyugat-AMERIKAI**.
1. Hozzon létre egy virtuális hálózatot, amely az ESP-kompatibilis HDInsight-fürtöt fogja üzemeltetni.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Hozzon létre társkapcsolatot az Azure AD DS (`HDIFabrikam-AADDSVNET`) szolgáltatást üzemeltető virtuális hálózat és az`HDIFabrikam-HDIVNet`ESP-kompatibilis HDInsight-fürt ( virtuális hálózata) között. Használja a következő PowerShell-kódot a két virtuális hálózat társviszony-létesítéséhez.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Hozzon létre egy új Azure Data Lake Storage Gen2 fiókot **Hdigen2store**néven. Konfigurálja a fiókot a **hdIFabrikamManagedIdentity**felhasználó által felügyelt identitással. További információ: [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Egyéni DNS beállítása a **HDIFabrikam-AADDSVNET** virtuális hálózaton.
    1. Nyissa meg az Azure Portal > **Erőforráscsoportok** > **OnPrepremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-kiszolgálók .**
    1. Válassza **az Egyéni lehetőséget,** és írja be a *10.0.0.4* és *10.0.0.5 értéket.*
    1. Kattintson a **Mentés** gombra.

        ![Egyéni DNS-beállítások mentése virtuális hálózathoz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Hozzon létre egy új, ESP-kompatibilis HDInsight Spark-fürtöt.
    1. Válassza **az Egyéni (méret, beállítások, alkalmazások)** lehetőséget.
    1. Adja meg **az alapok** részleteit (1. szakasz). Győződjön meg arról, hogy a **fürt típusa** **Spark 2.3 (HDI 3.6)**. Győződjön meg arról, hogy az **erőforráscsoport** **HDIFabrikam-CentralUS**.

    1. **A Biztonság + hálózat (2.** szakasz) esetében töltse ki a következő adatokat:
        * A **Vállalati biztonsági csomag csoportban**válassza az **Engedélyezve**lehetőséget.
        * Válassza **a Fürtfelügyeleti felhasználó** lehetőséget, és válassza ki a helyszíni rendszergazdai felhasználóként létrehozott **HDIAdmin** fiókot. Kattintson a **Kiválasztás** gombra.
        * Válassza a**HDIUserGroup** **fürthozzáférési csoport** > lehetőséget. A jövőben a csoporthoz hozzáadott bármely felhasználó hozzáférhet a HDInsight-fürtökhöz.

            ![A HDIUserGroup fürthozzáférési csoport kijelölése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Végezze el a fürtkonfiguráció további lépéseit, és ellenőrizze a **fürtösszegzés részleteit.** Kattintson a **Létrehozás** gombra.

1. Jelentkezzen be az Újonnan létrehozott fürt Ambari felhasználói felületére a helyen. `https://CLUSTERNAME.azurehdinsight.net` Használja a rendszergazdai felhasználónevet `hdiadmin@hdifabrikam.com` és annak jelszavát.

    ![Az Apache Ambari felhasználói felület bejelentkezési ablaka](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. A fürt irányítópultján válassza a **Szerepkörök**lehetőséget.
1. A **Szerepkörök** lap **Szerepkörök hozzárendelése ezekhez**csoportjában a **Fürtfelügyelő** szerepkör mellett adja meg a csoport *hdiusergroup*. 

    ![A fürtfelügyeleti szerepkör hozzárendelése a hdiusergrouphoz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Nyissa meg a Secure Shell (SSH) ügyfelet, és jelentkezzen be a fürtbe. Használja a helyszíni Active Directory-példányban létrehozott **hdiusert.**

    ![Bejelentkezés a fürtbe az SSH-ügyfél használatával](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Ha ezzel a fiókkal tud bejelentkezni, megfelelően konfigurálta az ESP-fürtöt, hogy szinkronizálja a helyszíni Active Directory-példányt.

## <a name="next-steps"></a>További lépések

Olvassa el [Az Apache Hadoop biztonságának bemutatkozása az ESP segítségével.](hdinsight-security-overview.md)
