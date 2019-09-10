---
title: Enterprise Security Package-fürtök létrehozása és konfigurálása az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre és konfigurálhat Enterprise Security Package-fürtöket az Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: bb6a3cff46c975ae6b59f0c6f97e37037f638620
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845784"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package-fürtök létrehozása és konfigurálása az Azure HDInsight

Az Azure HDInsight Enterprise Security Package hozzáférést biztosít Active Directory-alapú hitelesítéshez, többfelhasználós támogatáshoz és szerepköralapú hozzáférés-vezérléshez az Azure-beli Apache Hadoop-fürtökhöz. A HDInsight ESP-fürtök lehetővé teszik a szervezetek számára, hogy szigorú vállalati biztonsági szabályzatoknak megfeleljenek a bizalmas adatok biztonságos feldolgozásához.

Ennek az útmutatónak a célja, hogy megfelelően konfigurálja a szükséges erőforrásokat, hogy a helyszíni felhasználók bejelentkezhetnek egy ESP-kompatibilis HDInsight-fürtre. Ez a cikk végigvezeti egy Enterprise Security Package engedélyezett Azure HDInsight-fürt létrehozásához szükséges lépéseken. A lépések egy olyan Windows IaaS virtuális gép létrehozását fedik le, amelyen engedélyezve van a Active Directory & tartománynév-szolgáltatás (DNS). Ez a kiszolgáló helyettesíti a **tényleges** helyszíni környezetet, és lehetővé teszi a beállítási és konfigurációs lépések folytatását, így később is megismételheti őket a saját környezetében. Ez az útmutató segítséget nyújt a hibrid identitási környezetek létrehozásában is a jelszó-kivonatoló szinkronizálással Azure Active Directory használatával.

Az útmutató célja, hogy kiegészítse a [használati Enterprise Security Package a HDInsight-ben](apache-domain-joined-architecture.md)

Mielőtt a folyamatot a saját környezetében használja, a telepítő Active Directory és a tartománynév-szolgáltatás (DNS). Emellett engedélyezze Azure Active Directory és szinkronizálja a helyszíni felhasználói fiókokat a Azure Active Directory.

![Architektúra diagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Helyszíni környezet létrehozása

Áttekintés: Ebben a szakaszban egy Azure-beli gyors telepítési sablont fog használni új virtuális gépek létrehozásához, a tartománynév-szolgáltatások (DNS) és egy új AD-erdő konfigurálásához.

1. Nyissa meg az Azure-beli [virtuális gép létrehozása új ad-erdővel lehetőséget](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)a gyors üzembe helyezési sablon megtekintéséhez.

1. Kattintson az **üzembe helyezés az Azure-** ban lehetőségre.
1. Jelentkezzen be az Azure-előfizetésbe.
1. Az Azure-beli **virtuális gép létrehozása új ad-erdővel** képernyőn hajtsa végre a következő lépéseket:
    1. Válassza ki azt az előfizetést, amelyen az **előfizetések legördülő** menüjéből telepíteni kívánja az erőforrásokat.
    1. Válassza az **új létrehozása** az **erőforráscsoport** mellett lehetőséget, és adja meg a **OnPremADVRG** nevet
    1. Adja meg a következő adatokat a sablon többi mezőjénél:

        * **Hely**: USA középső régiója
        * **Rendszergazdai Felhasználónév**: HDIFabrikamAdmin
        * **Rendszergazdai jelszó**: < YOUR_PASSWORD >
        * **Tartomány**: HDIFabrikam.com
        * **DNS-előtag**: hdifabrikam

        ![Sablon Azure-beli virtuális gép és AD-erdő létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Kattintson a **vásárlás** elemre.
    1. Figyelje a központi telepítést, és várjon, amíg befejeződik.
    1. Erősítse meg, hogy az erőforrások a megfelelő erőforráscsoport `OnPremADVRG`alatt jönnek létre.

## <a name="configure-users-and-groups-for-cluster-access"></a>Felhasználók és csoportok konfigurálása a fürthöz való hozzáféréshez

Áttekintés: Ebben a szakaszban azokat a felhasználókat fogja létrehozni, akik hozzáférhetnek a HDInsight-fürthöz az útmutató végére.

1. Kapcsolódjon a tartományvezérlőhöz Távoli asztal használatával.
    1. Ha az elején említett sablont használta, a tartományvezérlő egy **adVM** nevű virtuális gép az `OnPremADVRG` erőforráscsoporthoz.
    1. Nyissa meg a Azure Portal > **erőforráscsoportok** > **OnPremADVRG** > **adVM** > **csatlakozni**.
    1. Kattintson az **RDP** fülre, majd az **RDP-fájl letöltése**elemre.
    1. Mentse a fájlt a számítógépre, és nyissa meg.
    1. Amikor a rendszer kéri a hitelesítő `HDIFabrikam\HDIFabrikamAdmin` adatokat, használja a nevet, majd adja meg a rendszergazdai fiókhoz választott jelszót.

1. Miután a Távoli asztal-munkamenet megnyílik a tartományvezérlő virtuális gépen, indítsa el **Active Directory felhasználókat és számítógépeket** a **Kiszolgálókezelő** irányítópultján. Kattintson a jobb felső sarokban található **eszközök** lehetőségre, majd **Active Directory felhasználók és számítógépek** elemre a legördülő listából.

    ![A Kiszolgálókezelő Active Directory felügyeletének megnyitása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Hozzon létre két új felhasználót, **HDIAdmin**, **HDIUser**. Ezt a két felhasználót fogja használni a rendszer a HDInsight-fürtökbe való bejelentkezéshez.

    1. A **Active Directory felhasználók és számítógépek** képernyőn kattintson az**új** > **felhasználó** **művelet** > elemre.

        ![Új Active Directory-felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Az **új objektum – felhasználó** képernyőn adja meg `HDIUser` a **felhasználói bejelentkezési nevet** , majd kattintson a **tovább**gombra.

        ![Első rendszergazda felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. A megjelenő előugró ablakban adja meg az új fiókhoz használni kívánt jelszót. Jelölje be azt a jelölőnégyzetet, amely szerint a **jelszó soha nem jár le**. HDIClick **OK**.
    1. Az új fiók létrehozásához kattintson a **Befejezés** gombra.
    1. Hozzon létre `HDIAdmin`egy másik felhasználót.

        ![Második rendszergazda felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. A **Active Directory felhasználók és számítógépek** képernyőn kattintson a **művelet** > **új** > **csoport**elemre. Új `HDIUserGroup` csoport létrehozása.

    ![Új Active Directory csoport létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![új Group2 létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Adja hozzá az előző lépésben létrehozott **HDIUser** a **HDIUserGroup** tagként.

    1. Kattintson a jobb gombbal a **HDIUserGroup** , majd kattintson a **Tulajdonságok**elemre.
    1. Nyissa meg a **tagok** lapot, és kattintson a **Hozzáadás**gombra.
    1. Adja `HDIUser` meg a kijelölendő **objektumok nevét** , majd kattintson az **OK**gombra.
    1. A másik fiók előző lépéseinek megismétlése`HDIAdmin`

        ![Tagok felvétele a csoportba](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Ezzel létrehozta Active Directory-környezetét, valamint két felhasználót és egy felhasználói csoportot a HDInsight-fürt eléréséhez.

Ezek a felhasználók az Azure AD-vel lesznek szinkronizálva.

### <a name="create-a-new-azure-active-directory"></a>Új Azure Active Directory létrehozása

1. Jelentkezzen be az Azure portálra.
1. Kattintson **az erőforrás létrehozása** elemre, és írja be a **könyvtárat**. Válassza a **Azure Active Directory** > **Létrehozás**elemet.
1. Adja meg a **HDIFabrikam** a **szervezet neve**alatt.
1. Adja meg a **HDIFabrikamoutlook** a **kezdeti tartománynév**területen.
1. Kattintson a **Create** (Létrehozás) gombra.
1. A Azure Portal bal oldalán kattintson az **Azure Active Directory**elemre.
1. Ha szükséges, kattintson a **váltás könyvtár** lehetőségre a **HDIFabrikamoutlook**létrehozott új könyvtárra való váltáshoz.
1. A **kezelés** alatt kattintson az >  **Egyéni tartománynevek** **egyéni tartomány hozzáadása**lehetőségre.
1. Adja meg az **HDIFabrikam.com** az **Egyéni tartománynév** területen, majd kattintson a **tartomány hozzáadása**lehetőségre.

![új Azure Active Directory létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![új egyéni tartomány létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Áttekintés: Most konfigurálja az Azure AD-bérlőt, hogy szinkronizálja a felhasználókat és a csoportokat a helyszíni AD-ből a felhőbe.

1. Hozzon létre egy AD-bérlői rendszergazdát.
    1. Jelentkezzen be a Azure Portalba, és válassza ki az Azure AD-bérlői **HDIFabrikam**
    1. A **kezelés** területen válassza a **felhasználók** , majd az **új felhasználó**lehetőséget.
    1. Adja meg az új felhasználó következő adatait:

        * Név: fabrikamazureadmin
        * Felhasználónév:fabrikamazureadmin@hdifabrikam.com
        * Password (jelszó): tetszőleges biztonságos jelszó

    1. Kattintson a **csoportok** szakaszra, keresse meg a **HRE tartományvezérlő rendszergazdáit**, és kattintson a **kiválasztás**elemre.

        ![Csoportok](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Kattintson a **címtár szerepkör** szakaszra, és válassza a **globális rendszergazda** lehetőséget a jobb oldalon. Kattintson az **OK** gombra.

        ![Címtárszerepkör](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Adja meg a felhasználó jelszavát. Kattintson a **Create** (Létrehozás) gombra.

1. Ha módosítani szeretné az újonnan létrehozott felhasználó <fabrikamazureadmin@hdifabrikam.com>jelszavát. Jelentkezzen be a Azure Portal az identitás használatával, majd a rendszer felszólítja a jelszó módosítására.

## <a name="sync-on-premises-users-to-azure-ad"></a>Helyszíni felhasználók szinkronizálása az Azure AD-vel

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory-kapcsolat letöltése és telepítése

1. [Azure ad Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594).

1. Telepítse Microsoft Azure Active Directory a kapcsolatot a tartományvezérlőn.
    1. Nyissa meg az előző lépésben letöltött végrehajtható fájlt, és fogadja el a licencfeltételeket. Kattintson a **Folytatás** gombra.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Kattintson az **expressz beállítások használata** lehetőségre, és fejezze be a telepítést.

        ![Gyorsbeállítások használata](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Szinkronizálás konfigurálása a helyszíni tartományvezérlővel

1. A **Kapcsolódás az Azure ad-hoz** képernyőn adja meg az Azure ad globális rendszergazdájának felhasználónevét és jelszavát. Kattintson a **tovább**gombra. Ez az ad- `fabrikamazureadmin@hdifabrikam.com` bérlő konfigurálásakor létrehozott Felhasználónév.
    ![Csatlakozás az Azure AD-hez](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)
1. A **kapcsolódás Active Directory tartományi szolgáltatások** képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Kattintson a **tovább**gombra. Ez a Felhasználónév `HDIFabrikam\HDIFabrikamAdmin` és a hozzá tartozó jelszó, amelyet korábban hozott létre.

   ![Kapcsolódás Active Directory tartományi szolgáltatásokhoz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Az **Azure ad bejelentkezési konfigurációja** lapon kattintson a **tovább**gombra.
    ![Azure AD bejelentkezési konfiguráció](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)
1. A konfigurálásra kész képernyőn kattintson a **telepítés**elemre.
    ![telepítése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Ha megjelenik a **konfiguráció befejezése** képernyő, kattintson a **Kilépés**gombra.
    ![a konfigurálás befejeződött](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. A szinkronizálás befejeződése után ellenőrizze, hogy a IAAS létrehozott felhasználók Active Directory szinkronizálva vannak-e a Azure Active Directory.
    1. Jelentkezzen be az Azure portálra.
    1. Válassza **Azure Active Directory** > HDIFabrikam- > **felhasználók**lehetőséget.

### <a name="create-an-user-assigned-managed-identity"></a>Felhasználóhoz rendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, amelyet a rendszer a Azure Active Directory Domain Services (Azure AD-DS) konfigurálására fog használni. A felhasználó által hozzárendelt felügyelt identitás létrehozásával kapcsolatos további információkért tekintse meg [a szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)című témakört.

1. Jelentkezzen be az Azure portálra.
1. Kattintson **az erőforrás létrehozása** elemre, és írja be a **felügyelt identitást**. Válassza a **felhasználóhoz rendelt felügyelt identitás** > **létrehozása**elemet.
1. Adja meg a HDIFabrikamManagedIdentity **nevet az erőforrás neveként**.
1. Válassza ki előfizetését.
1. Az **erőforráscsoport** területen kattintson az **új létrehozása** elemre, és írja be a **HDIFabrikam-CentralUS**értéket.
1. Válassza az **USA középső** **régiója**lehetőséget.
1. Kattintson a **Create** (Létrehozás) gombra.

![új, felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Az Active Directory Domain Services engedélyezése

További információ: [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Hozza létre a Virtual Network az Azure AD-DS üzemeltetéséhez. Futtassa a következő PowerShell-kódot.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Jelentkezzen be az Azure portálra.
1. Kattintson az **erőforrás létrehozása**elemre, írja be a **tartományi szolgáltatások** elemet, és válassza a **Azure ad Domain Services**lehetőséget.
1. Az **alapvető beállítások** képernyőn hajtsa végre a következő lépéseket:
    1. A **címtár neve** területen válassza ki a cikkhez létrehozott Azure Active Directory **HDIFabrikam**.
    1. Adja meg a **HDIFabrikam.com** **DNS-tartománynevét** .
    1. Válassza ki előfizetését.
    1. Itt adhatja meg az **HDIFabrikam-CentralUS** és az **USA középső** **régiójának helyét** .

        ![Az Azure AD DS alapszintű részletei](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. A **hálózat** képernyőn válassza ki az előző PowerShell-parancsfájllal létrehozott hálózatot (**HDIFabrikam-VNET**) és az alhálózatot (**AADDS-alhálózat**). Vagy használhatja az **új létrehozása** lehetőséget egy virtuális hálózat létrehozásához.

    ![hálózat kiválasztása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. A **Rendszergazda csoport** képernyőjén egy értesítés jelenik meg arról, hogy az **HRE DC-rendszergazdák** nevű csoport már létrejött a Csoport felügyeletéhez. Lehetősége van a csoport tagságának módosítására, de ez nem szükséges a jelen cikk lépéseihez. Kattintson az **OK** gombra.

    ![rendszergazdai csoport megtekintése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. A **szinkronizálási** képernyőn engedélyezze a teljes szinkronizálást az **összes** kiválasztásával, majd kattintson **az OK**gombra.

    ![szinkronizálás engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Az **Összefoglalás** képernyőn ellenőrizze az Azure AD-DS adatait, majd kattintson **az OK gombra**.

    ![Részletek ellenőrzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Az Azure AD-DS engedélyezése után a helyi tartománynév-szolgáltatási (DNS) kiszolgáló fut az AD Virtual Machineson (VM).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Az Azure AD-DS virtuális hálózat konfigurálása

Az ebben a szakaszban ismertetett lépések segítséget nyújtanak az Azure AD-DS virtuális hálózat (**HDIFabrikam-AADDSVNET**) konfigurálásához az egyéni DNS-kiszolgálók használatához.

1. Keresse meg az egyéni DNS-kiszolgálók IP-címeit. Kattintson a **HDIFabrikam.com** AD-DS erőforrásra **, kattintson a** **kezelés**   elemre, és tekintse meg az IP-cím területen felsorolt IP-címeket a **Virtual Networkon**.

    ![Az Azure AD-DS egyéni DNS IP-címeinek megkeresése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurálja a **HDIFabrikam-AADDSVNET** egyéni IP `10.0.0.4` - `10.0.0.5`címekre és.

    1. Válassza a **DNS-kiszolgálók** lehetőséget a **Beállítások** kategóriában. Ezután kattintson az **Egyéni**elem melletti választógombra, adja meg az első IP-címet (10.0.0.4) az alábbi szövegmezőben, majd kattintson a **Save (Mentés**) gombra.
    1. Adja hozzá a további IP-címeket (10.0.0.5) ugyanazon lépések használatával.

1. Ebben az esetben az Azure AD-DS-t úgy konfigurálták, hogy az IP-címek 10.0.0.4 és 10.0.0.5 használatára legyen konfigurálva, ugyanezt az IP-címet állítsa be a AADDS-VNet az alábbi képen látható módon.

    ![Egyéni DNS-kiszolgálók megtekintése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Az LDAP-forgalom biztonságossá tétele

A Lightweight Directory Access Protocol (LDAP) a Active Directory olvasására és írására szolgál. Az LDAP-forgalmat SSL (SSL)/Transport Layer Security (TLS) technológiával bizalmasan és biztonságossá teheti. Az LDAP-t SSL (LDAPs) protokollon keresztül is engedélyezheti egy megfelelően formázott tanúsítvány telepítésével.

A biztonságos LDAP-ról további információt a [biztonságos LDAP (LDAPS) konfigurálása Azure ad Domain Services felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)című témakörben talál.

Ebben a szakaszban létrehoz egy önaláírt tanúsítványt, letölti a tanúsítványt, és konfigurálja a biztonságos LDAP (LDAPs) szolgáltatást a **hdifabrikam** Azure AD-DS felügyelt tartományhoz.

A következő szkript létrehoz egy tanúsítványt a hdifabrikam számára. A tanúsítvány a "LocalMachine" elérési úton lesz mentve.

> [!Note] 
> Bármely olyan segédprogram vagy alkalmazás használható, amely egy \#érvényes PKCS 10 kérelmet hoz létre az SSL-tanúsítványkérelem megalkotása céljából.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Ellenőrizze, hogy a tanúsítvány telepítve van-e\'a számítógép személyes tárolójába. Hajtsa végre a következő lépéseket:

1. Indítsa el a Microsoft Management Console (MMC) programot.
1. Adja hozzá a Tanúsítványok beépülő modult, amely a helyi számítógépen lévő tanúsítványokat kezeli.
1. Bontsa ki a **tanúsítványok (helyi számítógép)** , **személyes**csomópontot, majd a **tanúsítványok**csomópontot. A személyes tárolóban léteznie kell egy új tanúsítványnak. Ezt a tanúsítványt a rendszer a teljes állomásnév számára adja ki.

    ![tanúsítvány létrehozásának ellenőrzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. A jobb oldali ablaktáblában kattintson a jobb gombbal az előző lépésben létrehozott tanúsítványra, mutasson a **minden feladat**pontra, majd kattintson az **Exportálás**parancsra.

1. A **titkos kulcs** exportálása lapon kattintson **az Igen gombra, exportálja a titkos kulcsot**. A titkos kulcs szükséges ahhoz, hogy a titkosított üzenetek beolvashatók legyenek a kulcs importálására szolgáló számítógépről.

    ![titkos kulcs exportálása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Az **Exportálás fájlformátuma** lapon hagyja meg az alapértelmezett beállításokat, majd kattintson a **tovább**gombra. 
1. A **jelszó** lapon adja meg a titkos kulcshoz tartozó jelszót, válassza a **TripleDES-SHA1** lehetőséget a **titkosításhoz** , és kattintson a **tovább**gombra.
1. Az exportálandó **fájl** lapon adja meg az exportált tanúsítványfájl elérési útját és nevét, majd kattintson a **tovább**gombra.
1. A fájlnévnek. pfx kiterjesztésű kell lennie, ez a fájl Azure Portal van konfigurálva a biztonságos kapcsolat létrehozásához.
1. A biztonságos LDAP (LDAPs) engedélyezése Azure AD Domain Services felügyelt tartományhoz.
    1. Válassza ki a Azure Portal **HDIFabrikam.com** tartományát.
    1. Kattintson **Secure LDAP** a **kezelés**alatt.
    1. A **Secure LDAP** képernyőn kattintson az **engedélyezés** elemre **Secure LDAP**alatt.
    1. Tallózással keresse meg a számítógépen exportált. pfx tanúsítványfájl-fájlt.
    1. Adja meg a tanúsítvány jelszavát.

    ![biztonságos LDAP engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Most, hogy engedélyezte Secure LDAP, győződjön meg arról, hogy elérhető a 636-es port engedélyezésével.
    1. Kattintson a **AADDS-HDIFabrikam.com-NSG** hálózati biztonsági csoportra a **HDIFabrikam-CentralUS** erőforráscsoporthoz.
    1. A **Beállítások** területen kattintson a **bejövő biztonsági szabályok** > **Hozzáadás**elemre.
    1. A **bejövő biztonsági szabály hozzáadása** képernyőn adja meg a következő tulajdonságokat, majd kattintson a **Hozzáadás**gombra:

        | Tulajdonság | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Cél | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name (Név) | Port_LDAP_636 |

    ![bejövő biztonsági szabály](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`a felhasználó által hozzárendelt felügyelt identitás, a HDInsight tartományi szolgáltatások közreműködői szerepköre engedélyezve van a felügyelt identitás számára, amely lehetővé teszi az identitás számára a tartományi szolgáltatások beolvasását, létrehozását, módosítását és törlését.

    ![felhasználóhoz rendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Enterprise Security Package engedélyezett HDInsight-fürt létrehozása

Ehhez a lépéshez a következő előfeltételek szükségesek:

1. Hozzon létre egy új `HDIFabrikam-WestUS` erőforráscsoportot a helyen `West US`.
1. Hozzon létre egy virtuális hálózatot, amely az ESP-kompatibilis HDInsight-fürtöt fogja üzemeltetni.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Hozzon létre egy társ-kapcsolatot a AADDS (`HDIFabrikam-AADDSVNET`) szolgáltatást futtató Virtual Network és az ESP-kompatibilis HDInsight-fürtöt (`HDIFabrikam-HDIVNet`) tároló Virtual Network között. Ezt a két virtuális hálózatot a következő PowerShell-kóddal használhatja.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Hozzon létre egy új Azure Data Lake Storage Gen2 fiókot, a **Hdigen2store**, amely a felhasználó által felügyelt identitás **HDIFabrikamManagedIdentity**van konfigurálva. A felhasználó által felügyelt identitásokkal engedélyezett Data Lake Storage Gen2 fiókok létrehozásával kapcsolatos további információkért lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Állítsa be az egyéni DNS **-t a HDIFabrikam-AADDSVNET** virtuális hálózaton.
    1. Nyissa meg a**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-kiszolgálók**Azure Portal > **erőforráscsoportot** > .
    1. Válassza az **Egyéni** lehetőséget `10.0.0.4` , `10.0.0.5`és adja meg a és a értéket.
    1. Kattintson a **Save** (Mentés) gombra.

        ![Egyéni DNS-beállítások mentése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Hozzon létre egy új ESP-kompatibilis HDInsight Spark-fürtöt.
    1. Kattintson **az egyéni (méret, beállítások, alkalmazások)** elemre.
    2. Adja meg az 1. szakasz **alapjaihoz**szükséges adatokat. Győződjön meg arról, hogy a **fürt típusa** **Spark 2,3 (HDI 3,6)** , és az **erőforráscsoport** a következő: **HDIFabrikam-CentralUS**

    1. A 2. szakasz **Biztonság és hálózatkezelés**területén hajtsa végre a következő lépéseket:
        1. **Enterprise Security Package**alatt kattintson az **engedélyezve** lehetőségre.
        1. Kattintson a **fürt rendszergazdai felhasználója** elemre, és válassza ki azt a **HDIAdmin** -fiókot, amelyet korábban a helyszíni rendszergazda felhasználóként hozott létre. Kattintson a **Kiválasztás** gombra.

        1. Kattintson a **fürt-hozzáférési csoport** , majd a **HDIUserGroup**elemre. A jövőben a csoportba felvett összes felhasználó hozzáférhet a HDInsight-fürtökhöz.

            ![fürt-hozzáférési csoport kiválasztása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Fejezze be a fürtkonfiguráció további lépéseit, és ellenőrizze a **fürt összegzésének**részleteit. Kattintson a **Create** (Létrehozás) gombra.

1. Jelentkezzen be az újonnan létrehozott fürt `https://CLUSTERNAME.azurehdinsight.net` Ambari felhasználói felületén a rendszergazdai `hdiadmin@hdifabrikam.com` felhasználónevével és jelszavával.

    ![Bejelentkezés a Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Kattintson a **szerepkörök** elemre a fürt irányítópultján.
1. A **szerepkörök** lapon adja meg azt a **hdiusergroup** , amelyet hozzá szeretne rendelni a **fürt rendszergazdai** szerepköréhez a **szerepkörök társítása ehhez**.

    ![fürt rendszergazdai szerepkörének kiosztása a hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Nyissa meg az SSH-ügyfelet, és jelentkezzen be a fürtbe a korábban a helyszíni Active Directoryban létrehozott **hdiuser** használatával.

    ![Bejelentkezés a fürtbe SSH-val](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Ha be tud jelentkezni ezzel a fiókkal, akkor helyesen konfigurálta az ESP-fürtöt a helyszíni Active Directoryval való szinkronizáláshoz.

## <a name="next-steps"></a>További lépések

* [Bevezetés a Apache Hadoop biztonság használatába Enterprise Security Package](hdinsight-security-overview.md)
