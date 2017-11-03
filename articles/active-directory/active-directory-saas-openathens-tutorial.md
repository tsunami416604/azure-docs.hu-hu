---
title: "Oktatóanyag: Azure Active Directoryval integrált OpenAthens |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és OpenAthens között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 2819e300d94a0bc3b0900419218561fc846effcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Oktatóanyag: Azure Active Directoryval integrált OpenAthens

Ebben az oktatóanyagban elsajátíthatja OpenAthens integrálása az Azure Active Directory (Azure AD).

OpenAthens integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér OpenAthens szabályozhatja.
- Engedélyezheti a felhasználók automatikusan bejelentkezéséhez OpenAthens (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs OpenAthens, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy OpenAthens egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [ingyenes egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből OpenAthens hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-openathens-from-the-gallery"></a>A gyűjteményből OpenAthens hozzáadása
Az Azure AD integrálása a OpenAthens konfigurálásához kell hozzáadnia OpenAthens a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből OpenAthens hozzáadása**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Keresse meg a **vállalati alkalmazások**, majd lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások ablaktábla][2]
    
3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **OpenAthens**, jelölje be **OpenAthens** az eredmények panelen, és válassza ki azt a **Hozzáadás** gombra.

    ![Az eredménylistában OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján OpenAthens

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó OpenAthens Újdonságok a felhasználó számára az Azure ad-ben. Más szóval kell egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására a OpenAthens.

OpenAthens, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a OpenAthens tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on), hogy lehetővé teszi a felhasználók a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user), az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Hozzon létre egy OpenAthens tesztfelhasználó](#create-a-openathens-test-user), hogy Britta Simon valami, amely csatolva van a felhasználó az Azure AD ábrázolását OpenAthens.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on), hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az OpenAthens alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés OpenAthens konfigurálása**

1. Az Azure portálon a a **OpenAthens** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Az egyszeri bejelentkezés hivatkozás konfigurálása][4]

2. Is engedélyezhető az egyszeri bejelentkezés, a **egyszeri bejelentkezés** párbeszédpanelen jelölje ki **SAML-alapú bejelentkezés** , a **mód**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. Az a **OpenAthens tartomány és az URL-címek** területen adja meg az érték `https://login.openathens.net/saml/2/metadata-sp` a a **azonosító** szövegmezőben.

    ![Az egyszeri bejelentkezés információk OpenAthens tartomány és az URL-címek](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **metaadatainak XML-kódja**, és mentse a metaadat-fájlt a számítógépen.

    ![A AMSL aláíró tanúsítvány letöltése hivatkozás](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Válassza ki a **Mentés** gombot.

    ![Az egyszeri bejelentkezés mentési gomb](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a OpenAthens vállalati webhely rendszergazdaként.

7. Válassza ki **kapcsolatok** alatt a listából a **felügyeleti** fülre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Válassza ki **SAML 1.1-es vagy 2.0-s**, majd válassza ki a **konfigurálása** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. A konfiguráció hozzáadásához válassza ki a **Tallózás** gombra kattint, az Azure-portálról letöltött metaadatok .xml fájl feltöltéséhez, majd válassza ki **Hozzáadás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. A következő lépések alapján a **részletek** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. A **megjelenítési névhozzárendelés**, jelölje be **attribútum használata**.

    b. Az a **megjelenített név attribútum** szöveg mezőbe írja be az érték `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. A **egyedi felhasználói hozzárendelésének**, jelölje be **attribútum használata**.

    d. Az a **egyedi felhasználói attribútum** szöveg mezőbe írja be az érték `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. A **állapot**, jelölje be mind a három jelölőnégyzetet.

    f. A **helyi fiókok létrehozása**, jelölje be **automatikusan**.

    g. Válassza ki **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg a állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez a dokumentáció a **konfigurációs** alsó szakasz. A beágyazott dokumentáció szolgáltatásról kapcsolatban bővebben lásd: a [az Azure AD dokumentációjában beágyazott](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon, úgynevezett "Britta Simon."

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőben **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be az e-mail cím a Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** szövegmezőben.

    d. Kattintson a **Létrehozás** gombra.
  
### <a name="create-an-openathens-test-user"></a>Hozzon létre egy OpenAthens tesztfelhasználó számára

OpenAthens támogatja a létesítést just-in-time, és a felhasználók a sikeres hitelesítés után automatikusan jönnek létre. Nem kell ebben a szakaszban bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított a hozzáférés OpenAthens Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**OpenAthens Britta Simon hozzárendelése**

1. Az Azure portálon, nyissa meg az alkalmazások megtekintéséhez keresse meg a könyvtár nézet, és navigáljon **vállalati alkalmazások**, majd válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az a **alkalmazások** listáról válassza ki **OpenAthens**.

    ![Az alkalmazások listáját a OpenAthens hivatkozás](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzáadása hozzárendelés** ablaktáblán.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** listáról válassza ki **Britta Simon**.

6. Válassza ki a **válasszon** gombra a **felhasználók és csoportok** listája.

7. Válassza ki a **hozzárendelése** gombra a **hozzáadása hozzárendelés** ablaktáblán.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha bejelöli a **OpenAthens** csempére a hozzáférési panelen, hogy kell automatikusan megtörténik az OpenAthens alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók listájáért lásd: [SaaS app integrációs oktatóanyagok használata az Azure ad-val](active-directory-saas-tutorial-list.md).
* Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval kapcsolatos további információkért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

