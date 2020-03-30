---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a TOPdesk - Nyilvános | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TOPdesk – Nyilvános között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71950422"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Oktatóanyag: Az Azure Active Directory integrációja a TOPdesk- nyilvános

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TOPdesk – Nyilvános azure Active Directoryval (Azure AD).
A TOPdesk integrálása – A nyilvános Azure AD a következő előnyökkel jár:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TOPdesk – Nyilvános szolgáltatásokhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a TOPdesk – Nyilvános (Egyszeri bejelentkezés) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a TOPdesk – Public szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* TOPdesk – Nyilvános egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* TOPdesk - Állami támogatás **SP** kezdeményezett Egyszeri szolgáltató

## <a name="adding-topdesk---public-from-the-gallery"></a>TOPdesk hozzáadása - Nyilvános a galériából

A TOPdesk – Nyilvános Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TOPdesk - Nyilvános a katalógusból a felügyelt SaaS-alkalmazások listájához.

**ToPdesk - Nyilvános hozzáadása a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TOPdesk – Public parancsot,** válassza a **TOPdesk – Nyilvános** lehetőséget az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![TOPdesk - Nyilvános az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a TOPdesk - Nyilvános egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat topdesk – nyilvános kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a TOPdesk – Public szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a TOPdesk – Nyilvános egyszeri bejelentkezés](#configure-topdesk---public-single-sign-on)** – konfigurálása az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre TOPdesk - Nyilvános teszt felhasználó](#create-topdesk---public-test-user)** -, hogy egy megfelelője Britta Simon a TOPdesk - Nyilvános, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a TOPdesk – Public szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **TOPdesk – Nyilvános** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4.  Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    >[!NOTE]
    >A Szolgáltató **metaadat-fájlját** a **TOPdesk konfigurálása – Nyilvános egyszeri bejelentkezés** szakaszból kapja, amelyet az oktatóanyag későbbi részében ismertetünk.

    a. Kattintson **a Metaadatfájl feltöltése gombra.**
    
    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **Azonosító** és a **Válasz URL-értékei** automatikusan feltöltődnek az Egyszerű SAML-konfiguráció szakaszban.

    ![TOPdesk – Nyilvános és URL-ek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.topdesk.net`

    e. Az **azonosító URL-címmezőjében** töltse ki a TOPdesk metaadat-URL-címét, amelyet a TOPdesk-konfigurációból bekereshet. Meg kell használni a következő mintát:`https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Ha az **azonosító** és a **válasz URL-értékei** nem lesznek automatikusan feltöltve, manuálisan kell megadnia őket. Az azonosító, kövesse a mintát, mint a fent említett, és megkapja a válasz URL-értékét a **Topdesk konfigurálása – Nyilvános egyszeri bejelentkezés** szakasz, amely később az oktatóanyag ismerteti. A **bejelentkezési URL-érték** nem valós, ezért frissítenie kell az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a TOPdesk – Public Client támogatási csapatával](https://help.topdesk.com/saas/enterprise/user/) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **TOPdesk beállítása – Nyilvános** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-topdesk---public-single-sign-on"></a>ToPdesk konfigurálása – nyilvános egyszeri bejelentkezés

1. Jelentkezzen be a **TOPdesk - Nyilvános** vállalati webhelyre rendszergazdaként.

2. A **TOPdesk** menüben kattintson a **Beállítások gombra.**
   
    ![Beállítások](./media/topdesk-public-tutorial/ic790598.png "Beállítások")

3. Kattintson **a Bejelentkezési beállítások gombra.**
   
    ![Bejelentkezési beállítások](./media/topdesk-public-tutorial/ic790599.png "Bejelentkezési beállítások")

4. Bontsa ki a **Bejelentkezési beállítások** menüt, majd kattintson az **Általános**parancsra.
   
    ![Általános](./media/topdesk-public-tutorial/ic790600.png "Általános kérdések")

5. Az **SAML bejelentkezési** konfigurációs szakasz **Nyilvános** szakaszában hajtsa végre a következő lépéseket:
   
    ![Műszaki beállítások](./media/topdesk-public-tutorial/ic790601.png "Műszaki beállítások")
   
    a. Kattintson a **Letöltés** gombra a nyilvános metaadatfájl letöltéséhez, majd mentse helyileg a számítógépre.
   
    b. Nyissa meg a letöltött metaadatfájlt, és keresse meg a **AssertionConsumerService** csomópontot.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Másolja a Fájlt a **AssertionConsumerService** értékre, és illessze be ezt az értéket az **Egyszerű SAML-konfiguráció** csoport **Válasz URL-mezőjébe.**      
   
6. Tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:
    
    ![Tanúsítvány](./media/topdesk-public-tutorial/ic790606.png "Tanúsítvány")
    
    a. Nyissa meg a letöltött metaadatfájlt az Azure Portalról.
    
    b. Bontsa ki az **xsi:fed:ApplicationServiceType**típusú **xsi:type** **RoleDescriptor** csomópontot.
    
    c. Másolja az **X509Certificate** csomópont értékét.
    
    d. Mentse a másolt **X509Certificate** értéket helyileg a számítógépre egy fájlba.

7. A **Nyilvános** csoportban kattintson a **Hozzáadás gombra.**
    
    ![SAML bejelentkezés](./media/topdesk-public-tutorial/ic790625.png "SAML bejelentkezés")

8. Az **SAML konfigurációs segéd** párbeszédpanelen hajtsa végre az alábbi lépéseket:
    
    ![SAML konfigurációs segéd](./media/topdesk-public-tutorial/ic790608.png "SAML konfigurációs segéd")
    
    a. Ha fel szeretné tölteni a letöltött metaadatfájlt az Azure Portalról, az **Összevonási metaadatok**csoportban kattintson a **Tallózás gombra.**

    b. A tanúsítványfájl feltöltéséhez a **Tanúsítvány (RSA) csoportban**kattintson a **Tallózás gombra.**

    c. A TOPdesk támogatási csoportjától kapott emblémafájl feltöltéséhez kattintson az **Embléma ikonra,** kattintson a **Tallózás gombra.**

    d. A **Felhasználónév attribútum** szövegmezőbe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`írja be a következőt:

    e. A **Név megjelenítése** mezőbe írja be a konfiguráció nevét.

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés tpdesk - nyilvános hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **TOPdesk – Nyilvános**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **TOPdesk - Public**lehetőséget.

    ![A TOPdesk – Nyilvános hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-topdesk---public-test-user"></a>TOPdesk létrehozása – Nyilvános tesztfelhasználó

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a TOPdesk - Nyilvános, ki kell építeni a TOPdesk - Nyilvános. ToPdesk – Nyilvános esetén a kiépítés manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **TOPdesk - Nyilvános** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **TOPdesk \> Új \> támogatási fájlok személy parancsára. \> **
   
    ![Személy](./media/topdesk-public-tutorial/ic790628.png "Személy")

3. Az Új személy párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Új személy](./media/topdesk-public-tutorial/ic790629.png "Új személy")
   
    a. Kattintson az Általános fülre.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simont.
 
    c. Válasszon **egy webhelyet** a fiókhoz.
 
    d. Kattintson a **Mentés** gombra.

> [!NOTE]
> Bármely más TOPdesk – Nyilvános felhasználói fiók létrehozása eszközök vagy API-k által biztosított TOPdesk - Nyilvános Azure AD felhasználói fiókok kiépítése.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEN a TOPdesk – Nyilvános csempére kattint, automatikusan be kell jelentkeznie a TOPdesk - Nyilvános lapra, amelyhez beállítja az Egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
