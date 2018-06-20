---
title: 'Oktatóanyag: Azure Active Directoryval integrált Slackhez |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Slackhez között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 5d16e633f29ca635a9b32118e699cb59d3896353
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221342"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Oktatóanyag: Azure Active Directoryval integrált Slackhez

Ebben az oktatóanyagban elsajátíthatja Slackhez integrálása az Azure Active Directory (Azure AD).

Slackhez integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Slackhez hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett slackhez (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Slackhez, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Slack egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Slackhez hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-slack-from-the-gallery"></a>A gyűjteményből Slackhez hozzáadása
A Slackhez az Azure AD-integráció konfigurálása, szüksége Slackhez hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**Adja hozzá a Slackhez a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Slackhez**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/tutorial_slack_search.png)

5. Az eredmények panelen válassza ki a **Slackhez**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Slackhez.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Slackhez tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Slackhez közötti kapcsolat kapcsolatot kell létrehozni.

A Slackhez, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Slackhez tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Slack tesztfelhasználó létrehozása](#creating-a-slack-test-user)**  - való Britta Simon egy megfelelője a Slackhez, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az közzététele a Slack-alkalmazásban.

**Konfigurálja az Azure AD egyszeri bejelentkezést a Slackhez, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Slackhez** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_samlbase.png)

3. Az a **Slackhez tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.slack.com`

    b. Az a **azonosító** szövegmező, írja be az URL-cím: `https://slack.com`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési az URL-címmel rendelkezik. Ügyfél [Slack támogatási csoport](https://slack.com/help/contact) az értéket be kell olvasni.
     
4. Alkalmazás közzététele a Slack vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Amennyiben a felhasználók, akik hozzá van rendelve **e-mail cím** nincs az Office365 licencet, a **User.Email** jogcím nem fog megjelenni a SAML-jogkivonat. Ezekben az esetekben, javasoljuk, hogy használatával **user.userprincipalname** , a **User.Email** megfeleltetett érték attribútum **egyedi azonosítója** helyette.

5. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédablakban válassza **user.mail** , **felhasználói azonosító** és az alábbi táblázatban szereplő minden egyes sorhoz kapcsolódóan végezze el a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | first_name | User.givenName |
    | last_name | User.surname |
    | User.Email | user.mail |  
    | User.Username | User.userPrincipalName |

    a. Kattintson a **attribútum** megnyitásához **attribútum szerkesztése** párbeszédpanel mezőbe, majd hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    b. Az a **érték** listára, válassza ki a sorhoz látható attribútum értéke.

    c. Hagyja a **Namespace** üres.

    d. Kattintson az **OK** gombra

6. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_certificate.png)

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_general_400.png)

8. A a **Slackhez konfigurációs** kattintson **konfigurálása Slackhez** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_configure.png)

9. Egy másik webes böngészőablakban jelentkezzen be a Slack vállalati webhely rendszergazdaként.

10. Navigáljon a **Microsoft Azure AD** majd navigáljon **Team beállításainak**.

     ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_001.png)

11. Az a **Team beállításainak** területen kattintson a **hitelesítési** fülre, majd **beállításainak módosítása**.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_002.png)

12. Az a **SAML-alapú hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Az a **SAML 2.0 végpontot (HTTP)** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    b.  A a **Identity Provider kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta.

    c.  Nyissa meg a letöltött fájlt a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be azt a **nyilvános tanúsítvány** szövegmező.

    d. Állítsa be a fenti három megfelelő beállításokat a Slack-csoport. A beállításokkal kapcsolatos további információkért kérjük található a **Slackhez tartozó SSO konfigurációs útmutató** itt. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kattintson a **konfigurációjának mentéséhez**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-slack-test-user"></a>Slack tesztfelhasználó létrehozása

Ez a szakasz célja a Slackhez Britta Simon nevű felhasználót létrehozni. Slackhez támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Ha még nem létezik a Slackhez elérésére tett kísérlet során. A tartalékidő is támogatja az automatikus a felhasználók átadása, további részletei [Itt](slack-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

> [!NOTE]
> Hozza létre a felhasználó manuálisan kell, ha kapcsolatba kell [Slack támogatási csoport](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon hozzáférés biztosítása a slackhez által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200]

**Britta Simon hozzárendelése a Slackhez, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Slackhez**.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A Slack csempe elemre a hozzáférési panelen, meg kell beolvasása automatikusan bejelentkezett a Slack alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
