---
title: 'Oktatóanyag: Azure Active Directory-integráció az nagyítás |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Nagyítás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 57ae31245a356a4cd5769fe71ef471922bf6faf9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440134"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Oktatóanyag: Azure Active Directory-integráció az nagyítás

Ebben az oktatóanyagban elsajátíthatja, hogyan nagyítás integrálása az Azure Active Directory (Azure AD).

Nagyítás integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Nagyítás Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett nagyítás (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs beállítja a nagyítás, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Nagyítás egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Nagyítás hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból
Nagyítás az Azure AD-be, az integráció konfigurálásához hozzá kell Nagyítás a galériából a felügyelt SaaS-alkalmazások listájára.

**Nagyítás hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **nagyítás**válassza **nagyítás** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában nagyítás](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés nagyítás, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó nagyítás mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó nagyítás hivatkozás kapcsolata kell létrehozni.

Nagyítás, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Nagyítás tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Nagyítás tesztfelhasználó létrehozása](#create-a-zoom-test-user)**  – egy megfelelője a Britta Simon nagyítása, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és nagyítás alkalmazását az egyszeri bejelentkezés konfigurálása.

**A Nagyítás konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **nagyítás** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

1. Az a **nagyítás tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Nagyítás tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.zoom.us`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `<companyname>.zoom.us`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [nagyítás ügyfél-támogatási csapatának](https://support.zoom.us/hc) beolvasni ezeket az értékeket.

1. A Nagyítás alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. 

    ![Egyszeri bejelentkezés konfigurálása](./media/zoom-tutorial/tutorial_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | Namespace érték |
    | ------------------- | -----------|--------- |    
    | E-mail-cím | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Utónév | User.givenName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Vezetéknév | User.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Telefonszám | User.telephoneNumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Részleg | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Az a **Namespace** szövegmezőbe írja be a névtér értéke a sorhoz látható.
    
    e. Kattintson az **OK** gombra. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/zoom-tutorial/tutorial_zoom_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/zoom-tutorial/tutorial_general_400.png)

1. Az a **nagyítás konfigurációs** területén kattintson **nagyítás konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Nagyítás konfigurálása](./media/zoom-tutorial/tutorial_zoom_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Nagyítás vállalati hely rendszergazdaként.

1. Kattintson a **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés lap](./media/zoom-tutorial/IC784700.png "egyszeri bejelentkezés")

1. Kattintson a **biztonsági ellenőrzést** lapra, és folytassa a a **egyszeri bejelentkezés** beállításait.

1. Az egyszeri bejelentkezés szakaszban hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés szakasz](./media/zoom-tutorial/IC784701.png "egyszeri bejelentkezés")
   
    a. A a **bejelentkezési oldal URL** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
   
    b. A a **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím** Azure Portalról másolt.
     
    c. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató identitástanúsítványt** szövegmezőbe.

    d. Az a **kibocsátó** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt. 

    e. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > További információért látogasson el a Nagyítás dokumentációja [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/zoom-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/zoom-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/zoom-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/zoom-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-zoom-test-user"></a>Nagyítás tesztfelhasználó létrehozása

Ahhoz, hogy a nagyításhoz. Jelentkezzen be az Azure AD-felhasználók, akkor ki kell építeni Nagyítás be. Nagyítás, esetén kiépítése a manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **nagyítás** rendszergazdaként a vállalati webhely.
 
1. Kattintson a **fiókkezelés** fülre, majd **felhasználókezelés**.

1. Jelölje be a felhasználói felügyeleti csoport **felhasználók hozzáadása**.
   
    ![Felhasználókezelés](./media/zoom-tutorial/IC784703.png "felhasználók kezelése")

1. Az a **felhasználók hozzáadása** lapon, a következő lépésekkel:
   
    ![Felhasználók hozzáadása](./media/zoom-tutorial/IC784704.png "felhasználók hozzáadása")
   
    a. Mint **felhasználótípus**válassza **alapszintű**.

    b. Az a **e-mailek** beviteli mező, írja be az e-mail-címe érvényes Azure AD-fiók kíván üzembe helyezni.

    c. Kattintson a **Hozzáadás** parancsra.

> [!NOTE]
> Bármely más nagyítás felhasználói fiók létrehozása eszközöket használhatja, vagy nagyítás üzembe helyezni az Azure Active Directory által biztosított API-k felhasználói fiókok.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés nagyítás Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel nagyítás, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **nagyítás**.

    ![A Nagyítás hivatkozásra az alkalmazások listáját](./media/zoom-tutorial/tutorial_zoom_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a nagyítása csempe a hozzáférési panelen, meg kell beolvasása automatikusan bejelentkezett a Nagyítás alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

