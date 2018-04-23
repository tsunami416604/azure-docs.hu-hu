---
title: 'Oktatóanyag: Azure Active Directoryval integrált BUKÓÍV |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és BUKÓÍV között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: 43dc50d0a5381ace8bcfeb3cae39e249ba743876
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Oktatóanyag: Azure Active Directoryval integrált BUKÓÍV

Ebben az oktatóanyagban elsajátíthatja BUKÓÍV integrálása az Azure Active Directory (Azure AD).

Bukóív integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér BUKÓÍV szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Bukóív (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs BUKÓÍV konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy BUKÓÍV egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből BUKÓÍV hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-rollbar-from-the-gallery"></a>A gyűjteményből BUKÓÍV hozzáadása
Az Azure AD integrálása a BUKÓÍV konfigurálásához kell hozzáadnia BUKÓÍV a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből BUKÓÍV hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **BUKÓÍV**, jelölje be **BUKÓÍV** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában BUKÓÍV](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján BUKÓÍV.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó BUKÓÍV a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a BUKÓÍV közötti kapcsolat kapcsolatot kell létrehozni.

BUKÓÍV, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a BUKÓÍV tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Bukóív tesztfelhasználó létrehozása](#create-a-rollbar-test-user)**  - való egy megfelelője a Britta Simon BUKÓÍV, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az BUKÓÍV alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés BUKÓÍV, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **BUKÓÍV** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. Az a **BUKÓÍV tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk BUKÓÍV tartomány és az URL-címek](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím: `https://saml.rollbar.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://rollbar.com/<accountname>/saml/sso/azure/`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk BUKÓÍV tartomány és az URL-címek](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [BUKÓÍV ügyfél-támogatási csoport](mailto:support@rollbar.com) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. Egy másik webes böngészőablakban jelentkezzen be a BUKÓÍV vállalati webhely rendszergazdaként.

8. Kattintson a a **Profilbeállítások** a jobb felső sarkában, és kattintson a **fióknév beállítások**.
    
    ![Konfiguráció](./media/active-directory-saas-rollbar-tutorial/general.png)

9. Kattintson a **identitásszolgáltató** biztonsági csoportban.

    ![Konfiguráció](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. Az a **SAML-Identitásszolgáltatóként** területen tegye a következőket:
    
    ![Konfiguráció](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. Válassza ki **AZURE** a a **SAML-Identitásszolgáltatóként** legördülő menüből.

    b. Nyissa meg a metaadat-fájlt a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **SAML metaadatok** szövegmező.

    c. Kattintson a **Save** (Mentés) gombra.

11. Miután rákattintott a Mentés gombra, a képernyő lesz ehhez hasonló:
    
    ![Konfiguráció](./media/active-directory-saas-rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Ahhoz, hogy a következő lépés elvégzése után, először hozzá kell saját kezűleg felhasználóként a BUKÓÍV alkalmazásba az Azure-ban.
    a. Ha szeretné-e előírása minden felhasználónak hitelesíteni Azure keresztül, majd kattintson az **jelentkezzen be az identitásszolgáltató keresztül** keresztül Azure újra hitelesíteni.  

    b.  Után a rendszer irányítja át a képernyőt, válassza a **szükséges bejelentkezési SAML-Identitásszolgáltatóként keresztül** jelölőnégyzetet.

    b. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-rollbar-test-user"></a>Bukóív tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BUKÓÍV bejelentkezni, akkor ki kell építenie a BUKÓÍV. BUKÓÍV, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a BUKÓÍV vállalati webhely.

2. Kattintson a a **Profilbeállítások** a jobb felső sarkában, és kattintson a **fióknév beállítások**.

    ![Felhasználó](./media/active-directory-saas-rollbar-tutorial/general.png)

3. Kattintson a **felhasználók**.
    
    ![Alkalmazott hozzáadása](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. Kattintson a **hívhat meg a csoport tagjai**.

    ![Felkérése](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. A szövegmezőben adja meg a nevét, például a felhasználó  **brittasimon@contoso.com**  , és kattintson a **Add/meghívás**.

    ![Felkérése](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. Felhasználó felkérést kap, és elfogadása után többé létrehozva a rendszerben.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BUKÓÍV Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése BUKÓÍV, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **BUKÓÍV**.

    ![Az alkalmazások listáját a BUKÓÍV hivatkozás](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen BUKÓÍV csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az BUKÓÍV alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

