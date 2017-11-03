---
title: "Oktatóanyag: Azure Active Directoryval integrált BitaBIZ |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és BitaBIZ között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: dca0a460224436d3886cf9a9c354ce662f99ae84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Oktatóanyag: Azure Active Directoryval integrált BitaBIZ

Ebben az oktatóanyagban elsajátíthatja BitaBIZ integrálása az Azure Active Directory (Azure AD).

BitaBIZ integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér BitaBIZ szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett BitaBIZ (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs BitaBIZ, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy BitaBIZ egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből BitaBIZ hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-bitabiz-from-the-gallery"></a>A gyűjteményből BitaBIZ hozzáadása
Az Azure AD integrálása a BitaBIZ konfigurálásához kell hozzáadnia BitaBIZ a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből BitaBIZ hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **BitaBIZ**, jelölje be **BitaBIZ** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában BitaBIZ](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján BitaBIZ.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó BitaBIZ a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a BitaBIZ közötti kapcsolat kapcsolatot kell létrehozni.

BitaBIZ, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a BitaBIZ tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[BitaBIZ tesztfelhasználó létrehozása](#create-a-bitabiz-test-user)**  - való Britta Simon valami BitaBIZ, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az BitaBIZ alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés BitaBIZ, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **BitaBIZ** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. Az a **BitaBIZ tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás által kezdeményezett IDP módban, hajtsa végre az alábbi lépéseket:

    ![Az egyszeri bejelentkezés információk BitaBIZ tartomány és az URL-címek](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url.png)

    Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > A fenti URL-cím értéke csak bemutatásához. Frissítse az értéket az oktatóanyag későbbi részében ismertetett tényleges azonosítóval.

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk BitaBIZ tartomány és az URL-címek](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím:`https://www.bitabiz.com/dashboard`

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-bitabiz-tutorial/tutorial_general_400.png)
    
7. A a **BitaBIZ konfigurációs** kattintson **konfigurálása BitaBIZ** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![BitaBIZ konfiguráció](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. Egy másik webes böngészőablakban bejelentkezés a BitaBIZ bérlő rendszergazdaként.

9. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ konfiguráció](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

10. Kattintson a **Microsoft integrációja** alatt **előnyei** szakasz.

    ![BitaBIZ konfiguráció](./media/active-directory-saas-bitabiz-tutorial/settings2.png)

11. Görgessen le a szakasz **Microsoft Azure AD (engedélyezése az egyszeri bejelentkezés)** és hajtsa végre a következő lépéseket:

    ![BitaBIZ konfiguráció](./media/active-directory-saas-bitabiz-tutorial/settings3.png)

    a. Másolja az értéket a **entitás azonosítója ("" az Azure AD-Identifier) azonosítót** szövegmező és illessze be azt a **azonosítója** a szövegmező a **BitaBIZ tartomány és az URL-címek** szakaszban az Azure portálon. 
    
    b. A a **az Azure AD egyszeri bejelentkezési URL-címe** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
    
    c. A a **Azure AD SAML Entitásazonosító** szövegmezőhöz Beillesztés **SAML Entitásazonosító**, amely az Azure-portálon másolta.

    d. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **az Azure AD-aláíró tanúsítványa (Base64-kódolású)** szövegmező.

    e. Adja hozzá a vállalati e-mail tartomány neve, amely azt jelenti, hogy a mycompany.com **tartománynév** szövegmező SSO hozzárendelése a felhasználók a vállalati e-mail tartomány (nem kötelező).
    
    f. Megjelölés **engedélyezett SSO** BitaBIZ fiók.
    
    g. Kattintson a **az Azure AD-konfiguráció mentéséhez** mentéséhez és az egyszeri bejelentkezés konfiguráció aktiválása.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-bitabiz-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-bitabiz-test-user"></a>BitaBIZ tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BitaBIZ bejelentkezni, akkor ki kell építenie a BitaBIZ.  
BitaBIZ, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a BitaBIZ vállalati webhely.

2. Kattintson a **telepítő rendszergazda**.

    ![BitaBIZ felhasználó hozzáadása](./media/active-directory-saas-bitabiz-tutorial/settings1.png)

3. Kattintson a **felhasználók hozzáadása az** alatt **szervezet** szakasz.

    ![BitaBIZ felhasználó hozzáadása](./media/active-directory-saas-bitabiz-tutorial/user1.png)

4. Kattintson a **hozzáadása új alkalmazott**.

    ![BitaBIZ felhasználó hozzáadása](./media/active-directory-saas-bitabiz-tutorial/user2.png)

5. Az a **"Hozzáadása új alkalmazott"** párbeszédpanel lapon, a következő lépésekkel:

    ![BitaBIZ felhasználó hozzáadása](./media/active-directory-saas-bitabiz-tutorial/user3.png)

    a. Az a **Keresztnév** szövegmezőhöz felhasználói Britta például az első nevét.

    b. Az a **Vezetéknév** szövegmező, írja be például Simon felhasználó vezetékneve.

    c. Az a **E-mail** szövegmező, a felhasználó e-mail címe típusát, például Brittasimon@contoso.com.

    d. Válassza ki a dátumot a **alkalmazási dátum**.

    e. Nincsenek más nem kötelező felhasználói attribútumok, amelyek a felhasználó állítható be. Tekintse meg a [alkalmazott telepítő Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) további részleteket.    
    
    f. Kattintson a **mentés alkalmazott**.
    
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.
    
### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BitaBIZ Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése BitaBIZ, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **BitaBIZ**.

    ![Az alkalmazások listáját a BitaBIZ hivatkozás](./media/active-directory-saas-bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen BitaBIZ csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az BitaBIZ alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bitabiz-tutorial/tutorial_general_203.png

