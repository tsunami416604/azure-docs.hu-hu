---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a TOPdesk - Secure | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TOPdesk – Secure között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 393557531fe69a494a16e0f4c49ac07891e490ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233403"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Az Azure Active Directory integrációja a TOPdeskkel – Biztonságos

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TOPdesk – Biztonságos az Azure Active Directoryval (Azure AD).
A TOPdesk integrálása – A Biztonságos az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TOPdesk – Secure szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a TOPdesk – Secure (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a TOPdesk – Secure szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* TOPdesk – Biztonságos egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* TOPdesk - Secure támogatja **sp** kezdeményezett Egyszeri szolgáltató

## <a name="adding-topdesk---secure-from-the-gallery"></a>TOPdesk hozzáadása - Biztonságos a galériából

A TOPdesk – Biztonságos az Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a TOPdesk - Secure-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**ToPdesk - Biztonságos hozzáadása a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TOPdesk - Secure**parancsot, válassza a **TOPdesk - Secure** from result panel parancsot, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![TOPdesk - Biztonságos az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a TOPdesk - Secure nevű tesztfelhasználó alapján **Britta Simon.**
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a TOPdesk – Secure kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a TOPdesk – Secure szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a TOPdesk - Secure Single Sign-On](#configure-topdesk---secure-single-sign-on)** --t az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre TOPdesk - Secure teszt felhasználó](#create-topdesk---secure-test-user)** -, hogy egy megfelelője Britta Simon a TOPdesk - Biztonságos, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a TOPdesk – Secure szolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **TOPdesk – Biztonságos** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![TOPdesk – Biztonságos tartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.topdesk.net`

    b. Az **azonosító URL-címe** mezőben töltse ki a TOPdesk metaadat-URL-címét, amelyet a TOPdesk-konfigurációból bekereshet. Meg kell használni a következő mintát:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a TOPdesk – Secure Client támogatási csapatával,](https://www.topdesk.com/us/support/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **TOPdesk beállítása - Biztonságos** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-topdesk---secure-single-sign-on"></a>ToPdesk konfigurálása – Egyszeri bejelentkezés biztonságossá tétele

1. Jelentkezzen be a **TOPdesk – Biztonságos** vállalati webhelyre rendszergazdaként.

2. A **TOPdesk** menüben kattintson a **Beállítások gombra.**

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "Beállítások")

3. Kattintson **a Bejelentkezési beállítások gombra.**

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "Bejelentkezési beállítások")

4. Bontsa ki a **Bejelentkezési beállítások** menüt, majd kattintson az **Általános**parancsra.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "Általános kérdések")

5. Az **SAML bejelentkezési** konfigurációs szakasz **Biztonságos** szakaszában hajtsa végre az alábbi lépéseket:

    ![Műszaki beállítások](./media/topdesk-secure-tutorial/ic790855.png "Műszaki beállítások")

    a. Kattintson a **Letöltés** gombra a nyilvános metaadatfájl letöltéséhez, majd mentse helyileg a számítógépre.

    b. Nyissa meg a metaadatfájlt, és keresse meg a **AssertionConsumerService** csomópontot.

    ![Helyességi szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "Helyességi szolgáltatás")

    c. Másolja a **Fájlta Feltételfeltétel szolgáltatás** értékére, illessze be ezt az értéket a **ToPdesk – Biztonságos tartomány és URL-címek** válaszURL-mezőjébe.

6. Tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:

    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "Tanúsítvány")

    a. Nyissa meg a letöltött metaadatfájlt az Azure Portalról.

    b. Bontsa ki az **xsi:fed:ApplicationServiceType**típusú **xsi:type** **RoleDescriptor** csomópontot.

    c. Másolja az **X509Certificate** csomópont értékét.

    d. Mentse a másolt **X509Certificate** értéket helyileg a számítógépre egy fájlba.

7. A **Nyilvános** csoportban kattintson a **Hozzáadás gombra.**

    ![Hozzáadás](./media/topdesk-secure-tutorial/ic790607.png "Hozzáadás")

8. Az **SAML konfigurációs segéd** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![SAML konfigurációs segéd](./media/topdesk-secure-tutorial/ic790608.png "SAML konfigurációs segéd")

    a. Ha fel szeretné tölteni a letöltött metaadatfájlt az Azure Portalról, az **Összevonási metaadatok**csoportban kattintson a **Tallózás gombra.**

    b. A tanúsítványfájl feltöltéséhez a **Tanúsítvány (RSA) csoportban**kattintson a **Tallózás gombra.**

    c. A **személyes kulcs (RSA, PKCS8, DER)** esetén feltöltheti saját személyes kulcsát, vagy kapcsolatba léphet a [TOPdesk – Secure Client támogatási csapatával](https://www.topdesk.com/us/support) a személyes kulcs lekérni.

    d. A TOPdesk támogatási csoportjától kapott emblémafájl feltöltéséhez kattintson az **Embléma ikonra,** kattintson a **Tallózás gombra.**

    e. A **Felhasználónév attribútum** szövegmezőbe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`írja be a következőt:

    f. A **Név megjelenítése** mezőbe írja be a konfiguráció nevét.

    g. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés tpdesk - biztonságos hozzáférést biztosít.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **TOPdesk – Secure**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza a **TOPdesk - Secure**lehetőséget.

    ![A TOPdesk - Secure hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk létrehozása – Biztonságos tesztfelhasználó

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a TOPdesk - Secure szolgáltatásba, ki kell építeni őket a TOPdesk – Secure szolgáltatásba.  
A TOPdesk - Secure esetében a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk - Biztonságos** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk \> Új \> támogatási fájlok \> operátora parancsra.**

    ![Művelet](./media/topdesk-secure-tutorial/ic790610.png "Művelet")

3. Az **Új operátor** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Új operátor](./media/topdesk-secure-tutorial/ic790611.png "Új operátor")

    a. Kattintson az **Általános** fülre.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Válasszon **egy webhelyet** a fiókhoz a **Hely** szakaszban.

    d. A **TOPdesk bejelentkezési** szakasz **Bejelentkezési név** mezőjébe írja be a felhasználó bejelentkezési nevét.

    e. Kattintson a **Mentés** gombra.

> [!NOTE]
> Bármely más TOPdesk - Biztonságos felhasználói fiók létrehozási eszközök vagy API-k által biztosított TOPdesk - Biztonságos azure AD felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a TOPdesk - Secure csempére kattint, automatikusan be kell jelentkeznie a TOPdesk - Secure rendszerbe, amelyhez beállítja az Egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

