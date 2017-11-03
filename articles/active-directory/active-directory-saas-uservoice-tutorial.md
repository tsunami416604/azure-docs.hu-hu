---
title: "Oktatóanyag: Azure Active Directoryval integrált UserVoice |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a UserVoice között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: fcfda1c2ecb162fb93b70574a18bd745b72ee4db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Oktatóanyag: Azure Active Directoryval integrált UserVoice

Ebben az oktatóanyagban elsajátíthatja UserVoice integrálása az Azure Active Directory (Azure AD).

UserVoice integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér UserVoice szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a UserVoice (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a UserVoice, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A UserVoice egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. UserVoice hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-uservoice-from-the-gallery"></a>UserVoice hozzáadása a gyűjteményből
Az Azure AD integrálása a UserVoice konfigurálásához kell hozzáadnia UserVoice a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a UserVoice a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **UserVoice**, jelölje be **UserVoice** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján UserVoice.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a UserVoice tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a UserVoice közötti kapcsolat kapcsolatot kell létrehozni.

A UserVoice, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a UserVoice tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[UserVoice tesztfelhasználó létrehozása](#create-a-uservoice-test-user)**  - való Britta Simon egy megfelelője a UserVoice, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a UserVoice-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a UserVoice, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **UserVoice** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Az a **UserVoice tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információt UserVoice tartomány és az URL-címek](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<tenantname>.UserVoice.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [UserVoice ügyfél-támogatási csoport](https://www.uservoice.com/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. A a **UserVoice konfigurációs** kattintson **konfigurálása UserVoice** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![UserVoice-konfiguráció](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a UserVoice vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **beállítások**, majd válassza ki **webes portál** a menüből.
   
    ![Az alkalmazás ügyféloldali beállítások szakaszban](./media/active-directory-saas-uservoice-tutorial/ic777519.png "beállítások")

9. A a **webes portál** lap a **felhasználóhitelesítés** területen kattintson **szerkesztése** megnyitásához a **felhasználói hitelesítés szerkesztése** párbeszédpanel lap.
   
    ![Webes portál lapon](./media/active-directory-saas-uservoice-tutorial/ic777520.png "webes portál")

10. Az a **felhasználói hitelesítés szerkesztése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Felhasználói hitelesítés szerkesztése](./media/active-directory-saas-uservoice-tutorial/ic777521.png "felhasználói hitelesítés szerkesztése")
   
    a. Kattintson a **egyszeri bejelentkezés (SSO)**.
 
    b. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** értéket, amely az Azure-portálról másolta a **SSO távoli bejelentkezés** szövegmező.

    c. Beillesztés a **Sign-Out URL-cím** értéket, amely az Azure-portálról másolta a **SSO távoli Sign-Out szövegmező**.
 
    d. Beillesztés a **ujjlenyomat** értéket, amely az Azure portálról másolta a **aktuális SHA1 tanúsítvány-ujjlenyomat** szövegmező.
    
    e. Kattintson a **hitelesítési beállításainak mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-uservoice-test-user"></a>UserVoice tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók UserVoice bejelentkezni, akkor ki kell építenie a UserVoice. UserVoice, ha egy kézi tevékenység.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be a **UserVoice** bérlő.

2. Ugrás a **beállítások**.
   
    ![Beállítások](./media/active-directory-saas-uservoice-tutorial/ic777811.png "beállítások")

3. Kattintson a **általános**.

4. Kattintson a **az ügynökök és az engedélyek**.
   
    ![Az ügynökök és az engedélyek](./media/active-directory-saas-uservoice-tutorial/ic777812.png "az ügynökök és engedélyek")

5. Kattintson a **rendszergazdák hozzáadása**.
   
    ![Adja hozzá a rendszergazdák](./media/active-directory-saas-uservoice-tutorial/ic777813.png "rendszergazdák hozzáadása")

6. Az a **rendszergazdák meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Rendszergazdák meghívása](./media/active-directory-saas-uservoice-tutorial/ic777814.png "rendszergazdák meghívása")
   
    a. Az e-mailek szövegmezőjének írja be a fiók kiépítése szeretne, és kattintson az e-mail cím **Hozzáadás**.
   
    b. Kattintson a **meghívása**.

> [!NOTE]
> Bármely más UserVoice felhasználói fiók létrehozása eszközök, vagy API-k megadott uservoice rendelkezés AAD felhasználói fiókokhoz.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés UserVoice Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése UserVoice, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **UserVoice**.

    ![Az alkalmazások listáját a UserVoice-hivatkozás](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A hozzáférési panelen a UserVoice csempére kattintva, meg kell beolvasása automatikusan bejelentkezett a UserVoice-alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png

