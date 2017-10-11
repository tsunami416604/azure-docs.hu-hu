---
title: "Oktatóanyag: Azure Active Directoryval integrált etouches |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és etouches között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9e9d6aae924369065ca492b1f6380c0ddc5fe
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Oktatóanyag: Azure Active Directoryval integrált etouches

Ebben az oktatóanyagban elsajátíthatja etouches integrálása az Azure Active Directory (Azure AD).

Etouches integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a etouches hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a etouches (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs etouches, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy etouches egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből etouches hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-etouches-from-the-gallery"></a>A gyűjteményből etouches hozzáadása
Az Azure AD integrálása a etouches konfigurálásához kell hozzáadnia etouches a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből etouches hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **etouches**, jelölje be **etouches** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredménylistában etouches](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján etouches.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó etouches a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a etouches közötti kapcsolat kapcsolatot kell létrehozni.

Etouches, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a etouches tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy etouches tesztfelhasználó](#create-an-etouches-test-user)**  - Britta Simon egy partner, a felhasználó az Azure AD ábrázolását kapcsolódó etouches rendelkezik.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az etouches alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés etouches, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **etouches** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_samlbase.png)

3. Az a **etouches tartomány és az URL-címek** területen tegye a következőket:

    ![az egyszeri bejelentkezési adatokat etouches tartomány és az URL-címek](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. A tényleges bejelentkezési URL-cím és az azonosítóját, amelynek az ismertetése, az oktatóanyag későbbi részében frissítse az értéket.
    > 

4. etouches alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútum** az alkalmazás. Az alábbi képernyőfelvételen látható egy példa a. 

    ![Felhasználói attribútum](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_attribute.png) 

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum-érték |
    | ------------------- | -------------------- |
    | E-mail cím | User.mail |    
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Attribútum hozzáadása](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_04.png)

    ![Attribútum párbeszédpanel hozzáadása](./media/active-directory-saas-etouches-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra. 

6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-etouches-tutorial/tutorial_general_400.png)

8. Ahhoz, hogy az egyszeri bejelentkezés az alkalmazáshoz konfigurált, a következő lépésekkel az etouches alkalmazásban: 

    ![etouches konfiguráció](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png) 

    a. Jelentkezzen be **etouches** alkalmazást a rendszergazdai jogosultságokkal.
   
    b. Lépjen a **SAML** konfigurációs.

    c. Az a **általános beállítások** szakaszt, nyissa meg a letöltött tanúsítvány Azure-portálon a Jegyzettömbben, másolja a tartalmat, és illessze be a kiállító terjesztési hely metaadatok szövegmezőjét. 

    d. Kattintson a **mentése & kapcsolat** gombra.
  
    e. Kattintson a **frissítéshez tartozó metaadatokat** a SAML metaadatszakasz gombjára. 

    f. Ez megnyitja a lapot, és hajtsa végre az egyszeri Bejelentkezést. Miután működik-e az SSO majd állíthat be a felhasználónevet.

    g. A felhasználónév mezőben válassza ki a **emailaddress** az alábbi ábrán látható módon. 

    h. Másolás a **SP Entitásazonosító** értékét, és illessze be azt a **azonosító** szövegmező, amely **etouches tartomány és az URL-címek** Azure-portál szakaszban.

    i. Másolás a **egyszeri bejelentkezési URL-cím / ACS** értékét, és illessze be azt a **bejelentkezési URL-cím** szövegmező, amely **etouches tartomány és az URL-címek** Azure-portál szakaszban.
   
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-etouches-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-etouches-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-etouches-test-user"></a>Hozzon létre egy etouches tesztfelhasználó számára

Ebben a szakaszban egy etouches Britta Simon nevű felhasználót hoz létre. Együttműködve [etouches ügyfél-támogatási csoport](https://www.etouches.com/event-software/support/customer-support/) a felhasználók hozzáadása a etouches platform.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó etouches való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése etouches, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **etouches**.

    ![Az alkalmazások listáját a etouches hivatkozás](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése


Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen etouches csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az etouches alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png

