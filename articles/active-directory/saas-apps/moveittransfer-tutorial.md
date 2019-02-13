---
title: 'Oktatóanyag: MOVEit átvitele – az Azure AD-integráció az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés MOVEit átvitele – az Azure AD-integrációs és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50bf623046094509170b5b5efc091013499b51b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169836"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: MOVEit átvitele – az Azure AD-integráció az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja MOVEit átvitele – az Azure AD-integráció az Azure Active Directoryval (Azure AD).

MOVEit átvitele – az Azure AD-integráció az Azure AD integrálása nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá MOVEit átvitele – az Azure AD-integráció az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett MOVEit átvitelhez – az Azure AD-integrációs (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

MOVEit átvitele – az Azure AD-integráció, az Azure AD-integráció konfigurálása a következőkre van szükség:

- Azure AD-előfizetés
- A MOVEit átvitel – az Azure AD integrációja egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás MOVEit átvitele – az Azure AD-integráció a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Hozzáadás MOVEit átvitele – az Azure AD-integráció a katalógusból
MOVEit átvitele – az Azure AD integrálása az Azure AD-integráció konfigurálásához hozzá kell MOVEit átvitele – az Azure AD-integrációs a katalógusból a listához, kezelt SaaS-alkalmazások.

**A következő lépésekkel adja hozzá a MOVEit átviteli - katalógusból, az Azure AD-integráció:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **MOVEit átvitele – az Azure AD-integrációs**, jelölje be **MOVEit átvitele – az Azure AD-integrációs** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![MOVEit átvitele – az eredménylistában az Azure AD-integráció](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az MOVEit átvitele – az Azure AD-integráció a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben kell tudja, hogy mi a partner felhasználó MOVEit átvitele – az Azure AD-integráció a felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az MOVEit átviteli - hivatkozás kapcsolata az Azure AD-integrációs kell létrehozni.

MOVEit átvitele – az Azure AD-integráció, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés MOVEit átvitele – az Azure AD-integráció, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy MOVEit átvitele – az Azure AD-integrációs tesztfelhasználó](#create-a-moveit-transfer---azure-ad-integration-test-user)**  – van egy megfelelője a Britta Simon MOVEit átvitele – az Azure AD-integráció, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a MOVEit átvitele – az Azure AD-integrációs alkalmazást az egyszeri bejelentkezés konfigurálása.

**MOVEit átvitele – az Azure AD-integráció, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **MOVEit átvitele – az Azure AD-integrációs** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. Az a **MOVEit átvitele – az Azure AD-integrációs tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://contoso.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://contoso.com/<tenatid>`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Tekintse meg ezeket az értékeket később a **szolgáltató metaadatok URL-címe** szakaszban, vagy forduljon [MOVEit átvitele – az Azure AD-integrációs ügyfél ügyfélszolgálathoz](https://community.ipswitch.com/s/support) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Jelentkezzen be rendszergazdaként a MOVEit átviteli bérlő.

1. A bal oldali navigációs panelén kattintson **beállítások**.

    ![Beállítások szakaszban az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Kattintson a **egyszeri bejelentkezés** hivatkozás, amely alatt található **biztonsági házirendek -> felhasználói hitelesítés**.

    ![Biztonsági szabályzatok az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. A metaadatok URL-címe hivatkozásra a metaadat-dokumentum letöltése.

    ![Service Provider metaadatok URL-címe](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Győződjön meg arról **entityID** megegyezik **azonosító** a a **MOVEit átvitele – az Azure AD-integrációs tartomány és URL-címek** szakaszban.
    * Győződjön meg arról **AssertionConsumerService** hely URL-címe megegyezik **válasz URL-cím** a a **MOVEit átvitele – az Azure AD-integrációs tartomány és URL-címek** szakaszban.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Kattintson a **identitásszolgáltató hozzáadása** gombra kattintva adhat hozzá egy új összevont identitásszolgáltatót.

    ![Add Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Kattintson a **Tallózás...**  kiválasztásához a metaadatait tartalmazó fájl, amely az Azure Portalról letöltött, majd kattintson a **identitásszolgáltató hozzáadása** feltölteni a letöltött fájlt.

    ![SAML Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Jelölje be "**Igen**" as **engedélyezve** a a **összevont identitás szolgáltató beállításainak szerkesztése...**  lapot, és kattintson **mentése**.

    ![Federated Identity Provider Settings](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. Az a **összevont identitás szolgáltató felhasználói beállítások szerkesztése** lapon, hajtsa végre a következő műveleteket:
    
    ![Edit Federated Identity Provider Settings](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Válassza ki **SAML NameID** , **bejelentkezési név**.
    
    b. Válassza ki **más** , **teljes fájlvisszaállítási név** és a a **attribútumnév** szövegmezőben, hogy az érték put: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Válassza ki **más** , **E-mail** és a a **attribútumnév** szövegmezőben, hogy az érték put: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Válassza ki **Igen** , **frissítsen-fiók automatikus létrehozása**.
    
    e. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/moveittransfer-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>A MOVEit átvitel – az Azure AD-integrációs tesztfelhasználó létrehozása

Ez a szakasz célja MOVEit átvitele – az Azure AD-integrációs Britta Simon nevű felhasználó létrehozásához. MOVEit átvitele – az Azure AD-integrációs támogatja a just-in-time-kiépítés, amelyhez engedélyezte. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az MOVEit átvitel – Ha még nem létezik az Azure AD-integrációs elérésére tett kísérlet során.

>[!NOTE]
>Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [MOVEit átvitele – az Azure AD-integrációs ügyfél ügyfélszolgálathoz](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés MOVEit átvitele – az Azure AD-integrációs Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése MOVEit átvitele – az Azure AD-integráció, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **MOVEit átvitele – az Azure AD-integrációs**.

    ![MOVEit átvitele – az Azure AD-integrációs hivatkozásra az alkalmazások listáját](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Kattintva MOVEit átvitele – az Azure AD-integrációs csempéje a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a MOVEit átvitelhez – Azure AD-integrációs alkalmazást. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

