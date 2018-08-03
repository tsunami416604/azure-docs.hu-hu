---
title: 'Oktatóanyag: Azure Active Directory-integráció a Citrix ShareFile |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Citrix ShareFile között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 9919be128ae651b589a37f957cc59ce6d171143f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431732"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Oktatóanyag: Azure Active Directory-integráció a Citrix ShareFile

Ebben az oktatóanyagban elsajátíthatja a Citrix ShareFile integrálása az Azure Active Directory (Azure AD).

A Citrix ShareFile integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Citrix ShareFile Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Citrix ShareFile (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Citrix ShareFile, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Citrix ShareFile egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Citrix ShareFile hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-citrix-sharefile-from-the-gallery"></a>A Citrix ShareFile hozzáadása a katalógusból
Az Azure AD-be a Citrix ShareFile integrációjának konfigurálása, hozzá kell a Citrix ShareFile a galériából a felügyelt SaaS-alkalmazások listájára.

**A Citrix ShareFile hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **a Citrix ShareFile**, jelölje be **a Citrix ShareFile** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A Citrix ShareFile a találatok listájában](./media/sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a Citrix ShareFile az Azure AD egyszeri bejelentkezés tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Citrix ShareFile tartozó felhasználó Mi az a felhasználó Azure AD-ben. Más szóval Azure AD-felhasználót és a Citrix ShareFile a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

A Citrix ShareFile, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Citrix ShareFile tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Citrix ShareFile tesztfelhasználó létrehozása](#create-a-citrix-sharefile-test-user)**  – egy megfelelője a Britta Simon van a Citrix ShareFile, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Citrix ShareFile alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Citrix ShareFile, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **a Citrix ShareFile** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/sharefile-tutorial/tutorial_sharefile_samlbase.png)

1. Az a **a Citrix ShareFile tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A Citrix ShareFile tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenant-name>.sharefile.com/saml/login`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-címe:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [a Citrix ShareFile ügyfél-támogatási csapatának](https://www.citrix.co.in/products/sharefile/support.html) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/sharefile-tutorial/tutorial_sharefile_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/sharefile-tutorial/tutorial_general_400.png)

1. Az a **a Citrix ShareFile konfigurációs** területén kattintson **konfigurálása a Citrix ShareFile** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![A Citrix ShareFile konfiguráció](./media/sharefile-tutorial/tutorial_sharefile_configure.png)

1. Egy másik böngészőablakban, jelentkezzen be a **a Citrix ShareFile** rendszergazdaként a vállalati webhely.

1. A felső eszköztáron kattintson **rendszergazdai**.

1. A bal oldali navigációs panelen válassza ki **konfigurálása egyszeri bejelentkezéshez**.
   
    ![Felügyeleti fiók](./media/sharefile-tutorial/ic773627.png "felügyeleti fiók")

1. Az a **egyszeri bejelentkezés / SAML 2.0 konfigurációs** párbeszédpanel lapján **alapbeállítások**, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés](./media/sharefile-tutorial/ic773628.png "egyszeri bejelentkezés")
   
    a. Kattintson a **SAML engedélyezése**.
    
    b. A **az Identitásszolgáltató kibocsátója / Entitásazonosító** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.

    c. Kattintson a **módosítása** mellett a **X.509-tanúsítvány** mezőben, és ezután töltse fel a tanúsítványt, az Azure Portalról letöltött.
    
    d. A **bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
    
    e. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

1. Kattintson a **mentése** a Citrix ShareFile felügyeleti portálon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sharefile-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sharefile-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/sharefile-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sharefile-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-citrix-sharefile-test-user"></a>A Citrix ShareFile tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Citrix ShareFile, akkor ki kell építeni a Citrix ShareFile be. A Citrix ShareFile, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **a Citrix ShareFile** bérlő.

1. Kattintson a **felhasználók kezelése \> otthoni felhasználók kezelése \> + alkalmazott létrehozása**.
   
   ![Alkalmazott létrehozása](./media/sharefile-tutorial/IC781050.png "alkalmazott létrehozása")

1. Az a **alapinformációk** szakaszban, hajtsa végre az alábbi lépéseket:
   
   ![Alapinformációk](./media/sharefile-tutorial/IC799951.png "alapvető információk")
   
   a. Az a **E-mail cím** szövegmezőbe írja be az e-mail-címe, Britta Simon **brittasimon@contoso.com**.
   
   b. Az a **Utónév** szövegmezőbe írja be **Utónév** , felhasználó **Britta**.
   
   c. Az a **Vezetéknév** szövegmezőbe írja be **Vezetéknév** , felhasználó **Simon**.

1. Kattintson a **felhasználó hozzáadása**.
  
   >[!NOTE]
   >Az Azure ad-ben fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik. A Citrix ShareFile felhasználói fiók létrehozása eszközöket és a Citrix ShareFile által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a Citrix ShareFile Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel a Citrix ShareFile, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **a Citrix ShareFile**.

    ![A Citrix ShareFile hivatkozásra az alkalmazások listáját](./media/sharefile-tutorial/tutorial_sharefile_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Citrix ShareFile csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Citrix ShareFile alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharefile-tutorial/tutorial_general_01.png
[2]: ./media/sharefile-tutorial/tutorial_general_02.png
[3]: ./media/sharefile-tutorial/tutorial_general_03.png
[4]: ./media/sharefile-tutorial/tutorial_general_04.png

[100]: ./media/sharefile-tutorial/tutorial_general_100.png

[200]: ./media/sharefile-tutorial/tutorial_general_200.png
[201]: ./media/sharefile-tutorial/tutorial_general_201.png
[202]: ./media/sharefile-tutorial/tutorial_general_202.png
[203]: ./media/sharefile-tutorial/tutorial_general_203.png
