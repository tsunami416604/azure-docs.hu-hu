---
title: 'Oktatóanyag: A Scout segít az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés segítségével Scout és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: 367967358d55eda40603b71893333080d7faa81a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816254"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Oktatóanyag: A Scout segít az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan segít Scout integrálása az Azure Active Directory (Azure AD).

Súgó Scout integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Scout segít az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Scout Súgó (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Scout segítségével, a következőkre van szükség:

- Azure AD-előfizetés
- A Scout súgó egyszeri bejelentkezéses engedélyezett előfizetés

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Súgó Scout hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-help-scout-from-the-gallery"></a>Súgó Scout hozzáadása a katalógusból
Konfigurálja a Scout súgó integrációja az Azure AD-be, szüksége súgó Scout hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Súgó Scout hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **súgó Scout**, jelölje be **súgó Scout** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Scout súgó az eredmények listájában](./media/helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Súgó Scout egy "Britta Simon." nevű tesztelési felhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Súgó Scout tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Súgó Scout hivatkozás kapcsolatát kell létrehozni.

Súgó Scout bejelentkezések e-mail-címeket használ, ezért a hivatkozást kapcsolat létrehozására, a azonos **e-mail-cím** , **felhasználónév** az Azure ad-ben.

Az Azure AD egyszeri bejelentkezés a Scout súgó tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Súgó Scout tesztfelhasználót](#create-a-help-scout-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása érdekében Scout.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és segítségével Scout alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés segítségével Scout, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **súgó Scout** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/helpscout-tutorial/tutorial_helpscout_samlbase.png)

1. Az a **súgó Scout tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Súgó a Scout tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/helpscout-tutorial/tutorial_helpscout_url.png)

    a. **Azonosító** van a **"Célközönség (Service Provider entitás azonosítója) URI"** a Scout súgó kezdődik `urn:`

    b. **Válasz URL-cím** van a **"Utáni visszahívási URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)"** a Scout súgó kezdődik `https://` 

    > [!NOTE] 
    > Az alábbi URL-címek értékei csak bemutatásához. Ezeket az értékeket a tényleges válasz URL-cím és azonosító frissíteni szeretné. Ezeket az értéteket kap a **egyszeri bejelentkezés** hitelesítés szakaszban, az oktatóanyag későbbi részében ismertetett lapján.

1. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a jelölőnégyzet **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést:

    ![Súgó a Scout tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/helpscout-tutorial/tutorial_helpscout_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://secure.helpscout.net/members/login/`
     
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/helpscout-tutorial/tutorial_helpscout_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/helpscout-tutorial/tutorial_general_400.png)


1. Az a **súgó Scout konfigurációs** területén kattintson **konfigurálja a Scout súgó** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban**.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/config.png) 

1. Egy másik böngészőablakban jelentkezzen be a Scout súgó vállalati hely rendszergazdaként.

1. Miután jelentkezett kattintson **"Kezelés"** a felső menüben, és válassza ki a **"Céges"** a legördülő menüből.

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings1.png) 
 
1. Válassza ki **"Hitelesítés"** elemet a bal oldali menüben. 

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings2.png) 

1. Ezzel továbblép a SAML-beállítások szakaszban, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings3.png) 
 
    a. Másolás a **utáni vissza URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** értékét, és illessze be az értéket a a **válasz URL-cím** mezőben az Azure Portalon, a Súgó Scout **tartomány és URL-címek** szakasz.
    
    b. Másolás a **célközönség-URI (Service Provider entitás azonosítója)** értékét, és illessze be az értéket a a **azonosító** mezőbe a Scout segít az Azure Portalon **tartomány és URL-címek** szakaszban.

1. Váltógomb **SAML engedélyezése** a, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/helpscout-tutorial/settings4.png) 
 
    a. A **egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
    
    b. Kattintson a **tanúsítvány feltöltése** feltölteni a **Certificate(Base64)** az Azure-portálról letöltött.

    c. Adja meg a szervezet e-mail tartomány(ok) például: - `contoso.com` a a **E-mail tartományokban** szövegmezőbe. Elkülönítheti a több tartomány vesszővel válasszon el. Bármikor súgó Scout felhasználó vagy rendszergazda, aki az adott tartomány ír a [súgó Scout bejelentkezési oldala](https://secure.helpscout.net/members/login/) identitásszolgáltató hitelesítő adataikkal hitelesítést lesznek átirányítva.

    d. Végül válthat **kényszerített SAML bejelentkezés** Ha azt szeretné, hogy a felhasználók számára csak jelentkezzen be súgó Scout keresztül ezzel a módszerrel. Ha továbbra is szeretné hagyja bejelölve az, hogy jelentkezzen be a Scout súgó hitelesítő adataikat, hagyhatja, kapcsolva. Akkor is, ha engedélyezve van, fióktulajdonos mindig lesz képes a fiókja jelszavát a Scout segítségével jelentkezzen be.

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/helpscout-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/helpscout-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/helpscout-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/helpscout-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-help-scout-test-user"></a>Súgó Scout tesztfelhasználó létrehozása

Ez a szakasz célja érdekében Scout Britta Simon nevű felhasználó létrehozásához. Súgó Scout támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Scout súgó, egy új jön létre, a hozzáférés érdekében Scout megkísérlésekor.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés érdekében Scout Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése érdekében Scout, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **súgó Scout**.

    ![A Scout Súgó hivatkozásra az alkalmazások listáját](./media/helpscout-tutorial/tutorial_helpscout_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Scout súgó csempére kattint, meg kell lekérése automatikusan bejelentkezett a Scout súgó alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/helpscout-tutorial/tutorial_general_01.png
[2]: ./media/helpscout-tutorial/tutorial_general_02.png
[3]: ./media/helpscout-tutorial/tutorial_general_03.png
[4]: ./media/helpscout-tutorial/tutorial_general_04.png

[100]: ./media/helpscout-tutorial/tutorial_general_100.png

[200]: ./media/helpscout-tutorial/tutorial_general_200.png
[201]: ./media/helpscout-tutorial/tutorial_general_201.png
[202]: ./media/helpscout-tutorial/tutorial_general_202.png
[203]: ./media/helpscout-tutorial/tutorial_general_203.png

