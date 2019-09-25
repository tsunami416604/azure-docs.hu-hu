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
ms.openlocfilehash: 9b9071eae4ec18cb1d5fd277f6f5403ce3997f48
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261740"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package-fürtök létrehozása és konfigurálása az Azure HDInsight

Az Azure HDInsight Enterprise Security Package (ESP) hozzáférést biztosít Active Directory-alapú hitelesítéshez, többfelhasználós támogatáshoz és szerepköralapú hozzáférés-vezérléshez az Azure-beli Apache Hadoop-fürtökhöz. A HDInsight ESP-fürtök lehetővé teszik a szigorú vállalati biztonsági szabályzatoknak megfelelő szervezetek számára a bizalmas adatok biztonságos feldolgozását.

Ez az útmutató bemutatja, hogyan hozhat létre ESP-kompatibilis Azure HDInsight-fürtöt. Azt is bemutatja, hogyan hozhat létre olyan Windows IaaS virtuális gépet, amelyen engedélyezve van a Active Directory és a tartománynévrendszer (DNS). Ezzel az útmutatóval konfigurálhatók a szükséges erőforrások, amelyek lehetővé teszik a helyszíni felhasználók számára, hogy bejelentkezzenek egy ESP-kompatibilis HDInsight-fürtbe.

Az Ön által létrehozott kiszolgáló helyettesíti a *tényleges* helyszíni környezetet. Ezt fogja használni a telepítési és konfigurálási lépésekhez. Később megismételheti a lépéseket a saját környezetében. 

Ez az útmutató a hibrid identitás-környezetek létrehozásához is segítséget nyújt a jelszó-kivonatoló szinkronizálással Azure Active Directory (Azure AD) használatával. Az útmutató kiegészíti az [ESP használatát a HDInsight-ben](apache-domain-joined-architecture.md).

Mielőtt a folyamatot a saját környezetében használja:
* A Active Directory és a DNS beállítása.
* Engedélyezze az Azure AD-t.
* Helyszíni felhasználói fiókok szinkronizálása az Azure AD-vel.

![Azure AD architektúra diagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Helyszíni környezet létrehozása

Ebben a szakaszban egy Azure rövid útmutató üzembe helyezési sablonját fogja használni új virtuális gépek létrehozásához, a DNS konfigurálásához és egy új Active Directory erdő hozzáadásához.

1. Nyissa meg a gyors üzembe helyezési sablont, és [hozzon létre egy új Active Directory erdővel rendelkező Azure-beli virtuális gépet](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Válassza **az üzembe helyezés az Azure-** ban lehetőséget.
1. Jelentkezzen be az Azure-előfizetésbe.
1. Az Azure-beli **virtuális gép létrehozása új ad-erdővel** lapon:
    1. Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyben az erőforrásokat telepíteni szeretné.
    1. Az **erőforráscsoport**mellett válassza az **új létrehozása**lehetőséget, és adja meg a *OnPremADVRG*nevet.
    1. A sablon többi mezőjénél adja meg a következő adatokat:

        * **Hely**: USA középső régiója
        * **Rendszergazdai Felhasználónév**: HDIFabrikamAdmin
        * **Rendszergazdai jelszó**: \<YOUR_PASSWORD >
        * **Tartománynév**: HDIFabrikam.com
        * **DNS-előtag**: hdifabrikam

        ![Sablon új Azure AD-erdővel rendelkező Azure-beli virtuális gép létrehozásához](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Válassza a **Beszerzés** lehetőséget.
    1. Figyelje a központi telepítést, és várjon, amíg befejeződik.
    1. Győződjön meg arról, hogy az erőforrások a megfelelő erőforráscsoport ( **OnPremADVRG**) alatt jönnek létre.

## <a name="configure-users-and-groups-for-cluster-access"></a>Felhasználók és csoportok konfigurálása a fürthöz való hozzáféréshez

Ebben a szakaszban azokat a felhasználókat fogja létrehozni, akik hozzáférhetnek a HDInsight-fürthöz az útmutató végére.

1. Kapcsolódjon a tartományvezérlőhöz Távoli asztal használatával.
    1. Ha az elején említett sablont használta, a tartományvezérlő egy **adVM** nevű virtuális gép a **OnPremADVRG** -erőforráscsoporthoz.
    1. A Azure Portal válassza az **erőforráscsoportok** > **OnPremADVRG** > **adVM** > -**kapcsolat**lehetőséget.
    1. Válassza az **RDP** fület > **RDP-fájl letöltése**lehetőséget.
    1. Mentse a fájlt a számítógépre, és nyissa meg.
    1. Ha a rendszer hitelesítő adatokat kér, használja a *HDIFabrikam\HDIFabrikamAdmin* -t felhasználónévként. Ezután adja meg a rendszergazdai fiókhoz választott jelszót.

1. Ha a Távoli asztal-munkamenet megnyílik a tartományvezérlő virtuális gépen, akkor a **Kiszolgálókezelő** irányítópultján nyissa meg **Active Directory felhasználókat és számítógépeket**. A jobb felső sarokban válassza az **eszközök** > **Active Directory felhasználók és számítógépek**lehetőséget.

    ![A Kiszolgálókezelő irányítópultján nyissa meg Active Directory felügyelet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Hozzon létre két új felhasználót: **HDIAdmin** és **HDIUser**. Ez a két felhasználó bejelentkezik a HDInsight-fürtökbe.

    1. A **Active Directory felhasználók és számítógépek** lapon válassza az**új** > **felhasználó** **művelet** > lehetőséget.

        ![Új Active Directory-felhasználó létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Az **új objektum – felhasználó** lapon a **felhasználói bejelentkezési név**mezőbe írja be a következőt: *HDIUser*. Ezután kattintson a **Tovább** gombra.

        ![Az első rendszergazdai felhasználói objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. A megjelenő előugró ablakban adjon meg egy jelszót az új fiókhoz. Válassza a **jelszó soha nem jár le** > **OK**elemet.
    1. Az új fiók létrehozásához válassza a **Befejezés** lehetőséget.
    1. Hozzon létre egy *HDIAdmin*nevű másik felhasználót.

        ![Második rendszergazdai felhasználói objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. A **Active Directory felhasználók és számítógépek** lapon válassza a **művelet** > **új** > **csoport**lehetőséget. Adjon hozzá egy *HDIUserGroup*nevű csoportot.

    ![Új Active Directory csoport létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Új objektum létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. **HDIUser** hozzáadása a **HDIUserGroup** .

    1. Kattintson a jobb gombbal a **HDIUserGroup** elemre, és válassza a **Tulajdonságok**lehetőséget.
    1. A **tagok** lapon válassza a **Hozzáadás**lehetőséget.
    1. Az **írja be a kijelölendő objektumok nevét** mezőbe írja be a következőt: *HDIUser*. Ezután kattintson az **OK** gombra.
    1. Ismételje meg az előző lépéseket a **HDIAdmin** -fiókhoz.

        ![A tag HDIUser hozzáadása a HDIUserGroup csoporthoz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Ezzel létrehozta Active Directory-környezetét. Két felhasználót és egy felhasználói csoportot adott hozzá, amely hozzáférhet a HDInsight-fürthöz.

A felhasználók szinkronizálása az Azure AD-vel történik.

### <a name="create-an-azure-ad-directory"></a>Azure AD-címtár létrehozása

1. Jelentkezzen be az Azure portálra.
1. Válassza **az erőforrás létrehozása** és a *könyvtár*megadása lehetőséget. Válassza a **Azure Active Directory** > **Létrehozás**elemet.
1. A **szervezet neve**alatt adja meg a *HDIFabrikam*.
1. A **kezdeti tartománynév**mezőben adja meg a *HDIFabrikamoutlook*nevet.
1. Kattintson a **Létrehozás** gombra.

    ![Azure AD-címtár létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. A Azure Portal bal oldalán válassza a **Azure Active Directory**lehetőséget.
1. Ha szükséges, válassza a **váltás könyvtár** lehetőséget az új **HDIFabrikamoutlook** könyvtárba való váltáshoz.
1. A **kezelés**területen válassza az >  **Egyéni tartománynevek** **egyéni tartomány hozzáadása**elemet.
1. Az **Egyéni tartománynév**területen adja meg a *HDIFabrikam.com* nevet, és válassza a **tartomány hozzáadása**elemet.

![Egyéni tartomány létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Az Azure AD-bérlő konfigurálása

Most konfigurálja az Azure AD-bérlőt, hogy szinkronizálja a felhasználókat és a csoportokat a helyszíni Active Directory-példányról a felhőbe.

1. Hozzon létre egy Active Directory bérlői rendszergazdát.
    1. Jelentkezzen be a Azure Portalba, és válassza ki az Azure AD-bérlőt ( **HDIFabrikam**).
    1. A **kezelés**területen válassza a **felhasználók** > **új felhasználó**lehetőséget.
    1. Adja meg az új felhasználó következő adatait:
        * **Név**: fabrikamazureadmin
        * **Felhasználónév**:fabrikamazureadmin@hdifabrikam.com
        * **Jelszó**: Tetszőleges biztonságos jelszó

    1. A **csoportok** szakaszban keresse meg a **HRE DC-rendszergazdák** kifejezést, majd kattintson a **kiválasztás**elemre.

        ![Az Azure AD-csoportok párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Nyissa meg a **címtár szerepkör** szakaszt, és a jobb oldalon válassza a **globális rendszergazda** > **OK**elemet.

        ![Az Azure AD-szerepkör párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Adja meg a felhasználó jelszavát. Ezután kattintson a **Létrehozás** elemre.

1. Ha módosítani szeretné az újonnan létrehozott felhasználói \< fabrikamazureadmin@hdifabrikam.com> jelszavát, az identitás használatával jelentkezzen be a Azure Portalba. Ezt követően a rendszer felszólítja a jelszó módosítására.

## <a name="sync-on-premises-users-to-azure-ad"></a>Helyszíni felhasználók szinkronizálása az Azure AD-vel

### <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect letöltése és telepítése

1. [Azure ad Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594).

1. Telepítse a Azure AD Connectt a tartományvezérlőre.

    1. Nyissa meg a letöltött végrehajtható fájlt, és fogadja el a licencfeltételeket. Válassza a **Folytatás** elemet.

        ![Az "üdvözlő Azure AD Connect" oldal](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Válassza az **expressz beállítások használata** lehetőséget, és fejezze be a telepítést.

        ![Azure AD Connect expressz beállítások](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Szinkronizálás konfigurálása a helyszíni tartományvezérlővel

1. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure ad globális rendszergazdájának felhasználónevét és jelszavát. Használja a Active Directory `fabrikamazureadmin@hdifabrikam.com` bérlő konfigurálásakor létrehozott felhasználónevet. Ezután válassza a **tovább**lehetőséget. 

    ![A "kapcsolódás az Azure AD szolgáltatáshoz" oldal](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. A **kapcsolódás Active Directory tartományi szolgáltatások** lapon adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Használja a korábban `HDIFabrikam\HDIFabrikamAdmin` létrehozott felhasználónevet és jelszót. Ezután válassza a **tovább**lehetőséget. 

   ![A "kapcsolódás az Azure AD szolgáltatáshoz" oldal](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Az **Azure ad bejelentkezési konfigurációja** lapon válassza a **tovább**lehetőséget.
   ![Az "Azure AD bejelentkezési konfigurációja" oldal](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. A **konfigurálásra kész** lapon válassza a **telepítés**lehetőséget.

   ![A "készen áll a konfigurálásra" oldal](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. A **konfiguráció kész** lapon válassza a **Kilépés**lehetőséget.
   ![A "konfigurálás befejeződött" lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. A szinkronizálás befejeződése után ellenőrizze, hogy a IaaS könyvtárban létrehozott felhasználók szinkronizálva vannak-e az Azure AD-vel.
   1. Jelentkezzen be az Azure portálra.
   1. Válassza **Azure Active Directory** > HDIFabrikam- > **felhasználók**lehetőséget.

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, amely a Azure AD Domain Services (Azure AD DS) konfigurálására használható. További információ: [Létrehozás, Listázás, törlés vagy a szerepkör hozzárendelése egy felhasználó által hozzárendelt felügyelt identitáshoz a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Jelentkezzen be az Azure portálra.
1. Válassza **az erőforrás létrehozása** és a *felügyelt identitás*típusa lehetőséget. Válassza a **felhasználóhoz rendelt felügyelt identitás** > **létrehozása**elemet.
1. Az **Erőforrás neve**mezőbe írja be a következőt: *HDIFabrikamManagedIdentity*.
1. Válassza ki előfizetését.
1. Az **erőforráscsoport**területen válassza az **új létrehozása** elemet, és adja meg a *HDIFabrikam-CentralUS*értéket.
1. A hely területen válassza ki az **USA középső** **régióját**.
1. Kattintson a **Létrehozás** gombra.

![új, felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure-AD DS engedélyezése

Az Azure AD DS engedélyezéséhez kövesse az alábbi lépéseket. További információ: [Az Azure AD DS engedélyezése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Hozzon létre egy virtuális hálózatot az Azure AD DS üzemeltetéséhez. Futtassa a következő PowerShell-kódot.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Jelentkezzen be az Azure portálra.
1. Válassza az **erőforrás létrehozása**lehetőséget, írja be a *tartományi szolgáltatások*elemet, majd válassza a **Azure ad Domain Services**lehetőséget.
1. Az **alapok** oldalon:
    1. A **címtár neve**területen válassza ki a létrehozott Azure ad-könyvtárat: **HDIFabrikam**.
    1. A **DNS-tartománynév**mezőben adja meg a *HDIFabrikam.com*nevet.
    1. Válassza ki előfizetését.
    1. Határozza meg az **HDIFabrikam-CentralUS**erőforráscsoportot. A hely mezőben válassza az **USA középső** **régiója**lehetőséget.

        ![Azure AD DS – alapszintű részletek](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. A **hálózat** lapon válassza ki a hálózatot (**HDIFabrikam-VNET**) és az alhálózatot (**AADDS-alhálózat**), amelyet a PowerShell-parancsfájl használatával hozott létre. Vagy válassza az **új létrehozása** lehetőséget egy virtuális hálózat létrehozásához.

    ![A "virtuális hálózat létrehozása" lépés](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. A **Rendszergazda csoport** lapon értesítést kap arról, hogy egy **HRE DC-rendszergazdák** nevű csoport már létrejött a Csoport felügyeletéhez. Ha szeretné, módosíthatja a csoport tagságát, de ebben az esetben nem kell módosítania. Kattintson az **OK** gombra.

    ![Az Azure AD-rendszergazda csoport megtekintése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. A **szinkronizálás** lapon engedélyezze a teljes szinkronizálást > az**OK gombra**kattintva.

    ![Az Azure AD DS szinkronizálásának engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Az **Összefoglalás** lapon ellenőrizze az Azure-AD DS adatait, majd kattintson **az OK gombra**.

    ![A "Azure AD Domain Services engedélyezése" összegzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Az Azure AD DS engedélyezése után a helyi DNS-kiszolgáló fut az Azure AD-beli virtuális gépeken.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Az Azure AD DS virtuális hálózat konfigurálása

Az alábbi lépésekkel konfigurálhatja Azure AD DS Virtual Network (**HDIFabrikam-AADDSVNET**) használatát az egyéni DNS-kiszolgálók használatára.

1. Keresse meg az egyéni DNS-kiszolgálók IP-címeit. 
    1. Válassza ki a **HDIFabrikam.com** Azure AD DS erőforrást. 
    1. A **kezelés**területen válassza a **Tulajdonságok**lehetőséget. 
    1. Keresse **meg a virtuális hálózat IP-címe**alatt található IP-címeket.

    ![Az Azure AD DS egyéni DNS IP-címeinek megkeresése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Konfigurálja a **HDIFabrikam-AADDSVNET** egyéni IP-címek 10.0.0.4 és 10.0.0.5 használatára.

    1. A **Beállítások**területen válassza a **DNS-kiszolgálók**elemet. 
    1. Válassza az **Egyéni**lehetőséget.
    1. A szövegmezőbe írja be az első IP-címet (*10.0.0.4*).
    1. Válassza a  **Mentés** lehetőséget.
    1. Ismételje meg a lépéseket a másik IP-cím (*10.0.0.5*) hozzáadásához.

A példában az Azure AD DS IP-címek 10.0.0.4 és 10.0.0.5 használatára konfiguráltuk, az Azure AD DS virtuális hálózatban ugyanazt az IP-címet állítja be:

![Az egyéni DNS-kiszolgálók lap](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Az LDAP-forgalom biztonságossá tétele

A Lightweight Directory Access Protocol (LDAP) a Azure Active Directory olvasására és írására szolgál. Az LDAP-forgalmat a SSL (SSL) vagy a Transport Layer Security (TLS) technológia segítségével bizalmasan és biztonságossá teheti. Az LDAP-t SSL (LDAPs) protokollon keresztül is engedélyezheti egy megfelelően formázott tanúsítvány telepítésével.

További információ a biztonságos LDAP-ről: [LDAPS konfigurálása Azure AD DS felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Ebben a szakaszban létrehoz egy önaláírt tanúsítványt, letölti a tanúsítványt, és konfigurálja az **HDIFabrikam** Azure AD DS felügyelt tartományhoz tartozó LDAPS-t.

A következő szkript létrehoz egy tanúsítványt a **HDIFabrikam**számára. A rendszer menti a tanúsítványt a *LocalMachine* útvonalon.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Bármely olyan segédprogram vagy alkalmazás, amely érvényes nyilvános kulcsú kriptográfiai szabványokat ( \#PKCS) tartalmazó 10 kérelmet hoz létre, az SSL-tanúsítványkérelem megírására használható.

Ellenőrizze, hogy a tanúsítvány telepítve van-e a számítógép **személyes** tárolójába:

1. Indítsa el a Microsoft Management Console (MMC) programot.
1. Adja hozzá a Tanúsítványok beépülő modult, amely a helyi **számítógépen lévő** tanúsítványokat kezeli.
1. Bontsa ki a **tanúsítványok (helyi számítógép)**  > **személyes** > **tanúsítványok**csomópontot. A **személyes** tárolóban léteznie kell egy új tanúsítványnak. Ezt a tanúsítványt a rendszer a teljes állomásnév számára adja ki.

    ![Helyi tanúsítvány létrehozásának ellenőrzése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. A jobb oldali ablaktáblán kattintson a jobb gombbal a létrehozott tanúsítványra. Mutasson a **minden feladat**elemre, majd válassza az **Exportálás**lehetőséget.

1. A **titkos kulcs** exportálása lapon válassza **az igen, majd a titkos kulcs exportálása**lehetőséget. A számítógépnek, amelyre a kulcsot importálni kívánja, szüksége lesz a titkos kulcsra a titkosított üzenetek olvasásához.

    ![A tanúsítvány exportálása varázsló titkos kulcs exportálása lapja](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Az **Exportálás fájlformátuma** lapon hagyja meg az alapértelmezett beállításokat, majd kattintson a **tovább**gombra. 
1. A **jelszó** lapon adja meg a titkos kulcs jelszavát. A **titkosításhoz**válassza a **TripleDES-SHA1**elemet. Ezután kattintson a **Tovább** gombra.
1. Az exportálandó **fájl** lapon adja meg az exportált tanúsítványfájl elérési útját és nevét, majd kattintson a **tovább**gombra. A fájl nevének. pfx kiterjesztéssel kell rendelkeznie. Ezt a fájlt a Azure Portal konfigurálja biztonságos kapcsolat létesítéséhez.
1. Azure AD DS felügyelt tartományhoz tartozó LDAP-szolgáltatás engedélyezése.
    1. A Azure Portal válassza ki a tartomány **HDIFabrikam.com**.
    1. A **kezelés**területen válassza a **Secure LDAP**lehetőséget.
    1. A **Secure LDAP** lapon a **Secure LDAP**területen válassza az **Engedélyezés**lehetőséget.
    1. Tallózással keresse meg a számítógépen exportált. pfx tanúsítványfájl-fájlt.
    1. Adja meg a tanúsítvány jelszavát.

    ![Biztonságos LDAP engedélyezése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Most, hogy engedélyezte az LDAPs szolgáltatást, ellenőrizze, hogy elérhető-e a 636-es port engedélyezésével.
    1. A **HDIFabrikam-CentralUS** erőforráscsoport területen válassza a **AADDS-HDIFabrikam.com-NSG**hálózati biztonsági csoportot.
    1. A **Beállítások**területen válassza a **bejövő biztonsági szabályok** > **Hozzáadás**lehetőséget.
    1. A **bejövő biztonsági szabály hozzáadása** lapon adja meg a következő tulajdonságokat, majd válassza a **Hozzáadás**lehetőséget:

        | Tulajdonság | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Kívánt szám > |
        | Name (Név) | Port_LDAP_636 |

    ![A "bejövő biztonsági szabály hozzáadása" párbeszédpanel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

A **HDIFabrikamManagedIdentity** a felhasználó által hozzárendelt felügyelt identitás. A HDInsight tartományi szolgáltatások közreműködői szerepköre engedélyezve van a felügyelt identitás számára, amely lehetővé teszi, hogy ez az identitás beolvassa, létrehozza, módosítsa és törölje a tartományi szolgáltatások műveleteit.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP-kompatibilis HDInsight-fürt létrehozása

Ehhez a lépéshez a következő előfeltételek szükségesek:

1. Hozzon létre egy új erőforráscsoportot a *HDIFabrikam-WestUS* az **USA nyugati**régiójában.
1. Hozzon létre egy virtuális hálózatot, amely az ESP-kompatibilis HDInsight-fürtöt fogja tárolni.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Hozzon létre egy társ-kapcsolatot az Azure AD DS (`HDIFabrikam-AADDSVNET`) és az ESP-kompatibilis HDInsight-fürtöt (`HDIFabrikam-HDIVNet`) futtató virtuális hálózat között. Használja a következő PowerShell-kódot a két virtuális hálózat társításához.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Hozzon létre egy új, **Hdigen2store**nevű Azure Data Lake Storage Gen2 fiókot. Konfigurálja a fiókot a felhasználó által felügyelt identitás **HDIFabrikamManagedIdentity**. További információ: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Állítsa be az egyéni DNS-t a **HDIFabrikam-AADDSVNET** virtuális hálózaton.
    1. Nyissa meg a**OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-kiszolgálók**Azure Portal > **erőforráscsoportot** > .
    1. Válassza az **Egyéni** lehetőséget, majd adja meg a *10.0.0.4* és a *10.0.0.5*.
    1. Kattintson a **Mentés** gombra.

        ![Virtuális hálózat egyéni DNS-beállításainak mentése](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Hozzon létre egy új ESP-kompatibilis HDInsight Spark-fürtöt.
    1. Válassza **az egyéni (méret, beállítások, alkalmazások)** lehetőséget.
    2. Adja meg az **alapismeretek** részleteit (1. szakasz). Győződjön meg arról, hogy a **fürt típusa** **Spark 2,3 (HDI 3,6)** . Győződjön meg arról, hogy az **erőforráscsoport** **HDIFabrikam-CentralUS**.

    1. A **Biztonság és hálózatkezelés** (2. szakasz) esetében adja meg a következő adatokat:
        * Az **Enterprise Security Package**területen válassza az **engedélyezve**lehetőséget.
        * Válassza ki a **fürt rendszergazdai felhasználóját** , és válassza ki a helyi rendszergazdaként létrehozott **HDIAdmin** -fiókot. Kattintson a **Kiválasztás** gombra.
        * Válassza ki a **fürt hozzáférési csoportjának** > **HDIUserGroup**. A jövőben a csoportba felvett összes felhasználó hozzáférhet a HDInsight-fürtökhöz.

            ![Válassza ki a fürt hozzáférési csoportjának HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Fejezze be a fürtkonfiguráció további lépéseit, és ellenőrizze a **fürt összegzésének**részleteit. Kattintson a **Létrehozás** gombra.

1. Jelentkezzen be az újonnan létrehozott fürt `https://CLUSTERNAME.azurehdinsight.net`Ambari felhasználói felületén. Használja a rendszergazdai felhasználónevét `hdiadmin@hdifabrikam.com` és jelszavát.

    ![Az Apache Ambari felhasználói felületének bejelentkezési ablaka](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. A fürt irányítópultján válassza a **szerepkörök**lehetőséget.
1. A **szerepkörök** lapon a **szerepkörök társítása**a **fürt rendszergazdai** szerepköre mellett adja meg a *hdiusergroup*csoportot. 

    ![A fürt rendszergazdai szerepkörének társítása a hdiusergroup-hez](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Nyissa meg a Secure Shell-(SSH-) ügyfelet, és jelentkezzen be a fürtbe. Használja a helyszíni Active Directory-példányban létrehozott **hdiuser** .

    ![Bejelentkezés a fürtbe az SSH-ügyfél használatával](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Ha be tud jelentkezni ezzel a fiókkal, megfelelően konfigurálta az ESP-fürtöt a helyszíni Active Directory-példánnyal való szinkronizálásra.

## <a name="next-steps"></a>További lépések

[Bemutatjuk az ESP-vel való Apache Hadoop biztonság bevezetését](hdinsight-security-overview.md).
