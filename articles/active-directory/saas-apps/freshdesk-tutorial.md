---
title: 'Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a FreshDesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010813"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directory-integráció a freshdesk szolgáltatással

Ebben az oktatóanyagban elsajátíthatja, hogyan átmásolása a Freshdeskbe integrálása az Azure Active Directory (Azure AD).

Freshdeskből integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a freshdesk szolgáltatással az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett átmásolása a freshdeskbe (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a freshdesk szolgáltatással, a következőkre van szükség:

- Azure AD-előfizetés
- A FreshDesk egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Freshdeskből hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-freshdesk-from-the-gallery"></a>Freshdeskből hozzáadása a katalógusból

Az Azure AD integrálása a FreshDesk konfigurálásához hozzá kell átmásolása a Freshdeskbe a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Freshdeskből hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **átmásolása a Freshdeskbe**, jelölje be **átmásolása a Freshdeskbe** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a freshdesk szolgáltatással](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a freshdesk szolgáltatással a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó átmásolása a Freshdeskbe mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a FreshDesk hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés vizsgálata a freshdesk szolgáltatással konfigurálni, kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Freshdeskből tesztfelhasználó létrehozása](#creating-a-freshdesk-test-user)**  – van egy Britta Simon megfelelője a freshdesk szolgáltatással, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a freshdesk nevű alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a freshdesk szolgáltatással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **átmásolása a Freshdeskbe** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Freshdeskből tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenant-name>.freshdesk.com` vagy bármely más érték javasolt átmásolása a Freshdeskbe.

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<tenant-name>.freshdesk.com` vagy bármely más érték javasolt átmásolása a Freshdeskbe.

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [átmásolása a Freshdeskbe ügyfél-támogatási csapatának](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) beolvasni ezeket az értékeket.

5. Átmásolása a Freshdeskbe alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. Az alapértelmezett érték **felhasználóazonosító** van **user.userprincipalname** , de **átmásolása a Freshdeskbe** vár ezt a képezhető le a felhasználó e-mail-címmel. Használhatja, amely **user.mail** attribútumot a listából, vagy használja a megfelelő attribútum értéket, a szervezet konfiguráció alapján.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    a. Kattintson a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Az a **forrásattribútum** listáról válassza ki **user.mail**.

    c. Kattintson a **Save** (Mentés) gombra.

7. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Nyissa meg **parancssor** , és futtassa a következő parancsokat:

    a. Adja meg `certutil.exe -dump FreshDesk.cer` érték a parancssorban.

    > [!NOTE]
    > Itt **FreshDesk.cer** a tanúsítvány, amely az Azure Portalról letöltött.

    b. Másolás a **Cert Hash(sha256)** értékét, és illessze be a Jegyzettömbben. 

9. Az a **átmásolása a Freshdeskbe beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Freshdeskből konfiguráció](common/configuresection.png)

10. Egy másik böngészőablakban jelentkezzen be a freshdesk nevű vállalat webhelye rendszergazdaként.

11. Válassza ki a **beállítások ikon** és a **biztonsági** szakaszban, a következő lépésekkel:

    ![Egyszeri bejelentkezés](./media/freshdesk-tutorial/IC776770.png "egyszeri bejelentkezés")
  
    a. A **egyszeri bejelentkezés (SSO)** válassza **a**.

    b. Válassza ki **SAML SSO**.

    c. Az a **SAML bejelentkezési URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    d. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezési URL-címe** érték, amely az Azure Portalról másolta.

    e. Az a **biztonsági tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be **Cert Hash(sha256)** értéket, amelyet korábban szerzett be.
  
    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

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

    a. Az a **E-mail** szövegmezőbe írja be az Azure AD e-mail címét, az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a kíván üzembe helyezni az Azure AD-fiók nevét.

    c. Az a **cím** szövegmezőbe írja be az Azure AD-szolgáltatásfiók kíván üzembe helyezni.

    d. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >Az Azure ad-ben fióktulajdonos előtt aktiválva van, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó e-mailt fog kapni.
    >
    >[!NOTE]
    >Bármely más átmásolása a Freshdeskbe felhasználói fiók létrehozása eszközöket használhatja, vagy az AAD felhasználói fiókok kiépítését a átmásolása a freshdeskbe átmásolása a Freshdeskbe által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával átmásolása a freshdeskbe Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **átmásolása a Freshdeskbe**.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a FreshDesk csempére kattint, akkor kell lekérése automatikusan bejelentkezett a freshdesk nevű alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
