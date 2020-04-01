---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Workplace-szel a Facebook által | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Workplace között a Facebook által.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de84f2aee5f59d14ab70cb1687968643c4cdb31e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136380"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Workplace-szel a Facebook által

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Workplace by Facebookot az Azure Active Directoryval (Azure AD). Ha integrálja a Workplace by Facebookot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a Workplace-hez a Facebookon keresztül.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezhessenek a Workplace-be a Facebookon az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Workplace by Facebook single sign-on (SSO) enabled subscription.

> [!NOTE]
> A Facebooknak két terméke van: a Workplace Standard (ingyenes) és a Workplace Premium (fizetett). Bármely Workplace Premium-bérlő konfigurálhatja az SCIM és az SSO-integrációt, a költségekre vagy a licencekre gyakorolt egyéb következmények nélkül. Az SSO és az SCIM nem érhető el a Workplace Standard példányokban.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Munkahely a Facebook támogatja **SP** kezdeményezett SSO
* A Workplace by Facebook támogatja **az just-in-time kiépítést**
* A Workplace by Facebook támogatja az ** [automatikus felhasználói kiépítést](workplacebyfacebook-provisioning-tutorial.md)**
* Workplace by Facebook Mobile alkalmazás most már konfigurálható az Azure AD az SSO engedélyezéséhez. Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.
* Miután beállítottad a Workplace by Facebook-ot, kényszerítheted a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Munkahely hozzáadása a Facebookáltal a galériából

A Workplace by Facebook Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Workplace by Facebook-ot a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írd be a Keresőmezőbe a **Workplace by Facebook (Munkahely a Facebook szerint)** kifejezést.
1. Válaszd a **Workplace by Facebook** lehetőséget az eredménypanelen, majd add hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Az Azure AD SSO munkahelyi szolgáltatásának konfigurálása és tesztelése a Facebook által

Konfigurálja és tesztelje az Azure AD SSO-t a Workplace by Facebook segítségével egy **B.Simon**nevű tesztfelhasználó segítségével. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Workplace by Facebook-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a Workplace by Facebook segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja a Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** -konfigurálása az egyszeri bejelentkezés i beállításait az alkalmazás oldalán.
    * **[Hozzlétre workplace by Facebook teszt felhasználó](#create-workplace-by-facebook-test-user)** - hogy egy megfelelője B.Simon a munkahelyen a Facebook, amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Workplace by Facebook** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<instancename>.facebook.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.facebook.com/company/<instanceID>`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Ezek az értékek nem a valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Tekintse meg a Munkahelyi vállalat irányítópultjának Hitelesítés ilapját a munkahelyi közösség megfelelő értékeiért, ezt az oktatóanyag későbbi részében ismerteti.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Munkahely beállítása a Facebook szerint** szakaszban másold le a megfelelő URL-cím(eke)t a követelmény edzése alapján.

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

Ebben a szakaszban engedélyezheti b.Simon számára, hogy egyetlen bejelentkezéssel használja az Azure-t azáltal, hogy hozzáférést biztosít a Workplace-hez a Facebook által.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Workplace by Facebook lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-workplace-by-facebook-sso"></a>Munkahely konfigurálása a Facebook sso szerint

1. A Workplace by Facebook konfigurációjának automatizálásához a Bővítmény telepítése gombra kattintva telepítened kell **a** **My Apps Secure Sign-in böngészőbővítményt.**

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadtál egy bővítményt a böngészőhöz, kattints a **Workplace by Facebook beállításra,** amely a Workplace by Facebook alkalmazáshoz vezet. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy bejelentkezz a Workplace by Facebook-ba. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-5.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretnéd beállítani a Workplace by Facebookot, nyiss meg egy új böngészőablakot, és jelentkezz be rendszergazdaként a Workplace by Facebook webhelyedre, és hajtsd végre a következő lépéseket:

    > [!NOTE]
    > Az SAML hitelesítési folyamat részeként a Workplace legfeljebb 2,5 kilobájt méretű lekérdezési karakterláncokat is felhasználhatja, hogy paramétereket továbbítson az Azure AD-nek.

1. A bal oldali navigációs panelen keresse meg a **Biztonsági** > **hitelesítés** lapot.

    ![Felügyeleti panel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Jelölje be **az Egyszeri bejelentkezés (Egyszeri bejelentkezés)** beállítást.
    
    b. Kattintson a **+Új SSO-szolgáltató hozzáadása gombra.**
    > [!NOTE]
    > Győződjön meg róla, hogy ellenőrizze a Jelszó bejelentkezés jelölőnégyzetet is. A rendszergazdáknak szükségük lehet erre a beállításra a bejelentkezéshez, miközben a tanúsítványváltást végzik annak érdekében, hogy ne zárják ki magukat.

1. A **Hitelesítés** lapon válassza **az Egyszeri bejelentkezés (SSO)** lehetőséget, és hajtsa végre a következő lépéseket:

    ![Hitelesítés lap](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Az **SSO-szolgáltató neve,** adja meg az SSO-példány nevét, például Azureadsso.

    b. Az **SAML URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Az **SAML Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    d. Nyissa meg az **alap-64 kódolású tanúsítványt** az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **SAML tanúsítvány** szövegdobozába.

    e. Másolja a **példány közönségének URL-címét,** és illessze be **az Azonosító (entitásazonosító)** szövegmezőbe az Azure Portal **Alapszintű SAML-konfiguráció szakaszában.**

    f. Másolja a **példány címzetturl-címét,** és illessze be **a Bejelentkezési URL-cím** beírt szövegmezőbe az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    g. Másolja a példány **ACS (helyességi feltétel fogyasztói szolgáltatás) URL-címét,** és illessze be a **Válasz URL-szövegmezőbe** az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    h. Görgessen a szakasz aljára, és kattintson az **SSO tesztelése** gombra. Ennek eredménye egy előugró ablak jelenik meg az Azure AD bejelentkezési lap jelenik meg. Adja meg hitelesítő adatait a hitelesítéshez a szokásos módon.

    **Hibaelhárítás:** Győződjön meg arról, hogy az Azure AD-ből visszaküldött e-mail-cím megegyezik azzal a Workplace-fiókkal, amellyel bejelentkezett.

    i. A teszt sikeres befejezése után görgessen az oldal aljára, és kattintson a **Mentés** gombra.

    j. A Workplace-t használó összes felhasználó megjelenik az Azure AD bejelentkezési lapja a hitelesítéshez.

1. **SAML kijelentkezés átirányítása (nem kötelező)** -

    Választhat, hogy szükség esetén konfigurálja az SAML kijelentkezési URL-címet, amely az Azure AD kijelentkezési lapjára mutat. Ha ez a beállítás engedélyezve van és konfigurálva van, a felhasználó a továbbiakban nem lesz a Munkahelyi kijelentkezés lapra irányítva. Ehelyett a rendszer átirányítja a felhasználót az SAML kijelentkezésátirányítási beállításban hozzáadott URL-címre.

### <a name="configuring-reauthentication-frequency"></a>Újrahitelesítési gyakoriság konfigurálása

Beállíthatja, hogy a Workplace minden nap, három nap, hét, két hét, hónap vagy soha saml-ellenőrzést kér.

> [!NOTE]
> A mobilalkalmazások SAML-ellenőrzésének minimális értéke egy hét.

Az SAML alaphelyzetbe állítását az összes felhasználó számára is kényszerítheti a gomb: Saml-hitelesítés megkövetelése az összes felhasználó számára.

### <a name="create-workplace-by-facebook-test-user"></a>Munkahely létrehozása a Facebook tesztfelhasználója szerint

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a Workplace szolgáltatásban a Facebook. A Workplace by Facebook támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs művelet. Ha egy felhasználó nem létezik a Workplace by Facebook alkalmazásban, akkor új at hoz létre, amikor a Facebook által próbálsz hozzáférni a Workplace szolgáltatáshoz.

>[!Note]
>Ha manuálisan kell létrehoznod egy felhasználót, lépj kapcsolatba [a Workplace szolgáltatással a Facebook-ügyfél támogatási csapata szerint.](https://workplace.fb.com/faq/)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Munkahely a Facebook on Facebook csempére kattintasz, a Facebook nak automatikusan be kell jelentkeznie a Workplace-be, amelyhez bejelölted az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Munkahelyi tesztelési szolgáltatás facebookos (mobil)

1. A Workplace megnyitása a Facebook Mobile alkalmazás szerint. A bejelentkezési lapon kattintson a **LOG IN gombra.**

    ![A bejelentkezés](./media/workplacebyfacebook-tutorial/test05.png)

2. Adja meg az üzleti e-mail címét, és kattintson **a FOLYTATÁS gombra.**

    ![Az e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Kattintson **csak egyszer**.

    ![Az egykor](./media/workplacebyfacebook-tutorial/test04.png)

4. Kattintson az **Engedélyezés** lehetőségre.

    ![Az engedélyezés](./media/workplacebyfacebook-tutorial/test03.png)

5. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.    

    ![A kezdőlap](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználói kiépítés konfigurálása](workplacebyfacebook-provisioning-tutorial.md)

- [Próbálja ki a Workplace by Facebook ot az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
