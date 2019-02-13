---
title: 'Oktatóanyag: Zene rendezése az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Settling zene között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b7dee41b226cdacea1d9c7f1cf581d9f095977e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190772"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Oktatóanyag: Zene rendezése az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Settling zene integrálása az Azure Active Directory (Azure AD).

Settling zene integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a music stabilizálódási Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a music (egyszeri bejelentkezés) stabilizálódási az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Zene stabilizálódási konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Settling zene egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zene Settling hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-settling-music-from-the-gallery"></a>Zene Settling hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Settling zene, hozzá kell Settling zene a galériából a felügyelt SaaS-alkalmazások listájára.

**Zene Settling hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **zene stabilizálódási**, jelölje be **zene stabilizálódási** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Zene stabilizálódási a találatok listájában](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az zene, a "Britta Simon" nevű felhasználó teszt stabilizálódási.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Settling zene mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó zene rendeznie hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Settling zene tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Settling zene tesztfelhasználót](#create-a-settling-music-test-user)**  – egy megfelelője a Britta Simon kell rendeznie zene, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Settling music alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása stabilizálódási zene, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **zene stabilizálódási** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

1. Az a **zene tartomány és URL-címek stabilizálódási** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Stabilizálódási zene tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [zene ügyfél-támogatási csapatának stabilizálódási](https://rakurakuseisan.jp/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/settlingmusic-tutorial/tutorial_general_400.png)

1. Az a **zeneszámok konfigurációs stabilizálódási** területén kattintson **konfigurálása stabilizálódási zene** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Zene konfigurációs rendezése](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

1. Egy másik böngészőablakban, jelentkezzen be egy biztonsági rendszergazdai, zene stabilizálódási.

1. Kattintson a lap fölött **felügyeleti** fülre.

    ![Stabilizálódási zene 1. lépés](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Kattintson a **rendszerbeállítás** fülre.

    ![Stabilizálódási zene 2. lépés](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Váltson **biztonsági** fülre.

    ![Stabilizálódási zene 3. lépés](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. Az a **egyszeri bejelentkezési beállítás** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Zene step5 stabilizálódási](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Kattintson a **engedélyezéséhez**.

    b. A a **bejelentkezési URL-cím az azonosító-szolgáltató** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    c. Az a **azonosító szolgáltató kijelentkezési URL-címe** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

    d. Kattintson a **fájl kiválasztása** feltölteni a **tanúsítvány (Base64)** amelyre letöltötte űrlapon az Azure Portalon.

    e. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/settlingmusic-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/settlingmusic-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/settlingmusic-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-settling-music-test-user"></a>Zene Settling tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű Settling zene hoz létre. Együttműködve [zene ügyfél-támogatási csapatának stabilizálódási](https://rakurakuseisan.jp/) a felhasználók hozzáadása az Settling zene platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés zene stabilizálódási való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Settling zene, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **zene stabilizálódási**.

    ![Az alkalmazások listáját a Settling zene hivatkozás](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a Settling zene csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Settling music alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

