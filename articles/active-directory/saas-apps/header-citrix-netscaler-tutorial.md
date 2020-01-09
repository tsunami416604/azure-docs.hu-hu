---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Citrix NetScaler (fejléc alapú hitelesítéssel) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Citrix NetScaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472162"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Citrix NetScaler (fejléc alapú hitelesítéssel)

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Citrix NetScaler Azure Active Directory (Azure AD) használatával. Ha a Citrix NetScaler-t az Azure AD-vel integrálja, a következőket teheti:

* A Citrix NetScaler elérését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Citrix NetScaler az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Citrix NetScaler egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Citrix NetScaler támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

* A Citrix NetScaler **csak időben támogatja a** felhasználók üzembe helyezését

- [A Citrix NetScaler egyszeri bejelentkezés konfigurálása a fejléc-alapú hitelesítéshez](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Citrix NetScaler egyszeri bejelentkezés konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>Citrix NetScaler hozzáadása a katalógusból

A Citrix NetScaler az Azure AD-be való integrálásának konfigurálásához a katalógusból Citrix NetScaler kell hozzáadnia a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Citrix NetScaler** kifejezést a keresőmezőbe.
1. Válassza ki a **Citrix NetScaler** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Citrix NetScaler-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Citrix NetScaler egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Citrix NetScaler-ben.

Az Azure AD SSO és a Citrix NetScaler konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Citrix NETSCALER SSO konfigurálása](#configure-citrix-netscaler-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Citrix NetScaler-teszt felhasználót](#create-citrix-netscaler-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel a Citrix NetScaler.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Citrix NetScaler** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<<Your FQDN>>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez lépjen kapcsolatba a [Citrix NetScaler ügyfél-támogatási csapatával](https://www.citrix.com/contact/technical-support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

    > [!NOTE]
    > Az SSO működésének beszerzéséhez ezeknek az URL-címeknek elérhetőnek kell lenniük a nyilvános webhelyekről. Engedélyeznie kell a tűzfalat vagy más biztonsági beállításokat a NetScaler oldalon ahhoz, hogy enble az Azure AD-t, hogy elküldhesse a tokent a konfigurált ACS URL-címen.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **alkalmazás-összevonási metaadatok URL-címét**, másolja ki az URL-címet, és mentse a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Citrix NetScaler alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra, és módosítsa az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Citrix NetScaler alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | mySecretID  | User. userPrincipalName |
    
    1. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névteret** .

    1. Válassza a forrás **attribútumként**lehetőséget.

    1. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson **az OK** gombra

    1. Kattintson a **Mentés** gombra.


1. A **Citrix NetScaler beállítása** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a Citrix NetScaler elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Citrix NetScaler**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-citrix-netscaler-sso"></a>A Citrix NetScaler SSO konfigurálása

- [A Citrix NetScaler egyszeri bejelentkezés konfigurálása a fejléc-alapú hitelesítéshez](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Citrix NetScaler egyszeri bejelentkezés konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Webkiszolgáló közzététele 

1. Hozzon létre egy **virtuális kiszolgálót**.

    a. Lépjen a **Traffic Management > terheléselosztás > szolgáltatások szolgáltatáshoz**.
    
    b. Kattintson a **Hozzáadás** parancsra.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/web01.png)

    c. Határozza meg az alábbi alkalmazásokat futtató webkiszolgáló adatait:
    * **Szolgáltatás neve**
    * **Kiszolgáló IP-címe/meglévő kiszolgáló**
    * **Protocol (Protokoll)**
    * **Port**

     ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Load Balancer konfigurálása

1. Load Balancer konfigurálásához hajtsa végre a következő lépéseket:

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/load01.png)

    a. Lépjen a **Traffic Management > terheléselosztás > virtuális kiszolgálók**szolgáltatáshoz.

    b. Kattintson a **Hozzáadás** parancsra.

    c. Határozza meg az alábbi adatokat:

    * **Name (Név)**
    * **Protocol (Protokoll)**
    * **IP-cím**
    * **Port**
    * Kattintson **az OK** gombra

### <a name="bind-virtual-server"></a>Kötési virtuális kiszolgáló

Kösse a Load Balancert a korábban létrehozott virtuális kiszolgálóval.

![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Kötési tanúsítvány

Mivel ezt a szolgáltatást SSL-Kötésként tesszük közzé, majd teszteljük az alkalmazást.

![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

### <a name="create-authentication-policy"></a>Hitelesítési házirend létrehozása

1. Nyissa meg a **biztonsági > AAA – alkalmazás-adatforgalom > házirendek > a hitelesítési > hitelesítési szabályzatok**lehetőséget.

2. Kattintson a **Hozzáadás** gombra, majd adja meg a részleteket.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. A **hitelesítési házirend**neve.

    b. Kifejezés: **true**.

    c. **SAML**-Művelettípus.

    d. Művelet = kattintson a **Hozzáadás** gombra (kövesse a hitelesítés SAML-kiszolgáló létrehozása varázslót).
    
    e. Kattintson a Létrehozás elemre a **hitelesítési házirendben**.

### <a name="create-authentication-saml-server"></a>Hitelesítési SAML-kiszolgáló létrehozása

1. Hajtsa végre a következő lépéseket:

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/server01.png)

    a. Adja meg a **nevet**.

    b. Metaadatok importálása (az Azure SAML felhasználói felületről átmásolt összevonási metaadatokat tartalmazó URL-cím megadása).
    
    c. Adja meg a **kiállító nevét**.

    d. Kattintson a **Létrehozás**gombra.

### <a name="create-authentication-virtual-server"></a>Hitelesítési virtuális kiszolgáló létrehozása

1.  Lépjen a **biztonság > AAA-alkalmazás forgalmára > > a hitelesítési virtuális kiszolgálók**elemre.

2.  Kattintson a **Hozzáadás** gombra, és hajtsa végre a következő lépéseket:

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  Adjon meg egy **nevet**.

    b.  Válassza a **nem címezhető**lehetőséget.

    c.  Protokoll **SSL**.

    d.  Kattintson az **OK** gombra.

    e.  Kattintson a **Folytatás** gombra.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>A hitelesítési virtuális kiszolgáló konfigurálása az Azure AD használatára

Módosítania kell a hitelesítési virtuális kiszolgáló 2 részét.

1.  **Speciális hitelesítési házirendek**

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. Válassza ki a korábban létrehozott **hitelesítési házirendet** .

    b. Kattintson a **kötés**elemre.

      ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **Űrlap alapú virtuális kiszolgálók**

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  A felhasználói felület által kényszerített **teljes tartománynevet** kell megadnia.

    b.  Válassza ki azt a **virtuális kiszolgálót Load Balancer** , amelyet az Azure ad-hitelesítéssel szeretne védelemmel ellátni.

    c.  Kattintson a **kötés**elemre.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Győződjön meg arról, hogy a hitelesítés virtuális kiszolgáló konfigurációja lapon a **kész** gombra kattint.

3. Ellenőrizze a módosításokat. Tallózással keresse meg az alkalmazás URL-címét. A korábban nem hitelesített hozzáférés helyett a bérlő bejelentkezési lapját kell látnia.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>A Citrix NetScaler egyszeri bejelentkezés konfigurálása a fejléc-alapú hitelesítéshez

### <a name="citrix-adc-configuration"></a>Citrix ADC-konfiguráció

### <a name="create-an-rewrite-action"></a>Újraírási művelet létrehozása

1. Ugrás a **AppExpert > > Újraírási műveleteinek újraírása**.
 
    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header01.png)

2.  Kattintson a **Hozzáadás** parancsra.

    a.  Adja meg a **nevet**.

    b.  Type = **INSERT_HTTP_HEADER**.

    c.  Adja meg a **fejléc nevét** (ebben a példában a SecretID).

    d.  Adja meg az **AAA kifejezést. Felhasználói. ATTRIBÚTUM (`mySecretID`)** , ahol a **MySecretID** a Citrix ADC-nek eljuttatott Azure ad SAML-jogcím.

    e.  Kattintson a **Create** (Létrehozás) gombra.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>Hozzon létre egy Újraírási szabályzatot.

1.  Ugrás a **AppExpert > > Újraírási házirendjeinek újraírása**.
 
    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header03.png)

2.  Kattintson a **Hozzáadás** parancsra.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  Adja meg a **nevet**.

    b.  Válassza ki a korábban létrehozott **műveletet** .

    c. A kifejezés **igaz**értéket ad meg.

    d.  Kattintson a **Létrehozás**gombra.

### <a name="bind-rewrite-policy-to-virtual-servers"></a>Újraírási házirend kötése virtuális kiszolgálókhoz

1. Egy Újraírási házirend kötése egy adott virtuális kiszolgálóhoz a grafikus felhasználói felület használatával.

2. Navigáljon a **Traffic Management > terheléselosztás > virtuális kiszolgálók**között.

3. A virtuális kiszolgálók részletek ablaktáblájának listájában válassza ki azt a **virtuális kiszolgálót** , amelyhez az Újraírási házirendet kötni kívánja, majd kattintson a **Megnyitás**gombra.

4. A virtuális kiszolgáló konfigurálása (terheléselosztás) párbeszédpanelen válassza a **házirendek** lapot. A NetScaler konfigurált összes házirend megjelenik a listában.
 
    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header06.png)

5.  Jelölje be a virtuális kiszolgálóhoz kötni kívánt szabályzat neve melletti **jelölőnégyzetet** .

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  Kattintson az **OK** gombra. Az állapotsorban megjelenik egy üzenet, amely azt jelzi, hogy a házirend konfigurálása sikeres volt.

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>SAML-kiszolgáló módosítása a jogcím attribútumainak kinyeréséhez

1.  Lépjen a **biztonság > AAA-alkalmazás forgalmára > házirendek > hitelesítés > speciális házirendek > műveletek >-kiszolgálók**elemre.

2.  Válassza ki a megfelelő **hitelesítési SAML-kiszolgálót** az alkalmazáshoz.
 
    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header09.png)

3. Az attribútumok szakaszban írja be a "," használatával kinyerni kívánt SAML-attribútumokat. Ebben az esetben a **mySecretID**nevű attribútumot adjuk meg.
 
    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header10.png)

4. Ellenőrizze az alkalmazások elérését.

    ![Citrix NetScaler-konfiguráció](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Citrix NetScaler-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Citrix NetScaler. A Citrix NetScaler az igény szerinti felhasználói üzembe helyezést támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Citrix NetScaler-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Citrix NetScaler ügyfél-támogatási csapatához](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Citrix NetScaler csempére kattint, automatikusan be kell jelentkeznie a Citrix NetScaler, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Citrix NetScaler kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Citrix NetScaler egyszeri bejelentkezés konfigurálása Kerberos-alapú hitelesítéshez](citrix-netscaler-tutorial.md)
