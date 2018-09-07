---
title: 'Oktatóanyag: Azure Active Directory-integráció az Slack |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Slack között.
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
ms.openlocfilehash: b742f3eb9124093bcf0c3c912bbae0367cdcce56
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053839"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Oktatóanyag: Slack-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Slack integrálható az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Slack nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Slack, az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Slack (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Slack, a következőkre van szükség:

- Azure AD-előfizetés
- A Slack egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Slack hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-slack-from-the-gallery"></a>Slack hozzáadása a katalógusból
Az Azure AD-be a Slack integrációjának konfigurálása, hozzá kell Slack a galériából a felügyelt SaaS-alkalmazások listájára.

**Slack hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Slack**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/tutorial_slack_search.png)

1. Az eredmények panelen válassza ki a **Slack**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Slack, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Slack tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a Slack, a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

A Slack, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés Slack-tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Slack tesztfelhasználó létrehozása](#creating-a-slack-test-user)**  – van egy Britta Simon megfelelője a Slack, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Slack alkalmazást az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Slack, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Slack** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Az a **Slack-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.slack.com`

    b. Az a **azonosító** szövegmezőbe, frissítse az értéket az URL-címe bejelentkezést. Ez a munkaterület tartományához. Például:`https://contoso.slack.com`

1. Slack alkalmazást a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Ha rendelkezik olyan felhasználókkal, akik hozzá van rendelve **e-mail-cím** egy Office 365-licencet, nem szerepel a **User.Email** jogcím nem fog megjelenni a SAML-jogkivonat. Ebben az esetben javasoljuk, hogy használatával **user.userprincipalname** , a **User.Email** attribútum-érték **egyedi azonosító** helyette.

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédablakban válassza **user.mail** , **felhasználói azonosító** és minden egyes sorára látható az alábbi táblázat a következő lépésekkel:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | first_name | User.givenName |
    | last_name | User.surname |
    | User.Email | user.mail |  
    | User.Username | User.userPrincipalName |

    a. Kattintson a **attribútum** megnyitásához **attribútum szerkesztése** párbeszédpanel mezőbe, majd hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    b. Az a **érték** listájához, válassza ki az adott sorhoz feltüntetett attribútum értéke.

    c. Hagyja a **Namespace** üres.

    d. Kattintson az **OK** gombra

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_general_400.png)

1. Az a **Slack konfigurációs** területén kattintson **konfigurálása Slack** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Slack vállalati hely rendszergazdaként.

1. Navigáljon a **a Microsoft Azure AD** majd lépjen a **Team-beállítások**.

     ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/slack-tutorial/tutorial_slack_001.png)

1. Az a **Team-beállítások** területén kattintson a **hitelesítési** fülre, majd **Változásbeállítások**.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/slack-tutorial/tutorial_slack_002.png)

1. Az a **SAML-hitelesítési beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/slack-tutorial/tutorial_slack_003.png)

    a.  A a **SAML 2.0-s végpontot (HTTP)** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-**, az Azure Portalról másolt.

    b.  Az a **identitásszolgáltató kibocsátója** szövegmező, illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.

    c.  Nyissa meg a letöltött tanúsítvány-fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **nyilvános tanúsítvány** szövegmezőbe.

    d. A fenti három beállítást konfigurálja a Slack csapat megfelelő. A beállításokkal kapcsolatos további információkért kérjük keresse meg a **Slack az egyszeri bejelentkezés beállítási útmutató** itt. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kattintson a **konfiguráció mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/slack-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-slack-test-user"></a>Slack tesztfelhasználó létrehozása

Ez a szakasz célja, a Slack Britta Simon nevű felhasználó létrehozásához. Slack támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a Slack, ha még nem létezik jön létre. Slack is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](slack-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolatba kell [Slack támogatási csapatának](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Slack Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200]

**Britta Simon rendel a Slack, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Slack**.

    ![Egyszeri bejelentkezés konfigurálása](./media/slack-tutorial/tutorial_slack_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Slack csempére kattintva a hozzáférési panelen azt kell lekérése automatikusan bejelentkezett a Slack alkalmazást.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](slack-provisioning-tutorial.md)


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
