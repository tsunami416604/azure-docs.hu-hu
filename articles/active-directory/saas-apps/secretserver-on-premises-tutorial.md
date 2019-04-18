---
title: 'Oktatóanyag: Titkos kulcs kiszolgálóval (helyszíni) az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a titkos kulcs kiszolgáló (helyszíni) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885842"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Oktatóanyag: Titkos kulcs kiszolgálóval (helyszíni) az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a titkos kulcs kiszolgáló (helyszíni) integrálása az Azure Active Directory (Azure AD).

Titkos kulcs kiszolgáló (helyszíni) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a titkos kulcs kiszolgáló (helyszíni) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a titkos kulcs kiszolgáló (helyszíni) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a titkos kulcs kiszolgálóval (helyszíni), a következőkre van szükség:

- Azure AD-előfizetés
- A titkos kulcs kiszolgáló (helyszíni) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Titkos kulcs kiszolgáló (helyszíni) hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Titkos kulcs kiszolgáló (helyszíni) hozzáadása a katalógusból
Konfigurálja a titkos kulcs kiszolgáló (helyszíni) integrációja az Azure AD-be, szüksége titkos kiszolgáló (helyszíni) hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Titkos kulcs kiszolgáló (helyszíni) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **titkos kiszolgáló (helyszíni)**, jelölje be **titkos kiszolgáló (helyszíni)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában titkos kiszolgáló (helyszíni)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés titkos kiszolgálóval (helyszíni), a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a titkos kulcs kiszolgáló (helyszíni) tartozó felhasználó Mi az a felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a titkos kulcs Server (helyszíni) hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés titkos kiszolgálóval (helyszíni) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy titkos kulcsot kiszolgáló (helyszíni) tesztfelhasználót](#create-a-secret-server-on-premises-test-user)**  - a-megfelelője a Britta Simon rendelkezik a titkos kulcs Server (helyszíni), amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a titkos kulcs kiszolgáló (helyszíni) alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a titkos kulcs kiszolgálóval (helyszíni), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **titkos kiszolgáló (helyszíni)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Az a **titkos kiszolgáló (helyszíni) tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Titkos kód kiszolgáló (helyszíni) tartomány és URL-címek egyszeri bejelentkezési adatait](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a kiválasztott érték, mint például a felhasználó: `https://secretserveronpremises.azure`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > A fenti Entitásazonosító csak egy példa, és választhatja az összes egyedi érték, amely azonosítja a titkos kulcs kiszolgáló példányát az Azure ad-ben. Kell küldenie az entitás azonosítója a [titkos kiszolgáló (helyszíni) ügyfél-támogatási csapatának](https://thycotic.force.com/support/s/) és konfigurálása saját oldalán. További részletekért olvassa el [Ez a cikk](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Titkos kód kiszolgáló (helyszíni) tartomány és URL-címek egyszeri bejelentkezési adatait](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [titkos kiszolgáló (helyszíni) ügyfél-támogatási csapatának](https://thycotic.force.com/support/s/) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Ellenőrizze **speciális tanúsítvány-aláírási beállítások megjelenítése** válassza **aláírási beállítás** , **bejelentkezési SAML-válasz és -előfeltétel**.

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/signing.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. Az a **titkos kiszolgáló (helyszíni) konfigurációs** területén kattintson **Server konfigurálása a titkos kulcs (helyszíni)** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Titkos kód kiszolgáló (helyszíni) konfigurációja](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Az egyszeri bejelentkezés konfigurálása **titkos kiszolgáló (helyszíni)** oldalon kell küldenie a letöltött **Certificate(Base64), kijelentkezéses URL-CÍMÉT, SAML egyszeri bejelentkezési szolgáltatás URL-cím**, és **SAML-entitás ID** való [titkos kiszolgáló (helyszíni) támogatási csoport](https://thycotic.force.com/support/s/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Titkos kulcs kiszolgáló (helyszíni) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű titkos kiszolgáló (helyszíni) felhasználói hoz létre. Együttműködve [titkos kiszolgáló (helyszíni) támogatási csoport](https://thycotic.force.com/support/s/) a felhasználók hozzáadása a titkos kulcs kiszolgáló (helyszíni) platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés hozzáférést biztosít a titkos kulcs kiszolgáló (helyszíni) használja.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a titkos kulcs kiszolgáló (helyszíni), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **titkos kiszolgáló (helyszíni)**.

    ![A titkos kulcs kiszolgáló (helyszíni) hivatkozásra az alkalmazások listáját](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a titkos kulcs kiszolgáló (helyszíni) csempére kattint, akkor kell lekérése automatikusan bejelentkezett a titkos kulcs kiszolgáló (helyszíni) alkalmazással.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

