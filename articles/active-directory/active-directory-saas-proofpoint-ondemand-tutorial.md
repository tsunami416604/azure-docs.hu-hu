---
title: "Oktatóanyag: Azure Active Directoryval integrált az igény szerinti Proofpoint |} Microsoft Docs"
description: "Megtudhatja, hogyan igény szerinti konfigurálása egyszeri bejelentkezés Azure Active Directory és Proofpoint között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 55479406487bf445c5f449b13663c0bfaee751fd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Oktatóanyag: Azure Active Directoryval integrált Proofpoint igény szerint

Ebben az oktatóanyagban elsajátíthatja az igény szerinti Proofpoint integrálása az Azure Active Directory (Azure AD).

Az igény szerinti Proofpoint integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az igény szerinti Proofpoint szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Proofpoint az igény szerinti (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD rendszerrel történő integráció konfigurálása az igény szerinti Proofpoint, szüksége van a következőkre:

- Az Azure AD szolgáltatásra
- Egy igény szerinti egyszeri bejelentkezés engedélyezve van az előfizetésben lévő Proofpoint

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Proofpoint igény szerinti hozzáadásával a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Proofpoint igény szerinti hozzáadásával a gyűjteményből
Proofpoint integrálása igény szerint az Azure AD-be történő konfigurálásához szüksége a katalógusban az igény szerinti Proofpoint hozzáadása a kezelt SaaS-alkalmazások listáját.

**Adja hozzá az igény szerinti Proofpoint a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **az igény szerinti Proofpoint**, jelölje be **az igény szerinti Proofpoint** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az igény szerinti az eredménylistában Proofpoint](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Proofpoint az igény szerinti "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Proofpoint az igény szerinti megfelelője felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az igény szerinti Proofpoint kapcsolódó felhasználói, valamint az Azure AD-felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az igény szerinti Proofpoint, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az igény szerinti Proofpoint tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Proofpoint az igény szerinti tesztfelhasználó](#create-a-proofpoint-on-demand-test-user)**  - való egy megfelelője a Britta Simon Proofpoint igény szerint, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az igény szerinti alkalmazástól Proofpoint egyszeri bejelentkezés konfigurálása.

**Az igény szerinti Proofpoint konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **az igény szerinti Proofpoint** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_samlbase.png)

3. Az a **Proofpoint igény szerinti tartomány és az URL-címekről** területen tegye a következőket:

    ![Igény szerinti tartomány és az URL-címek egyetlen bejelentkezési adatok Proofpoint](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<hostname>.pphosted.com/ppssamlsp`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Proofpoint az igény szerinti ügyfél-támogatási csoport](https://www.proofpoint.com/us/support-services) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_400.png)
    
7. A a **igény szerinti konfigurációban Proofpoint** kattintson **Proofpoint konfigurálása az igény szerinti** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Igény szerinti konfigurációban Proofpoint](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **az igény szerinti Proofpoint** oldalon kell küldeniük a letöltött **Certificate(Base64)**, **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** való [Proofpoint igény támogatási csoport](https://www.proofpoint.com/us/support-services). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-proofpoint-on-demand-test-user"></a>Hozzon létre egy Proofpoint az igény szerinti tesztfelhasználó számára

Ebben a szakaszban az igény szerinti Proofpoint Britta Simon nevű felhasználó létrehozása. Együttműködve [Proofpoint az igény szerinti ügyfél-támogatási csoport](https://www.proofpoint.com/us/support-services) felhasználót is hozzáadhat a Proofpoint igény szerint a platformon.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít az igény szerinti Proofpoint által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Az igény szerinti Proofpoint Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Proofpoint az igény szerinti**.

    ![Az alkalmazások listáját az igény szerinti kapcsolat Proofpoint](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A igény szerinti csempére a hozzáférési panelen Proofpoint gombra kattintva meg kell beolvasni automatikusan aláírt a a Proofpoint igény szerint az alkalmazástól.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png

