---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Cisco Webex-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Cisco Webex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046792"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Cisco Webex-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Cisco Webexet az Azure Active Directoryval (Azure AD). Ha integrálja a Cisco Webexet az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, amely hozzáfér a Cisco Webex-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek a Cisco Webexbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Cisco Webex egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Cisco Webex támogatja az **SP** által kezdeményezett SSO-t.
* A Cisco Webex támogatja **az automatikus** felhasználói kiépítést.
* A Cisco Webex konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a galériából

A Cisco Webex Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Cisco Webex-et a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Cisco Webex** kifejezést a keresőmezőbe.
1. Válassza ki a **Cisco Webex** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Cisco Webex számára

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco Webex-szel egy **B.Simon**nevű tesztfelhasználó segítségével. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Cisco Webex.

Az Azure AD SSO és a Cisco Webex konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
2. **[Konfigurálja a Cisco Webex-et](#configure-cisco-webex)** az SSO-beállítások alkalmazásoldali beállításához.
    1. **[Hozzon létre Cisco Webex tesztfelhasználót,](#create-cisco-webex-test-user)** hogy a Cisco Webex-ben b.Simon megfelelője legyen, amely a felhasználó Azure AD-reprezentációjához kapcsolódik.
3. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Cisco Webex** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban töltse fel a letöltött **szolgáltató metaadatfájlját,** és konfigurálja az alkalmazást az alábbi lépések végrehajtásával:

    >[!Note]
    >A Szolgáltató metaadatfájlját a **Cisco Webex konfigurálása** című részből kapja, amelyet az oktatóanyag későbbi részében ismertetünk. 

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    c. A Szolgáltató metaadatfájljának sikeres feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az **Alapvető SAML konfiguráció szakaszban:**

    A Bejelentkezés az **URL-címmezőbe** illessze be a **Válasz URL értékét,** amelyet az SP metaadatfájl-feltöltése automatikusan kitölt.

1. A Cisco Webex alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. Amellett, hogy a fenti, Cisco Webex alkalmazás elvárja, hogy néhány további attribútumokat kell átadni vissza SAML választ, amelyek az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.
  
    | Név |  Forrás attribútuma|
    | ---------------|--------- |
    | Uid | user.userprincipalname |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Cisco Webex beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Cisco Webex hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Cisco Webex**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-cisco-webex"></a>Cisco Webex konfigurálása

1. A Cisco Webex konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Cisco Webex** irányítja, hogy a Cisco Webex alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Cisco Webex-be való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-8.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Cisco Webex-et, jelentkezzen be a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) be a teljes rendszergazdai hitelesítő adataival.

4. Válassza a **Beállítások** lehetőséget, és a **Hitelesítés** csoportban kattintson a **Módosítás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Válassza **a Harmadik fél identitásszolgáltatójának integrálása lehetőséget. (Speciális)** és lépjen a következő képernyőre.

6. Az **Idp metaadatok importálása** lapon húzza az Azure AD metaadatfájlját a lapra, vagy használja a fájlböngésző tanait az Azure AD metaadatfájl megkereséséhez és feltöltéséhez. Ezután válassza **a Hitelesítésszolgáltató által aláírt tanúsítvány megkövetelése lehetőséget a metaadatokban (biztonságosabb)** és kattintson a **Tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Válassza **az SSO-kapcsolat tesztelése**lehetőséget, és amikor megnyílik egy új böngészőlap, jelentkezzen be az Azure AD-vel.

8. Térjen vissza a **Cisco Cloud Collaboration Management** böngészőfülre. Ha a teszt sikeres volt, válassza **a Ez a teszt sikeres volt lehetőséget. Engedélyezze az egyszeri bejelentkezés t,** és kattintson a **Tovább**gombra.

### <a name="create-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a Cisco Webex alkalmazásban. Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a Cisco Webex alkalmazásban.

1. Nyissa meg a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) et a teljes rendszergazdai hitelesítő adatokkal.

2. Kattintson **a Felhasználók,** majd **a Felhasználók kezelése parancsra.**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. A **Felhasználó kezelése** ablakban válassza a **Felhasználók manuális hozzáadása vagy módosítása** lehetőséget, majd kattintson a **Tovább**gombra.

4. Válassza **a Nevek és e-mail cím lehetőséget.** Ezután töltse ki a szövegdobozt az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B**.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. Az **E-mail cím** mezőbe írja be b.simon@contoso.coma felhasználó e-mail címét, például .

5. B.Simon hozzáadásához kattintson a pluszjelre. Ezután kattintson a **Tovább gombra.**

6. A **Szolgáltatások hozzáadása felhasználókhoz** ablakban kattintson a **Mentés** és **befejezés gombra.**

## <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Cisco Webex csempét, automatikusan be kell jelentkeznie a Cisco Webex-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Cisco Webexet az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Cisco Webex védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-webex)