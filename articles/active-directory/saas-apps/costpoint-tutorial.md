---
title: 'Oktatóanyag: Azure Active Directory integráció a Costpoint-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Costpoint között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 16c9d64cc4dd49898245d74108cd6a4f2f0e4660
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455170"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Oktatóanyag: a Costpoint és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Costpoint a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Costpoint-t az Azure AD-vel, a következőket teheti:

* A Costpoint-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Costpoint az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Costpoint egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli környezetekben végezheti el. A Costpoint **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="generate-costpoint-metadata"></a>Costpoint-metaadatok előállítása

A Costpoint SAML SSO-konfiguráció ismertetése a **DeltekCostpoint711Security.pdf** útmutatóban található. Töltse le ezt az útmutatót a Deltek Costpoint támogatási webhelyéről, és tekintse meg az **SAML egyszeri bejelentkezés**beállítása  >  **SAML egyszeri bejelentkezést a Costpoint és a Microsoft Azure szakasz között** . Kövesse az utasításokat, és készítsen **COSTPOINT SP összevonási metaadatokat tartalmazó XML-** fájlt. 

![Képernyőfelvétel: a "termék-konfigurációs segédprogram" a "Weblogic-Security" lapon van kiválasztva.](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Costpoint hozzáadása a gyűjteményből

A Costpoint és az Azure AD integrálásához először adja hozzá a felügyelt SaaS-alkalmazások listájához a Azure Portal a galériából:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.

1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.

   ![A Azure Active Directory gomb](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.

   ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.

   ![Az új alkalmazás gomb](common/add-new-app.png)

1. A **Hozzáadás a** katalógusból szakaszban adja meg a **Costpoint** kifejezést a keresőmezőbe.

   ![Costpoint az eredmények listájában](common/search-new-app.png)

1. Az eredmények listájában válassza a **Costpoint**lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Az Azure AD egyetlen SGN konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Costpoint-mel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Costpoint-ben.

Az Azure AD SSO és a Costpoint konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
1. **[Konfigurálja a Costpoint](#configure-costpoint)** az SAML SSO beállításainak konfigurálásához az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Hozzon létre egy Costpoint-tesztelési felhasználót](#create-a-costpoint-test-user)** , hogy a Costpoint B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO-t a Azure Portalban:

1. A **Costpoint** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

   ![Az egyszeri bejelentkezés hivatkozásának konfigurálása](common/select-sso.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha rendelkezik a szolgáltatói *metaadatok fájljával*, hajtsa végre a következő lépéseket:

   > [!NOTE]
   > A szolgáltatói metaadatokat a Costpoint- [metaadatok létrehozása](#generate-costpoint-metadata)című fájlban szerezheti be. A fájl használatát az oktatóanyag későbbi részében ismertetjük.
 
   1. Kattintson a **metaadatok feltöltése** gombra, majd válassza ki a **Costpoint SP összevonási METAADATOKAT tartalmazó XML-** fájlt, amelyet korábban a Costpoint hozott létre, majd kattintson a **Hozzáadás** gombra a fájl feltöltéséhez.

      ![A metaadat-fájl feltöltése](./media/costpoint-tutorial/upload-metadata.png)
    
   1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan ki lesznek töltve a Costpoint szakaszban.

      > [!NOTE]
      > Ha az **azonosító** és a **Válasz URL-címe** nem automatikus polulated, a követelménynek megfelelően adja meg manuálisan az értékeket. Győződjön meg arról, hogy az **azonosító (Entity ID)** és a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** megfelelően van beállítva, és az **ACS URL-** cím egy érvényes Costpoint URL-cím, amely **/LoginServlet.CPS**végződik.

   1. Válassza a **további URL-címek beállítása**lehetőséget. A **továbbítási állapot**mezőben adja meg az értéket a következő minta használatával: `system=[your system]` (például **System = DELTEKCP**).

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** ikont az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a Jegyzettömbbe mentéséhez.

   ![SAML-aláíró tanúsítvány](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint konfigurálása

1. Térjen vissza a Costpoint konfigurációs segédprogramhoz. Az **identitásszolgáltató összevonási metaadatok XML-** szövege szövegmezőbe illessze be az *alkalmazás-összevonási metaadatok URL-* fájljának tartalmát. 

   ![Costpoint-konfigurációs segédprogram](./media/costpoint-tutorial/config-utility-idp.png)

1. Folytassa a **DeltekCostpoint711Security.pdf** útmutató utasításait a Costpoint SAML-beállítás befejezéséhez.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy létrehozzon egy teszt felhasználót a B. Simon nevű Azure Portalban.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.

   ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

1. Válassza az **új felhasználó**lehetőséget.

   ![Új felhasználó gomb](common/new-user.png)

1. A **felhasználó** tulajdonságaiban hajtsa végre a következő lépéseket:

   ![A felhasználó párbeszédpanel](common/user-properties.png)

   1. A név mezőbe írja be a **B. Simon** **nevet** .
   
   1. A **Felhasználónév** mezőbe írja be a `b.simon\@yourcompanydomain.extension` következőt: (például: B.Simon@contoso.com ).
   
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát a B. Simon elérésének biztosításával a Costpoint.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.

1. Az alkalmazások listában válassza a **Costpoint**lehetőséget.

1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

   ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanel **felhasználók** listájában válassza ki a **B. Simon**elemet. Ezután válassza a **kiválasztás**lehetőséget.

1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd válassza a **kiválasztás**lehetőséget.

1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-costpoint-test-user"></a>Costpoint-teszt felhasználó létrehozása

Ebben a szakaszban egy felhasználót hoz létre a Costpoint-ben. Tegyük fel, hogy a felhasználói azonosító **b. Simon** , a felhasználó neve pedig **b. Simon**. A [Costpoint ügyfél-támogatási csapattal](https://www.deltek.com/about/contact-us) együttműködve vegye fel a felhasználót a Costpoint-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználót.

A felhasználó létrehozása után a felhasználó **hitelesítési módszerének** **Active Directory**kell lennie, az **SAML egyszeri bejelentkezés** jelölőnégyzetet ki kell jelölni, és a felhasználónévnek Azure Active Directory kell LENNIe **Active Directory vagy tanúsítvány-azonosítónak** (az alábbi képernyőképen látható).

![Costpoint-felhasználó](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Costpoint csempét a hozzáférési panelen, automatikusan be kell jelentkeznie a Costpoint alkalmazásba, mert be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálására szolgáló oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)