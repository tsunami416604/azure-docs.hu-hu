---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Alibaba Cloud Service szolgáltatással (szerepköralapú egyszeri bejelentkezés) | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Alibaba Cloud Service (szerepköralapú egyszeri bejelentkezés) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22bec224d185d0306f2b0032aef929f627c910e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77367938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Alibaba Cloud Service szolgáltatással (szerepköralapú egyszeri bejelentkezés)

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Alibaba Cloud Service (szerepköralapú egyszeri bejelentkezés) szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja az Alibaba Cloud Service-t (szerepköralapú egyszeri szolgáltatót) az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér az Alibaba Cloud Service (szerepköralapú egyszeri bejelentkezés) hozzáféréssel rendelkezik.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek az Alibaba Cloud Service (Szerepkör-alapú egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Alibaba Cloud Service (Szerepkör-alapú Egyszeri bejelentkezés) egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Alibaba felhőszolgáltatás (szerepköralapú egyszeri szolgáltató) támogatja az **IDP** által kezdeményezett egyszeri szolgáltatót

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Alibaba felhőszolgáltatás (szerepköralapú egyszeri szolgáltató) hozzáadása a galériából

Az Alibaba Cloud Service (Szerepkör-alapú Egyszeri szolgáltató) Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Alibaba Cloud Service (szerepköralapú egyszeri szolgáltató) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be az **Alibaba Cloud Service (Szerepkör-alapú Egyszeri bejelentkezés)** kifejezést a keresőmezőbe.
1. Válassza ki **az Alibaba Cloud Service (Szerepkör-alapú Egyszeri szolgáltató)** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.
5. Az **Alibaba felhőszolgáltatás (szerepköralapú egyszeri bejelentkezés)** lapján kattintson a bal oldali navigációs ablak **Tulajdonságok gombjára,** és másolja az **objektumazonosítót,** és mentse a számítógépre későbbi használatra.

    ![Tulajdonságok konfigurációja](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az Alibaba Cloud Service (szerepköralapú egyszeri bejelentkezés) szolgáltatásához

Konfigurálja és tesztelje az Azure AD Egyszeri szolgáltatást az Alibaba Cloud Service (Szerepköralapú egyszeri szolgáltató) szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Alibaba Cloud Service (szerepköralapú egyszeri szolgáltató) szolgáltatásban.

Az Azure AD Egyszeri szolgáltatás konfigurálásához és teszteléséhez az Alibaba Felhőszolgáltatással (szerepköralapú egyszeri szolgáltató) a következő építőelemeket hajtsa végre:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja a szerepköralapú egyszeri bejelentkezést az Alibaba Cloud Service szolgáltatásban](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** , hogy a felhasználók használhassák ezt a funkciót.
    1. **[Konfigurálja az Alibaba Cloud Service (Role-based SSO) Egyszeri bejelentkezés](#configure-alibaba-cloud-service-role-based-sso-sso)** t – az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
    1. **[Hozzon létre Alibaba Cloud Service (Role-based SSO) teszt felhasználó](#create-alibaba-cloud-service-role-based-sso-test-user)** – egy megfelelője Britta Simon az Alibaba Cloud Service (szerepkör-alapú Egyszeri bejelentkezés), amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[Egyszeri egyszeri egyszeri sso](#test-sso)** tesztelése - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Alibaba Cloud Service (Szerepkör-alapú Egyszeri bejelentkezés)** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    >[!NOTE]
    >A Szolgáltató metaadatait ebből az [URL-címből](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) kapja meg

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    c. A metaadatfájl sikeres feltöltése után az **Azonosító** és **a Válasz URL-értéke** automatikusan feltöltődik az Alibaba Cloud Service (Role-based SSO) szakasz szövegmezőjébe:

    > [!Note]
    > Ha az **azonosító** és a **válasz URL-értékei** nem töltődnek ki automatikusan, akkor manuálisan töltse ki az értékeket a követelménynek megfelelően.

1. Az Alibaba cloud service (szerepköralapú egyszeri bejelentkezés) szerepkörök konfigurálását igényli az Azure AD-ben. A szerepkörjogcím előre konfigurálva van, így nem kell konfigurálnia, de továbbra is létre kell hoznia őket az Azure AD-ben ezzel a [cikkel.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Alibaba felhőszolgáltatás (szerepköralapú egyszeri szolgáltató) beállítása (szerepköralapú egyszeri szolgáltató)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t az Alibaba Cloud Service (szerepköralapú egyszeri bejelentkezés) engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Alibaba Cloud Service (Szerepköralapú Egyszeri bejelentkezés) lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** lapon válassza a felhasználói listából az u2 elemet, majd kattintson a **Kijelölés gombra.** Ezután kattintson **a Hozzárendelés gombra.**

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Tekintse meg a hozzárendelt szerepkört, és tesztelje az Alibaba felhőszolgáltatást (szerepköralapú egyszeri szolgáltató).

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >A felhasználó (u2) hozzárendelése után a létrehozott szerepkör automatikusan csatolva lesz a felhasználóhoz. Ha több szerepkört hozott létre, szükség szerint csatolnia kell a megfelelő szerepkört a felhasználóhoz. Ha szeretné megvalósítani a szerepköralapú egyszeri szolgáltató az Azure AD több Alibaba Cloud-fiókok, ismételje meg az előző lépéseket.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Szerepköralapú egyszeri bejelentkezés konfigurálása az Alibaba felhőszolgáltatásában

1. Jelentkezzen be az Alibaba Cloud [RAM konzolra](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) az Account1 használatával.

2. A bal oldali navigációs ablaktáblában válassza az **Egyszeri bejelentkezés**lehetőséget.

3. A **szerepköralapú egyszeri szolgáltatás** lapon kattintson az **IdP létrehozása gombra.**

4. A megjelenített lapon `AAD` írja be az IdP-név mezőbe a leírás megadását a **Megjegyzés** mezőbe, kattintson a **Feltöltés gombra** a korábban letöltött összevonási metaadatfájl feltöltéséhez, majd kattintson az **OK**gombra.

5. Az IdP sikeres létrehozása után kattintson a **RAM-szerepkör létrehozása gombra.**

6. A **RAM szerepkör** neve `AADrole`mezőbe `AAD` írja be a lehetőséget az **IdP kiválasztása** legördülő listából, és kattintson az OK gombra.

    >[!NOTE]
    >A szerepkört szükség szerint is megadhatja. Az IdP és a megfelelő szerepkör létrehozása után azt javasoljuk, hogy mentse az IdP ÉS a szerepkör ARNs későbbi használatra. Az ARN-eket az IdP információs lapon és a szerepkör-információs lapon szerezheti be.

7. Társítsa az Alibaba Cloud RAM szerepkör (ADrole) az Azure AD-felhasználó (u2): A RAM-szerepkör társításához az Azure AD-felhasználó, létre kell hoznia egy szerepkört az Azure AD az alábbi lépéseket követve:

    a. Jelentkezzen be a [Microsoft Graph Explorer programba.](https://developer.microsoft.com/graph/graph-explorer)

    b. Kattintson **a módosítási engedélyekre** a szerepkör létrehozásához szükséges engedélyek beszerzéséhez.

    ![Grafikon konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Válassza ki a következő engedélyeket a listából, és kattintson az **Engedélyek módosítása**gombra az alábbi ábrán látható módon.

    ![Grafikon konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Az engedélyek megadása után jelentkezzen be újra a Graph Explorerprogramba.

    d. A Graph Explorer lapon válassza a **GET** elemet a második legördülő listából az első legördülő listából és a **bétaverzió** t. Ezután `https://graph.microsoft.com/beta/servicePrincipals` írja be a legördülő listák melletti mezőbe, és kattintson a **Lekérdezés futtatása gombra.**

    ![Grafikon konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Ha több könyvtárat használ, a `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` lekérdezés mezőjébe is beírhatja.

    e. A **Válasz előnézete** szakaszban bontsa ki az appRoles tulajdonságot a "Egyszerű" szolgáltatásból későbbi használatra.

    ![Grafikon konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Az appRoles tulajdonságot a `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` lekérdezés mezőjébe való beírással keresheti meg. Vegye figyelembe, hogy az `objectID` az objektum azonosítóját másolt az Azure AD **tulajdonságai** lapról.

    f. Lépjen vissza a Graph Explorer programba, módosítsa a módszert **GET-ről** PATCH-re, illessze be a következő tartalmat a **Kérelem törzsszakaszába,** majd kattintson a **Lekérdezés futtatása gombra:** **PATCH**
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > A `value` az IdP ARN-ek és a RAM konzolon létrehozott szerepkör. Itt szükség szerint több szerepkört is hozzáadhat. Az Azure AD elküldi ezeknek a szerepköröknek az értékét az SAML-válasz jogcímértékeként. Azonban csak a javítási művelet `msiam_access` alkatrésze után adhat hozzá új szerepköröket. A létrehozási folyamat zökkenőmentessé teszi, javasoljuk, hogy használjon azonosítógenerátort, például guid-generátort, hogy valós időben hozzon létre azonosítókat.

    g. Miután a "Szolgáltatás egyszerű" a szükséges szerepkörrel javítva, csatolja a szerepkört az Azure AD-felhasználó (u2) az **oktatóanyag Azure AD-teszt felhasználószakaszának hozzárendelése** lépéseit követve.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Az Alibaba felhőszolgáltatás (szerepköralapú egyszeri szolgáltató) egyszeri szolgáltatójának konfigurálása

Az **Alibaba Cloud Service (Role-based SSO)** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról az [Alibaba Cloud Service (Role-based SSO) támogatási csapatának.](https://www.aliyun.com/service/) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud Service (szerepköralapú egyszeri szolgáltató) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Alibaba Cloud Service (Szerepköralapú Egyszeri bejelentkezés) szolgáltatásban. Együttműködve [alibaba Cloud Service (Szerepkör-alapú Egyszeri bejelentkezés) támogatási csapat](https://www.aliyun.com/service/) a felhasználók hozzáadása az Alibaba Cloud Service (Szerepkör-alapú SSO) platform. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Az előző konfigurációk befejezése után tesztelje az Alibaba Cloud Service (Szerepkör-alapú Egyszeri szolgáltató) szolgáltatást az alábbi lépésekkel:

1. Az Azure Portalon nyissa meg az **Alibaba Felhőszolgáltatás (Szerepköralapú Egyszeri bejelentkezés)** lapot, válassza **az Egyszeri bejelentkezés**lehetőséget, és kattintson a **Teszt**gombra.

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kattintson a **Bejelentkezés az aktuális felhasználóként** elemre.

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. A fiókválasztó lapon válassza az u2 lehetőséget.

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. A következő lap jelenik meg, jelezve, hogy a szerepköralapú egyszeri szolgáltatás sikeres.

    ![Teszt konfiguráció](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Alibaba Felhőszolgáltatást (szerepköralapú egyszeri szolgáltató) az Azure AD-vel](https://aad.portal.azure.com/)

