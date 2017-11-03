---
title: "Oktatóanyag: Azure Active Directoryval integrált által Merces HR2day |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és HR2day által Merces között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 77e2fcf9c306259286b15767f3a992510d6d79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Oktatóanyag: Azure Active Directoryval integrált HR2day Merces által

Ebben az oktatóanyagban elsajátíthatja által Merces HR2day integrálása az Azure Active Directory (Azure AD).

HR2day által Merces integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér HR2day Merces által szabályozható.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett HR2day Merces által a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs HR2day Merces által, a következőkre van szükség:

- Az Azure AD-előfizetés.
- Egy HR2day által Merces egyszeri bejelentkezés előfizetés engedélyezve van.

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Első egy [ingyenes egy hónapos az Azure AD](https://azure.microsoft.com/pricing/free-trial/) Ha már nincs.  

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az itt ismertetett forgatókönyvben két fő építőelemeket áll:

1. HR2day által Merces hozzáadása a gyűjteményből.
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Adja hozzá a HR2day Merces által a gyűjteményből
Az Azure AD integrálása a HR2day által Merces konfigurálásához vegye fel HR2day Merces által a gyűjteményből a felügyelt SaaS-alkalmazásokhoz.

**A gyűjteményből által Merces HR2day hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali navigációs panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **által Merces HR2day**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. Az eredmények panelen válassza ki a **által Merces HR2day**, majd válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a HR2day "Britta Simon." nevű tesztfelhasználó alapján Merces által

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó által Merces HR2day egy felhasználó számára az Azure AD. Más szóval kell HR2day Merces által az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat létrehozásához.

HR2day Merces által, rendelje hozzá a **felhasználónév** az Azure AD- **felhasználónév** a kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a HR2day által Merces tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on): lehetővé teszi a felhasználók a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#creating-an-azure-ad-test-user): tesztelése az Azure AD egyszeri bejelentkezést a Britta Simon.
3. [Hozzon létre egy HR2day Merces teszt felhasználó](#creating-an-hr2day-by-merces-test-user): hozzon létre egy megfelelője a Britta Simon HR2day által Merces, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assigning-the-azure-ad-test-user): Britta Simon engedélyezése az Azure AD egyszeri bejelentkezéshez használandó.
5. [Egyszeri bejelentkezés tesztelése](#testing-single-sign-on): Győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez a HR2day Merces alkalmazás.

**Az Azure AD az egyszeri bejelentkezés konfigurálása HR2day Merces által, a következő lépéseket:**

1. Az Azure portálon a a **által Merces HR2day** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az egyszeri bejelentkezést, engedélyezése a **egyszeri bejelentkezés** párbeszédpanelen jelölje ki **mód** , **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. Az a **HR2day Merces tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írja be egy URL-cím használatával a következő mintát: `https://<tenantname>.force.com/<instancename>`.

    b. Az a **azonosító** mezőbe írja be egy URL-cím használatával a következő mintát: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Lépjen kapcsolatba a [HR2day Merces ügyfél-támogatási csapat](mailto:servicedesk@merces.nl) beolvasni ezeket az értékeket. 
 


4. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. Ez a szakasz ismerteti, hogyan szeretné a felhasználók hitelesítéséhez által Merces HR2day fiókkal az Azure AD. Ennek segítségével tehetik összevonási, amely az SAML protokoll alapul.

    A HR2day Merces alkalmazás által meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat a SAML helyességi feltételek vár. Az alábbi képernyőfelvételen látható példa erre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    A SAML-előfeltétel konfigurálása előtt vegye fel a kapcsolatot a [Merces ügyfél-támogatási csapat HR2day](mailto:servicedesk@merces.nl) és az egyedi azonosító attribútum értékének kérhetnek a bérlő. Ez az érték a következő szakaszban a lépések elvégzéséhez szüksége. 

6. Az a **egyszeri bejelentkezés** párbeszédpanel a **felhasználói attribútumok** területen konfigurálja az SAML-jogkivonat attribútum a következő ábrán látható módon. Ezután az alábbi lépéseket.
    
      | Attribútum neve    |   Attribútum-érték |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | csatlakoztatás ([mail] "102938475Z", "@" |
    
      a. Lehetőségre a **attribútum hozzáadása** párbeszédablakban válassza **Hozzáadás attribútum**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** mezőbe írja be **ATTR_LOGINCLAIM**.

    c. Az a **érték** listáról válassza ki **Join()**.

    d. Az a **karakterlánc1** listáról válassza ki **user.mail**.

    e. A **karakterlánc2**, írja be a HR2day csapata által biztosított egyedi azonosítója.

    f. Az a **elválasztó** mezőbe írja be  **@** .
    
    g. Válassza ki **Ok**.

7. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. Az a **Merces konfigurációja HR2day** szakaszban jelölje be **konfigurálása HR2day által Merces** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló** szakasz.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. Egyszeri bejelentkezés konfigurálása az alkalmazáshoz, lépjen kapcsolatba a [Merces ügyfél-támogatási csapat HR2day](mailTo:servicedesk@merces.nl). Csatlakoztassa a letöltött **Certificate(Base64)** fájlt az e-maileket. Is megadhatja a **Sign-Out URL-cím**, **SAML Entitásazonosító**, és **SAML-alapú egyszeri bejelentkezési URL-címe** , hogy az egyszeri bejelentkezés integráció konfigurálható.

    > [!NOTE]
    >A Merces csapathoz említse meg, hogy ez az integráció kell-e a mintával kell beállítani az entitás azonosítója **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. Majd hozzáférni a beágyazott dokumentáció keresztül a **konfigurációs** szakasz alján. További embedded dokumentációjából szolgáltatásáról a [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs panelen válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó**, és jegyezze fel a jelszót.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Hozzon létre egy HR2day Merces teszt felhasználó

Ez a szakasz célja Britta Simon a HR2day Merces által meghívott felhasználó létrehozásához. A felhasználók a HR2day fiók hozzáadásához dolgozni a [Merces ügyfél-támogatási csapat HR2day](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Ha a felhasználó manuális létrehozása, forduljon a [Merces ügyfél-támogatási csapat HR2day](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon HR2day Merces által biztosított saját hozzáférés szerint használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése HR2day Merces által, a következő lépéseket:**

1. Az Azure portálon, az alkalmazások nézet megnyitásához, nyissa meg a könyvtár nézetet, és folytassa a **vállalati alkalmazások**. Válassza ki, **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **által Merces HR2day**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

6. Kattintson a **válasszon** gombra.

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési Panel tesztelése az Azure AD egyszeri bejelentkezés beállításai.  

Ha a HR2day jelöl ki a hozzáférési panelen Merces csempe, automatikusan beolvasása jelentkezett be a HR2day Merces alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png

