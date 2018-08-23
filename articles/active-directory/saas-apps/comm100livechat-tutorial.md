---
title: 'Oktatóanyag: Azure Active Directory-integráció az Comm100 élő csevegés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Comm100 élő csevegés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059456"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Oktatóanyag: Azure Active Directory-integráció az Comm100 élő csevegés

Ebben az oktatóanyagban elsajátíthatja, hogyan Comm100 élő csevegés integrálása az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása a Comm100 élő csevegés nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Comm100 élő csevegés az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Comm100 élő csevegés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Comm100 élő csevegés az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Comm100 élő csevegés egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Hozzáadás a katalógusból, Comm100 élő csevegés
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Hozzáadás a katalógusból, Comm100 élő csevegés
Az Azure AD integrálása a Comm100 élő csevegés konfigurálásához hozzá kell Comm100 élő csevegés a galériából a felügyelt SaaS-alkalmazások listájára.

**Élő csevegés a Comm100 hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Comm100 élő csevegés**válassza **Comm100 élő csevegés** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Comm100 az eredménylistában élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Comm100 élő csevegés egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Comm100 élő csevegés mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Comm100 élő csevegés hivatkozás kapcsolatának kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Comm100 élő csevegés tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Comm100 élő csevegés tesztfelhasználót](#create-a-comm100-live-chat-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Comm100 élő csevegés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Comm100 élő csevegés alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Comm100 élő csevegés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Comm100 élő csevegés** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. Az a **Comm100 élő csevegés tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Comm100 élő csevegés tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. A tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíteni a bejelentkezési URL-érték.

4. Comm100 élő csevegőalkalmazás létrehozása a SAML helyességi feltételek tartalmazza az adott attribútumok vár. Konfigurálja a következő attribútumok ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    |  Attribútum neve  | Attribútum értéke |
    | --------------- | -------------------- |    
    |   e-mailben    | user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

6. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. Az a **Comm100 élő csevegés konfigurációs** területén kattintson **Comm100 élő csevegés konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Élő csevegés konfigurációs Comm100](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. Egy másik böngészőablakban, jelentkezzen be Comm100 élő csevegés egy biztonsági-rendszergazdaként.

10. Kattintson a lap felső jobb oldalán, **My Account**.

    ![Myaccount Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. A menüt a bal oldali menüjében kattintson az **biztonsági** majd **ügynök egyszeri bejelentkezés**.

    ![Biztonsági Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Az a **ügynök egyszeri bejelentkezés** lapon, a következő lépésekkel:

    ![Biztonsági Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Az első kiemelt hivatkozásra másolja és illessze be azt a **bejelentkezési URL-** szövegmezőjébe **Comm100 élő csevegés tartomány és URL-címek** szakaszban az Azure Portalon.

    b. A a **SAML egyszeri bejelentkezési URL-cím** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-**, az Azure Portalról másolt.

    c. Az a **távoli kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    d. Kattintson a **válasszon ki egy fájlt** feltölteni a base-64 kódolású tanúsítványt, amelyet már letöltötte az Azure Portalról, a **tanúsítvány**.

    e. Kattintson a **módosítások mentése**

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Élő csevegés a Comm100 tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Comm100 élő csevegés jelentkezzen be, akkor ki kell építeni Comm100 élő csevegés be. Élő csevegés Comm100 kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Élő csevegés Comm100 egy biztonsági-rendszergazdaként jelentkezzen be.

2. Kattintson a lap felső jobb oldalán, **My Account**.

    ![Myaccount Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A menüt a bal oldali menüjében kattintson az **ügynökök** majd **új ügynök**.

    ![Az ügynök Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Az a **új ügynök** lapon, a következő lépésekkel:

    ![Új ügynök Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon@contoso.com**.

    b. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    d. Az a **megjelenítendő név** szövegmezőbe írja be például a felhasználó megjelenített neve **Britta simon**

    e. Az a **jelszó** szövegmezőbe írja be a jelszavát.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Comm100 élő csevegés Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Comm100 élő csevegés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Comm100 élő csevegés**.

    ![Az alkalmazások listáját a Comm100 élő csevegés hivatkozás](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Comm100 élő csevegés csempére kattint, meg kell lekérése automatikusan bejelentkezett az Comm100 élő csevegés alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

