---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Comm100 Live Chat szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Comm100 Live Chat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41161a2e1bfd5544410bba0cb470bacbf152fd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561252"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Comm100 élő csevegéssel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Comm100 élő csevegést az Azure Active Directoryval (Azure AD). Ha integrálja a Comm100 Live Chat szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Comm100 élő csevegéshez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Comm100 Live Chat az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Comm100 Live Chat egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Comm100 Live Chat támogatja az **SP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Comm100 élő csevegés hozzáadása a galériából

A Comm100 Live Chat Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Comm100 Live Chat-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be a **Comm100 Élő csevegés** kifejezést a keresőmezőbe.
1. Válassza a **Comm100 Élő csevegés** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Comm100 élő csevegéshez

Konfigurálja és tesztelje az Azure AD SSO-t a Comm100 Live Chat segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Comm100 Live Chat-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Comm100 Élő csevegéssel hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Comm100 Live Chat SSO-t](#configure-comm100-live-chat-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Comm100 Live Chat teszt felhasználó](#create-comm100-live-chat-test-user)** - hogy egy megfelelője B.Simon a Comm100 Live Chat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Comm100 Live Chat alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valós. Frissíteni fogja a bejelentkezési URL-értéket a tényleges bejelentkezési URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk.

1. A Comm100 Live Chat alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Comm100 Live Chat alkalmazás arra számít, hogy néhány további attribútumot vissza kell adni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név |  Forrás attribútuma|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Comm100 Élő csevegés beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Comm100 Élő csevegéshez.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Comm100 Live Chat**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-comm100-live-chat-sso"></a>A Comm100 élő chat sso-jának konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Comm100 Élő csevegésbe biztonsági rendszergazdaként.

1. A lap jobb felső részén kattintson a **Saját fiók**elemre.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. A menü bal oldalán kattintson a **Biztonság,** majd **az Ügynök egyszeri bejelentkezés parancsára.**

   ![A Comm100 Élő csevegés biztonsága](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Az **Ügynök egyszeri bejelentkezése** lapon hajtsa végre az alábbi lépéseket:

   ![A Comm100 Élő csevegés biztonsága](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Másolja az első kiemelt hivatkozást, és illessze be **a Bejelentkezési URL-cím** beszövegébe az Azure Portal **Alapszintű SAML-konfiguráció szakaszában.**

   b. Az **SAML SSO URL-cím** szövegdobozába illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

   c. A **Távoli kijelentkezés URL-címszövegbe** illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.

   d. Kattintson **a Fájl kiválasztása** elemre az Azure Portalról letöltött 64-es alapú kódolt tanúsítvány nak a tanúsítványba való feltöltéséhez. **Certificate**

   e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-comm100-live-chat-test-user"></a>Comm100 Live Chat tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Comm100 élő csevegésbe, ki kell építeni őket a Comm100 Élő csevegésbe. A Comm100 Live Chat, kiépítése egy manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Comm100 élő csevegésbe biztonsági rendszergazdaként.

2. A lap jobb felső részén kattintson a **Saját fiók**elemre.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A menü bal oldalán kattintson az **Ügyintézők,** majd az **Új ügynök parancsra.**

    ![Comm100 Élő Chat ügynök](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Az **Új ügynök** lapon hajtsa végre az alábbi lépéseket:

    ![Comm100 Live Chat új ügynök](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Az **E-mail** szöveg mezőbe írja be a felhasználó e-mail címét, például **\@B.simon contoso.com**.

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B.**

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, **például Simon**.

    d. A **Megjelenítendő név** mezőbe írja be a felhasználó megjelenítendő nevét, például **B.simon**

    e. A **Jelszó** mezőbe írja be a jelszót.

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Comm100 Élő csevegés csempére kattint, automatikusan be kell jelentkeznie arra a Comm100 élő csevegésbe, amelyhez az SSO-t állította be. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Comm100 élő csevegést az Azure AD-vel](https://aad.portal.azure.com/)

