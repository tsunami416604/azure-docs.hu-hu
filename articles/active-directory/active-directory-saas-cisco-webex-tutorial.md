---
title: 'Oktatóanyag: Azure Active Directoryval integrált Cisco Webex |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Cisco Webex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 527d74910244058506e8435d431e239968896cd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Azure Active Directoryval integrált Cisco Webex

Ebben az oktatóanyagban elsajátíthatja Cisco Webex integrálása az Azure Active Directory (Azure AD).

Cisco Webex integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a Cisco Webex szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Cisco Webex a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további tudnivalókért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy-egy bejelentkezési a alkalmas Cisco Webex előfizetéshez

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Cisco Webex hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a gyűjteményből
Az Azure AD integrálása a Cisco Webex konfigurálásához kell hozzáadnia a Cisco Webex a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex hozzáadása a gyűjteményből, tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Cisco Webex**. 

5. Válassza ki **Cisco Webex** az eredmények panelen. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Cisco Webex az eredménylistában](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Cisco Webex "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a Cisco Webex tartozó felhasználót egy felhasználó számára az Azure AD. Más szóval kell Cisco Webex az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

Cisco Webex biztosítanak a érték **felhasználónév** azonos érték, mint a **felhasználónév** az Azure ad-ben. Most hozott létre a hivatkozást a két felhasználó között. 

Az Azure AD egyszeri bejelentkezést a Cisco Webex tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Cisco Webex tesztfelhasználó létrehozása](#create-a-cisco-webex-test-user) való Britta Simon egy megfelelője a Cisco Webex, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Cisco Webex alkalmazásban egyszeri bejelentkezés konfigurálása.

**Cisco Webex konfigurálása az Azure AD egyszeri bejelentkezést, tegye a következőket:**

1. Az Azure portálon a a **Cisco Webex** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Az a **Cisco Webex tartomány és az URL-címek** területen tegye a következőket:

    ![Cisco Webex tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írja be a következő mintát olyan URL-címe: `https://<subdomain>.webex.com`

    b. Az a **azonosító** mezőben adja meg az URL-cím `http://www.webex.com`.

    c. Az a **válasz URL-CÍMEN** mezőbe írja be a következő mintát olyan URL-címe: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-címet és bejelentkezés URL-CÍMÉT. Ügyfél [Cisco Webex ügyfél-támogatási csoport](https://www.webex.co.in/support/support-overview.html) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**, és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Kattintson a **Mentés** gombra.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. Az a **Cisco Webex konfigurációs** szakaszban jelölje be **konfigurálásához Cisco Webex** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló** szakasz.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Cisco Webex vállalati webhely rendszergazdaként.

8. A felső menüben válassza ki a **helyfelügyelet**.

    ![Hely felügyelete](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "hely felügyelete")

9. Az a **kezelése hely** szakaszban jelölje be **SSO konfigurációs**.
   
    ![Egyszeri bejelentkezés konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO-konfiguráció")

10. Az a **összevont webes egyszeri bejelentkezés konfigurálása** területen tegye a következőket:
   
    ![Összevont egyszeri Bejelentkezéses konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "összevont egyszeri bejelentkezés konfigurálása")  

    a. Az a **összevonási protokoll** listáról válassza ki **SAML 2.0**.

    b. A **egyszeri bejelentkezési profil**, jelölje be **Szolgáltató kezdeményezett**.

    c. A letöltött tanúsítvány megnyitása a Jegyzettömbben, és másolja a tartalmat.

    d. Válassza ki **SAML-metaadatok importálása**, majd illessze be a tanúsítvány másolt tartalmát.

    e. Az a **SAML (IdP ID) kiállító** mezőbe illessze be az értékét a **SAML Entitásazonosító** másolt Azure-portálról.

    f. Az a **ügyfél SSO szolgáltatás bejelentkezési URL-címe** mezőbe illessze be **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolt.

    g. Az a **NameID formátum** listáról válassza ki **E-mail cím**.

    h. Az a **AuthnContextClassRef** mezőbe írja be **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**.

    i. Az a **ügyfél egyszeri bejelentkezési szolgáltatás kijelentkezési URL-címe** mezőbe illessze be **Sign-Out URL-cím**, amely másolt Azure-portálról.
   
    j. Válassza ki **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapot, és hozzáférhet a beágyazott a dokumentáció a **konfigurációs** alsó szakasz. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cisco Webex bejelentkezni, akkor ki kell építenie a Cisco Webex. Cisco Webex, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához a következő lépéseket:**

1. Jelentkezzen be a **Cisco Webex** bérlő.

2. Ugrás a **felhasználók kezelése** > **felhasználó hozzáadása**.
   
    ![Felhasználók hozzáadása az](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "felhasználók hozzáadása")

3. Az a **felhasználó hozzáadása** területen tegye a következőket:
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "felhasználó hozzáadása")   

    a. A **fióktípus**, jelölje be **állomás**.

    b. Az a **Utónév** mezőbe írja be a felhasználó utóneve (ebben az esetben **Britta**).

    c. Az a **Vezetéknév** mezőbe írja be a felhasználó vezetékneve (ebben az esetben **Simon**).

    d. Az a **felhasználónév** mezőbe írja be az e-mail a felhasználó (ebben az esetben **Brittasimon@contoso.com**).

    e. Az a **E-mail** mezőbe írja be a felhasználó e-mail címe (ebben az esetben **Brittasimon@contoso.com**).

    f. Az a **jelszó** mezőbe írja be a jelszót.

    g. Az a **megerősítése** jelszó mezőbe írja be újra a jelszót.

    h. Válassza a **Hozzáadás** lehetőséget.

>[!NOTE]
>Cisco Webex felhasználói fiók létrehozása eszközök vagy Cisco Webex kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-kat is használhatja. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ez a szakasz lehetővé teszik a felhasználónak Britta Simon szerint Cisco Webex hozzáférést adna Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Cisco Webex Britta Simon hozzárendeléséhez a következő lépéseket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Következő, nyissa meg a könyvtár nézet, majd a **vállalati alkalmazások**.  

2. Válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

3. Az alkalmazások listában válassza ki a **Cisco Webex**.

    ![Az alkalmazások listáját a Cisco Webex hivatkozás](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra.

7. Válassza ki a **hozzárendelése** gombra a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

Ha bejelöli a Cisco Webex csempe a hozzáférési panelen automatikusan lekérni jelentkezett be a Cisco Webex alkalmazás.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

