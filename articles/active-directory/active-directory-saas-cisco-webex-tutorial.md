---
title: "Oktatóanyag: Azure Active Directoryval integrált Cisco Webex |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Cisco Webex között."
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
ms.openlocfilehash: 835b7c139fc466719489c6fd1986dcbf16de549f
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Azure Active Directoryval integrált Cisco Webex

Ebben az oktatóanyagban elsajátíthatja Cisco Webex integrálása az Azure Active Directory (Azure AD).

Cisco Webex integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a Cisco Webex szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Cisco Webex (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Cisco Webex egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Cisco Webex hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a gyűjteményből
Az Azure AD integrálása a Cisco Webex konfigurálásához kell hozzáadnia a Cisco Webex a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Cisco Webex**, jelölje be **Cisco Webex** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Cisco Webex az eredménylistában](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Cisco Webex "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Cisco Webex tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Cisco Webex közötti kapcsolat kapcsolatot kell létrehozni.

A Cisco Webex, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Cisco Webex tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Cisco Webex tesztfelhasználó létrehozása](#create-a-cisco-webex-test-user)**  - való Britta Simon egy megfelelője a Cisco Webex, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Cisco Webex alkalmazásban egyszeri bejelentkezés konfigurálása.

**Cisco Webex konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Cisco Webex** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Az a **Cisco Webex tartomány és az URL-címek** területen tegye a következőket:

    ![Cisco Webex tartomány és az URL-címeket az egyszeri bejelentkezés információk](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.webex.com`

    b. Az a **azonosító** szövegmező, írja be az URL-cím:`http://www.webex.com`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Cisco Webex ügyfél-támogatási csoport](https://www.webex.co.in/support/support-overview.html) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. A a **Cisco Webex konfigurációs** kattintson **konfigurálásához Cisco Webex** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Cisco Webex vállalati webhely rendszergazdaként.

8. Kattintson a felső menüben **helyfelügyelet**.

    ![Hely felügyelete](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "hely felügyelete")

9. Az a **kezelése hely** területén kattintson **SSO konfigurációs**.
   
    ![Egyszeri bejelentkezés konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO-konfiguráció")

10. Az a **összevont webes egyszeri bejelentkezés konfigurálása** területen tegye a következőket:
   
    ![Összevont egyszeri Bejelentkezéses konfigurációs](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "összevont egyszeri bejelentkezés konfigurálása")  

    a. Az a **összevonási protokoll** listáról válassza ki **SAML 2.0**.

    b. Mint **egyszeri bejelentkezési profil**, jelölje be **SP Intiated**.

    c. A letöltött tanúsítvány megnyitása a Jegyzettömbben, és másolja a tartalmát.

    d. Kattintson a **SAML-metaadatok importálása**, majd illessze be a tanúsítvány másolt tartalmát.

    e. Az a **SAML (IdP ID) kiállító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    f. Az a **ügyfél SSO szolgáltatás bejelentkezési URL-címe** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    g. Az a **NameID formátum** listáról válassza ki **E-mail cím**.

    h. Az a **AuthnContextClassRef** szövegmezőhöz típus **urn: oasis: nevek: tc: SAML:2.0:ac:classes:Password**.

    i. Az a **ügyfél egyszeri bejelentkezési szolgáltatás kijelentkezési URL-címe** szövegmezőhöz Beillesztés **Sign-Out URL-cím** ami Azure-portálon másolta.
   
    j. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Cisco Webex bejelentkezni, akkor ki kell építenie a Cisco Webex. Cisco Webex, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Cisco Webex** bérlő.

2. Ugrás a **felhasználók kezelése \> felhasználó hozzáadása**.
   
    ![Felhasználók hozzáadása az](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "felhasználók hozzáadása")

3. A felhasználó hozzáadása területen hajtsa végre a következő lépéseket:
   
    ![Felhasználó hozzáadása](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "felhasználó hozzáadása")   

    a. Mint **fióktípus**, jelölje be **állomás**.

    b. Az a **Keresztnév** szövegmezőhöz felhasználói Britta például az első nevét.

    c. Az a **Vezetéknév** szövegmező, írja be például Simon felhasználó vezetékneve.

    d. Az a **felhasználónév** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.

    e. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    f. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    g. Az a **megerősítése** jelszó szövegmezőjét, adja meg újból a felhasználó jelszavát.

    h. Kattintson az **Add** (Hozzáadás) parancsra.

>[!NOTE]
>Bármely más Cisco Webex felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Cisco Webex által nyújtott API-k. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cisco Webex Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Cisco Webex Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Cisco Webex**.

    ![Az alkalmazások listáját a Cisco Webex hivatkozás](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a Cisco Webex csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Cisco Webex alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

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

