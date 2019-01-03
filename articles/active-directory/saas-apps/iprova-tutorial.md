---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iProva |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iProva között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: eb64fd7a7270c3bae03a94385fc77581637ac675
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53978087"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iProva

Ebben az oktatóanyagban elsajátíthatja, hogyan iProva integrálása az Azure Active Directory (Azure AD).
IProva integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá iProva Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve iProva (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

IProva az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* iProva egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* támogatja a iProva **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-iprova-from-the-gallery"></a>IProva hozzáadása a katalógusból

Az Azure AD integrálása a iProva konfigurálásához hozzá kell iProva a katalógusból a felügyelt SaaS-alkalmazások listájára.

**IProva hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **iProva**válassza **iProva** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![az eredmények listájában iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az iProva nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó iProva hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az iProva tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Konfigurációs adatok lekérését iProva](#retrieve-configuration-information-from-iprova)**  – egy előkészítő lépések szerint.
2. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
3. **[Egyszeri bejelentkezés iProva konfigurálása](#configure-iprova-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
4. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
5. **[Hozzon létre iProva tesztfelhasználót](#create-iprova-test-user)**  – egy megfelelője a Britta Simon a felhasználó Azure ad-ben reprezentációja kapcsolódó iProva rendelkeznie.
6. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
7. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="retrieve-configuration-information-from-iprova"></a>Konfigurációs adatok lekérését iProva

Ebben a szakaszban lekéri a iProva néhány alapvető információkat.
Ez az információ van szüksége annak érdekében, hogy az Azure AD egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy webböngészőt, és keresse meg a **egy SAML2 információs oldalán** iProva, használja a következő URL-minta:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Megtekintheti a iProva egy SAML2 információs oldalán](media/iprova-tutorial/iprova-saml2-info.png)

2. Hagyja nyitva a böngészőlapot, miközben, folytassa a következő lépéseket egy új böngészőlapon.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iProva, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **iProva** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Töltse ki a **azonosító** mezőt az érték, amely mögött a felirat jelenik meg a **EntityID** a a **iProva egy SAML2 információs oldalán** (még mindig nyitva a másik böngészőlapon található).

    b. Töltse ki a **válasz-URL-cím** mezőt az érték, amely mögött a felirat jelenik meg a **válasz URL-cím** a a **iProva egy SAML2 információs oldalán** (még mindig nyitva a másik böngészőlapon található).

    c. Töltse ki a **bejelentkezési URL-** mezőt az érték, amely mögött a felirat jelenik meg a **bejelentkezési URL-** a a **iProva egy SAML2 információs oldalán** (még mindig nyitva a másik böngészőlapon található).

    ![iProva tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

5. iProva alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum| Névtér |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **Namespace** list, írja be a névtér értéke a sorhoz látható.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>IProva egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a iProva használatával a **rendszergazda** fiókot.

2. Nyissa meg a **Ugrás** menü.

3. Kattintson a **Alkalmazáskezelés**.

4. Kattintson a **általános** a a **rendszerbeállítások** panel.

5. Kattintson a **szerkesztése**.

6. Görgessen le a **hozzáférés-vezérlés**.

    ![hozzáférés-vezérlési beállításokkal iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Keresse meg a beállítás **automatikusan bejelentkezett felhasználó hálózati fiókjukkal**, és módosítsa a következőre **Igen, az SAML-n keresztül hitelesítést**. További beállítások megjelenik.

8. Kattintson a **telepítő** gombra.

9. Kattintson a **tovább** gombra.

10. iProva most kéri szeretné-e összevonási adatokat letölteni a URL-címet, vagy töltse fel egy fájlból. Válassza ki a **URL-címről** lehetőséget.

    ![Az Azure AD-metaadatok letöltése](media/iprova-tutorial/iprova-download-metadata.png)

11. Illessze be a metaadatok URL-címe az az előző lépésben mentette a **konfigurálása az Azure AD egyszeri bejelentkezés** fejezet.

12. A alakú nyíl gombra kattintva töltse le a metaadatokat az Azure ad-ből.

13. Ha a letöltés befejeződött, a megerősítést kérő üzenet **letöltött fájl érvényes összevonási adatok** jelenik meg.

14. Kattintson a **tovább** gombra.

15. Hagyja ki a **teszt bejelentkezési** most lehetőséget, majd kattintson a **tovább** gombra.

16. A legördülő listában nevű **használandó Jogcímszabály**válassza **windowsaccountname**.

17. Kattintson a **Befejezés** gombra.

18. Ekkor visszatér a **általános beállításainak szerkesztése** képernyő. **Görgessen lefelé** alján a lapot, és kattintson a **OK** gombra kattintva mentse a konfigurációt.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be például a név **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **yourname@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iProva való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **iProva**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **iProva**.

    ![Az alkalmazások listáját a iProva hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-iprova-test-user"></a>IProva tesztfelhasználó létrehozása

1. Jelentkezzen be a iProva használatával a **rendszergazda** fiókot.

2. Nyissa meg a **Ugrás** menü.

3. Kattintson a **Alkalmazáskezelés**.

4. Kattintson a **felhasználók** a a **felhasználók és csoportok** panel.

5. Kattintson a **Hozzáadás** gombra.

6. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

7. Az a **teljes fájlvisszaállítási név** mezőbe írja be például a teljes név **BrittaSimon**.

8. Válassza ki a **(használata egyszeri bejelentkezéshez) jelszavát nem** lehetőséget.

9. Az a **E-mail-cím** mezőbe írja be **yourname@yourcompanydomain.extension** például BrittaSimon@contoso.com

10. Görgessen a lap aljára, és kattintson a **Befejezés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a iProva csempére kattint, meg kell lehet automatikusan bejelentkezett a iProva, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [iProva – egy SAML2 egyszeri bejelentkezés konfigurálása](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)