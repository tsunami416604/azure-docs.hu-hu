---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Oracle Cloud Infrastructure Console konzollal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Oracle Cloud Infrastructure Console között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289101"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Oktatóanyag: Az Oracle Cloud Infrastructure Console integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Oracle Cloud Infrastructure Console-t az Azure Active Directoryval (Azure AD). Ha integrálja az Oracle Cloud Infrastructure Console-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá az Oracle Cloud Infrastructure Console konzolhoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek az Oracle Cloud Infrastructure Console-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Az Oracle Cloud Infrastructure Console egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Oracle Cloud Infrastructure Console támogatja az **SP** által kezdeményezett sso-t.
* Az Oracle Cloud Infrastructure Console konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Oracle Cloud Infrastructure Console hozzáadása a galériából

Az Oracle Cloud Infrastructure Console Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Oracle Cloud Infrastructure Console-t a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **Oracle Cloud Infrastructure Console kifejezést** a keresőmezőbe.
1. Válassza az **Oracle Cloud Infrastructure Console** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO szolgáltatást az Oracle Cloud Infrastructure Console segítségével egy **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Oracle Cloud Infrastructure Console-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az Oracle Cloud Infrastructure Console konzolon hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
1. **[Konfigurálja az Oracle Cloud Infrastructure Console-t](#configure-oracle-cloud-infrastructure-console)** az sso-beállítások alkalmazásoldali konfigurálásához.
    1. **[Hozzon létre oracle cloud infrastructure konzolteszt-felhasználót,](#create-oracle-cloud-infrastructure-console-test-user)** hogy b. Simon megfelelője legyen az Oracle Cloud Infrastructure Console-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Oracle Cloud Infrastructure Console** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

   > [!NOTE]
   > A Szolgáltató metaadat-fájlját az oktatóanyag **Oracle Cloud Infrastructure Console Single Sign-On** című szakaszából kapja meg.
    
   1. Kattintson **a Metaadatfájl feltöltése gombra.**

   1. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

   1. A metaadatfájl sikeres feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az **Egyszerű SAML konfigurációszakasz** szövegmezőjében.
    
      > [!NOTE]
      > Ha az **azonosító** és a **válasz URL-értékei** nem kapnak automatikus polulated, majd töltse ki az értékeket manuálisan a követelménynek megfelelően.

      A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba az [Oracle Cloud Infrastructure Console Ügyfél támogatási csapatával](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az Oracle Cloud Infrastructure Console alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

   ![image](common/edit-attribute.png)

1. A fentieken kívül az Oracle Cloud Infrastructure Console alkalmazás azt várja, hogy néhány további attribútum ot visszaadjon az SAML-válaszban. A **Felhasználói attribútumok & Jogcímek** párbeszédpanel **jogcímek (előzetes verzió)** szakaszában hajtsa végre az alábbi lépéseket:

   1. Kattintson a **Névazonosító értéke**melletti **tollra.**

   1. Válassza **az Állandó** lehetőséget a **Névazonosító formátum ának kiválasztása ként.**
 
   1. Kattintson a **Mentés** gombra.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Kattintson a **jogcímben visszaadott csoportok**melletti **tollra.**

   1. Válassza a **biztonsági csoportok lehetőséget** a rádiólistából.

   1. Válassza a **Csoportazonosító** **forrásattribútumát.**

   1. Jelölje **be a Csoportjogcím nevének testreszabása**jelölőnégyzetet.

   1. A **Név** mezőbe írja be a **csoportnév mezőjét.**

   1. A **Névtér (nem kötelező)** `https://auth.oraclecloud.com/saml/claims`mezőbe írja be a következőt:

   1. Kattintson a **Mentés** gombra.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Az **Oracle Cloud Infrastructure Console beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B. Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi B. Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az Oracle Cloud Infrastructure Console konzolhoz.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Oracle Cloud Infrastructure Console lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

   ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-oracle-cloud-infrastructure-console"></a>Az Oracle Cloud Infrastructure Console konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az Oracle Cloud Infrastructure Console szolgáltatásba.

1. Kattintson a bal oldalon a menü, és kattintson a **Személyazonosság,** majd keresse meg a **Föderáció**.

   ![Konfiguráció](./media/oracle-cloud-tutorial/config01.png)

1. Mentse a **szolgáltató metaadatfájlját** a **Dokumentum letöltése** hivatkozásra kattintva, és töltse fel az Azure Portal **Basic SAML Konfiguráció szakaszába,** majd kattintson az **Identitásszolgáltató hozzáadása gombra.**

   ![Konfiguráció](./media/oracle-cloud-tutorial/config02.png)

1. Az **Identitásszolgáltató hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

   ![Konfiguráció](./media/oracle-cloud-tutorial/config03.png)

   1. A **NÉV** mezőbe írja be a nevét.

   1. A **LEÍRÁS** mezőbe írja be a leírást.

   1. Típusként válassza a **MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) vagy az SAML 2.0 compliant IDENTITY PROVIDER (Microsoft Active Directory FEDERATION SERVICE, ADFS) vagy az SAML 2.0-kompatibilis identitásszolgáltatót** . **TYPE**

   1. Kattintson a **Tallózás** gombra az Összevonási metaadat-XML feltöltéséhez, amelyet az Azure Portalról töltött le.

   1. Kattintson a **Folytatás** gombra, és az **Identitásszolgáltató szerkesztése** szakaszban hajtsa végre az alábbi lépéseket:

      ![Konfiguráció](./media/oracle-cloud-tutorial/config09.png)

   1. Az **IDENTITY PROVIDER GROUP-ot** egyéni csoportként kell kiválasztani. A CSOPORTazonosítónak az Azure Active Directoryból származó csoport GUID azonosítójának kell lennie. A csoportot le kell képezni az **OCI GROUP** mező megfelelő csoportjával.

   1. Több csoportot is leképezhet az Azure Portalon beállított beállítások nak és a szervezet igényének. Kattintson a **+ Térkép hozzáadása** gombra, hogy annyi csoportot adjon hozzá, amennyire szüksége van.

   1. Kattintson a **Küldés gombra.**
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle Cloud Infrastructure Console tesztfelhasználó létrehozása

 Az Oracle Cloud Infrastructure Console támogatja a just-in-time kiépítést, amely alapértelmezés szerint. Ebben a szakaszban nincs műveletelem. Az új felhasználó nem jön létre a hozzáférési kísérlet során, és nem kell létrehoznia a felhasználót.

### <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja az Oracle Cloud Infrastructure Console csempét a hozzáférési panelen, a rendszer átirányítja az Oracle Cloud Infrastructure Console bejelentkezési oldalára. Válassza ki az **IDENTITY PROVIDER** elemet a legördülő menüből, majd a Bejelentkezéshez kattintson a **Folytatás gombra** az alábbi módon. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

![Konfiguráció](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az Oracle Cloud Infrastructure Console védelme fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
