---
title: 'Oktatóanyag: Azure Active Directory-integráció az ív közzétételi – egyszeri bejelentkezés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Arc - SSO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9decab5c35cda03e8532c48654203be7d95a5801
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858843"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Oktatóanyag: Azure Active Directory-integráció az ív közzétételi – egyszeri bejelentkezés

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja ív közzétételi – egyszeri bejelentkezés az Azure Active Directoryval (Azure AD).

Ív közzététel – az Azure AD SSO integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá ív közzétételi – egyszeri bejelentkezés az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, Arc közzétételi – egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Ív közzétételi - SSO, Azure AD-integráció konfigurálása a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az Arc-közzététel – egyszeri bejelentkezés az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Ív közzététel – egyszeri bejelentkezés a katalógusból hozzáadása
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Ív közzététel – egyszeri bejelentkezés a katalógusból hozzáadása
Ív közzétételi – egyszeri bejelentkezés az Azure ad-ben, az integráció konfigurálásához hozzá kell ív közzétételi - SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Ív közzétételi - katalógusból, egyszeri bejelentkezés hozzáadása hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **ív közzétételi – egyszeri bejelentkezés**, jelölje be **ív közzétételi – egyszeri bejelentkezés** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Közzététel – egyszeri bejelentkezés az eredménylistában ívvé](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az ív közzétételi tesztelése és konfigurálása, – egyszeri bejelentkezés egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés használatát Azure ad-ben tudnia kell, a partner felhasználó az ív Publishing – egyszeri Bejelentkezést, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a közzétételi Arc - hivatkozás kapcsolata SSO kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az ív közzétételi - SSO, szüksége a következő építőelemeit végrehajtásához:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy ív közzétételi – egyszeri bejelentkezés tesztelése felhasználói](#create-an-arc-publishing---sso-test-user)**  – egy megfelelője a Britta Simon ív közzétételi –, amely kapcsolódik a felhasználói reprezentációja az Azure AD egyszeri bejelentkezés van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a ív közzétételi - alkalmazás egyszeri bejelentkezési az egyszeri bejelentkezés konfigurálása.

**Ív közzétételi - SSO, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ív közzétételi – egyszeri bejelentkezés** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. Az a **ív közzétételi. – egyszeri bejelentkezés tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Ív közzététel – egyszeri bejelentkezés tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/arc-tutorial/tutorial_arc_url.png)

    1. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Ív közzététel – egyszeri bejelentkezés tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/arc-tutorial/tutorial_arc_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [ív közzétételi - SSO ügyfél-támogatási csapatának](mailto:inf@washpost.com) beolvasni ezeket az értékeket. 

1. Közzétételi arc - alkalmazás egyszeri bejelentkezés a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_arc_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | Keresztnév | User.givenName |
    | Vezetéknév | User.surname |
    | e-mailben | user.mail |
    | csoportok | User.assignedroles |

    1. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

     ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    1. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    1. Hagyja a **Namespace** üres.
    
    1. Kattintson a **Ok**

    > [!NOTE]
    > Itt a **csoportok** attribútum van leképezve a **user.assignedroles**. Ezek a leképezéséhez a csoportnevek vissza az alkalmazás Azure AD-ben létrehozott egyéni szerepkörök. További útmutatás található [Itt](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) egyéni szerepkörök létrehozásával az Azure ad-ben. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/arc-tutorial/tutorial_general_400.png)
    
1. Az a **ív közzétételi - egyszeri bejelentkezési konfiguráció** területén kattintson **konfigurálása ív közzétételi – egyszeri bejelentkezés** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Ív közzététel – egyszeri bejelentkezés konfigurálása](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **ív közzétételi – egyszeri bejelentkezés** oldalon kell küldenie a letöltött **tanúsítvány (Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** való [ívvé Közzététel – egyszeri bejelentkezés támogatási csapatának](mailto:inf@washpost.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/arc-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/arc-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/arc-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/arc-tutorial/create_aaduser_04.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.

    1. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    1. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    1. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Hozzon létre egy ív közzétételi - SSO tesztfelhasználó számára

Ez a szakasz célja Britta Simon ív közzétételi - SSO nevű felhasználó létrehozásához. Közzétételi arc - egyszeri Bejelentkezést támogatja a létesítést just-in-time, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az ív közzétételi – egyszeri bejelentkezés, ha még nem létezik elérésére tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [ív közzétételi - SSO támogatási csapatának](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ív közzétételi - SSO Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése ív közzétételi - SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ív közzétételi - SSO**.

    ![A Arc közzétételi – az alkalmazások listáját az egyszeri bejelentkezés hivatkozás](./media/arc-tutorial/tutorial_arc_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintva a ív közzétételi - SSO csempe a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a ív közzétételi - alkalmazás egyszeri Bejelentkezéssel való.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

