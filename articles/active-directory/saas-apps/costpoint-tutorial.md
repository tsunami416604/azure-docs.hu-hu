---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Costpointtal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Costpoint között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71840089"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Oktatóanyag: Costpoint integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Costpointot az Azure Active Directoryval (Azure AD). Ha integrálja a Costpointot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Costpointhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve costpoint az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A Costpoint egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Costpoint támogatja az **SP és az IDP** által kezdeményezett sso-t.

## <a name="generate-costpoint-metadata"></a>Costpoint-metaadatok létrehozása

A Costpoint SAML SSO konfigurációját a **DeltekCostpoint711Security.pdf** útmutató ismerteti. Töltse le ezt az útmutatót a Deltek Costpoint támogatási webhelyéről, és tekintse meg az **SAML Single Sign-on Setup** > **Configure SAML Single Sign-on -ra costpoint és a Microsoft Azure között** című szakaszt. Kövesse az utasításokat, és hozzon létre egy **Costpoint SP összevonási metaadat-XML-fájlt.** 

![Costpoint konfigurációs segédprogram](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint hozzáadása a gyűjteményből

A Costpoint azure AD-vel való integrálásához először adja hozzá a Costpointot a felügyelt SaaS-alkalmazások listájához az Azure Portalon található galériából:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.

1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.

   ![Az Azure Active Directory gombja](common/select-azuread.png)

1. Válassza a **Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

   ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.

   ![Az Új alkalmazás gomb](common/add-new-app.png)

1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Költségpontot** a keresőmezőbe.

   ![Költségpont az eredménylistában](common/search-new-app.png)

1. Az eredménylistában válassza a **Costpoint**lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Az Azure AD egyetlen sgn-on konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD-sSO-t a Costpoint tal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Costpointban.

Az Azure AD SSO Costpoint tal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
1. **[A Costpoint konfigurálásával](#configure-costpoint)** konfigurálja az SAML SSO-beállításokat az alkalmazás oldalon.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. **[Hozzon létre egy Costpoint teszt felhasználó,](#create-a-costpoint-test-user)** hogy egy megfelelője B.Simon a Costpoint, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az alábbi lépéseket az Azure Portalon:

1. A **Costpoint** alkalmazásintegrációs lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

   ![Az egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

1. Az **Egyszerű SAML-konfiguráció** csoportban, ha rendelkezik a *Szolgáltató metaadatfájljával, hajtsa*végre az alábbi lépéseket:

   > [!NOTE]
   > A Szolgáltató metaadat-fájlját a [Costpoint metaadatok generálása](#generate-costpoint-metadata)című részben kapja meg. Hogyan kell használni a fájlt később a tutorial.
 
   1. Válassza a **Metaadat-fájl feltöltése** gombot, majd a **Costpoint SP összevonási metaadat-fájlját,** amelyet korábban a Costpoint hozott létre, majd a **Hozzáadás** gombra kattintva töltse fel a fájlt.

      ![A metaadatfájl feltöltése](./media/costpoint-tutorial/upload-metadata.png)
    
   1. A metaadatfájl sikeres feltöltésekén az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik a Costpoint szakaszban.

      > [!NOTE]
      > Ha az **azonosító** és a **válasz URL-értékei** nem automatikusan poluláltak, adja meg az értékeket manuálisan a követelménynek megfelelően. Ellenőrizze, hogy **az azonosító (entitásazonosító)** és **a válasz URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** megfelelően van-e beállítva, és hogy az **ACS URL-címe** egy érvényes Costpoint URL-cím-e, amely **a /LoginServlet.cps végződéssel végződik.**

   1. Válassza **a További URL-címek beállítása**lehetőséget. A **Továbbítási állapot**mezőbe írjon`system=[your system]` be egy értéket a következő minta használatával: (például **system=DELTEKCP**).

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** csoportjában válassza a **Másolás** ikont az **Alkalmazásösszevonás metaadat-címének** másolásához és a Jegyzettömbbe való mentéséhez.

   ![SAML aláíró tanúsítvány](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Költségpont konfigurálása

1. Visszatérés a Costpoint konfigurációs segédprogramhoz. Az **IdP-összevonás metaadat-XML-szövegmezőjében** illessze be az *App Federation metaadat-url-fájltartalmát.* 

   ![Costpoint konfigurációs segédprogram](./media/costpoint-tutorial/config-utility-idp.png)

1. Folytassa a **DeltekCostpoint711Security.pdf** útmutató utasításait a Costpoint SAML beállítás befejezéséhez.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portal nevű B.Simon.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

   ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

1. Válassza az **Új felhasználó**lehetőséget.

   ![Új felhasználó gomb](common/new-user.png)

1. A **Felhasználói** tulajdonságok párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![A Felhasználó párbeszédpanel](common/user-properties.png)

   1. A **Név** mezőbe írja be a **B.Simon**értéket.
   
   1. A **Felhasználónév** mezőbe `b.simon\@yourcompanydomain.extension` írja be B.Simon@contoso.coma (például ) mezőt.
   
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Costpoint-hoz való hozzáférés biztosításával B.Simon.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

1. Az alkalmazások listájában válassza a **Costpoint**lehetőséget.

1. Az alkalmazás áttekintése lap **Kezelés** szakaszában válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

   ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanel **Felhasználók** listájában válassza a **B.Simon**elemet. Ezután válassza a **Kijelölés lehetőséget.**

1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd válassza a **Kijelölés gombot.**

1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-costpoint-test-user"></a>Costpoint-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználót a Costpointban. Tegyük fel, hogy a felhasználói azonosító **B.SIMON,** a felhasználó neve **Pedig B.Simon**. Együttműködve a [Costpoint ügyfél támogatási csapatával](https://www.deltek.com/about/contact-us) a felhasználó hozzáadása a Costpoint platformon. A felhasználót létre kell hozni és aktiválni kell az egyszeri bejelentkezés használata előtt.

A felhasználó létrehozása után a felhasználó **hitelesítési módszerének** kiválasztása active **directory**nak kell lennie, be kell jelölnie az **SAML Single Sign-on** jelölőnégyzetet, és az Azure Active Directory felhasználónevének **Active Directorynak vagy tanúsítványazonosítónak** kell lennie (a következő képernyőképen látható).

![Costpoint felhasználó](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>SSO tesztelése

Amikor a Costpoint csempét választja a hozzáférési panelen, automatikusan be kell jelentkeznie a Costpoint alkalmazásba, mert beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálására irányuló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
