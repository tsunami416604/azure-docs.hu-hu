---
title: "Oktatóanyag: Azure Active Directoryval integrált Coupa |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Coupa között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 30149f181d8b0ebdc1ae6820da5d561f3a942fa3
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Oktatóanyag: Azure Active Directoryval integrált Coupa

Ebben az oktatóanyagban elsajátíthatja Coupa integrálása az Azure Active Directory (Azure AD).

Coupa integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Coupa szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Coupa (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Coupa, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Coupa egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Coupa hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-coupa-from-the-gallery"></a>A gyűjteményből Coupa hozzáadása
Az Azure AD integrálása a Coupa konfigurálásához kell hozzáadnia Coupa a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Coupa hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Coupa**, jelölje be **Coupa** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Coupa](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Coupa.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Coupa a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Coupa közötti kapcsolat kapcsolatot kell létrehozni.

Coupa, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Coupa tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Coupa tesztfelhasználó létrehozása](#create-a-coupa-test-user)**  - való Britta Simon valami Coupa, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Coupa alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Coupa, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Coupa** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_samlbase.png)

3. Az a **Coupa tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Coupa tartomány és az URL-címek](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`http://<companyname>.Coupa.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`<companyname>.coupahost.com`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.coupahost.com/sp/ACS.saml2`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosítóját és a válasz URL-CÍMEN. Ügyfél [Coupa ügyfél-támogatási csoport](https://success.coupa.com/Support/Contact_Us?) beolvasni ezeket az értékeket. a metaadatok, az oktatóanyag későbbi részében ismertetett elérhetővé válik a válasz URL-címével.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-coupa-tutorial/tutorial_general_400.png)

6. Jelentkezzen be rendszergazdaként a Coupa vállalati webhely.

7. Ugrás a **telepítő \> biztonsági ellenőrzést**.
   
   ![Biztonsági vezérlők](./media/active-directory-saas-coupa-tutorial/ic791900.png "biztonsági vezérlőket")

8. Az a **jelentkezzen be Coupa hitelesítő adatok használatával** területen tegye a következőket:

    ![Coupa SP metaadatok](./media/active-directory-saas-coupa-tutorial/ic791901.png "Coupa SP metaadatok")
    
    a. Válassza ki **jelentkezzen be SAML**.
    
    b. A számítógépre a Coupa metaadatait tartalmazó fájl letöltéséhez kattintson **letöltési és SP metaadatok importálása**. Nyissa meg a metaadatok, másolja a **AssertionConsumerService index/URL-cím** értékét, illessze be az érték a **válasz URL-CÍMEN** textbox a **Coupa tartomány és az URL-címek** szakasz. 
    
    c. Kattintson a **Tallózás** feltöltése az Azure-portálról letöltött metaadatok.
    
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-coupa-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-coupa-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-coupa-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-coupa-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-coupa-test-user"></a>Coupa tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Coupa bejelentkezni, akkor ki kell építenie Coupa be.  

* Coupa, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Coupa** vállalati hely rendszergazdaként.

2. Kattintson a felső menüben **telepítő**, és kattintson a **felhasználók**.
   
   ![Felhasználók](./media/active-directory-saas-coupa-tutorial/ic791908.png "felhasználók")

3. Kattintson a **Create** (Létrehozás) gombra.
   
   ![Felhasználók létrehozása](./media/active-directory-saas-coupa-tutorial/ic791909.png "felhasználók létrehozása")

4. Az a **felhasználó hozhat létre** területen tegye a következőket:
   
   ![Felhasználó adatait](./media/active-directory-saas-coupa-tutorial/ic791910.png "felhasználó részletei")
   
   a. Típus a **bejelentkezési**, **Utónév**, **Vezetéknév**, **egyszeri bejelentkezési azonosító**, **E-mail** egy érvényes Azure Active Directory-fiókot szeretné azokat a kapcsolódó szövegmezők rendelkezés attribútumait.

   b. Kattintson a **Create** (Létrehozás) gombra.   
   
   >[!NOTE]
   >Az Azure Active Directory fióktulajdonos kap egy e-mailt egy hivatkozás, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók. 
   > 

>[!NOTE]
>Bármely más Coupa felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Coupa által nyújtott API-k. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Coupa Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Coupa, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Coupa**.

    ![Az alkalmazások listáját a Coupa hivatkozás](./media/active-directory-saas-coupa-tutorial/tutorial_coupa_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Coupa csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Coupa alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-coupa-tutorial/tutorial_general_203.png

