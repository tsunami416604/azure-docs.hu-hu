---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Google Cloud (G Suite) összekötővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Google Cloud (G Suite) connector között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3282dd88b62a6811031e95672638d67702215a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048450"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Google Cloud (G Suite) összekötővel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Google Cloud (G Suite) összekötőt az Azure Active Directoryval (Azure AD). Ha integrálja a Google Cloud (G Suite) Connectort az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a Google Cloud (G Suite) connector-hoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek a Google Cloud (G Suite) Connector-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

- Egy Azure AD-előfizetés.
- A Google Cloud (G Suite) Connector egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.
- Google Apps-előfizetés vagy Google Cloud Platform-előfizetés.

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát. Ez a dokumentum az új egyszeri bejelentkezési felület használatával jött létre. Ha még mindig a régit használja, a beállítás máshogy fog kinézni. Az új felületet a G-Suite alkalmazás egyszeri bejelentkezési beállításaiban engedélyezheti. Nyissa meg az **Azure AD, Enterprise alkalmazások at**, válassza a Google Cloud **(G Suite) összekötő**lehetőséget, válassza az Egyszeri **bejelentkezés** lehetőséget, majd kattintson az Új **élmény kipróbálása**elemre.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. **K: Támogatja ez az integráció a Google Cloud Platform SSO-integrációt az Azure AD-vel?**

    V: Igen. A Google Cloud Platform és a Google Apps ugyanazon a hitelesítési platformon osztozik. Tehát a GCP-integrációhoz konfigurálnia kell az SSO-t a Google Apps alkalmazásokkal.

2. **K: A Chromebookok és más Chrome-eszközök kompatibilisek az Azure AD egyszeri bejelentkezéssel?**
  
    A: Igen, a felhasználók azure-beli AD-hitelesítő adataikkal jelentkeznek be a Chromebook-eszközeikbe. Tekintse meg ezt a [Google Cloud (G Suite) összekötő támogatási cikket](https://support.google.com/chrome/a/answer/6060880) arról, hogy a felhasználók miért kérhetnek kétszer hitelesítő adatokat.

3. **K: Ha engedélyezem az egyszeri bejelentkezést, a felhasználók azure-beli AD-hitelesítő adataikkal jelentkezhetnek be bármely Google-termékbe, például a Google Osztályterembe, a GMailbe, a Google Drive-ba, a YouTube-ba és így tovább?**

    Válasz: Igen, attól függően, hogy [melyik Google Cloud (G Suite) összekötőt](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) engedélyezi vagy tiltja le a szervezetszámára.

4. **K: Engedélyezhetem az egyszeri bejelentkezést a Google Cloud (G Suite) összekötő felhasználóinak csak egy részhalmazához?**

    A: Nem, az egyszeri bejelentkezés azonnali bekapcsolása szükséges az összes Google Cloud (G Suite) összekötő felhasználók hitelesítéséhez az Azure AD hitelesítő adatait. Mivel a Google Cloud (G Suite) Connector nem támogatja a több identitásszolgáltató t, a Google Cloud (G Suite) Connector környezet identitásszolgáltatója lehet Azure AD vagy Google – de nem egyszerre mindkettő.

5. **K: Ha egy felhasználó windowsos rendszeren keresztül jelentkezik be, automatikusan hitelesíti magát a Google Cloud (G Suite) összekötőn anélkül, hogy jelszót kérne?**

    A: Két lehetőség van a forgatókönyv engedélyezésére. Először is, a felhasználók bejelentkezhetnek a Windows 10-es eszközökre az [Azure Active Directory-csatlakozáson](../device-management-introduction.md)keresztül. Azt is megteheti, hogy a felhasználók bejelentkezhetnek olyan Windows-eszközökre, amelyek tartományhoz csatlakoznak egy olyan helyszíni Active Directoryhoz, amely engedélyezve van az Azure AD-be való egyszeri bejelentkezéshez az [Active Directory összevonási szolgáltatások (AD FS)](../hybrid/plan-connect-user-signin.md) központi telepítésén keresztül. Mindkét lehetőség megköveteli, hogy hajtsa végre a lépéseket a következő oktatóanyag egyszeri bejelentkezés az Azure AD és a Google Cloud (G Suite) Connector között.

6. **K: Mi a teendő, ha "érvénytelen e-mail" hibaüzenet jelenik meg?**

    A: Ehhez a beállításhoz az e-mail attribútum szükséges ahhoz, hogy a felhasználók be tudjanak jelentkezni. Ez az attribútum nem állítható be manuálisan.

    Az e-mail attribútum automatikusan kivan töltve minden érvényes Exchange-licenccel rendelkező felhasználó számára. Ha a felhasználó nem e-mailben engedélyezve van, ez a hiba akkor jelenik meg, mert az alkalmazásnak szüksége van az attribútum eléréséhez.

    A rendszergazdai fiókkal portal.office.com, majd a Felügyeleti központban, a számlázáson, az előfizetéseken, kiválaszthatja az Office 365-előfizetést, majd a felhasználókhoz rendelve kattintson, válassza ki azokat a felhasználókat, akiket ellenőrizni szeretne az előfizetésükben, és a jobb oldali ablaktáblában kattintson a licenceket szerkeszt.

    Az O365 licenc hozzárendelése után eltarthat néhány percig. Ezt követően a user.mail attribútum automatikusan kilesz töltve, és a problémát meg kell oldani.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Google Cloud (G Suite) connector támogatja az **SP** által kezdeményezett SSO-t

* A Google Cloud (G Suite) connector támogatja [ **az automatikus** felhasználói kiépítést](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* A Google Cloud (G Suite) connector konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Google Cloud (G Suite) összekötő hozzáadása a galériából

A Google Cloud (G Suite) Connector Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Google Cloud (G Suite) Connector-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Google Cloud (G Suite) Connector** kifejezést a keresőmezőbe.
1. Válassza ki a **Google Cloud (G Suite) connector** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Google Cloud (G Suite) Connector számára

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást a Google Cloud (G Suite) összekötővel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Google Cloud (G Suite) Connector.

Az Azure AD SSO konfigurálásához és teszteléséhez a Google Cloud (G Suite) Connector segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Google Cloud (G Suite) Connector Egyszeri bejelentkezést](#configure-google-cloud-g-suite-connector-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Google Cloud (G Suite) összekötő teszt felhasználó](#create-google-cloud-g-suite-connector-test-user)** - egy megfelelője B.Simon a Google Cloud (G Suite) connector, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Google Cloud (G Suite) összekötő alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha konfigurálni szeretné a **Gmailhez,** hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával: 

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha konfigurálni szeretné a **Google Cloud Platformot,** hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával: 
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. A Google Cloud (G Suite) Connector nem adja meg az Entitásazonosító/azonosító értéket az egyszeri bejelentkezés konfigurációban, `google.com`így ha nem jelöli be a **tartományspecifikus kibocsátó** i. Ha ellenőrzi a **tartomány-specifikus kibocsátó** `google.com/a/<yourdomainname.com>`opció lesz . A **tartományspecifikus kibocsátó** opció ellenőrzéséhez/törölje a jelet, meg kell adnia a **Google Cloud (G Suite) Összekötő SSO konfigurálása** szakaszt, amelyet az oktatóanyag későbbi részében ismertetünk. További információért forduljon a [Google Cloud (G Suite) Connector ügyféltámogatási csapatához.](https://www.google.com/contact/)

1. A Google Cloud (G Suite) connector alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútumleképezések hozzáadása az SAML token attribútumok konfigurációját. A következő képernyőkép egy példát mutat erre. Az egyedi **felhasználói azonosító** alapértelmezett értéke a **user.userprincipalname,** de a Google Cloud (G Suite) Connector azt várja, hogy ez le legyen képezve a felhasználó e-mail címével. Ehhez használhatja **a listából származó user.mail** attribútumot, vagy használhatja a megfelelő attribútumértéket a szervezet konfigurációja alapján.

    ![image](common/default-attributes.png)


1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Google Cloud (G Suite) összekötő beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Google Cloud (G Suite) connector hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Google Cloud (G Suite) Connector**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>A Google Cloud (G Suite) összekötő sso-jának konfigurálása

1. Nyisson meg egy új lapot a böngészőjében, és jelentkezzen be a [Google Cloud (G Suite) Connector Admin Console-ba](https://admin.google.com/) a rendszergazdai fiókjával.

2. Kattintson a **Biztonság gombra.** Ha nem látja a hivatkozást, előfordulhat, hogy a hivatkozás a képernyő alján található **További vezérlők** menü alatt van elrejtve.

    ![Kattintson a Biztonság gombra.][10]

3. A **Biztonság** lapon kattintson az **Egyszeri bejelentkezés (Egyszeri bejelentkezés) beállítása elemre.**

    ![Kattintson az SSO gombra.][11]

4. Hajtsa végre a következő konfigurációs módosításokat:

    ![SSO konfigurálása][12]

    a. Válassza **a telepítési egyszeri szolgáltatót külső identitásszolgáltatóval.**

    b. A Google Cloud (G Suite) Connector **Bejelentkezési oldal URL-címe** mezőjébe illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    c. A Google Cloud (G Suite) Connector **Kijelentkezési oldal URL-címe** mezőjébe illessze be a **Kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. A **Jelszó módosítása URL-cím** mezőben a Google Cloud (G Suite) Connector, illessze be a **Jelszó módosítása URL-címet,** amely et másolt az Azure Portalon.

    e. A Google Cloud (G Suite) connector, az **ellenőrzési tanúsítvány,** töltse fel az Azure Portalról letöltött tanúsítványt.

    f. Jelölje be/törölje a **tartományspecifikus kiállító használata** beállítást az Azure AD fent **en yakad konfigurációs** szakaszában említett megjegyzésnek megfelelően.

    g. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Google Cloud (G Suite) összekötő tesztfelhasználó létrehozása

Ez a szakasz célja, hogy [hozzon létre egy felhasználót a Google Cloud (G Suite) Connector](https://support.google.com/a/answer/33310?hl=en) nevű B.Simon. Miután a felhasználót manuálisan hozták létre a Google Cloud (G Suite) connector alkalmazásban, a felhasználó most már bejelentkezhet az Office 365 bejelentkezési hitelesítő adataival.

A Google Cloud (G Suite) Connector is támogatja az automatikus felhasználói kiépítést. Az automatikus felhasználói kiépítés konfigurálásához először [konfigurálnia kell a Google Cloud (G Suite) összekötőt az automatikus felhasználói kiépítéshez.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

> [!NOTE]
> Győződjön meg arról, hogy a felhasználó már létezik a Google Cloud (G Suite) connector, ha kiépítése az Azure AD-ben nem volt bekapcsolva tesztelése előtt single sign-on.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Google támogatási csapatához.](https://www.google.com/contact/)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Google Cloud (G Suite) összekötő csempére kattint, automatikusan be kell jelentkeznie arra a Google Cloud (G Suite) összekötőbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználói kiépítés konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Próbálja ki a Google Cloud (G Suite) connector-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Google Cloud (G Suite) összekötő védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png