---
title: "Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - rendszergazda felhasználói felület |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Palo Alto hálózatok - rendszergazda felhasználói felület közötti."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 5dca976449ee856cc61407d0eae831fc1e1e7a50
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Oktatóanyag: Azure Active Directoryval integrált Palo Alto hálózatok - rendszergazda felhasználói felület

Ebben az oktatóanyagban elsajátíthatja Palo Alto hálózatok - rendszergazda felhasználói felület az Azure Active Directoryval (Azure AD) integrálása.

Palo Alto hálózatok - rendszergazda felhasználói felület és az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Palo Alto hálózatok - rendszergazda felhasználói felület szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Palo Alto hálózatokhoz - rendszergazda felhasználói felület (egyszeri bejelentkezés) az Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Palo Alto hálózatokkal - rendszergazda felhasználói felületén, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Palo Alto hálózatok - felügyeleti felhasználói felület egyszeri bejelentkezés engedélyezve az előfizetéshez

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Palo Alto hálózatok - hozzáadása a felügyeleti felhasználói felület a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Palo Alto hálózatok - hozzáadása a felügyeleti felhasználói felület a gyűjteményből
Palo Alto hálózatok - rendszergazda felhasználói felület az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto hálózatok - rendszergazda felhasználói Felületet a gyűjteményből a kezelt SaaS-alkalmazások listáját.

**Adja hozzá a Palo Alto hálózatok - felügyeleti felhasználói felület a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Palo Alto hálózatok - rendszergazda felhasználói felület**, jelölje be **Palo Alto hálózatok - rendszergazda felhasználói felület** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Palo Alto hálózatok - felügyeleti felhasználói felület az eredménylistában](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal tesztelése és konfigurálása,-rendszergazda felhasználói felület "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Palo Alto hálózatokban - rendszergazda felhasználói felületén a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Palo Alto hálózatokban - hivatkozás kapcsolatának felügyeleti felhasználói felület kell létrehozni.

Palo Alto hálózatokban - rendszergazda felhasználói felületén, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Palo Alto hálózatokkal - tesztelése és konfigurálása felügyeleti felhasználói felület kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Palo Alto hálózatok - rendszergazda felhasználói felület tesztfelhasználó létrehozása](#create-a-palo-alto-networks---admin-ui-test-user)**  - rendelkezik egy megfelelője a Britta Simon Palo Alto hálózatokból - rendszergazda felhasználói felület, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Palo Alto hálózatokon - rendszergazda felhasználói felület alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Palo Alto hálózatok - rendszergazda felhasználói felületén, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Palo Alto hálózatok - rendszergazda felhasználói felület** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. Az a **Palo Alto hálózatok - rendszergazda felhasználói felület tartomány és az URL-címek** területen tegye a következőket:

    ![Palo Alto hálózatok - rendszergazda felhasználói felület tartomány és URL-címek egyszeri bejelentkezés információk](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<Customer Firewall URL>/php/login.php`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Palo Alto hálózatok - rendszergazda felhasználói felület ügyfél-támogatási csoport](https://support.paloaltonetworks.com/support) beolvasni ezeket az értékeket. 
 
4. Palo Alto hálózatok - rendszergazda felhasználói felület alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumnak. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanel, SAML-jogkivonat attribútum konfigurálása a fenti ábrán látható módon, és hajtsa végre a következő lépéseket: Ügyeljen arra, hogy attribútumértékek csak, például a megfelelő értékeket a felhasználónév és a adminrole rendeljen. Nincs más nem kötelező attribútum "accessdomain", amely a rendszergazdai hozzáférés korlátozása a tűzfalon az adott virtuális rendszereken használható.
        
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | felhasználónév | User.userPrincipalName |
    | adminrole | customadmin |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

    > [!NOTE]
    > Olvassa el a következő cikkekben olvashat az attribútumokat.
    > 1. Felügyeleti felhasználói felület (adminrole) rendszergazdai szerepkör profilja: https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. Eszköz hozzáférési tartományi rendszergazda felhasználói felülete (accessdomain): https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. Nyissa meg a Palo Alto egy másik böngészőablakban rendszergazdaként.

9. Kattintson a **eszköz**.

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Válassza ki **SAML-Identitásszolgáltatóként** a bal oldali navigációs sáv megnyitásához, majd kattintson a "Importálása" a metaadatait tartalmazó fájl importálása.

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Hajtsa végre a következő importálási ablakban műveletek

    ![Palo Alto egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Az a **profilnév** szövegmező, adjon meg egy nevet például Azure AD felügyeleti felhasználói felület.
    
    b. A **Identity Provider metaadatok**, kattintson a **Tallózás** jelölje ki a metadata.xml fájlt, amely az Azure-portálról letöltött
    
    c. Kattintson az **OK** gombra

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Palo Alto hálózatok - rendszergazda felhasználói felület tesztfelhasználó létrehozása

Palo Alto hálózatok - rendszergazda felhasználói felület csak időponthoz kötött támogatja a felhasználók átadása, a felhasználó automatikusan létrejön a rendszer a sikeres hitelesítés után már nem létezik. Nem kell itt bármely művelet elvégzésére.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Palo Alto hálózatok - rendszergazda felhasználói felület használata.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Palo Alto hálózatok - rendszergazda felhasználói felületén, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Palo Alto hálózatok - rendszergazda felhasználói felület**.

    ![Palo Alto hálózatok - rendszergazda felhasználói felület hivatkozásra az alkalmazások listáját](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Palo Alto hálózatok - rendszergazda felhasználói felület csempe a hozzáférési panelen kattintva meg kell beolvasni automatikusan bejelentkezett a Palo Alto hálózatokhoz - rendszergazda felhasználói felület alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

