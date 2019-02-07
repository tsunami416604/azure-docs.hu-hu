---
title: 'Oktatóanyag: HR2day Merces által az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés HR2day Merces által és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: 7d77d72264580e4cde83f6cbb9f83bf7030d941e
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820045"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Oktatóanyag: HR2day Merces által az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan által Merces HR2day integrálása az Azure Active Directory (Azure AD).

Által Merces HR2day integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá HR2day Merces által az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett HR2day Merces által az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

HR2day Merces által az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetés.
- Egy HR2day által Merces egyszeri bejelentkezés előfizetés engedélyezve van.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Get- [egy hónapos ingyenes próbaverzió az Azure AD](https://azure.microsoft.com/pricing/free-trial/) Ha már nincs.  

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az itt ismertetett forgatókönyvben két fő építőelemeket áll:

1. Által Merces HR2day hozzáadása a katalógusból.
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Által Merces HR2day hozzáadása a katalógusból
Által Merces HR2day integrálása az Azure AD beállításához adja hozzá a katalógus HR2day Merces által a felügyelt SaaS-alkalmazások listájában.

**Által Merces HR2day hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **által Merces HR2day**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. Az eredmények panelen válassza ki a **HR2day Merces által**, majd válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az HR2day Merces a teszt "Britta Simon." nevű felhasználó által

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, akik HR2day Merces által a partner felhasználót, hogy egy felhasználó Azure AD-ben. Más szóval kell HR2day Merces által az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolatot létesít.

HR2day által Merces, rendelje hozzá a **felhasználónév** az Azure AD- **felhasználónév** a kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezés az HR2day Merces által tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. Az Azure AD egyszeri bejelentkezés konfigurálása: Engedélyezze a felhasználók a funkció használatához.
1. Hozzon létre egy Azure ad-ben tesztfelhasználó: Tesztelje az Azure AD egyszeri bejelentkezés az Britta Simon.
1. Hozzon létre egy HR2day Merces tesztfelhasználó szerint: Hozzon létre egy megfelelője a Britta Simon HR2day által Merces, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. Az Azure ad-ben tesztfelhasználó hozzárendelése: Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezése.
1. Egyszeri bejelentkezés vizsgálata: Győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Merces alkalmazás által a HR2day konfigurálása egyszeri bejelentkezéshez.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés HR2day Merces által, az alábbi lépéseket:**

1. Az Azure Portalon az a **HR2day Merces által** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanelen jelölje ki **mód** , **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. Az a **Merces tartomány és URL-címek HR2day** területén az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-cím használatával a következő mintának: `https://<tenantname>.force.com/<instancename>`.

    b. Az a **azonosító** mezőbe írja be egy URL-cím használatával a következő mintának: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [Merces ügyfél-támogatási csapata által HR2day](mailto:servicedesk@merces.nl) beolvasni ezeket az értékeket. 
 


1. Az a **SAML-aláíró tanúsítvány** szakaszban jelölje be **Certificate(Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. Ez a szakasz ismerteti, hogyan engedélyezhet felhasználókat a hitelesítésre által Merces HR2day fiókkal az Azure ad-ben. Ezek a SAML-protokoll alapján összevonási segítségével hajthatja végre.

    A HR2day Merces alkalmazás által a SAML helyességi feltételek vár egy megadott formátumban, amelyhez egyéni attribútum-leképezéshez az SAML-jogkivonat hozzáadása. Az alábbi képernyőképen Ez egy példát mutat be. 

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    Az SAML-előfeltétel konfigurálása előtt, kapcsolatba kell lépnie a [HR2day Merces ügyfél-támogatási csapata által](mailto:servicedesk@merces.nl) és az egyedi azonosító attribútum értékét a bérlő számára. Ez az érték a következő szakaszban szereplő lépések végrehajtásához szüksége lesz. 

1. Az a **egyszeri bejelentkezési** párbeszédpanel a **felhasználói attribútumok** területen konfigurálja az SAML-jogkivonat attribútum, az alábbi képen látható módon. Ezután az alábbi lépéseket.
    
      | Attribútum neve    |   Hodnota atributu |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Megnyitásához a **attribútum hozzáadása** párbeszédablakban válassza **attribútum hozzáadása**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** mezőbe írja be **ATTR_LOGINCLAIM**.

    c. Az a **érték** listáról válassza ki **Join()**.

    d. Az a **szöveg1** listáról válassza ki **user.mail**.

    e. A **karakterlánc2**, írja be a HR2day csapata által biztosított egyedi azonosítója.

    f. Az a **elválasztó** mezőbe írja be **\@**.
    
    g. Kattintson az **OK** gombra.

1. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_general_400.png)

1. Az a **Merces konfigurációja HR2day** szakaszban jelölje be **konfigurálása HR2day Merces által** megnyitásához a **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** a a **rövid összefoglaló** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Egyszeri bejelentkezés konfigurálása az alkalmazáshoz, lépjen kapcsolatba a [Merces ügyfél-támogatási csapata által HR2day](mailTo:servicedesk@merces.nl). Csatlakoztassa a letöltött **Certificate(Base64)** fájlt az e-maileket. Is biztosítanak a **kijelentkezéses URL-cím**, **SAML Entitásazonosító**, és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** , hogy az egyszeri bejelentkezés integráció konfigurálható.

    > [!NOTE]
    >A Merces csapatának megemlíteni, hogy ez az integráció kell állítani a mintával az entitás azonosítója **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** fülre. A beágyazott dokumentáció majd hozzáférni a **konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásáról a [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése**, majd írja le a jelszót.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Hozzon létre egy HR2day Merces tesztfelhasználó szerint

Ez a szakasz célja Britta Simon HR2day Merces szerint nevű felhasználó létrehozásához. A felhasználók hozzáadása a HR2day fiókban, dolgozni a [Merces ügyfél-támogatási csapata által HR2day](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Merces ügyfél-támogatási csapata által HR2day](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával HR2day Merces által az Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése HR2day Merces által, az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, nyissa meg a könyvtár nézet, és folytassa a **vállalati alkalmazások**. Majd **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **által Merces HR2day**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][203]

1. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

1. Kattintson a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ez a szakasz célja az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.  

A HR2day által a hozzáférési panelen Merces csempe kiválasztásakor automatikusan első jelentkezett be a HR2day Merces alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

