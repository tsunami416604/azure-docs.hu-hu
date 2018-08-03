---
title: 'Oktatóanyag: Azure Active Directory-integráció az SkyDesk E-mail |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés SkyDesk e-mailek és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 058aad72ea8e5741bc632b3c27c032613683ae78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444082"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Oktatóanyag: Azure Active Directory-integráció SkyDesk e-mail-címmel

Ebben az oktatóanyagban elsajátíthatja, hogyan SkyDesk E-mail integrálása az Azure Active Directory (Azure AD).

SkyDesk E-mail integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá SkyDesk e-mailt az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SkyDesk e-mailek (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SkyDesk e-mail-címmel, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy e-mailek SkyDesk egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SkyDesk E-mail hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-skydesk-email-from-the-gallery"></a>SkyDesk E-mail hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a SkyDesk e-mailt, hozzá kell SkyDesk e-mailt a galériából a felügyelt SaaS-alkalmazások listájára.

**SkyDesk e-mail hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SkyDesk E-mail**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

1. Az eredmények panelen válassza ki a **SkyDesk E-mail**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és SkyDesk e-mailt az Azure AD egyszeri bejelentkezés tesztelése egy "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó SkyDesk e-mailben mi egy felhasználó számára az Azure ad-ben. Más szóval közötti Azure AD-felhasználót és a kapcsolódó felhasználó SkyDesk e-mailben szereplő hivatkozásra kapcsolatot kell hozható létre.

SkyDesk e-mailben, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Tesztelje az Azure AD egyszeri bejelentkezés SkyDesk e-mail-címmel konfigurálni, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[SkyDesk E-mail tesztfelhasználó létrehozása](#creating-a-skydesk-email-test-user)**  – egy megfelelője a Britta Simon SkyDesk e-mailt, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a SkyDesk levelezőprogramban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása SkyDesk e-mail-címével, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SkyDesk E-mail** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

1. Az a **SkyDesk E-mail tartománya és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [SkyDesk E-mail ügyfél-támogatási csapatának](https://www.skydesk.sg/support/) a gépkulcsengedélyek értékének. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_general_400.png)

1. Az a **SkyDesk E-mail-konfigurációjának** területén kattintson **konfigurálása SkyDesk E-mail** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

1. Az egyszeri bejelentkezés engedélyezése **SkyDesk E-mail**, hajtsa végre az alábbi lépéseket:

    a. Bejelentkezés SkyDesk E-mail fiókja rendszergazdaként.

    b. A felső menüben kattintson **telepítő**, és válassza ki **szervezeti**. 
    
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. Kattintson a **tartományok** a bal oldali panelen.
    
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Kattintson a **tartomány hozzáadása**.
    
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Adja meg a tartomány nevét, és ellenőrizze a tartományt.
    
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Kattintson a **SAML-hitelesítés** a bal oldali panelen.
    
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Az a **SAML-hitelesítés** párbeszédpanel lapon, a következő lépésekkel:
   
      ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >SAML-alapú hitelesítés használatára, vagy rendelkeznie kell **ellenőrzött tartomány** vagy **portál URL-cím** beállítása. Beállíthatja, hogy a portál URL-CÍMÉT az egyedi névvel.
    > 
    > 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
   
    b. Az a **kijelentkezési** URL-címe szövegmezőbe, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    c. **Jelszó URL-Címének módosítása** nem kötelező, ezért hagyja üresen a mezőt.

    d. Kattintson a **kulcs beolvasása fájlból** válassza ki a letöltött tanúsítvány az Azure Portalról, és kattintson a **nyílt** feltölteni a tanúsítványt.

    e. Mint **algoritmus**válassza **RSA**.

    f. Kattintson a **Ok** menti a módosításokat.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-skydesk-email-test-user"></a>SkyDesk E-mail tesztfelhasználó létrehozása

Ebben a szakaszban egy e-mailben SkyDesk Britta Simon nevű felhasználó létrehozásához.

1. Kattintson a **felhasználói hozzáférés** bal oldali panelen SkyDesk e-mailben, és írja be a felhasználónevét. 

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Ha a felhasználók tömeges létrehozásához szüksége, lépjen kapcsolatba kell a [SkyDesk E-mail ügyfél-támogatási csapatának](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon SkyDesk levelezéshez való hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel SkyDesk e-mailt, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SkyDesk E-mail**.

    ![Egyszeri bejelentkezés konfigurálása](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a SkyDesk E-mail csempére kattint, akkor kell lekérése automatikusan bejelentkezett az SkyDesk E-mail alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

