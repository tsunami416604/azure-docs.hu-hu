---
title: 'Oktatóanyag: Azure Active Directory integráció a Costpoint-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Costpoint között.
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
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879600"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Oktatóanyag: A Costpoint integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Costpoint a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Costpoint-t az Azure AD-vel, a következőket teheti:

* A Costpoint-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Costpoint az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Costpoint egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli környezetekben végezheti el. A Costpoint **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-costpoint-from-the-gallery"></a>Costpoint hozzáadása a gyűjteményből

A Costpoint Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Costpoint a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Costpoint** kifejezést a keresőmezőbe.
1. Válassza ki a **Costpoint** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a Costpoint a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Costpoint-ben.

Az Azure AD SSO és a Costpoint konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a Costpoint](#configure-costpoint)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre Costpoint-tesztelési felhasználót](#create-costpoint-test-user)** , hogy a Costpoint B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Costpoint** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal**rendelkezik, hajtsa végre a következő lépéseket:

    > [!NOTE]
    > A szolgáltatói metaadat-fájlt a **Costpoint-metaadatok létrehozása** szakaszban találja, amelyet az oktatóanyag későbbi részében ismertet.
 
    1. Kattintson a **metaadatfájl feltöltése**.
    
    1. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.
    
    1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-** értékei automatikusan fel lesznek töltve a Costpoint szakasz szövegmezői között.

        > [!Note]
        > Ha az **azonosító** és a **Válasz URL-** értékei nem kapnak automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értékeket. Győződjön meg arról, hogy az **azonosító (Entity ID)** és a **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** megfelelően van beállítva, és hogy az **ACS URL-** cím érvényes Costpoint URL-cím **/LoginServlet.CPS**végződik.

    1. Kattintson a **további URL-címek beállítása**elemre.

    1. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó értéket:`system=[your system], (for example, **system=DELTEKCP**)`

1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:
    
    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a továbbítási állapottal. Az értékek lekéréséhez forduljon a [Costpoint](https://www.deltek.com/about/contact-us) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a másolás ikonra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a Jegyzettömbbe mentéséhez.

   ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Costpoint-metaadatok előállítása

A Costpoint SAML SSO konfigurációját a **DeltekCostpoint711Security. pdf** útmutató ismerteti. Ebből az **SAML egyszeri bejelentkezés beállítása – > az SAML egyszeri bejelentkezés konfigurálása a Costpoint és az Azure ad szakasz között** . Kövesse az utasításokat, és készítsen **COSTPOINT SP összevonási metaadatok XML-** fájlját. Ezt a Azure Portal alapszintű **SAML** -konfigurációjában használhatja.

![Costpoint-konfigurációs segédprogram](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> A **DeltekCostpoint711Security. pdf** útmutatót a [Costpoint ügyfél-támogatási csapattól](https://www.deltek.com/about/contact-us)kapja meg. Ha nem rendelkezik ezzel a fájllal, kérjük, lépjen kapcsolatba velük a fájl beszerzéséhez.

### <a name="configure-costpoint"></a>Costpoint konfigurálása

Térjen vissza a **Costpoint konfigurációs segédprogramhoz** , és illessze be az **alkalmazás-összevonási metaadatok URL-címét** a **identitásszolgáltató összevonási metaadatok XML-** szövegmezőbe, és folytassa a **DeltekCostpoint711Security. pdf** útmutató utasításait a következő befejezéséhez: Costpoint SAML-telepítő. 

![Costpoint-konfigurációs segédprogram](./media/costpoint-tutorial/config01.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha B. Simon-hozzáférést biztosít a Costpoint.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Costpoint**lehetőséget.
1. Az alkalmazás Áttekintés lapjának **kezelés** szakaszában válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-costpoint-test-user"></a>Costpoint-tesztelési felhasználó létrehozása

Ebben a szakaszban egy felhasználót fog létrehozni a Costpoint-ben. Tegyük fel, hogy a **felhasználói azonosító** a **b. Simon** és a **b. Simon**név. A [Costpoint ügyfél-támogatási csapattal](https://www.deltek.com/about/contact-us) együttműködve vegye fel a felhasználót a Costpoint-platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználót.
 
A létrehozást követően a felhasználó **hitelesítési módszerének** **Active Directory**kell lennie, az **SAML egyszeri bejelentkezés** jelölőnégyzetet ki kell jelölni, és a felhasználónévnek Azure Active Directory kell LENNIe **Active Directory vagy a tanúsítvány azonosítója** (az alább látható módon).

![Costpoint-felhasználó](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Costpoint csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Costpoint, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)