---
title: 'Oktatóanyag: Azure Active Directory a munkahelyi integrációt a Facebook használatával | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a munkahely között a Facebook használatával.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4328c6b5b85050ae5caf30fd4d321e50428f10b9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825331"
---
# <a name="tutorial-integrate-workplace-by-facebook-with-azure-active-directory"></a>Oktatóanyag: A munkahelyi környezet integrálása a Facebook használatával Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a munkahelyeket Facebook-ba a Azure Active Directory (Azure AD) használatával. A munkahelyi Facebook és az Azure AD integrálásával a következőket teheti:

* Az Azure AD-ben elérhető, a Facebook által a munkahelyhez hozzáférő vezérlő.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkahelyre a Facebookban az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A munkahelyi Facebook egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

> [!NOTE]
> A Facebook két termékkel rendelkezik, a munkahelyi standard (ingyenes) és a munkahelyi prémium (fizetős). A munkahelyhez tartozó prémium bérlők a SCIM és az SSO-integrációt más, a szükséges díjakra vagy licencekre vonatkozó tényezők nélkül is konfigurálhatják. Az SSO és a SCIM nem érhető el a munkahelyi standard példányokban.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A munkahelyi Facebook által támogatott, **SP** által kezdeményezett egyszeri bejelentkezés
* A munkahelyen a Facebook az **igény szerinti üzembe** helyezést támogatja
* A munkahelyi Facebook által támogatott  **[automatikus felhasználó](workplacebyfacebook-provisioning-tutorial.md) -kiépítés**
* A Facebook Mobile alkalmazás munkahelye mostantól konfigurálható az Azure AD-vel az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Munkahelyi felvétel a Facebook használatával a katalógusból

A munkahelyi Facebook és az Azure AD közötti integráció konfigurálásához a Facebookban fel kell vennie a munkaterületet a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **munkahely a Facebookon** kifejezést a keresőmezőbe.
1. Válassza a **munkahely a facebookban** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a munkahelyen a Facebook használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a munkahelyi felhasználó között a Facebook használatával.

Az Azure AD SSO és a munkahelyi Facebook közötti konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[Munkahelyi beállítások konfigurálása a Facebook SSO](#configure-workplace-by-facebook-sso)** -ben – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Munkahelyi munkahely létrehozása Facebook](#create-workplace-by-facebook-test-user)** -teszttel – a felhasználó által a munkahelyi Azure ad-képviselethez kapcsolódó B. Simon-partnernek.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **munkahelyi Facebook** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instancename>.facebook.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. A munkahelyi Közösség megfelelő értékeinek megtekintéséhez tekintse meg a munkahelyi vállalati irányítópult hitelesítés lapját.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A **munkahely beállítása Facebook használatával** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-workplace-by-facebook-sso"></a>Munkahelyi konfiguráció konfigurálása a Facebook SSO használatával

1. Egy másik böngészőablakban jelentkezzen be a munkahelyre a Facebook vállalati webhelyén rendszergazdaként.
  
    > [!NOTE]
    > Az SAML hitelesítési folyamat részeként a munkahely akár 2,5 kilobájtos lekérdezési karakterláncot is használhat a paraméterek Azure AD-ba való továbbításához.

2. A **felügyeleti panelen**lépjen a **Biztonság** lapra.

    ![Felügyeleti panel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. A **hitelesítés** lapon válassza az **egyszeri bejelentkezés (SSO)** lehetőséget, és hajtsa végre a következő lépéseket:

    ![Hitelesítés lap](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Az **SAML URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    b. Az **SAML-kiállító URI-ja szövegmezőben**illessze be a Azure Portalból másolt **Azure ad-azonosító**értékét.

    c. Az **SAML** kijelentkezésének átirányítása (nem kötelező) mezőben illessze be a kijelentkezési **URL-címet**, amelyet a Azure Portalból másolt.

    d. Nyissa meg az alapszintű **64-kódolású tanúsítványt** a jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **SAML-tanúsítvány** szövegmezőbe.

    e. Másolja a példány **célközönségének URL-címét** , és illessze be az **azonosító (Entity ID)** szövegmezőbe a Azure Portal alapszintű **SAML-konfiguráció** szakaszában.

    f. Másolja a példányhoz tartozó **címzett URL-címet** , és illessze be a **bejelentkezési URL-cím** szövegmezőbe az alapszintű **SAML-konfiguráció** szakaszának Azure Portalján.

    g. Görgessen a szakasz aljára, és kattintson az **egyszeri bejelentkezés tesztelése** gombra. Ennek eredményeként megjelenik egy előugró ablak, amely az Azure AD bejelentkezési oldalán jelenik meg. A hitelesítéshez adja meg a hitelesítő adatait a szokásos módon.

    **Hibaelhárítás** Győződjön meg arról, hogy az Azure AD-ből visszaadott e-mail-cím megegyezik azzal a munkahelyi fiókkal, amelybe bejelentkezett.

    h. Miután a teszt sikeresen befejeződött, görgessen a lap aljára, és kattintson a Save ( **Mentés** ) gombra.

    i. A munkahelyen használó összes felhasználó most megjelenik az Azure AD bejelentkezési oldala a hitelesítéshez.

4. **SAML-kijelentkezés átirányítása (nem kötelező)**  -

    Dönthet úgy, hogy opcionálisan konfigurál egy SAML kijelentkezési URL-címet, amely az Azure AD kijelentkezési oldalán is használható. Ha ez a beállítás engedélyezve és konfigurálva van, a felhasználó nem lesz átirányítva a munkahelyi kijelentkezés lapra. Ehelyett a rendszer átirányítja a felhasználót az SAML kijelentkezési átirányítási beállításban hozzáadott URL-címre.

### <a name="configuring-reauthentication-frequency"></a>Az újrahitelesítés gyakoriságának konfigurálása

A munkahelyet beállíthatja úgy, hogy minden nap, három nap, hét, két hét, hónap vagy soha ne Kérdezzen rá SAML-vizsgálatra.

> [!NOTE]
> A mobileszközök SAML-vizsgálatának minimális értéke egy hétig van beállítva.

Az SAML alaphelyzetbe állítását az összes felhasználóra vonatkozóan a következő gomb használatával is kényszerítheti: SAML-hitelesítés megkövetelése az összes felhasználó számára.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a munkahelyi Facebook-hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **munkahely a Facebook alapján**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-workplace-by-facebook-test-user"></a>Munkahely létrehozása Facebook-teszt felhasználó által

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a munkahelyen a Facebookban. A munkahelyen a Facebook az igény szerinti üzembe helyezést támogatja, ami alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs művelet. Ha a Facebookban nem szerepel egy felhasználó a munkahelyen, a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a munkahelyi Facebook-hoz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a munkahelyi kapcsolattal a [Facebook](https://workplace.fb.com/faq/) ügyfélszolgálati csapatával

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a munkahelyi Facebook csempére kattint, automatikusan be kell jelentkeznie a munkahelyre a Facebookban, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>A munkahelyi egyszeri bejelentkezés tesztelése a Facebookban (mobil)

1. Nyissa meg a munkahelyet Facebook Mobile-alkalmazással. A bejelentkezés lapon kattintson a **Bejelentkezés**elemre.

    ![A bejelentkezés](./media/workplacebyfacebook-tutorial/test05.png)

2. Adja meg az üzleti levelezését, és kattintson a **Folytatás**gombra.

    ![Az e-mail cím](./media/workplacebyfacebook-tutorial/test02.png)

3. Kattintson **egyszer csak egyszer**.

    ![Egyszer](./media/workplacebyfacebook-tutorial/test04.png)

4. Kattintson az **Engedélyezés**gombra.

    ![Az engedélyezés](./media/workplacebyfacebook-tutorial/test03.png)

5. Végül a sikeres bejelentkezés után megjelenik az alkalmazás kezdőlapja.    

    ![A Kezdőlap](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](workplacebyfacebook-provisioning-tutorial.md)

