---
title: 'Oktatóanyag: Azure Active Directory-integráció Mimecast személyes portállal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és személyes portál Mimecast között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 71ecffebe095fd325837aeb1d6e741a2f3321aea
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421838"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Oktatóanyag: Azure Active Directory-integráció Mimecast személyes portállal

Ebben az oktatóanyagban elsajátíthatja, hogyan Mimecast személyes portál integrálható az Azure Active Directory (Azure AD).

Személyes portál Mimecast integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Mimecast személyes Portal Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Mimecast személyes portálra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Mimecast személyes portállal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy személyes portál Mimecast egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Mimecast személyes portál hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Mimecast személyes portál hozzáadása a katalógusból
Az Azure AD-be Mimecast személyes portál integráció konfigurálásához, hozzá kell Mimecast személyes portál a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Mimecast személyes portál hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Mimecast személyes portál**, jelölje be **Mimecast személyes portál** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Mimecast személyes portál](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Mimecast személyes Portal "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Mimecast személyes portálon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Mimecast személyes portálon hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Mimecast személyes portállal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Mimecast személyes portál tesztfelhasználót](#create-a-mimecast-personal-portal-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Mimecast személyes portálon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd Mimecast személyes portál alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Mimecast személyes portállal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Mimecast személyes portál** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

1. Az a **Mimecast személyes portál tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Mimecast személyes portál tartomány és URL-címek egyszeri bejelentkezési adatait](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:

    | Régió  |  Érték | 
    | --------------- | --------------- |
    | Európa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Egyesült Államok   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Dél-Afrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Ausztrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: 

    | Régió  |  Érték | 
    | --------------- | --------------- | 
    | Európa          | `https://eu-api.mimecast.com/login/saml`|
    | Egyesült Államok   | `https://us-api.mimecast.com/login/saml`|
    | Dél-Afrika    | `https://za-api.mimecast.com/login/saml`|
    | Ausztrália       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Az azonosító értéke nem valódi. Frissítse az értéket a tényleges azonosítója. Kapcsolattartó [Mimecast személyes portál ügyfél-támogatási csapatának](http://www.mimecast.com/customer-success/technical-support/) a gépkulcsengedélyek értékének. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

1. Az a **Mimecast személyes portál konfigurációs** területén kattintson **Mimecast személyes portál konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Mimecast személyes Portálkonfiguráció](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Mimecast személyes portálra rendszergazdaként.

1. Lépjen a **szolgáltatások \> alkalmazások**.
   
    ![Alkalmazások](./media/mimecast-personal-portal-tutorial/ic794998.png "alkalmazások")

1. Kattintson a **hitelesítési profilok**.
   
    ![Hitelesítési profilok](./media/mimecast-personal-portal-tutorial/ic794999.png "hitelesítési profilok")

1. Kattintson a **új hitelesítési profilt**.
   
    ![Új hitelesítés profil](./media/mimecast-personal-portal-tutorial/ic795000.png "új hitelesítési profil")

1. Az a **hitelesítési profilt** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Hitelesítési profilt](./media/mimecast-personal-portal-tutorial/ic795001.png "hitelesítési profilt")
   
    a. Az a **leírás** szövegmezőbe írja be a konfiguráció nevét.
   
    b. Válassza ki **Mimecast személyes portál SAML-hitelesítés**.
   
    c. Mint **szolgáltató**válassza **Azure Active Directory**.
   
    d. A **kiállítójának URL-címe** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító**, Azure Portalról másolt.
   
    e. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
   
    f. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    g. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **Identitástanúsítványt szolgáltató (Metadata)** szövegmezőbe.

    h. Válassza ki **egyszeri bejelentkezés engedélyezése**.
   
    i. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Személyes portál Mimecast tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók személyes portál Mimecast szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Mimecast személyes portált. Mimecast személyes portálon esetén kiépítése a manuális feladat.

Szeretne regisztrálni egy tartományban, felhasználók létrehozása előtt.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mimecast személyes portál** rendszergazdaként.

1. Lépjen a **könyvtárak \> belső**.
   
    ![Könyvtárak](./media/mimecast-personal-portal-tutorial/ic795003.png "könyvtárak")

1. Kattintson a **új tartomány regisztrálása**.
   
    ![Új tartomány regisztrálása](./media/mimecast-personal-portal-tutorial/ic795004.png "új tartomány regisztrálása")

1. Az új tartomány létrehozása után kattintson **új cím**.
   
    ![Új cím](./media/mimecast-personal-portal-tutorial/ic795005.png "új cím")

1. Az új cím párbeszédpanelen hajtsa végre az alábbi lépéseket egy érvényes Azure AD-fiókot kíván üzembe helyezni:
   
    ![Mentés](./media/mimecast-personal-portal-tutorial/ic795006.png "mentése")
   
    a. Az a **E-mail cím** szövegmezőbe írja be **E-mail cím** , a felhasználó **BrittaSimon@contoso.com**.
    
    b. Az a **globális név** szövegmezőbe írja be a **felhasználónév** , **BrittaSimon**.

    c. Az a **jelszó**, és **jelszó megerősítése** szövegmezőből, írja be a **jelszó** felhasználó.
   
    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Mimecast személyes portál felhasználói fiók létrehozása eszközöket és Mimecast személyes portál által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Mimecast személyes portálra a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Mimecast személyes portál, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Mimecast személyes portál**.

    ![Az alkalmazások listáját a személyes portál Mimecast hivatkozásra](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a személyes portál Mimecast csempére kattint, meg kell lekérése automatikusan bejelentkezett a Mimecast személyes portál alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

