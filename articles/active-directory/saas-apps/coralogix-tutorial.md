---
title: 'Oktatóanyag: Azure Active Directory integráció a Coralogix-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Coralogix között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8589c366c029ab51c7cd740a1b63cff7c0481a51
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158467"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Oktatóanyag: Azure Active Directory integráció a Coralogix

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Coralogix a Azure Active Directory (Azure AD) szolgáltatással.
A Coralogix és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Coralogix.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Coralogix (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való SaaS-alkalmazások integrálásával kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Coralogix való konfigurálásához a következő elemek szükségesek:

- Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is beszerezhet.
- Coralogix egyszeri bejelentkezésre alkalmas előfizetés. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Coralogix támogatja az SP által kezdeményezett egyszeri bejelentkezést.

## <a name="add-coralogix-from-the-gallery"></a>Coralogix hozzáadása a gyűjteményből

A Coralogix Azure AD-ba való integrálásának konfigurálásához először adja hozzá a Coralogix a katalógusból a felügyelt SaaS-alkalmazások listájához.

Ha Coralogix szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)bal oldali ablaktábláján válassza a **Azure Active Directory** ikont.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Coralogix**kifejezést. Válassza az **Coralogix** lehetőséget az eredmények ablaktábláján, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Coralogix az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Coralogix-mel konfigurálja és teszteli a Britta Simon nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Coralogix-ben.

Az Azure AD egyszeri bejelentkezés Coralogix való konfigurálásához és teszteléséhez először végezze el a következő építőelemeket:

1. Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
2. [Konfigurálja az Coralogix egyszeri bejelentkezést](#configure-coralogix-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
3. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. [Hozzon létre egy Coralogix-tesztelési felhasználót](#create-a-coralogix-test-user) , hogy a Britta Simon a Coralogix-hoz tartozó, a felhasználó Azure ad-képviseletéhez kapcsolódó partnere legyen.
6. Az [egyszeri bejelentkezés tesztelésével](#test-single-sign-on) ellenőrizheti, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Coralogix való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Coralogix** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri **Bejelentkezés módszerének kiválasztása** párbeszédpanelen válassza az **SAML** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza a **Szerkesztés** ikont az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Coralogix tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-** cím mezőbe írjon be egy URL-címet a következő mintával: `https://<SUBDOMAIN>.coralogix.com`

    b. Az **azonosító (Entity ID)** szövegmezőben adjon meg egy URL-címet, például:
    
    `https://api.coralogix.com/saml/metadata.xml`

    vagy

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Coralogix ügyfél-támogatási csapatával](mailto:info@coralogix.com) . A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának mintázatait is megtekintheti.

5. A Coralogix alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A felhasználói **jogcímek** szakaszban a **felhasználói attribútumok** párbeszédpanelen szerkessze a jogcímeket a **Szerkesztés** ikon használatával. A jogcímeket az **új jogcím hozzáadása** lehetőséggel is hozzáadhatja az SAML-jogkivonat attribútum konfigurálásához az előző képen látható módon. Ezután hajtsa végre a következő lépéseket:
    
    a. Kattintson a **Szerkesztés ikonra** a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![rendszerkép](./media/coralogix-tutorial/tutorial_usermail.png) ![rendszerkép](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. A **név-azonosító formátum kiválasztása** listából válassza az **e-mail cím**elemet.

    c. A **forrás attribútum** listából válassza a **User. mail**elemet.

    d. Kattintson a **Mentés** gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget az **összevonási metaadatok XML-** fájljának az adott beállítások alapján való letöltéséhez. Ezután mentse a számítógépére.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

8. A **Coralogix beállítása** szakaszban másolja a megfelelő URL-címeket (ka) t.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-coralogix-single-sign-on"></a>Coralogix egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést az **Coralogix** oldalon szeretné konfigurálni, küldje el a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt, és másolja a Azure Portal a [Coralogix támogató csapatának](mailto:info@coralogix.com)URL-címét. Gondoskodnak arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. A képernyő felső részén válassza az **új felhasználó**lehetőséget.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőben adja meg a "brittasimon@yourcompanydomain.extension" értéket. Ebben az esetben például megadhatja a "brittasimon@contoso.com" értéket.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Coralogix hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Coralogix**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Coralogix**lehetőséget.

    ![Az Coralogix hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása** gombot. Ezután válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listában. Ezután kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-állításban a szerepkör értékét várja, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-a-coralogix-test-user"></a>Coralogix-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Coralogix-ben. Együttműködik a [Coralogix támogatási csapatával](mailto:info@coralogix.com) , hogy hozzáadja a felhasználókat a Coralogix platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hoznia és aktiválnia kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a MyApps-portál használatával.

Amikor kiválasztja a Coralogix csempét a MyApps-portálon, automatikusan be kell jelentkeznie a Coralogix-be. További információ a MyApps-portálról: [Mi a MyApps-portál?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

