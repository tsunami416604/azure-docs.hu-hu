---
title: 'Oktatóanyag: Azure Active Directory-integráció az Trisotech digitális Enterprise Server |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Trisotech digitális Enterprise Server és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e36a4be3a95b67c040855171d4b167e495a22496
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439626"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Oktatóanyag: Azure Active Directory-integráció az Trisotech digitális vállalati kiszolgáló

Ebben az oktatóanyagban elsajátíthatja, hogyan Trisotech digitális Enterprise Server integrálható az Azure Active Directory (Azure AD).

Trisotech digitális Enterprise Server integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Trisotech digitális Enterprise Server az Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Trisotech digitális vállalati kiszolgáló (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Trisotech digitális vállalati kiszolgáló, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- Egy Trisotech digitális Enterprise Server egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Trisotech digitális vállalati kiszolgáló hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Trisotech digitális vállalati kiszolgáló hozzáadása a katalógusból
Trisotech digitális Enterprise Server integrálása az Azure AD beállításához szüksége Trisotech digitális vállalati kiszolgáló hozzáadása a katalógusból a felügyelt SaaS-alkalmazások listájában.

**Trisotech digitális vállalati kiszolgáló hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Trisotech digitális Enterprise Server**, jelölje be **Trisotech digitális Enterprise Server** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Trisotech digitális Enterprise Server a találatok listájában](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és tesztelés az Azure AD egyszeri bejelentkezés Trisotech digitális Enterprise-kiszolgálóval a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Trisotech digitális Enterprise Server mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Trisotech digitális Enterprise Server hivatkozás kapcsolatát kell létrehozni.

Konfigurálása és az Azure AD egyszeri bejelentkezés az Trisotech digitális vállalati kiszolgáló teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Trisotech digitális Enterprise Server tesztfelhasználót](#create-a-trisotech-digital-enterprise-server-test-user)**  – egy megfelelője a Britta Simon Trisotech digitális vállalati kiszolgáló, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Trisotech digitális Enterprise Server alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Trisotech digitális vállalati kiszolgáló, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Trisotech digitális Enterprise Server** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. Az a **Trisotech digitális vállalati kiszolgáló tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Trisotech digitális vállalati kiszolgáló tartomány és URL-címek egyszeri bejelentkezési adatait](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.trisotech.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Trisotech digitális vállalati kiszolgáló ügyfél-támogatási csapatának](mailto:support@trisotech.com) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe. 

    ![A tanúsítvány letöltési hivatkozás](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban jelentkezzen be a digitális vállalati kiszolgálókonfiguráció Trisotech vállalati hely rendszergazdaként.

1. Kattintson a **menüikonra** majd **felügyeleti**.

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. Válassza ki **Felhasználószolgáltató**.

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. Az a **felhasználói szolgáltató konfiguráció** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Válassza ki **védett helyességi feltétel Markup Language 2 (SAML 2)** a a legördülő listából a **hitelesítési módszer**.

    b. Az a **metaadatok URL-címe** szövegmezőjébe illessze be a **alkalmazás összevonási metaadatainak URL-címe** érték, amely másolta űrlapon az Azure Portalon.

    c. Az a **Alkalmazásazonosító** szövegmezőbe írja be az URL-cím a következő minta használatával: `https://<companyname>.trisotech.com`.

    d. Kattintson a **Mentés** gombra.

    e. Adja meg a tartománynevet a **engedélyezett tartományok (üres azt jelenti, hogy mindenki)** szövegmezőbe azt automatikusan licenceket rendel hozzá a megengedett tartomány megfelelő felhasználókat keres

    f. Kattintson a **Mentés** gombra.

 ### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Trisotech digitális Enterprise Server tesztfelhasználó létrehozása

Ez a szakasz célja Trisotech digitális Enterprise Server Britta Simon nevű felhasználó létrehozásához. Trisotech digitális Enterprise Server támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó Trisotech digitális vállalati kiszolgáló eléréséhez, ha még nem létezik tett kísérlet során jön létre.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Trisotech digitális Enterprise Server támogatási csoport](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Trisotech digitális vállalati kiszolgálóra a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Trisotech digitális vállalati kiszolgáló, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Trisotech digitális Enterprise Server**.

    ![Az alkalmazások listáját a Trisotech digitális Enterprise Server hivatkozás](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Trisotech digitális Enterprise Server csempére kattint, meg kell lekérése automatikusan bejelentkezett az Trisotech digitális Enterprise Server-alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

