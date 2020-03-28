---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az AirWatch-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AirWatch között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 772b37816b83c275bae927d825434dc3ca76a35c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231993"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Oktatóanyag: Az AirWatch integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AirWatchot az Azure Active Directoryval (Azure AD). Ha integrálja az AirWatch-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá az AirWatchhoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve legyenek az AirWatch-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* AirWatch egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Az AirWatch támogatja az **SP** által kezdeményezett SSO-t.

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch hozzáadása a galériából

Az AirWatch Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az AirWatch-ot a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be az **AirWatch** kifejezést a keresőmezőbe.
1. Válassza az **AirWatch** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az AirWatch segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az AirWatch-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az AirWatch segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az AirWatch SSO-t](#configure-airwatch-sso)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Hozzon létre AirWatch teszt felhasználó](#create-airwatch-test-user)** -, hogy egy megfelelője Britta Simon az AirWatch, amely kapcsolódik az Azure AD felhasználói ábrázolása.
5. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **AirWatch** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Az **Azonosító (entitásazonosító)** mezőbe írja be az értéket:`AirWatch`

    > [!NOTE]
    > Ez az érték nem az igazi. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték értéshez lépjen kapcsolatba az [AirWatch ügyféltámogatási csapatával.](https://www.air-watch.com/company/contact-us/) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az AirWatch alkalmazás az SAML-állításokat egy adott formátumban várja. Konfigurálja az alkalmazás következő jogcímeket. Ezeknek az attribútumoknak az értékeit az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszában kezelheti. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában a **Szerkesztés ikon** használatával szerkesztse a jogcímeket, vagy adja hozzá a jogcímeket az **Új jogcím hozzáadása** paragrafussal az SAML token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Név |  Forrás attribútuma|
    |---------------|----------------|
    | Uid | user.userprincipalname |
    | | |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** keresse meg az **összevonási metaadat-XML-t,** és válassza a **Letöltés** lehetőséget a metaadat-XML letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **AirWatch beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Az AirWatch sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AirWatch vállalati webhelyére rendszergazdaként.

1. A beállítások lapon. Válassza **a Vállalati integráció > beállítások > a címtárszolgáltatások lehetőséget.**

   ![Beállítások](./media/airwatch-tutorial/ic791921.png "Beállítások")

1. Kattintson a **Felhasználó** fülre, az **Alap dn** szövegmezőbe írja be a tartomány nevét, majd kattintson a **Mentés gombra.**

   ![Felhasználó](./media/airwatch-tutorial/ic791922.png "Felhasználó")

1. Kattintson a **Kiszolgáló** fülre.

   ![Kiszolgáló](./media/airwatch-tutorial/ic791923.png "Kiszolgáló")

1. Hajtsa végre az alábbi lépéseket az **LDAP** szakaszban:

    ![Feltöltés](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. **Címtártípusként**válassza a **Nincs**lehetőséget.

    b. Válassza **az SAML használata hitelesítéshez**lehetőséget.

1. Az **SAML 2.0** szakaszban a letöltött tanúsítvány feltöltéséhez kattintson a **Feltöltés gombra.**

    ![Feltöltés](./media/airwatch-tutorial/ic791932.png "Feltöltés")

1. A **Kérelem** szakaszban hajtsa végre az alábbi lépéseket:

    ![Kérés](./media/airwatch-tutorial/ic791925.png "Kérés")  

    a. **Kötési típusként**válassza a **POST**lehetőséget.

    b. Az Azure Portalon, az **egyetlen bejelentkezés konfigurálása az AirWatch-on** párbeszédpanelen másolja a **bejelentkezési URL-címet,** majd illessze be az **identitásszolgáltató egyetlen bejelentkezési URL-címmezőjébe.**

    c. **NameID formátumként**válassza **az E-mail cím lehetőséget.**

    d. Hitelesítési **kérelem biztonságaként**válassza a **Nincs**lehetőséget.

    e. Kattintson a **Mentés** gombra.

1. Kattintson ismét a **Felhasználó** fülre.

    ![Felhasználó](./media/airwatch-tutorial/ic791926.png "Felhasználó")

1. Az **Attribútum szakaszban** hajtsa végre az alábbi lépéseket:

    ![Attribútum](./media/airwatch-tutorial/ic791927.png "Attribútum")

    a. Az **Objektumazonosító** szövegmezőbe `http://schemas.microsoft.com/identity/claims/objectidentifier`írja be a következőt:

    b. A **Felhasználónév** mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`be a következőt:

    c. A **Megjelenítendő név** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`mezőbe írja be a következőt:

    d. Az **Utónév** mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`be a következőt:

    e. A **Vezetéknév** mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`be a következőt:

    f. Az **E-mail** mezőbe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`írja be a következőt:

    g. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t az AirWatch hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **AirWatch**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-airwatch-test-user"></a>AirWatch tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az AirWatchszolgáltatásba, be kell őket építeni az AirWatchba. Az AirWatch esetében a kiépítés manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be **az AirWatch** vállalati webhelyére rendszergazdaként.

2. A bal oldali navigációs ablakban kattintson a **Fiókok**, majd a **Felhasználók**elemre.
  
   ![Felhasználók](./media/airwatch-tutorial/ic791929.png "Felhasználók")

3. A **Felhasználók menüben** kattintson a **Listanézet**menü **Felhasználó hozzáadása > parancsra.**
  
   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791930.png "Felhasználó hozzáadása")

4. A **Felhasználó hozzáadása/szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791931.png "Felhasználó hozzáadása")

   a. Írja be a **felhasználónevet**, **jelszó**, **jelszó megerősítése**, **vezetéknév**, **Last Name** **e-mail-cím** egy érvényes Azure Active Directory-fiók kiépíteni a kapcsolódó szövegdobozok.

   b. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az AirWatch által biztosított bármely más AirWatch felhasználói fiók-létrehozási eszközzel vagy API-val azure AD-felhasználói fiókokat hozhat létre.

### <a name="test-sso"></a>SSO tesztelése

Amikor a hozzáférési panelen kiválasztja az AirWatch csempét, automatikusan be kell jelentkeznie az AirWatchba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
