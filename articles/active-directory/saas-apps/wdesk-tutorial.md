---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Wdeskkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Wdesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 349a9920adc0d9353ee8e8e821f54bc2ea2f2616
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666994"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Wdeskkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Wdesket az Azure Active Directoryval (Azure AD). Ha integrálja a Wdesket az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Wdeskhez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Wdesk az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Wdesk egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Wdesk támogatja az **SP-t** és az **IDP** által kezdeményezett SSO-t
* A Wdesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>A Wdesk hozzáadása a galériából

A Wdesk Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Wdesket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a katalógusból szakaszban** írja be a **Wdesk** kifejezést a keresőmezőbe.
1. Válassza a **Wdesk** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Wdeskkel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a wdeski kapcsolódó felhasználó közötti kapcsolat létre kell hozni.

Az Azure AD SSO wdesk-del való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Wdesk Egyszeri bejelentkezést](#configure-wdesk-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Wdesk-tesztfelhasználót](#create-wdesk-test-user)** – b.Simon megfelelőjének a Wdeskben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Wdesk-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Wdesk-tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Wdesk-tartomány és URL-címek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezeket az értékeket a WDesk portálról kapja meg az SSO konfigurálásakor.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Wdesk beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést a Wdesk hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd a **Wdesk**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Wdesk**lehetőséget.

    ![A Wdesk hivatkozás a Jelentkezések listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-wdesk-sso"></a>A Wdesk sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Wdeskbe biztonsági rendszergazdaként.

2. A bal alsó sarokban kattintson a **Rendszergazda** elemre, és válassza **a Fiókadminisztrátor**lehetőséget:
 
     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. A Wdesk Admin területen keresse meg a **Biztonság**, majd **az SAML** > **SAML beállítások :**

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Az **SAML felhasználói azonosító beállításai csoportban**ellenőrizze, **hogy az SAML-felhasználóazonosító Wdesk-felhasználónév-azonosító.**

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/wdesk-username.png)

4. Az **Általános beállítások csoportban**jelölje be az **SAML egyszeri bejelentkezés engedélyezése jelölőnégyzetet:**

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. A **Szolgáltató adatai csoportban**hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Másolja a **bejelentkezési URL-címet,** és illessze be **a bejelentkezési URL-cím** beszövegbe az Azure Portalon.
   
      b. Másolja a **metaadat-url-címet,** és illessze be **az Azure** Portal azonosító szövegmezőjébe.
       
      c. Másolja a **fogyasztói url-címet,** és illessze be a **Válasz URL-cím** szövegmezőjébe az Azure Portalon.
   
      d. A módosítások mentéséhez kattintson a **Mentés** az Azure Portalon gombra.      

6. Az **IdP-beállítások konfigurálása** gombra kattintva nyissa meg **az IdP-beállítások szerkesztése párbeszédpanelt.** Kattintson **a Fájl kiválasztása elemre** az Azure Portalról mentett **Metadata.xml** fájl megkereséséhez, majd töltse fel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Kattintson **a Módosítások mentése gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesk-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Wdeskbe, ki kell építeni őket a Wdeskbe. A Wdesk ben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Wdeskbe biztonsági rendszergazdaként.

2. Nyissa meg a > **Rendszergazdafiók-rendszergazda lehetőséget.** **Admin**

     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kattintson **a Tagok** elemre a **Személyek csoportban.**

4. Most kattintson **a Tag hozzáadása** gombra a Tag **hozzáadása** párbeszédpanel megnyitásához. 
   
    ![Azure AD-tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser1.png)  

5. A **Felhasználó** szövegmezőbe írja be a b.simon@contoso.com felhasználó felhasználónevét, és kattintson a **Folytatás** gombra.

    ![Azure AD-tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser3.png)

6.  Adja meg a részleteket az alábbiak szerint:
  
    ![Azure AD-tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser4.png)
 
    a. Az **E-mail** mezőbe írja be a b.simon@contoso.comfelhasználó e-mail címét, például .

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B.**

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

7. Kattintson **a Tag mentése** gombra.  

    ![Azure AD-tesztfelhasználó létrehozása](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Wdesk csempére kattint, automatikusan be kell jelentkeznie arra a Wdeskbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)