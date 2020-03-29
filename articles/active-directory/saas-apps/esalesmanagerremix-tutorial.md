---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az E Sales Manager Remixprogrammal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az E Sales Manager Remix között.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65406953"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Az Azure Active Directory integrálása az E Sales Manager Remix-el

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Azure Active Directoryt (Azure AD) az E Sales Manager Remix-el.

Az Azure AD és az E Sales Manager Remix integrálásával a következő előnyöket élvezheti:

- Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az E Sales Manager Remixhez.
- Engedélyezheti a felhasználóknak, hogy automatikusan bejelentkezhessenek az E Sales Manager Remix (egyszeri bejelentkezés vagy egyszeri bejelentkezés) alkalmazásba az Azure AD-fiókjukkal.
- A fiókokat egy központi helyen, az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az E Sales Manager Remix-el a következő elemekre van szükség:

- Azure AD-előfizetés
- E Sales Manager Remix SSO-kompatibilis előfizetés

> [!NOTE]
> Ha teszteli az oktatóanyag lépéseit, azt javasoljuk, hogy *ne* használjon éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Egy Azure AD próbakörnyezettel, [egy hónapos próbaverziót kaphat.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben. 

Az oktatóanyagban ismertetett forgatókönyv két fő építőelemből áll:

* E Sales Manager Remix hozzáadása a galériából
* Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>E Sales Manager Remix hozzáadása a galériából
Az Azure AD és az E Sales Manager Remix integrációjának konfigurálásához adja hozzá az E Sales Manager Remixet a gyűjteményből a felügyelt SaaS-alkalmazások listájához az alábbi módon:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget. 

    ![Az Azure Active Directory gombja][1]

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A "Vállalati alkalmazások" ablak][2]
    
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején.

    ![Az Új alkalmazás gomb][3]

1. A keresőmezőbe írja be az **E Sales Manager Remix**kifejezést, válassza az **E Sales Manager Remix** elemet az eredménylistában, majd kattintson a **Hozzáadás gombra.**

    ![E Sales Manager Remix az eredménylistában](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az E Sales Manager Remix-szel, egy "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD-nek azonosítania kell az E Sales Manager Remix felhasználóját és az Azure AD megfelelőjét. Más szóval létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és az e Sales Manager Remix ugyanazon felhasználója között.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az E Sales Manager Remix segítségével végezze el az építőelemeket a következő öt szakaszban:

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Engedélyezze az Azure AD egyszeri bejelentkezését az Azure Portalon, és konfigurálja az egyszeri bejelentkezést az E Sales Manager Remix alkalmazásában az alábbi módon:

1. Az Azure Portalon az **E Sales Manager Remix** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Az "Egyszeri bejelentkezés" hivatkozás][4]

1. Az **Egyszeri bejelentkezés** ablak egyszeri **bejelentkezési mód** mezőjében válassza az **SAML-alapú bejelentkezés**lehetőséget.
 
    ![Az "Egyszeri bejelentkezés" ablak](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Az **E Sales Manager Remix Domain és URL-címek csoportban**tegye a következőket:

    ![E Sales Manager Remix Domain és URL egyszeri bejelentkezési információk](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő formátumban: *\<https:// Kiszolgálóalapú URL->/\<altartomány>/esales-pc*.

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő formátumban: *https://\<Kiszolgálóalapú URL->/\<altartomány>/*.

    c. Figyelje meg az **azonosító** értékét későbbi használatra ebben az oktatóanyagban.
    
    > [!NOTE] 
    > Az előző értékek nem valósak. Frissítse őket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek beszerzéséhez forduljon az [E Sales Manager Remix Ügyfél támogatási csapatához.](mailto:esupport@softbrain.co.jp)

1. Az **SAML aláíró tanúsítvány csoportban**válassza a **Tanúsítvány (Base64)** lehetőséget, majd mentse a tanúsítványfájlt a számítógépre.

    ![A tanúsítvány (Base64) letöltési hivatkozása](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Jelölje be az **Összes többi felhasználói attribútum megtekintése és szerkesztése jelölőnégyzetet,** majd jelölje be az **e-mailaddress** attribútumot.
    
    ![A Felhasználói attribútumok ablak](./media/esalesmanagerremix-tutorial/configure1.png)

    Megnyílik **az Attribútum szerkesztése** ablak.

1. Másolja a **Névtér** és a **Név** értékeket. Hozza létre az értéket a * \<névtér\< *mintában>/ Név>, és mentse későbbi használatra ebben az oktatóanyagban.

    ![Az Attribútum szerkesztése ablak](./media/esalesmanagerremix-tutorial/configure2.png)

1. Az **E Sales Manager Remix konfigurációja csoportban**válassza az E Sales Manager **Remix konfigurálása**lehetőséget.

    ![E Sales Manager Remix konfigurációja](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Megnyílik **a Bejelentkezés konfigurálása** ablak.

1. A **Rövid útmutató** szakaszban másolja a kijelentkezési URL-címet és az SAML egyszeri bejelentkezési szolgáltatás URL-címét.

1. Kattintson a **Mentés** gombra.

    ![A Mentés gomb](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Jelentkezzen be az E Sales Manager Remix alkalmazásba rendszergazdaként.

1. A jobb felső sarokban válassza a **Rendszergazda menü lehetőséget.**

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

1. A bal oldali ablaktáblában válassza a **Rendszerbeállítások** > **Együttműködés külső rendszerrel**lehetőséget.

    ![A "Rendszerbeállítások" és az "Együttműködés külső rendszerrel" linkek](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. A **Külső rendszerrel együttműködés** ablakban válassza az **SAML lehetőséget.**

    ![Az "Együttműködés külső rendszerrel" ablak](./media/esalesmanagerremix-tutorial/configure6.png)

1. Az **SAML-hitelesítési beállítás csoportban**tegye a következőket:

    ![Az "SAML hitelesítési beállítás" szakasz](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Jelölje be a **SZÁMÍTÓGÉP-verzió** jelölőnégyzetet.
    
    b. Az **Együttműködés elem** csoport legördülő listájában válassza a **levelezés**lehetőséget.

    c. Az **Együttműködés elem** mezőbe illessze be az Azure Portalról korábban másolt jogcímértéket **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**(azaz).

    d. A **Kiállító (entitásazonosító)** mezőbe illessze be a korábban másolt azonosító értéket az Azure Portal **E Sales Manager Remix tartomány és URL-címei** szakaszából.

    e. Ha fel szeretné tölteni a letöltött tanúsítványt az Azure Portalról, válassza a **Fájlkiválasztása lehetőséget.**

    f. Az **azonosítószolgáltató bejelentkezési URL-címét** illessze be az SAML egyszeri bejelentkezési szolgáltatás URL-címét, amelyet korábban másolt az Azure Portalon.

    g. Az **Identitásszolgáltató kijelentkezési URL-címe** mezőbe illessze be a korábban az Azure Portalon másolt kijelentkezési URL-címet.

    h. Válassza a **Beállítás kész lehetőséget.**

> [!TIP]
> Az alkalmazás beállítása kor elolvashatja az előző utasítások tömör verzióját az [Azure Portalon.](https://portal.azure.com) Miután hozzáadta az alkalmazást az **Active Directory** > **vállalati alkalmazások** szakaszban, válassza az **Egyszeri bejelentkezés** lapot, majd lépjen be a beágyazott dokumentációba a **Konfiguráció** szakasz alján. A beágyazott dokumentációs funkcióról az [Azure AD beágyazott dokumentációjában olvashat bővebben.]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban az alábbi módon hoz létre britta simon tesztfelhasználót az Azure Portalon:

![Azure AD-tesztfelhasználó létrehozása][100]

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory hivatkozása](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Az aktuális felhasználók listájának megjelenítéséhez válassza a **Felhasználók és csoportok** > **minden felhasználó lehetőséget.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. A **Minden felhasználó** ablak tetején válassza a **Hozzáadás**lehetőséget.

    ![A Hozzáadás gomb](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Megnyílik **a Felhasználó** ablak.

1. A **Felhasználó** ablakban tegye a következőket:

    ![A Felhasználó ablak](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. A **Név** mezőbe írja be **a BrittaSimon (BrittaSimon**) mezőbe.

    b. A **Felhasználónév** mezőbe írja be Britta Simon felhasználó e-mail címét.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **Jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix tesztfelhasználó létrehozása

1. Jelentkezzen be az E Sales Manager Remix alkalmazásba rendszergazdaként.

1. Válassza a jobb felső sarokban található menü **Rendszergazda menüjének** parancsát.

    ![E Sales Manager Remix konfigurációja](./media/esalesmanagerremix-tutorial/configure4.png)

1. **Válassza a Vállalat beállításaiTA** > **részlegek és alkalmazottak karbantartása**lehetőséget, majd válassza **a Regisztrált alkalmazottak**lehetőséget.

    ![Az "Alkalmazottak regisztrálva" lap](./media/esalesmanagerremix-tutorial/user1.png)

1. Az **Új alkalmazott regisztrációja** csoportban tegye a következőket:
    
    ![Az "Új alkalmazott regisztrációja" szakasz](./media/esalesmanagerremix-tutorial/user2.png)

    a. Az **Alkalmazott neve** mezőbe írja be a felhasználó nevét (például **Britta).**

    b. Töltse ki a fennmaradó kötelező mezőket.
    
    c. Ha engedélyezi az SAML-t, a rendszergazda nem tud bejelentkezni a bejelentkezési lapról. Rendszergazdai bejelentkezési jogosultságokat adhat a felhasználónak a **Rendszergazdabejelentkezés** jelölőnégyzet bejelölésével.

    d. Válassza **a Regisztráció**lehetőséget.

1. A jövőben rendszergazdaként jelentkezzen be rendszergazdaként, jelentkezzen be rendszergazdai jogosultságokkal rendelkező felhasználóként, majd a jobb felső sarokban válassza a **Rendszergazda menü lehetőséget.**

    ![A "Rendszergazda menü" parancs](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi britta Simon felhasználó számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az E Sales Manager Remix számára. Ehhez tegye a következőket: 

![A felhasználói szerepkör hozzárendelése][200] 

1. Az Azure Portalon nyissa meg az **Alkalmazások nézetet,** lépjen a **Címtár** nézetre, és válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A "Vállalati alkalmazások" és a "Minden alkalmazás" linkek][201] 

1. Az **Alkalmazások** listában válassza az **E Sales Manager Remix**lehetőséget.

    ![Az E Sales Manager Remix hivatkozása](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás][202]

1. Válassza a **Hozzáadás** lehetőséget, majd a **Hozzárendelés hozzáadása** ablaktáblán válassza a Felhasználók **és csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla][203]

1. A **Felhasználók és csoportok** ablak **Felhasználók** listájában válassza **a Britta Simon**elemet.

1. Válassza a **Kijelölés** gombot.

1. A **Hozzárendelés hozzáadása** ablakban válassza a **Hozzárendelés**lehetőséget.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor a Hozzáférési panelen kiválasztja az E Sales Manager Remix csempét, automatikusan be kell jelentkeznie az E Sales Manager Remix alkalmazásba.

A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](../user-help/active-directory-saas-access-panel-introduction.md) 

## <a name="additional-resources"></a>További források

* [A SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](tutorial-list.md)
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

