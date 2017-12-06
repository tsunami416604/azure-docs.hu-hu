---
title: "Oktatóanyag: Azure Active Directory-integráció SAML-alapú egyszeri a bambusz felbontása GmbH |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a SAML SSO bambusz a felbontása GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 3195dee2df560993e7885d138168e4514272f7eb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory-integráció SAML-alapú egyszeri a bambusz felbontása GmbH

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálását a SAML SSO bambusz a feloldási GmbH az Azure Active Directoryval (Azure AD).

SAML SSO bambusz a felbontása GmbH az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik SAML SSO bambusz a feloldási GmbH által szabályozható.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a SAML SSO bambusz a felbontása GmbH (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs bambusz a SAML SSO feloldási GmbH, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A SAML SSO a bambusz felbontása GmbH egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Megoldási GmbH bambusz a SAML SSO hozzáadását a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Megoldási GmbH bambusz a SAML SSO hozzáadását a gyűjteményből
A integrálása a SAML SSO bambusz a feloldási GmbH által az Azure AD konfigurálása, kell hozzáadnia bambusz a SAML SSO felbontása GmbH a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a SAML SSO bambusz a felbontása GmbH a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **felbontása GmbH bambusz a SAML SSO**, jelölje be **felbontása GmbH bambusz a SAML SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel a az alkalmazás.

    ![A felbontása GmbH az eredménylistában bambusz SAML SSO](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés SAML-alapú egyszeri a bambusz GmbH alapján "Britta Simon" nevű tesztfelhasználó felbontása.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, milyen a párjukhoz felhasználó bambusz a SAML SSO az Azure AD egy felhasználónak van GmbH felbontása. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAML SSO bambusz a felbontása hivatkozás kapcsolatának GmbH kell létrehozni.

SAML SSO a felbontása GmbH bambusz, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés SAML-alapú egyszeri a bambusz felbontása GmbH tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy SAML SSO a feloldási GmbH teszt felhasználó bambusz](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  - való egy megfelelője a Britta Simon bambusz a SAML SSO felbontása GmbH, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, majd állítsa egyszeri bejelentkezéshez a SAML SSO bambusz a felbontása GmbH alkalmazás.

**Konfigurálja az Azure AD az egyszeri bejelentkezés SAML-alapú egyszeri a bambusz feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **felbontása GmbH bambusz a SAML SSO** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlbase.png)

3. Az a **SAML SSO bambusz felbontása GmbH tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás által kezdeményezett IDP módban, hajtsa végre az alábbi lépéseket:

    ![Az egyszeri bejelentkezési adatokat felbontása GmbH tartomány bambusz a SAML SSO és URL-címek](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezési adatokat felbontása GmbH tartomány bambusz a SAML SSO és URL-címek](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [bambusz feloldási GmbH ügyfél által a SAML SSO támogatási csoport](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-bamboo-tutorial/tutorial_general_400.png)

7. Bejelentkezés a SAML SSO a feloldási GmbH vállalati hely bambusz rendszergazdaként.

8. Kattintson a jobb oldalon a fő eszköztárnak, **beállítások** > **bővítmények**.

    ![A beállítások](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_setings.png)

9. Ugrás a biztonság szakaszában, kattintson a **SAML SingleSignOn** a Menubar meg.

    ![A Samlsingle](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlsingle.png)

10. Az a **SAML SIngleSignOn beépülő modul konfiguráció lapon**, kattintson a **idp hozzáadása**. 

    ![A kiállító terjesztési hely hozzáadása](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addidp.png)

11. Az a **válassza a SAML-Identitásszolgáltatóként** lapon hajtsa végre a következő lépéseket:

    ![Az identitásszolgáltató](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Válassza ki **Idp típus** , **az AZURE AD**.

    b. Az a **neve** szövegmező, írja be a nevet.

    c. Az a **leírás** szövegmező, írja be a leírást.

    d. Kattintson a **Tovább** gombra.

12. Az a **identitás szolgáltató konfigurálása** kattintson **következő**.

    ![Az identitás-konfiguráció](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityconfig.png)

13.  A a **SAML Idp metaadatok importálása** kattintson **fájl betöltése** feltölteni a **METAADATAINAK XML-kódja** Azure-portálról letöltött fájl.

    ![A idpmetadata](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

14. Kattintson a **Tovább** gombra.

15. Kattintson a **beállítások mentése**.

    ![A mentési](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-bamboo-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-bamboo-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-bamboo-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-bamboo-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Hozzon létre egy SAML SSO a bambusz feloldási GmbH teszt felhasználó

Ez a szakasz célja Britta Simon lévő nevű SAML SSO bambusz felbontása GmbH felhasználó létrehozásához. SAML SSO felbontása GmbH bambusz támogatja a létesítést just-in-time és is felhasználók hozhatók létre manuálisan, forduljon [bambusz feloldási GmbH ügyfél által a SAML SSO támogatási csoport](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) igényektől szerint.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon által biztosított hozzáférés bambusz a SAML SSO feloldási GmbH által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon GmbH megoldási történő bambusz a SAML SSO hozzárendelését, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **felbontása GmbH bambusz a SAML SSO**.

    ![A SAML SSO számára az alkalmazások listáját a feloldási GmbH csatlakozásonkénti bambusz](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Gombra a SAML SSO bambusz a feloldási GmbH csempe a hozzáférési panelen által meg kell beolvasni automatikusan aláírt a a SAML SSO bambusz a felbontása GmbH alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_203.png

