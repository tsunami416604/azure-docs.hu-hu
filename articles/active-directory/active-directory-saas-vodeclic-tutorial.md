---
title: "Oktatóanyag: Azure Active Directoryval integrált Vodeclic |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Vodeclic között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Oktatóanyag: Azure Active Directoryval integrált Vodeclic

Ebben az oktatóanyagban elsajátíthatja Vodeclic integrálása az Azure Active Directory (Azure AD).

Vodeclic integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Vodeclic szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezve Vodeclic (egyszeri bejelentkezés, vagy az SSO) és az Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Vodeclic, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Előfizetés Vodeclic SSO engedélyezése

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Vodeclic hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-vodeclic-from-the-gallery"></a>Adja hozzá a Vodeclic a gyűjteményből
Az Azure AD integrálása a Vodeclic konfigurálásához kell hozzáadnia Vodeclic a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Vodeclic hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Vodeclic**. Válassza ki **Vodeclic** az eredmények panelen, és válassza ki azt a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Vodeclic

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó Vodeclic egy felhasználó számára az Azure AD. Más szóval kell Vodeclic az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

Vodeclic, biztosítanak a érték **felhasználónév** azonos érték, mint a **felhasználónév** az Azure ad-ben. Most hozott létre a hivatkozást a két felhasználó között.

Az Azure AD egyszeri bejelentkezést a Vodeclic tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Vodeclic tesztfelhasználó létrehozása](#create-a-vodeclic-test-user) való Britta Simon valami Vodeclic, amely csatolva van a felhasználó az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Vodeclic alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása Vodeclic, tegye a következőket:**

1. Az Azure portálon a a **Vodeclic** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanel **módot Single-Sign-on**, jelölje be **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Ha az alkalmazás a konfigurálni kívánt **IDP** indított módban, a **Vodeclic tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Vodeclic tartomány és az URL-címek](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. Az a **azonosító** mezőbe írja be a következő mintát olyan URL-címe:`https://<companyname>.lms.vodeclic.net/auth/saml`

    b. Az a **válasz URL-CÍMEN** mezőbe írja be a következő mintát olyan URL-címe:`https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módot, válassza ki a **megjelenítése speciális URL-beállításainak** jelölőnégyzetet, majd a következő lépéssel:

    ![Az egyszeri bejelentkezés információk Vodeclic tartomány és az URL-címek](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    Az a **bejelentkezési URL-cím** mezőbe írja be a következő mintát olyan URL-címe:`https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Ezek az értékek nem valódi. Frissítheti ezeket az értékeket a tényleges azonosítójú válasz URL-címet, és a bejelentkezési URL-cím. Lépjen kapcsolatba a [Vodeclic ügyfél-támogatási csoport](mailto:hotline@vodeclic.com) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**. Mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Egyszeri bejelentkezés konfigurálása a **Vodeclic** oldalán, küldjön a letöltött **metaadatainak XML-kódja** számára a [Vodeclic támogatási csoport](mailto:hotline@vodeclic.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com) közben állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez a dokumentáció a **konfigurációs** alsó szakasz. További, a beágyazott dokumentáció szolgáltatásról [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-vodeclic-test-user"></a>Vodeclic tesztfelhasználó létrehozása

Ebben a szakaszban egy Vodeclic Britta Simon nevű felhasználót hoz létre. Együttműködik az [Vodeclic támogatási csoport](mailto:hotline@vodeclic.com) a felhasználók hozzáadása a Vodeclic platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

> [!NOTE]
> Alkalmazás követelményeinek megfelelően szükség lehet lekérni a gép szerepel az engedélyezési listán. Hogy megtörténjen-e, meg kell osztani a nyilvános IP-cím a [Vodeclic támogatási csoport](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Vodeclic Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Vodeclic, tegye a következőket:**

1. Az Azure portálon az alkalmazások nézet megnyitásához, és keresse meg a könyvtár nézet. A következő Ugrás **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Vodeclic**.

    ![Az alkalmazások listáját a Vodeclic hivatkozás](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzáadása hozzárendelés** párbeszédpanel megnyitásához.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

A Vodeclic csempe a hozzáférési panelen válassza ki, amikor első automatikusan jelentkezett be a Vodeclic alkalmazás.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

