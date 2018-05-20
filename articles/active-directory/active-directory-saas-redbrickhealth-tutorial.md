---
title: 'Oktatóanyag: Azure Active Directoryval integrált RedBrick állapotfigyelő |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és RedBrick állapotát.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 8d51d516d8716c0ac07bc851210733d352c47b6a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Oktatóanyag: Azure Active Directoryval integrált RedBrick állapota

Ebben az oktatóanyagban elsajátíthatja RedBrick állapotfigyelő integrálása az Azure Active Directory (Azure AD).

RedBrick állapotfigyelő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér RedBrick állapotfigyelő szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett RedBrick Health (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs RedBrick állapotát, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy RedBrick állapotfigyelő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből RedBrick állapotfigyelő hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-redbrick-health-from-the-gallery"></a>A gyűjteményből RedBrick állapotfigyelő hozzáadása
Az Azure AD integrálása a RedBrick állapotfigyelő konfigurálásához kell hozzáadnia RedBrick állapotát a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből RedBrick állapotfigyelő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **RedBrick állapotfigyelő**, jelölje be **RedBrick állapotfigyelő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában redBrick állapota](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés RedBrick állapota "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó RedBrick állapotát a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a RedBrick állapotfigyelő közötti kapcsolat kapcsolatot kell létrehozni.

RedBrick állapotot, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az RedBrick Health tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[RedBrick állapotfigyelő tesztfelhasználó létrehozása](#create-a-redbrick-health-test-user)**  - való egy megfelelője a Britta Simon RedBrick állapotát, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az RedBrick állapotfigyelő alkalmazásban.

**Az RedBrick Health konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **RedBrick állapotfigyelő** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. Az a **RedBrick állapotfigyelő tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezési adatokat redBrick állapotfigyelő tartomány- és URL-címek](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. Az a **azonosító** szövegmezőhöz URL-címet írja be: `http://www.redbrickhealth.com`
    
    b. Az a **válasz URL-CÍMEN** szövegmezőhöz URL-címet írja be: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Éles környezetben: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Kattintson a **megjelenítése speciális URL-beállításainak**.
    
    ![Az egyszeri bejelentkezési adatokat redBrick állapotfigyelő tartomány- és URL-címek](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. Az a **továbbítási állapotot** szövegmező, adja meg a következő minta használatával URL-címe: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Továbbító állapot értéke nincs valós. Frissítse ezt az értéket a tényleges továbbító állapottal. Ügyfél [RedBrick állapotfigyelő támogatási csoport](https://home.redbrickhealth.com/contact/) lekérni ezt az értéket.

4. A RedBrick állapotfigyelő alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Ezeket a jogcímeket adott ügyfelünk, és a követelmény függ. A rendszer például következő választható jogcímeket csak amely konfigurálhatja az alkalmazás. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-redbrickhealth-tutorial/attribute.png)

5. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:

    | Attribútum neve | Attribútum értéke |
    | ---------------| ----------------|
    | egyszerű felhasználónév | ********** |
    | ügyfél-azonosító | ********** |
    | a résztvevő azonosítója | ********** |
    
    > [!NOTE]
    > Ezek az értékek csak referencia célra áll. A szervezeti követelmények szerint attribútumok megadása szükséges. Lépjen kapcsolatba a [RedBrick állapotfigyelő támogatási csoport](https://home.redbrickhealth.com/contact/) szükséges jogcímeket kapcsolatos további adatok beolvasása.
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_400.png)

8. Az a **RedBrick Állapotkonfiguráció** területen kattintson **RedBrick állapotfigyelő konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![RedBrick Állapotkonfiguráció](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Egyszeri bejelentkezés konfigurálása **RedBrick állapotfigyelő** oldalon kell küldeniük a letöltött **Certificate(Base64)** és **SAML Entitásazonosító** való [RedBrick állapota támogatási csoport](https://home.redbrickhealth.com/contact/). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-redbrick-health-test-user"></a>RedBrick állapotfigyelő tesztfelhasználó létrehozása

Ebben a szakaszban RedBrick állapotfigyelő Britta Simon nevű felhasználó létrehozása. Együttműködve [RedBrick állapotfigyelő támogatási csoport](https://home.redbrickhealth.com/contact/) a felhasználók hozzáadása a RedBrick állapotfigyelő platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezés által biztosított hozzáférés RedBrick állapotát.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése RedBrick állapotát, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **RedBrick állapotfigyelő**.

    ![Az alkalmazások listáját a RedBrick állapotfigyelő hivatkozás](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a RedBrick állapota csempe gombra kattint, meg kell beolvasása automatikusan bejelentkezett RedBrick állapotfigyelő Alkalmazásmódosítások.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_203.png

