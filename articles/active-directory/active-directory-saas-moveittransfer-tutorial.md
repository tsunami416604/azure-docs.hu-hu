---
title: "Oktatóanyag: Azure Active Directoryval integrált MOVEit átadás - Azure AD-integrációs |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és MOVEit átadás - integráció az Azure AD között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: d35aceb9be2d0ff49f86a00cc84f5deb198d88f0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: Azure Active Directoryval integrált MOVEit átadás - Azure AD-integrációs

Ebben az oktatóanyagban elsajátíthatja MOVEit átadás - Azure AD integrálása az Azure Active Directoryval (Azure AD) integrálása.

MOVEit átadás - Azure AD integrálása az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér MOVEit átadás - Azure AD-integrációs szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett MOVEit átviteléig – az Azure AD integrálása (egyszeri bejelentkezés) az Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs MOVEit átadás - Azure AD-integrációs, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A MOVEit átvitel – az Azure AD integrálása egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Hozzáadás MOVEit átadás - Azure AD integrálása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Hozzáadás MOVEit átadás - Azure AD integrálása a gyűjteményből
A MOVEit átadás - az Azure AD integrálása az Azure AD-integráció konfigurálása kell hozzáadnia a MOVEit átadás - az Azure AD integrálása a gyűjteményből listájára felügyelt SaaS-alkalmazásokhoz.

**Adja hozzá a MOVEit átadás - Azure AD integrálása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **MOVEit átadás - Azure AD-integrációs**, jelölje be **MOVEit átadás - Azure AD-integrációs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel a az alkalmazás.

    ![MOVEit átadás - Azure AD integrálása az eredménylistában](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a MOVEit átadás - Azure AD-integrációs "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó MOVEit átadás - Azure AD-integrációs a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a MOVEit átadás - hivatkozás kapcsolata az Azure AD-integrációs kell létrehozni.

Az MOVEit átviteli - Azure AD-integrációs, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a MOVEit átadás - tesztelése és konfigurálása az Azure AD-integrációs kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy MOVEit átadás - az Azure AD-integrációs tesztfelhasználó](#create-a-moveit-transfer---azure-ad-integration-test-user)**  - való egy megfelelője a Britta Simon MOVEit átadás - Azure AD-integrációs, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a MOVEit átadás - Azure AD-integrációs alkalmazást az egyszeri bejelentkezés konfigurálása.

**Az Azure AD konfigurálása egyszeri bejelentkezéshez az MOVEit átadás - Azure AD-integrációs, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **MOVEit átadás - Azure AD-integrációs** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. Az a **MOVEit átviteli - tartomány az Azure AD-integrációs és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://contoso.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://contoso.com/<tenatid>`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Olvassa el ezeket az értékeket később a **szolgáltató metaadatainak URL-címe** szakaszban, vagy forduljon a [MOVEit átadás - az Azure AD integrálása ügyfél támogatási csoport](https://community.ipswitch.com/s/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. Jelentkezzen be rendszergazdaként a MOVEit átviteli bérlő.

7. A bal oldali navigációs ablaktábláján kattintson **beállítások**.

    ![Beállítások szakaszban az alkalmazás ügyféloldali](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. Kattintson a **egyszeri bejelentkezés** hivatkozás, amely alatt **biztonsági házirendek -> felhasználói hitelesítési**.

    ![Biztonsági házirendek az alkalmazás ügyféloldali](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. Kattintson a metaadatok hivatkozásra kattintva töltse le a metaadat-dokumentum.

    ![Szolgáltató metaadatainak URL-címe](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Győződjön meg arról **entityid beállítást** megfelelő **azonosító** a a **MOVEit átviteli - tartomány az Azure AD-integrációs és URL-címek** szakasz.
    * Győződjön meg arról **AssertionConsumerService** hely URL-címe megegyezik **válasz URL-CÍMEN** a a **MOVEit átviteli - tartomány az Azure AD-integrációs és URL-címek** szakasz.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. Kattintson a **identitásszolgáltató hozzáadása** új összevont identitás szolgáltató hozzáadása gomb.

    ![Identitás-szolgáltató felvétele](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. Kattintson a **Tallózás...**  válassza ki a metaadatok fájlt, amely az Azure-portálról letöltött, majd kattintson a **identitásszolgáltató hozzáadása** fel kell töltenie a letöltött fájlt.

    ![SAML-Identitásszolgáltatóként](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. Jelölje ki "**Igen**" mint **engedélyezett** a a **összevont identitás Szolgáltatóbeállítások szerkesztése...**  lapot, és kattintson **mentése**.

    ![Összevont identitás-szolgáltató beállításai](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. Az a **összevont identitás szolgáltató felhasználói beállítások szerkesztése** lapon, a következő műveleteket:
    
    ![Összevont identitás Szolgáltatóbeállítások szerkesztése](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Válassza ki **SAML NameID** , **bejelentkezési név**.
    
    b. Válassza ki **más** , **teljes nevét** és a a **attribútumnév** szövegmezőbe írja be az értéket: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Válassza ki **más** , **E-mail** és a a **attribútumnév** szövegmezőbe írja be az értéket: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Válassza ki **Igen** , **fiók automatikus létrehozása frissítsen**.
    
    e. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Hozzon létre egy MOVEit átadás - Azure AD-integrációs teszt felhasználó

Ez a szakasz célja Britta Simon meghívta MOVEit átadás - Azure AD-integrációs felhasználó létrehozásához. MOVEit átadás - Azure AD-integrációs támogatja közvetlenül az időponthoz kötött kiosztást, amelyhez engedélyezte. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az MOVEit átviteli – Ha még nem létezik az Azure AD-integrációs elérésére tett kísérlet során.

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha szeretné-e lépjen kapcsolatba a [MOVEit átadás - az Azure AD integrálása ügyfél támogatási csoport](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon használandó Azure egyszeri bejelentkezés által biztosított hozzáférés MOVEit átadás - Azure AD-integrációs engedélyezni.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése MOVEit átadás - Azure AD-integrációs, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **MOVEit átadás - Azure AD-integrációs**.

    ![A MOVEit átadás - Azure AD-integrációs hivatkozásra az alkalmazások listáját](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

A MOVEit átadás - kattintva az Azure AD-integrációs csempét a hozzáférési panelen, meg kell beolvasása automatikusan aláírt-a MOVEit átviteléig - Azure AD-integrációs alkalmazást. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

