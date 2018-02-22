---
title: "Oktatóanyag: Azure Active Directoryval integrált E értékesítés Manager Remix |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az E értékesítés Manager Remix között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Oktatóanyag: Azure Active Directoryval integrált E értékesítés Manager Remix

Ebben az oktatóanyagban elsajátíthatja E értékesítés Manager Remix integrálása Azure Active Directory (Azure AD).

E értékesítés Manager Remix integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér E értékesítés Manager Remix szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az E értékesítés Manager Remix (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

E értékesítés Manager Remix konfigurálása az Azure AD-integrációs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az E értékesítés Manager Remix egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. E értékesítés Manager Remix hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>E értékesítés Manager Remix hozzáadása a gyűjteményből
Az Azure AD integrálása a E értékesítés Manager Remix konfigurálásához kell hozzáadnia E értékesítés Manager Remix a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az E értékesítés Manager Remix a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **E értékesítés Manager Remix**, jelölje be **E értékesítés Manager Remix** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![E értékesítés Manager Remix az eredménylistában](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést E értékesítés Manager Remix "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a partner felhasználót E értékesítés Manager Remix a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a E értékesítés Manager Remix közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést az E értékesítés Manager Remix tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy E értékesítés Manager Remix tesztfelhasználó](#create-an-e-sales-manager-remix-test-user)**  - való egy megfelelője a Britta Simon E értékesítés Manager Remix, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az E értékesítés Manager Remix alkalmazásban egyszeri bejelentkezés konfigurálása.

**E értékesítés Manager Remix konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **E értékesítés Manager Remix** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Az a **E értékesítés Manager Remix tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információkat E értékesítés Manager Remix tartomány és az URL-címek](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Server-Based-URL>/<sub-domain>/`

    c. Másolás a **azonosító** érték a Jegyzettömbben. Az oktatóanyag későbbi részében azonosító értéket fogja használni.
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [E értékesítés Manager Remix ügyfél-támogatási csoport](mailto:esupport@softbrain.co.jp) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Válassza ki **nézet és egyéb felhasználói attribútumok szerkesztése** , majd kattintson a **emailaddress** attribútum.
    
    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Másolás a **Namespace** és **neve** a jogcím a beviteli mező értékét. Az érték létrehozása a következő mintában - `<Namespace>/<Name>`. Ezt az értéket az oktatóanyag későbbi részében fogja használni.

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. A a **E értékesítés Manager Remix konfigurációs** kattintson **konfigurálása E értékesítés Manager Remix** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Jelentkezzen be rendszergazdaként az E értékesítés Manager Remix alkalmazás.

10. Válassza ki **Rendszergazda menü** jobb felső sarokban a menüből.

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Válassza ki **rendszerbeállítások**>**külső rendszerekből való együttműködés**

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Válassza ki **SAML**

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. Az a **SAML-hitelesítési beállítás** területen tegye a következőket:

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Válassza ki **PC-verzió**
    
    b. Válassza ki **e-mail** a együttműködve a legördülő menüből konfigurációelem-szakasz.

    c. Az együttműködés elem szövegmezőjének illessze be a **jogcím értéke** az Azure portál Egytényezős a másolt `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Az a **kibocsátó (entitás azonosítója)** szövegmező, illessze be a **azonosító** érték, amely az Azure-portálról másolta **E értékesítés Manager Remix tartomány és az URL-címek** szakasz.

    e. Töltse fel a letöltött **tanúsítvány** válassza ki az Azure-portálon **kijelölés fájl**.

    f. A **azonosító szolgáltató bejelentkezési URL-cím** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** másolt Azure-portálon.

    g. A **Identity Provider kijelentkezési URL-cím** szövegmezőhöz Beillesztés **Sign-Out URL-cím** érték, amely az Azure-portálon másolta.

    h. Kattintson a **beállítása kész**

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Az E értékesítés Manager Remix tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként az E értékesítés Manager Remix alkalmazás.

2. Válassza ki **Rendszergazda menü** jobb felső sarokban a menüből.

    ![E értékesítési vezető Remix konfiguráció](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Válassza ki **a vállalati beállítások**>**karbantartás a részleg és az alkalmazottak** válassza **regisztrált alkalmazottak**.

    ![A felhasználó](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. Az a **új alkalmazott regisztrációs** területen tegye a következőket:
    
    ![A felhasználó](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. Az a **alkalmazott neve** szövegmező, írja be például a Britta felhasználói nevét.

    b. Az összes megfelelő kötelező mezők kitöltése a felhasználói adatokat.
    
    c. SAML engedélyezése, ha a rendszergazda nem fogja tudni jelentkezni a bejelentkezési képernyőt, így ad rendszergazdai bejelentkezési jogosultságokat a felhasználó kiválasztásával a **rendszergazdai bejelentkezés**

    d. Kattintson a **regisztrációs**

5. A későbbiekben, ha azt szeretné, hogy jelentkezzen be rendszergazdaként, jelentkezzen be a felhasználó a rendszergazda engedélyt kapott, és kattintson a **Rendszergazda menü** jobb felső sarokban a menüből.

    ![A felhasználó](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés E értékesítés Manager Remix Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése E értékesítés Manager Remix, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **E értékesítés Manager Remix**.

    ![Az alkalmazások listáját a E értékesítés Manager Remix hivatkozás](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen E értékesítés Manager Remix csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az E értékesítés Manager Remix alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

