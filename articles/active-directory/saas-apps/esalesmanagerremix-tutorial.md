---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező E Sales Manager Remix |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az E Sales Manager Remix között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 2e2bc0c7e590796a533d9f593eb61e57def91fc8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187998"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Az Azure Active Directory integrálása az értékesítési vezető Remix E

Ebben az oktatóanyagban elsajátíthatja az Azure Active Directory (Azure AD) integrálása E Sales Manager Remix.

Integrálja az Azure AD-E Sales Manager Remix, kérje le a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az E Sales Manager Remix Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy az első bejelentkezett automatikusan E Sales Manager Remix (egyszeri bejelentkezés, vagy az SSO) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az E Sales Manager Remix, a következőkre van szükség:

- Azure AD-előfizetés
- Az E Sales Manager Remix SSO-kompatibilis előfizetéssel

> [!NOTE]
> Ha ebben az oktatóanyagban a lépéseket, azt javasoljuk, hogy *nem* éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. 

Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

* E Sales Manager Remix hozzáadása a katalógusból
* Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix hozzáadása a katalógusból
A rendszerrel történő integráció konfigurálása az Azure AD E Sales Manager Remix, hozzá E Sales Manager Remix a galériából a kezelt SaaS-alkalmazások listáját az alábbiak szerint:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

1. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A "Nagyvállalati alkalmazások" ablak][2]
    
1. Új alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **E Sales Manager Remix**válassza **E Sales Manager Remix** a találatok listájában, és válassza ki a **Hozzáadás**.

    ![E Sales Manager Remix a találatok listájában](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az E Sales Manager Remix, egy "Britta Simon." nevű tesztelési felhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell állapítania az E Sales Manager Remix felhasználó és a megfelelő Azure AD-ben. Más szóval egy Azure AD-felhasználót és az E Sales Manager Remix ugyanaz a felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezés az E Sales Manager Remix tesztelése és konfigurálása, hajtsa végre a következő öt szakaszokban építőelemeket:

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezés konfigurálása az E Sales Manager Remix alkalmazásban a következő módon:

1. Az Azure Portalon az a **E Sales Manager Remix** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Az "Egyszeri bejelentkezés" hivatkozásra][4]

1. Az a **egyszeri bejelentkezési** ablakban, a a **egyszeri bejelentkezési mód** jelölje ki **SAML-alapú bejelentkezés**.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. A **E Sales Manager Remix tartomány és URL-címek**, tegye a következőket:

    ![E Sales Manager Remix tartomány és URL-címek egyszeri bejelentkezési adatait](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-címet a következő formátumban: *https://\<Server-alapú-URL > /\<altartomány > / esales-pc*.

    b. Az a **azonosító** mezőbe írja be egy URL-címet a következő formátumban: *https://\<Server-alapú-URL > /\<altartomány > /*.

    c. Megjegyzés: a **azonosító** értéket az oktatóanyag későbbi használatra.
    
    > [!NOTE] 
    > Az előző értékek nem valódi. Frissítse azokat a tényleges bejelentkezési URL-CÍMÉT és azonosító. Az értékek beszerzéséhez forduljon [E Sales Manager Remix ügyfélalkalmazás támogatási csapatának](mailto:esupport@softbrain.co.jp).

1. Alatt **SAML-aláíró tanúsítvány**válassza **tanúsítvány (Base64)**, majd mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány (Base64) letöltési hivatkozás](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Válassza ki a **megtekintése és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzetet, majd válassza ki a **emailaddress** attribútum.
    
    ![A felhasználói attribútumok ablak](./media/esalesmanagerremix-tutorial/configure1.png)

    A **attribútum szerkesztése** ablak nyílik meg.

1. Másolás a **Namespace** és **neve** értékeket. Az érték létrehozása a mintában  *\<Namespace > /\<neve >*, és ez az oktatóanyag későbbi használatra mentse azt.

    ![Az attribútum szerkesztése ablakban](./media/esalesmanagerremix-tutorial/configure2.png)

1. A **E Sales Manager Remix konfigurációs**válassza **konfigurálása E Sales Manager Remix**.

    ![E Sales Manager Remix konfigurálása](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    A **bejelentkezés konfigurálása** ablak nyílik meg.

1. Az a **rövid összefoglaló** területén másolja a kijelentkezési URL-cím és az SAML egyszeri bejelentkezési szolgáltatás URL-cím.

1. Kattintson a **Mentés** gombra.

    ![A Mentés gombra](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Jelentkezzen be rendszergazdaként E Sales Manager Remix alkalmazását.

1. Jobb felső sarokban, válassza ki a **Rendszergazda menü**.

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

1. A bal oldali panelen válassza ki a **rendszerbeállítások** > **együttműködés a külső rendszer**.

    ![A "rendszer" és "Együttműködés a külső rendszer" Hivatkozásaim](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. Az a **együttműködés a külső rendszer** ablakban válassza **SAML**.

    ![A "Külső rendszer együttműködve" ablak](./media/esalesmanagerremix-tutorial/configure6.png)

1. A **SAML-hitelesítés beállítást**, tegye a következőket:

    ![Az "SAML-hitelesítés beállítást" szakasz](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Válassza ki a **PC-s verzió** jelölőnégyzetet.
    
    b. Az a **együttműködés elem** szakaszban, a legördülő listában válassza ki **e-mail**.

    c. Az a **együttműködés elem** mezőbe illessze be a korábban az Azure Portalról másolt jogcímérték (azt jelenti, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. Az a **kibocsátó (entitás azonosítója)** mezőbe illessze be az értéket, amely a korábban kimásolt a **E Sales Manager Remix tartomány és URL-címek** szakaszában az Azure Portalon.

    e. Az Azure-portálról letöltött tanúsítvány feltöltéséhez kattintson **fájl kiválasztása**.

    f. Az a **azonosító szolgáltató bejelentkezési URL-cím** mezőbe illessze be a SAML egyszeri bejelentkezési szolgáltatás URL-cím, amelyet korábban az Azure Portalon vágólapra másolt.

    g. A **Identity Provider kijelentkezési URL-címe** mezőbe illessze be a kijelentkezési URL-érték, amelyet korábban az Azure Portalon vágólapra másolt.

    h. Válassza ki **beállítás kész**.

> [!TIP]
> Az alkalmazás beállítása, ahogy az előző utasítások a tömör verziója olvashat a [az Azure portal](https://portal.azure.com). Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és hozzáférhet a dokumentáció az Embedded a **konfigurációs** alul található szakaszában. A beágyazott dokumentáció szolgáltatással kapcsolatos további információkért lásd: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban tesztfelhasználó Britta Simon az Azure Portalon létrehozhat az alábbiak szerint:

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Az aktuális felhasználó megjelenítéséhez válassza **felhasználók és csoportok** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Felső részén a **minden felhasználó** ablakban válassza **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A **felhasználói** ablak nyílik meg.

1. Az a **felhasználói** ablakban tegye a következőket:

    ![A felhasználó ablak](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Egy teszt E Sales Manager Remix felhasználó létrehozása

1. Jelentkezzen be rendszergazdaként E Sales Manager Remix alkalmazását.

1. Válassza ki **Rendszergazda menü** jobb felső sarokban a menüből.

    ![E Sales Manager Remix konfigurálása](./media/esalesmanagerremix-tutorial/configure4.png)

1. Válassza ki **a vállalati beállítások** > **részlegek és alkalmazottak karbantartási**, majd válassza ki **regisztrált alkalmazottak**.

    ![Az "Alkalmazottak registered" lap](./media/esalesmanagerremix-tutorial/user1.png)

1. Az a **új alkalmazott regisztrálása** területén tegye a következőket:
    
    ![Az "új alkalmazott regisztráció" szakasz](./media/esalesmanagerremix-tutorial/user2.png)

    a. Az a **alkalmazott neve** mezőbe írja be annak a felhasználónak a nevét (például **Britta**).

    b. Végezze el a többi szükséges mezőt.
    
    c. Ha engedélyezi a SAML, a rendszergazda nem tud bejelentkezni a bejelentkezési oldalról. Engedélyezés rendszergazdai bejelentkezési jogokkal kiválasztásával a felhasználó a **rendszergazdai bejelentkezési név** jelölőnégyzetet.

    d. Válassza ki **regisztrációs**.

1. A későbbiekben, jelentkezzen be rendszergazdaként, jelentkezzen be rendszergazdai engedélyekkel rendelkezik, és ezt követően a jobb felső sarokban, válassza ki a felhasználó **Rendszergazda menü**.

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon által biztosított hozzáférés E Sales Manager Remix Azure egyszeri bejelentkezés használatára. Ehhez tegye a következőket: 

![A felhasználói szerepkör hozzárendelése][200] 

1. Az Azure Portalon nyissa meg a **alkalmazások** nézet, nyissa meg a **Directory** megtekintheti, és válassza ki **vállalati alkalmazások** > **összes alkalmazások**.

    ![A "Nagyvállalati alkalmazások" és "Összes alkalmazás" hivatkozások][201] 

1. Az a **alkalmazások** listáról válassza ki **E Sales Manager Remix**.

    ![Az E Sales Manager Remix hivatkozás](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

1. Az a **felhasználók és csoportok** ablakban, a a **felhasználók** listáról válassza ki **Britta Simon**.

1. Válassza ki a **kiválasztása** gombra.

1. Az a **hozzárendelés hozzáadása** ablakban válassza **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Amikor kiválasztja a E Sales Manager Remix csempe a hozzáférési panelen, be kell jelentkeznie automatikusan az E Sales Manager Remix alkalmazáshoz.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

