---
title: 'Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a FreshDesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: db4750e01b62835cf08fd52e3288e94aea539b26
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161322"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással

Ebben az oktatóanyagban elsajátíthatja, hogyan átmásolása a Freshdeskbe integrálása az Azure Active Directory (Azure AD).

Freshdeskből integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a freshdesk szolgáltatással az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett átmásolása a freshdeskbe (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a freshdesk szolgáltatással, a következőkre van szükség:

- Azure AD-előfizetés
- A FreshDesk egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti az egy hónapos próbaidőszak [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Freshdeskből hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-freshdesk-from-the-gallery"></a>Freshdeskből hozzáadása a katalógusból

Az Azure AD integrálása a FreshDesk konfigurálásához hozzá kell átmásolása a Freshdeskbe a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Freshdeskből hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]

3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **átmásolása a Freshdeskbe**. Válassza ki **átmásolása a Freshdeskbe** az eredmények panelen, és válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a freshdesk szolgáltatással a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó átmásolása a Freshdeskbe mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a FreshDesk hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a freshdesk szolgáltatással.

Az Azure AD egyszeri bejelentkezés vizsgálata a freshdesk szolgáltatással konfigurálni, kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Freshdeskből tesztfelhasználó létrehozása](#creating-a-freshdesk-test-user)**  – van egy Britta Simon megfelelője a freshdesk szolgáltatással, amely kapcsolódik az Azure ad-ben ábrázolása számára.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a freshdesk nevű alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a freshdesk szolgáltatással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **átmásolása a Freshdeskbe** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Az a **átmásolása a Freshdeskbe tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenant-name>.freshdesk.com` vagy bármely más érték javasolt átmásolása a Freshdeskbe.

    > [!NOTE]
    > Vegye figyelembe, hogy ez nem a tényleges érték. Frissítse az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [átmásolása a Freshdeskbe ügyfél-támogatási csapatának](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) lekérni ezt az értéket.  

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Ha problémája van, tekintse meg ezt [hivatkozás](https://support.freshdesk.com/support/discussions/topics/317543).

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_general_400.png)

6. Telepítés **OpenSSL** a rendszerben, ha nem telepítette a rendszerben.

7. Nyissa meg **parancssor** , és futtassa a következő parancsokat:

    a. Adja meg `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` érték a parancssorban.

    > [!NOTE]
    > Itt **FreshDesk.cer** a tanúsítvány, amely az Azure Portalról letöltött.

    b. Adja meg `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` érték a parancssorban. Itt **certificate.crt** a kimeneti tanúsítvány, ami akkor jön létre, az előző lépésben.

    c. Másolás a **ujjlenyomat** értékét, és illessze be a Jegyzettömbben. Ujjlenyomat kettőspontokat eltávolítása, és szerezze be az utolsó ujjlenyomat értékét.

8. Az a **átmásolása a Freshdeskbe konfigurációs** területén kattintson **konfigurálása átmásolása a Freshdeskbe** konfigurálása bejelentkezési ablak megnyitásához. Másolja a SAML egyszeri bejelentkezési szolgáltatás URL-cím és kijelentkezéses URL-CÍMÉT a **rövid összefoglaló** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

9. Egy másik böngészőablakban jelentkezzen be a freshdesk nevű vállalat webhelye rendszergazdaként.

10. A felső menüben kattintson **rendszergazdai**.

    ![Rendszergazdai](./media/freshdesk-tutorial/IC776768.png "rendszergazda")

11. Az a **általános beállítások** lapra, majd **biztonsági**.
  
    ![Biztonsági](./media/freshdesk-tutorial/IC776769.png "biztonsági")

12. Az a **biztonsági** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/IC776770.png "egyszeri bejelentkezés")
  
    a. A **egyszeri bejelentkezés (SSO)** válassza **a**.

    b. Válassza ki **SAML SSO**.

    c. Az a **SAML bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta.

    d. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezéses URL-cím** érték, amely az Azure Portalról másolta.

    e. Az a **biztonsági tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be **ujjlenyomat** értéket, amelyet korábban a eltávolítása a kettőspont után szerzett be.
  
    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-freshdesk-test-user"></a>Freshdeskből tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók FreshDesk-ba való bejelentkezéshez, akkor ki kell építeni történő átmásolása a Freshdeskbe.  
Esetén a freshdesk szolgáltatással kiépítés a manuális feladat.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **átmásolása a Freshdeskbe** bérlő.

2. A felső menüben kattintson **rendszergazdai**.

   ![Rendszergazdai](./media/freshdesk-tutorial/IC776772.png "rendszergazda")

3. Az a **általános beállítások** lapra, majd **ügynökök**.
  
   ![Ügynökök](./media/freshdesk-tutorial/IC776773.png "ügynökök")

4. Kattintson a **új ügynök**.

    ![Új ügynök](./media/freshdesk-tutorial/IC776774.png "új ügynök")

5. Az ügynök adatait párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![Az ügynök adatait](./media/freshdesk-tutorial/IC776775.png "ügynök adatait")

   a. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a kíván üzembe helyezni az Azure AD-fiók nevét.

   b. Az a **E-mail** szövegmezőbe írja be az Azure AD e-mail címét, az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

   c. Az a **cím** szövegmezőbe írja be az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

   d. Válassza ki **ügynökök szerepkör**, és kattintson a **hozzárendelése**.

   e. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >Az Azure ad-ben fióktulajdonos előtt aktiválva van, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó e-mailt fog kapni.
    >
    >[!NOTE]
    >Bármely más átmásolása a Freshdeskbe felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését átmásolása a Freshdeskbe által biztosított API-k.
    átmásolása a freshdeskbe.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon számára a hozzáférés biztosításával a Boxba Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200]

**Britta Simon átmásolása a freshdeskbe rendelni, végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **átmásolása a Freshdeskbe**.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a FreshDesk csempére kattint, első bejelentkezett a FreshDesk-alkalmazásba való bejelentkezési oldal szerezheti be.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png