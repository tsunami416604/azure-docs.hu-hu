---
title: 'Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció a Bitbucket SAML SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Bitbucket SAML SSO közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c38ae23b4d040ceb12d69b908b247c313cab605c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447489"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció a Bitbucket SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja a Bitbucket SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.
Feloldási GmbH az Azure AD által a Bitbucket SAML SSO integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá SAML SSO a bitbucket-alapú megoldás GmbH által az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve SAML SSO a bitbucket-alapú megoldás GmbH (egyszeri bejelentkezés) által az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integráció a Bitbucket SAML SSO-val feloldási GmbH, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAML egyszeri bejelentkezés, Bitbucket felbontása GmbH egyszeri bejelentkezés az előfizetés engedélyezve van

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A Bitbucket felbontása GmbH támogatja az SAML SSO **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* A Bitbucket felbontása GmbH támogatja az SAML SSO **igény szerinti** felhasználók átadása


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH SAML SSO a Bitbucket hozzáadását a katalógusból

Integráció konfigurálásához a Bitbucket SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell SAML SSO a Bitbucket felbontása GmbH a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Bitbucket SAML SSO felbontása GmbH a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAML SSO a Bitbucket felbontása GmbH**, jelölje be **bitbucket-alapú megoldás GmbH által a SAML SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

     ![SAML egyszeri bejelentkezés, a Bitbucket felbontása GmbH, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a SAML SSO nevű tesztfelhasználó alapján Bitbucket felbontása GmbH **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO a bitbucket-alapú megoldás által hivatkozás kapcsolatának GmbH kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Bitbucket SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SAML SSO a bitbucket-alapú megoldás GmbH egyszeri bejelentkezés konfigurálásához](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a bitbucket-alapú megoldás GmbH teszt felhasználó által a SAML SSO](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  - megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH által van egy megfelelője a Britta Simon SAML SSO a Bitbucket.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés a Bitbucket SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAML SSO a Bitbucket felbontása GmbH** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![SAML SSO a Bitbucket felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![SAML SSO a Bitbucket felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [bitbucket-alapú megoldás GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>SAML SSO a bitbucket-alapú megoldás GmbH egyszeri bejelentkezés konfigurálása

1. Bejelentkezés a SAML SSO, a bitbucket-alapú megoldás GmbH vállalati hely rendszergazdaként.

2. A fő eszköztár jobb oldalán kattintson a **beállítások**.

3. Ugrás a fiókok területen, kattintson a **SAML egyszeri bejelentkezés** a menüsávon a.

    ![A Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Az a **SAML egyszeri bejelentkezés beépülő modul konfigurációs lapon**, kattintson a **identitásszolgáltató hozzáadása**. 

    ![Az identitásszolgáltató hozzáadása](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Az a **válassza ki a SAML-identitásszolgáltató** lapon tegye a következőket:

    ![Az identitásszolgáltató](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Válassza ki **identitásszolgáltató típusa** , **AZURE ad-ben**.

    b. Az a **neve** szövegmezőbe írja be a nevét.

    c. Az a **leírás** szövegmezőbe írja be a leírást.

    d. Kattintson a **tovább**.

6. Az a **Identity provider konfigurációs lapon**, kattintson a **tovább**.

    ![Az identitás-config](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  A a **SAML-identitásszolgáltató metaadatok importálása** kattintson **fájl betöltése** feltölteni a **METAADATAINAK XML** fájlt, amely az Azure Portalról letöltött.

    ![A idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Kattintson a **tovább**.

9. Kattintson a **Beállítások mentése** lehetőségre.

    ![A Mentés](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon SAML SSO a Bitbucket, a hozzáférés biztosításával felbontása GmbH Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **bitbucket-alapú megoldás GmbH által a SAML SSO**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAML SSO a Bitbucket felbontása GmbH**.

    ![Az SAML SSO a bitbucket-alapú megoldás GmbH csatlakozásonkénti alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO a bitbucket-alapú megoldás GmbH teszt felhasználó létrehozása

Ez a szakasz célja egy nevű felhasználó létrehozásához Britta Simon a SAML SSO a Bitbucket GmbH felbontása. SAML SSO felbontása GmbH Bitbucket támogatja a létesítést igény és a felhasználók is manuálisan is létrehozhatók, lépjen kapcsolatba [bitbucket-alapú megoldás GmbH ügyfél által a SAML SSO támogatási csoportjának](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) a követelmény alapján.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Feloldási GmbH csempe a hozzáférési panelen által kattintva a SAML SSO a Bitbucket, meg kell automatikusan megtörténik a az SAML SSO-nak a Bitbucket feloldási GmbH, amelynek beállítása egyszeri bejelentkezés által. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

